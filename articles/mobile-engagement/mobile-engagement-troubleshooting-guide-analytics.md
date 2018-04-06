---
title: Azure Mobile Engagement 문제 해결 가이드 - 분석
description: Azure Mobile Engagement에서 분석, 모니터링, 구분 및 대시보드 문제 해결
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 04a7020a-ad74-4491-be69-0bd574890029
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: b19d018b83ee8b3d5848d29afff190d3dcaf3fde
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="troubleshooting-guide-for-analytics-monitoring-segmentation-and-dashboard-issues"></a>분석, 모니터링, 구분 및 대시보드 문제에 대한 문제 해결 가이드
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

다음은 Azure Mobile Engagement에서 응용 프로그램, 장치 및 사용자에 대한 정보를 수집하는 방법과 관련해서 발생할 수 있는 문제입니다.

## <a name="missingdelayed-information"></a>정보 누락/지연
### <a name="issue"></a>문제
* 분석, 구분 또는 대시보드에서 정보 표시가 지연됩니다.
* 모니터링에서 정보가 누락됩니다.
* 분석, 구분 또는 대시보드에서 정보가 누락됩니다.
* 구분 제한에 도달합니다.

### <a name="causes"></a>원인
* 분석 API, 모니터 API 및 세그먼트 API를 사용하여 UI에서 누락된 데이터가 API에서는 표시되는지 확인할 수 있습니다.
* Azure Mobile Engagement SDK가 앱에 올바르게 통합되어 있지 않으면 분석, 구분, 모니터링 또는 대시보드에서 정보를 볼 수 없습니다.
* 세그먼트는 작성하고 나면 변경할 수 없으며 "복제"(복사) 또는 "소멸"(삭제)만 가능합니다. 세그먼트는 기준을 10개만 포함할 수 있습니다.
* 모니터링에서 정보가 누락되었는지 테스트하는 가장 효율적인 방법은 테스트 장치를 설정한 다음 해당 장치에서 앱을 제거 및/또는 다시 설치하는 것입니다.
* 분석, 구분 또는 대시보드의 경우 정보는 24시간마다 갱신됩니다.
* 세그먼트가 이전 정보를 기준으로 하더라도 새 세그먼트의 정보는 작성된 후 24시간 동안 표시되지 않을 수 있습니다.
* UI에서 분석 데이터를 필터링하면 앱 버전에 관계없이 해당 형식의 모든 예제가 표시됩니다. 예를 들어 "크래시"를 이름으로 필터링하면 앱 버전 1과 2의 예제가 모두 표시됩니다.
* 분석 기간은 사용자 장치 설정의 날짜를 기준으로 하므로 휴대폰에 날짜가 잘못 설정된 사용자의 경우 잘못된 기간의 데이터가 표시됩니다.
* 푸시를 "테스트"하는 단추를 사용할 때는 서버 쪽 데이터가 기록되지 않으며 실제 푸시 캠페인에 대해서만 데이터기 기록됩니다.

## <a name="cant-locate-items-in-ui"></a>UI에서 항목을 찾을 수 없음
### <a name="issue"></a>문제
* 특정 기본 제공 앱 또는 사용자 지정 앱의 정보 태그를 기준으로 세그먼트를 만들 수 없습니다.
* 분석, 모니터링 또는 대시보드에서 특정 기본 제공 앱 또는 사용자 지정 앱의 정보 태그 기준을 찾을 수 없습니다.
* 분석, 모니터링, 구분 또는 대시보드에서 데이터를 해석할 수 없습니다.

### <a name="causes"></a>원인
* 일부 기본 제공 항목 및 앱 정보 태그는 푸시 기준으로만 제공되며 분석, 모니터링 또는 대시보드에서 보거나 세그먼트에 추가하지는 못할 수도 있습니다. 
* 세그먼트에 추가할 수 없는 기본 제공 항목 및 앱 정보 태그의 경우 세그먼트 기준 대상 지정과 같은 기능을 수행하도록 각 캠페인에서 대상 기준 목록을 설정해야 합니다.
* 자세한 도움말과 방법 정보는 Azure Mobile Engagement UI에서 분석, 모니터링, 구분 또는 대시보드 섹션의 상황에 맞는 메뉴를 참조하세요.

## <a name="crash-troubleshooting"></a>작동 중단 문제 해결
### <a name="issue"></a>문제
* 분석, 모니터링 또는 대시보드에서 응용 프로그램 작동 중단 현상이 나타납니다.

### <a name="causes"></a>원인
* 분석, 모니터링 또는 대시보드에서 나타나는 응용 프로그램 작동 중단 문제를 해결하려면 릴리스 정보에서 이전 버전 SDK의 알려진 문제를 확인하세요.
* 응용 프로그램 작동 중단 문제를 추가로 해결하려면 응용 프로그램이 설치된 테스트 장치에서 이벤트를 수행한 다음 Azure Mobile Engagement UI의 "모니터 - 이벤트" 섹션에서 장치 ID를 조회합니다. 그런 다음 응용 프로그램 작동 중단의 원인이 되는 이벤트를 수행하여 Azure Mobile Engagement UI의 "모니터 - 이벤트" 섹션에서 추가 정보를 조회합니다. 

