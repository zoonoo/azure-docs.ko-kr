---
title: "Windows 유니버설 앱 SDK 콘텐츠"
description: "Azure Mobile Engagement용 Windows 유니버설 앱 SDK의 콘텐츠에 대해 알아봅니다."
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 8fa1b701-1c2b-4aec-940c-06c974ef5405
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ea47cba55c9689e23eb3592ae2559649d3377055
ms.lasthandoff: 11/17/2016


---
# <a name="windows-universal-apps-sdk-content"></a>Windows 유니버설 앱 SDK 콘텐츠
이 문서는 응용 프로그램에서 SDK가 배포한 콘텐츠를 나열하고 설명합니다.

## <a name="the-resources-folder"></a>`/Resources` 폴더
이 폴더에는 Mobile Engagement에 필요한 모든 리소스가 포함되어 있습니다. 앱에 맞게 리소스를 사용자 지정할 수도 있습니다.

* `EngagementConfiguration.xml` : Mobile Engagement 구성 파일입니다. 여기에서 Mobile Engagement 설정을 사용자 지정할 수 있습니다(Mobile Engagement 연결 문자열, 충돌 보고서...).

### <a name="html-folder"></a>/html 폴더
* `EngagementNotification.html` : 앱 내 배너에 대한 `Notification` 웹 보기 HTML 디자인입니다.
* `EngagementAnnouncement.html` : 앱 내 중간 보기에 대한 `Announcement` 웹 보기 HTML 디자인입니다.

### <a name="images-folder"></a>/images 폴더
* `EngagementIconNotification.png` : 알림 왼쪽에 표시되는 브랜드 아이콘이며, 이것을 브랜드 아이콘으로 대체합니다.
* `EngagementIconOk.png` : 도달률 콘텐츠 페이지의 작업 또는 유효성 검사 단추용 `Ok` 아이콘입니다.
* `EngagementIconNOK.png` : 도달률 콘텐츠 페이지의 유효성 검사 단추를 사용하지 않도록 설정하면 `NOK` 아이콘이 사용됩니다.
* `EngagementIconClose.png` : 해제 단추에 대한 도달률 알림 및 콘텐츠용 `Close` 아이콘입니다.

### <a name="overlay-folder"></a>/overlay 폴더
* `EngagementPageOverlay.cs` : 오버레이 페이지는 자식에 Engagement 도달률 앱 내 UI를 추가합니다.


