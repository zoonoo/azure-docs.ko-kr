---
title: Azure Mobile Engagement iOS SDK 릴리스 정보 | Microsoft Docs
description: Azure Mobile Engagement용 iOS SDK의 최신 업데이트 및 절차
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a43ff0f6-90d5-4b3c-8d7a-a1db21bc776b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/17/2017
ms.author: piyushjo
ms.openlocfilehash: 0268d65d2c0ea3cefeb8f06793838bc263e443bd
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="azure-mobile-engagement-ios-sdk-release-notes"></a>Azure Mobile Engagement iOS SDK 릴리스 정보
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 


## <a name="410-07172017"></a>4.1.0 (07/17/2017)
* 백그라운드에서 지워진 배지를 수정했습니다.
* 기본 큐에서 호출되지 않는 API에 대한 XCode 9의 경고를 수정했습니다.
* 도달률 설문에서 메모리 누수를 해결했습니다.
* iOS 6.X에 대한 지원을 삭제했습니다. 이 버전부터 응용 프로그램의 배포 대상은 iOS 7 이상이어야 합니다.

## <a name="401-12132016"></a>4.0.1 (12/13/2016)
* 백그라운드에서 로그 전달을 개선하였습니다.

## <a name="400-09122016"></a>4.0.0(09/12/2016)
* iOS 10 장치에서 알림이 작동하지 않는 문제를 해결했습니다.
* XCode 7은 더 이상 사용되지 않습니다.

## <a name="324-06302016"></a>3.2.4(2016년 6월 30일)
* 기술 로그 및 기타 로그 간 집계를 수정했습니다.

## <a name="323-06072016"></a>3.2.3(06/07/2016)
* 앱이 백그라운드에 있을 때 전달 피드백이 보고되지 않는 버그를 수정했습니다.
* 기술 로그의 전송이 최적화되었습니다.

## <a name="322-04072016"></a>3.2.2(2016/04/07)
* 경우에 따라 충돌을 일으킬 수 있는 HTTP 요청 취소에 대한 버그가 수정되었습니다.

## <a name="321-12112015"></a>3.2.1(2015/12/11)
* 딥 링크를 사용하는 알림에서 새 앱 인스턴스를 트리거할 때의 지연을 수정했습니다.

## <a name="320-10082015"></a>3.2.0(2015/10/08)
* **Xcode 7**과 연동되도록 SDK의 Bitcode를 활성화했습니다.
* 앱 내 알림 메시지와 관련된 버그를 수정했습니다.
* 배터리가 부족한 경우 및 기타 이러한 시나리오에서 앱 내 알림 메시지를 더욱 안정적으로 만들었습니다.
* 타사 라이브러리에서 생성하는 추가 콘솔 로그를 제거했습니다.

## <a name="310-08262015"></a>3.1.0(08/26/2015)
* 타사 라이브러리와 iOS 9 호환성 버그를 수정합니다. 설문 조사 결과, 응용 프로그램 정보 또는 추가 데이터를 보내는 동안 충돌을 야기합니다.

## <a name="300-06192015"></a>3.0.0(2015/06/19)
* Mobile Engagement는 자동 푸시 알림을 사용합니다.
* iOS 4.X에 대한 지원을 삭제했습니다. 이 버전부터 응용 프로그램의 배포 대상은 iOS 6 이상이어야 합니다.

## <a name="220-05212015"></a>2.2.0(05/21/2015)
* iOS 6 이전 장치에 대한 Mobile Engagement 장치 ID는 설치 시 생성된 GUID를 따릅니다.

## <a name="210-04242015"></a>2.1.0(2015/04/24)
* Swift 호환성을 추가하였습니다.
* 알림을 클릭하면 이제 응용 프로그램을 연 직후에 작업 URL이 실행됩니다.
* SDK 패키지에 누락된 헤더 파일을 추가하였습니다.
* 모바일 고객 관리 크래시 리포터를 사용할 수 없는 문제를 해결하였습니다.

## <a name="200-02172015"></a>2.0.0(2015/02/17)
* Azure Mobile Engagement의 최초 릴리스입니다.
* appId/sdkKey 구성이 연결 문자열 구성으로 바뀌었습니다.
* 임의의 XMPP 엔터티에서 임의의 XMPP 메시지를 보내고 받기 위한 API가 제거되었습니다.
* 장치 간에 메시지를 보내고 받기 위한 API가 제거되었습니다.
* 보안이 개선되었습니다.
* SmartAd 추적 기능이 제거되었습니다.
