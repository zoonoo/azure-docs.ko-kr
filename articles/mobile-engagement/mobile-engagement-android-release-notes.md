---
title: "Azure Mobile Engagement Android SDK 통합"
description: "Azure Mobile Engagement용 Android SDK의 최신 업데이트 및 절차"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 585341f9-3f39-459a-af42-864e400a0128
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 03/30/2017
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: e50c1c70a4378560f8ce3182069cba4832fa4d22
ms.lasthandoff: 03/31/2017


---
# <a name="release-notes"></a>릴리스 정보

## <a name="424-03302017"></a>4.2.4 (03/30/2017)
* Android 7의 앱 내 알림 텍스트 색을 이전 Android 버전과 동일하게 수정합니다.

## <a name="423-08102016"></a>4.2.3 (08/10/2016)
* 더 이상 WIFI 잠금은 없습니다.
* Init 이전에 getDeviceId를 호출할 경우 교착 상태를 해결합니다.(4.2.0에 유입된 버그)

## <a name="422-05172016"></a>4.2.2(2016/05/17)
* 안정성 향상

## <a name="421-05102016"></a>4.2.1(2016/05/10)
* 보안: 웹 보기 로컬 파일 액세스를 비활성화합니다.
* 보안: 사용되지 않고 보호되지 않는 `PreferenceActivity` 클래스를 확장하는 `EngagementPreferenceActivity` 클래스를 제거합니다.
* 보안: 도달률 활동에서 현재 `exported="false"`을(를) 사용하도록 문서화되어 있으며 이 플래그는 이전 SDK 버전에서 사용할 수 있습니다.

## <a name="420-03112016"></a>4.2.0(2016/03/11)
* SDK는 이제 MIT에 따라 사용이 허가됩니다.
* SDK 초기화 시에 사용자 지정 장치 식별자를 지정할 수 있습니다.

## <a name="415-02012016"></a>4.1.5(2016/02/01)
* 안정성 향상

## <a name="414-01262016"></a>4.1.4(2016/01/26)
* 안정성 향상

## <a name="413-1292015"></a>4.1.3(2015/12/9)
* 안정성 향상

## <a name="412-11252015"></a>4.1.2(2015/11/25)
* 안정성 향상

## <a name="411-11042015"></a>4.1.1(2015/11/04)
* 안정성 향상

## <a name="410-08252015"></a>4.1.0(08/25/2015)
* Android M에 대한 새 권한 모델을 다룹니다.
* `AndroidManifest.xml`을 사용하는 대신 이제 런타임 시 위치 기능을 구성할 수 있습니다.
* 사용 권한 버그 수정: `ACCESS_FINE_LOCATION`을 사용하는 경우 `ACCESS_COARSE_LOCATION`이 더 이상 필요하지 않습니다.
* 안정성 향상

## <a name="400-07062015"></a>4.0.0(07/06/2015)
* 내부 프로토콜이 분석을 수행하고 더 안정적으로 푸시하도록 변경됩니다.
* 네이티브 푸시(GCM/ADM)가 이제 앱 알림에도 사용되므로 모든 푸시 캠페인 유형에 대한 네이티브 푸시 자격 증명을 구성해야 합니다.
* 큰 그림 알림을 수정합니다. 푸시된 후 10초만 표시되었습니다.
* 웹 보기에서 버그 수정: 링크를 클릭하면 기본 작업 URL도 실행되었습니다.
* 로컬 저장소 관리와 관련된 드문 충돌을 수정합니다.
* 동적 구성 문자열 관리를 수정합니다.
* EULA를 업데이트합니다.

## <a name="300-02172015"></a>3.0.0(2015/02/17)
* Azure Mobile Engagement의 최초 릴리스입니다.
* appId 구성이 연결 문자열 구성으로 대체됩니다.
* 임의의 XMPP 엔터티에서 임의의 XMPP 메시지를 보내고 받기 위한 API가 제거되었습니다.
* 장치 간에 메시지를 보내고 받기 위한 API가 제거되었습니다.
* 보안이 개선되었습니다.
* Google Play 및 SmartAd 추적을 제거했습니다.


