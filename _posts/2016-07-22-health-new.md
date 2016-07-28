---
layout: post
title: "ihanxing application 구현"
date: 2016-07-23
backgrounds:
    - https://i.ytimg.com/vi/_0NM7Ag73MM/maxresdefault.jpg
thumb: /assets/images/1.gif
categories: development work
tags: home work coding matilda38.github.io
---
일단 이 web application은 model이 celebrity, posts, timelines, schedules, favorites, user, tips으로 많았다. 여기서 가장 중요했던 것은 모델들 관의 관계였는데, 모델이 많다보니 controller를 각각 1개씩 만들었다. 내겐 scaffold가 훨씬 편했기 때문에 user빼고는 모두 scaffold로 제작하였다. 일전에 posting했던 누워서 scaffold application 만들기는 강의평가사이트였는데 모델이 딱 두개라 professor_evaluations_path(pro) 로 pro라는 교수 객체를 던져 professor/professor_id/evaluations, 즉 해당 id의 교수 평가를 조회할 수 있었다.

하지만 ihanxing application의 경우 연예인 personal page에서 timeline, schedule, post를 모두 확인하고 싶었기 때문에 (action의 이동이 없었다) celebrities/celebrity_id/timelines 보다는, celebrities/celebrity_id 로 celebrity 모델의 show 액션에서 모든 모델들을 보여주는 방식을 택했다.

일단 route 를 확인해보자.

{%highlight ruby%}
root 'celebrities#index'

  devise_for :users

  get 'home/mypage' => '/mypage'

  resources :tips

  resources :celebrities do
    resources :schedules
    resources :timelines
    resources :posts
  end
{%endhighlight%}

![Sample Image](/assets/images/1.png)
route와 image에서 볼수 있듯이 모델의 관계는 저렇게 정의 되어있다. celebrity와 1:N 관계에있는 세개의 모델, user와 M:N 관계를 형성하는 celebrity, 그 관계를 구현하기위한 favorite 모델이 존재한다. 마지막으로 독립적으로 존재하는 tips 모델.

이제 실제 코드를 살펴보자.
{%highlight ruby%}

 def index
    unless user_signed_in?
      redirect_to '/users/sign_in'
    end
    if user_signed_in?
      $stars=current_user.celebrities
    end
    @celebrities = Celebrity.all

  end

  # GET /celebrities/1
  # GET /celebrities/1.json
  def show
    @timelines = @celebrity.timelines.all
    @schedules = @celebrity.schedules.all
    @posts = @celebrity.posts.all
  end

{%endhighlight%}

if user_signed_in? 사용자가 로그인 되어있으면 현재 유저의 연결된 celebrities를 호출할 수 있다. 두 모델이 M:N으로 연결되어있기에 가능한 것이다.

마찬가지로 show action에서 해당 연예인의 타임라인 스케줄 포스트를 모두 호출 가능하다. 1:N으로 연결해놓았기 때문이다. 그렇다면 @celebrity는 어디서 오는가?
{%highlight ruby%}

before_action :set_celebrity, only: [:show, :edit, :update, :destroy, :goods]
    ~
private
  # Use callbacks to share common setup or constraints between actions.
  def set_celebrity
    @celebrity = Celebrity.find(params[:id])
  end
{%endhighlight%}

id로 해당 연예인을 찾는다. 아마 디테일 버튼 등을 누르면 celebrity 객체가 날라가고, 자동적으로 그는 id를 날리기 때문에(변경가능한 설정) set_celebrity action을 먼저 수행할 수 있다.
예를 들어,'celebrity_path(cb)' cb는 @celebrities.each do |cb|의 cb이다.

이제 celebrity의 show 액션으로 들어오면 프로필, 타임라인, 스케줄, ...이 있다. 이는 tab으로 넘기게 구현하였다. default는 timeline이다.
timeline, schedule는 모두 관리자가 데이터베이스를 업데이트하는 문제여서 별다른 action들을 사용할 필요가 없었다. 게시판은 직접 유저가 글을 올려야했기에,

{%highlight ruby%}
<%=link_to 'write', new_celebrity_post_path(@celebrity), class:'btn btn-success text-uppercase waves' %>
{%endhighlight%}
celebrities/celebrity_id/posts/new로 가게되어 post의 new 액션이 수행된다.

{%highlight html%}
<%= form_for(post,url: celebrity_posts_path) do |f| %>
생략.
  <div class="field">
    <%= f.hidden_field :user_id, :value => current_user.id %>
    <%= f.hidden_field :celebrity_id, :value => @celebrity.id %>
  </div>
<% end %>
{%endhighlight%}
user_id, celebrity_id가 hidden field로 전송된다. 그 값은 각각 현재 접속 유저, set_celebrity로 찾아진 해당 연예인의 id이다.
routing의 경우, helper, path의 적극 사용으로 이런 hidden field 사용이 필요 없었으나, 직접 업로드를 가능하게 해야하는 post의 특성상 이렇게 hidden field를 사용할 수 밖에 없었다.



