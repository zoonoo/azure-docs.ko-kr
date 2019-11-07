---
title: 진단 설정을 사용 하 여 Azure 활동 로그 수집 (미리 보기)-Azure Monitor | Microsoft Docs
description: 진단 설정을 사용 하 여 Azure 활동 로그를 Azure Monitor 로그, Azure storage 또는 Azure Event Hubs에 전달 합니다.
author: bwren
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 10/31/2019
ms.openlocfilehash: 9f8783dc6d3c14b086364639b60273dbae626cee
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587980"
---
# <a name="collect-azure-activity-log-with-diagnostic-settings-preview"></a>진단 설정을 사용 하 여 Azure 활동 로그 수집 (미리 보기)
[Azure 활동 로그](activity-logs-overview.md) 는 azure에서 발생 한 구독 수준 이벤트에 대 한 통찰력을 제공 하는 [플랫폼 로그](platform-logs-overview.md) 입니다. 지금까지 [이벤트 허브 또는 저장소 계정](activity-log-export.md) 에 활동 로그 항목을 보내고 커넥터를 사용 하 여 [Log Analytics 작업 영역](activity-log-collect.md)으로 수집 하는 로그 프로필을 만들었습니다.

이제 [리소스 로그](resource-logs-overview.md)를 수집 하는 데 사용 되는 것과 동일한 [진단 설정을](diagnostic-settings.md) 사용 하 여 Azure 활동 로그의 컬렉션을 구성할 수 있습니다. 진단 설정을 사용 하면 현재 방법에 비해 다음과 같은 이점이 있습니다.

- 모든 플랫폼 로그를 수집 하기 위한 일관 된 방법입니다.
- 여러 구독 및 테 넌 트에 걸쳐 활동 로그를 수집 합니다.
- 컬렉션을 필터링 하 여 특정 범주에 대 한 로그만 수집 합니다.

## <a name="considerations"></a>고려 사항
이 기능을 사용 하도록 설정 하기 전에 진단 설정을 사용 하 여 활동 로그 컬렉션의 다음 세부 사항을 고려 합니다.

- 진단 설정을 사용 하도록 설정 하기 전에 기존 작업 컬렉션을 사용 하지 않도록 설정 해야 합니다. 둘 다 사용 하도록 설정 하면 중복 된 데이터가 생성 될 수 있습니다.
- 활동 로그를 Azure storage에 수집 하는 보존 설정이 제거 되었습니다. 즉, 제거 될 때까지 데이터가 무기한으로 저장 됩니다.
- 여러 구독에서 단일 Log Analytics 작업 영역으로 활동 로그를 보낼 수 있습니다. 단일 구독에서 최대 5 개의 Log Analytics 작업 영역으로 활동 로그를 보낼 수 있습니다.

## <a name="configure-diagnostic-settings"></a>진단 설정 구성
다음 절차를 사용 하 여 Azure 활동 로그를 수집 하는 Azure Portal에서 진단 설정을 만들 수 있습니다. 현재 다른 방법을 사용 하 여 구독 수준 진단 설정을 만들 수 없습니다.

1. 활동 로그에 대해 기존 Log Analytics 작업 영역 컬렉션을 사용 하지 않도록 설정 합니다.
   1. Azure Portal에서 **Log Analytics 작업 영역** 메뉴를 열고 작업 영역을 선택 하 여 활동 로그를 수집 합니다.
   2. 작업 영역 메뉴의 **작업 영역 데이터 원본** 섹션에서 **Azure 활동 로그**를 선택 합니다.
   3. 연결을 끊을 구독을 클릭 합니다.
   4. **연결 끊기** 를 클릭 한 다음 선택 사항을 확인 하 라는 메시지가 표시 되 면 **예** 를 클릭 합니다.
2. Azure Portal **Azure Monitor** 메뉴에서 **활동 로그**를 선택 합니다.
3. **진단 설정**을 클릭합니다.
   
   ![진단 설정](media/diagnostic-settings-subscription/diagnostic-settings.png)
   
4. 레거시 환경에 대해 자주색 배너를 클릭 하 고 저장소 또는 event hubs에 대 한 현재 컬렉션을 사용 하지 않도록 설정 합니다. 

    ![레거시 환경](media/diagnostic-settings-subscription/legacy-experience.png)

5. [Azure Portal에서 진단 설정 만들기](diagnostic-settings.md#create-diagnostic-settings-in-azure-portal) 의 절차에 따라 진단 설정을 만듭니다. 활동 로그에서 이벤트를 필터링 하는 데 사용할 수 있는 범주에 대 한 설명은 [활동 로그의 범주](activity-logs-overview.md#categories-in-the-activity-log) 를 참조 하세요. 


## <a name="differences-in-data"></a>데이터의 차이점
진단 설정은 활동 로그를 수집 하는 데 사용 된 이전 메서드와 동일한 데이터를 다음과 같은 현재 차이로 수집 합니다.

다음 속성이 제거 되었습니다.

- ActivityStatus
- ActivitySubstatus 상태
- OperationName
- ResourceProvider 

추가 된 속성은 다음과 같습니다.

- Authorization_d
- Claims_d
- Properties_d

## <a name="activity-log-monitoring-solution"></a>활동 로그 모니터링 솔루션
Azure Log Analytics 모니터링 솔루션에는 Log Analytics 작업 영역에서 활동 로그 레코드를 분석 하기 위한 여러 로그 쿼리 및 뷰가 포함 되어 있습니다. 이 솔루션은 Log Analytics 작업 영역에 수집 된 로그 데이터를 사용 하며, 진단 설정을 사용 하 여 활동 로그를 수집 하는 경우 변경 없이 계속 작동 합니다. 이 솔루션에 대 한 자세한 내용은 [활동 로그 분석 모니터링 솔루션](activity-log-collect.md#activity-logs-analytics-monitoring-solution) 을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [활동 로그에 대한 자세한 내용](../../azure-resource-manager/resource-group-audit.md)
* [진단 설정에 대 한 자세한 정보](diagnostic-settings.md)
