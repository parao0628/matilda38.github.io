---
layout: post
title: "Cryptography 중간고사"
date: 2016-10-21
backgrounds:
    - http://feelgrafix.com/data/background/background-9.jpg
thumb: https://s3.ap-northeast-2.amazonaws.com/matilda38.github.io/screen3.png
categories: informationSecurity
tags:
---

필기와 자료를 올려본다.

이번 중간에는 기본적인 개념(ex/ two basic building block method)과 연습문제(주로 ppt에 나와있던, ex)GF(1750))들이 나왔다.

대부분 계산이 매우 복잡한 문제였고, 다음 시험부터는 ppt 문제들 답을 어느정도 외워가야겠다는 생각이 들었다. 계속 보면서 친숙해지던지.

일단 이번 시험은 마지막 단원을 못봐서...너무 아쉬웠다. 다음엔 더 잘 봐야될 필요성이 생겼다. 다음엔 진짜 미리 공부해야지..!


이번 중간고사는 Symmetric Cipher였다.

# CH2

(다양한 암호화 기법, ceaser부터 playfair(쌍), monoalphabetic, polyalphabetic, autokey, vinegere 등 improved 된 암호기법들이 설명되어있다.)

![](https://s3.ap-northeast-2.amazonaws.com/matilda38.github.io/1.png)
![](https://s3.ap-northeast-2.amazonaws.com/matilda38.github.io/2.png)
![](https://s3.ap-northeast-2.amazonaws.com/matilda38.github.io/3.png)
![](https://s3.ap-northeast-2.amazonaws.com/matilda38.github.io/4.png)

# CH3

(DES 기법, Fiestal 구조와 상세한 round 에 대한 설명이 있다)
(plaintext가 substitution, Permutation, Key와 XOR 연산 등을 거쳐 ciphertext가 되는 과정)

![](https://s3.ap-northeast-2.amazonaws.com/matilda38.github.io/9.png)
![](https://s3.ap-northeast-2.amazonaws.com/matilda38.github.io/10.png)
![](https://s3.ap-northeast-2.amazonaws.com/matilda38.github.io/11.png)

# CH4

(Mod 즉 나머지 연산을 먼저 소개하고 이어 GCD, modular Arthimetic에 관한 설명. (A+B)mod N= ((A mod N) + (B mod N)) mod N, A와 B의 합동이란 무엇인가,
 Finite set 등의 개념을 소개하면서 갈루아 필드, AES에 필요한 내용(GF(2^8)들을 기본적으로 가르친다.)

![](https://s3.ap-northeast-2.amazonaws.com/matilda38.github.io/5.png)
![](https://s3.ap-northeast-2.amazonaws.com/matilda38.github.io/6.png)
![](https://s3.ap-northeast-2.amazonaws.com/matilda38.github.io/7.png)
![](https://s3.ap-northeast-2.amazonaws.com/matilda38.github.io/8.png)


