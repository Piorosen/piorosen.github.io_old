---
layout: post
title: OAuth2.0과 JWT를 활용한 회원가입 구현
author: piorosen
tags: [jwt, sms, oauth, secure, regist]
hide_title: false
---

# 개요

2022년 4월 언저리부터 개발에 시작하게 된 족민의 달배 프로젝트의 이야기 입니다. 아무튼 족민의 달배라는 프로젝트를 진행하면서, 자체 회원가입 시스템을 구축하는 것은 보안적인 위협이나, 번거로움이 존재 할것 같아, 타 업체의 OAuth2.0을 이용하고, OAuth2.0에서 나온 토큰 값을 이용하여 자동적인 로그인 및 세션 관리를 하고자 하였습니다. 사용자 입장에서는 최초 1회 회원가입을 진행하면, 그 이후에는 로그인 버튼을 누르기만 하면 자동적으로 로그인이 처리가 됩니다. 그래서 OAuth2.0을 이용하고, 회원가입까지 안정적으로 어떻게 할것인지 생각하고, 만들게 되었습니다.

# OAuth2.0 이란?

간단하게 이야기를 하자면 사용자가 Naver나, Kakao와 같은 로그인 서버에 거치고, 인증 코드를 발급 받고 API 서버에 요청하여 Access Token을 받아 내부적으로 로그인이나, API를 호출하는 방식 입니다. 저의 구현 방법으로는 아래와 같습니다.

> 1. Client가 OAuth2.0 서버에 접속하여 코드를 발급 받음. <br>
> 2. Client가 발급받은 OAuth2.0 토큰을 자체 로그인 서버에 전달 Access Token을 발급 받음<br>
> 3. Access Token을 보고 회원가입, 메인 페이지로 라우팅을 함.<br>
>> 3-1. 회원가입 할 때, 사용자 정보, Access Token, SMS 관련 토큰 3가지를 입력하면 회원가입에 성공함<br>
>> 3-2. 회원가입에 성공하면 메인페이지로 라우팅함.<br>
> 4. 메인 화면 관련 API<br>

# 어떻게 회원가입을 구현 하였는가?

![이미지](/assets/img/post/2022-08-16-schedule.png)
스케줄러 방식
{: .text-center } 

모든 행위에 대한 관측이나, 추적을 할 수 있습니다. Client가 OAuth2.0으로 발급받은 내용으로, Login 서버에 요청하여 Access Token을 발급 받고, 해당 Access Token을 확인 한 뒤 회원가입할지, 메인 페이지로 이동할지 라우팅을 합니다. 이러한 것이 가능한 이유는 발급받은 Access Token이 JWT 형식이기 때문에 JWT에서 Payload 값을 읽어 회원가입인지, 아닌지 확인 할 수 있습니다. 회원가입이 아닌 상태의 JWT 값과, 값이 유효한 상태인 값이 다르므로 비회원 상태에서 발급받은 Access Token을 메인 화면에서 사용하더라도 오류가 발생합니다.

# 마무리

회원가입을 진행 할 때 SMS 인증이 필수적이다 보니, SMS 인증 기능을 넣어야만 했습니다. 그래서 SMS 인증 요청 하면 JWT 값을 발급 받고, SMS 인증을 할 땐, JWT와 함께 발급 코드를 전송하면, 성공 JWT를 발급 받습니다. 그리고 SMS 인증 성공 JWT와 함께, OAuth2.0때 발급 받았던, Access Token과 함께, 회원가입 정보를 전송하면 성공적으로 회원가입이 되는 과정을 구현 하였습니다. 물론 이 과정에서 JWT가 아닌, RSA나, AES와 같이 암호화를 이용할 수 있습니다.
