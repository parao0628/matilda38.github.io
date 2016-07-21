---
layout: post
title: "밤샘 코딩! 드디어 완성."
date: 2016-07-21
backgrounds:
    - http://feelgrafix.com/data/background/background-9.jpg
    - https://i.ytimg.com/vi/_0NM7Ag73MM/maxresdefault.jpg
thumb: http://pic.prepics-cdn.com/fa8133cef3407/44912513.gif
categories: development work
tags: home coding matilda38.github.io
---

드디어 ihanxing rails application 기능 대부분 구현. 일단 user와 celebrity를 연결하는 (M:N관계) favorite이라는 table을 generate하였고, 그 과정에서
'has_many :celebrities, through: :favorites'을 사용!
