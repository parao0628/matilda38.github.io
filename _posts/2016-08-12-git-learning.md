---
layout: post
title: "Git!"
date: 2016-08-15
backgrounds:
    - http://feelgrafix.com/data/background/background-9.jpg
thumb: https://s3.ap-northeast-2.amazonaws.com/matilda38.github.io/111.jpg
categories: development study
tags: home coding
---


[sourceTree로 배우기](http://greatgift.tistory.com/36)

[깃 따라하기](http://gitimmersion.com/lab_06.html)

[클래스 라이언](http://class.likelion.net/tutorials/8)

Git focuses on the changes to a file rather than the file itself. When you say git add file, you are not telling git to add the file to the repository. Rather you are saying that git should make note of the current state of that file to be committed later.

$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	modified:   hello.rb

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   hello.rb

Notice how hello.rb is listed twice in the status. The first change (adding a default) is staged and is ready to be committed. The second change (adding a comment) is unstaged. If you were to commit right now, the comment would not be saved in the repository.

Let’s try that.

GDG 세션에서 배운것도 활용하자.