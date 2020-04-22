---
title: Azure 모니터 로그에 대한 사용량 및 비용 관리 | 마이크로 소프트 문서
description: Azure Monitor의 로그 분석 작업 영역에 대한 가격 책정 계획을 변경하고 데이터 볼륨 및 보존 정책을 관리하는 방법을 알아봅니다.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 9a7d0530c4f03138fad3e4aaa473d54e1cfd5b0a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686569"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Azure 모니터 로그를 통해 사용량 및 비용 관리

> [!NOTE]
> 이 문서에서는 Azure 모니터 로그에 대한 비용을 이해하고 제어하는 방법을 설명합니다. 관련 문서, [모니터링 사용량 및 예상 비용은](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) 다양한 가격 책정 모델에 대한 여러 Azure 모니터링 기능에서 사용량 및 예상 비용을 보는 방법을 설명합니다. 이 문서에 표시된 모든 가격과 비용은 예를 들어 목적으로만 사용됩니다. 

Azure Monitor Logs는 엔터프라이즈의 모든 원본또는 Azure에 배포된 모든 원본에서 하루에 방대한 양의 데이터를 수집, 인덱싱 및 저장하도록 확장 및 지원하도록 설계되었습니다.  조직에 대한 주 드라이버인 반면 비용 효율성은 궁극적으로 기본 드라이버입니다. 이를 위해 Log Analytics 작업 영역의 비용은 수집된 데이터의 양만을 기반으로 하는 것이 아니라 선택한 계획및 연결된 소스에서 생성된 데이터를 저장하는 데 선택한 기간에도 따라 달라집니다.  

이 문서에서는 수집된 데이터 볼륨 및 저장소 증가를 사전에 모니터링하고 관련 비용을 제어하기 위한 제한을 정의하는 방법을 검토합니다. 

## <a name="pricing-model"></a>가격 책정 모델

Log Analytics의 기본 가격은 수집된 데이터 볼륨을 기반으로 하는 **종량제** 모델이며 선택적으로 더 긴 데이터 보존을 위해 선택적으로 설정합니다. 데이터 볼륨은 저장될 데이터의 크기로 측정됩니다. 각 Log Analytics 작업 영역은 별도의 서비스로 청구되며 Azure 구독에 대한 청구서에 기여합니다. 데이터 수집량은 다음 요인에 따라 상당히 다를 수 있습니다. 

  - 활성화된 관리 솔루션 수 및 구성(예: 
  - 모니터링되는 VM 수
  - 모니터링되는 각 VM에서 수집된 데이터 유형 
  
종량제 모델 외에도 Log Analytics에는 **용량 예약** 계층이 있어 종량제 가격대비 최대 25%를 절약할 수 있습니다. 용량 예약 가격을 사용하면 1일 100GB부터 예약을 구매할 수 있습니다. 예약 수준 이상의 모든 사용량은 종량제 요금으로 청구됩니다. 용량 예약 계층에는 31일 약정 기간이 있습니다. 약정 기간 동안 더 높은 수준의 용량 예약 계층(31일 약정 기간을 다시 시작)으로 변경할 수 있지만 약정 기간이 완료될 때까지 종량제 또는 더 낮은 용량 예약 계층으로 이동할 수 없습니다. 용량 예약 계층에 대한 청구는 매일 이루어집니다. 로그 애널리틱스 종량제 및 용량 예약 가격에 대해 [자세히 알아보세요.](https://azure.microsoft.com/pricing/details/monitor/) 

모든 가격 책정 계층에서 데이터 볼륨은 저장될 준비가 된 데이터의 문자열 표현에서 계산됩니다. [모든 데이터 형식에 공통적인](https://docs.microsoft.com/azure/azure-monitor/platform/log-standard-properties) 몇 가지 속성은 을 `_ResourceId` `_ItemId` `_IsBillable` 포함하여 이벤트 `_BilledSize`크기 계산에 포함되지 않습니다.

또한 [Azure 보안 센터](https://azure.microsoft.com/pricing/details/security-center/) 및 [Azure Sentinel과](https://azure.microsoft.com/pricing/details/azure-sentinel/)같은 일부 솔루션에는 자체 가격 책정 모델이 있습니다. 

## <a name="estimating-the-costs-to-manage-your-environment"></a>환경 관리 비용 예측 

아직 Azure 모니터 로그를 사용하지 않는 경우 [Azure Monitor 가격 계산기를](https://azure.microsoft.com/pricing/calculator/?service=monitor) 사용하여 Log Analytics 사용 비용을 예측할 수 있습니다. 먼저 검색 상자에 "Azure 모니터"를 입력하고 결과 Azure 모니터 타일을 클릭합니다. 페이지를 아래로 스크롤하여 Azure 모니터로 이동한 다음 유형 드롭다운에서 로그 분석을 선택합니다.  여기에서 각 VM에서 수집할 것으로 예상되는 데이터의 수와 GB를 입력할 수 있습니다. 일반적으로 1~3GB의 데이터 월은 일반적인 Azure VM에서 수집됩니다. 이미 Azure 모니터 로그를 이미 평가중인 경우 사용자 고유의 환경에서 데이터 통계를 사용할 수 있습니다. [모니터링되는 VM 수와](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) [작업 영역에서 수집되는 데이터의 양을](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)확인하는 방법은 아래를 참조하십시오. 

## <a name="understand-your-usage-and-estimate-costs"></a>사용량 파악 및 예상 비용

지금 Azure Monitor Logs를 사용하는 경우 최근 사용 패턴을 기반으로 하는 비용을 쉽게 이해할 수 있습니다. 이렇게 하려면 **로그 분석 사용량 및 예상 비용을** 사용하여 데이터 사용량을 검토하고 분석합니다. 이는 각 솔루션에서 수집되는 데이터의 양, 보존되는 데이터의 양 및 수집된 데이터 양과 포함된 양을 초과하는 추가 보존에 따른 비용 추정을 보여줍니다.

![사용량 및 예상 비용](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

데이터를 더 자세히 탐색하려면 **사용량 및 예상 비용** 페이지의 차트 중 하나의 오른쪽 상단 모서리에 있는 아이콘을 클릭합니다. 이제 이 쿼리에 대해 작업하여 사용량을 자세히 살펴볼 수 있습니다.  

![로그 보기](media/manage-cost-storage/logs.png)

**사용량 및 예상 비용** 페이지에서 해당 월의 데이터 볼륨을 검토할 수 있습니다. 여기에는 Log Analytics 작업 영역에 수신되고 보존된 모든 데이터가 포함됩니다.  페이지 상단에서 **사용 내역을** 클릭하여 소스, 컴퓨터 및 제품별 데이터 볼륨 추세에 대한 정보가 있는 사용 대시보드를 봅니다. 보존 기간을 수정하거나 일일 한도를 설정하고 확인하려면 **데이터 볼륨 관리**를 클릭합니다.
 
Log Analytics 요금은 Azure 청구서에 추가됩니다. Azure 청구서의 자세한 내용은 Azure Portal의 청구 섹션 또는 [Azure 청구 포털](https://account.windowsazure.com/Subscriptions)에서 참고할 수 있습니다.  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Azure 청구서에서 로그 분석 사용량 보기 

Azure는 [Azure 비용 관리 + 청구](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) 허브에서 많은 유용한 기능을 제공합니다. 예를 들어 "비용 분석" 기능을 사용하면 Azure 리소스에 대한 지출을 볼 수 있습니다. 리소스 유형별 필터를 추가하면(Log Analytics의 microsoft.운영 인사이트/작업 영역에) 지출을 추적할 수 있습니다.

[Azure 포털에서 사용량을 다운로드하여 사용량에](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal)대한 더 많은 이해를 얻을 수 있습니다. 다운로드한 스프레드시트에서 하루에 Azure 리소스(예: 로그 분석 작업 영역)당 사용량을 볼 수 있습니다. 이 Excel 스프레드시트에서 로그 분석 작업 영역의 사용량은 먼저 "미터 범주" 열을 필터링하여 "인사이트 및 분석" (일부 레거시 가격 책정 계층에서 사용) 및 "Log Analytics"를 표시한 다음 "작업 영역 포함"인 "인스턴스 ID" 열에 필터를 추가하여 찾을 수 있습니다. 사용량은 "소모된 수량" 열에 표시되고 각 항목의 단위는 "측정 단위" 열에 표시됩니다.  자세한 내용은 [Microsoft Azure 청구서를 이해하는](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)데 도움이 됩니다. 

## <a name="changing-pricing-tier"></a>가격 책정 계층 변경

작업 영역의 로그 분석 가격 책정 계층을 변경하려면 

1. Azure 포털에서 이 작업 영역에서 사용할 수 있는 각 가격 책정 계층의 목록을 볼 수 있는 작업 영역에서 **사용 및 예상 비용을** 엽니다.

2. 각 가격 책정 계층의 예상 비용을 검토합니다. 이 추정치는 지난 31일 사용량을 기준으로 하므로 이 비용 추정값은 일반적인 사용량을 대표하는 지난 31일에 의존합니다. 아래 예제에서는 지난 31일 간의 데이터 패턴을 기반으로 이 작업 영역이 종량제 계층(#1)에서 100GB/일 용량 예약 계층(#2)에 비해 비용이 적게 드는 방법을 확인할 수 있습니다.  

    ![가격 책정 계층](media/manage-cost-storage/pricing-tier-estimated-costs.png)

3. 지난 31일 사용량을 기준으로 예상 비용을 검토한 후 가격 책정 계층을 변경하려면 선택 을 **클릭합니다.**  

매개 변수(Azure`pricingTier` 리소스 관리자 템플릿)를 사용하여 [Azure 리소스 관리자를 통해 가격 책정 계층을 설정할](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) 수도 있습니다. `sku` 

## <a name="legacy-pricing-tiers"></a>레거시 가격 책정 계층

2018년 4월 2일 이전에 Log Analytics 작업 영역 또는 애플리케이션 인사이트 리소스가 있거나 2019년 2월 1일 이전에 시작된 엔터프라이즈 계약에 연결된 구독은 레거시 가격 책정 계층인 **무료**, **GB당 독립 실행형(GB당)** 및 **노드당(OMS)을**계속 사용할 수 있습니다.  무료 가격 책정 계층의 작업 영역에는 일일 데이터 수집이 500MB로 제한되며(Azure Security Center에서 수집한 보안 데이터 유형 제외) 데이터 보존기간은 7일로 제한됩니다. 무료 가격 책정 계층은 평가목적으로만 사용됩니다. 독립 실행형 또는 노드별 가격 책정 계층의 작업 영역에는 사용자가 구성할 수 있는 보존기간이 30일에서 730일까지입니다.

모니터링되는 VM(노드당 노드당 가격 책정 계층)은 1시간 단위로 청구됩니다. 모니터링되는 각 노드에 대해 작업 영역에는 청구되지 않은 하루에 500MB의 데이터가 할당됩니다. 이 할당은 작업 영역 수준에서 집계됩니다. 집계 일별 데이터 할당 위에 수집된 데이터는 데이터 초과량으로 GB당 청구됩니다. 청구서에서 작업 영역이 노드별 가격 책정 계층에 있는 경우 서비스는 로그 분석 사용에 대한 **인사이트 및 분석이** 됩니다. 

> [!TIP]
> 작업 영역에 **노드별** 가격 책정 계층에 액세스할 수 있지만 종량제 계층에서 비용이 적게 드는지 궁금한 경우 [아래 쿼리를 사용하여](#evaluating-the-legacy-per-node-pricing-tier) 권장 사항을 쉽게 얻을 수 있습니다. 

2016년 4월 이전에 생성된 작업 공간은 각각 30일과 365일의 고정 데이터 보존이 있는 원래 **표준** 및 **프리미엄** 가격 책정 계층에 액세스할 수도 있습니다. **표준** 또는 **프리미엄** 가격 책정 계층에서 새 작업 영역을 만들 수 없으며 작업 영역이 이러한 계층 밖으로 이동되면 다시 이동할 수 없습니다. 

가격 책정 계층 제한에 대한 자세한 내용은 [여기에서](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces)확인할 수 있습니다.

> [!NOTE]
> System Center용 OMS E1 Suite, OMS E2 Suite 또는 OMS 추가 기능을 구매할 때 제공되는 자격을 사용하려면 Log Analytics의 *노드별* 가격 책정 계층을 선택합니다.

## <a name="change-the-data-retention-period"></a>데이터 보존 기간 변경

다음 단계에서는 로그 데이터가 작업 영역에 보존되는 기간을 구성하는 방법을 설명합니다. 데이터 보존은 레거시 무료 가격 책정 계층을 사용하지 않는 한 모든 작업 영역에 대해 30일에서 730일(2년)까지 구성할 수 있습니다. 

### <a name="default-retention"></a>기본 보존

작업 영역에 대한 기본 보존을 설정하려면 
 
1. Azure 포털에서 작업 영역에서 왼쪽 창에서 **사용 및 예상 비용을** 선택합니다.
2. **사용량 및 예상 비용** 창의 페이지의 상단에서 **데이터 볼륨 관리**를 클릭합니다.
3. 창에서 슬라이더를 이동하여 일 수를 늘리거나 줄인 다음, **확인**을 클릭합니다.  *무료* 계층에서 작업 중인 경우 데이터 보존 기간을 수정할 수 없으며 이 설정을 제어하기 위해 유료 계층으로 업그레이드해야 합니다.

    ![작업 영역 데이터 보존 설정 변경](media/manage-cost-storage/manage-cost-change-retention-01.png)

보존이 낮아지면 가장 오래된 데이터가 제거되기 전에 며칠의 유예 기간이 있습니다. 
    
매개 변수를 [사용하여 Azure 리소스 관리자를 통해](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) 보존을 `retentionInDays` 설정할 수도 있습니다. 또한 데이터 보존을 30일로 설정하면 `immediatePurgeDataOn30Days` 매개 변수를 사용하여 이전 데이터의 즉각적인 제거를 트리거할 수 있으며, 이는 규정 준수 관련 시나리오에 유용할 수 있습니다. 이 기능은 Azure 리소스 관리자를 통해서만 노출됩니다. 

기본적으로 두 `Usage` 가지 `AzureActivity` 데이터 유형이 기본적으로 90일 동안 유지되며 이 90일 보존에 대한 요금은 없습니다. 이러한 데이터 형식은 데이터 수집 비용도 무료입니다. 



### <a name="retention-by-data-type"></a>데이터 유형별 보존

또한 30일에서 730일까지 개별 데이터 유형에 대해 서로 다른 보존 설정을 지정할 수도 있습니다(레거시 무료 가격 책정 계층의 작업 영역 제외). 각 데이터 형식은 작업 영역의 하위 리소스입니다. 예를 들어 SecurityEvent 테이블은 [Azure 리소스 관리자에서](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 다음과 같이 해결할 수 있습니다.

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

데이터 형식(테이블)은 대/소문자를 구분합니다.  특정 데이터 형식의 데이터 형식 보존 설정당 현재를 얻으려면(이 예제 SecurityEvent)을 사용하십시오.

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

작업 영역의 모든 데이터 형식에 대한 현재 데이터 형식 보존 설정을 얻으려면 특정 데이터 형식을 생략하십시오.

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

특정 데이터 형식(이 예제 SecurityEvent)의 보존을 730일로 설정하려면

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

유효한 `retentionInDays` 값은 30에서 730 사이입니다.

및 `Usage` `AzureActivity` 데이터 형식은 사용자 지정 보존으로 설정할 수 없습니다. 기본 작업 영역 보존 또는 90일의 최대 작업을 수행합니다. 

Azure 리소스 관리자에 직접 연결하여 데이터 유형별 보존을 설정하는 훌륭한 도구는 OSS 도구 [ARMclient](https://github.com/projectkudu/ARMClient)입니다.  [데이비드 Ebbo와](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) [다니엘 보비즈의](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/)기사에서 ARMclient에 대해 자세히 알아보십시오.  다음은 ARMClient를 사용하여 SecurityEvent 데이터를 730일 보존으로 설정하는 예제입니다.

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!TIP]
> 개별 데이터 유형에 대한 보존 을 설정하면 데이터 보존 비용을 절감할 수 있습니다.  2019년 10월부터 수집된 데이터의 경우(이 기능이 릴리스된 경우) 일부 데이터 형식의 보존기간을 줄이면 시간이 지남에 따라 보존 비용이 감소할 수 있습니다.  이전에 수집된 데이터의 경우 개별 형식에 대한 보존 기간을 낮게 설정하면 보존 비용에 영향을 주지 않습니다.  

## <a name="manage-your-maximum-daily-data-volume"></a>최대 일일 데이터 볼륨 관리

작업 영역에 대한 일일 한도를 구성하고 일일 수집량을 제한할 수 있지만 목표치가 일일 한도에 도달하지 않도록 주의하십시오.  그렇지 않으면 남은 기간 동안의 데이터가 손실됩니다. 이는 해당 기능이 작업 영역에서 사용할 수 있는 최신 데이터에 의존할 수도 있는 다른 Azure 서비스 및 솔루션에 영향을 줄 수 있습니다.  결과적으로 리소스의 상태 조건이 IT 서비스를 지원할 때 경고를 관찰하고 수신하는 기능이 영향을 받습니다.  일일 한도는 관리되는 리소스에서 예기치 않은 데이터 볼륨 증가를 관리하고 한도 내에서 유지하거나 작업 영역에 대한 계획되지 않은 요금을 제한하려는 경우 를 관리하는 방법으로 사용됩니다.  

일일 한도에 도달하면 청구 가능한 데이터 형식의 수집이 하루 의 나머지 시간 동안 중지됩니다. (일일 한도 적용에 내재된 대기 시간은 캡이 지정된 일일 한도 수준으로 정확하게 적용되지 않음을 의미할 수 있습니다.) 선택한 Log Analytics 작업 영역의 페이지 상단에 경고 배너가 표시되고 작업 이벤트가 **LogManagement** 범주 *아래의 작업* 테이블로 전송됩니다. *일일 한도 아래 정의된 재설정 시간이 *로 설정된 후 데이터 수집이 다시 시작합니다. 일일 데이터 한도에 도달했을 때 알려주도록 구성된 이 작업 이벤트를 기반으로 경고 규칙을 정의하는 것이 좋습니다. 

> [!WARNING]
> 일일 한도는 2017년 6월 19일 이전에 Azure 보안 센터가 설치된 작업 영역을 제외하고 Azure 보안 센터에서 데이터 수집을 중지하지 않습니다. 

### <a name="identify-what-daily-data-limit-to-define"></a>정의할 일일 데이터 한도 식별

데이터 수집 추세 및 정의할 일일 볼륨 한도를 이해하려면 [Log Analytics 사용량 및 예상 비용](usage-estimated-costs.md)을 검토합니다. 한도에 도달한 후 리소스를 모니터링할 수 없으므로 주의해서 고려해야 합니다. 

### <a name="set-the-daily-cap"></a>일일 캡 설정

다음 단계에서는 Log Analytics 작업 영역이 하루에 수집할 데이터의 양을 관리하기 위해 제한을 구성하는 방법을 설명합니다.  

1. 작업 영역의 왼쪽 창에서 **사용량 및 예상 비용**을 선택합니다.
2. 선택한 작업 영역에 대한 **사용량 및 예상 비용** 창의 페이지의 상단에서 **데이터 볼륨 관리**를 클릭합니다. 
3. 일일 한도는 기본적으로 **ON을** 클릭하여 활성화한 다음 GB/day의 데이터 볼륨 제한을 설정합니다. **ON**

    ![Log Analytics 데이터 제한 구성](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>일일 한도에 도달하면 경고

데이터 제한 임계값에 도달하는 경우 Azure Portal에 시각적 큐를 표시하는 반면, 이 동작은 즉각적인 주의가 필요한 운영 문제를 관리하는 방법에 맞출 필요는 없습니다.  경고 알림을 수신하려면 Azure Monitor에서 새 경고 규칙을 만들 수 있습니다.  자세한 내용은 [경고를 생성, 보기 및 관리하는 방법을 참조하세요.](alerts-metric.md)

시작하려면 경고에 대한 권장 설정은 다음과 같습니다.

- 대상: Log Analytics 리소스 선택
- 조건: 
   - 신호 이름: 사용자 지정 로그 검색
   - 검색 쿼리: 작업 | 세부 정보에 'OverQuota'가 있는 경우
   - 기준: 결과 수
   - 조건: 보다 큼
   - 임계값: 0
   - 기간: 5(분)
   - 빈도: 5(분)
- 경고 규칙 이름: 일일 데이터 한계 도달
- 심각도: 경고(심각도 1)

경고가 정의되고 한계에 도달하면 경고가 트리거되고 작업 그룹에서 정의된 응답을 수행합니다. 이메일 및 텍스트 메시지를 통해 팀에 알리거나 웹후크, Automation Runbook 또는 [외부 ITSM 솔루션을 사용해 통합](itsmc-overview.md#create-itsm-work-items-from-azure-alerts)을 사용하여 작업을 자동화할 수 있습니다. 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>사용량이 예상보다 더 높은 원인 해결

사용량이 높은 원인은 다음과 같습니다.
- 로그 분석 작업 공간으로 데이터를 보내는 예상보다 많은 노드
- Log Analytics 작업 영역으로 전송되는 예상 보다 많은 데이터(새 솔루션 사용 또는 기존 솔루션에 대한 구성 변경으로 인해)

## <a name="understanding-nodes-sending-data"></a>데이터를 보내는 노드 이해

지난 달에 에이전트에서 매일 하트비트를 보고하는 노드 수를 이해하려면

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
지난 24시간 동안 데이터를 보내는 노드 수를 얻는 것은 쿼리를 사용합니다. 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

데이터를 보내는 노드 목록(및 각 데이터에서 전송되는 데이터 양)을 얻으려면 다음 쿼리를 사용할 수 있습니다.

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

> [!TIP]
> 여러 데이터 형식을 검색할 경우 비용이 많이 들기 때문에 이러한 `union withsource = tt *` 쿼리는 자주 사용하지 않도록 합니다. 이 쿼리는 컴퓨터별 정보를 쿼리하는 이전 방법을 사용 데이터 유형으로 바꿉습니다.  

## <a name="understanding-ingested-data-volume"></a>수집된 데이터 볼륨 이해

**사용량 및 예상 비용** 페이지에서 *솔루션당 데이터 수집* 차트는 전송된 총 데이터 양과 각 솔루션이 전송하는 데이터 양을 보여 줍니다. 이를 통해 전체 데이터 사용량(또는 특정 솔루션에 의한 사용량)이 증가하는지, 고정적인지, 감소하는지 여부의 추세를 판단할 수 있습니다. 

### <a name="data-volume-for-specific-events"></a>특정 이벤트에 대한 데이터 볼륨

특정 이벤트 집합에 대해 수집된 데이터의 크기를 살펴보기 위해 특정 테이블(이 `Event`예제에서)을 쿼리한 다음 관심 있는 이벤트로 쿼리를 제한할 수 있습니다(이 예제 이벤트 ID 5145 또는 5156).

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
``` 

절은 `where IsBillable = true` 수집 요금이 부과되지 않는 특정 솔루션의 데이터 형식을 필터링합니다. 

### <a name="data-volume-by-solution"></a>솔루션별 데이터 볼륨

지난 달 동안 솔루션별로 청구 가능한 데이터 볼륨을 보는 데 사용되는 쿼리는 다음과 같습니다(마지막 부분 일 제외).

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution | render barchart
```

이 `TimeGenerated` 절은 Azure Portal의 쿼리 환경이 기본 24시간 을 초과하는 것을 다시 볼 수 있도록 하기 위한 것입니다. 사용 데이터 형식을 `StartTime` 사용하는 `EndTime` 경우 결과가 표시되는 시간 버킷을 나타냅니다. 

### <a name="data-volume-by-type"></a>유형별 데이터 볼륨

추가로 드릴다운하여 데이터 유형별 데이터 추세를 확인할 수 있습니다.

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType | render barchart
```

또는 지난 달의 솔루션 및 유형별 표를 보려면

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>컴퓨터별 데이터 볼륨

데이터 `Usage` 형식에는 완전수준 정보가 포함되지 않습니다. 컴퓨터당 수집된 데이터 **크기를** 보려면 바이트 `_BilledSize` 크기를 제공하는 [속성을](log-standard-properties.md#_billedsize)사용합니다.

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

이 `_IsBillable` [속성은](log-standard-properties.md#_isbillable) 수집된 데이터에 요금이 부과되는지 여부를 지정합니다.

컴퓨터당 청구 가능한 이벤트 **수를** 확인하려면 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  | sort by eventCount nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Azure 리소스, 리소스 그룹 또는 구독별 데이터 볼륨

Azure에서 호스팅되는 노드의 데이터에 대해 __컴퓨터당__수집된 데이터의 **크기를** 가져와 리소스에 대한 전체 경로를 제공하는 _ResourceId [속성을](log-standard-properties.md#_resourceid)사용할 수 있습니다.

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

Azure에서 호스팅되는 노드의 데이터에 대해 __Azure 구독당__수집된 데이터 **크기를** 가져와 `_ResourceId` 속성을 다음과 같이 구문 분석할 수 있습니다.

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize BillableDataBytes = sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

Azure `subscriptionId` `resourceGroup` 리소스 그룹에서 청구 가능한 수집된 데이터 볼륨을 표시 합니다. 

> [!WARNING]
> 사용량 데이터 형식의 일부 필드가 여전히 스키마에 있지만 더 이상 사용되지 않으며 해당 값은 더 이상 채워지지 않습니다. 이는 **컴퓨터**일 뿐 아니라 수집과 관련된 필드(**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** 및 **AverageProcessingTimeMs**)이기도 합니다.

### <a name="querying-for-common-data-types"></a>공통 데이터 형식에 대한 쿼리

특정 데이터 형식의 데이터 소스를 더 자세히 알아보려면 다음 예와 같은 몇 가지 쿼리를 사용합니다.

+ **보안** 솔루션
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ **로그 관리** 솔루션
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ **성능** 데이터 형식
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ **이벤트** 데이터 형식
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ **Syslog** 데이터 형식
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ **AzureDiagnostics** 데이터 형식
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

### <a name="tips-for-reducing-data-volume"></a>데이터 볼륨을 줄이기 위한 팁

수집된 로그 볼륨을 줄이기 위한 몇 가지 제안 사항은 다음과 같습니다.

| 높은 데이터 볼륨의 소스 | 데이터 볼륨을 줄이는 방법 |
| -------------------------- | ------------------------- |
| 보안 이벤트            | [일반 또는 최소한의 보안 이벤트](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)를 선택합니다. <br> 보안 감사 정책을 변경하여 필요한 이벤트만을 수집합니다. 특히, 다음 항목에 대한 이벤트를 수집할 필요를 검토합니다. <br> - [감사 필터링 플랫폼](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [감사 레지스트리](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [감사 파일 시스템](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [감사 커널 개체](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [감사 핸들 조작](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - 이동식 저장소 감사 |
| 성능 카운터       | [성능 카운터 구성](data-sources-performance-counters.md)을 다음과 같이 변경합니다. <br> - 컬렉션의 빈도 감소 <br> - 성능 카운터의 수 감소 |
| 이벤트 로그                 | [이벤트 로그 구성](data-sources-windows-events.md)을 다음과 같이 변경합니다. <br> - 수집된 이벤트 로그의 수 감소 <br> - 필수 이벤트 수준만 수집 예를 들어 *정보* 수준 이벤트를 수집하지 않습니다. |
| syslog                     | [syslog 구성](data-sources-syslog.md)을 다음과 같이 변경합니다. <br> - 수집된 기능의 수 감소 <br> - 필수 이벤트 수준만 수집 예를 들어 *정보* 및 *디버그* 수준 이벤트를 수집하지 않습니다. |
| AzureDiagnostics           | 다음 작업을 수행하도록 리소스 로그 컬렉션을 변경합니다. <br> - Log Analytics로 보내는 리소스 송신 로그의 수 축소 <br> - 필요한 로그만 수집 |
| 솔루션을 사용하지 않는 컴퓨터의 솔루션 데이터 | [솔루션 대상](../insights/solution-targeting.md)을 사용하여 필수 그룹의 컴퓨터에서 데이터를 수집합니다. |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>노드당 가격 책정 계층에서 청구된 노드 받기

작업 영역이 노드당 레거시 가격 책정 계층에 있는 경우 노드로 청구되는 컴퓨터 목록을 얻으려면 **청구된 데이터 형식을** 보내는 노드를 찾습니다(일부 데이터 형식은 무료임). 이렇게 하려면 `_IsBillable` [속성을](log-standard-properties.md#_isbillable) 사용하고 정규화된 도메인 이름의 맨 왼쪽 필드를 사용합니다. 이렇게 하면 시간당 청구된 데이터가 있는 컴퓨터 수가 반환됩니다(노드가 계산되고 청구되는 세분성).

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

### <a name="getting-security-and-automation-node-counts"></a>보안 및 자동화 노드 수 얻기

“노드별 (OMS)” 가격 책정 계층에 속하는 경우 사용하는 노드 및 솔루션 수에 따라 비용이 청구됩니다. 요금이 청구되는 Insights 및 Analytics 노드의 수는 **사용량 및 예상 비용** 페이지의 표에 표시됩니다.  

고유한 보안 노드의 수를 보려면 다음 쿼리를 사용할 수 있습니다.

```kusto
union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count
```

고유한 Automation 노드의 수를 보려면 다음 쿼리를 사용합니다.

```kusto
 ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc
```

## <a name="evaluating-the-legacy-per-node-pricing-tier"></a>노드당 레거시 가격 책정 계층 평가

**레거시 노드당** 가격 책정 계층에 액세스할 수 있는 작업 영역이 해당 계층에서 더 나은지 또는 현재 **종량제** 또는 **용량 예약** 계층에서 더 나은지 여부를 결정하는 것은 고객이 평가하기 어려운 경우가 많습니다.  여기에는 노드당 가격 책정 계층에서 모니터링되는 노드당 고정 비용과 포함된 데이터 할당(일 500MB/노드/일) 및 종량제(GB당) 계층에서 수집된 데이터에 대한 지불 비용 간의 장단점을 이해하는 작업이 포함됩니다. 

이 평가를 용이하게 하기 위해 다음 쿼리를 사용하여 작업 영역의 사용 패턴을 기반으로 최적의 가격 책정 계층에 대한 권장 사항을 만들 수 있습니다.  이 쿼리는 지난 7일 동안 작업 영역으로 수집된 모니터링된 노드 및 데이터를 살펴보고 매일 어떤 가격 책정 계층이 최적이었을지 평가합니다. To use the query, you need to specify whether the workspace is using Azure Security Center by setting `workspaceHasSecurityCenter` to `true` or `false`, and then (optionally) updating the Per Node and Per GB prices that your organizaiton receives. 

```kusto
// Set these parameters before running query
let workspaceHasSecurityCenter = true;  // Specify if the workspace has Azure Security Center
let PerNodePrice = 15.; // Enter your price per node / month 
let PerGBPrice = 2.30; // Enter your price per GB 
// ---------------------------------------
let SecurityDataTypes=dynamic(["SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent", "Update", "UpdateSummary"]);
union withsource = tt * 
| where TimeGenerated >= startofday(now(-7d)) and TimeGenerated < startofday(now())
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodesPerHour = dcount(computerName) by bin(TimeGenerated, 1h)  
| summarize nodesPerDay = sum(nodesPerHour)/24.  by day=bin(TimeGenerated, 1d)  
| join (
    Heartbeat 
    | where TimeGenerated >= startofday(now(-7d)) and TimeGenerated < startofday(now())
    | where Computer != ""
    | summarize ASCnodesPerHour = dcount(Computer) by bin(TimeGenerated, 1h) 
    | extend ASCnodesPerHour = iff(workspaceHasSecurityCenter, ASCnodesPerHour, 0)
    | summarize ASCnodesPerDay = sum(ASCnodesPerHour)/24.  by day=bin(TimeGenerated, 1d)   
) on day
| join (
    Usage 
    | where TimeGenerated > ago(8d)
    | where StartTime >= startofday(now(-7d)) and EndTime < startofday(now())
    | where IsBillable == true
    | extend NonSecurityData = iff(DataType !in (SecurityDataTypes), Quantity, 0.)
    | extend SecurityData = iff(DataType in (SecurityDataTypes), Quantity, 0.)
    | summarize DataGB=sum(Quantity)/1000., NonSecurityDataGB=sum(NonSecurityData)/1000., SecurityDataGB=sum(SecurityData)/1000. by day=bin(StartTime, 1d)  
) on day
| extend AvgGbPerNode =  NonSecurityDataGB / nodesPerDay
| extend PerGBDailyCost = iff(workspaceHasSecurityCenter,
             (NonSecurityDataGB + max_of(SecurityDataGB - 0.5*ASCnodesPerDay, 0.)) * PerGBPrice,
             DataGB * PerGBPrice)
| extend OverageGB = iff(workspaceHasSecurityCenter, 
             max_of(DataGB - 0.5*nodesPerDay - 0.5*ASCnodesPerDay, 0.), 
             max_of(DataGB - 0.5*nodesPerDay, 0.))
| extend PerNodeDailyCost = nodesPerDay * PerNodePrice / 31. + OverageGB * PerGBPrice
| extend Recommendation = iff(PerNodeDailyCost < PerGBDailyCost, "Per Node tier", 
             iff(NonSecurityDataGB > 85., "Capacity Reservation tier", "Pay-as-you-go (Per GB) tier"))
| project day, nodesPerDay, ASCnodesPerDay, NonSecurityDataGB, SecurityDataGB, OverageGB, AvgGbPerNode, PerGBDailyCost, PerNodeDailyCost, Recommendation | sort by day asc
| project day, Recommendation // Comment this line to see details
| sort by day asc
```

이 쿼리는 사용량계산 방법에 대한 정확한 복제는 아니지만 대부분의 경우 가격 책정 계층 권장 사항을 제공하는 데 사용됩니다.  

## <a name="create-an-alert-when-data-collection-is-high"></a>데이터 수집이 높을 때 경고 만들기

이 섹션에서는 경고가 발생하는 경우를 설명합니다.
- 데이터 볼륨이 지정된 크기를 초과합니다.
- 데이터 볼륨이 지정된 크기를 초과할 것으로 예측됩니다.

검색 쿼리를 사용하는 Azure 경고 지원 [경고 로그](alerts-unified-log.md)입니다. 

최근 24시간 내에 수집된 데이터가 100GB를 초과하는 경우 다음 쿼리에 결과가 표시됩니다.

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table | summarize DataGB = sum((Quantity / 1000.)) by Type 
| where DataGB > 100
```

다음 쿼리는 간단한 수식을 사용하여 하루에 100GB를 초과하는 데이터가 전송되는 시기를 예측합니다. 

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table 
| summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type 
| where EstimatedGB > 100
```

다른 크기의 데이터 볼륨에 대해 경고하려면 쿼리의 100GB를 원하는 수로 변경합니다.

[새 로그 경고 만들기](alerts-metric.md)에 설명한 단계를 사용하여 데이터 컬렉션이 예상보다 높은 경우 알림을 받을 수 있습니다.

첫 번째 쿼리에 대한 경고를 만들 때 즉, 24시간 내에 데이터가 100GB를 초과하는 경우 다음을 설정합니다.  

- **경고 조건 정의**는 리소스 대상으로 Log Analytics 작업 영역을 지정합니다.
- **경고 조건**은 다음을 지정합니다.
   - **신호 이름** 선택 **사용자 지정 로그 검색**
   - **쿼리 검색**을 `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1000.)) by Type | where DataGB > 100`으로
   - **경고 논리는** *결과 수를* **기준으로** 하며 **조건은** **임계값** *0보다 큽합니다.* *0*
   - 사용량 데이터가 시간당 한 번만 업데이트되므로 **기간**은 *1440*분이고 **주파수 경고**는 *60*분마다입니다.
- **경고 세부 정보 정의**는 다음을 지정합니다.
   - **이름**을 *24시간 내에 100GB를 초과하는 데이터 볼륨*으로
   - **심각도**를 *경고*로

기존 또는 새 [작업 그룹](action-groups.md)을 지정하거나 만들어서 로그 경고가 조건과 일치하는 경우 알려줍니다.

두 번째 쿼리에 대한 경고를 만들 때 즉, 24시간 내에 100GB를 초과하는 데이터가 예측되는 경우 다음을 설정합니다.

- **경고 조건 정의**는 리소스 대상으로 Log Analytics 작업 영역을 지정합니다.
- **경고 조건**은 다음을 지정합니다.
   - **신호 이름** 선택 **사용자 지정 로그 검색**
   - **쿼리 검색**을 `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type | where EstimatedGB > 100`으로
   - **경고 논리는** *결과 수를* **기준으로** 하며 **조건은** **임계값** *0보다 큽합니다.* *0*
   - 사용량 데이터가 시간당 한 번만 업데이트되므로 **기간**은 *180*분이고 **주파수 경고**는 *60*분마다입니다.
- **경고 세부 정보 정의**는 다음을 지정합니다.
   - **이름**을 *24시간 내에 100GB를 초과한다고 예측되는 데이터 볼륨*으로
   - **심각도**를 *경고*로

기존 또는 새 [작업 그룹](action-groups.md)을 지정하거나 만들어서 로그 경고가 조건과 일치하는 경우 알려줍니다.

경고를 수신하는 경우 사용량이 예상보다 더 높은 원인을 해결하려면 다음 섹션의 단계를 사용합니다.

## <a name="data-transfer-charges-using-log-analytics"></a>로그 분석을 사용한 데이터 전송 요금

로그 애널리틱스로 데이터를 전송하면 데이터 대역폭 요금이 부과될 수 있습니다. Azure 대역폭 [가격 책정 페이지에](https://azure.microsoft.com/pricing/details/bandwidth/)설명된 대로 정상 요금으로 아웃바운드 데이터 전송으로 청구되는 두 지역에 있는 Azure 서비스 간의 데이터 전송입니다. 인바운드 데이터 전송은 무료입니다. 그러나,이 요금은 매우 작습니다 (소수의 %) 로그 애널리틱스 데이터 수집 비용과 비교할 수 있습니다. 따라서 Log Analytics의 비용을 제어하려면 수집된 데이터 볼륨에 집중해야 하며 여기에서 이를 이해하는 데 도움이 되는 지침이 [있습니다.](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)   


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Log Analytics에서 더 이상 데이터를 수집하지 않는 문제 해결

레거시 무료 가격 책정 계층을 사용 중이고 하루에 500MB 이상의 데이터를 보낸 경우 남은 날 동안 데이터 수집이 중지됩니다. 일일 한도에 도달하는 것은 Log Analytics가 데이터 수집을 중지하고 데이터가 사라진 것처럼 표시되는 일반적인 이유입니다.  Log Analytics는 데이터 수집을 시작하고 중지할 때 Operation 형식의 이벤트를 만듭니다. 일일 한도에 도달하고 데이터 누락이 있는지 확인하려면 검색에서 다음 쿼리를 실행합니다. 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

데이터 수집이 중지되는 경우 OperationStatus가 **Warning**입니다. 데이터 수집이 시작되는 경우 OperationStatus가 **Succeeded**입니다. 다음 표에서 데이터 수집을 중지하는 이유 및 데이터 수집을 다시 시작하는 권장되는 작업을 설명합니다.  

|수집 중지 이유| 솔루션| 
|-----------------------|---------|
|레거시 무료 가격 책정 계층의 일일 한도에 도달함 |수집이 다음 날에 자동으로 다시 시작될 때까지 대기 또는 유료 가격 책정 계층으로 변경합니다.|
|작업 영역의 일일 상한에 도달함|수집이 자동으로 다시 시작될 때까지 대기하거나, 최대 일일 데이터 볼륨 관리의 설명처럼 일일 데이터 볼륨 한도를 늘립니다. 일일 상한 다시 설정 시간이 **데이터 볼륨 관리** 페이지에 표시됩니다. |
|Azure 구독이 다음으로 인해 일시 중단된 상태:<br> 평가판 종료<br> Azure 암호 만료<br> 월별 지출 한도 도달(예: MSDN 또는 Visual Studio 구독에서)|유료 구독으로 전환<br> 한도 제거 또는 한도가 재설정될 때까지 대기|

데이터 수집이 중지될 때 알림을 받으면 *일일 데이터 한도* 경고 만들기에 설명된 단계를 사용하여 데이터 수집이 중지될 때 알림을 받을 수 있습니다. [작업 그룹을 만드는 데](action-groups.md) 설명된 단계를 사용하여 경고 규칙에 대한 전자 메일, 웹후크 또는 Runbook 작업을 구성합니다. 

## <a name="limits-summary"></a>제한 요약

몇 가지 추가 로그 분석 제한이 있으며, 그 중 일부는 로그 분석 가격 책정 계층에 따라 다릅니다. 여기에 설명되어 [있습니다.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces)


## <a name="next-steps"></a>다음 단계

- 검색 언어를 사용하는 방법에 대해 알아보려면 [Azure 모니터 로그에서 로그 검색을](../log-query/log-query-overview.md) 참조하세요. 사용량 현황 데이터에 대한 추가 분석을 수행하려면 검색 쿼리를 사용할 수 있습니다.
- [새 로그 경고 만들기](alerts-metric.md)에 설명한 단계를 사용하여 검색 기준이 충족되는 경우 알림을 받을 수 있습니다.
- [솔루션 대상](../insights/solution-targeting.md)을 사용하여 필수 그룹의 컴퓨터에서 데이터를 수집합니다.
- [Azure Security Center 필터링 정책](../../security-center/security-center-enable-data-collection.md)을 검토하여 효과적인 이벤트 컬렉션 정책을 구성합니다.
- [성능 카운터 구성을](data-sources-performance-counters.md)변경합니다.
- 이벤트 컬렉션 설정을 수정하려면 [이벤트 로그 구성을](data-sources-windows-events.md)검토합니다.
- syslog 컬렉션 설정을 수정하려면 [syslog 구성을](data-sources-syslog.md)검토합니다.