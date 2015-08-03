<properties 
	pageTitle="Windows 유니버설 SDK 개요" 
	description="Azure Mobile Engagement의 Windows 유니버설 SDK 개요" 									
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="piyushjo" />

#Azure Mobile Engagement의 Windows 유니버설 SDK 개요

이 문서에서는 Windows 유니버설 앱에 Azure Mobile Engagement를 통합하는 방법에 대한 세부 정보를 확인할 수 있습니다. 먼저 통합을 연습해 보려면 [15분 자습서](mobile-engagement-windows-store-dotnet-get-started.md)를 완료합니다.

[SDK 콘텐츠](mobile-engagement-windows-store-sdk-content.md)를 보려면 클릭하세요.

##통합 절차

1. 시작: [Windows 유니버설 앱에서 Mobile Engagement를 통합하는 방법](mobile-engagement-windows-store-integrate-engagement.md)

2. 알림: [Windows 유니버설 앱에서 도달률(알림)을 통합하는 방법](mobile-engagement-windows-store-integrate-engagement-reach.md)

3. 태그 계획 구현: [Windows 유니버설 앱에서 고급 Mobile Engagement 태깅 API를 사용하는 방법](mobile-engagement-windows-store-use-engagement-api.md)

##릴리스 정보

###3.1.0(05/21/2015)

-   이제 Mobile Engagement 장치 ID는 설치 시 생성된 GUID를 따릅니다.

이전 버전에 대한 내용은 [전체 릴리스 정보](mobile-engagement-windows-store-release-notes.md)를 참조하세요.

##업그레이드 절차

이전 버전의 Engagement를 응용 프로그램에 이미 통합한 경우에는 SDK를 업그레이드할 때 다음 사항을 고려해야 합니다.

여러 SDK 버전을 건너뛴 경우에는 여러 절차를 수행해야 하는 경우, 전체 [업그레이드 절차](mobile-engagement-windows-store-upgrade-procedure.md)를 참조하세요. 예를 들어 0.10.1에서 0.11.0으로 마이그레이션하는 경우에는 먼저 "0.9.0에서 0.10.1로 마이그레이션" 절차를 수행한 후에 "0.10.1에서 0.11.0으로 마이그레이션" 절차를 수행해야 합니다.

###2.0.0에서 3.0.0으로

#### 리소스
이 단계는 사용자 지정된 리소스에만 관련됩니다. SDK(html, 이미지, 오버레이)에서 제공되는 리소스를 사용자 지정한 경우 업그레이드된 리소스에서 사용자 지정한 내용을 업그레이드 및 다시 적용하기 전에 백업해야 합니다.

### 이전 버전에서 업그레이드

[업그레이드 절차](mobile-engagement-windows-store-upgrade-procedure/)를 참조하세요.

<!---HONumber=July15_HO4-->