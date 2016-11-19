---
layout: post
title: "Bot framework seminar 준비"
date: 2016-11-06
backgrounds:
    - http://feelgrafix.com/data/background/background-9.jpg
thumb: https://cdn.pixabay.com/photo/2013/02/12/09/07/microsoft-80658_960_720.png
categories: Microsoft Bot
tags: Bot
---

Bot framework 문서를 번역하면서 공부를 시작한다.

자세한 설명은
[마이크로 소프트 docs](https://docs.botframework.com)에 나와있으니 참고하시길.

## Bot Framework Overview

*MS 봇 프레임워크는 유저들이 대화 경험을 즐길수 있도록 높은 품질의 봇들을 만들고 deploy할 수 있도록 하는 framework이다.*

봇을 사용하는 개발자들은 같은 문제를 직면한다: 봇들은 기본적인 i/o를 필요로한다.

그들은 언어와 대화 스킬을 가져야 한다. 그들은 최적의 속도를 가져야하고, Responsive해야하며 확장가능해야한다. 그들은 user들과 연결되어야한다 - 이상적으로 user의 어떤 대화 경험이나 언어에. Bot Framework는 당신의 user들이 얘기하고 있는 어디에나 - 스카이프, slack, Facebook Messanger, Kik, Office 365 mail에 문자한다던가 - 자연스럽게 interact하는 똑똑한 봇들을 publish, 관리,연결 하는데 필요한 정확한 그것을 제공한다.

Bot framework는 Bot Builder SDK와 관리 Portal, Bot Directory로 이루어 진다.

# Bot Builder SDK
![](https://docs.botframework.com/en-us/images/faq-overview/bot_builder_sdk_july.png)
Bot Builder SDK는 Node-js, .NET, Rest-API 기반 봇에서 대화를 만들기에 필요한 모든 것을 제공하는, (깃허브에 의해서 호스팅된) 오픈 소스이다.

# Bot Framework Developer Portal
![](https://docs.botframework.com/en-us/images/faq-overview/developer_portal_july.png)

봇 프레임워크 개발자 포털은 당신의 bot들을 Skype등 유명 서비스에 연결될 수 있도록 한다. 간단히 당신의 봇을 등록하고, 희망되는 채널을 확인하고 봇 directory를 publish한다. 등록된 모든 봇들은 자동 확인된다.

# Bot Directory (개발중) - 다른 사람들이 개발한 봇을 사용해볼 수 있고, 내가 개발한 봇을 평가받을 수 있는 장소.
![](https://docs.botframework.com/en-us/images/faq-overview/bot_directory_july.png)
봇 Directory는 개발자 포털을 통해 등록된 봇들의 Public Directory이다. 사용자들은 봇 directory에서 그들의 선호하는 대화경험에 봇을 발견하고, 시도하고 더할 수 있을 것이다. 처음엔, Microsoft Build 2016에서 발표된 bot framework의 봇들을 게시할 것이다.


# 왜 Bot을 사용해야하는가?

대화형 User 인터페이스 시대가 도달했다. (CUI)

skype나 Facebook Messenger같은 다양한 대화 채널안에서 쏟아지는 채팅 봇들이 우리를 위해 무언가를 하도록 제안하고 있다. 이런 경험의 우선적인 interface는 email, 문자, 카드, 버튼, 음성이다. 대화형 ui는 택시를 잡고, 전자 bill을 지불하고, 친구한테 돈을 보낼 수 있게까지 해준다. Siri, Google Now, Cortana는 GUI보다 CUI가 우선적이거나, 보충할 수 있는 모바일 기기에서 특히 매일 수백만의 사람들에게 가치를 증명한다.

Bot과 대화형 비서(Agent)들은 빠르게 어떤 사람의 디지털 경험의 중요한 부분이 되고 있다. 유저들이 서비스/application과 소통하는데에서 웹사이트나 모바일 서비스만큼 필수적이 되고 있다.

Bot 프레임워크는 봇 인터페이스와 새로운 서비스를 시작하고 싶은 개발자들을 타겟으로 하고 있다.

Bot Framework를 이용함으로써, 당신은,

skype, Web 채널 자동확인,
web chat 제어를 심을 수 있고,
Bot Framework Emulator(on/offline)을 포함한 디버깅 도구,
당신의 봇을 채널 responsive하게 만들 수 있는 자동 카드 표준화,
봇을 당신의 앱에 host하는데 쓸 수 있는 Direct Line API,
언어 이해, 30개가 넘는 언어를 자동 번역을 제공하는 LUIS와 같은 cognitive service나, reflection 제공하는 formflow를 활용하여 당신의 봇을 더 똑똑하게 만드는 강력한 확장기능,

을 할 수 있다.

# Microsoft가 bot framework를 개발하는 이유

결국 플랫폼사업때문이겠지만,
“cui가 우리에게 있다”.라는 측면에서 이런 새로운 대화형 경험을 구축하는데 필요한 도구나 전문성을 가진 개발자는 드물다. 우리는 개발자들이 봇을 만들고 user에게 연결하는것을 쉽게 하기 위하여 bot framework을 만들었다.

cui에 관해 짧게 번역해봤다

1980년대에 애플이나 MS에 의해 개발된 GUI는 오늘날 대부분의 유저들이 친숙한 UI이다. 이전의 컴퓨터들은 text기반이었고 기계와의 우선적인 소통이 흑백 화면에 명령어를 타이핑하는 암호화, 고도의 코드화된 소통이었다. GUI의 등장은 컴퓨팅을 훨씬 직관적이고, 친숙하게, 또 접근이 쉽게 만들었고 우리의 모든 개인 기기의 표준이 되었다.

하지만 GUI가 단점이 없는 건 아니었다. 형편없이 디자인 된 웹사이트나 모바일 어플리케이션은 2분만에 끝날 수 있는 저녁 식사 예약을 20분 동안 싸매게 만들기도 하였다. 이제 가능한 대안을 생각해보자.

이제 우리는, 가끔은, 컴퓨터와 대화를 하는것이 우리가 tap하고 swipe하고 click을 하는 것보다 훨씬 효율적이라는 것을 알수있다. 이때 CUI의 개념이 등장한다. CUI의 가장 매력적인 특징은 대화가 가능하다는 것이다. 물론 현재는 그 통신이 사람과 사람에 한정지어져 있지만, 인공지능은 천천히 등장할 것이다. Siri는 좋은 시작이다. 하지만 아직 우리는 그녀에게 ‘말’하는 것이지, ‘대화’하는 것이다. 하지만 Siri는 분명히 우리에게 AI(인공지능)의 미래가 꽤 밝을 것이며 인공지능 기반의 Interface, CUI의 시대가 도래할 것임을 보여준다. CUI는 분명, 인간을 위한 디자인이다.

# 끝으로, Bot framework를 시작하는 방법.

1. [공식 홈페이지](https://dev.botframework.com/)에 접속하면 각종 정보를 얻을 수 있다.
2. Developer portal에 봇을 등록하기 위해서, microsoft 계정이 필요하다.
3. Tech 에 따라서 Get started 페이지가 존재한다.
[Node.js](https://docs.botframework.com/en-us/node/builder/overview/)
[.Net](https://docs.botframework.com/en-us/csharp/builder/sdkreference/)
[RestAPI](https://docs.botframework.com/en-us/csharp/builder/sdkreference/gettingstarted.html)


참고--

MS 김영욱 부장님 블로그
[Microsoft Bot Framework 시작해보기](http://blog.naver.com/PostView.nhn?blogId=warit&logNo=220833049518&parentCategoryNo=&categoryNo=&viewDate=&isShowPopularPosts=false&from=postView)