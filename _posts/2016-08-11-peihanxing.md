---
layout: post
title: "peihanxing!"
date: 2016-08-11
backgrounds:
    - http://feelgrafix.com/data/background/background-9.jpg
thumb: http://pic.prepics-cdn.com/fa8133cef3407/44912513.gif
categories: development work
tags: home coding gokathon
---

지난 고카톤 때 peihanxing 기획하고 진행했던 것을 기록하려한다. 개인적으로 우수상까지 수상하게 되어 너무 기뻤다.

peihanxing의 경우에는 이름 궁합을 보는 것이다. 특이한 점은 그것을 한자로 보는 것인데, 한자의 획수로 세서 이름 궁합을 세는 시스템이다. 무한도전에서 나왔던 이름궁합 특집에서 따온 아이디어이다.

{%highlight ruby%}
require 'open-uri'
require 'nokogiri'

class HomeController < ApplicationController

  def index
    @profile = Profile.all
    if params[:profile_id] != nil
      @celebrity= Profile.find(params[:profile_id])
      @img=@celebrity.cel_url
      @name=@celebrity.cel_name
    end
  end

  def select
    @count =0
    @profile = Profile.all
  end

  def show
    @imgupload= Imageup.new(image: params[:image])

    @stro = Array.new
    @stro2 = Array.new
    url = "http://dict.baidu.com/s?wd="+params[:ch1].encode('utf-8')+"&device=pc&from=home&q="+params[:ch1].encode('utf-8')
    url = URI.escape (url)
    doc = Nokogiri::HTML(open(url))
    @st_counts = doc.xpath('//div[@id="stroke_count"]/span')

    @st_counts.each do |stroke|
        @stro.push(stroke.content)
    end
    url = "http://dict.baidu.com/s?wd="+params[:ch2].encode('utf-8')+"&device=pc&from=home&q="+params[:ch2].encode('utf-8')
    url = URI.escape (url)
    doc = Nokogiri::HTML(open(url))
    @st_counts = doc.xpath('//div[@id="stroke_count"]/span')

    @st_counts.each do |stroke|
        @stro.push(stroke.content)
    end
    url = "http://dict.baidu.com/s?wd="+params[:ch3].encode('utf-8')+"&device=pc&from=home&q="+params[:ch3].encode('utf-8')
    url = URI.escape (url)
    doc = Nokogiri::HTML(open(url))
    @st_counts = doc.xpath('//div[@id="stroke_count"]/span')

    @st_counts.each do |stroke|
        @stro.push(stroke.content)
    end

    @chinese1 = params[:ch1] #ch1은 input name으로 index.html에 있는거
    @chinese2 = params[:ch2] #chinese1은 view파일 안에 출력하는 용
    @chinese3 = params[:ch3]
#--------------------------------------------------------------------------------------------------------------------------------------
    url = "http://dict.baidu.com/s?wd="+params[:your_cel][0].encode('utf-8')+"&device=pc&from=home&q="+params[:your_cel][0].encode('utf-8')
    url = URI.escape (url)
    doc = Nokogiri::HTML(open(url))
    @st_counts = doc.xpath('//div[@id="stroke_count"]/span')

    @st_counts.each do |stroke|
        @stro2.push(stroke.content)
    end
    url = "http://dict.baidu.com/s?wd="+params[:your_cel][1].encode('utf-8')+"&device=pc&from=home&q="+params[:your_cel][1].encode('utf-8')
    url = URI.escape (url)
    doc = Nokogiri::HTML(open(url))
    @st_counts = doc.xpath('//div[@id="stroke_count"]/span')

    @st_counts.each do |stroke|
        @stro2.push(stroke.content)
    end
    url = "http://dict.baidu.com/s?wd="+params[:your_cel][2].encode('utf-8')+"&device=pc&from=home&q="+params[:your_cel][2].encode('utf-8')
    url = URI.escape (url)
    doc = Nokogiri::HTML(open(url))
    @st_counts = doc.xpath('//div[@id="stroke_count"]/span')

    @st_counts.each do |stroke|
        @stro2.push(stroke.content)
    end

    @your_cel1 = params[:your_cel][0]
    @your_cel2 = params[:your_cel][1]
    @your_cel3 = params[:your_cel][2]

    @celebrity=Profile.find(params[:id])

#-----------calculate-------------------------
    for i in 0..2
      @stro[i] = Integer(@stro[i])
      @stro2[i] = Integer(@stro2[i])
    end
    @next_stro = Array.new
    @next_stro.push((@stro[0]+@stro2[0]) % 10)
    @next_stro.push((@stro2[0]+@stro[1]) % 10)
    @next_stro.push((@stro[1]+@stro2[1]) % 10)
    @next_stro.push((@stro2[1]+@stro[2]) % 10)
    @next_stro.push((@stro[2]+@stro2[2]) % 10)

    @next_next_stro = Array.new
    for i in 0..3
      @next_next_stro.push((@next_stro[i]+@next_stro[i+1])%10)
    end
    @next3_stro = Array.new
    for i in 0..2
      @next3_stro.push((@next_next_stro[i]+@next_next_stro[i+1])%10)
    end
    @final_stro = Array.new
    @final_stro.push((@next3_stro[0]+@next3_stro[1])%10)
    @final_stro.push((@next3_stro[1]+@next3_stro[2])%10)

    @percent = 10*@final_stro[0]+@final_stro[1]
  end

  def calculate
  end
end

{%endhighlight%}

{%highlight ruby%}
	<!--form tag(이미지 업로드 포함) 시작-->
			<%=form_tag('/show', multipart: true) do %>
			<div class="col-xs-6">
				<a href="/select">
				<input class="form-control" name="your_cel" type="text" value="<%=@name%>" required> </a>
			</div>
		</div>

		<div class="row">
			<div class="col-xs-4 name">
				 我的名字
			</div>
			<div class="col-xs-2">
				<input class="form-control" name="ch1" type="text" maxlength="1" size="3" required>
			</div>
			<div class="col-xs-2">
				<input class="form-control" name="ch2" type="text" maxlength="1" size="3" required>
			</div>
			<div class="col-xs-2">
				<input class="form-control" name="ch3" type="text" maxlength="1" size="3">
			</div>
			<%if @celebrity !=nil%>
				<%= hidden_field_tag 'id', @celebrity.id%>
				<!--<input type="hidden" name="id" value="<%#@celebrity.id%>">-->
			<%end%>
		</div>
		<hr>
		<div class="row" align="center">
			<%if @img !=nil%>
				<img class="img-circle" src="<%=@img%>" style="width:100px;">
			<%else%>
				<img class="img-circle" src="/img/male.png" style="width:100px;">
			<%end%>
		<img src="/img/question.png">
		<img id="blah" class="img-circle" style="width:100px; height:100px;" src="#" alt=""/>

			<div style="padding-top:10px;">
				<div class="field">
					<%=label_tag :image %><br>
					<%=file_field_tag :image, onchange: 'readURL(this)', accept: 'image/png,image/gif,image/jpeg,image/jpg' %>
				</div>
				<div class="actions">
					<%=submit_tag('提交', class: 'btn btn-warning') %>
				</div>
			<%end%>
			</div>
		</div>
		<hr>
		<button type="button" class="btn btn-warning btn-circle btn-lg" onclick="myFunction()"><i class="fa fa-question" aria-hidden="true"></i></button>
		<div id="demo" style="font-size:20px;">
		</div>
	</div>
</div>

<script>
function myFunction() {
    var mybr = document.createElement('br');
    document.getElementById("demo").innerHTML =" 1. 姓名配对是为了玩笑，通过两个人姓名的笔画数中个位数字的合计来，分析两个人的缘分指数。 <br\/>2. 配韩星是韩式姓名配对测试，用笔画数来分析缘分指数。";
}
function readURL(input) {
        if (input.files && input.files[0]) {
            var reader = new FileReader();
            reader.onload = function (e) {
                $('#blah')
                    .attr('src', e.target.result)
                    .width(100)
                    .height(100);
            };
            reader.readAsDataURL(input.files[0]);
        }
    }
</script>

{%endhighlight%}