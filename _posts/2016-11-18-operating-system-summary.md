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


# Process란 무엇인가

Process란 간단히, 실행중에 있는 Program이다. 영어로, process is a particular executing instance of a program when loaded to main memory.

즉, 프로그램이 main memory로 load 되었을 때의 실행중인 특정 instance라고 할수있다.

교수님께선 한글(hwp)나 워드도 프로세스의 예로 볼 수 있다고 하셨다. 프로세스는 몇 가지 구성사항을 가지고 있다.

*프로세스는 각자의 메모리공간을 가진다.*

![출처: gabrieletolomei.wordpress.com](https://gabrieletolomei.files.wordpress.com/2013/10/program_in_memory2.png?w=960)
따라서 다른 프로세스의 메모리 공간에 접근할 수 없다. 따라서 한 프로세스가 다른 프로세스의 결과값을 사용해야할 일이 있다던가 할때 IPC라는 inter process communication, 프로세스간 통신 절차가 필요하다는 얘기이다.

-> 프로세스의 단점.(Context Switching 다수 발생. 프로세스 간 통신이 힘들다)

* process의 메모리 구조

![](https://elgaabeb.files.wordpress.com/2011/01/process_memory_organization.png?w=598&h=464)


# 그 단점을 해결하는 Thread

![출처: wikipedia](http://1.bp.blogspot.com/-IuOPNytNwqc/VMVK_OX4raI/AAAAAAAAACc/ZoDkKILClvk/s1600/2000px-Multithreaded_process.svg.png)

Thread는 실타래라는 뜻을 가지고 있듯이, 한 프로그램 내의 실행 흐름이다. 위 그림과 같이 한 프로그램 내의 여러개의 쓰레드를 가지는 것이 Multi-Thread이다.

실행흐름이 여러개이므로 Thread 단위 Scheduling 시에도 이점을 가진다.
![출처: ibm](http://2.bp.blogspot.com/-3AB4sE53Dfw/VMVNdWa_V0I/AAAAAAAAACo/UAGFO7f6_UA/s1600/euva3a00.p54z.gif)

또한, 위 그림과 같이 각 Thread는 각각의 register를 가지지만, 다른 Thread들과 Heap, Code, Data를 공유한다. 즉, stack을 제외한 메모리 부분을 공유한다.

> stack: 지역변수 할당과 함수 호출 시 전달되는 인자값들의 저장하기 위한 공간
heap: C의 malloc, calloc과 C++의 new를 통한 동적 할당을 위해 존재하는 공간
data: 전역변수나 static 변수의 할당을 위해 존재하는 공간
code(text): 프로그램을 실행시키면 실행파일 내에 존재하는 명령어가 메모리 상에 올라가야지 프로그램을 동작시킬 수 있을 것이다. 이 명령어들을 위해 존재하는 공간.

출처: http://mooneegee.blogspot.kr/2015/01/os-process.html

code를 공유하기 때문에 프로세스가 가지는 함수를 자유롭게 사용할 수 있고, heap, data를 공유하기 때문에 IPC없이 thread간 통신이 가능하다.

동일한 프로세스 내에 존재하는 여러 개의 쓰레드는 heap영역에 변수를 메모리 공간을 (동적)할당하여 통신을 하면 된다. 물론 쓰레드 단위 스케줄링에서 프로세스와 같이 컨텍스트 스위칭이 일어날 수 있지만,

메모리공간을 공유하기 때문에 그 오버 헤드(overhead)가 더 작다.
