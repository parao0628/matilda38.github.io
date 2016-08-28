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

#### 이 튜토리얼은 Nginx와 Passenger를 사용하여 Rails application 을 Azure에 Deploy하는 법을 다루고 있습니다.

>**[참고 튜토리얼1](https://github.com/m-gagne/azure-tutorials/blob/master/tutorial-windows-azure-ruby-on-rails-capistrano-3-postgresql.md)**
단, azure의 예전 포탈의 내용이라는 점 주의하시길. 이걸 보고 저도 azure deploy 한글판 튜토리얼을 작성해야겠다는 생각을 하게 되었습니다. **[Deploying a Ruby app with Passenger to production](https://www.phusionpassenger.com/library/walkthroughs/deploy/)**
passenger 라는 서버 deploy tool의 tutorial이었고 개인적으로 가장 친절하고 도움되는 튜토리얼이었습니다.


## 1. 일단 ssh key 만들기 (for 원격접속)

SSH 공개키 만들기
[ssh란?](https://en.wikipedia.org/wiki/Secure_Shell)

:원격 접속. 일반 로그인 프로그램들이 패킷을 전송할 때 평문으로 전달을 하기 때문에 패스워드를 쉽게 가로챌 수 있는 것에 비해 SSH는 패킷 자체를 암호화 하여 전송하기 때문에 원격 관리에 혁명을 일으킨 프로그램입니다.

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
$ git clone git://github.com/sstephenson/rbenv.git .rbenv
{% endhighlight %}

.bashrc에 다음 추가. (bash_profile 없으면 그냥 bashrc에다가!! ubuntu는 .bashrc)
[공식문서](https://github.com/rbenv/rbenv)

{% highlight Commandline %}
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile
~/.rbenv/bin/rbenv init
type rbenv
{% endhighlight %}

[ruby-build 설치](https://github.com/rbenv/ruby-build#readme)

rbenv가 저장된 디렉토리를 RBENV_ROOT 환경 변수에 export 하고, rbenv 실행 파일이 들어 있는 디렉토리를 PATH에 추가한다.

쉘을 실행할때마다 rbenv init - 명령을 실행한다. (MAC에서 나도 겪었던 에러! 안하면 절대 안됨)

rbenv가 설치 된 경로를 PATH에 저장해서 rbenv 실행시 엉뚱한게 실행되지 않게 함입니다. 터미널을 다룰 땐 항상 PATH가 민감한 문제죠.

**맥에 루비 까는 법도 rbenv를 통해서 깔 수 있습니다. .bash_profile/ .bashrc 설정을 따로 해주어야 하는 점 또한 동일합니다.**

>[혹시 이 방법이 안된다면 참고](https://gist.github.com/m-gagne/9234940)

## 4. 재설정 및 bundler 설치, 재시작

{% highlight Commandline %}
# 엄청 오래걸릴 수 있습니다
rbenv install 2.3.0

rbenv rehash
rbenv global 2.3.0
ruby -v
ruby 2.3.0p0 (2015-12-25 revision 53290) [x86_64-linux]
gem install bundler
{% endhighlight %}

#### (주의) 이거 생각보다 오래걸립니다. 일반 컴퓨터에 설치하는 것보다 약간 더 오래걸린다고 보시면 됩니다. 에러 아닙니다!


{% highlight Commandline %}
sudo apt-get install -y nodejs &&
sudo ln -sf /usr/bin/nodejs /usr/local/bin/node
{% endhighlight %}
nodejs 설치. Rails's asset pipeline compiler requires a Javascript runtime라서 설치한다고 합니다.

/usr/bin/nodejs로의 연결을 /usr/local/bin/node로 바꿔주었군요.

## *5. postgresql 설치 및 user 추가.*

SQL 중 하나인 postgresql을 설치...하려하였으나 왜...안되는걸까요...나중에 다시...일단...

## *6. Passenger 설치.*

# 원래는 capistrano를 설치하려 하였으나...
capistrano는 루비로 작성된 원격 서버 automation과 deployment 툴입니다. 서버 세팅 관리와 디플로이에 사용할 것입니다!
[tutorial](http://www.talkingquickly.co.uk/2014/01/deploying-rails-apps-to-a-vps-with-capistrano-v3/)
[tutorial2](https://www.tiagoamaro.com.br/2013/10/28/how-to-compile-assets-locally-with-capistrano/)
튜토리얼 따라하다가 포기.

그러다가 passenger라는 것을 발견하게 되었습니다. 잉? capistrano가 deploy 툴인 줄 알았더니 얜 또 뭐야 스러웠죠! 그렇게 웹 서버의 종류에 대해 알아보게 되다가...stackoverflow를 뒤지다 굉장히 좋은 글을 발견하고 다음 포스팅에서 번역을 진행하겠습니다.

암튼! 결론은 Passenger라는 툴로 갈아탔습니다. 그래서 이 포스팅이 Passenger+ Nginx 로 RoR Application을 Deploy하기! 가 되었죠 ㅎㅎ

기준: Ubuntu 12.04 LTS (with APT).

제 Azure 계정의 가상 컴퓨터는 위 모델이었기 때문에 해당 모델 버전의 tutorial 을 참고하였습니다.

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

드디어!

## app deploy 하기

1. 일단 내 코드를 원격 저장소 git에 업로드해주세요.
2. ssh 로 내 가상 컴퓨터로 로그인.
3. 튜토리얼에는 새로운 유저를 만들어서 deploy용 유저로 활용하라는 내용이 있습니다만,(you should create an operating system user account for your app) 저는 새로운 유저를 만들면 왜 인진 모르겠느나 자꾸 루비가 예전버전으로 회귀 ㅠㅠ. 알고보니 새로 만든 유저로 su를 이용해 접속하면 rbenv도 없고 설정 자체가 안되더라구요. 그래서 그냥 제 원래 관리자계정으로 ㅠㅠ 진행하였습니다.
4. nano config/database.yml로 database.yml을 수정해줍니다.

{% highlight Commandline %}
production:
  adapter: sqlite3
  database: db/production.sqlite3
{% endhighlight %}

배포 모드를 설정해주는 거에요. nano는 text editor라고 생각하시면 됩니다.
5. 세션을 암호화하기 위한 비밀 키를 만들어 주세요.
{% highlight Commandline %}
bundle exec rake secret
{% endhighlight %}

만들어 진 키를 secret.yml에 넣어주어야 합니다.
{% highlight Commandline %}
# 파일 오픈
nano config/secrets.yml

# 여기 환경변수 자리에 넣어주세요
production:
  secret_key_base: <%=ENV["SECRET_KEY_BASE"]%> # 이 자리!!
{% endhighlight %}
6. 본격 배포 시작 전, precomplie과 migrate 수행. 저는 seed까지. (RAILS_ENV=production가 매우 중요합니다. default 는 development이기 떄문에 production 설정을 해주지 않으면 배포시 migrate가 되지 않았던 에러가 날 것입니다.)
{% highlight Commandline %}
bundle exec rake assets:precompile db:migrate RAILS_ENV=production
bundle exec rake db:seed RAILS_ENV=production
{% endhighlight %}
7. passenger가 수행하는 루비버전입니다. 메모해놓으세요. 설정에서 써야됩니다!
{% highlight Commandline %}
passenger-config about ruby-command
passenger-config was invoked through the following Ruby interpreter:
  Command: # 이거!(루비 저장 위치)

sudo nano /etc/nginx/sites-enabled/(*내꺼 이름*).conf

#/etc/nginx 에 각종 설정들이 저장되어 있습니다. sites-enabled 폴더에는 활성화 된 어플리케이션들 설정이 들어가고 거기에 내 어플리케이션 설정을 추가해야합니다.

server {
    listen 80; # 포트 80
    server_name name; # DNS 주소 또는 IP 주소 값을 넣어주세요. IP 추천.

    # 내 코드가 들어있는 위치!
    root /var/www/myapp/code/public;

    # Turn on Passenger
    passenger_enabled on;
    passenger_ruby ruby_path; # 내 루비가 설치되어있는 위치(아까 복사한 값)
}

curl http://yourserver.com/
#뭔가가 나오나요?

sudo service nginx restart
# 설정을 바꿨다면 서버를 재시작해주세요.

{% endhighlight %}


## **Deploy 완료...!**
![sample Image](https://s3.ap-northeast-2.amazonaws.com/matilda38.github.io/sc.png)

# +추가

계속 겪었던 어려움.

nginx error들.
=> conf 파일(아마 nginx.conf)를 보면 error log가 어디에 저장되어있는지가 나와있습니다. 해당 파일을 참고하여 에러를 해결해주시면 됩니다. 에러메세지를 구글에다 쳐보세요.

사이트 내부 에러.
=> 내 application의 log 폴더 => production.rb에 배포 단계의 log 가 나와있습니다. **꼭 참고해주세요.**

저 같은 경우에 post 라우팅이 안되서 계속 찾아봤더니, nginx 에러가 아니라 **code error**였습니다. (*var/nginx/error.log 에선 더이상 에러가 없는데 계속 에러가 나는게 이상했죠.*)

code 수정 후 migrate를 수행했는데 그냥 rake db:migrate했더니 development 모드애서만 migrate 가 되었고,

**production 모드의 경우 수정 전 migrate 만 적용되어 수정사항이 반영되지 않았던 거죠.**

rake db:migrate RAILS_ENV=production rake db:seed RAILS_ENV=production 수행 후엔 해결 되었습니다.

배포 단계에서의 migrate는 따로 해줘야 하는 것을 몰랐던 거죠.

rails console을 통해 알았어야 하는데 이것 또한 *rails console production*이라고 해야 배포단계의 console이 나오기 때문에 계속 헤맸습니다.

rails console 만 쳐서 development 단계의 migration만 확인하고 왜 안될까 계속 헤맸습니다...ㅠㅠ 무식하면 힘들다.

