---
title: Windows Azure 진단 확장에서 Azure 이벤트 허브로 데이터 보내기
description: Azure Monitor에서 진단 확장을 구성하여 Azure 외부 위치로 데이터를 전달할 수 있도록 Azure Event Hub로 데이터를 보냅니다.
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/18/2020
ms.openlocfilehash: 5e5034e99d37d3681192c2ad066f28acd1c4aeeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672534"
---
# <a name="send-data-from-windows-azure-diagnostics-extension-to-azure-event-hubs"></a>Windows Azure 진단 확장에서 Azure 이벤트 허브로 데이터 보내기
Azure 진단 확장은 Azure 가상 시스템 및 기타 계산 리소스의 게스트 운영 체제 및 워크로드에서 모니터링 데이터를 수집하는 Azure Monitor의 에이전트입니다. 이 문서에서는 Azure 외부 위치로 전달할 수 있도록 WAD(Windows Azure 진단 확장)에서 Azure 이벤트 허브로 데이터를 보내는 방법을 [설명합니다.](https://azure.microsoft.com/services/event-hubs/)

## <a name="supported-data"></a>지원되는 데이터

이벤트 허브로 전송할 수 있는 게스트 운영 체제에서 수집된 데이터에는 다음이 포함됩니다. IIS 로그 및 크래시 덤프를 포함하여 WAD에서 수집한 다른 데이터 원본은 이벤트 허브로 보낼 수 없습니다.

* Windows (ETW) 이벤트에 대한 이벤트 추적
* 성능 카운터
* Windows 이벤트 로그에 응용 프로그램 로그를 포함한 Windows 이벤트 로그
* Azure Diagnostics 인프라 로그

## <a name="prerequisites"></a>사전 요구 사항

* 윈도우 진단 확장 1.6 이상. [지원되는 리소스에](diagnostics-extension-versions.md) 대한 버전 기록 및 Azure 진단 [확장 개요에](diagnostics-extension-overview.md) 대한 Azure 진단 확장 구성 구성 스키마 버전 및 기록을 참조하세요.
* 이벤트 허브 네임스페이스는 항상 프로비전되어야 합니다. 자세한 내용은 [이벤트 허브시작처를 참조하세요.](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)


## <a name="configuration-schema"></a>구성 스키마
구성 스키마에 대한 참조를 위해 진단 확장 및 [Azure 진단 구성 스키마를](diagnostics-extension-schema-windows.md) 활성화 및 구성하기 위한 다양한 옵션에 대한 [Windows Azure 진단 확장(WAD) 설치](diagnostics-extension-windows-install.md) 및 구성을 참조하십시오. 이 문서의 나머지 부분에서는 이 구성을 사용하여 이벤트를 이벤트 허브로 보내는 방법을 설명합니다. 

Azure 진단은 항상 Azure 저장소 계정에 로그 및 메트릭을 보냅니다. 데이터를 추가 위치로 보내는 하나 이상의 *데이터 싱크를* 구성할 수 있습니다. 각 싱크는 공용 구성의 [SinksConfig 요소에](diagnostics-extension-schema-windows.md#sinksconfig-element) 정의되어 있으며 개인 구성의 중요한 정보가 있습니다. 이벤트 허브에 대한 이 구성은 다음 표의 값을 사용합니다.

| 속성 | 설명 |
|:---|:---|
| 이름 | 싱크에 대한 설명 이름입니다. 구성에서 싱크에 보낼 데이터 원본을 지정하는 데 사용됩니다. |
| Url  | 양식 \<이벤트 허브-네임스페이스\>.servicebus.windows.net/\<이벤트 허브 이름에서\>이벤트 허브의 URL입니다.          |
| 공유 액세스 키 이름 | 최소한 **Send** 권한이 있는 이벤트 허브에 대한 공유 액세스 정책의 이름입니다. |
| SharedAccessKey     | 이벤트 허브에 대한 공유 액세스 정책의 기본 또는 보조 키입니다. |

예를 들어 공용 및 개인 구성은 다음과 같습니다. 이 구성은 이벤트 허브 데이터 싱크를 구성하고 사용하는 방법을 설명하기 위해 단일 성능 카운터 및 이벤트 로그를 사용하는 최소한의 구성입니다. 보다 복잡한 예제는 [Azure 진단 구성 스키마를](diagnostics-extension-schema-windows.md) 참조하십시오.

### <a name="public-configuration"></a>공용 구성

```JSON
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 5120
        },
        "PerformanceCounters": {
            "scheduledTransferPeriod": "PT1M",
            "sinks": "myEventHub",
            "PerformanceCounterConfiguration": [
                {
                    "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                    "sampleRate": "PT3M"
                }
            ]
        },
        "WindowsEventLog": {
            "scheduledTransferPeriod": "PT1M",
            "sinks": "myEventHub",
                "DataSource": [
                {
                    "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                }
            ]
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "myEventHub",
                    "EventHub": {
                        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule"
                    }
                }
            ]
        }
    },
    "StorageAccount": "mystorageaccount",
}
```


### <a name="private-configuration"></a>프라이빗 구성

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "EventHub": {
        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    }
}
```



## <a name="configuration-options"></a>구성 옵션
데이터 싱크에 데이터를 보내려면 데이터 원본 노드에서 **싱크 특성을** 지정합니다. **싱크 특성을** 배치할 위치에 따라 할당 범위가 결정됩니다. 다음 예제에서 **싱크** 특성은 모든 자식 성능 카운터를 이벤트 허브로 전송하는 **PerformanceCounters** 노드에 정의됩니다.

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "MyEventHub",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        }
    ]
}
```


다음 예제에서는 **싱크** 특성이 세 개의 카운터에 직접 적용되어 해당 성능 카운터만 이벤트 허브로 전송됩니다. 

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Rejected",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Queued",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        }
    ]
}
```

## <a name="validating-configuration"></a>구성 유효성 검사
다양한 방법을 사용하여 데이터가 이벤트 허브로 전송되고 있는지 확인할 수 있습니다. ne 간단한 방법은 [Azure Blob 저장소 또는 Azure Data Lake 저장소의 Azure 이벤트 허브를 통해 캡처 이벤트에](../../event-hubs/event-hubs-capture-overview.md)설명된 대로 이벤트 허브 캡처를 사용하는 것입니다. 


## <a name="troubleshoot-event-hubs-sinks"></a>Event Hubs 싱크 문제 해결

- Azure 진단 진단 자체에 대한 로그와 오류가 포함된 AZURE 저장소 테이블 **WADDiagnosticInfrastructureLogsTable을** 살펴보십시오. 한 가지 옵션은 [Azure Storage Explorer](https://www.storageexplorer.com) 등의 도구를 사용하여 이 Storage 계정에 연결하고 이 테이블을 본 후 지난 24시간 동안의 타임스탬프에 대한 쿼리를 추가하는 것입니다. 이 도구를 사용하여 .csv 파일을 내보내고 Microsoft Excel과 같은 애플리케이션에서 열 수 있습니다. Excel를 통해 어떤 오류가 보고되는지 확인하는 **EventHubs**와 같은 전화 카드 문자열을 쉽게 검색할 수 있습니다.  

- 이벤트 허브가 성공적으로 프로비저닝되었는지 확인합니다. 구성의 **PrivateConfig** 섹션의 모든 연결 정보는 포털에 있는 것과 같이 리소스의 값과 일치해야 합니다. 포털에 SAS 정책(예제의*SendRule)이* 정의되어 있고 *Send* 권한이 부여되었는지 확인합니다.  

## <a name="next-steps"></a>다음 단계

* [Event Hubs 개요](../../event-hubs/event-hubs-about.md)
* [이벤트 허브 만들기](../../event-hubs/event-hubs-create.md)
* [Event Hubs FAQ](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png



