---
layout: post
title: "scaffold로 Rails application 누워서 만들기"
date: 2016-07-19
backgrounds:
    - http://feelgrafix.com/data/background/background-9.jpg
    - https://i.ytimg.com/vi/_0NM7Ag73MM/maxresdefault.jpg
thumb: https://i.ytimg.com/vi/_0NM7Ag73MM/maxresdefault.jpg
categories: development work
tags: home work coding matilda38.github.io
---

scaffold로 쉽게 rails application을 만드는 걸 접했을땐 정말 신세계였습니다...와..대박이당...!

이제 여러분께 그 경험을 나누려고 합니다.

오늘은 scaffold로 수강평가사이트를 만들어 보겠습니다! 일단 과목 정보를 담은 table, 과목에 대한 평가를 담은 evaluation table 두개가 필요합니다.

두 개의 테이블은 1:N 관계가 되겠죠.

{%highlight commandline%}
rails g scaffold subject name:string professor:string place:string semester:string
rails g scaffold evaluation title:string author:string content:text subject_id:integer
{% endhighlight %}

여기까지 하고! 이제 subject, evaluation이란 이름으로 model controller view가 동시에 생성됩니다.

각 모델마다 controller view를 갖게 되는 것이지요. 게다가 실제 코드를 보면 미리 일부 코드가 작성되어있는것을 볼 수 있습니다.

일단 아래 코드로 evaluation table 과 subject table의 관계를 정의하겠습니다. evaluation table을 먼저 봅시다

evaluation.rb
{%highlight ruby%}
belongs_to :subject

validates :name,  :presence => true
validates :title, :presence => true,
                :length => { :minimum => 5 }
{% endhighlight %}
validates 문 같은 경우에는, name을 반드시 작성, title의 경우에는 반드시 작성해야하고 길이가 최소 5자 이상이어야 한다는 조건입니다. 저 조건을 만족하여야지, evaluation table이 제대로 create/update 된다는 것입니다.

subject.rb
{%highlight ruby%}
has_many :evaluations
{% endhighlight %}

이제 controller를 보겠습니다 controller는 어떤 블로그에서 본 표현인데 정말 적절하여 빌려씁니다. "컨트롤러는 URL 요청을 들어올 때 특정 url pattern에 해당되는 메소드와 매핑하여 필요한 Model과 또는 특정 작업을 처리한 이후에 관련된 View를 랜더링시켜서 데이터와 함께 view를 호출하는 곳이다. "
크으...
{%highlight ruby%}
class SubjectsController < ApplicationController
 before_action :set_subject, only: [:show, :edit, :update, :destroy]
    # GET /subjects
    # GET /subjects.json
   def index
     @subjects = subject.all
   end

   # GET /subjects/1
   # GET /subjects/1.json
   def show
   end

   # POST /subjects
   # POST /subjects.json
   def create
     @subject = subject.new(subject_params)

     respond_to do |format|
       if @subject.save
         format.html { redirect_to @subject, notice: 'subject was successfully created.' }
         format.json { render :show, status: :created, location: @subject }
       else
         format.html { render :new }
         format.json { render json: @subject.errors, status: :unprocessable_entity }
       end
     end
   end

   # PATCH/PUT /subjects/1
   # PATCH/PUT /subjects/1.json
   def update
     respond_to do |format|
       if @subject.update(subject_params)
         format.html { redirect_to @subject, notice: 'subject was successfully updated.' }
         format.json { render :show, status: :ok, location: @subject }
       else
         format.html { render :edit }
         format.json { render json: @subject.errors, status: :unprocessable_entity }
       end
     end
   end

   # DELETE /subjects/1
   # DELETE /subjects/1.json
   def destroy
     @subject.destroy
     respond_to do |format|
       format.html { redirect_to subjects_url, notice: 'subject was successfully destroyed.' }
       format.json { head :no_content }
     end
   end

 private
    # Use callbacks to share common setup or constraints between actions.
    def set_subject
      @subject = subject.find(params[:id])
    end

    # Never trust parameters from the scary internet, only allow the white list through.
    def subject_params
      params.fetch(:subject, {})
    end

end
{% endhighlight %}

보시면 알겠지만 코드가 다 미리 짜져있습니다..헉! scaffold란 이런것입니다 여러분!

기본적인 crud가 구현이 되어있고 주석처리 되어 있는 부분은 기본 routing 되어있는 것입니다. 가장 첫줄의
'before_action :set_subject, only: [:show, :edit, :update, :destroy]'의 경우에는 해당 action 수행 전에 set_subject 액션을 먼저 수행해라. 즉, 밑에 보시면 알겠지만, id로 먼저 해당 과목을 찾고 @subject에 집어 넣는다. 그렇다면 params[:id]는 어디서 오는가? 바로 view 파일에서 옵니다.
{% highlight html%}
        <% @timelines.each do |timeline| %>
               <li>
                  <time class="cbp_tmtime" datetime="<%=timeline.created_at%>"><span><%=timeline.date%></span><span><%=timeline.time%></span></time>
                  <div class="cbp_tmicon cbp_tmicon-screen"></div>
                  <div class="cbp_tmlabel">
                  <h2><%=timeline.title%></h2>
                  <p><%=timeline.content%></p>
                </div>
                </li>
              <% end %>

{% endhighlight %}

