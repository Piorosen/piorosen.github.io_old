---
layout: post
title: 객체 지향 프로그래밍과, 디자인 패턴 적용 후기
author: piorosen
tags: [design-pattern, oop, mvc, event-driven, callback]
hide_title: false
---

# 개발론
다양한 프로젝트를 개발하면서 유지보수와 수정안이 발생했을 때 빠르게 대응을 할 수 있어야 했습니다. 특히 외주를 진행하면서 짧은 기간안에 여러번 시안이 변경이 될 수 있는데 그 때 객체지향과 디자인 패턴이 정말 중요하게 적용이 되었습니다. 그래서 개발을 하더라도 유연하게 변경이 될 수 있도록 개발을 해야 했습니다.

# 그래서 어떻게 개발 하는가?

## 객체지향이 아니라 프로젝트 지향!
저는 기능을 객체별로 나누는 것이 아닌, 완전히 별개의 시스템이 있다는 프로젝트로 먼저 나눕니다. 예를 들어서 카메라와 GPIO 통신을 하며, 설정을 보여주는 GUI 환경 까지 있다고 한다면 객체가 3개인것이 아닌, 프로젝트가 3개로 나눕니다.

> 카메라 프로젝트
> GPIO 통신 프로젝트
> GUI 프로젝트

기능을 프로젝트 별로 나눌 경우 장점으로는 어떠한 환경이든지 독립적으로 사용이 가능하고, 다른 프로젝트에 이식을 할 때 간편 합니다. 특히 독립적으로 사용이 가능해야 하기 때문에 환경적으로 기능이 나눠지기 떄문에 컴파일이나 코드를 작성할 때 부터 이건 뭔가.. 코드가 안이쁜데..? 란 생각으로 실수를 줄일 수 있었습니다. 그래서 무언가 프레임워크를 구현할 때 계층별로 구현 하기 쉬워집니다.

단점으로는 수직적인 환경은 쉽지만 수평적인 환경을 구성하기 위해서 중간 매개체인 어댑터나, 부모 프로젝트를 경유해서 주고 받아야 하기 때문에 번거로움이 존재한다. 프로젝트 지향 개발은 살짝쿵 이지만 Go언어나, JAVA 처럼 도메인 주도 개발을 자주 하다 보니 생긴 병이지 않을까 싶습니다.

## 기능별이 아닌 추상화 단계를 만들자!

기능별로 구현하게 될 경우 데이터를 수신하는 클래스, 데이터를 저장하는 클래스등 덩어라가 커지게 되면서 자연스럽게 수정이 불가능한 레거시 코드로 바뀔 가능성이 매우 높습니다. 그렇기 때문에 레거시 코드가 되더라도 하나의 규칙을 만들어 교체, 수정이 쉽게 되도록 해야합나디ㅏ. 특히 저는 개발이 끝나고 1년 6개월이 지난 프로젝트를 관에서 꺼내 다시 프로젝트를 진행하게 되는 경험을 겪게 되었습니다. 수정안은 HILS 기반 해양시추장비 통합 운용제어 시스템에서 발생하는 로그 데이터를 파일로 저장하고 있었는데 파일이 아닌 NoSQL인 몽고DB로 이전하는 것 이였습니다. 특히 로그를 몽고DB에 저장을 하는 것이 아닌, 로그를 다시 읽어 리플레이 하는 기능이 있기 때문에 복잡한 구현이 있을것이라 생각하였습니다.

그렇지만 추상화 단계를 만들어 구현 한 결과 데이터를 파일 형식으로 저장 하던 것을 데이터베이스로 변경은 매우 쉬웠으며, 리플레이 기능을 구현하는것도 단 코드 50줄 정도 작성을 하면 해결이 되었습니다.

제가 나눈 추상화의 단계는 HILS 시스템에서 발생하는 로그를 수신 하는 클래스와, 데이터 저장, 데이터 읽기, 리플레이로 클래스가 모두 나눠져 있었기 때문에 데이터를 읽고, 쓰는 모듈을 DB에 쓰기만 하면 나머지는 알아서 기존 형태를 유지하면 되는 것 이였습니다.

기능의 레벨에 따라 확실히 나누자! 만약 장비와 소통을 한다면 총 3개의 클래스로 구현이 될 것 입니다.

> 1. 해당 장비와 직접적으로 통신하는 클래스
> 2. 해당 장비의 데이터를 분석한 뒤 객체로 만들어주는 ORM 객체
>> ORM : Object Relational Mapping
> 3. 객체를 받아서 UI나 Controller에 보내 데이터 가공

이때 총 3개의 클래스로 나누게 되고, 각 계층별 중간마다 추상화가 필요하다면 Interface를 통해 추상화 할 수 있습니다. 특히 장비와 통신 하는 부분은 네트워크가 될 수 있으며, 시리얼을 통해 넘어오는 데이터가 될 수 있습니다. 그 반대로 데이터를 수신 하는 클래스가 3개가 있다면 송신하는 클래스도 3개가 됩니다.

# 유지보수 중요한가?

실제 개발을 진행해보면서 디자인 패턴이 왜 존재하는지 알게 되었습니다. 처음에는 단순하게 유지보수를 간편하게 하기 위해서 필요하다고 들었었지만 유지보수의 중요성을 느끼지 못했습니다. 그렇지만 5년짜리 프로젝트를 진행하고 1년 쉬고 이어받으려고 하니 코드가 기억이 나지 않고, 파일을 DB 로 바꿔야하는 등 예상치 못한 변경점이 있다보니 기능을 나누더라도 확실하게 나눠야겠다는 생각을 하게 되었습니다. 유지보수가 크게 중요하지 않는 개인프로젝트는 상관없지만 기업용 프로젝트는 5년에서 10년 갈 수 있기 때문에 기능별로 나누게 되면 기능이 바뀌게 되거나 새로운 내용이 나오게 되면 유연하지 못한 코드가 될 수 있기 때문에 개념으로 추상화하고, 객체로 만들어야지 유지보수가 쉽게 될 수 있습니다.

# 어떻게 분리 하는게 좋을까?

저는 C#과 Swift에 영향을 상당히 많이 받았습니다. C#은 Event Driven Develop 즉 이벤트 주도 개발이며, Swift는 콜백함수, 함수 포인터를 적극적으로 기용하는 모습입니다. 즉 기존에는 객체간 통신을 하기 위해 함수를 호출하여 반환값을 받았지만 이벤트나 콜백 함수를 이용하게 될 경우 데이터 발행을 통해 다른 객체에 전파하기 때문에 독립적으로 사용할 수 있습니다.

해당 구조를 정말 좋은 구조로 구현이 된 예제로는 ReactiveX와 Pub-Sub 구조인 이벤트 주도 개발이 있습니다. Event를 직접 구현하기 어렵다면 Callback을 이용하여 구현하는것도 좋습니다. Callback의 좋은 예시로는 SwiftUI나, React, Flutter, NodeJS등등 다양한 언어가 채택하고 있습니다. 해당 방식의 장점으로는 데이터 발행의 주체가 다른 클래스나 객체가 아닌 자기 자신이 직접 데이터 제어권을 가지고 있기 때문에 무언가 다른 요소와 관계를 가지지 않습니다.

# 마무리

그러므로 객체지향은 최고이고, 개념적인 추상화가 최곱니다. 이걸 호출 하면 어떻게든 된다가 보장이 된다면 수천, 수만줄이여도 OK이고, 굳이 저 코드를 수정하지 않고 해당 객체를 새롭게 만들면 됩니다. 그러나, 수정이 자주 일어난다면 기능을 또 추상화하고 클래스로 나눠버리는게 제일 최곱니다.

추상화는? 개념적으로!
