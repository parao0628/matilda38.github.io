---
layout: post
title: "밤샘 코딩! 드디어 완성.(0802 수정)"
date: 2016-07-21
backgrounds:
    - https://dl.dropboxusercontent.com/u/18322837/cdn/Streetwill/tube.jpg
thumb: https://dl.dropboxusercontent.com/u/18322837/cdn/Streetwill/thumbs/coding.jpg
categories: development work
tags: home coding matilda38.github.io
---

## 드디어 ihanxing rails application

## 기능 대부분 구현.

일단 user와 celebrity를 연결하는 (M:N관계) favorite이라는 table을 generate하였고, 그 과정에서
'has_many :celebrities, through: :favorites'을 사용!

일단 favorite 테이블의 코드를 살펴보자.

{%highlight ruby%}
class CreateFavorites < ActiveRecord::Migration
  def change
    create_table :favorites, id: false do |t|
      t.integer :celebrity_id
      t.integer :user_id

      t.timestamps null: false
    end
  end

end
{% endhighlight %}
코드를 보면 celebrity_id, user_id가 뭘 의미하는지 느낌이 올겁니다. M:N관계를 구현하기 위해 user외 celebrity를 연결하는 장치죠. 각 테이블의 primary key가 favorite table의 foriegn key인 셈입니다. 동시에 그 두 개의 key는 favorite 의 composite primary key입니다. 두 개의 column이 primary key역할을 하는 거죠. 저렇게 되어야지만 unique조건을 만족합니다. 연예인 1명에 몇명의 유저가 좋아할 수도 있고, 한 유저가 다양한 연예인을 좋아할 수도 있기 때문이죠. 하지만 특정 유저가 특정 연예인을 좋아하는 row가 겹치면 안되기 때문에 composite primary key를 도입한겁니다. favorite 자체의 id는 key 역할을 하면 안되겠죠?
'id: false' 조건을 도입한 이유입니다.

{%highlight ruby%}
rails g migration AddIndexToFavorites
{% endhighlight %}
이 명령어로 migration 파일을 생성한뒤 해당 파일을 이렇게 수정해줍시다.

{%highlight ruby%}
class AddIndexToFavorites < ActiveRecord::Migration
    def change
      add_index :favorites, [:user_id, :celebrity_id], unique: true
    end
end
{% endhighlight %}

이렇게 수정하준뒤

{%highlight ruby%}
rake db:migrate
{% endhighlight %}

마이그레이트 수행하면! schema.rb가 수정될겁니다.

celebrity.rb
{%highlight ruby%}
  has_many :favorites
  has_many :user, through: :favorites
{%endhighlight%}

favorite.rb
{%highlight ruby%}
  belongs_to :user
  belongs_to :celebrity
{%endhighlight%}

user.rb
{%highlight ruby%}
  has_many :favorites
  has_many :celebrities, through: :favorites
{%endhighlight%}

추가해주면! 완료입니다...구현은!

간단한 사용법을 안내해드리면~!

{%highlight ruby%}
current_user.favorites.create(celebrity: @celebrity)
{%endhighlight%}

현재 유저가 좋아하는 스타를 즐겨찾기에 넣고 싶을 때! 버튼을 클릭하면 자동으로 넣어지게 하기 위해 짠 코드입니다. user가 연결되어있는 celebrity는 객체로 받아왔습니다.

쉽쬬?

bar에 항상 떠있는 view입니다.(application.html.erb)

{%highlight html%}
    <%if user_signed_in?%>
       <%$stars.each do |s|%>
        <li><%= link_to s.name, celebrity_timelines_path(s), class:'a'%></li>
       <%end%>
    <%end%>
{%endhighlight%}

$stars라는 괴이한 변수를 선언했는데, 페이지가 이동해도 계속 유지되어야 하기 때문에 전역변수로 설정해서입니다.

{%highlight ruby%}
    if user_signed_in?
       $stars=current_user.celebrities
    end
{%endhighlight%}

celebrity_controller에 이렇게 변수를 선언했습니다. favorite으로 연결되어있기때문에 저렇게 .celebrities로 호출이 가능한것이죠.

이제 사용자별 즐겨찾기 스타 설정 가능! 흐..드디어!

p.s 좋아요 기능 찾아봤는데 그게 진짜 어렵다.(ajax 때문에..)

$stars가 아무래도 걸려서.. 결국 수정하였습니다! applicationcontroller에서

{%highlight ruby%}
class ApplicationController < ActionController::Base
  # Prevent CSRF attacks by raising an exception.
  # For APIs, you may want to use :null_session instead.
  protect_from_forgery with: :exception
  ActiveSupport.halt_callback_chains_on_return_false = false
  before_action :set_variable

  def set_variable
    if user_signed_in?
      @stars=current_user.celebrities
    end
  end

end
{%endhighlight%}
로 set variable이라는 action를 만든후, @stars에 current_user의 연예인들을(favorite으로 연결되어있습니다) 호출하여 집어넣었다.
application controller이기 때문에 모든 controller에 적용된다. 하지만 주 목적은 layout 에 application.html.erb 에 적용시키기 위해 해당 controller에 넣었다. before_action로 무조건 set_variable을 실행하게 하였다.
따라서! application.html.erb에서 사용가능하게 되었다! 이렇게 쉬운걸 $stars라는 이상한 전역변수를 만들었다니! 아는 대로 보인다더니..암튼!

이걸 해결하고 나니 더 중대한 문제를 발견했다. 물론 변수 형태를 바꿔서 생긴 문제는 아니었고, 원래 문제였는데 지금 밝혀진것...상세페이지를 방문하면...무조건 favorite에 추가가 된다...도대체 왜...일단 고쳐야겠다는 생각이 들어
'onclick=<%current_user.favorites.create(celebrity: @celebrity)%>'부터 고쳤다. 얘를 고치고 대신

{%highlight html%}
<div class="profile-userbuttons">
          <button class="btn btn-danger btn-lg" onclick="favorite()" data-toggle="popover" data-content="등록 완료!" data-placement="top"><i class="fa fa-star-o"></i> Favorite</button>
        </div>
        <script>
          function favorite() {
            var xhttp;
            xhttp = new XMLHttpRequest();
            xhttp.onreadystatechange = function() {
              if (xhttp.readyState == 4 && xhttp.status == 200) {
                document.getElementById("txtHint").innerHTML = xhttp.responseText;
              }
            };
            xhttp.open("GET", "/favorite/<%=@celebrity.id%>", true);
            xhttp.send();
          }
        </script>

{%endhighlight%}
이런식으로 view파일을 수정했다. ajax를 사용한 셈이다. 즉, click 되면 favorite()이란 함수를 수행하여,

xhttp.open("GET", "/favorite/<%=@celebrity.id%>", true); GET 방식으로 접속하였다

routing을 get 'favorite/:id' => 'celebrity#favorite"로 설정하여 해당 연예인의 id를 전달한 셈이 된다.

controller를 보면

favorite이란 새로운 action 을 celebrity controller에 만들고! favorite 추가해주는 코드를 여기에다 넣었다.

{%highlight ruby%}
    def favorite
        current_user.favorites.create(celebrity: @celebrity)
    end
{%endhighlight%}

@celebrity= Celebrity.find(:id)로 해당 연예인을 찾았다. routing이 /:id으로 되어있기 때문에 가능한 일이다.

여기서 @celebrity는 before_action :set_celebrity에서 찾은건데

routing을 get 'favorite/:id' => 'celebrity#favorite"로 설정하여 @celebrity= Celebrity.find(:id)로 해당 연예인을 찾았다.


끝!



