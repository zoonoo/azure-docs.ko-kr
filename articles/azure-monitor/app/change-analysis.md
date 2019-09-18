---
title: Azure Monitor에서 응용 프로그램 변경 분석을 사용 하 여 웹 앱 문제 찾기 | Microsoft Docs
description: Azure Monitor에서 응용 프로그램 변경 분석을 사용 하 여 Azure App Service에서 라이브 사이트의 응용 프로그램 문제를 해결할 수 있습니다.
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: 84e423ac055c074028df217060a548b932823496
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033375"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Azure Monitor에서 응용 프로그램 변경 분석 (미리 보기) 사용

라이브 사이트 문제 또는 중단이 발생 하는 경우 근본 원인을 신속 하 게 확인 하는 것이 중요 합니다. 표준 모니터링 솔루션은 문제를 경고할 수 있습니다. 실패 한 구성 요소를 나타낼 수도 있습니다. 그러나이 경고는 항상 오류의 원인을 즉시 설명 하지는 않습니다. 사이트는 5 분 전에 작동 했으며 이제는 중단 되었습니다. 최근 5 분 동안 변경 된 내용은 무엇 인가요? 이는 응용 프로그램 변경 분석을 Azure Monitor에서 답변 하도록 설계 된 질문입니다.

[Azure 리소스 그래프](https://docs.microsoft.com/azure/governance/resource-graph/overview)의 기능을 기반으로 하는 변경 분석을 통해 azure 응용 프로그램 변경 내용에 대 한 통찰력을 제공 하 여 관찰성를 늘리고 MTTR (평균 복구 시간)를 줄일 수 있습니다.

> [!IMPORTANT]
> 변경 분석은 현재 미리 보기 상태입니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공 됩니다. 이 버전은 프로덕션 워크 로드에는 권장 되지 않습니다. 일부 기능은 지원 되지 않거나 제한 된 기능을 가질 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기의 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조 하세요.

## <a name="overview"></a>개요

변경 분석은 응용 프로그램 배포에 이르기까지 인프라 계층에서 다양 한 유형의 변경 내용을 검색 합니다. 구독의 리소스 변경 내용을 확인 하는 구독 수준 Azure 리소스 공급자입니다. 변경 분석은 사용자가 문제의 원인이 되는 변경 내용을 이해 하는 데 도움이 되는 다양 한 진단 도구에 대 한 데이터를 제공 합니다

다음 다이어그램은 변경 분석의 아키텍처를 보여 줍니다.

![변경 분석에서 변경 데이터를 가져오고 클라이언트 도구에 제공 하는 방법에 대 한 아키텍처 다이어그램](./media/change-analysis/overview.png)

현재 변경 분석은 App Service 웹 앱의 **진단 및 문제 해결** 환경에 통합 되어 있습니다. 웹 앱에서 변경 내용 검색을 사용 하도록 설정 하 고 변경 내용을 확인 하려면이 문서의 뒷부분에 있는 *Web Apps 기능에 대 한 변경 분석* 섹션을 참조 하세요.

### <a name="azure-resource-manager-deployment-changes"></a>Azure Resource Manager 배포 변경 내용

[Azure 리소스 그래프](https://docs.microsoft.com/azure/governance/resource-graph/overview)를 사용 하 여 변경 분석을 통해 응용 프로그램을 호스트 하는 azure 리소스가 시간에 따라 어떻게 변경 되었는지에 대 한 기록 기록을 제공 합니다. 변경 분석은 예를 들어, IP 구성 규칙의 변경, 관리 되는 id 및 SSL 설정을 검색할 수 있습니다. 따라서 웹 앱에 태그를 추가 하는 경우 변경 내용 분석에서 변경 내용을 반영 합니다. 이 정보는 Azure 구독에서 `Microsoft.ChangeAnalysis` 리소스 공급자를 사용 하도록 설정한 경우에만 사용할 수 있습니다.

### <a name="changes-in-web-app-deployment-and-configuration"></a>웹 앱 배포 및 구성의 변경 내용

변경 분석은 4 시간 마다 응용 프로그램의 배포 및 구성 상태를 캡처합니다. 예를 들어 응용 프로그램 환경 변수의 변경 내용을 검색할 수 있습니다. 이 도구는 차이점을 계산 하 고 변경 된 내용을 표시 합니다. 리소스 관리자 변경과 달리 코드 배포 변경 정보는 도구에서 즉시 사용 하지 못할 수도 있습니다. 변경 분석에서 최신 변경 내용을 보려면 **지금 변경 내용 검색**을 선택 합니다.

!["지금 변경 내용 검색" 단추의 스크린샷](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>종속성 변경

리소스 종속성이 변경 되 면 웹 앱에서 문제가 발생할 수도 있습니다. 예를 들어 웹 앱이 Redis cache를 호출 하는 경우 Redis cache SKU는 웹 앱 성능에 영향을 줄 수 있습니다. 종속성의 변경 내용을 감지 하기 위해 변경 분석은 웹 앱의 DNS 레코드를 확인 합니다. 이러한 방식으로 문제를 일으킬 수 있는 모든 응용 프로그램 구성 요소의 변경 내용을 식별 합니다.
현재 지원 되는 종속성은 다음과 같습니다.
- Web Apps
- Azure Storage
- Azure SQL


## <a name="change-analysis-for-the-web-apps-feature"></a>Web Apps 기능에 대 한 변경 분석

Azure Monitor 변경 분석은 현재 셀프 서비스 **진단 및 문제 해결** 환경에 기본 제공 됩니다. App Service 응용 프로그램의 **개요** 페이지에서이 환경에 액세스 합니다.

!["개요" 단추의 스크린샷 및 "문제 진단 및 해결" 단추](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>문제 진단 및 해결 도구에서 변경 분석을 사용 하도록 설정

1. **가용성 및 성능을**선택 합니다.

    !["가용성 및 성능" 문제 해결 옵션의 스크린샷](./media/change-analysis/availability-and-performance.png)

1. **응용 프로그램 변경 내용**을 선택 합니다. **응용 프로그램 작동 중단**에서도 기능을 사용할 수 있는 것은 아닙니다.

   !["응용 프로그램 작동 중단" 단추의 스크린샷](./media/change-analysis/application-changes.png)

1. 변경 분석을 사용 하도록 설정 하려면 **지금 사용**을 선택 합니다.

   !["응용 프로그램 작동 중단" 옵션 스크린샷](./media/change-analysis/enable-changeanalysis.png)

1. **변경 분석** 을 설정 하 고 **저장**을 선택 합니다.

    !["변경 분석 설정" 사용자 인터페이스의 스크린샷](./media/change-analysis/change-analysis-on.png)


1. 변경 분석에 액세스 하려면 >  **진단 및 문제 해결 및** **성능** > **응용 프로그램 충돌**을 선택 합니다. 시간에 따른 변경 내용 유형과 해당 변경 내용에 대 한 세부 정보를 요약 하는 그래프가 표시 됩니다.

     ![Diff 뷰 변경의 스크린샷](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>규모에 맞게 변경 분석 설정

구독에 다양 한 웹 앱이 포함 되어 있는 경우 웹 앱 수준에서 서비스를 사용 하도록 설정 하는 것은 비효율적입니다. 다음 스크립트를 실행 하 여 구독의 모든 웹 앱을 사용 하도록 설정 합니다.

필수 조건:
* PowerShell Az Module. [Azure PowerShell 모듈 설치](https://docs.microsoft.com/en-us/powershell/azure/install-az-ps?view=azps-2.6.0) 의 지침을 따르세요.

다음 스크립트를 실행합니다.

```PowerShell
# Log in to your Azure subscription
Connect-AzAccount

# Get subscription Id
$SubscriptionId = Read-Host -Prompt 'Input your subscription Id'

# Make Feature Flag visible to the subscription
Set-AzContext -SubscriptionId $SubscriptionId

# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"


# Enable each web app
$webapp_list = Get-AzWebApp | Where-Object {$_.kind -eq 'app'}
foreach ($webapp in $webapp_list)
{
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId $webapp.Id -Tag $tags -Force
}

```



## <a name="next-steps"></a>다음 단계

- [Azure 앱 Services 앱](azure-web-apps.md)에 대 한 Application Insights를 사용 하도록 설정 합니다.
- [AZURE VM 및 azure 가상 머신 확장 집합 IIS에서 호스팅되는 앱](azure-vm-vmss-apps.md)에 대 한 Application Insights를 사용 하도록 설정 합니다.
- Power Change 분석에 도움이 되는 [Azure 리소스 그래프](https://docs.microsoft.com/azure/governance/resource-graph/overview)에 대해 자세히 알아보세요.
