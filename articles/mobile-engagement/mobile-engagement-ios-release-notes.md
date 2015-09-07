<properties
	pageTitle="Azure Mobile Engagement iOS SDK 릴리스 정보"
	description="Azure Mobile Engagement용 iOS SDK의 최신 업데이트 및 절차"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="MehrdadMzfr"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="08/05/2015"
	ms.author="MehrdadMzfr"/>

#릴리스 정보

##3\.1.0(08/26/2015)

-   타사 라이브러리와 iOS 9 호환성 버그를 수정합니다. 설문 조사 결과, 응용 프로그램 정보 또는 추가 데이터를 보내는 동안 충돌을 야기합니다.

##3\.0.0(2015/06/19)

-   Mobile Engagement는 자동 푸시 알림을 사용합니다.
-   iOS 4.X에 대한 지원을 삭제했습니다. 이 버전부터 응용 프로그램의 배포 대상은 iOS 6 이상이어야 합니다.

##2\.2.0(05/21/2015)

-   iOS 6 이전 장치에 대한 Mobile Engagement 장치 ID는 설치 시 생성된 GUID를 따릅니다.

##2\.1.0(2015/04/24)

-   Swift 호환성을 추가하였습니다.
-   알림을 클릭하면 이제 응용 프로그램을 연 직후에 작업 URL이 실행됩니다.
-   SDK 패키지에 누락된 헤더 파일을 추가하였습니다.
-   모바일 고객 관리 크래시 리포터를 사용할 수 없는 문제를 해결하였습니다.

##2\.0.0(2015/02/17)

-   Azure Mobile Engagement의 최초 릴리스입니다.
-   appId/sdkKey 구성이 연결 문자열 구성으로 바뀌었습니다.
-   임의의 XMPP 엔터티에서 임의의 XMPP 메시지를 보내고 받기 위한 API가 제거되었습니다.
-   장치 간에 메시지를 보내고 받기 위한 API가 제거되었습니다.
-   보안이 개선되었습니다.
-   SmartAd 추적 기능이 제거되었습니다.

<!---HONumber=August15_HO9-->