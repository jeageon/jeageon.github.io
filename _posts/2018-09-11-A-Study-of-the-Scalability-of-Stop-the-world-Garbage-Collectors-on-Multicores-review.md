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

GC는 크게 두가지로 분류되는데, collecting을 하기위해 모든 application을 멈추는 **Stop-the-world** GC와,
이와 상반되는 개념으로 collecting을 할 때 application과 parallel하게 동작하는 **concurrent** GC로 분류된다.
일부에서는 사용가능한 메모리가 커져감에 따라 **Stop-the-world** GC가 application을 멈추는 시간이 증가하므로 
점점 커지는 머신들에 적응하지 못할것이라는 의견이 있으며, 실제 최근 연구에서도 8개의 GC thread 이상에서 부터는 성능이 감소하는 결과를 보여주었다.

해당 논문에서는 이러한 **Stop-the-world** GC의 bottlenek을 다음의 두가지로 나누고 있다.
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
