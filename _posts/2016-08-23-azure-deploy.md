---
layout: post
title: "azure deploy!(Nginx+Passenger)"
date: 2016-08-23
backgrounds:
    - http://feelgrafix.com/data/background/background-9.jpg
thumb: http://previews.123rf.com/images/nito500/nito5001003/nito500100300271/6597215-vue-a-rienne-des-anciens-trimestre-de-S-ville-S-ville-Espagne--Banque-d'images.jpg
categories: development study
tags: azure deploy
---

# 이 튜토리얼은 Nginx와 Passenger를 사용하여 Rails application 을 Azure에 Deploy하는 법을 다루고 있습니다.

[참고 튜토리얼](https://github.com/m-gagne/azure-tutorials/blob/master/tutorial-windows-azure-ruby-on-rails-capistrano-3-postgresql.md)
단, azure의 예전 포탈의 내용이라는 점 주의하시길. 이걸 보고 저도 azure deploy 한글판 튜토리얼을 작성해야겠다는

[Deploying a Ruby app with Passenger to production](https://www.phusionpassenger.com/library/walkthroughs/deploy/)
passenger 라는 서버 deploy tool의 tutorial이었고 개인적으로 가장 친절하고 도움되는 튜토리얼이었습니다.


## 1. 일단 ssh key 만들기 (for 원격접속)

SSH 공개키 만들기
[ssh란?](https://en.wikipedia.org/wiki/Secure_Shell)

원격 접속. 일반 로그인 프로그램들이 패킷을 전송할 때 평문으로 전달을 하기 때문에 패스워드를 쉽게 가로챌 수 있는 것에 비해 SSH는 패킷 자체를 암호화 하여 전송하기 때문에 원격 관리에 혁명을 일으킨 프로그램입니다.

SSH 서버를 운영하지 않는 서버 관리자는 보안에 전혀 관심이 없다고도 할 수 있습니다. 여기서는 ssh1, ssh2 와 호환이 되는 OpenSSH로 설명을 하며 인증키 방식을 설명하지 안고 패드워드 방식으로 설명을 합니다

[ssh key 만들기 포스팅](http://www.jeff.wilcox.name/2013/06/secure-linux-vms-with-ssh-certificates/)

참고로 저는 **ssh key gen** 이용했습니다.

일단 ~/.ssh 폴더 들어가시고 .pub의 공개키를 찾아보신후 없으시면 터미널에 ssh-keygen 을 입력해주시면 자동 생성됩니다.

## 2. azure portal 에서 가상컴퓨터 생성

주의점 endpoint 에서 http 생성해야합니다!(port 80). 포털에 끝점 추가 기능을 활용해주세요.

[도움을 많이 받았던 VM, SSH 연결법 azure 도움말](https://azure.microsoft.com/ko-kr/documentation/articles/virtual-machines-linux-ssh-from-linux/)

저 같은 경우에는 ssh username@dnsname.cloudapp.net -p 22 로 가상컴퓨터에 접속하려 할때 계속 public key denied 에러가 나서..

.pub의 공개키암호 파일을 .pem으로도 바꿔보고 열심히 해봤으나... 계속 안되던 문제가 azure CLI 설치로 해결되었습니다.(ㅠㅠ)

즉 포털에서 가상컴퓨터 만들기 -> 공개키

![Sample Image](https://s3.ap-northeast-2.amazonaws.com/matilda38.github.io/azure1.png)

즉 여기서 key-gen 으로 생성한 제 공개키를 넣고 터미널에 azure CLI를 설치한거죠. **cat filename.pub**으로 복사한 뒤 입력해주시면 됩니다.

*나중에 접속 후 확인해보니, ~/.ssh에 authorized key가 저장되어 있었습니다.*

## 3. ssh로 접속 했으면! Git등 필요한 패키지 설치.

*우리는 가상컴퓨터로 linux 컴퓨터에서 작업하고 있다는 사실을 잊으시면 안됩니다~! 내 컴퓨터 아닙니다!^^*

sudo apt-get 으로 필요한것들 설치. Git libssl, libsqlite3, nodejs, nginx등을 설치한다.

**apt-get**이란?
ubuntu같은 리눅스 계열의 패키지 관리 명령어! yum등 다른 패키지 관리자도 있습니다. Mac에서는 homebrew를 쓰기도 합니다.

{% highlight Commandline %}
# for ubuntu

sudo apt-get update
sudo apt-get install -y curl gnupg build-essential

{% endhighlight %}

설치후엔 rbenv를 설치해주어야합니다 루비를 깔아야합니까! 내 가상컴퓨터엔 없기 때문입니다.

rbenv는 rvm과 같은 루비 설치 관리자라고 생각하시면 됩니다.

{% highlight Commandline %}
'''
$ git clone git://github.com/sstephenson/rbenv.git .rbenv
'''
{% endhighlight %}

.bash_profile에 다음 행 추가
{% highlight Commandline %}
[ -f "$HOME/.profile" ] && source "$HOME/.profile"
[ -f "$HOME/.bashrc" ] && source "$HOME/.bashrc"
{% endhighlight %}

.bashrc에 다음 추가.

{% highlight Commandline %}
export RBENV_ROOT="${HOME}/.rbenv"
if [ -d "${RBENV_ROOT}" ]; then
  export PATH="${RBENV_ROOT}/bin:${PATH}"
  eval "$(rbenv init -)"
fi
{% endhighlight %}

rbenv가 저장된 디렉토리를 RBENV_ROOT 환경 변수에 export 하고, rbenv 실행 파일이 들어 있는 디렉토리를 PATH에 추가한다.

쉘을 실행할때마다 rbenv init - 명령을 실행한다. (MAC에서 나도 겪었던 에러! 안하면 절대 안됨)

rbenv가 설치 된 경로를 PATH에 저장해서 rbenv 실행시 엉뚱한게 실행되지 않게 함입니다. 터미널을 다룰 땐 항상 PATH가 민감한 문제죠.

**맥에 루비 까는 법도 rbenv를 통해서 깔 수 있습니다. .bash_profile/ .bashrc 설정을 따로 해주어야 하는 점 또한 동일합니다.**

## 4. 재설정 및 bundler 설치, 재시작

{% highlight Commandline %}
rbenv rehash
rbenv global 2.3.0
ruby -v
ruby 2.3.0p0 (2015-12-25 revision 53290) [x86_64-linux]
gem install bundler
{% endhighlight %}

# (주의) 이거 생각보다 오래걸립니다. 일반 컴퓨터에 설치하는 것보다 약간 더 오래걸린다고 보시면 됩니다. 에러 아닙니다!


{% highlight Commandline %}
sudo apt-get install -y nodejs &&
sudo ln -sf /usr/bin/nodejs /usr/local/bin/node
{% endhighlight %}
nodejs 설치. Rails's asset pipeline compiler requires a Javascript runtime라서 설치한다고 합니다.

/usr/bin/nodejs로의 연결을 /usr/local/bin/node로 바꿔주었군요.

## 5. postgresql 설치 및 user 추가.

SQL 중 하나인 postgresql을 설치...하려하였으나 왜...안되는걸까요...나중에 다시...일단...

## 6. Passenger 설치.

# 원래는 capistrano를 설치하려 하였으나...
capistrano는 루비로 작성된 원격 서버 automation과 deployment 툴입니다. 서버 세팅 관리와 디플로이에 사용할 것입니다!
[tutorial](http://www.talkingquickly.co.uk/2014/01/deploying-rails-apps-to-a-vps-with-capistrano-v3/)
튜토리얼 따라하다가 포기.

그러다가 passenger라는 것을 발견하게 되었습니다. 잉? capistrano가 deploy 툴인 줄 알았더니 얜 또 뭐야 스러웠죠! 그렇게 웹 서버의 종류에 대해 알아보게 되다가...stackoverflow를 뒤지다 굉장히 좋은 글을 발견하고 다음 포스팅에서 번역을 진행하겠습니다.

암튼! 결론은 Passenger라는 툴로 갈아탔습니다. 그래서 이 포스팅이 Passenger+ Nginx 로 RoR Application을 Deploy하기! 가 되었죠 ㅎㅎ

{% highlight Commandline %}
# PGP key를 받고 설치. APT를 위한 HTTP 지원 추가.
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 561F9B9CAC40B2F7
sudo apt-get install -y apt-transport-https ca-certificates

# Add our APT repository
sudo sh -c "echo deb https://oss-binaries.phusionpassenger.com/apt/passenger precise main > /etc/apt/sources.list.d/passenger.list"
sudo apt-get update

# Install Passenger + Nginx
sudo apt-get install -y nginx-extras passenger
{% endhighlight %}

/etc/nginx에 설치 되었을 겁니다. /etc/nginx/nginx.conf 파일의

{% highlight Commandline %}
# include /etc/nginx/passenger.conf;

요 주석을 풀어주세요. passenger 설정을 풀어주는 겁니다!

# 재시작
sudo service nginx restart

# 제대로 설치되었는가!
sudo /usr/bin/passenger-config validate-install
 * Checking whether this Phusion Passenger install is in PATH... ✓
 * Checking whether there are no other Phusion Passenger installations... ✓

# nginx가 passenger core process를 시작했는지 체크
sudo /usr/sbin/passenger-memory-stats
# pid와 size등으로 프로세스가 출력되면 성공.

# apt로 업데이트 해주세요.
sudo apt-get update
sudo apt-get upgrade
{% endhighlight %}

## app deploy 하기
