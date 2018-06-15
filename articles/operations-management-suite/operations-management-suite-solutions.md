---
title: Azure 관리 솔루션 | Microsoft Docs
description: 관리 솔루션에는 고객이 Log Analytics 작업 영역에 추가할 수 있는 Azure의 패키지된 관리 시나리오가 포함되어 있습니다.  이 문서에서는 고객 및 파트너가 사용자 지정 솔루션을 만드는 방법에 대한 세부 정보를 제공합니다.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 1f054a4e-6243-4a66-a62a-0031adb750d8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/01/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d46b869815fef44a8137bb5121133a1c0140ca30
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2018
ms.locfileid: "29394258"
---
# <a name="working-with-management-solutions-in-azure-preview"></a>Azure의 관리 솔루션 사용(Preview)
> [!NOTE]
> 현재 Preview로 제공되는 Azure의 관리 솔루션에 대한 예비 설명서입니다.    
> 
> 

관리 솔루션에는 고객이 Azure 환경에 추가할 수 있는 패키지된 관리 시나리오가 포함되어 있습니다.  [Microsoft에서 제공한 솔루션](../log-analytics/log-analytics-add-solutions.md) 외에도 파트너 및 고객은 자신의 환경에서 사용하거나 커뮤니티를 통해 고객이 이용할 수 있는 관리 솔루션을 만들 수 있습니다.

## <a name="finding-and-installing-management-solutions"></a>관리 솔루션 찾기 및 설치
다음 섹션에 설명된 대로 관리 솔루션을 찾아 설치하는 방법은 여러 가지가 있습니다.

### <a name="azure-marketplace"></a>Azure Marketplace
Microsoft 및 신뢰할 수 있는 파트너에서 제공한 관리 솔루션은 Azure Portal의 Azure Marketplace로부터 설치할 수 있습니다.

1. Azure 포털에 로그인합니다.
2. 왼쪽 창에서 **모든 서비스**를 선택합니다.
3. **솔루션**까지 아래로 스크롤하거나 **필터** 대화 상자에 *솔루션*을 입력합니다.
4. **+추가** 단추를 클릭합니다.
5. 찾아보거나 **필터** 단추를 클릭하거나 **모두 검색** 상자에 입력하여 관심 있는 솔루션을 검색합니다.
6. 해당 세부 정보를 보려면 마켓플레이스 항목을 클릭합니다.
7. **만들기**를 클릭하여 **솔루션 추가** 창을 엽니다.
8. 솔루션의 모든 매개 변수에 대한 값 외에도 [Log Analytics 작업 영역 및 Automation 계정](#log-analytics-workspace-and-automation-account)과 같은 필수 정보가 표시됩니다.
9. **만들기**를 클릭하여 솔루션을 설치합니다.

### <a name="oms-portal"></a>OMS 포털
OMS 포털의 솔루션 갤러리로부터 Microsoft에서 제공하는 관리 솔루션을 설치할 수 있습니다.

1. OMS 포털에 로그인합니다.
2. **솔루션 갤러리** 타일을 클릭합니다.
3. OMS 솔루션 갤러리 페이지에서 사용 가능한 각 솔루션에 대해 알아봅니다. 추가할 솔루션의 이름을 클릭합니다.
4. 선택한 솔루션에 대한 페이지에서 솔루션에 대한 자세한 정보가 표시됩니다. **추가**를 클릭합니다.
5. 추가한 솔루션의 새 타일이 포털의 개요 페이지에 표시되며 Log Analytics가 데이터를 처리한 후 새 타일을 사용하여 시작할 수 있습니다.

### <a name="azure-quickstart-templates"></a>Azure 빠른 시작 템플릿
커뮤니티 구성원은 관리 솔루션을 Azure 빠른 시작 템플릿에 제출할 수 있습니다.  이 템플릿을 다운로드하여 나중에 설치하거나 템플릿을 검사하여 [사용자 고유의 솔루션을 만드는](#creating-a-solution) 방법을 배울 수 있습니다.

1. [ 작업 영역 및 Automation 계정](#log-analytics-workspace-and-automation-account)에 설명된 프로세스에 따라 작업 영역 및 계정에 연결합니다.
2. [Azure 빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates/)으로 이동합니다.  
3. 관심 있는 솔루션을 검색합니다.
4. 해당 정보를 보려면 결과에서 솔루션을 선택합니다.
5. **Azure에 배포** 단추를 클릭합니다.
6. 솔루션의 모든 매개 변수에 대한 값 외에도 리소스 그룹 및 위치와 같은 정보가 표시됩니다.
7. **구매**를 클릭하여 솔루션을 설치합니다.

### <a name="deploy-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 배포
커뮤니티에서 가져오거나 사용자가 [직접 만든](#creating-a-solution) 솔루션은 Resource Manager 템플릿으로 구현되며, [템플릿 배포](../azure-resource-manager/resource-group-template-deploy-portal.md)를 위한 표준 방법을 사용할 수 있습니다.  솔루션을 설치하기 전에 [ 작업 영역 및 Automation 계정](#log-analytics-workspace-and-automation-account)을 만들어 연결해야 합니다.

## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics 작업 영역 및 Automation 계정
대부분의 관리 솔루션은 뷰를 포함하는 [ 작업 영역](../log-analytics/log-analytics-manage-access.md)과 Runbook 및 관련 리소스를 포함하는 [Automation 계정](../automation/automation-security-overview.md#automation-account-overview)이 필요합니다. 작업 영역 및 계정은 다음 요구 사항을 충족해야 합니다.

* 솔루션은 하나의 Log Analytics 작업 영역과 하나의 Automation 계정만 사용할 수 있습니다.  
* 솔루션이 사용하는 Log Analytics 작업 영역과 Automation 계정은 서로 연결되어야 합니다. Log Analytics 작업 영역은 하나의 Automation 계정에만 연결되고, Automation 계정은 하나의 Log Analytics 작업 영역에만 연결될 수 있습니다.
* 연결하려면 Log Analytics 작업 영역 및 Automation 계정은 동일한 리소스 그룹 및 지역에 있어야 합니다.  미국 동부 지역에 있는 작업 영역과 미국 동부 2에 있는 Automation 계정의 경우는 예외입니다.

### <a name="creating-a-link-between-a-log-analytics-workspace-and-automation-account"></a>Log Analytics 작업 영역 및 Automation 계정 간의 링크 만들기
Log Analytics 작업 영역 및 Automation 계정을 지정하는 방법은 솔루션의 설치 방법에 따라 달라집니다.

* OMS 포털을 통해 Microsoft 솔루션을 설치하는 경우 현재 작업 영역에 설치되며 Automation 계정은 필요하지 않습니다.
* Azure Marketplace를 통해 솔루션을 설치하는 경우 작업 영역 및 Automation 계정에 대한 메시지가 표시되고 이들 간에 링크가 생성됩니다.  
* Azure Marketplace의 외부 솔루션의 경우 솔루션을 설치하기 전에 Log Analytics 작업 영역 및 Automation 계정을 연결해야 합니다.  Azure Marketplace에서 솔루션을 선택하고 Log Analytics 작업 영역 및 Automation 계정을 선택하여 이를 수행할 수 있습니다.  Log Analytics 작업 영역 및 Automation 계정을 선택하는 즉시 링크가 만들어지기 때문에 솔루션을 직접 설치할 필요가 없습니다.  링크가 만들어지면 솔루션의 해당 Log Analytics 작업 영역 및 Automation 계정을 사용할 수 있습니다. 

### <a name="verifying-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Log Analytics 작업 영역 및 Automation 계정 간의 링크 확인
다음 절차를 통해 Log Analytics 작업 영역 및 Automation 계정 간의 링크를 확인할 수 있습니다.

1. Azure Portal에서 Automation 계정을 선택합니다.
2. 메뉴의 **관련 리소스** 섹션에 있는 **작업 영역** 설정이 사용 가능하게 구성되면 이 계정이 Log Analytics 작업 영역에 연결됩니다.  **작업 영역**을 클릭하여 작업 영역의 세부 정보를 확인할 수 있습니다.

## <a name="listing-management-solutions"></a>목록 관리 솔루션
다음 절차를 통해 Azure 구독에 연결된 작업 영역에 있는 관리 솔루션을 봅니다.

1. Azure 포털에 로그인합니다.
2. 왼쪽 창에서 **모든 서비스**를 선택합니다.
3. **솔루션**까지 아래로 스크롤하거나 **필터** 대화 상자에 *솔루션*을 입력합니다.
4. 모든 작업 영역에 설치된 솔루션이 나열됩니다.

OMS 포털을 사용하면 현재 작업 영역에 설치된 Microsoft 솔루션만 볼 수 있습니다.

## <a name="removing-a-management-solution"></a>관리 솔루션 제거
관리 솔루션을 제거하면 솔루션의 모든 리소스도 제거됩니다.  

1. [솔루션 나열](#listing-solutions)의 절차를 통해 Azure Portal에서 솔루션을 찾습니다.
2. 제거하려는 솔루션을 선택합니다.
3. **삭제** 단추를 클릭합니다.

## <a name="creating-a-management-solution"></a>관리 솔루션 만들기
관리 솔루션 만들기에 대한 전체 지침은 [OMS(Operations Management Suite)](operations-management-suite-solutions-creating.md)에서 확인할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
* [Azure 빠른 시작 템플릿](https://azure.microsoft.com/documentation/templates)에서 다양한 Resource Manager 템플릿 샘플을 검색합니다.
* 자신만의 [관리 솔루션](operations-management-suite-solutions-creating.md)을 만듭니다.

