---
layout: post
title: "Thread와 Process의 차이"
date: 2017-01-16
backgrounds:
    - http://feelgrafix.com/data/background/background-9.jpg
thumb: http://www.tooli.co.kr/files/attach/images/571585/680/575/%EC%8A%A4%EB%A7%88%EC%9D%BC%20%EA%B0%95%EC%95%84%EC%A7%80.jpg
categories: development study
tags: home coding
---

11-18 게시물에 OS의 입장에서 바라본 프로세스와 쓰레드에 대해서 포스팅헸다. 오늘 포스팅은 Kernel단과 Hardware단의 Thread에, 특히 스케줄링에 중점을 두어 포스팅하겠다.

Thread의 종류

-       User level threads (application)

-       Kernel level thread (operating system)

-       Hardware Threads (each processor)



*thread는 Scheduling과 연관이 높다.



Single Thread: => process 단위 스케줄링

But,

Thread 단위 scheduling

Multi-thread => 자원 사용 확률 (thread가 늘어 날 수 록 선점효과 상승). 연산량/ Real-time/ Delay-critical한 Application들에 사용.



VM – VM – VM => VM 내 thread scheduler (VM의 OS)

Hypervisor => Hypervisor내 vCPU scheduler

Hardware



VM은 자기 자신이 그냥 Machine 인줄 알기 때문에 (OS 그대로 넣으니까)

일반적인 scheduling을 한다. 하지만 OS가 schedule하는 vCPU들은 실제 CPU가 아니기 때문에, hypervisor가 vCPU들을 실제 pCPU에 schedule 시켜야한다. (vcpu scheduler)



vcpu affinity: vcpu – pcpu 간의 연결. (관리자 지정) – pinning 과 비슷한 개념.

NUMA(Non Uniform Memory Access) : 일종의 processor 설계 방법으로, 메모리 접근 속도를 높이기 위해 core를 분산하여 설계.

NUMA node 1, NUMA node 2 이런 식으로 분할한다.

Vcpu가 Pcpu 개수 이상이면, overbooking이라고 한다. => Public Cloud에서는 이런식으로 장사 함. (Azure, AWS EC2)

- Context switching 이 많이 일어날 수 밖에 없다.

- 대규모 Parallel application에서는 절대 안됨!

*  네트워크에서 동적 주소 할당과 비슷한 개념.

[관련 자료 링크](http://mooneegee.blogspot.kr/2015/01/os-thread.html)

