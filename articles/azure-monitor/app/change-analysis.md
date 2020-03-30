---
title: Azure 모니터에서 응용 프로그램 변경 분석을 사용하여 웹 앱 문제를 찾습니다 | 마이크로 소프트 문서
description: Azure 모니터에서 응용 프로그램 변경 분석을 사용하여 Azure 앱 서비스의 라이브 사이트에서 응용 프로그램 문제를 해결합니다.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/07/2019
ms.openlocfilehash: 036b8c084bdfdc11c02274758c550c76bdc7b1e7
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348742"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Azure 모니터에서 응용 프로그램 변경 분석(미리 보기) 사용

라이브 사이트 문제 또는 중단이 발생하면 근본 원인을 신속하게 파악하는 것이 중요합니다. 표준 모니터링 솔루션은 문제를 경고할 수 있습니다. 실패한 구성 요소를 나타낼 수도 있습니다. 그러나 이 경고가 항상 실패의 원인을 즉시 설명하는 것은 아닙니다. 사이트가 5분 전에 작동했음을 알 수 있으며 이제 는 망가졌습니다. 지난 5분 동안 어떤 변화가 있었는지? 이 질문은 응용 프로그램 변경 분석Azure 모니터에서 답변하도록 설계된 질문입니다.

[Azure 리소스 그래프의](https://docs.microsoft.com/azure/governance/resource-graph/overview)기능을 기반으로 하는 변경 분석은 Azure 응용 프로그램 변경 사항에 대한 통찰력을 제공하여 관찰 가능성을 높이고 MTTR(평균 복구 시간)을 줄입니다.

> [!IMPORTANT]
> 변경 분석은 현재 미리 보기 상태입니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공됩니다. 이 버전은 프로덕션 워크로드에는 권장되지 않습니다. 일부 기능은 지원되지 않거나 제한된 기능이 있을 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관을](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)참조하십시오.

## <a name="overview"></a>개요

변경 분석은 인프라 계층에서 응용 프로그램 배포에 이르는 다양한 유형의 변경을 감지합니다. 구독의 리소스 변경 내용을 확인하는 구독 수준 Azure 리소스 공급자입니다. 변경 분석은 사용자가 변경으로 인해 문제가 발생할 수 있는 사항을 이해하는 데 도움이 되는 다양한 진단 도구에 대한 데이터를 제공합니다.

다음 다이어그램은 변경 해석의 아키텍처를 보여 줍니다.

![변경 분석이 변경 데이터를 얻고 클라이언트 도구에 제공하는 방법에 대한 아키텍처 다이어그램](./media/change-analysis/overview.png)

현재 변경 분석은 앱 서비스 웹 앱의 **진단 및 문제** 발생 에 통합되어 있을 뿐만 아니라 Azure Portal에서 독립 실행형 탭으로 사용할 수 있습니다.
이 문서의 후반부 웹 앱 포털에서 사용하기 위한 변경 분석 블레이드 및 *웹 앱에 대한 변경 분석 기능* 섹션에 액세스하려면 Azure 섹션의 모든 *리소스에 대한 변경 내용 보기를* 참조하십시오.

### <a name="azure-resource-manager-tracked-properties-changes"></a>Azure 리소스 관리자 추적 속성 변경 내용

[Azure 리소스 그래프](https://docs.microsoft.com/azure/governance/resource-graph/overview)를 사용하여 변경 분석은 응용 프로그램을 호스트하는 Azure 리소스가 시간이 지남에 따라 어떻게 변경되었는지에 대한 기록 레코드를 제공합니다. 관리되는 ID, 플랫폼 OS 업그레이드 및 호스트 이름과 같은 추적된 설정을 검색할 수 있습니다.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Azure 리소스 관리자 프록시 설정 변경 내용
IP 구성 규칙, TLS 설정 및 확장 버전과 같은 설정은 아직 ARG에서 사용할 수 없으므로 변경 분석 쿼리를 쿼리하고 계산하여 앱에서 변경된 내용에 대한 자세한 내용을 제공합니다. 이 정보는 Azure 리소스 그래프에서 아직 사용할 수 없지만 곧 사용할 수 있습니다.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>웹 앱 배포 및 구성 변경(게스트 내 변경)

변경 분석은 4시간마다 응용 프로그램의 배포 및 구성 상태를 캡처합니다. 예를 들어 응용 프로그램 환경 변수의 변경 내용을 검색할 수 있습니다. 이 도구는 차이점을 계산하고 변경된 내용을 제공합니다. 리소스 관리자 변경과 달리 코드 배포 변경 정보는 도구에서 즉시 사용하지 못할 수 있습니다. 변경 해석의 최신 변경 내용을 보려면 **지금 검사 변경 내용을**선택합니다.

!["지금 스캔 변경" 버튼의 스크린샷](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>종속성 변경

리소스 종속성을 변경하면 웹 앱에서 문제가 발생할 수도 있습니다. 예를 들어 웹 앱이 Redis 캐시를 호출하는 경우 Redis 캐시 SKU는 웹 앱 성능에 영향을 줄 수 있습니다. 종속성의 변화를 감지하기 위해 변경 분석은 웹 앱의 DNS 레코드를 확인합니다. 이러한 방식으로 문제를 일으킬 수 있는 모든 앱 구성 요소의 변경 내용을 식별합니다.
현재 다음과 같은 종속성이 지원됩니다.
- Web Apps
- Azure Storage
- Azure SQL

### <a name="enablement"></a>사용 여부
"Microsoft.ChangeAnalysis" 리소스 공급자는 Azure Resource Manager 추적 속성에 대한 구독에 등록해야 하며 사용할 수 있는 프록시 설정 변경 데이터를 사용할 수 있습니다. 웹 앱을 입력하면 문제 진단 및 해결 도구 또는 변경 분석 독립 실행형 탭이 나타나면 이 리소스 공급자가 자동으로 등록됩니다. 구독에 대한 성능 및 비용 구현이 없습니다. 웹 앱에 대한 변경 분석을 사용하도록 설정하거나 문제 진단 및 해결 도구에서 사용하도록 설정하면 웹 앱에 미치는 성능에 거의 영향을 미치지 않으며 청구 비용이 없습니다.
웹 앱 인게스트 변경의 경우 웹 앱 내에서 코드 파일을 검색하려면 별도의 사용이 필요합니다. 자세한 내용은 [진단 및 문제 해결 도구 섹션의 변경 활성화 를](https://docs.microsoft.com/azure/azure-monitor/app/change-analysis#enable-change-analysis-in-the-diagnose-and-solve-problems-tool) 참조하여 자세한 내용을 보려면 이 문서의 다음 을 참조하십시오.


## <a name="viewing-changes-for-all-resources-in-azure"></a>Azure의 모든 리소스에 대한 변경 내용 보기
Azure Monitor에는 인사이트 및 응용 프로그램 종속성 리소스를 사용하여 모든 변경 내용을 볼 수 있는 변경 분석을 위한 독립 실행형 블레이드가 있습니다.

Azure 포털의 검색 창에서 변경 분석을 검색하여 블레이드를 시작합니다.

![Azure 포털에서 변경 변경 분석을 검색하는 스크린샷](./media/change-analysis/search-change-analysis.png)

변경 내용 보기를 시작하려면 리소스 그룹 및 리소스를 선택합니다.

![Azure 포털에서 변경 분석 블레이드의 스크린샷](./media/change-analysis/change-analysis-standalone-blade.png)

응용 프로그램을 호스트하는 Insights 및 관련 종속성 리소스를 볼 수 있습니다. 이 보기는 개발자가 문제를 해결할 수 있도록 응용 프로그램 중심으로 설계되었습니다.

현재 지원되는 리소스는 다음과 같습니다.
- Virtual Machines
- 가상 머신 스케일 세트
- Azure 네트워킹 리소스
- 게스트 내 파일 추적 및 환경 변수 변경 사항이 있는 웹 앱

모든 피드백을 위해 블레이드 또는 전자 메일의 changeanalysisteam@microsoft.com피드백 보내기 단추를 사용합니다.

![변경 분석 블레이드의 피드백 버튼 스크린샷](./media/change-analysis/change-analysis-feedback.png)

## <a name="change-analysis-for-the-web-apps-feature"></a>웹 앱 기능에 대한 변경 분석 기능

Azure 모니터에서 변경 분석은 셀프 서비스 진단 및 문제 환경 **해결에도** 기본 제공됩니다. 앱 서비스 응용 프로그램의 **개요** 페이지에서 이 경험에 액세스합니다.

!["개요" 버튼과 "문제 진단 및 해결" 버튼의 스크린샷](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>문제 진단 및 해결 도구에서 변경 분석 활성화

1. **가용성 및 성능을**선택합니다.

    !["가용성 및 성능" 문제 해결 옵션의 스크린샷](./media/change-analysis/availability-and-performance.png)

1. **응용 프로그램 변경 내용을**선택합니다. 응용 **프로그램 충돌에서도**이 기능을 사용할 수 없습니다.

   !["응용 프로그램 충돌" 단추의 스크린샷](./media/change-analysis/application-changes.png)

1. 변경 분석을 사용하려면 **지금 사용]을 선택합니다.**

   !["응용 프로그램 충돌" 옵션의 스크린샷](./media/change-analysis/enable-changeanalysis.png)

1. 변경 **분석을** 켜고 **저장을**선택합니다. 이 도구는 앱 서비스 요금제 아래에 모든 웹 앱을 표시합니다. 계획 수준 스위치를 사용하여 계획에 따라 모든 웹 앱에 대한 변경 분석을 켤 수 있습니다.

    !["변경 분석 사용" 사용자 인터페이스의 스크린샷](./media/change-analysis/change-analysis-on.png)


1. 변경 분석에 액세스하려면 **문제** > 진단 및 해결**가용성 및 성능** > **응용 프로그램 충돌을 선택합니다.** 시간에 따른 변경 유형과 해당 변경 사항에 대한 세부 정보를 요약한 그래프가 표시됩니다. 기본적으로 지난 24시간의 변경 내용이 표시되어 즉각적인 문제를 해결할 수 있습니다.

     ![변경 개요의 스크린샷](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>대규모 변경 분석 활성화

구독에 수많은 웹 앱이 포함된 경우 웹 앱 수준에서 서비스를 사용하도록 설정하는 것은 비효율적입니다. 다음 스크립트를 실행하여 구독의 모든 웹 앱을 사용하도록 설정합니다.

필수 조건:
* 파워 쉘 아즈 모듈. [Azure PowerShell 설치 모듈의](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.6.0) 지침 따르기

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

- Azure 앱 서비스 앱에 대한 응용 프로그램 인사이트를 [활성화합니다.](azure-web-apps.md)
- [Azure VM 및 Azure 가상 시스템 규모 집합 IIS 호스팅 앱에](azure-vm-vmss-apps.md)대한 응용 프로그램 인사이트를 활성화합니다.
- 변경 분석을 하는 데 도움이 되는 [Azure 리소스 그래프에](https://docs.microsoft.com/azure/governance/resource-graph/overview)대해 자세히 알아봅니다.
