---
layout: post
title: "rails 5 업데이트!"
date: 2016-07-21
backgrounds:
    - http://feelgrafix.com/data/background/background-9.jpg
    - https://i.ytimg.com/vi/_0NM7Ag73MM/maxresdefault.jpg
thumb: http://pic.prepics-cdn.com/fa8133cef3407/44912513.gif
categories: development work
tags: home coding matilda38.github.io
---

# 지상 최고 생산성 프레임워크 rails 파이팅!!

>**일단 영어 원문을 올려 놓습니다...언젠간 번역할거니까...**

*Rails 5.0: Action Cable, API mode, and so much more*

레일즈 5.0이 출시되었습니다. 역시 바로 바로 업그레이드 해줘야겠죠. 본 포스팅에서 제시하는 업그레이드법은 rails 4.2.0 이상에서 업그레이드 할때입니다. 전 4.2.5에서 업그레이드 했습니다. rails -v로 버전을 확인해보세요.

공식홈페이지에 따르면, 4번의 베타를 거친 끝에 드디어 5.0이 제대로 release되었다고 합니다. 가장 완전한 버전이라 확신한다고 하네요!가장 두드러진 특징 두 가지 먼저!

>Action Cable은 rails에서 web socket을 다룰때 필요한 framework라고 합니다. server-side를 위한 channel layer와 client-side를 위한 javascript layer를 이어준다고 하는데

*뭔소리다냐? 실시간 채팅이나 알림을 쉽게 해준다고 하는데, web socket을 공부해야 할듯합니다 Basecamp3는 또 뭘까요 ㅋㅋㅋ*

>action cable이 진짜 사랑스러운 건 당신의 웹 소켓 작업에서 active record와 poro에 접근할 수 있다는 것입니다.

*디비에 접근 가능하단 건가?*

>개발시에, action cable은 당신의 앱 나머지와 in process로 실행됩니다. 그걸 위해 기본 서버 환경을 webbrick에서 puma로 바꿨다고 하네요. production 시에는 action cable server가 각자 process에서 실행되도록 원할 수 있다. 그게 basecamp에서 우리가 하는 거다.

*점점 미궁에 빠지고 있습니다.*

API mode

api가 무슨 뜻인지 올해초에 알게되었고 수업을 들으면 서 많이 접하게 되었죠. Application Programming Interface로 아랫단쪽(운영체제등)에서 윗단이 아랫단 신경안쓰고 편하게 쓸수 있도록 제공하는 interface입니다. framework와 비슷한 맥락에서 나온 개념이지용.
Rails is not only a great choice when you want to build a full-stack application that uses server-side rendering of HTML templates, but also a great companion for the new crop of client-side JavaScript or native applications that just needs the backend to speak JSON. We’ve made this even clearer now with the new –api mode. If you create a new Rails application using rails new backend --api, you’ll get a slimmed down skeleton and configuration that assumes you’ll be working with JSON, not HTML.

There’s still more work to be done on this feature, but we’re off to a great start. By default, API mode just relies on #to_json calls on model classes. But you can either use Jbuilder, Active Model Serializers, or look at the new JSONAPI::Resources project for a more advanced solution.

Thanks in particular to Santiago Pastorino and Jorge Bejar for making this happen.

**다른 주요 업데이트 사항!**

이제 rake db:migrate 아니고 rails db:migrate입니다. 헷갈리셨던 분들께 좋은 소식일듯하네요!
test runner가 이제 실패를 inline으로 report하기 때문에 뭐가 문젠지 살피기 위해 suite를 완료할 필요가 없답니다.
application record라는 모든 모델들에대한 부모 class가 새로 생긴듯합니다.
ActiveRecord::Relation#in_batches makes it much easier to deal with record work in batches at a time to lessen memory overloads.
관계정의에 in_batches가 생겨서 악 뭔소리지??
Post.where(‘id = 1’).or(Post.where(‘id = 2’)) gives you exactly what you’d think!
rails console창에서 Celebrity.find_by(id:1)이렇게 했던거랑 비슷한걸까요.
예전엔 이게 안됬나? where로 id찾는거였던것 같은데??

No more accidentally halting Active Record callbacks because the last statement is false. Now you throw(:abort) explicitly!
이것도 뭔소린지...

언급되지 않은 업데이트들로 여러 changelog들이 link되어 있습니다.(mailer model record등 다양하네요)


Rails 5.0 also ships with Turbolinks 5 – the one with native iOS and Android wrapper implementations! If you’ve dismissed Turbolinks in the past,
I urge you to checkout Sam Stephenson’s RailsConf presentation: Turbolinks 5: I Can’t Believe It’s Not Native!.

rails update하기!

gemfile에

gem 'rails', '~> 5.0'
# Bundle edge Rails instead: gem 'rails', github: 'rails/rails'
해주고 bundle!

그 뒤 rails app:update를 수행해주자.

몇개 overwrite하면 update 완료!

주의 devise가 안 먹히는 문제가 있었습니다.
user.rb에
'extend Devise::Models'
gemfile에
gem 'devise', :github => 'plataformatec/devise', :branch => 'master'
로 추가 해주니 다시 되더군요 주의하시길 바랍니다.