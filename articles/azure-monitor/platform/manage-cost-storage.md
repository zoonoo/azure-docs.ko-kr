---
title: Azure Monitor 로그의 사용량 및 비용 관리 | Microsoft Docs
description: Azure Monitor에서 Log Analytics 작업 영역에 대 한 가격 책정 계획을 변경 하 고 데이터 볼륨 및 보존 정책을 관리 하는 방법을 알아봅니다.
services: azure-monitor
documentationcenter: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: magoedte
ms.subservice: ''
ms.openlocfilehash: f60b0b9294fa3f11889613a7d63f21e87fbea201
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74894116"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Azure Monitor 로그를 사용 하 여 사용량 및 비용 관리

> [!NOTE]
> 이 문서에서는 Azure Monitor 로그의 비용을 이해 하 고 제어 하는 방법을 설명 합니다. 관련 문서, [사용량 및 예상 비용 모니터링](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) 은 다양 한 가격 책정 모델에 대 한 여러 Azure 모니터링 기능에서 사용량 및 예상 비용을 보는 방법을 설명 합니다.

> [!NOTE]
> 이 문서에 표시 된 모든 가격과 비용은 예를 들어 목적 으로만 사용 됩니다. 

Azure Monitor 로그는 기업의 모든 원본에서 하루에 대량의 데이터를 수집, 인덱싱 및 저장 하거나 Azure에 배포 하는 기능을 확장 하 고 지원 하도록 설계 되었습니다.  조직에 대한 주 드라이버인 반면 비용 효율성은 궁극적으로 기본 드라이버입니다. 이를 위해 Log Analytics 작업 영역의 비용은 수집 된 데이터의 양에 따라 달라 지는 것이 아니라, 선택한 계획에 따라 달라 지 며, 연결 된 원본에서 생성 된 데이터를 저장 하기 위해 선택한 계획에 따라 달라 지는 것을 이해 하는 것이 중요 합니다.  

이 문서에서는 수집 데이터 볼륨 및 저장소 증가를 사전에 모니터링 하 고 이러한 관련 비용을 제어 하는 제한을 정의 하는 방법을 검토 합니다. 

## <a name="pricing-model"></a>가격 책정 모델

Log Analytics에 대 한 기본 가격은 데이터 볼륨 수집을 기반으로 하는 **종 량** 제 모델 이며 필요에 따라 더 긴 데이터 보존을 위해 필요 합니다. 데이터 볼륨은 저장 되는 데이터의 크기로 측정 됩니다. 각 Log Analytics 작업 영역은 별도의 서비스로 청구 되며 Azure 구독에 대 한 청구에 기여 합니다. 데이터 수집의 양은 다음 요인에 따라 상당히 길어질 수 있습니다. 

  - 사용 하도록 설정 된 관리 솔루션의 수 및 구성 (예: 
  - 모니터링 되는 Vm 수
  - 모니터링 되는 각 VM에서 수집 된 데이터 유형 
  
종 량 제 모델 외에도 Log Analytics에는 종 량 제 가격과 비교 하 여 25%까지 절감할 수 있는 **용량 예약** 계층이 있습니다. 용량 예약 가격 책정을 통해 100 g b/일에 시작 하는 예약을 구매할 수 있습니다. 예약 수준 위의 모든 사용량은 종 량 제 요금으로 청구 됩니다. 용량 예약 계층에는 31 일 약정 기간이 있습니다. 약정 기간 동안에는 더 높은 수준의 용량 예약 계층 (31 일 약정 기간을 다시 시작 함)으로 변경할 수 있지만,이 기간 후에는 약정 기간이 끝날 때까지 종 량 제 또는 더 낮은 용량의 예약 계층으로 다시 이동할 수 없습니다. 날짜. 
Log Analytics 종 량 제 및 용량 예약 가격 책정에 [대해 자세히 알아보세요](https://azure.microsoft.com/pricing/details/monitor/) . 

또한 [Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/) 및 [Azure 센티널](https://azure.microsoft.com/pricing/details/azure-sentinel/)과 같은 일부 솔루션에는 고유한 가격 책정 모델이 있습니다. 

## <a name="estimating-the-costs-to-manage-your-environment"></a>환경을 관리 하는 비용 예측 

Azure Monitor 로그를 아직 사용 하 고 있지 않은 경우 [Azure Monitor 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=monitor) 를 사용 하 여 Log Analytics를 사용 하는 비용을 예측할 수 있습니다. 검색 상자에 "Azure Monitor"를 입력 하 고 결과 Azure Monitor 타일을 클릭 하 여 시작 합니다. 페이지 아래쪽으로 스크롤하여 Azure Monitor 하 고 유형 드롭다운에서 Log Analytics를 선택 합니다.  여기에서 각 VM에서 수집 해야 하는 Vm 수와 GB 데이터를 입력할 수 있습니다. 일반적으로 1-3gb의 데이터 월은 일반적인 Azure VM에서 수집 됩니다. 이미 Azure Monitor 로그를 평가 하 고 있는 경우 자신의 환경에서 데이터 통계를 사용할 수 있습니다. 모니터링 되는 [vm 수](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) 및 [작업 영역에서 수집 된 데이터의](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)양을 확인 하는 방법은 아래를 참조 하세요. 

## <a name="understand-your-usage-and-estimate-costs"></a>사용량 및 예상 비용 이해

지금 Azure Monitor 로그를 사용 하는 경우 최근 사용 패턴에 따른 비용을 쉽게 파악할 수 있습니다. 이렇게 하려면 **Log Analytics 사용량 및 예상 비용** 을 사용 하 여 데이터 사용량을 검토 하 고 분석 합니다. 이는 각 솔루션에 의해 수집 되는 데이터의 양, 유지 되 고 있는 데이터의 양과 포함 된 용량을 초과 하는 데이터 수집 및 추가 보존을 기반으로 하는 예상 비용을 보여 줍니다.

![사용량 및 예상 비용](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

데이터를 더 자세히 탐색하려면 **사용량 및 예상 비용** 페이지의 차트 중 하나의 오른쪽 상단 모서리에 있는 아이콘을 클릭합니다. 이제 이 쿼리에 대해 작업하여 사용량을 자세히 살펴볼 수 있습니다.  

![로그 보기](media/manage-cost-storage/logs.png)

**사용량 및 예상 비용** 페이지에서 해당 월의 데이터 볼륨을 검토할 수 있습니다. 여기에는 Log Analytics 작업 영역에 수신되고 보존된 모든 데이터가 포함됩니다.  페이지 맨 위에서 **사용 세부 정보** 를 클릭 하 여 원본, 컴퓨터 및 제품의 데이터 볼륨 추세에 대 한 정보가 포함 된 사용 대시보드를 봅니다. 보존 기간을 수정하거나 일일 한도를 설정하고 확인하려면 **데이터 볼륨 관리**를 클릭합니다.
 
Log Analytics 요금은 Azure 청구서에 추가됩니다. Azure 청구서의 자세한 내용은 Azure Portal의 청구 섹션 또는 [Azure 청구 포털](https://account.windowsazure.com/Subscriptions)에서 참고할 수 있습니다.  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Azure 청구서에서 Log Analytics 사용량 보기 

Azure는 [Azure Cost Management + 청구](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) 허브에서 상당한 유용한 기능을 제공 합니다. 예를 들어 "비용 분석" 기능을 사용 하 여 Azure 리소스에 대 한 소비를 확인할 수 있습니다. Operationalinsights/작업 영역에 Log Analytics 대 한 리소스 종류에 따라 필터를 추가 하면 지출을 추적할 수 있습니다.

사용량을 [Azure Portal에서 다운로드](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal)하 여 사용량을 더 자세히 파악할 수 있습니다. 다운로드 한 스프레드시트에서 하루에 Azure 리소스 (예: Log Analytics 작업 영역) 별 사용량을 볼 수 있습니다. 이 Excel 스프레드시트에서는 "측정기 범주" 열을 기준으로 필터링 하 여 "정보 및 분석" (일부 레거시 가격 책정 계층에서 사용) 및 "Log Analytics"를 표시 하 고 "인스턴스에 필터를 추가 하 여 Log Analytics 작업 영역의 사용을 찾을 수 있습니다. ID "열에" 작업 영역 포함 "열이 있습니다. 사용량은 "소비 된 수량" 열에 표시 되 고 각 항목에 대 한 단위는 "측정 단위" 열에 표시 됩니다.  [Microsoft Azure 청구서를 이해](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)하는 데 도움이 되는 자세한 정보를 제공 합니다. 

## <a name="changing-pricing-tier"></a>가격 책정 계층 변경

작업 영역의 Log Analytics 가격 책정 계층을 변경 하려면 

1. Azure Portal 작업 영역에서 **사용 및 예상 비용** 을 열고,이 작업 영역에서 사용할 수 있는 각 가격 책정 계층의 목록이 표시 됩니다.

2. 각 가격 책정 계층에 대 한 예상 비용을 검토 합니다. 이 예상치는 최근 31 일간의 사용량을 기준으로 하므로이 비용 예상치는 일반적인 사용량을 대표 하는 최근 31 일에 의존 합니다. 아래 예제에서는 지난 31 일 동안의 데이터 패턴에 따라이 작업 영역은 100 g b/일 용량 예약 계층 (#2)과 비교 하 여 종 량 제 계층 (#1)에서 더 적은 비용을 볼 수 있습니다.  

    ![가격 책정 계층](media/manage-cost-storage/pricing-tier-estimated-costs.png)

3. 최근 31 일간의 사용량을 기준으로 예상 비용을 검토 한 후 가격 책정 계층을 변경 하려면 **선택**을 클릭 합니다.  

`sku` 매개 변수 (Azure Resource Manager 템플릿의`pricingTier`)를 사용 하 여 [Azure Resource Manager를 통해 가격 책정 계층을 설정할](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) 수도 있습니다. 

## <a name="legacy-pricing-tiers"></a>레거시 가격 책정 계층

2018 년 4 월 2 일 이전에 Log Analytics 작업 영역 또는 Application Insights 리소스가 기업계약 있거나 2019 년 2 월 1 일 이전에 시작 된 구독에는 계속 해 서 레거시 가격 책정 계층을 사용할 수 있습니다. **무료**, **독립 실행형 (GB 당)** 및 노드당 **(OMS)**  무료 가격 책정 계층의 작업 영역에는 500 MB (Azure Security Center에서 수집 하는 보안 데이터 형식은 제외)로 제한 된 일일 데이터 수집 및 데이터 보존 기간이 7 일로 제한 됩니다. 무료 가격 책정 계층은 평가 목적 으로만 사용 됩니다. 독립 실행형 또는 노드당 가격 책정 계층의 작업 영역에는 최대 2 년의 사용자 구성 가능 보존이 있습니다. 

4 월 2016 이전에 만들어진 작업 영역은 각각 30 및 365 일의 고정 데이터 보존을 포함 하는 원래의 **표준** 및 **프리미엄** 가격 책정 계층에 액세스할 수도 있습니다. **표준** 또는 **프리미엄** 가격 책정 계층에서는 새 작업 영역을 만들 수 없으며, 작업 영역을 이러한 계층 외부로 이동 하는 경우에는 다시 이동할 수 없습니다. 

가격 책정 계층 제한 사항에 대 한 자세한 내용은 [여기](https://docs.microsoft.com/azure/azure-subscription-service-limits#log-analytics-workspaces)에서 제공 됩니다.

> [!NOTE]
> System Center용 OMS E1 Suite, OMS E2 Suite 또는 OMS 추가 기능을 구매할 때 제공되는 자격을 사용하려면 Log Analytics의 *노드별* 가격 책정 계층을 선택합니다.

## <a name="change-the-data-retention-period"></a>데이터 보존 기간 변경

다음 단계에서는 로그 데이터가 작업 영역에 보존되는 기간을 구성하는 방법을 설명합니다.

### <a name="default-retention"></a>기본 보존

작업 영역에 대 한 기본 보존을 설정 하려면 
 
1. Azure Portal의 작업 영역에서 왼쪽 창에 있는 **사용량 및 예상 비용** 을 선택 합니다.
2. **사용량 및 예상 비용** 창의 페이지의 상단에서 **데이터 볼륨 관리**를 클릭합니다.
3. 창에서 슬라이더를 이동하여 일 수를 늘리거나 줄인 다음, **확인**을 클릭합니다.  *무료* 계층에서 작업 중인 경우 데이터 보존 기간을 수정할 수 없으며 이 설정을 제어하기 위해 유료 계층으로 업그레이드해야 합니다.

    ![작업 영역 데이터 보존 설정 변경](media/manage-cost-storage/manage-cost-change-retention-01.png)
    
`retentionInDays` 매개 변수를 사용 하 여 [Azure Resource Manager를 통해](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) 보존을 설정할 수도 있습니다. 또한 데이터 보존 기간을 30 일로 설정 하면 `immediatePurgeDataOn30Days` 매개 변수를 사용 하 여 이전 데이터를 즉시 제거할 수 있습니다 .이는 규정 준수 관련 시나리오에 유용할 수 있습니다. 이 기능은 Azure Resource Manager 통해서만 노출 됩니다. 

`Usage` 및 `AzureActivity`의 두 데이터 형식은 기본적으로 90 일 동안 유지 되며, 90 일 보존에 대해 요금이 청구 되지 않습니다. 이러한 데이터 형식은 데이터 수집 요금에도 무료로 제공 됩니다. 

### <a name="retention-by-data-type"></a>데이터 유형별 보존

또한 개별 데이터 형식에 대해 서로 다른 보존 설정을 지정할 수 있습니다. 각 데이터 형식은 작업 영역의 하위 리소스입니다. 예를 들어 다음과 같이 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 에서 securityevent 테이블의 주소를 지정할 수 있습니다.

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

데이터 형식 (테이블)은 대/소문자를 구분 합니다.  특정 데이터 형식의 현재 데이터 형식 보존 설정 (이 예제 SecurityEvent)을 가져오려면 다음을 사용 합니다.

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

작업 영역에 있는 모든 데이터 형식에 대 한 현재 데이터 형식 보존 설정을 가져오려면 특정 데이터 형식을 생략 합니다. 예를 들면 다음과 같습니다.

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

특정 데이터 형식 (이 예제에서는 SecurityEvent)의 보존을 730 일로 설정 하려면 다음을 수행 합니다.

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

사용자 지정 보존을 사용 하 여 `Usage` 및 `AzureActivity` 데이터 형식을 설정할 수 없습니다. 기본 작업 영역 보존 또는 90 일의 최대값을 사용 합니다. 

데이터 형식으로 보존을 설정 하기 위해 Azure Resource Manager에 직접 연결 하는 좋은 도구는 OSS 도구 [ARMclient](https://github.com/projectkudu/ARMClient)입니다.  [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) 및 [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/)에서 ARMclient에 대해 자세히 알아보세요.  ARMClient를 사용 하 여 securityevent 데이터를 730 일 보존으로 설정 하는 예제는 다음과 같습니다.

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!NOTE]
> 개별 데이터 형식에 대 한 보존 설정을 사용 하 여 데이터 보존에 대 한 비용을 줄일 수 있습니다.  10 월 2019 (이 기능이 릴리스된 경우)부터 수집 된 데이터의 경우 일부 데이터 형식에 대 한 보존을 줄이면 시간이 지남에 따라 보존 비용이 줄어들 수 있습니다.  이전에 수집 된 데이터의 경우 개별 유형에 대해 보존 기간을 낮게 설정 해도 보존 비용은 영향을 받지 않습니다.  

## <a name="manage-your-maximum-daily-data-volume"></a>최대 일일 데이터 볼륨 관리

작업 영역에 대한 일일 한도를 구성하고 일일 수집량을 제한할 수 있지만 목표치가 일일 한도에 도달하지 않도록 주의하십시오.  그렇지 않으면 남은 기간 동안의 데이터가 손실됩니다. 이는 해당 기능이 작업 영역에서 사용할 수 있는 최신 데이터에 의존할 수도 있는 다른 Azure 서비스 및 솔루션에 영향을 줄 수 있습니다.  결과적으로 리소스의 상태 조건이 IT 서비스를 지원할 때 경고를 관찰하고 수신하는 기능이 영향을 받습니다.  일일 한도는 관리 되는 리소스에서 예기치 않은 데이터 볼륨 증가를 관리 하 고 한도 내로 유지 하거나 작업 영역에 대 한 계획 되지 않은 요금을 제한 하려는 경우에 사용 됩니다.  

일일 한도에 도달하면 하루의 나머지 시간 동안 청구 가능한 데이터 형식의 수집을 중지합니다. 선택된 Log Analytics 작업 영역에 대한 페이지의 상단에 경고 배너가 표시되고 작업 이벤트가 **LogManagement** 범주 아래의 *작업* 테이블로 전송됩니다. *일일 한도 아래 정의된 재설정 시간이* 로 설정된 후 데이터 수집이 다시 시작합니다. 일일 데이터 한도에 도달했을 때 알려주도록 구성된 이 작업 이벤트를 기반으로 경고 규칙을 정의하는 것이 좋습니다. 

> [!NOTE]
> 일일 한도는 2017 년 6 월 19 일 이전에 Azure Security Center 설치 된 작업 영역을 제외 하 고는 Azure Security Center에서 데이터 수집을 중지 하지 않습니다. 

### <a name="identify-what-daily-data-limit-to-define"></a>정의할 일일 데이터 한도 식별

데이터 수집 추세 및 정의할 일일 볼륨 한도를 이해하려면 [Log Analytics 사용량 및 예상 비용](usage-estimated-costs.md)을 검토합니다. 한도에 도달한 후에는 리소스를 모니터링할 수 없으므로 신중하게 고려해야 합니다. 

### <a name="set-the-daily-cap"></a>일일 상한 설정

다음 단계에서는 Log Analytics 작업 영역에서 수집 하는 데이터의 양을 관리 하는 한도를 구성 하는 방법을 설명 합니다.  

1. 작업 영역의 왼쪽 창에서 **사용량 및 예상 비용**을 선택합니다.
2. 선택한 작업 영역에 대한 **사용량 및 예상 비용** 창의 페이지의 상단에서 **데이터 볼륨 관리**를 클릭합니다. 
3. 일일 한도는 기본으로 **OFF**이며 이를 사용하도록 설정하려면 **ON**을 클릭한 다음, 데이터 볼륨 한도를 GB/일로 설정합니다.

    ![데이터 제한 구성 Log Analytics](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>일일 상한에 도달 했을 때 경고

데이터 제한 임계값에 도달하는 경우 Azure Portal에 시각적 큐를 표시하는 반면, 이 동작은 즉각적인 주의가 필요한 운영 문제를 관리하는 방법에 맞출 필요는 없습니다.  경고 알림을 수신하려면 Azure Monitor에서 새 경고 규칙을 만들 수 있습니다.  자세히 알아보려면 [경고를 만들고, 보고, 관리 하는 방법](alerts-metric.md)을 참조 하세요.

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
- Log Analytics 작업 영역에 데이터를 전송 하는 데 필요한 노드가 더 많이 있습니다.
- Log Analytics 작업 영역으로 전송 되는 것 보다 많은 데이터가 필요 합니다.

## <a name="understanding-nodes-sending-data"></a>데이터를 보내는 노드 이해

지난 달에 매일 하트 비트를 보고 하는 컴퓨터 수를 이해 하려면 다음을 사용 합니다.

```kusto
Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```

작업 영역이 레거시 노드당 가격 책정 계층에 있는 경우 노드로 청구 되는 컴퓨터 목록을 가져오려면 **청구 된 데이터 형식을** 보내는 노드 (일부 데이터 형식은 무료)를 찾습니다. 이렇게 하려면 `_IsBillable` [속성](log-standard-properties.md#_isbillable) 을 사용 하 고 정규화 된 도메인 이름의 맨 왼쪽 필드를 사용 합니다. 그러면 청구 된 데이터가 있는 컴퓨터의 목록이 반환 됩니다.

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

표시 되는 청구 가능한 노드 수는 다음과 같이 예상할 수 있습니다. 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName)
```

> [!NOTE]
> 여러 데이터 형식을 검색할 경우 비용이 많이 들기 때문에 이러한 `union withsource = tt *` 쿼리는 자주 사용하지 않도록 합니다. 이 쿼리는 컴퓨터별 정보를 사용 데이터 형식으로 쿼리 하는 이전 방법을 대체 합니다.  

실제로 청구 되는 항목에 대 한 보다 정확한 계산은 청구 되는 데이터 형식을 전송 하는 시간당 컴퓨터 수를 가져오는 것입니다. (노드당 레거시 가격 책정 계층의 작업 영역에서는 Log Analytics 시간 단위로 요금이 청구 되어야 하는 노드 수를 계산 합니다.) 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="understanding-ingested-data-volume"></a>수집 데이터 볼륨 이해

**사용량 및 예상 비용** 페이지에서 *솔루션당 데이터 수집* 차트는 전송된 총 데이터 양과 각 솔루션이 전송하는 데이터 양을 보여 줍니다. 이를 통해 전체 데이터 사용량(또는 특정 솔루션에 의한 사용량)이 증가하는지, 고정적인지, 감소하는지 여부의 추세를 판단할 수 있습니다. 이를 생성하는 데 사용되는 쿼리는 다음과 같습니다.

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1000. by bin(TimeGenerated, 1d), Solution| render barchart
```

여기서 “where IsBillable = true” 절은 수집 비용이 없는 특정 솔루션에서 데이터 형식을 필터링합니다. 

데이터 추세를 보다 자세히 조사하여 특정 데이터 형식을 발견할 수 있습니다(예: IIS 로그로 인한 데이터를 연구하려는 경우).

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1000. by bin(TimeGenerated, 1d), Solution| render barchart
```

### <a name="data-volume-by-computer"></a>컴퓨터별 데이터 볼륨

컴퓨터당 수집 청구 가능 이벤트의 **크기** 를 확인 하려면 바이트 단위로 크기를 제공 하는 `_BilledSize` [속성](log-standard-properties.md#_billedsize)을 사용 합니다.

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize Bytes=sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

`_IsBillable` [속성](log-standard-properties.md#_isbillable) 은 수집 데이터가 요금을 부과 하는지 여부를 지정 합니다.

컴퓨터별 수집 **청구** 가능 이벤트 수를 확인 하려면 다음을 사용 합니다. 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount=count() by computerName  | sort by eventCount nulls last
```

특정 컴퓨터로 데이터를 전송하는 청구 가능 데이터 형식 수를 확인하려면 다음을 사용합니다.

```kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Azure 리소스, 리소스 그룹 또는 구독에의 한 데이터 볼륨

Azure에서 호스트 되는 노드의 데이터의 __경우 수집에__대 한 청구 가능 이벤트의 **크기** 를 가져올 수 있습니다 .이 속성은 리소스의 전체 경로를 제공 하는 _ResourceId [속성](log-standard-properties.md#_resourceid)을 사용 합니다.

```kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

Azure에서 호스트 되는 노드의 데이터에 대해 수집 속성을 다음과 같이 구문 `_ResourceId` 분석 하는 __azure 구독 당__청구 가능한 이벤트의 **크기** 를 가져올 수 있습니다.

```kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

`resourceGroup` `subscriptionId` 변경 하면 Azure 리소스 그룹별 청구 가능한 수집 데이터 볼륨이 표시 됩니다. 


> [!NOTE]
> 사용량 데이터 형식의 일부 필드가 여전히 스키마에 있지만 더 이상 사용되지 않으며 해당 값은 더 이상 채워지지 않습니다. 이는 **컴퓨터**일 뿐 아니라 수집과 관련된 필드(**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** 및 **AverageProcessingTimeMs**)이기도 합니다.

### <a name="querying-for-common-data-types"></a>공통 데이터 형식에 대 한 쿼리

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

### <a name="getting-security-and-automation-node-counts"></a>보안 및 자동화 노드 수 가져오기

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
   - **신호 이름**은 **로그 검색 사용자 지정**을 선택합니다.
   - **쿼리 검색**을 `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1000.)) by Type | where DataGB > 100`으로
   - **경고 논리**는 *결과 수*에 **기반**하고 **조건**은 *0*의 **임계값**을 *초과*합니다.
   - 사용량 데이터가 시간당 한 번만 업데이트되므로 **기간**은 *1440*분이고 **주파수 경고**는 *60*분마다입니다.
- **경고 세부 정보 정의**는 다음을 지정합니다.
   - **이름**을 *24시간 내에 100GB를 초과하는 데이터 볼륨*으로
   - **심각도**를 *경고*로

기존 또는 새 [작업 그룹](action-groups.md)을 지정하거나 만들어서 로그 경고가 조건과 일치하는 경우 알려줍니다.

두 번째 쿼리에 대한 경고를 만들 때 즉, 24시간 내에 100GB를 초과하는 데이터가 예측되는 경우 다음을 설정합니다.

- **경고 조건 정의**는 리소스 대상으로 Log Analytics 작업 영역을 지정합니다.
- **경고 조건**은 다음을 지정합니다.
   - **신호 이름**은 **로그 검색 사용자 지정**을 선택합니다.
   - **쿼리 검색**을 `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type | where EstimatedGB > 100`으로
   - **경고 논리**는 *결과 수*에 **기반**하고 **조건**은 *0*의 **임계값**을 *초과*합니다.
   - 사용량 데이터가 시간당 한 번만 업데이트되므로 **기간**은 *180*분이고 **주파수 경고**는 *60*분마다입니다.
- **경고 세부 정보 정의**는 다음을 지정합니다.
   - **이름**을 *24시간 내에 100GB를 초과한다고 예측되는 데이터 볼륨*으로
   - **심각도**를 *경고*로

기존 또는 새 [작업 그룹](action-groups.md)을 지정하거나 만들어서 로그 경고가 조건과 일치하는 경우 알려줍니다.

경고를 수신하는 경우 사용량이 예상보다 더 높은 원인을 해결하려면 다음 섹션의 단계를 사용합니다.

## <a name="data-transfer-charges-using-log-analytics"></a>Log Analytics를 사용 하 여 데이터 전송 요금

Log Analytics 데이터를 전송 하면 데이터 대역폭 요금이 발생할 수 있습니다. [Azure 대역폭 가격 책정 페이지](https://azure.microsoft.com/pricing/details/bandwidth/)에 설명 된 대로, 두 지역에 있는 Azure 서비스 간의 데이터 전송은 일반 요금으로 아웃 바운드 데이터 전송으로 청구 됩니다. 인바운드 데이터 전송은 무료입니다. 그러나이 요금은 매우 작음 (몇%) Log Analytics 데이터 수집에 대 한 비용과 비교 됩니다. 따라서 Log Analytics에 대 한 비용을 제어 하 여 수집 데이터 볼륨에 집중 해야 하며, [여기서](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)는이에 대해 이해 하는 데 도움이 되는 지침을 제공 합니다.   


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Log Analytics에서 더 이상 데이터를 수집하지 않는 문제 해결

레거시 무료 가격 책정 계층을 사용 중이고 하루에 500MB 이상의 데이터를 보낸 경우 남은 날 동안 데이터 수집이 중지됩니다. 일일 한도에 도달하는 것은 Log Analytics가 데이터 수집을 중지하고 데이터가 사라진 것처럼 표시되는 일반적인 이유입니다.  Log Analytics는 데이터 수집을 시작하고 중지할 때 Operation 형식의 이벤트를 만듭니다. 일일 한도에 도달하고 데이터 누락이 있는지 확인하려면 검색에서 다음 쿼리를 실행합니다. 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

데이터 수집이 중지 되 면 OperationStatus가 **Warning**입니다. 데이터 수집이 시작 되 면 OperationStatus가 **성공**입니다. 다음 표에서 데이터 수집을 중지하는 이유 및 데이터 수집을 다시 시작하는 권장되는 작업을 설명합니다.  

|수집 중지 이유| 솔루션| 
|-----------------------|---------|
|레거시 무료 가격 책정 계층의 일일 한도에 도달함 |수집이 다음 날에 자동으로 다시 시작될 때까지 대기 또는 유료 가격 책정 계층으로 변경합니다.|
|작업 영역의 일일 상한에 도달함|수집이 자동으로 다시 시작될 때까지 대기하거나, 최대 일일 데이터 볼륨 관리의 설명처럼 일일 데이터 볼륨 한도를 늘립니다. 일일 상한 다시 설정 시간이 **데이터 볼륨 관리** 페이지에 표시됩니다. |
|Azure 구독이 다음으로 인해 일시 중단된 상태:<br> 평가판 종료<br> Azure 암호 만료<br> 월별 지출 한도 도달(예: MSDN 또는 Visual Studio 구독에서)|유료 구독으로 전환<br> 한도 제거 또는 한도가 재설정될 때까지 대기|

데이터 수집이 중지 될 때 알림을 받으려면 *매일 데이터 캡 만들기* 경고에 설명 된 단계를 사용 하 여 데이터 수집이 중지 될 때 알림을 받을 수 있습니다. [작업 그룹 만들기](action-groups.md) 에 설명 된 단계를 사용 하 여 경고 규칙에 대 한 전자 메일, webhook 또는 runbook 작업을 구성 합니다. 

## <a name="limits-summary"></a>제한 요약

몇 가지 추가 Log Analytics 제한이 있으며, 그 중 일부는 Log Analytics 가격 책정 계층에 따라 달라 집니다. 여기에는 [여기](https://docs.microsoft.com/azure/azure-subscription-service-limits#log-analytics-workspaces)에 설명 되어 있습니다.


## <a name="next-steps"></a>다음 단계

- 검색 언어를 사용 하는 방법을 알아보려면 [Azure Monitor 로그의 로그 검색](../log-query/log-query-overview.md) 을 참조 하세요. 사용량 현황 데이터에 대한 추가 분석을 수행하려면 검색 쿼리를 사용할 수 있습니다.
- [새 로그 경고 만들기](alerts-metric.md)에 설명한 단계를 사용하여 검색 기준이 충족되는 경우 알림을 받을 수 있습니다.
- [솔루션 대상](../insights/solution-targeting.md)을 사용하여 필수 그룹의 컴퓨터에서 데이터를 수집합니다.
- [Azure Security Center 필터링 정책](../../security-center/security-center-enable-data-collection.md)을 검토하여 효과적인 이벤트 컬렉션 정책을 구성합니다.
- [성능 카운터 구성](data-sources-performance-counters.md)을 변경합니다.
- 이벤트 컬렉션 설정을 수정하려면 [이벤트 로그 구성](data-sources-windows-events.md)을 검토합니다.
- syslog 컬렉션 설정을 수정하려면 [syslog 구성](data-sources-syslog.md)을 검토합니다.
