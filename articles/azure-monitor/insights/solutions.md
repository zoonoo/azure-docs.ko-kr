---
title: Azure Monitor의 모니터링 솔루션 | Microsoft Docs
description: Azure Monitor의 모니터링 솔루션은 특정 문제 영역을 중심으로 피벗된 메트릭을 제공하는 논리, 시각화 및 데이터 취득 규칙의 컬렉션입니다.  이 문서에서는 모니터링 솔루션을 설치하고 사용하는 방법을 설명합니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/12/2020
ms.openlocfilehash: 4edcb22ed6bd33b1174354cf0cbb9a590e35c207
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84906890"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Azure Monitor의 모니터링 솔루션
모니터링 솔루션은 Azure의 서비스를 활용 하 여 특정 응용 프로그램 또는 서비스의 작업에 대 한 추가 분석을 제공 합니다. 이 문서에서는 Azure 모니터링 솔루션에 대해 간략하게 설명하고 모니터링 솔루션을 설치 및 사용하는 방법을 자세히 알아봅니다. 사용하는 애플리케이션 및 서비스의 Azure Monitor에 모니터링 솔루션을 추가할 수 있습니다. 일반적으로 무료로 제공되지만 데이터 수집 시 사용 요금이 발생할 수 있습니다.

## <a name="use-monitoring-solutions"></a>모니터링 솔루션 사용

Azure Monitor의 **개요** 페이지를 열면 작업 영역에 설치된 각 솔루션의 타일이 표시됩니다. 

1. [Azure 포털](https://ms.portal.azure.com)로 이동합니다. **모니터**를 검색하고 선택합니다.
1. **인사이트** 메뉴 아래에서 **자세히**를 선택합니다.
1. 타일에 사용되는 작업 영역 또는 시간 범위를 변경하려면 화면 맨 위에 있는 드롭다운 상자를 사용합니다.
1. 솔루션의 타일을 클릭하면 수집된 데이터를 자세히 분석하는 보기가 열립니다.

![개요](media/solutions/overview.png)

모니터링 솔루션은 여러 유형의 Azure 리소스를 포함할 수 있으며, 다른 리소스와 마찬가지로 솔루션에 포함된 모든 리소스를 볼 수 있습니다. 예를 들어 솔루션에 포함된 모든 로그 쿼리는 [쿼리 탐색기](../log-query/get-started-portal.md#load-queries)의 **솔루션 쿼리** 아래에 표시됩니다. [로그 쿼리](../log-query/log-query-overview.md)를 사용하여 임시 분석을 수행할 때 해당 쿼리를 사용할 수 있습니다.

## <a name="list-installed-monitoring-solutions"></a>설치된 모니터링 솔루션 나열

다음 절차를 사용하여 구독에 설치된 모니터링 솔루션을 나열할 수 있습니다.

1. [Azure 포털](https://ms.portal.azure.com)로 이동합니다. **솔루션**을 검색하고 선택합니다.
1. 모든 작업 영역에 설치된 솔루션이 나열됩니다. 솔루션 이름 뒤에는 솔루션이 설치된 작업 영역의 이름이 나옵니다.
1. 화면 맨 위에 있는 드롭다운 상자를 사용하여 구독 또는 리소스 그룹으로 필터링할 수 있습니다.


![모든 솔루션 나열](media/solutions/list-solutions-all.png)

솔루션 이름을 클릭하면 요약 페이지가 열립니다. 이 페이지에는 솔루션에 포함된 보기가 표시되고 솔루션 자체 및 해당 작업 영역에 대한 여러 옵션이 제공됩니다. 위의 절차 중 하나를 사용하여 솔루션의 요약 페이지를 살펴본 후 솔루션 이름을 클릭하세요.

![솔루션 속성](media/solutions/solution-properties.png)

## <a name="install-a-monitoring-solution"></a>모니터링 솔루션 설치

Microsoft 및 파트너의 모니터링 솔루션은 [Azure Marketplace](https://azuremarketplace.microsoft.com)에서 사용할 수 있습니다. 다음 절차를 사용하여 사용 가능한 솔루션을 검색하고 설치할 수 있습니다. 솔루션을 설치할 때 솔루션이 설치되고 데이터가 수집될 [Log Analytics 작업 영역](../platform/manage-access.md)을 선택해야 합니다.

1. [구독의 솔루션 목록](#list-installed-monitoring-solutions)에서 **추가**를 클릭합니다.
1. 솔루션을 찾아보거나 검색합니다. [이 검색 링크](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions)에서 솔루션을 찾아볼 수도 있습니다.
1. 원하는 모니터링 솔루션을 찾아서 해당 설명을 자세히 읽습니다.
1. 설치 프로세스를 시작하려면 **만들기**를 클릭합니다.
1. 설치 프로세스가 시작되면 Log Analytics 작업 영역을 지정하고 솔루션에 필요한 구성을 제공하라는 메시지가 표시됩니다.

![솔루션 설치](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>커뮤니티에서 솔루션 설치

커뮤니티 구성원은 관리 솔루션을 Azure 빠른 시작 템플릿에 제출할 수 있습니다. 이러한 솔루션을 직접 설치하거나 나중에 설치할 수 있도록 템플릿을 다운로드할 수 있습니다.

1. [Log Analytics 작업 영역 및 Automation 계정](#log-analytics-workspace-and-automation-account)에 설명된 프로세스에 따라 작업 영역 및 계정에 연결합니다.
2. [Azure 빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/)으로 이동합니다. 
3. 관심 있는 솔루션을 검색합니다.
4. 해당 정보를 보려면 결과에서 솔루션을 선택합니다.
5. **Azure에 배포** 단추를 클릭합니다.
6. 솔루션의 매개 변수 값 외에도 리소스 그룹 및 위치 같은 정보를 제공해야 합니다.
7. **구매**를 클릭하여 솔루션을 설치합니다.

## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics 작업 영역 및 Automation 계정

모든 모니터링 솔루션에는 수집된 데이터를 저장하고 로그 검색 및 보기를 호스트할 [Log Analytics 작업 영역](../platform/manage-access.md)이 필요합니다. 일부 솔루션은 Runbook 및 관련 리소스를 포함할 [Automation 계정](../../automation/automation-security-overview.md)도 필요합니다. 작업 영역 및 계정은 다음 요구 사항을 충족해야 합니다.

* 각 솔루션 설치 시 하나의 Log Analytics 작업 영역과 하나의 Automation 계정만 사용할 수 있습니다. 솔루션을 여러 작업 영역에 개별적으로 설치할 수 있습니다.
* 솔루션에서 Automation 계정을 요구하는 경우 Log Analytics 작업 영역과 Automation 계정이 서로 연결되어야 합니다. Log Analytics 작업 영역은 하나의 Automation 계정에만 연결되고, Automation 계정은 하나의 Log Analytics 작업 영역에만 연결될 수 있습니다.
* 연결하려면 Log Analytics 작업 영역과 Automation 계정이 동일한 구독에 있어야 하지만 동일한 지역에 배포된 다른 리소스 그룹에 있을 수 있습니다. 미국 동부 지역에 있는 작업 영역과 미국 동부 2에 있는 Automation 계정의 경우는 예외입니다.

Azure Marketplace를 통해 솔루션을 설치하는 경우 작업 영역 및 Automation 계정을 요구하는 메시지가 표시됩니다. 작업 영역과 계정이 아직 서로 연결되지 않았으면 둘 사이에 연결이 만들어집니다.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Log Analytics 작업 영역 및 Automation 계정 간의 링크 확인

다음 절차를 통해 Log Analytics 작업 영역 및 Automation 계정 간의 링크를 확인할 수 있습니다.

1. Azure Portal에서 Automation 계정을 선택합니다.
1. 메뉴의 **관련 리소스** 섹션으로 스크롤하여 **연결된 작업 영역**을 선택합니다.
1. **작업 영역**이 Automation 계정에 연결된 경우 이 페이지에는 연결된 작업 영역이 나열됩니다. 나열된 작업 영역 이름을 선택하면 해당 작업 영역의 개요 페이지로 리디렉션됩니다.

## <a name="remove-a-monitoring-solution"></a>모니터링 솔루션 제거

설치된 솔루션을 제거하려면 [설치된 솔루션 목록](#list-installed-monitoring-solutions)에서 해당 솔루션을 찾습니다. 솔루션 이름을 클릭하여 요약 페이지를 열고 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

* [Microsoft에서 모니터링 솔루션 목록](solutions-inventory.md)을 가져옵니다.
* 모니터링 솔루션에서 수집한 데이터를 분석하는 [쿼리 만들기](../log-query/log-query-overview.md) 방법을 알아봅니다.