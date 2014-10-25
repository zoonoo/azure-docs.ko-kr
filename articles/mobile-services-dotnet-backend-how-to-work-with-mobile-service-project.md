<properties pageTitle="Work with a .NET backend mobile service" metaKeywords="Web API, mobile service, Azure, controllers" description="Provides examples on how to define, register, and use server-side components in Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Work with server scripts in Mobile Services" authors="glenga" solutions="mobile" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>


# .NET 백 엔드 모바일 서비스 작업

이 문서에서는 Azure 모바일 서비스에서 모바일 서비스를 정의하는 .NET 백 엔드 Visual Studio 프로젝트를 사용하는 방법에 대한 자세한 정보와 예를 제공합니다. 이 토픽의 내용은 다음과 같습니다.

+ [소개][소개]
+ [테이블 작업][테이블 작업]
	+ [방법: 테이블 작업 등록][방법: 테이블 작업 등록]
	+ [방법: 기본 응답 재정의][방법: 기본 응답 재정의]
	+ [방법: execute success 재정의][방법: execute success 재정의]
	+ [방법: 기본 오류 처리 재정의][방법: 기본 오류 처리 재정의]
	+ [방법: 사용자 지정 매개 변수 추가][방법: 사용자 지정 매개 변수 추가]
	+ [방법: 테이블 사용자 작업][방법: 테이블 사용자 작업]
+ [사용자 지정 API][사용자 지정 API]
	+ [방법: 사용자 지정 API 정의][방법: 사용자 지정 API 정의]
	+ [방법: HTTP 메서드 구현][방법: HTTP 메서드 구현]
	+ [방법: XML로 데이터 보내기 및 받기][방법: XML로 데이터 보내기 및 받기]
	+ [방법: 사용자 지정 API에서 사용자 및 헤더 작업][방법: 사용자 지정 API에서 사용자 및 헤더 작업]
	+ [방법: 사용자 지정 API에서 여러 경로 정의][방법: 사용자 지정 API에서 여러 경로 정의]
+ [작업 스케줄러][작업 스케줄러]
	+ [방법: 예약된 작업 스크립트 정의][작업 스케줄러]
+ [소스 제어, 공유 코드 및 도우미 함수][소스 제어, 공유 코드 및 도우미 함수]
	+ [방법: Node.js 모듈 로드][방법: Node.js 모듈 로드]
	+ [방법: 도우미 함수 사용][방법: 도우미 함수 사용]
	+ [방법: 소스 제어를 사용하여 코드 공유][방법: 소스 제어를 사용하여 코드 공유]
	+ [방법: 앱 설정 작업][방법: 앱 설정 작업]
+ [명령줄 도구 사용][명령줄 도구 사용]
+ [테이블 작업][1]
	+ [방법: 스크립트에서 테이블 액세스][방법: 스크립트에서 테이블 액세스]
	+ [방법: 대량 삽입 수행][방법: 대량 삽입 수행]
	+ [방법: JSON 형식을 데이터베이스 형식에 매핑][방법: JSON 형식을 데이터베이스 형식에 매핑]
	+ [Transact-SQL을 사용하여 테이블 액세스][Transact-SQL을 사용하여 테이블 액세스]
+ [디버그 및 문제 해결][디버그 및 문제 해결]
	+ [방법: 모바일 서비스 로그에 출력 쓰기][방법: 모바일 서비스 로그에 출력 쓰기]

## <a name="intro"></a> 소개

.NET 백 엔드 모바일 서비스에서 사용자 지정 비즈니스 논리를 서버에 저장 및 실행되는 JavaScript 코드로 정의할 수 있습니다. 이 서버 스크립트 코드는 다음과 같은 서버 기능 중 하나에 할당됩니다.

+ [주어진 테이블에 대한 삽입, 읽기, 업데이트 또는 삭제 작업][테이블 작업]
+ [예약된 작업][작업 스케줄러]
+ [사용자 지정 API에서 정의된 HTTP 메서드][사용자 지정 API]

서버 스크립트에서 main 함수의 서명은 스크립트가 사용되는 위치의 컨텍스트에 따라 달라집니다. 일반 스크립트 코드를 스크립트 간에 공유되는 nodes.js 모듈로 정의할 수도 있습니다. 자세한 내용은 [소스 제어 및 공유 코드][소스 제어, 공유 코드 및 도우미 함수]를 참조하세요.

개별 서버 스크립트 개체 및 함수에 대한 설명은 [모바일 서비스 서버 스크립트 참조]를 참조하세요.



<!-- Anchors. -->

[소개]: #intro
[테이블 작업]: #table-scripts
[방법: 테이블 작업 등록]: #register-table-scripts
[방법: 기본 응답 재정의]: #override-response
[방법: execute success 재정의]: #override-success
[방법: 기본 오류 처리 재정의]: #override-error
[방법: 사용자 지정 매개 변수 추가]: #access-headers
[방법: 테이블 사용자 작업]: #work-with-users
[사용자 지정 API]: #custom-api
[방법: 사용자 지정 API 정의]: #define-custom-api
[방법: HTTP 메서드 구현]: #handle-methods
[방법: XML로 데이터 보내기 및 받기]: #api-return-xml
[방법: 사용자 지정 API에서 사용자 및 헤더 작업]: #get-api-user
[방법: 사용자 지정 API에서 여러 경로 정의]: #api-routes
[작업 스케줄러]: #scheduler-scripts
[소스 제어, 공유 코드 및 도우미 함수]: #shared-code
[방법: Node.js 모듈 로드]: #modules-helper-functions
[방법: 도우미 함수 사용]: #helper-functions
[방법: 소스 제어를 사용하여 코드 공유]: #shared-code-source-control
[방법: 앱 설정 작업]: #app-settings
[명령줄 도구 사용]: #command-prompt
[1]: #working-with-tables
[방법: 스크립트에서 테이블 액세스]: #access-tables
[방법: 대량 삽입 수행]: #bulk-inserts
[방법: JSON 형식을 데이터베이스 형식에 매핑]: #JSON-types
[Transact-SQL을 사용하여 테이블 액세스]: #TSQL
[디버그 및 문제 해결]: #debugging
[방법: 모바일 서비스 로그에 출력 쓰기]: #write-to-logs
