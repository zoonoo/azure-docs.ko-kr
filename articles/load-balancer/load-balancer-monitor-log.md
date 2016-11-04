---
title: 부하 분산 장치에 대한 작업, 이벤트 및 카운터 모니터링 | Microsoft Docs
description: Azure 부하 분산 장치에 대한 경고 이벤트 및 상태 프로브 상태 로깅을 사용하도록 설정하는 방법에 대해 알아보기
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: tysonn
tags: azure-resource-manager

ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2016
ms.author: sewhee

---
# Azure 부하 분산 장치에 대한 로그 분석(미리보기)
Azure에서 부하 분산 장치를 관리하고 문제를 해결하는 데 다양한 유형의 로그를 사용할 수 있습니다. 이러한 로그 중 일부는 포털을 통해 액세스할 수 있으며 Azure Blob 저장소에서 모든 로그를 추출하고 다양한 도구(예: Excel 및 PowerBI)에서 볼 수 있습니다. 아래 목록에서 다른 종류의 로그에 대해 자세히 알아볼 수 있습니다.

* **감사 로그:** [Azure 감사 로그](../azure-portal/insights-debugging-with-events.md)(이전의 작업 로그)를 사용하여 Azure 구독에 제출된 모든 작업과 상태를 볼 수 있습니다. 감사 로그는 기본적으로 사용하도록 설정되며 Azure 포털에서 볼 수 있습니다.
* **경고 이벤트 로그:** 이 로그를 사용하여 부하 분산 장치에 대해 어떤 경고가 발생했는지 볼 수 있습니다. 부하 분산 장치에 대한 상태는 5분 마다 수집됩니다. 이 로그는 부하 분산 장치 경고 이벤트가 발생하는 경우에만 기록됩니다.
* **상태 프로브 로그:** 이 로그를 사용하여 프로브 상태 검사 상태, 부하 분산 장치 백 엔드에서 온라인 상태인 인스턴스 개수 및 부하 부산 장치에서 네트워크 트래픽을 받는 가상 컴퓨터의 백분율을 확인할 수 있습니다. 이 로그는 프로브 상태 이벤트 변경에 기록됩니다.

> [!WARNING]
> 로그는 리소스 관리자 배포 모델에 배포된 리소스에 대해서만 사용할 수 있습니다. 클래식 배포 모델에서 리소스에 대한 로그를 사용할 수 없습니다. 두 모델의 이해를 돕기 위해 [리소스 관리자 배포 및 클래식 배포 이해](../resource-manager-deployment-model.md) 문서를 참조하세요. <BR> 로그 분석은 현재 인터넷 연결 부하 분산 장치에 대해서만 작동합니다. 이 제한은 일시적이며 언제든지 변경될 수 있습니다. 향후 변경 내용을 확인하려면 이 페이지를 다시 방문해야 합니다.
> 
> 

## 로깅 사용
감사 로깅은 모든 리소스 관리자 리소스에 대해 항상 사용하도록 설정됩니다. 이러한 로그를 통해 사용 가능한 데이터 수집을 시작하려면 이벤트 및 상태 프로브 로깅을 사용하도록 설정해야 합니다. 로깅을 사용하려면 다음 단계를 따릅니다.

[Azure 포털](http://portal.azure.com)에 로그인합니다. 부하 분산 장치가 아직 없으면, 계속하기 전에 [부하 분산 장치를 만듭니다](load-balancer-get-started-internet-arm-ps.md).

포털에서 **찾아보기** >> **부하 분산 장치**를 클릭합니다.

![포털 - 부하 분산 장치](./media/load-balancer-monitor-log/load-balancer-browse.png)

기존 부하 분산 장치 >> **모든 설정**을 선택합니다.

![포털 - 부하 분산 장치-설정](./media/load-balancer-monitor-log/load-balancer-settings.png) <BR>

**설정** 블레이드에서 **진단**을 클릭한 다음 **진단** 창의 **상태** 옆에서 **설정** 블레이드의 **켜기**를 클릭하고 **저장소 계정**을 클릭하고 기존 저장소 계정을 선택하거나 새로 만듭니다.

**저장소 계정** 바로 아래 드롭다운 목록에서 경고 이벤트, 상태 프로브 상태 또는 둘 다를 로깅할지 선택한 다음 **저장**을 클릭합니다.

![Preview 포털 - 진단 로그](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

> [AZURE.INFORMATION] 감사 로그에는 별도의 저장소 계정이 필요하지 않습니다. 이벤트 및 상태 프로브 로깅에 대한 저장소를 사용할 경우 서비스 요금이 부과됩니다.
> 
> 

## 감사 로그
이 로그(이전의 "작업 로그")는 기본적으로 Azure에 의해 생성됩니다. 이 로그는 Azure의 이벤트 로그 저장소에서 90일 동안 유지됩니다. [이벤트 및 감사 로그 보기](../azure-portal/insights-debugging-with-events.md) 문서를 읽어 이러한 로그에 대해 자세히 알아보세요.

## 경고 이벤트 로그
이 로그는 위에서 설명한 대로 부하 분산 장치별로 설정한 경우에만 생성됩니다. 데이터는 로깅을 사용하도록 설정할 때 지정된 저장소 계정에 저장됩니다. 정보는 아래와 같이 JSON 형식으로 기록됩니다.

    {
    "time": "2016-01-26T10:37:46.6024215Z",
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }


JSON 출력은 경고가 생성된 부하 분산 장치에 대한 이유를 설명하는 *eventname* 속성을 표시합니다. 이 경우 경고는 원본 IP NAT 제한(SNAT)으로 인해 발생된 TCP 포트 소모로 인해 발생되었습니다.

## 상태 프로브 로그
이 로그는 위에서 설명한 대로 부하 분산 장치별로 설정한 경우에만 생성됩니다. 데이터는 로깅을 사용하도록 설정할 때 지정된 저장소 계정에 저장됩니다. 'insights-logs-loadbalancerprobehealthstatus'라는 컨테이너가 생성되고 다음 데이터가 기록됩니다.

        {
        "records":

        {
               "time": "2016-01-26T10:37:46.6024215Z",
            "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
            "category": "LoadBalancerProbeHealthStatus",
            "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
            "operationName": "LoadBalancerProbeHealthStatus",
            "properties": {
                "publicIpAddress": "40.83.190.158",
                "port": "81",
                "totalDipCount": 2,
                "dipDownCount": 1,
                "healthPercentage": 50.000000
            }
        },
        {
            "time": "2016-01-26T10:37:46.6024215Z",
            "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
            "category": "LoadBalancerProbeHealthStatus",
            "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
            "operationName": "LoadBalancerProbeHealthStatus",
            "properties": {
                "publicIpAddress": "40.83.190.158",
                "port": "81",
                "totalDipCount": 2,
                "dipDownCount": 0,
                "healthPercentage": 100.000000
            }
        }

    ]
    }

속성 필드의 JSON 출력은 상태 프로브 상태에 대한 기본 정보를 보여 줍니다. *dipDownCount* 속성은 실패한 프로브 응답으로 인해 네트워크 트래픽을 수신하지 않은 백 엔드의 총 인스턴스 수를 보여 줍니다.

## 감사 로그 보기 및 분석
다음 방법을 사용하여 감사 로그 데이터를 보고 분석할 수 있습니다.

* **Azure 도구:** Azure PowerShell, Azure 명령줄 인터페이스(CLI), Azure REST API 또는 Azure Preview 포털을 통해 감사 로그에서 정보를 검색합니다. 각 방법에 대한 단계별 지침은 [리소스 관리자로 작업 감사](../resource-group-audit.md) 문서에 자세히 나와 있습니다.
* **Power BI:** [Power BI](https://powerbi.microsoft.com/pricing) 계정이 아직 없는 경우에는 무료로 사용해볼 수 있습니다. [Power BI에 대한 Azure 감사 로그 콘텐츠 팩](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs)을 사용하여 미리 구성된 대시보드를 있는 그대로 사용하거나 사용자 지정하여 데이터를 분석할 수 있습니다.

## 상태 프로브 및 이벤트 로그 보기 및 분석
저장소 계정에 연결하고 이벤트 및 상태 프로브 로그에 대한 JSON 로그 항목을 검색해야 합니다. JSON 파일을 다운로드한 후 CSV로 변환하여 Excel, PowerBI 또는 기타 데이터 시각화 도구에서 볼 수 있습니다.

> [!TIP]
> Visual Studio를 익숙하게 사용할 수 있고 C#에서 상수 및 변수에 대한 값 변경에 대한 기본 개념이 있는 경우 Github에서 제공하는 [로그 변환기 도구](https://github.com/Azure-Samples/networking-dotnet-log-converter)를 사용할 수 있습니다.
> 
> 

## 추가 리소스
* [Power BI를 사용하여 Azure 감사 로그 시각화](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) 블로그 게시물.
* [Power BI 등에서 Azure 감사 로그 보기 및 분석](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) 블로그 게시물.

<!---HONumber=AcomDC_0824_2016-->