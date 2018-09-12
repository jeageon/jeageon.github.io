---
title: A Study of the Scalability of Stop-the-world Garbage Collectors on Multicores 리뷰
updated: 2018-09-11 15:38
---

>**NOTE:** 이 게시물은 [**A Study of the Scalability of Stop-the-world Garbage Collectors on Multicores**](https://hal.inria.fr/hal-00868012/document)을 읽고 한국어로 간단하게 정리하는 리뷰입니다.

## A Study of the Scalability of Stop-the-world Garbage Collectors on Multicores

1. Introduction
2. Parallel Scavenge
3. Bottlenecks and optimisations
4. Evaluation
5. State of the Art
6. Conclusion

<div class="divider"></div>

## 1. Introduction

큰 메모리와 많은 코어를 가지는 현대의 하드웨어(cpu)에서 garbage collectors (GCs)는 새로운 국면을 맞이한다.

GC는 크게 두가지로 분류되는데, collecting을 하기위해 모든 application을 멈추는 **stop-the-world** GC와,
이와 상반되는 개념으로 collecting을 할 때 application과 parallel하게 동작하는 **concurrent** GC로 분류된다.
일부에서는 사용가능한 메모리가 커져감에 따라 **stop-the-world** GC가 application을 멈추는 시간이 증가하므로 
점점 커지는 머신들에 적응하지 못할것이라는 의견이 있으며, 실제 최근 연구에서도 8개의 GC thread 이상에서 부터는 성능이 감소하는 결과를 보여주었다.

해당 논문에서는 이러한 **stop-the-world** GC의 bottlenek을 다음의 두가지로 나누고 있다.
* Lack of **NUMA**-awareness

각각의 노드에 별도의 memory를 가지는 **NUMA**구조를 가지는 현대의 하드웨어에 대해서 
현재 많은 application들과 GC에서 오브젝트들을 하나의 노드에만 할당하고 있기때문에

위와 같은 문제점을 해결하기 위해서 각각의 노드들 사이의 메모리를 밸런싱하기위한 여러 방법들을 비교하였고 더 나아가서 
오브젝트들을 그들이 사용되는 노드의 메모리에 위치시킴으로써 spatial locality의 향상 시켰다.

* A heavily contended lock inside the parallel phase of **Parallel Scavenge**

**Parallel Scavenge**의 parallel phase에(논문 뒤의 내용에서 설명) 매우 많이 중첩되는 lock들이 존재하기 때문에

위와 같은 문제점을 해결하기위해서 (i) 작업 큐를 lock-free한 큐로 교체하였고, (ii) GC의 parallel phase를 종료시키는 동기화 프로토콜을 단순화 시켰다.

해당 작업의 결과물로 Numa-Aware Parallel Scavenge인 **NAPS** GC를 만들었으며 해당 GC는
살아있는 오브젝트들을 노드들 사이에 밸런스를 맞춰 분포시켜주고 collection의 parallel한 phase에서 완벽하게 locking을 피한다.
해당 논문은 가능한한 간단한 접근을 통해서 1000줄 이하의 코드 수정만으로 GC를 수정해 냈으며 이러한 결과는 구조의 전반적인 재설계 없이도 
알려진 문제를 해결하기에 충분하다는 것을 보여준다.

해당 논문은 자신들의 벤치마크 측정결과를 바탕으로 **NAPS** GC의 구현 결과를 다음과 같이 정리하였다.

1. **NAPS**는 많은 코어에서도 잘 scale함을 확인하였다. 많은 메모리를 요구하는 application에 대해, 일정한 힙(heap) 사이즈에서, 
하드웨어의 코어가 증가함에 따라 **NAPS**의 collection 시간이 감소하였다. **Parallel Scavenge**와 비교하여 **NAPS**는 모든 경우에서 더 
높은 성능을 보여준다.
2.  노드 사이의 메모리 접근을 균형있게 해줌으로써 단순히 collection 시간을 줄이는것 뿐만아니라, application의 접근 시간도 약 28% 향상(감소)시켰다. 
이러한 조합의 GC의 성능 향상은, 가장 메모리를 많이 사용하는 application에서는, 거의 두배로 측정되었다.
3. 가장 주요한 성능의 향상은, 가장 큰 bottleneck을 없앨 수 있는, 노드사이의 메모리의 밸런스를 맞추는것에서 가장 크게 나타난다고 측정되었다. 
32개 이상의 코어에서는 각 노드들 사이의 메모리를 건너서 접근하는 load를 줄이기 위해서 메모리의 locality를 증가시키는 것이 중요해진다. 
반대로 메모리의 locality의 증가덕분에 메모리 접근 수행시간을 줄이는 것은 효과가 미미하다.

<div class="divider"></div>

## 2. Parallel Scavenge

이 섹션에서는 해당 연구의 기초가 되는 **Parallel Scavenge**를 설명한다. 
**Parallel Scavenge**는 OpenJDK의 HotSpot virtual machine의 기본 GC이고 **stop-the-world** collecter이다.
**stop-the-world** 설계의 가장 큰 장점은 여러가지 발전된 기술(generational, copying and compacting)들과의 합치는것이 매우 간단하다는 것이다.(설계 자체가 간단하므로)

### 2.1 Overview

대부분의 오브젝트들은 생성된지 얼마 되지 않았을 때 죽는다. generational collector는 이 사실을 이용하여 힙을 여러 세대(generation)로 분리하고 
젊은 세대의 영역을 늙은 세대의 영역보다 더 자주 collecting한다. **Parallel Scavenge**는 3개의 세대를 정의한다: 작은 젊은세대; 큰 늙은세대; 
그리고 늙은세대와 비슷하지만 훨씬 작은 영구적인 세대; 

**Parallel Scavenge**의 젊은 세대는 eden 공간과 2개의 생존 공간들로(from-space and to-space), 총 3개의 공간으로 나뉘어진다. 
mutator(application thread와 같은)가 오브젝트를 생성하면 처음에는 eden 공간으로 할당된다. 만약 오브젝트가 GC의 첫 collect로부터 살아남는다면 
GC는 해당 오브젝트는 생존 공간들 중 하나로 복사하고, 두번째 collect로부터도 살아남는다면 늙은 세대로 복사한다. 늙은 세대와 영구적 세대는 각각 하나의 
공간으로 구성되고 영구적인 세대는 오직 자바 클래스를 위한 공간이다.

### 2.2 Allocation

mutator들의 각각의 할당으로부터 동기화를 피하기위해 각 mutator는 첫 할당을 **TLAB**(Tread-Local Alloctaion Buffer)라고 불리는 
eden 공간에 존재하는 커다란 메모리 덩어리 부분에 한다. 그 후 동기화하지 않고 **TLAB**로 부터 bump pointer를 사용하여 오브젝트를 할당한다. 
비슷하게 늙은 세대로 복사하는 과정에서도 **PLAB**(Promotion-Local Allocation Buffer)를 이용하여 할당한다.

### Young gengeration collection

젋은 세대의 collection은 현재 **TLAB**의 공간이 부족하고 eden 공간으로부터 새로운 **TLAB**를 할당받는것에 실패 했을 때 시작된다. 
이 때 eden 공간에는 가장 최근에 할당된 오브젝트들이 존재하며 from-공간에는 이전 collect로부터 살아남은 오브젝트들이 존재하고, to-공간은 비어있다. 
collector는 eden 공간에 존재하는 살아있는 오브젝트들을 to-공간으로 옮기고 from-공간에 존재하는 살아있는 오브젝트들을 늙은 세대의 영역으로 옮긴다. 
이 후에는 eden 공간과 from-공간에는 죽은 오브젝트들만 존재하므로 그냥 빈 공간으로 취급하며 from-공간과 to-공간을 서로 교체해주므로써 collection을 마친다.

**Parallel Scavenge**는 몇 개의 phase로 구성된다.

1.  mutators들은 **stop-the-world** 벽을 만나 모두 멈추게된다.(initialisation phase) 
initialisation phase동안은 VM thread라고 불리는 단 한개의 thread가 parallel phase에서 GC thread들이 수행해야 할 GC task들을 준비한다. 
