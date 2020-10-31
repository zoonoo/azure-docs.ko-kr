---
title: 몰입 형 판독기 SDK 릴리스 정보
titleSuffix: Azure Cognitive Services
description: 몰입 형 판독기 JavaScript SDK의 새로운 기능에 대해 자세히 알아보세요.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 10/12/2020
ms.author: dylankil
ms.openlocfilehash: 6b041916c6fa446732e95d49391d9ead63eb1b17
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93133596"
---
# <a name="immersive-reader-javascript-sdk-release-notes"></a>몰입 형 판독기 JavaScript SDK 릴리스 정보

## <a name="version-110"></a>버전 1.1.0

이 릴리스에는 새로운 기능, 보안 취약점 수정, 버그 수정, 코드 샘플 업데이트 및 구성 옵션이 포함 되어 있습니다.

#### <a name="new-features"></a>새로운 기능

* 다양 한 브라우저 및 장치에서 사용자 기본 설정을 저장 하 고 로드할 수 있습니다.
* 기본 표시 옵션 구성 사용
* 몰입 형 판독기를 시작할 때 번역 언어를 설정 하 고, 단어 번역을 활성화 하 고, 문서 번역을 사용 하도록 설정 하는 옵션 추가
* 옵션을 통한 소리내어 읽기 구성 지원 추가
* 첫 실행 환경을 사용 하지 않도록 설정 하는 기능 추가
* UWP 용 ImmersiveReaderView 추가

#### <a name="improvements"></a>개선 사항

* 최신 SDK를 사용 하도록 Android 코드 샘플 HTML 업데이트
* 처리 된 문자 수를 반환 하는 시작 응답 업데이트
* V 1.1.0를 사용 하도록 코드 샘플 업데이트
* 이미 로드 될 때 launchAsync가 호출 되는 것을 허용 하지 않습니다.
* 데이터가 문자열이 아닌 메시지를 무시 하 여 잘못 된 내용이 있는지 확인 합니다.
* If 절의 창에 대 한 호출을 래핑하여 브라우저의 약속 지원 확인

#### <a name="fixes"></a>수정 프로그램

* .Gitignore에서 yarn를 제거 하 여 dependabot을 수정 합니다.
* 빠른 시작-nodejs 코드 샘플에서 pug를 v 3.0.0로 업그레이드 하 여 보안 취약성 해결
* Jest 및 TypeScript 패키지를 업그레이드 하 여 여러 보안 취약점 해결
* System.identitymodel. ActiveDirectory를 v 5.2.0로 업그레이드 하 여 보안 취약성을 해결 합니다.

<br>

## <a name="version-100"></a>Version 1.0.0

이 릴리스에는 주요 변경 내용, 새로운 기능, 코드 샘플 기능 및 버그 수정이 포함 되어 있습니다.

#### <a name="breaking-changes"></a>주요 변경 내용

* Azure AD 토큰과 하위 도메인 및 이전 버전에서 사용 되는 지지 토큰을 요구 합니다.
* CookiePolicy를 disabled로 설정 합니다. 사용자 기본 설정의 보존은 기본적으로 사용 되지 않습니다. CookiePolicy이 사용으로 설정 되어 있지 않으면 판독기는 항상 기본 설정으로 시작 됩니다.

#### <a name="new-features"></a>새로운 기능

* 쿠키를 사용 하거나 사용 하지 않도록 지원 추가
* Android Kotlin 빠른 시작 코드 샘플 추가
* Android Java 빠른 시작 코드 추가 샘플
* Node.js 빠른 시작 코드 샘플 추가

#### <a name="improvements"></a>개선 사항

* Node.js advanced README.md 업데이트
* 고급에서 빠른 시작으로 Python 코드 샘플 변경
* IOS Swift 코드 샘플을 js/samples로 이동
* V 1.0.0을 사용 하도록 코드 샘플 업데이트

#### <a name="fixes"></a>수정 프로그램

* Node.js 고급 코드 샘플에 대 한 수정
* 고급-csharp-다중 리소스에 대해 누락 된 파일 추가
* 하이퍼링크에서 en-us 제거

<br>

## <a name="version-003"></a>버전 0.0.3

이 릴리스에는 새로운 기능, 코드 샘플에 대 한 향상 된 기능, 보안 취약점 수정, 버그 수정이 포함 되어 있습니다.

#### <a name="new-features"></a>새로운 기능

* IOS Swift 코드 샘플 추가
* 여러 리소스 사용을 보여 주는 c # 고급 코드 샘플 추가 
* 전체 화면 토글 기능을 사용 하지 않도록 설정 하는 지원 추가
* 몰입 형 판독기 응용 프로그램 종료 단추를 숨기기 위한 지원 추가
* 몰입 형 판독기를 종료할 때 호스트 응용 프로그램에서 사용할 수 있는 콜백 함수를 추가 합니다.
* Azure Active Directory 인증을 사용 하도록 코드 샘플 업데이트

#### <a name="improvements"></a>개선 사항

* Word 문서를 포함 하도록 c # 고급 코드 샘플 업데이트
* V 0.0.3를 사용 하도록 코드 샘플 업데이트

#### <a name="fixes"></a>수정 프로그램

* Lodash를 버전 4.17.14로 업그레이드 하 여 보안 취약성 해결
* C # MSAL 라이브러리를 업데이트 하 여 보안 취약성 해결
* 1.3.2를 버전으로 업그레이드 하 여 보안 취약성을 해결 합니다.
* 취약 한 버전의 집합 값과 mixin을 사용 하 여 보안 취약성을 해결 한 jest, webpack 및 webpack-cli를 업그레이드 합니다.

<br>

## <a name="version-002"></a>버전 0.0.2

이 릴리스에는 새로운 기능, 코드 샘플에 대 한 향상 된 기능, 보안 취약점 수정, 버그 수정이 포함 되어 있습니다.

#### <a name="new-features"></a>새로운 기능

* Python 고급 코드 샘플 추가
* Java 빠른 시작 코드 추가 샘플
* 간단한 코드 샘플 추가

#### <a name="improvements"></a>개선 사항

* 도메인 이름을 cogSvcsSubdomain 도메인으로 바꿉니다.
* 코드에서 비밀을 이동 하 고 환경 변수 사용
* V 0.0.2를 사용 하도록 코드 샘플 업데이트

#### <a name="fixes"></a>수정 프로그램

* 몰입 형 판독기 단추 접근성 버그 수정
* 분할 된 스크롤 수정
* Handlebars package를 version 4.1.2로 업그레이드 하 여 보안 취약성 해결
* SDK 단위 테스트의 버그를 수정 합니다.
* JavaScript Internet Explorer 11 호환성 버그를 수정 합니다.
* SDK url 업데이트

<br>

## <a name="version-001"></a>버전 0.0.1

몰입 형 판독기 JavaScript SDK의 초기 릴리스입니다.

* 몰입 형 판독기 JavaScript SDK 추가
* UI 언어를 지정 하는 지원 추가
* 시간 초과를 추가 하 여 launchAsync 함수에서 시간 초과 오류가 발생 하는 시기를 결정 합니다.
* 몰입 형 판독기 iframe의 z 인덱스를 지정 하는 지원을 추가 합니다.
* Chrome 앱과의 호환성을 위해 iframe 대신 웹 보기 태그를 사용 하는 지원을 추가 합니다.
* SDK 단위 테스트 추가
* Node.js 고급 코드 샘플 추가
* C # 고급 코드 샘플 추가
* C # 빠른 시작 코드 샘플 추가
* 패키지 구성, Yarn 및 기타 빌드 파일 추가
* Git 구성 파일 추가
* 코드 샘플 및 SDK에 README.md 파일 추가
* MIT 라이선스 추가
* 참가자 지침 추가
* 정적 아이콘 단추 SVG 자산 추가

## <a name="next-steps"></a>다음 단계

몰입형 판독기로 시작:

* [몰입형 리더 클라이언트 라이브러리 참조](./reference.md) 읽기
* [GitHub의 몰입형 리더 클라이언트 라이브러리](https://github.com/microsoft/immersive-reader-sdk) 살펴보기
