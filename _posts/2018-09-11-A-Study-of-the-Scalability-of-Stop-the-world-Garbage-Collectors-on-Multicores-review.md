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

큰 메모리와 많은 코어를 가지는 현대의 하드웨어(cpu)에서 garbage collectors (GCs) 는 새로운 국면을 맞이한다.
GC는 크게 두가지로 분류되는데, collecting을 하기위해 모든 application을 멈추는 **Stop-the-world** GC와,
이와 상반되는 개념으로 collecting을 할 때 application이 parallel하게 동작하는 **concurrent** GC이 존재한다.
일부에서는 사용가능한 메모리가 커져감에 따라 **Stop-the-world** GC가 application을 멈추는 시간이 증가하므로 
점점 커지는 머신들에 적응하지 못할것이라는 의견이 있으며, 실제 최근 연구에서도 8개의 GC thread 이상에서 부터 성능이 감소하는 결과를 보여주었다.
