---
title: Azure 모니터 응용 프로그램 변경 분석-Azure Monitor 응용 프로그램을 사용 하 여 라이브 사이트 문제/중단에 영향을 줄 수 있는 변경 분석 검색 | Microsoft Docs
description: Azure Monitor 응용 프로그램 변경 분석을 사용 하 여 Azure App Service에서 응용 프로그램 라이브 사이트 문제 해결
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: 5bd3816e65398283de85b4551a137b3f97db4cc7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226319"
---
# <a name="application-change-analysis-public-preview"></a>응용 프로그램 변경 분석 (공개 미리 보기)

라이브 사이트 문제/중단이 발생 하는 경우 근본 원인을 신속 하 게 결정이 중요 합니다. 솔루션을 모니터링 하는 standard를 사용 하는 문제가 발생 하는 구성 요소에도 종종 실패를 신속 하 게 식별 도움이 될 수 있습니다. 하지만이 오류가 발생 하는 이유는 무엇에 대 한 즉시 설명이로 인해 항상 없습니다. 사이트는 손상 된 이제 5 분 이전으로 작동 합니다. 지난 5 분 동안에서 변경 내용? 새 응용 프로그램 변경 분석 응답 하도록 디자인 되어 Azure Monitor 기능 하는 질문입니다. 기능을 빌드하여 합니다 [Azure 리소스 그래프](https://docs.microsoft.com/azure/governance/resource-graph/overview) 관찰성을 높이고 MTTR (평균 시간에 복구)를 절감할 대 한 변경 내용을 Azure 응용 프로그램에 대 한 정보를 제공 하는 응용 프로그램 변경 분석 합니다.

> [!IMPORTANT]
> Azure 모니터 응용 프로그램 변경 분석 현재 공개 미리 보기 중입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="overview-of-change-analysis-service"></a>변경 분석 서비스 개요
변경 분석 서비스는 다양 한 유형의 응용 프로그램 배포에 이르는 인프라 계층에서 변경 내용 검색합니다. 구독 수준 Azure 리소스 공급자를 구독에서 리소스 변경 내용을 검토 하 고 사용자가 변경 내용을 이해 하는 데 다양 한 진단 도구에 대 한 데이터 문제가 원인일 수를 제공 하는 것입니다.

다음 다이어그램에서는 변경 분석 서비스의 아키텍처를 보여 줍니다. ![아키텍처 다이어그램 변경 분석 서비스를 통해 가져오는 방법에 대 한 변경 데이터 및 클라이언트 도구에 데이터를 제공 합니다.](./media/change-analysis/overview.png)

현재이 도구는 웹 앱 진단 및 환경 문제를 해결 하는 App Services에 통합 됩니다. 참조 *App Services 웹 앱에 대 한 변경 Analysis service* 사용 하도록 설정 하 고 웹 앱에 대 한 변경 내용을 확인 하는 방법에 대 한 섹션입니다.

### <a name="azure-resource-manager-deployment-changes"></a>Azure Resource Manager 배포 변경
활용 [Azure 리소스 그래프](https://docs.microsoft.com/azure/governance/resource-graph/overview) 변경 분석 도구를 사용 하는 시간이 지남에 따라 응용 프로그램을 호스트 하는 Azure 리소스를 변경 하는 방법의 기록 레코드를 제공 합니다. 예를 들어, 웹 앱에 추가할 태그 있으면 변경 분석 도구에서 변경 내용이 반영 됩니다.
이 정보를 항상 사용할 수 있다면는 `Microsoft.ChangeAnalysis` 리소스 공급자는 Azure 구독에 등록 합니다.

### <a name="web-application-deployment-and-configuration-changes"></a>웹 응용 프로그램 배포 및 구성 변경
변경 분석 도구를 차이 계산 하 여 변경 된 내용을 제공 4 시간 마다 응용 프로그램의 배포 및 구성 상태를 캡처합니다. 이러한 변경의 예로 응용 프로그램 환경 변수 변경, IP 구성 규칙 변경, 관리 서비스 Id 변경, SSL 설정 변경 및 등이 있습니다.
리소스 관리자 변경 내용 달리이 유형의 변경 내용은 있습니다 도구에서 즉시 사용할 수 없습니다. 최신 변경 내용을 보려는 도구에서 '검색 변경 이제' 단추를 사용 합니다.

![이제 진단 단추 및 app service web apps에 대 한 변경 분석 통합 도구 문제 해결 변경에 대 한 검색 스크린샷](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>종속성 변경
종속성 리소스 문제의 원인을 수도 있습니다. 예를 들어, Redis cache를 웹 앱을 호출 하면 웹 앱 성능 Redis cache SKU 영향을 받을 수 있습니다. 웹 앱 DNS 레코드를 확인 하 여 서비스 정보를 제공할 수도 종속성 변경 문제를 일으킬 수 있는 앱의 모든 구성 요소에서 변경 내용을 식별 하는 분석을 변경 합니다.


## <a name="change-analysis-service-for-app-services-web-app"></a>앱 서비스 웹 앱에 대 한 분석 서비스를 변경 합니다.

Azure 모니터 응용 프로그램 변경 분석은 현재 셀프 서비스 내장 **진단 및 문제 해결** 환경에서 액세스할 수 있는 합니다 **개요** Azure App Service의 섹션 응용 프로그램:

![Azure App Service의 스크린 샷 개요 개요 단추 주위에 빨간색 상자를 사용 하 여 페이지와 진단 및 문제 단추 해결](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-diagnose-and-solve-problems-tool"></a>변경 분석 진단에서 사용 하 고 도구 문제 해결

1. 선택 **가용성 및 성능**

    ![가용성 및 성능 문제 해결 옵션의 스크린샷](./media/change-analysis/availability-and-performance.png)

2. 클릭 합니다 **응용 프로그램이 크래시 되** 바둑판식으로 배열 합니다.

   ![응용 프로그램 충돌 타일을 사용 하 여 스크린 샷](./media/change-analysis/application-crashes-tile.png)

3. 사용할 수 있도록 **변경 Analysis** 선택 **지금 사용**합니다.

   ![가용성 및 성능 문제 해결 옵션의 스크린샷](./media/change-analysis/application-crashes.png)

4. 전체 활용 되려면 분석 기능 집합을 변경 **변경 분석**, **코드 변경을 검색할**, 및 **무중단** 에 **에서** 선택한 **저장할**합니다.

    ![Azure App Service 설정 변경 분석 사용자 인터페이스 스크린샷](./media/change-analysis/change-analysis-on.png)

    하는 경우 **변경 분석** 가 사용 하도록 설정 해야 리소스 수준 변경 내용을 검색할 수 있습니다. 하는 경우 **코드 변경 사항 검색** 가 사용 하도록 설정 또한 배포 파일을 참조 하 고 사이트 구성 변경 합니다. 사용 하도록 설정 **무중단** 변경 검색 성능을 최적화 하지만 청구 관점에서 추가 비용이 발생할 수 있습니다.

5.  모든 활성화 되 면 선택 **진단 및 문제 해결** > **가용성과 성능을** > **응용 프로그램이 크래시 되** 하면 변경 분석 환경에 액세스할 수 있습니다. 그래프에서는 이러한 변경 내용에 세부 정보와 함께 시간이 지남에 따라 발생 한 변경 유형을 요약:

     ![변경 diff 보기의 스크린샷](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-service-at-scale"></a>대규모로 변경 분석 서비스를 사용 하도록 설정
많은 웹 앱 구독에 있는 경우 웹 앱 수준에서 서비스를 사용 하도록 설정 효율적인 수 없습니다. 다음은 몇 가지 대체 온 보 딩 지침입니다.

#### <a name="registering-change-analysis-resource-provider-for-your-subscription"></a>구독에 대 한 변경 분석 리소스 공급자 등록

1. 변경 분석 미리 보기 기능 플래그를 등록 합니다.

    이 기능은 미리 보기 이므로 먼저 구독에 표시 되도록 하는 기능 플래그를 등록 해야 합니다.
    - [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)을 엽니다.

    ![Azure Cloud Shell 변화의 스크린 샷](./media/change-analysis/cloud-shell.png)

    - PowerShell 셸 형식을 변경 합니다.

    ![Azure Cloud Shell 변화의 스크린 샷](./media/change-analysis/choose-powershell.png)

    - 다음 PowerShell 명령을 실행합니다.

    ``` PowerShell

    Set-AzContext -Subscription <your_subscription_id> #set script execution context to the subscription you are trying to onboard
    Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.ChangeAnalysis" -ListAvailable #Check for feature flag availability
    Register-AzureRmProviderFeature -FeatureName PreviewAccess -ProviderNamespace Microsoft.ChangeAnalysis #Register feature flag

    ```

2. 구독에 대 한 변경 분석 리소스 공급자 등록

    - 구독을 찾습니다 온보드 하려는 변경 서비스 구독 선택한 리소스 공급자를 클릭 합니다.

        ![구독 블레이드에서 변경 분석 RP 등록에 대 한 스크린 샷](./media/change-analysis/register-rp.png)

    - 선택 *Microsoft.ChangeAnalysis* 누릅니다 *등록* 페이지 맨 위에 있는 합니다.

    - 리소스 공급자 등록 되 면 지침에 따라 *변경 분석 정보를 가져올 수 없습니다* 아래 배포를 사용 하도록 설정 하려면 웹 앱에 숨겨진된 태그를 설정 하려면 수준 변경 웹 앱에서 검색 합니다.

3. 또는 위의 2 단계에 PowerShell 스크립트를 통해 리소스 공급자에 등록할 수 있습니다.

    ```PowerShell
    Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState #Check if RP is ready for registration

    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis" #Register the Change Analysis RP
    ```

4. PowerShell을 사용 하 여 웹 앱에는 숨겨진된 태그를 설정 하려면 다음 명령을 실행 합니다.

    ```powershell
    $webapp=Get-AzWebApp -Name <name_of_your_webapp>
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
    ```

> [!NOTE]
> 숨겨진된 태그 추가 되 면 처음 수 있으려면 먼저 변경 내용을 보려면 최대 4 시간을 대기 해야 계속 합니다. 변경 분석 서비스 검색의 성능 영향을 제한 하는 동안 웹 앱을 검색 하는 데 사용 하는 4 시간 freqeuncy 때문입니다.

## <a name="next-steps"></a>다음 단계

- Azure App Services의 모니터링을 향상 [Application Insights 기능을 사용 하 여](azure-web-apps.md) Azure Monitor의 합니다.
- 에 대 한 이해를 향상 합니다 [Azure 리소스 그래프](https://docs.microsoft.com/azure/governance/resource-graph/overview) power Azure Monitor 응용 프로그램 분석을 변경할 수 있습니다.
