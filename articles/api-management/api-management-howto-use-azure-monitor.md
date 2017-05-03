---
title: "Azure Monitor를 사용하여 API Management 모니터링 | Microsoft Docs"
description: "Azure Monitor를 사용하여 Azure API Management 서비스를 모니터링하는 방법에 대해 알아봅니다."
services: api-management
documentationcenter: 
author: miaojiang
manager: erikre
editor: 
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 0f64947755c79739bb6f15325929bd074cfd7210
ms.lasthandoff: 03/31/2017

---
# <a name="monitor-api-management-with-azure-monitor"></a>Azure Monitor를 사용하여 API Management 모니터링
Azure Monitor는 모든 Azure 리소스 모니터링을 위한 단일 소스를 제공하는 Azure 서비스입니다. Azure Monitor를 통해 API Management와 같은 Azure 리소스의 메트릭과 로그에 대해 시각화, 쿼리, 라우팅, 보관 및 조치를 수행할 수 있습니다. 

다음 비디오는 Azure Monitor를 사용하여 API Management를 모니터링하는 방법을 보여 줍니다. Azure Monitor에 대한 자세한 내용은 [Azure Monitor 시작]을 참조하세요. 


> [!비디오 https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]
>
>
 
## <a name="metrics"></a>메트릭
API Management는 현재 5개의 메트릭을 내보내고 향후 더 추가할 계획입니다. 이러한 메트릭은 1분마다 내보내지므로 거의 실시간으로 API의 상태를 확인할 수 있습니다. 다음은 메트릭에 대한 요약입니다.
* 총 게이트웨이 요청: 기간 동안의 API 요청 수입니다. 
* 성공적인 게이트웨이 요청: 304, 307 및 301보다 작은(예: 200) 모든 항목을 포함하여 성공적인 HTTP 응답 코드를 수신하는 API 요청의 수입니다. 
* 실패한 게이트웨이 요청: 400 및 500보다 큰 모든 항목을 포함하여 잘못된 HTTP 응답 코드를 수신하는 API 요청의 수입니다.
* 허가되지 않은 게이트웨이 요청: 401, 403 및 429를 포함하는 HTTP 응답 코드를 수신하는 API 요청의 수입니다. 
* 기타 게이트웨이 요청: 앞의 범주 중 하나에 속하지 않는(예: 418) HTTP 응답 코드를 수신하는 API 요청의 수입니다.

API Management 서비스에서 메트릭에 액세스하거나 Azure Monitor에서 모든 Azure 리소스의 메트릭에 액세스할 수 있습니다. API Management 서비스에서 메트릭을 보려면:
1. Azure Portal을 엽니다.
2. API Management 서비스로 이동합니다.
3. **메트릭**을 클릭합니다.

![메트릭 블레이드][metrics-blade]

메트릭을 사용하는 방법에 대한 자세한 내용은 [메트릭 개요]를 참조하세요.

## <a name="activity-logs"></a>활동 로그
활동 로그는 API Management 서비스에서 수행된 작업에 대한 정보를 제공합니다. 이전에는 이러한 로그를 "감사 로그" 또는 "작업 로그"라고도 했습니다. 활동 로그를 통해 API Management 서비스에 대한 모든 쓰기 작업(PUT, POST, DELETE)에서 "무엇을, 누가, 언제"를 판단할 수 있습니다. 

> [!NOTE]
> 활동 로그는 읽기(GET) 작업 또는 클래식 게시자 포털에서 수행되었거나 원본 Management API를 사용하는 작업을 포함하지 않습니다.

API Management 서비스에서 활동 로그에 액세스하거나 Azure Monitor에서 모든 Azure 리소스의 로그에 액세스할 수 있습니다. API Management 서비스에서 활동 로그를 보려면:
1. Azure Portal을 엽니다.
2. API Management 서비스로 이동합니다.
3. **활동 로그**를 클릭합니다.

![활동 로그 블레이드][activity-logs-blade]

메트릭을 사용하는 방법에 대한 자세한 내용은 [활동 로그 개요]를 참조하세요.

## <a name="alerts"></a>경고
메트릭 및 활동 로그를 기반으로 경고를 수신하도록 구성할 수 있습니다. Azure Monitor를 사용하여 트리거되면 다음을 수행하도록 경고를 구성할 수 있습니다.

* 전자 메일 알림 보내기
* 웹후크 호출
* Azure 논리 앱 호출

API Management 서비스 또는 Azure Monitor에서 경고 규칙을 구성할 수 있습니다. API Management에서 구성하려면: 
1. Azure Portal을 엽니다.
2. API Management 서비스로 이동합니다.
3. **경고 규칙**을 클릭합니다.

![경고 규칙 블레이드][alert-rules-blade]

경고 사용에 대한 자세한 내용은 [경고 개요]를 참조하세요.

## <a name="diagnostic-logs"></a>진단 로그
진단 로그는 감사 뿐만 아니라 문제 해결에 중요한 작업 및 오류에 대한 풍부한 정보를 제공합니다. 진단 로그는 활동 로그와 다릅니다. 활동 로그는 Azure 리소스에서 수행된 작업에 대한 정보를 제공합니다. 진단 로그는 리소스 자체에서 수행하는 작업에 대한 정보를 제공합니다.

API Management는 현재 다음 구조를 갖는 각 항목으로 개별 API 요청에 대한 진단 로그(시간 단위로 일괄 처리됨)를 제공합니다.

```
{
    "Tenant": "",
      "DeploymentName": "",
      "time": "",
      "resourceId": "",
      "category": "GatewayLogs",
      "operationName": "Microsoft.ApiManagement/GatewayLogs",
      "durationMs": ,
      "Level": ,
      "properties": "{
          "ApiId": "",
          "OperationId": "",
          "ProductId": "",
          "SubscriptionId": "",
          "Method": "",
          "Url": "",
          "RequestSize": ,
          "ServiceTime": "",
          "BackendMethod": "",
          "BackendUrl": "",
          "BackendResponseCode": ,
          "ResponseCode": ,
          "ResponseSize": ,
          "Cache": "",
          "UserId"
      }"
 }
```

API Management 서비스에서 진단 로그에 액세스하거나 Azure Monitor에서 모든 Azure 리소스의 로그에 액세스할 수 있습니다. API Management 서비스에서 진단 로그를 보려면:
1. Azure Portal을 엽니다.
2. API Management 서비스로 이동합니다.
3. **진단 로그**를 클릭합니다.

![진단 로그 블레이드][diagnostic-logs-blade]

메트릭을 사용하는 방법에 대한 자세한 내용은 [진단 로그 개요]를 참조하세요.

## <a name="next-step"></a>다음 단계

* [Azure Monitor 시작]
* [메트릭 개요]
* [활동 로그 개요]
* [진단 로그 개요]
* [경고 개요]

[Azure Monitor 시작]: ../monitoring-and-diagnostics/monitoring-get-started.md
[메트릭 개요]: ../monitoring-and-diagnostics/monitoring-overview-metrics.md
[활동 로그 개요]: ../monitoring-and-diagnostics/monitoring-overview-activity-logs.md
[진단 로그 개요]: ../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
[경고 개요]: ../monitoring-and-diagnostics/insights-alerts-portal.md



[metrics-blade]: ./media/api-management-azure-monitor/api-management-metrics-blade.png
[activity-logs-blade]: ./media/api-management-azure-monitor/api-management-activity-logs-blade.png
[alert-rules-blade]: ./media/api-management-azure-monitor/api-management-alert-rules-blade.png
[diagnostic-logs-blade]: ./media/api-management-azure-monitor/api-management-diagnostic-logs-blade.png

