---
layout: post
title: "배열 기초."
date: 2016-10-09
backgrounds:
    - http://feelgrafix.com/data/background/background-9.jpg
thumb: http://mblogthumb4.phinf.naver.net/20160315_111/mkhjsk_1458023052522xOxyW_GIF/015.gif?type=w2
categories: c algorithm
tags: c
---

알고리즘을 공부하다보면 부끄러워지는 순간이 한두번이 아니다. 아직까지 이것도 몰랐다니, 또한 어떻게 이런 기본을 까먹을 수 있다니, 등?

오늘의 부끄러움은, 2차원 배열의 저장이다.

c언어에서 2차원 배열을 저장할 때,

1. 행/ 열 구분.

{% highlight ruby %}
int arr[i][j]= ~ //초기화
{% endhighlight %}

당연히 행이 세로이고 열이 가로이므로.
![n차원 행렬 예시](https://s3.ap-northeast-2.amazonaws.com/matilda38.github.io/screen1.png)
이런 식으로 행렬이 생성되겠죠? 그런데 거꾸로 알고 있었어요. 진짜 미쳤나봄 ㅠㅠㅠ 부끄럽다.

2. 주소값 저장.

Transpose 즉 전치 행렬 만드는 기본 코드 쓰다가 깨달은 사실
{% highlight c %}
int arr[2][3]={ {1,2,3},{4,5,6} }; //2행 3열 행렬이 생기겠죠?
int *ptr=arr;//arr의 주소를 저장합니다.
for(i=0;i<6;i++)
    printf("%d",*ptr++); //요건 가능.
for(i=0;i<6;i++)
    printf("%d",*(arr+i)); //요건 절대루 안됩니다.
{% endhighlight %}
이유를 생각해볼까요?

사실 당연해요. 2차원 배열 같은 경우에는
![다차원배열 주소값](https://s3.ap-northeast-2.amazonaws.com/matilda38.github.io/screen3.png)
이런식으로 저장되기 때문에 arr[i]같은 경우에는 해당 행의 시작주소를 저장하는 것이죠.

arr[i]=&arr[i][0]
*(arr[i])==arr[i][0]
가 되는 거죠.

{% highlight ruby %}

{% endhighlight %}


반성...!

블로그 검색 시스템은 언제쯤 만들수 있을까....허허
