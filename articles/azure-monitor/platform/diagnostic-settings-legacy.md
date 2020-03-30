---
title: 진단 설정으로 Azure 활동 로그 수집 - Azure 모니터 | 마이크로 소프트 문서
description: 진단 설정을 사용하여 Azure 활동 로그를 Azure 모니터 로그, Azure 저장소 또는 Azure 이벤트 허브로 전달합니다.
author: bwren
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: 6d4c724c7cfb4c1779f0fc6592a7e61e060755b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096900"
---
# <a name="update-to-azure-activity-log-collection-and-export"></a>Azure 활동 로그 컬렉션 및 내보내기업데이트
[Azure 활동 로그는](platform-logs-overview.md) Azure에서 발생한 구독 수준 이벤트에 대한 통찰력을 제공하는 [플랫폼 로그입니다.](platform-logs-overview.md) 활동 로그 항목을 [이벤트 허브 또는 저장소 계정](activity-log-export.md) 또는 [로그 분석 작업 영역으로](activity-log-collect.md) 보내는 방법이 [진단 설정을](diagnostic-settings.md)사용하도록 변경되었습니다. 이 문서에서는 진단 설정으로 변경하기 위해 레거시 설정을 지우는 방법과 메서드간의 차이점에 대해 설명합니다.


## <a name="differences-between-methods"></a>메서드 간의 차이점

### <a name="advantages"></a>장점
진단 설정을 사용하면 현재 방법에 비해 다음과 같은 장점이 있습니다.

- 모든 플랫폼 로그를 수집하는 일관된 방법.
- 여러 구독 및 테넌트와 의 활동 로그를 수집합니다.
- 특정 범주에 대한 로그만 수집하도록 컬렉션을 필터링합니다.
- 모든 활동 로그 범주를 수집합니다. 일부 범주는 레거시 메서드를 사용하여 수집되지 않습니다.
- 로그 생성을 위한 더 빠른 대기 시간. 이전 방법은 약 15분 의 대기 시간을 가지며 진단 설정은 약 1분만 추가합니다.

### <a name="considerations"></a>고려 사항
이 기능을 활성화하기 전에 진단 설정을 사용하여 활동 로그 컬렉션의 다음 세부 정보를 고려하십시오.

- Azure 저장소에 활동 로그를 수집하기 위한 보존 설정이 제거되어 데이터를 제거할 때까지 데이터가 무기한 저장됩니다.
- 현재 Azure 포털을 사용하여 구독 수준 진단 설정만 만들 수 있습니다. PowerShell 또는 CLI와 같은 다른 방법을 사용하려면 리소스 관리자 템플릿을 만들 수 있습니다.


### <a name="differences-in-data"></a>데이터 차이
진단 설정은 활동 로그를 수집하는 데 사용된 이전 방법과 동일한 데이터를 수집하며 다음과 같은 현재 차이점을 가지고 있습니다.

다음 열이 제거되었습니다. 이러한 열의 대체는 다른 형식이므로 열을 사용하는 로그 쿼리를 수정해야 할 수 있습니다. 스키마에서 제거된 열이 계속 표시될 수 있지만 데이터로 채워지지는 않습니다.

| 제거된 열 | 대체 열 |
|:---|:---|
| 활동 상태    | ActivityStatusValue    |
| 활동하위 상태 | 활동하위 상태값 |
| OperationName     | 작업 이름값     |
| ResourceProvider  | 리소스 공급자값  |

다음 열이 추가되었습니다.

- Authorization_d
- Claims_d
- Properties_d

> [!IMPORTANT]
> 경우에 따라 이러한 열의 값은 모든 대문자에 있을 수 있습니다. 이러한 열이 포함된 쿼리가 있는 경우 [=~ 연산자(=~ 연산자)를](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) 사용하여 대/소문자를 구분하지 않는 비교를 수행해야 합니다.

## <a name="work-with-legacy-settings"></a>레거시 설정으로 작업
작업 로그 를 수집하기 위한 레거시 설정은 진단 설정으로 대체하도록 선택하지 않으면 계속 작동합니다. 다음 방법을 사용하여 구독의 로그 프로필을 관리합니다.

1. Azure 포털의 **Azure 모니터** 메뉴에서 **활동 로그를**선택합니다.
3. **진단 설정을**클릭합니다.

   ![진단 설정](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. 레거시 환경을 보려면 보라색 배너를 클릭합니다.

    ![레거시 경험](media/diagnostic-settings-subscription/legacy-experience.png)


레거시 컬렉션 메서드 사용에 대한 자세한 내용은 다음 문서를 참조하십시오.

- [Azure 모니터의 로그 분석 작업 영역에서 Azure 활동 로그 수집 및 분석](activity-log-collect.md)
- [Azure Active 디렉터리 테넌드 전체에서 Azure 활동 로그를 Azure 모니터에 수집합니다.](activity-log-collect-tenants.md)
- [저장소 또는 Azure 이벤트 허브로 Azure 활동 로그 내보내기](activity-log-export.md)

## <a name="disable-existing-settings"></a>기존 설정 사용 안 함
진단 설정을 사용하여 활성화하기 전에 활동의 기존 컬렉션을 비활성화해야 합니다. 둘 다 사용하도록 설정하면 중복 데이터가 발생할 수 있습니다.

### <a name="disable-collection-into-log-analytics-workspace"></a>로그 분석 작업 영역으로 수집 을 사용하지 않도록 설정

1. Azure 포털에서 **로그 분석 작업 영역** 메뉴를 열고 작업 영역을 선택하여 활동 로그를 수집합니다.
2. 작업 **영역** 의 메뉴의 작업 영역 데이터 원본 섹션에서 Azure **활동 로그를**선택합니다.
3. 연결을 끊으려는 구독을 클릭합니다.
4. **연결을** 끊고 선택을 확인하라는 메시지가 **표시됩니다.**

### <a name="disable-log-profile"></a>로그 프로필 사용 안 함

1. [레거시 설정에서](#work-with-legacy-settings) 설명하는 절차를 사용하여 레거시 설정을 엽니다.
2. 저장소 또는 이벤트 허브에 대한 현재 컬렉션을 사용하지 않도록 설정합니다.



## <a name="activity-log-monitoring-solution"></a>활동 로그 모니터링 솔루션
Azure Log Analytics 모니터링 솔루션에는 로그 분석 작업 영역의 활동 로그 레코드를 분석하기 위한 여러 로그 쿼리 및 보기가 포함됩니다. 이 솔루션은 Log Analytics 작업 영역에서 수집된 로그 데이터를 사용하며 진단 설정을 사용하여 활동 로그를 수집하는 경우 변경 없이 계속 작동합니다. 이 솔루션에 대한 자세한 내용은 [활동 로그 분석 모니터링 솔루션을](activity-log-collect.md#activity-logs-analytics-monitoring-solution) 참조하십시오.

## <a name="next-steps"></a>다음 단계

* [활동 로그에 대해 자세히 알아보기](../../azure-resource-manager/management/view-activity-logs.md)
* [진단 설정에 대해 자세히 알아보기](diagnostic-settings.md)
