---
title: "Azure Portal에서 작업 그룹 만들기 및 관리 | Microsoft Docs"
description: 
author: anirudhcavale
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 9a290422bf897397942fccf2e3733d0709701e91
ms.lasthandoff: 03/31/2017


---
# <a name="create-and-manage-action-groups-in-azure-portal"></a>Azure Portal에서 작업 그룹 만들기 및 관리
## <a name="overview"></a>개요 ##
이 문서에서는 Azure Portal에서 작업 그룹을 만들고 관리하는 방법을 보여 줍니다.

작업 그룹을 사용하여 수신자 목록을 구성할 수 있습니다. 그런 후 활동 로그 경고를 정의할 때 이러한 그룹을 사용할 수 있습니다. 예를 들어 활동 로그 경고가 트리거될 때 특정 작업 그룹이 알림을 받도록 할 수 있습니다.

하나의 작업 그룹에는 각 작업 유형이 최대 10개 포함될 수 있습니다. 작업은 다음을 조합해서 정의합니다.

**이름:** 작업 그룹 내의 고유 식별자입니다.  
**작업 유형:** 수행되는 작업을 정의합니다. 옵션으로는 SMS 보내기, 전자 메일 보내기 또는 웹후크 호출이 있습니다.  
**세부 정보:** 작업 유형에 따라 해당 전화 번호, 전자 메일 주소 또는 웹후크 URI를 제공해야 합니다.

다음을 사용하여 서비스 상태 알림 경고에 대한 정보를 구성하고 가져올 수 있습니다.
* [Azure 포털](monitoring-action-groups.md)
- [Resource Manager 템플릿](monitoring-create-action-group-with-resource-manager-template.md)

## <a name="creating-an-action-group-for-the-azure-portal"></a>Azure Portal에 대한 작업 그룹 만들기 ##
1.    [포털](https://portal.azure.com)에서 **모니터** 서비스로 이동합니다.

    ![모니터](./media/monitoring-action-groups/home-monitor.png)
2.    **모니터** 옵션을 클릭하여 모니터 블레이드를 엽니다. 이 블레이드에서는 모든 모니터링 설정과 데이터를 하나의 통합 보기로 모읍니다. 처음에는 **활동 로그** 섹션이 열립니다.

3.    이제 **작업 그룹** 섹션을 클릭합니다.

    ![Action-Group](./media/monitoring-action-groups/action-groups-blade.png)
4.    **추가** 작업 그룹 명령을 입력하고 필드를 채웁니다.

    ![Add-Action-Group](./media/monitoring-action-groups/add-action-group.png)
5.    작업 그룹의 **이름** 및 **약식 이름**을 제공합니다. 약식 이름은 이 그룹에 전송된 알림에서 참조됩니다.

      ![Action-Group-Define](./media/monitoring-action-groups/action-group-define.png)

6.    **구독**은 작업 그룹이 저장되는 위치입니다. 현재 작동 중인 구독으로 자동으로 채워집니다.

7.    이 작업 그룹에 대한 **리소스 그룹**을 선택합니다.

8.    그런 후 다음을 조합해서 작업 목록을 정의합니다.
  1. **이름:** 작업 그룹 내의 고유 식별자입니다.
  2. **작업 유형:** 수행되는 작업을 정의합니다. 옵션으로는 SMS 보내기, 전자 메일 보내기 또는 웹후크 호출이 있습니다.
  3. **세부 정보:** 작업 유형에 따라 해당 전화 번호, 전자 메일 주소 또는 웹후크 URI를 제공해야 합니다.

9.    작업 그룹을 다 만들었으면 **확인**을 선택합니다.

## <a name="managing-your-action-groups"></a>작업 그룹 관리 ##
작업 그룹을 만들면 모니터 서비스의 작업 그룹 섹션에 표시됩니다. 관리하려는 작업 그룹을 선택하면 다음 작업을 수행할 수 있습니다.
* 수신기를 추가, 편집 또는 제거합니다.
-    작업 그룹을 삭제합니다.

## <a name="next-steps"></a>다음 단계: ##
[SMS 경고 동작](monitoring-sms-alert-behavior.md)에 대해 자세히 알아보기  
[활동 로그 경고 웹후크 스키마의 이해](monitoring-activity-log-alerts-webhook.md) 확인  
경고의 [속도 제한](monitoring-alerts-rate-limiting.md)에 대해 자세히 알아보기  
[활동 로그 경고의 개요](monitoring-overview-alerts.md)를 확인하고 알림을 받는 방법 알아보기  
[서비스 상태 알림이 게시될 때마다 경고를 구성하는](monitoring-activity-log-alerts-on-service-notifications.md) 방법

