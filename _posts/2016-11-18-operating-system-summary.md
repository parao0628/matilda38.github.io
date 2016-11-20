---
layout: post
title: "operating system 요약"
date: 2016-11-18
backgrounds:
    - http://feelgrafix.com/data/background/background-9.jpg
thumb: https://s3.ap-northeast-2.amazonaws.com/matilda38.github.io/%E1%84%8B%E1%85%B5%E1%84%8C%E1%85%A6%E1%84%92%E1%85%AE%E1%86%AB.jpg
categories: OS
tags: os
---
![how to interact with hardware](http://i.dailymail.co.uk/i/pix/2009/07/08/article-1198262-05A2F11C000005DC-856_233x353.jpg)

## OS는 무엇일까?

사용자(USER)가 hardware를 직접 다루기는 힘들다. 이때 사용자와 hardware를 연결(bridge)하는 역할을 맡은게 운영체제이다.

우리가 흔히 computer system이라고 부르는 것들은 이렇게 4가지 component를 가진다.

> *User, application programs, Operating systems, hardware.*

이 중 기본적인 computing 자원(*resource*)을 제공하는 것이 hardware, 예를 들어 cpu, i/o (입출력장치), 메모리등이 있겠다.

application program은 유저의 computing problem을 해결하기 위해 resource에 대한 사용 명세서를 담은 프로그램이다.

그리고 operating system은 application과 user의 하드웨어 사용을 제어하고 돕는 역할을 하는 중재자이다.

기본적으로 os의 역할은

1. Resource allocator (각 프로그램 마다 필요한 자원이 있을테니, 프로그램들이 필요할때 해당하는 자원(hardware)를 할당하는 역할.
2. Control program (프로그램들이 에러가 나지 않게 execute되고 부적절한 사용이 이루어지지 않도록 제어한다)

두 가지라고 할 수 있다.

우리가 OS를 이해할 때 빠뜨릴 수 없는 개념이 OS 핵심 구성요소인 kernel인데 kernel에 대해서 잠깐 짚고 넘어가자.

kernel(커널)은 컴퓨터에서 항상 돌아가고 있는 프로그램이다. system programs는 os와 관련된 프로그램이지만, 커널의 일부는 아닌 프로그램이다. 반면 application program은 시스템의 운영(operation)과는 관련없는 모든 프로그램을 일컫는다.

cpu 및 자원들이 공통된 bus를 통해 shared memory에 접근하는 극도로 추상화된 그림을 살펴보며 이해해보자.

![concurrent execution of cpus and devices competing for memory cycle](https://s3.ap-northeast-2.amazonaws.com/matilda38.github.io/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA+2016-11-20+%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE+2.48.10.png)
"메모리 cycle을 위해 경쟁하는 cpu와 device들의 병렬적 execution" 을 담고있는 그림이다.