---
layout: post
title: iOS 어플리케이션 외주 개발 중 기술 및 내부 정리
author: piorosen
tags: [ios, swift, database, stt, webview, video-player, submodule, git, outsourcing, material, design]
hide_title: false
---

# 프로젝트 소개
2021년 11월 부터 2022년 1월 까지 개발일정이 있는 외주를 진행 하였었다. 개발 내용은 현재 개발이 되어있는 어플리케이션의 내용 확장 및 시스템 개선 이였다. 기존 개발 하던 업체는 그만 두고, 다른 업체를 찾던 중 우리 학교가 맡게 되면서 우리팀이 개발하게 되었다.

혼자서 디자인과 PM을 모두 진행하기에는 어려움이 있어, 디자인과 시스템 설계는 [황진주(MFDO)](https://github.com/oMFDOo), 안드로이드 어플리케이션 개발은 [대학교 동기(Lee0701)](https://github.com/Lee0701) 가 맡게 되었다.

외주의 개발 범위는 외부의 데이터베이스, 서버를 활용하지 않고 스마트폰에서 독립적으로 동작하는 Stand-Alone(스탠드얼론) 프로그램으로 개발이였다. 그렇다 보니 회원가입이나, 로그인 정보는 프로그램 내부에 저장 해야 했다.

# 팀원 구성

팀원 정해져 있는게 아닌, 매번 하고 싶은 사람을 모집을 하는 형식으로 이뤄지고 있다. 그렇다 보니 팀원 모집을 할 때 필수적인 요소로 실제 개발 경험과, git을 사용 해본 학우로 팀을 꾸리고자 했다. 

그렇다 보니 [황진주(MFDO)](https://github.com/oMFDOo)는 과거 유림아이엔에스에서 웹페이지 개발과 디자인 진행을 맡았던 경험이 있던 친구에게 이야기를 해서 같이 개발을 하게 되었다. [대학교 동기(Lee0701)](https://github.com/Lee0701)는 코틀린, 자바스크립트를 이용해서 웹 서버나 안드로이드 어플을 만들어 출시한 경험이 있는 친구가 참여하게 되었다.

이렇게 모두가 실제 원하는 프로젝트를 만들어 완성한 경험이 있으며, git을 이용해 소스 코드를 관리 경험이 있는 학우로 팀을 꾸렸다.

# Git 프로젝트 관리

프로젝트 개발 할 때 같이 개발 하므로 하나의 Organization이 필요 했다. 그래서 [Organization](https://github.com/lebengrida)을 하나 만들어서 총 4개의 Repository를 만들어서 각각의 데이터를 관리 했다.

> 1. Document/Design/Schedule 목적
> 2. iOS 어플
> 3. AOS 어플
> 4. iOS, AOS 공용 데이터

첫번째 Repository는 업체와 미팅, 디자인 결과, issue나 여러가지를 활용해 스케줄 관리에 활용했다.

두번째와 세번째 Repository(이하 레포지토리)는 iOS와 aos 어플리케이션 개발을 할 때 형상 관리를 위해서 사용이 되었다.

네번째 레포지토리는 iOS와 aos 에서 공통으로 사용이 되는 데이터를 한 군데 모아서 데이터를 저장 했다. 그래서 iOS, AOS 레포지토리에 submodule을 통하여 데이터 연결을 하여 중복되는 데이터 수정을 않고, 한 군데에서 수정을 하면 모든 레포지토리에 적용이 되게끔 하였다.

그 외에 매우 큰 데이터(동영상) 경우는 학생용 OneDrive를 통하여 데이터 관리 하였다.

# 안드로이드와 아이폰 디자인 구현

처음 개발 기획 할 때 안드로이드와 아이폰을 Flutter, Xamarin, React Native와 같이 크로스 플랫폼으로 개발하는 것이 아닌, "각각의 운영체제에 맞는 네이티브"란 주제로 진행이 되었기 때문에 UI와 이미지 리소스나 각각의 OS에 맞게 디자인을 해야 했다.

단적인 예로는 Android에서는 Toast라는 명령을 통해 상태 출력이 가능하지만, iOS에서는 Toast라는 UI 자체가 없어 라이브러리를 사용해야 하는 등 다양한 통합적으로 UI 기획이 필요 했다.

그래서 UI에 적용되는 이미지 리소스의 경우는 Google의 Material Design을 하기로 해서 이미지의 형식, 표준을 정했다.

[![이미지?](https://lh3.googleusercontent.com/0NzGA34i5BEsHtf7WEv6yMySTpbVmsPAoBAE60bwgDflHHolSTGY_43BvEJxcX0BLOBZTu4UPdORAE_iM5Wb7HwWQ0vldbsZhW-asHY7FA6k-uQ1RM4=w760-h380)](https://fonts.google.com/icons)

### 그래프 처리는 어떻게 했는가?

이미지나, 영상 부분은 공용 데이터를 저장하는 레포지토리에서 정의를 하면 크게 문제가 없지만 소프트웨어적으로 구현 해야하거나, 동적으로 변화가 되는 그래프, 차트 경우에는 다르게 처리를 해야 했다. 그래서 차트 경우에는 어떻게 해야지 모두가 편하고, 빠르게 개발이 될 수 있는가를 고민을 하다가 웹뷰를 나타낸다면 많이 편하지 않을까? 라는 생각을 하게 되었고, 현재 운용중이고 사용 중인 현재 이 블로그에 특정 url로 들어오게 된다면 그래프가 나타나는 형식으로 구현하였다.

[![이미지!?!](/assets/img/post/2022-01-18-image.png)](https://blog.udon.party/assets/lebengrida/graph.html?a1=2&a2=3&b=7&c=2&d=8)

특히 github.io 서비스는 POST나 다양한 메서드를 지원하는 것이 아닌 오직 GET 메서드만 지원 하므로 URL 주소에서 GET 을 요청하면 그림을 그리게 하였다. 그렇지만 웹 서비스를 이용하게 되면 결국 외부 서비스와 연동이 되는 문제이므로 네트워크가 연결이 되어야 한다는 점이 있지만, 해당 어플리케이션이 고도화가 된다면 대부분 서비스는 서버로 올라가게 될 예정이므로, 큰 문제가 되진 않았다. 현재 어플은 고도화 되기 전인, 서버 연동 기능만 제외한 나머지 기능 구현이다.

# 내부적인 개발된 내용

> 1. 동영상 재생 기능
> 2. 음성 인식 기능
> 3. 회원가입 및 게시글 작성, 문제 풀기와 같은 검사 기능

### 동영상 재생 기능

동영상 재생 기능은 BMPlayer란 라이브러리를 사용했다. 하지만 BMPlayer 자체가 구버전 라이브러리 이였기 때문에 현재 사용중인 패키지 매니저인 SwiftPM을 지원하지 않아 Fork를 한 뒤 코드 구조를 바꾸고 SwiftPM 형식으로 만들어서 적용하였다.

[![뀨뿌ㅃ!](https://github.com/BrikerMan/resources/raw/master/BMPlayer/demo.gif)](https://github.com/Piorosen/BMPlayer)

물론 BMPlayer을 사용 할 때 일반 화면인 Portrait 상태에서 Landscape로 전체화면이 일어날 때 하단바와 상단바가 지워지지 않아 문제가 발생했다. 그렇지만 SnapKit을 이용하여 동적으로 Constraints를 직접 추가하여 해결하였다.

이때 안 사실은 AOS나 iOS는 동영상을 "재생"하는 것은 기본적으로 라이브러리가 제공이 되지만 "재생" 외에 UI로 재생이 되는 부분은 직접 구현해야하는 내용을 알게 되었다. 그렇지만 빠른 구현을 위해 안드로이드와 아이폰 둘 다 라이브러리를 사용하다 보니, 동영상 재생 부분만은 UI가 자유로 작성이 되었다.

# Speach to Text 기술 구현

기존 어플리케이션에서 고도화 작업을 할 때 기존 어플이 음성을 녹음 한 뒤 서버에 전송하여 음성 인식 하는 기술을 사용하고 있었다. 하지만 인수인계 받은건 기존 어플에 관한 코드만 있었기 때문에 서버를 건드는것이 불가능했다. 원래 계약 상 서버를 구현하지 않기로 되어있었기 때문에 서버를 구현하지 않는게 맞긴 하다. 하지만 기존 어플에서는 동작하던 내용이 똑같이 구현이 되야한다는 조건 때문에 음성 인식 기술을 적용해야 했다. 그래서 기존 어플의 코드를 확인하니 음성을 녹음 한 뒤 서버에 전송만 하면 결과 값이 나왔기 때문에 구현하기가 나름 수월 했다.

# 내부 데이터 베이스

내부적으로 로그인, 회원가입, 검사 결과 표시, 문제 풀기와 같은 다양한 기능이 있었기 때문에 필수적으로 데이터 저장와 구조를 설계해야 했다. 정적 데이터는 공유하고 있지만 데이터를 어떻게 저장할지 설계는 자유로 정하다 보니 안드로이드는 SQLite를 이용하여 데이터베이스를 구축 하였다. 하지만 나의 경우는 아이폰에서 물론 SQLite나, Realms와 같은 다양한 ORM 라이브러리가 있지만 Text형식으로 제공이 되면서 배열이 가능한 NoSQL을 사용하였다.

말이 NoSQL이지 실제로는 JSON 형식으로 모든 데이터를 관리 했다. 잦은 변경에도 유연한 변경, 배열의 사용 가능 유무 덕분에 많이 편리했다.

# 느낀점

1. 비 개발자와의 소통, 개발 회의르 진행 할 때 예상 외로 많은 내용을 이야기를 해야한다.
2. 다음 부터는 기획 또는 개발 명세서를 정확하게 짚은 다음에 하는게 옳고 백번 옳다.
3. 외주를 진행 할 때는 기획부터 이상하면 참여하지 않는게 맞다고 본다.
4. 일을 시작 할 때는 무조건 계약서 부터 읽고 개발 명세서를 작성하고 개발 해야한다.
5. 개발 명세서 작성은 개발 기간에 포함해서 기간 산정을 해야한다.

정말로 많은 것을 겪은 외주이다.