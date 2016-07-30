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

## comment_controller.rb
{% highlight ruby%}
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

## celebrity#show

{% highlight html%}
<h2>댓글</h2>
    <div class="comments">
     <div class="comment_header">
        <h2>Comments:</h2>
     </div>
      <div class="comments_list">
       <ul id="comments_list_<%=post.id%>">
        <% if post.comments.size > 0 %>
         <%= render post.comments %>
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

_comment.html.erb

{% highlight html%}

<li id="comment_<%=comment.id %>">
  <%= comment.content%>
  <%= time_ago_in_words(comment.created_at) %> ago
  <%= link_to 'delete', [comment.post, comment], method: :delete, remote: true, data: { confirm: "Are you sure?" } %>
</li>
{%endhighlight%}


comments/_form.html.erb

{% highlight html%}

<% @comment = post.comments.new%>
<!--댓글 form-->
<div class='comments_form'>
  <div id="comments_form_<%=post.id%>">
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

create.js.erb
{% highlight js%}
<% if @comment.errors.size == 0 %>
$('<%=j render @comment %>').appendTo($("#comments_list_<%=@post.id %>")).hide().fadeIn('fast');
$("#comments_form_<%=@post.id %> form")[0].reset();
<% else %>
alert("Please submit after commenting...");
<% end %>
{%endhighlight%}

destroy.js.erb

{% highlight js%}
$("#comment_<%=@comment.id %>").slideUp('fast');
{%endhighlight%}