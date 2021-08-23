---
title: 몰입형 리더 SDK 릴리스 정보
titleSuffix: Azure Applied AI Services
description: 몰입형 리더 JavaScript SDK의 새로운 기능에 대해 자세히 알아봅니다.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 10/12/2020
ms.author: dylankil
ms.openlocfilehash: 833066839b597e52c1aa0e965f959c2c81e4ab85
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566755"
---
# <a name="immersive-reader-javascript-sdk-release-notes"></a>몰입형 리더 JavaScript SDK 릴리스 정보

## <a name="version-110"></a>버전 1.1.0

이 릴리스에는 새로운 기능, 보안 취약성 수정, 버그 수정, 코드 샘플 업데이트, 구성 옵션이 포함되어 있습니다.

#### <a name="new-features"></a>새로운 기능

* 여러 가지 브라우저 및 디바이스에서 사용자 기본 설정을 저장하고 로드할 수 있도록 설정
* 기본 표시 옵션을 구성할 수 있도록 설정
* 몰입형 리더를 시작할 때 번역 언어를 설정하고, 단어 번역을 사용하도록 설정하고, 문서 번역을 사용하도록 설정하는 옵션 추가
* 옵션을 통해 소리 내어 읽기를 구성하는 것에 대한 지원 추가
* 첫 실행 환경을 사용하지 않도록 설정하는 기능 추가
* UWP용 ImmersiveReaderView 추가

#### <a name="improvements"></a>향상 된 기능

* 최신 SDK를 사용하도록 Android 코드 샘플 HTML 업데이트
* 처리된 문자 수를 반환하도록 시작 응답 업데이트
* v1.1.0을 사용하도록 코드 샘플 업데이트
* 이미 로드가 진행 중일 때는 launchAsync가 호출되는 것을 허용하지 않음
* 문자열이 아닌 데이터가 있는 메시지를 무시하여 잘못된 콘텐츠 확인
* If 절에서 창에 대한 호출을 래핑하여 브라우저가 Promise를 지원하는지 확인

#### <a name="fixes"></a>수정 프로그램

* gitignore에서 yarn.lock을 제거하여 dependabot 수정
* 빠른 시작-nodejs 코드 샘플에서 pug를 v3.0.0으로 업그레이드하여 보안 취약성 해결
* Jest 및 TypeScript 패키지를 업그레이드하여 여러 개의 보안 취약성 해결
* Microsoft.IdentityModel.Clients.ActiveDirectory를 v5.2.0으로 업그레이드하여 보안 취약성 해결

<br>

## <a name="version-100"></a>Version 1.0.0

이 릴리스에는 호환성이 손상되는 변경, 새로운 기능, 코드 샘플 개선, 버그 수정이 포함되어 있습니다.

#### <a name="breaking-changes"></a>주요 변경 내용

* Azure AD 토큰 및 하위 도메인이 필요하고 이전 버전에서 사용된 토큰은 더 이상 사용되지 않습니다.
* CookiePolicy를 disabled로 설정합니다. 사용자 기본 설정의 보존은 기본적으로 사용하지 않도록 설정되어 있습니다. CookiePolicy가 enabled로 설정되지 않으면 리더는 매번 기본 설정으로 시작됩니다.

#### <a name="new-features"></a>새로운 기능

* 쿠키를 사용하도록 설정하거나 사용하지 않도록 설정하는 지원 추가
* Android Kotlin 빠른 시작 코드 샘플 추가
* Android Java 빠른 시작 코드 샘플 추가
* Node.js 빠른 시작 코드 샘플 추가

#### <a name="improvements"></a>향상 된 기능

* Node.js 고급 README.md 업데이트
* 고급에서 빠른 시작으로 Python 코드 샘플 변경
* iOS Swift 코드 샘플을 js/samples로 이동
* v1.0.0을 사용하도록 코드 샘플 업데이트

#### <a name="fixes"></a>수정 프로그램

* Node.js 고급 코드 샘플에 대한 수정
* advanced-csharp-multiple-resources에 대해 누락된 파일 추가
* 하이퍼링크에서 en-us 제거

<br>

## <a name="version-003"></a>버전 0.0.3

이 릴리스에는 새로운 기능, 코드 샘플에 대한 개선, 보안 취약성 해결, 버그 수정이 포함되어 있습니다.

#### <a name="new-features"></a>새로운 기능

* iOS Swift 코드 샘플 추가
* 여러 개의 리소스 사용을 보여 주는 C# 고급 코드 샘플 추가 
* 전체 화면 토글 기능을 사용하지 않도록 설정하는 지원 추가
* 몰입형 리더 애플리케이션 종료 단추를 숨기기 위한 지원 추가
* 몰입형 리더 종료 시 호스트 애플리케이션에서 사용할 수 있는 콜백 함수 추가
* Azure Active Directory 인증을 사용하도록 코드 샘플 업데이트

#### <a name="improvements"></a>향상 된 기능

* Word 문서를 포함하도록 C# 고급 코드 샘플 업데이트
* v0.0.3을 사용하도록 코드 샘플 업데이트

#### <a name="fixes"></a>수정 프로그램

* lodash를 버전 4.17.14로 업그레이드하여 보안 취약성 해결
* C# MSAL 라이브러리를 업데이트하여 보안 취약성 해결
* mixin-deep을 버전 1.3.2로 업그레이드하여 보안 취약성 해결
* set-value 및 mixin-deep의 취약한 버전을 사용 하던 jest, webpack, webpack-cli를 업그레이드하여 보안 취약성 해결

<br>

## <a name="version-002"></a>버전 0.0.2

이 릴리스에는 새로운 기능, 코드 샘플에 대한 개선, 보안 취약성 해결, 버그 수정이 포함되어 있습니다.

#### <a name="new-features"></a>새로운 기능

* Python 고급 코드 샘플 추가
* Java 빠른 시작 코드 샘플 추가
* 간단한 코드 샘플 추가

#### <a name="improvements"></a>향상 된 기능

* resourceName을 cogSvcsSubdomain으로 이름 변경
* 코드 밖으로 비밀을 이동하고 환경 변수 사용
* v0.0.2를 사용하도록 코드 샘플 업데이트

#### <a name="fixes"></a>수정 프로그램

* 몰입형 리더 단추 접근성 버그 수정
* 끊어진 스크롤 수정
* handlebars 패키지를 버전 4.1.2로 업그레이드하여 보안 취약성 해결
* SDK 단위 테스트의 버그 수정
* JavaScript Internet Explorer 11 호환성 버그 수정
* SDK URL 업데이트

<br>

## <a name="version-001"></a>버전 0.0.1

몰입형 리더 JavaScript SDK의 초기 릴리스입니다.

* 몰입형 리더 JavaScript SDK 추가
* UI 언어를 지정하는 지원 추가
* launchAsync 함수에서 시간 제한 오류로 인해 실패하게 되는 때를 결정하는 시간 제한 추가
* 몰입형 리더 iframe의 z 인덱스를 지정하는 지원 추가
* Chrome 앱과의 호환성을 위해 iframe 대신 webview 태그를 사용하는 지원 추가
* SDK 단위 테스트 추가
* Node.js 고급 코드 샘플 추가
* C# 고급 코드 샘플 추가
* C# 빠른 시작 코드 샘플 추가
* 패키지 구성, Yarn, 기타 빌드 파일 추가
* Git 구성 파일 추가
* 코드 샘플 및 SDK에 README.md 파일 추가
* MIT 라이선스 추가
* Contributor 지침 추가
* 정적 아이콘 단추 SVG 자산 추가

## <a name="next-steps"></a>다음 단계

몰입형 판독기로 시작:

* [몰입형 리더 클라이언트 라이브러리 참조](./reference.md) 읽기
* [GitHub의 몰입형 리더 클라이언트 라이브러리](https://github.com/microsoft/immersive-reader-sdk) 살펴보기
