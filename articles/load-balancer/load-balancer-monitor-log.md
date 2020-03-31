---
title: 공용 기본 Load Balancer에 대한 작업, 이벤트 및 카운터 모니터링
titleSuffix: Azure Load Balancer
description: 공용 기본 Load Balancer에 대한 경고 이벤트 및 상태 프로브 상태 로깅을 사용하도록 설정하는 방법에 대해 알아보기
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: allensu
ms.openlocfilehash: 0a21af683d9fa7849d3e96c545983c9f40a8d4c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76935327"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>공용 기본 Load Balancer용 Azure Monitor 로그

>[!IMPORTANT]
>Azure Load Balancer는 기본 및 표준이라는 두 가지 형식을 지원합니다. 이 문서는 기본 Load Balancer에 대해 설명합니다. 표준 Load Balancer에 대한 자세한 내용은 Azure Monitor에서 다차원 메트릭을 통해 원격 분석을 노출하는 [표준 Load Balancer 개요](load-balancer-standard-overview.md)를 참조하세요.

Azure에서 기본 Load Balancer를 관리하고 문제를 해결하는 데 다양한 유형의 로그를 사용할 수 있습니다. 이러한 로그 중 일부는 포털을 통해 액세스할 수 있으며, 로그는 이벤트 허브 또는 로그 분석 작업 영역으로 스트리밍할 수 있습니다. 모든 로그는 Azure Blob 저장소에서 추출할 수 있으며 Excel 및 Power BI와 같은 다양한 도구에서 볼 수 있습니다.  아래 목록에서 다른 종류의 로그에 대해 자세히 알아볼 수 있습니다.

* **활동 로그:** 활동 보기 로그를 사용하여 [리소스의 작업을 모니터링하여](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) Azure 구독에 제출되는 모든 활동과 해당 상태를 볼 수 있습니다. 활동 로그는 기본적으로 활성화되어 있으며 Azure 포털에서 볼 수 있습니다.
* **경고 이벤트 로그:** 이 로그를 사용하여 부하 분산 장치에서 발생한 경고를 볼 수 있습니다. 부하 분산 장치에 대한 상태는 5분 마다 수집됩니다. 이 로그는 부하 분산 장치 경고 이벤트가 발생하는 경우에만 기록됩니다.
* **상태 프로브 로그:** 상태 프로브 오류 때문에 부하 분산 장치에서 요청을 받지 않는 백 엔드 풀에 있는 인스턴스의 수와 같은 상태 프로브에서 발견한 문제를 보기 위해 이 로그를 사용할 수 있습니다. 상태 프로브 상태가 변경되는 경우에 이 로그가 기록됩니다.

> [!IMPORTANT]
> Azure Monitor 로그는 현재 공용 기본 로드 밸런서에 대해서만 작동합니다. 로그는 Resource Manager 배포 모델에 배포된 리소스에서만 사용할 수 있습니다. 클래식 배포 모델에서 리소스에 대한 로그를 사용할 수 없습니다. 배포 모델에 대한 자세한 내용은 [리소스 관리자 배포 및 클래식 배포 이해](../azure-resource-manager/management/deployment-models.md)를 참조하세요.

## <a name="enable-logging"></a>로깅 사용

활동 로깅은 모든 Resource Manager 리소스에 대해 사용하도록 설정됩니다. 이벤트 및 상태 프로브 로깅을 활성화하여 해당 로그를 통해 사용할 수 있는 데이터 수집을 시작합니다. 로깅을 사용하려면 다음 단계를 사용합니다.

[Azure 포털에](https://portal.azure.com)로그인합니다. 부하 분산 장치가 아직 없으면, 계속하기 전에 [부하 분산 장치를 만듭니다](https://docs.microsoft.com/azure/load-balancer/quickstart-create-basic-load-balancer-portal) .

1. 포털에서 리소스 **그룹을 클릭합니다.**
2. 로드 밸러버가 있는 ** \<>리소스 그룹 이름을** 선택합니다.
3. 부하 분산장치를 선택합니다.
4. 진단 설정 **모니터링을** > **Diagnostic settings**선택합니다.
5. 진단 **설정** 창에서 진단 **설정에서**진단 **설정에서 + 진단 설정 추가를**선택합니다.
6. 진단 **설정** 만들기 창에서 **이름** 필드에 **myLBDiagnostics를 입력합니다.**
7. **진단 설정에**대한 세 가지 옵션이 있습니다.  하나, 둘 또는 세 가지 모두를 선택하고 요구 사항에 맞게 각각 구성할 수 있습니다.
   * **저장소 계정에 보관**
   * **이벤트 허브로 스트리밍**
   * **Log Analytics에 보내기**

    ### <a name="archive-to-a-storage-account"></a>스토리지 계정에 보관
    이 프로세스에 대해 이미 만든 저장소 계정이 필요합니다.  저장소 계정을 만들려면 [저장소 계정 만들기를](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) 참조하십시오.

    1. 저장소 계정으로 보관 옆에 **있는 확인란을 선택합니다.**
    2. 저장소 계정 선택 창을 **열려면** **구성을** 선택합니다.
    3. 풀다운 상자에서 저장소 계정이 생성된 **구독을** 선택합니다.
    4. 풀다운 상자에서 저장소 **계정에서 저장소 계정의** 이름을 선택합니다.
    5. 확인을 선택합니다.

    ### <a name="stream-to-an-event-hub"></a>이벤트 허브로 스트림
    이 프로세스에 대해 이미 만든 이벤트 허브가 필요합니다.  이벤트 허브를 만들려면 [빠른 시작: Azure 포털을 사용하여 이벤트 허브 만들기를](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) 참조하십시오.

    1. 이벤트 허브로 스트리밍 옆의 **확인란을 선택합니다.**
    2. **선택 이벤트 허브** 창을 열려면 **구성을** 선택합니다.
    3. 풀다운 상자에서 이벤트 허브가 만들어진 **구독을** 선택합니다.
    4. 풀다운 상자에서 **이벤트 허브 네임스페이스를 선택합니다.**
    5. 풀다운 상자에서 **이벤트 허브 정책 이름을 선택합니다.**
    6. 확인을 선택합니다.

    ### <a name="send-to-log-analytics"></a>Log Analytics에 보내기
    이 프로세스에 대해 로그 분석 작업 영역을 이미 만들고 구성해야 합니다.  로그 분석 작업 영역을 만들려면 [Azure 포털에서 로그 분석 작업 영역 만들기를](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 참조하세요.

    1. 로그 분석으로 보내기 옆의 **확인란을 선택합니다.**
    2. 로그 분석 작업 영역이 풀다운 상자에 있는 **구독을** 선택합니다.
    3. 풀다운 상자에서 **로그 분석 작업 영역을** 선택합니다.


8. **진단 설정** 창의 **LOG** 섹션 아래에서 둘 다음 의 확인란을 선택합니다.
   * **LoadBalancerAlertEvent**
   * **LoadBalancerProbeHealthStatus**

9.  **진단 설정** 창의 **메트릭** 섹션 아래에서 다음 의 확인란을 선택합니다.
   * **모든 메트릭**

11. 모든 것이 올바르게 보이는지 확인하고 진단 **설정** 만들기 창 상단의 **저장을** 클릭합니다.

## <a name="activity-log"></a>활동 로그

활동 로그는 기본적으로 생성됩니다. 이 로그는 Azure의 이벤트 로그 저장소에 90일 동안 유지됩니다. 리소스 문서의 [작업을 모니터링하기 위해 활동 보기 로그를](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit) 읽고 이러한 로그에 대해 자세히 알아봅니다.

## <a name="archive-to-storage-account-logs"></a>저장소 계정 로그에 보관

### <a name="alert-event-log"></a>경고 이벤트 로그

이 로그는 부하 분산 장치별로 설정한 경우에만 생성됩니다. 이벤트는 JSON 형식으로 로깅되며, 로깅을 사용하도록 설정할 때 지정한 스토리지 계정에 저장됩니다. 다음 예제는 이벤트의 예입니다.

```json
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
}
```

JSON 출력에는 *로드* 밸런서가 경고를 만든 이유를 설명하는 이벤트 이름 속성이 표시됩니다. 이 경우 생성된 경고는 소스 IP NAT 제한(SNAT)으로 인한 TCP 포트 소모 때문입니다.

### <a name="health-probe-log"></a>상태 프로브 로그

이 로그는 위에서 설명한 대로 부하 분산 장치별로 설정한 경우에만 생성됩니다. 데이터는 로깅을 사용하도록 설정할 때 지정된 스토리지 계정에 저장됩니다. 'insights-logs-loadbalancerprobehealthstatus'라는 컨테이너가 생성되고 다음 데이터가 기록됩니다.

```json
{
    "records":[
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
    }]
}
```

속성 필드의 JSON 출력은 상태 프로브 상태에 대한 기본 정보를 보여 줍니다. *dipDownCount* 속성은 실패한 프로브 응답으로 인해 네트워크 트래픽을 수신하지 않는 백 엔드의 총 인스턴스 수를 표시합니다.

### <a name="view-and-analyze-the-activity-log"></a>활동 로그 보기 및 분석

다음 방법을 사용하여 활동 로그 데이터를 보고 분석할 수 있습니다.

* **Azure 도구:** Azure PowerShell, CLI(Azure 명령줄 인터페이스), Azure REST API 또는 Azure 포털을 통해 활동 로그에서 정보를 검색합니다. 각 방법에 대한 단계별 지침은 [리소스 관리자로 작업 감사](../azure-resource-manager/management/view-activity-logs.md) 문서에 자세히 나와 있습니다.
* **Power BI:**[Power BI](https:// .microsoft.com/pricing) 계정이 아직 없는 경우에는 무료로 사용해볼 수 있습니다. [Power BI에 대한 Azure 감사 로그 콘텐츠 팩](https:// .microsoft.com/documentation/ -content-pack-azure-audit-logs)을 사용하여 미리 구성된 대시보드에서 데이터를 분석하거나 요구 사항에 맞게 보기를 사용자 지정할 수 있습니다.

### <a name="view-and-analyze-the-health-probe-and-event-log"></a>상태 프로브 및 이벤트 로그 보기 및 분석

저장소 계정에 연결하고 이벤트 및 상태 프로브 로그에 대한 JSON 로그 항목을 검색합니다. JSON 파일을 다운로드하면 CSV로 변환하여 Excel, Power BI 또는 기타 데이터 시각화 도구에서 볼 수 있습니다.

> [!TIP]
> Visual Studio를 익숙하게 사용할 수 있고 C#에서 상수 및 변수에 대한 값 변경에 대한 기본 개념이 있는 경우 GitHub에서 제공하는 [로그 변환기 도구](https://github.com/Azure-Samples/networking-dotnet-log-converter)를 사용할 수 있습니다.

## <a name="stream-to-an-event-hub"></a>이벤트 허브로 스트림
진단 정보가 이벤트 허브로 스트리밍되면 Azure 모니터 통합을 사용하는 타사 SIEM 도구에서 중앙 집중식 로그 분석에 사용할 수 있습니다. 자세한 내용은 [Azure 모니터링 데이터를 이벤트 허브로 스트리밍 참조하세요.](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)

## <a name="send-to-log-analytics"></a>Log Analytics에 보내기
Azure의 리소스는 문제 해결 및 분석을 위해 정보에 대해 복잡한 쿼리를 실행할 수 있는 Log Analytics 작업 영역으로 직접 진단 정보를 보낼 수 있습니다.  자세한 내용은 [Azure 모니터의 로그 분석 작업 영역에서 Azure 리소스 로그 수집을](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace) 참조하십시오.

## <a name="next-steps"></a>다음 단계

[부하 분산 장치 프로브 이해](load-balancer-custom-probe-overview.md)
