---
layout: post
title: "데이터 마이닝."
date: 2016-09-15
backgrounds:
    - http://feelgrafix.com/data/background/background-9.jpg
thumb: http://cfile2.uf.tistory.com/image/245FBF4D5745D05C123540
categories: Datamining ML
tags: ML
---

ML methods.

Basic.

1.
Numeric attributes(continuous): 실수 또는 정수값을 가진다 ex) 1.74
Nominal attributes(finite): 유한한 집합중 하나의 값을 가지며 categorical 이라고도 한다. Ex) True, false.
2.
Arff 형식: Dataset을 표현하는 standard 방식. 독립적이고 순서가 없는 instance들로 이루어져있고 instance들사이의 관계는 포함하지 않는다.
{%highlight ruby%}
# weather에 관한 데이터들임을 표시.
@relation weather

# 3개의 attribute(속성)를 가진다. 각각 nominal, numeric value를 가진다. 앞 두개의 attribute로 세번째 attribute play?를 predict해야한다.
@attribute outlook { sunny, overcast, rainy }
@attribute humidity numeric
@attribute play? { yes, no }

@data
%
% 3 instances
%
sunny, 85, no
rainy, 80, no
overcast, 83, yes
{%endhighlight%}

Supervised Learning(지도학습)
예시들의 집합 기반으로 예측을 한다. 이제까지의 주식 가격으로 미래 가격을 예측하는데 쓰이는 것과 같다. 훈련을 하는 데 쓰인 각 예시는 이율값으로 label되어있다. 지도 학습 알고리즘은 그런 값 label들에서 pattern을 찾는다. 관련있는 어떤 정보도 사용할 수 있고 각각의 알고리즘은 다른 타입의 패턴을 찾는다. 알고리즘이 최고의 패턴을 찾은 후에, 라벨되지않은 테스팅 데이터를 예측하는데 그 패턴을 사용한다.

# 1. Classification
데이터들을 category를 예측하는데 사용하는 경우 Classification이라고 한다.

# 2. Regression
어떤 값(예. 주식 가격)을 예측하는 경우 Regression

# 3. Clustering
객체들을 비슷한 집합끼리 묶는것.

# 4. Association
변수들간의 연관성을 찾는것.

# 5. Numeral Prediction
