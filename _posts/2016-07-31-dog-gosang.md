---
layout: post
title: "드디어 3일간의 개고생이 끝났다."
date: 2016-07-31
backgrounds:
    - http://feelgrafix.com/data/background/background-9.jpg
thumb: https://media.timeout.com/images/100644443/image.jpg
categories: development work
tags: home work coding matilda38.github.io
---

3일동안 celebrity#show 에 게시판 만들고 그 안에 post를 만들고 또 그 아래 댓글을 ajax로 하다가 진짜 죽는줄 알았다. 땀은 줄줄 나는데 망할 놈의 404 500 에러는 진짜 계속나고...처음엔 통신문젠가 했는데 역시 코드 문제였다. 코드 문제로 골머리를 앓을땐, 특히 웹개발할 땐 터미널의 로그를 보도록 한다. 거기에 답이 있다. 에러를 반드시 표시한다.

일단 날라갈지도 모르므로...메모를...git 너무 무섭다...

## 가장 중요한 controller

지금은 이해 안될지도 모른다. 일단 잘 살펴보고 뒤에 파일들도 보도록 한다.

comment_controller.rb
{%highlight ruby%}
class CommentsController < ApplicationController
  before_action :comment_params, only: [:create]


  def create
    #1st you retrieve the post thanks to params[:post_id]
    @post = Post.find(comment_params[:post_id])
    #2nd you create the comment with arguments in params[:comment]
    @comment = @post.comments.create(comment_params)

    respond_to do |format|
      if @comment.save
        format.js
      end
    end
  end


  def destroy
    @post =Post.find(params[:post_id])
    @comment = @post.comments.find(params[:id])
      respond_to do |format|
        if @comment.destroy
        format.js
        end
      end
  end

  private
    # Use callbacks to share common setup or constraints between actions.
    def comment_params
      params.require(:comment).permit(:post_id,:celebrity_id, :user_id, :content)
    end

end
{%endhighlight%}

## view 파일

## (상세정보 -> 게시판 -> 댓글 ajax가 한화면에서 이뤄져야 한다)

*celebrity#show*

{%highlight html%}
<h2>댓글</h2>
    <div class="comments">
     <div class="comment_header">
        <h2>Comments:</h2>
     </div>
      <div class="comments_list">
       <ul id="comments_list_<%=post.id%>">
        <% if post.comments.size > 0 %>
         <%= render post.comments %> #post에 연결된 comment들이 출력된다.
        <% else %>
         <li>
         No comments
         </li>
        <% end %>
       </ul>
     </div>
    <%=render 'comments/form', post:post%>
    </div>

{%endhighlight%}

render post.comments를 보자. post와 연결된 comment들이 호출된다. 물론 post.rb에서 1:n 으로 comment를 연결해주었기 때문에 post.comments 같은 method 호출이 가능하다. 주목할 점은 render post.comments가 comment 폴더내에 있는 _comment.html.erb파일의 layout에 맞춰 모든 comment들을 출력해준다는 점이다. code를 작성하는 입장해서 @comments.each do |c| 이런 식의 문법이 필요없었으므로 매우 편리했다.
두번째 줄의 <%=render 'comments/form' post:post%> 이건 밑에 _form.html.erb를 보면서 설명하겠다.

## comment 폴더 내 파일들

*_comment.html.erb*

{%highlight html%}

<li id="comment_<%=comment.id %>">
  <%= comment.content%>
  <%= time_ago_in_words(comment.created_at) %> ago
  <%= link_to 'delete', [comment.post, comment], method: :delete, remote: true, data: { confirm: "Are you sure?" } %>
</li>
{%endhighlight%}

_comment.html.erb 파일명을 주목하자 _가 붙어있는 걸 보아 render partial로 사용할것이 짐작이 가는가? 뒤에 나오는 _form.html.erb도 같은 맥락으로 이해하면 된다.

*comments/_form.html.erb*

{%highlight html%}

<% @comment = post.comments.new%> #post에 해당하는 댓글을 새로 생성한다.
<!--댓글 form-->
<div class='comments_form'>
  <div id="comments_form_<%=post.id%>"> # 추후에 js에서 유용히 쓸 예정이다.
    <%= form_for [post, @comment], remote: true do |f| %>
        <div class="field">
          <%= f.hidden_field :user_id, :value => current_user.id %>
          <%= f.hidden_field :post_id, :value => post.id %>
          <%= f.hidden_field :celebrity_id, :value => @celebrity.id %>
        </div>

        <div class="field">
          <%= f.label :content %>
          <%= f.text_area :content, placeholder: 'Add a comment.' %>
        </div>

        <div class="actions">
          <%= f.submit %>
        </div>
    <% end %>
  </div>
</div>
{%endhighlight%}

<%=render 'comments/form' post:post%>를 기억하는가? 위에 언급했었다. celebrity#show에 작성되어 있었다. 이는 form 에서 사용하는 변수 post는 현재 우리 view 파일에서 사용하는 변수 post를 의미하는 겁니다~ 라고 이해하자. 일종의 parameter로 이해하면 되겠다.
'form_for [post, @comment], remote: true' 이 부분에 주목해야하는데 form_for 뒤에 것은 url에 관련된 것이다. 즉, posts/:post_id/comments 이고 method는 post로 던진다.
'remote true'이는 create.js.erb가 작동하게 해준다. 즉, post로 posts/:post_id/commments를 request 하므로 보통 controller에서 create 액션으로 연결되고 당연히 create.html.erb가 작동하지만 우리가 필요한건 create.js.erb이다. ajax를 통해 화면 이동 없이 댓글이 달려야 하기 때문이다. 따라서 create.js.erb로 연결가능하게 만들어 주려면
'remote: true' option이 꼭 필요하다.

create.js.erb

{%highlight js%}

<% if @comment.errors.size == 0 %>
$('<%=j render @comment %>').appendTo($("#comments_list_<%=@post.id %>")).hide().fadeIn('fast');
$("#comments_form_<%=@post.id %> form")[0].reset();
<% else %>
alert("Please submit after commenting...");
<% end %>

{%endhighlight%}

create.js.erb의 내용을 설명하자면,
<%=j render @comment%>는 js로 @comment, 즉 controller의 create action을 통해 생성된 댓글 객체의 layout을 render하고 그것을 show action view file에 id= "comment_list_<%=@post.id%>" 인 list에 추가하라는 것이다.
그리고 comment_form 은 지워진 상태로 reset한다.

destroy.js.erb

{%highlight js%}
$("#comment_<%=@comment.id %>").slideUp('fast');
{%endhighlight%}

삭제이므로, 삭제될 comment가 slideup되는 것이다. _comment.html.erb에 있는 view file를 참고하면 왜 $("#comment_<%=@comment.id %>")인지 알수 있을것이다.

{%highlight ruby%}
class CreateComments < ActiveRecord::Migration[5.0]
  def change
    create_table :comments do |t|

      # user와 post에 종속
      t.integer :user_id
      t.integer :post_id
      t.integer :celebrity_id

      t.string :content

      t.timestamps
    end
  end
end

class Comment < ApplicationRecord
  belongs_to :post
  belongs_to :user
end

{%endhighlight%}
참고로 이 두개는 기본적으로 되어있어야한다.




P.S

말이 씨가 된다더니 이 포스팅 끝나고 fetch 강제로 해서 업데이트 상황 다운 받으려다 망했다.

무섭다 정말..ㅠㅠㅠㅠㅠㅠㅠㅠ

깃 제대로 배워야겠다 진짜 ㅠㅠ