---
title: Azure Monitor에서 응용 프로그램 변경 분석을 사용 하 여 웹 앱 문제 찾기 | Microsoft Docs
description: Azure Monitor에서 응용 프로그램 변경 분석을 사용 하 여 Azure App Service에서 라이브 사이트의 응용 프로그램 문제를 해결할 수 있습니다.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/04/2020
ms.openlocfilehash: e59d4ecd238879eddb9d842245395d58aff28385
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519425"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Azure Monitor에서 응용 프로그램 변경 분석 (미리 보기) 사용

라이브 사이트 문제 또는 중단이 발생 하는 경우 근본 원인을 신속 하 게 확인 하는 것이 중요 합니다. 표준 모니터링 솔루션은 문제를 경고할 수 있습니다. 실패 한 구성 요소를 나타낼 수도 있습니다. 그러나이 경고는 항상 오류의 원인을 즉시 설명 하지는 않습니다. 사이트는 5 분 전에 작동 했으며 이제는 중단 되었습니다. 최근 5 분 동안 변경 된 내용은 무엇 인가요? 이는 응용 프로그램 변경 분석을 Azure Monitor에서 답변 하도록 설계 된 질문입니다.

[Azure 리소스 그래프](../../governance/resource-graph/overview.md)의 기능을 기반으로 하는 변경 분석을 통해 azure 응용 프로그램 변경 내용에 대 한 통찰력을 제공 하 여 관찰성를 늘리고 MTTR (평균 복구 시간)를 줄일 수 있습니다.

> [!IMPORTANT]
> 변경 분석은 현재 미리 보기 상태입니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공 됩니다. 이 버전은 프로덕션 워크 로드에는 권장 되지 않습니다. 일부 기능은 지원 되지 않거나 제한 된 기능을 가질 수 있습니다. 자세한 내용은 Microsoft Azure Preview에 대한 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="overview"></a>개요

변경 분석은 응용 프로그램 배포에 이르기까지 인프라 계층에서 다양 한 유형의 변경 내용을 검색 합니다. 구독의 리소스 변경 내용을 확인 하는 구독 수준 Azure 리소스 공급자입니다. 변경 분석은 사용자가 문제의 원인이 되는 변경 내용을 이해 하는 데 도움이 되는 다양 한 진단 도구에 대 한 데이터를 제공 합니다

다음 다이어그램은 변경 분석의 아키텍처를 보여 줍니다.

![변경 분석에서 변경 데이터를 가져오고 클라이언트 도구에 제공 하는 방법에 대 한 아키텍처 다이어그램](./media/change-analysis/overview.png)

## <a name="supported-resource-types"></a>지원되는 리소스 유형

응용 프로그램 변경 내용 분석 서비스는 다음과 같은 일반적인 리소스를 비롯 하 여 모든 Azure 리소스 유형에 서 리소스 속성 수준 변경을 지원 합니다.
- Virtual Machine
- 가상 머신 크기 집합
- App Service
- Azure Kubernetes 서비스
- Azure Function
- 네트워킹 리소스: 네트워크 보안 그룹, Virtual Network, Application Gateway 등
- Data services: Storage, SQL, Redis Cache, Cosmos DB 등

## <a name="data-sources"></a>데이터 원본

응용 프로그램 변경 분석 쿼리 Azure Resource Manager 추적 된 속성, 프록시 구성 및 웹 앱 게스트 변경 내용에 대 한 쿼리입니다. 또한 서비스는 리소스 종속성 변경을 추적 하 여 종단 간 응용 프로그램을 진단 하 고 모니터링 합니다.

### <a name="azure-resource-manager-tracked-properties-changes"></a>추적 된 속성 변경 내용 Azure Resource Manager

[Azure 리소스 그래프](../../governance/resource-graph/overview.md)를 사용 하 여 변경 분석을 통해 응용 프로그램을 호스트 하는 azure 리소스가 시간에 따라 어떻게 변경 되었는지에 대 한 기록 기록을 제공 합니다. 관리 되는 id, 플랫폼 OS 업그레이드 및 호스트 이름과 같은 추적 된 설정을 검색할 수 있습니다.

### <a name="azure-resource-manager-proxied-setting-changes"></a>프록시 설정 변경 Azure Resource Manager

IP 구성 규칙, TLS 설정, 확장 버전 등의 설정은 Azure 리소스 그래프에서 아직 사용할 수 없으므로 앱에서 변경 된 내용에 대 한 자세한 정보를 제공 하기 위해 분석 쿼리를 변경 하 고 이러한 변경 내용을 안전 하 게 계산 합니다.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>웹 앱 배포 및 구성의 변경 내용 (게스트 내 변경)

변경 분석은 4 시간 마다 응용 프로그램의 배포 및 구성 상태를 캡처합니다. 예를 들어 응용 프로그램 환경 변수의 변경 내용을 검색할 수 있습니다. 이 도구는 차이점을 계산 하 고 변경 된 내용을 표시 합니다. 리소스 관리자 변경과 달리 코드 배포 변경 정보는 도구에서 즉시 사용 하지 못할 수도 있습니다. 변경 분석에서 최신 변경 내용을 보려면 **새로 고침** 을 선택 합니다.

!["지금 변경 내용 검색" 단추의 스크린샷](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>종속성 변경

리소스 종속성을 변경 하면 리소스에서 문제가 발생할 수도 있습니다. 예를 들어 웹 앱이 Redis cache를 호출 하는 경우 Redis cache SKU는 웹 앱 성능에 영향을 줄 수 있습니다. 또 다른 예는 가상 컴퓨터의 네트워크 보안 그룹에서 포트 22를 닫은 경우 연결 오류가 발생 하는 것입니다.

#### <a name="web-app-diagnose-and-solve-problems-navigator-preview"></a>웹 앱 진단 및 문제 해결 탐색기 (미리 보기)

종속성의 변경 내용을 감지 하기 위해 변경 분석은 웹 앱의 DNS 레코드를 확인 합니다. 이러한 방식으로 문제를 일으킬 수 있는 모든 응용 프로그램 구성 요소의 변경 내용을 식별 합니다.
현재 **웹 앱 진단 및 문제 해결에서 지원 되는 종속성은 다음과 같습니다. 탐색기 (미리 보기)**:

- Web Apps
- Azure Storage
- Azure SQL

#### <a name="related-resources"></a>관련 참고 자료

응용 프로그램 변경 분석은 관련 된 리소스를 검색 합니다. 일반적인 예는 가상 머신과 관련 된 네트워크 보안 그룹, Virtual Network, Application Gateway 및 Load Balancer입니다.
네트워크 리소스는 일반적으로 해당 리소스를 사용 하는 리소스와 동일한 리소스 그룹에 자동으로 프로 비전 되므로 리소스 그룹별 변경 내용에 대 한 필터링을 통해 가상 컴퓨터 및 관련 네트워킹 리소스에 대 한 모든 변경 내용이 표시 됩니다.

![네트워킹 변경의 스크린샷](./media/change-analysis/network-changes.png)

## <a name="application-change-analysis-service-enablement"></a>응용 프로그램 변경 분석 서비스 지원

응용 프로그램 변경 분석 서비스는 위에서 언급 한 데이터 원본의 변경 데이터를 계산 하 고 집계 합니다. 사용자가 모든 리소스 변경 사항을 쉽게 탐색 하 고 문제 해결 또는 모니터링 컨텍스트와 관련 된 변경 내용을 식별할 수 있도록 분석 집합을 제공 합니다.
"Microsoft. ChangeAnalysis" 리소스 공급자는 Azure Resource Manager 추적 속성 및 프록시 설정 변경 데이터를 사용할 수 있도록 구독에 등록 해야 합니다. 웹 앱 진단 및 문제 해결 도구를 입력 하거나 변경 분석 독립 실행형 탭을 표시할 때이 리소스 공급자가 자동으로 등록 됩니다.
웹 앱 게스트 변경의 경우 웹 앱 내에서 코드 파일을 검색 하는 데 별도의 기능이 필요 합니다. 자세한 내용은이 문서의 뒷부분에 나오는 [문제 진단 및 해결 도구 섹션에서 변경 분석](change-analysis-visualizations.md#application-change-analysis-in-the-diagnose-and-solve-problems-tool) 을 참조 하세요.

## <a name="cost"></a>비용

응용 프로그램 변경 분석은 무료 서비스입니다. 구독에 대 한 청구 비용은 사용 하도록 설정 되지 않습니다. 또한 서비스는 Azure 리소스 속성 변경 내용 검색에 대 한 성능 영향을 주지 않습니다. 웹 앱 게스트 파일 변경 내용에 대 한 변경 분석을 사용 하도록 설정 하는 경우 (또는 문제 진단 및 해결 도구를 사용 하는 경우), 웹 앱의 성능에 영향을 주지 않으며 요금 청구 비용이 없습니다.

## <a name="enable-change-analysis-at-scale"></a>규모에 맞게 변경 분석 설정

구독에 다양 한 웹 앱이 포함 되어 있는 경우 웹 앱 수준에서 서비스를 사용 하도록 설정 하는 것은 비효율적입니다. 다음 스크립트를 실행 하 여 구독의 모든 웹 앱을 사용 하도록 설정 합니다.

필수 조건:

- PowerShell Az Module. [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps) 의 지침을 따르세요.

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

- [변경 분석의 시각화](change-analysis-visualizations.md) 에 대 한 자세한 정보
- [변경 분석의 문제를 해결](change-analysis-troubleshoot.md) 하는 방법 알아보기
- [Azure 앱 Services 앱](azure-web-apps.md)에 대 한 Application Insights를 사용 하도록 설정 합니다.
- [AZURE VM 및 azure 가상 머신 확장 집합 IIS에서 호스팅되는 앱](azure-vm-vmss-apps.md)에 대 한 Application Insights를 사용 하도록 설정 합니다.
