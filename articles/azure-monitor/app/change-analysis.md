---
title: Azure Monitor에서 응용 프로그램 변경 분석을 사용 하 여 웹 앱 문제를 찾도록 | Microsoft Docs
description: Azure Monitor 응용 프로그램 변경 분석을 사용 하 여 Azure App Service에서 라이브 사이트에서 응용 프로그램 문제를 해결 합니다.
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: 2a31131b662d01f9841a3f1c5b0a6c459a117e77
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075373"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Azure Monitor에서 응용 프로그램 변경 분석 (미리 보기) 사용

라이브 사이트 문제 또는 중단이 발생 하면 근본 원인을 빠르게 파악이 중요 합니다. 표준 모니터링 솔루션 문제를 경고 수 있습니다. 도 있는 구성 요소 실패를 나타낼 수 있습니다 이러한. 하지만이 경고는 실패의 원인을 설명 항상 즉시 하지 않겠습니다. 사이트에는 5 분 전 작업 및 분리는 이제 알 수 있습니다. 지난 5 분 동안에서 변경 내용? 이 문제는 Azure Monitor에서 응답 하도록 설계 된 응용 프로그램 변경 분석 합니다. 

기능을 토대로 [Azure 리소스 그래프](https://docs.microsoft.com/azure/governance/resource-graph/overview), 변경 분석은 Azure 응용 프로그램 변경 내용을 관찰성을 늘리고 줄일 MTTR (평균 복구 시간)에 대 한 정보를 제공 합니다.

> [!IMPORTANT]
> 변경 분석은 현재 미리 보기 상태에서입니다. 이 미리 보기 버전을 서비스 수준 계약 없이 제공 됩니다. 이 버전은 프로덕션 워크 로드에 대 한 권장 되지 않습니다. 일부 기능은 지원 되지 않는 또는 기능이 제한 될 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대 한 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)합니다.

## <a name="overview"></a>개요

변경 분석에는 다양 한 유형의 응용 프로그램 배포에 이르는 인프라 계층에서 변경 내용 검색합니다. 이 구독에서 리소스 변경 내용을 확인 하는 구독 수준 Azure 리소스 공급자입니다. 변경 분석 사용자가 변경 내용을 이해 하는 다양 한 진단 도구에 대 한 데이터 문제가 원인일 수를 제공 합니다.

다음 다이어그램에서는 변경 분석의 아키텍처를 보여 줍니다.

![변경 분석 변경 데이터를 가져옵니다 하 고 클라이언트 도구를 제공 하는 방법을 아키텍처 다이어그램](./media/change-analysis/overview.png)

변경 분석에 통합 되어 현재는 **진단 및 문제 해결** App Service 웹 앱에서 발생 합니다. 변경 내용 검색을 사용 하도록 설정 하 고 웹 앱에서 변경 내용을 보려면을 참조 합니다 *Web Apps 기능에 대 한 변경 분석* 이 문서의 뒷부분에 나오는 섹션입니다.

### <a name="azure-resource-manager-deployment-changes"></a>Azure Resource Manager 배포 변경

사용 하 여 [Azure 리소스 그래프](https://docs.microsoft.com/azure/governance/resource-graph/overview), 변경 분석 시간이 지남에 따라 응용 프로그램을 호스트 하는 Azure 리소스를 변경 하는 방법의 기록 레코드를 제공 합니다. 변경 분석 검색 하 고, 예를 들어, IP 구성 규칙, 관리 되는 id 및 SSL 설정을 변경 합니다. 따라서 태그는 웹 앱에 추가 되 면 변경 분석에 해당 변경 내용이 반영 합니다. 이 정보를 사용으로 `Microsoft.ChangeAnalysis` Azure 구독에서 리소스 공급자를 사용할 수 있습니다.

### <a name="changes-in-web-app-deployment-and-configuration"></a>웹 앱 배포 및 구성 변경 내용

변경 분석 4 시간 마다 응용 프로그램의 배포 및 구성 상태를 캡처합니다. 이 검색할 수 있는 예를 들어, 응용 프로그램 환경 변수를 변경 합니다. 도구는 차이 계산 하 고 변경 된 내용을 제공 합니다. 리소스 관리자 변경 내용 달리 코드 배포 변경 내용 수 도구에서 즉시 사용할 수 없습니다. 선택 변경 분석의 최신 변경 내용을 보려는 **검색 지금 변경**합니다.

!["검색 지금 변경" 단추 스크린샷](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>종속성 변경

리소스 종속성 변경 내용을 웹 앱에서 문제를 일으킬 수도 있습니다. 예를 들어, Redis cache를 웹 앱을 호출 하는 경우 Redis cache SKU 영향을 줄 수를 웹 앱 성능. 변경 분석 종속성의 변경 내용을 감지 하려면 웹 앱의 DNS 레코드를 확인 합니다. 이러한 방식으로 문제를 일으킬 수 있는 모든 앱 구성 요소에서 변경 내용을 식별 합니다.

## <a name="change-analysis-for-the-web-apps-feature"></a>Web Apps 기능에 대 한 변경 분석

Azure Monitor 변경 분석 현재 셀프 서비스에 내장 되 **진단 및 문제 해결** 발생 합니다. 이 환경에서 액세스 합니다 **개요** App Service 응용 프로그램의 페이지입니다.

!["개요" 단추 스크린샷 및 "진단 및 문제 해결" 단추](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>변경 분석 진단에서 사용 하 고 도구 문제 해결

1. 선택 **가용성과 성능을**합니다.

    !["가용성 및 성능" 문제 해결 옵션의 스크린샷](./media/change-analysis/availability-and-performance.png)

1. 선택 **응용 프로그램 작동 중단**합니다.

   !["응용 프로그램이 크래시 되" 단추 스크린샷](./media/change-analysis/application-crashes-tile.png)

1. 변경 분석을 사용 하려면 **지금 사용**합니다.

   !["응용 프로그램이 크래시 되" 옵션의 스크린샷](./media/change-analysis/application-crashes.png)

1. 전체 변경 분석 기능을 활용 하려면 켜 세요 **변경 분석**, **코드 변경을 검색할**, 및 **무중단**합니다. 그런 다음 **저장**을 선택합니다.

    !["변경 분석 사용" 사용자 인터페이스 스크린샷](./media/change-analysis/change-analysis-on.png)

    - 사용 하도록 설정 **변경 분석** 리소스 수준 변경을 감지할 수 있습니다. 
    - 사용 하도록 설정 **코드 변경 사항 검색** 배포 파일을 참조 하 여 사이트 구성 변경 합니다. 
    - 사용 하도록 설정 **무중단** 변경 검색의 성능을 최적화 합니다. 하지만이 설정은 추가 청구 요금이 발생할 수 있는 염두에 둡니다.

1. 변경 분석에 액세스 하려면 **진단 및 문제 해결** > **가용성과 성능을** > **응용 프로그램이 크래시 되**합니다. 이러한 변경 내용에 대 한 세부 정보와 함께 시간이 지남에 따라 변경 유형을 요약 하는 그래프를 볼 수 있습니다.

     ![변경 diff 보기의 스크린샷](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>대규모로 변경 분석을 사용 합니다.

구독의 다양 한 웹 앱에 포함 된 경우 웹 앱 수준에서 서비스를 사용 하면 비효율적입니다. 이 경우 이러한 다른 지침을 따릅니다.

### <a name="register-the-change-analysis-resource-provider-for-your-subscription"></a>구독에 대 한 변경 분석 리소스 공급자 등록

1. 변경 분석 기능 플래그 (미리 보기)를 등록 합니다. 기능 플래그를 미리 보기로 제공에서 되므로, 구독에 표시 하려면 등록 해야 합니다.

   1. [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)을 엽니다.

      ![Cloud Shell 변화의 스크린 샷](./media/change-analysis/cloud-shell.png)

   1. 셸 형식 변경 **PowerShell**합니다.

      ![Cloud Shell 변화의 스크린 샷](./media/change-analysis/choose-powershell.png)

   1. 다음 PowerShell 명령을 실행합니다.

        ``` PowerShell
        Set-AzContext -Subscription <your_subscription_id> #set script execution context to the subscription you are trying to enable
        Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.ChangeAnalysis" -ListAvailable #Check for feature flag availability
        Register-AzureRmProviderFeature -FeatureName PreviewAccess -ProviderNamespace Microsoft.ChangeAnalysis #Register feature flag
        ```
    
1. 구독에 대 한 변경 분석 리소스 공급자를 등록 합니다.

   - 로 이동 **구독**, 변경 서비스에 사용 하도록 설정 하려는 구독을 선택 합니다. 리소스 공급자를 선택 합니다.

        ![변경 분석 리소스 공급자를 등록 하는 방법을 보여 주는 스크린샷](./media/change-analysis/register-rp.png)

       - 선택 **Microsoft.ChangeAnalysis**합니다. 페이지의 맨 위에 있는 선택한 **등록**합니다.

       - 리소스 공급자를 설정한 후 배포 수준에서 변경 내용을 검색 하려면 웹 앱에 숨겨진된 태그를 설정할 수 있습니다. 숨겨진된 태그를 설정 하려면 지침을 따릅니다 **변경 분석 정보를 가져올 수 없습니다**합니다.

   - 또는 리소스 공급자를 등록 하는 PowerShell 스크립트를 사용할 수 있습니다.

        ```PowerShell
        Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState #Check if RP is ready for registration
    
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis" #Register the Change Analysis RP
        ```

        웹 앱에서 숨겨진된 태그를 설정 하려면 PowerShell을 사용 하려면 다음 명령을 실행 합니다.
    
        ```powershell
        $webapp=Get-AzWebApp -Name <name_of_your_webapp>
        $tags = $webapp.Tags
        $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
        Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
        ```

     > [!NOTE]
     > 숨겨진된 태그를 추가한 후 변경 내용이 표시를 시작 하기 전에 최대 4 시간 동안 대기 여전히 필요할 수 있습니다. 변경 분석 4 시간 마다 웹 앱을 검색 하기 때문에 결과 지연 됩니다. 4 시간 일정에는 검색의 성능에 영향을 제한합니다.

## <a name="next-steps"></a>다음 단계

- App Service 모니터링에서 효과적 [Application Insights 기능을 사용 하도록 설정 하면](azure-web-apps.md) Azure Monitor에서.
- 에 대해 자세히 알아보세요 [Azure 리소스 그래프](https://docs.microsoft.com/azure/governance/resource-graph/overview)를 사용 하면 전원 변경 분석 합니다.
