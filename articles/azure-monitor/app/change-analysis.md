---
title: Azure Monitor에서 애플리케이션 변경 분석을 사용하여 웹앱 문제 찾기 | Microsoft Docs
description: Azure Monitor에서 애플리케이션 변경 분석을 사용하여 Azure App Service에서 라이브 사이트의 애플리케이션 문제를 해결할 수 있습니다.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/04/2020
ms.openlocfilehash: 43ece2cb0f5cb9428d8d73f769018e9fe2408ab8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104655815"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Azure Monitor에서 애플리케이션 변경 분석 사용(미리 보기)

라이브 사이트 문제 또는 중단이 발생하는 경우 근본 원인을 신속하게 확인하는 것이 중요합니다. 표준 모니터링 솔루션을 통해 문제에 대한 경고를 받을 수 있습니다. 실패한 구성 요소를 파악할 수도 있습니다. 그러나 이 경고를 통해 항상 오류의 원인을 즉시 파악할 수 있는 것은 아닙니다. 사이트가 5분 전에는 작동했지만 현재는 중단되었습니다. 최근 5분 동안 무엇이 변경되었나요? 이는 Azure Monitor에서 답변하도록 설계된 애플리케이션 변경 분석 질문입니다.

[Azure Resource Graph](../../governance/resource-graph/overview.md)의 기능을 기반으로 하는 변경 분석을 통해 Azure 애플리케이션 변경 사항에 대한 인사이트를 제공하여 가시성을 늘리고 MTTR(평균 복구 시간)를 줄일 수 있습니다.

> [!IMPORTANT]
> 변경 분석은 현재 미리 보기 상태입니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공됩니다. 프로덕션 워크로드에는 추천하지 않습니다. 특정 기능은 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 Microsoft Azure Preview에 대한 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="overview"></a>개요

변경 분석은 애플리케이션 배포에 이르기까지 인프라 계층에서 다양한 유형의 변경 사항을 검색합니다. 구독 리소스 변경 사항을 확인하는 구독 수준 Azure 리소스 공급자입니다. 변경 분석은 사용자가 문제의 원인이 되는 변경 사항을 이해하는 데 도움이 되는 다양한 진단 도구에 대한 데이터를 제공합니다.

다음 다이어그램에서는 변경 분석의 아키텍처를 보여 줍니다.

![변경 분석에서 변경 데이터를 가져오고 클라이언트 도구에 제공하는 방법에 대한 아키텍처 다이어그램](./media/change-analysis/overview.png)

## <a name="supported-resource-types"></a>지원되는 리소스 유형

애플리케이션 변경 사항 분석 서비스는 다음과 같은 일반적인 리소스를 비롯하여 모든 Azure 리소스 유형에서 리소스 속성 수준 변경을 지원합니다.
- Virtual Machine
- 가상 머신 크기 집합
- App Service
- Azure Kubernetes 서비스
- Azure Function
- 네트워킹 리소스: 네트워크 보안 그룹, Virtual Network, Application Gateway 등
- 데이터 서비스: Storage, SQL, Redis Cache, Cosmos DB 등

## <a name="data-sources"></a>데이터 원본

애플리케이션 변경 분석은 Azure Resource Manager 추적된 속성, 프록시 구성 및 웹 앱 게스트 내 변경 사항을 쿼리합니다. 또한 서비스는 리소스 종속성 변경을 추적하여 종단 간 애플리케이션을 진단하고 모니터링합니다.

### <a name="azure-resource-manager-tracked-properties-changes"></a>Azure Resource Manager 추적된 속성 변경 사항

[Azure Resource Graph](../../governance/resource-graph/overview.md)를 사용하여 변경 분석을 통해 애플리케이션을 호스트하는 Azure 리소스가 시간에 따라 어떻게 변경되었는지에 대한 기록을 제공합니다. 관리 ID, 플랫폼 OS 업그레이드 및 호스트 이름과 같은 추적된 설정을 검색할 수 있습니다.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Azure Resource Manager 프록시 설정 변경

IP 구성 규칙, TLS 설정, 확장 버전 등의 설정은 Azure Resource Graph에서 아직 사용할 수 없으므로 앱에서 변경된 내용에 대한 자세한 정보를 제공하기 위해 분석 쿼리를 변경하고 이러한 변경 사항을 안전하게 계산합니다.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>웹 앱 배포 및 구성 변경 사항(게스트 내 변경 사항)

변경 분석에서는 4시간마다 애플리케이션의 배포 및 구성 상태를 캡처합니다. 예를 들어 애플리케이션 환경 변수의 변경 사항을 검색할 수 있습니다. 이 도구는 차이점을 계산하고 변경된 내용을 표시합니다. 리소스 관리자 변경과 달리 코드 배포 변경 정보는 도구에서 즉시 사용할 수 없을 수도 있습니다. 변경 분석에서 최신 변경 사항을 보려면 **새로 고침** 을 선택합니다.

!["지금 변경 사항 검색" 단추 스크린샷](./media/change-analysis/scan-changes.png)

현재 다음 확장을 포함하는 사이트 루트 **wwwroot** 아래의 모든 텍스트 기반 파일이 지원됩니다.
- *.config
- *.xml
- *.json
- *.gem
- *.yml
- *.txt
- *.ini
- *.env

### <a name="dependency-changes"></a>종속성 변경 사항

리소스 종속성을 변경하면 리소스에서 문제가 발생할 수도 있습니다. 예를 들어 웹 앱에서 Redis cache를 호출하는 경우 Redis cache SKU가 웹 앱 성능에 영향을 줄 수 있습니다. 또 다른 예는 Virtual Machine의 네트워크 보안 그룹에서 포트 22를 닫은 경우 연결 오류가 발생하는 것입니다.

#### <a name="web-app-diagnose-and-solve-problems-navigator-preview"></a>웹 앱 진단 및 문제 해결 탐색기(미리 보기)

종속성의 변경 사항을 감지하기 위해 변경 분석은 웹 앱의 DNS 레코드를 확인합니다. 이러한 방식으로 문제를 일으킬 수 있는 모든 애플리케이션 구성 요소의 변경 사항을 식별합니다.
현재 **웹 앱 진단 및 문제 해결 | 탐색기(미리 보기)** 에서 지원되는 종속성은 다음과 같습니다.

- Web Apps
- Azure Storage
- Azure SQL

#### <a name="related-resources"></a>관련 참고 자료

애플리케이션 변경 분석은 관련된 리소스를 검색합니다. 일반적인 예는 네트워크 보안 그룹, Virtual Network, Application Gateway 및 Virtual Machine과 관련된 Load Balancer입니다.
네트워크 리소스는 일반적으로 해당 리소스를 사용하는 리소스와 동일한 리소스 그룹에 자동으로 프로비전되므로 리소스 그룹별 변경 사항에 대한 필터링을 통해 Virtual Machine 및 관련 네트워킹 리소스에 대한 모든 변경 사항이 표시됩니다.

![네트워킹 변경 사항 스크린샷](./media/change-analysis/network-changes.png)

## <a name="application-change-analysis-service-enablement"></a>애플리케이션 변경 분석 서비스 사용

애플리케이션 변경 분석 서비스는 위에서 언급한 데이터 원본의 변경 데이터를 계산하고 집계합니다. 사용자가 모든 리소스 변경 사항을 쉽게 탐색하고 문제를 해결하거나 모니터링 컨텍스트와 관련된 변경 사항을 식별할 수 있도록 분석 집합을 제공합니다.
"Microsoft.ChangeAnalysis" 리소스 공급자는 Azure Resource Manager 추적 속성 및 프록시 설정 변경 데이터를 사용할 수 있도록 구독을 등록해야 합니다. 웹 앱 진단 및 문제 해결 도구를 입력하거나 변경 분석 독립 실행형 탭을 표시할 때 이 리소스 공급자가 자동으로 등록됩니다.
웹 앱 게스트 내 변경 사항의 경우 웹 앱 내에서 코드 파일을 검색하는 데 별도의 기능이 필요합니다. 자세한 내용은 이 문서 뒷부분의 [진단 및 문제 해결 도구 섹션에서 변경 분석](change-analysis-visualizations.md#application-change-analysis-in-the-diagnose-and-solve-problems-tool)을 참조하세요.

## <a name="cost"></a>Cost
애플리케이션 변경 분석은 무료 서비스입니다. 사용하도록 설정하는데 구독에 대한 비용은 발생하지 않습니다. 또한 서비스는 Azure 리소스 속성 변경 사항 검색 성능에 영향을 주지 않습니다. 웹 앱 게스트 파일 변경 사항에 대한 변경 분석을 사용하도록 설정하는 경우(또는 진단 및 문제 해결 도구를 사용하는 경우), 웹 앱의 성능에 영향을 주지 않으며 요금 청구 비용이 없습니다.


## <a name="enable-change-analysis-at-scale-for-web-app-in-guest-file-and-environment-variable-changes"></a>웹 앱의 게스트 내에 대한 대규모 변경 및 환경 변수 변경 분석을 사용하도록 설정

구독에 다양한 웹 앱이 포함되어 있는 경우 웹 앱 수준에서 서비스를 사용하도록 설정하는 것은 비효율적입니다. 다음 스크립트를 실행하여 구독의 모든 웹 앱을 사용하도록 설정합니다.

필수 조건:

- PowerShell Az 모듈. [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)의 지침을 따름

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

- [변경 분석의 시각화](change-analysis-visualizations.md)에 대한 자세한 정보
- [변경 분석 문제를 해결하는 방법](change-analysis-troubleshoot.md) 알아보기
- [Azure App Service 앱](azure-web-apps.md)에 대해 Application Insights를 사용하도록 설정합니다.
- [Azure VM 및 Azure 가상 머신 확장 집합 IIS에서 호스트하는 앱](azure-vm-vmss-apps.md)에 대해 Application Insights를 사용하도록 설정합니다.
