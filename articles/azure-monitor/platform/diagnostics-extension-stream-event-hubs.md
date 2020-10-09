---
title: Microsoft Azure 진단 확장에서 Azure Event Hubs로 데이터 전송
description: Azure 이벤트 허브로 데이터를 전송 하는 Azure Monitor에서 진단 확장을 구성 하 여 Azure 외부의 위치로 전달 합니다.
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/18/2020
ms.openlocfilehash: 979535b1f9a237f6975908178fb1e5ed819181b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82233468"
---
# <a name="send-data-from-windows-azure-diagnostics-extension-to-azure-event-hubs"></a>Microsoft Azure 진단 확장에서 Azure Event Hubs로 데이터 전송
Azure Diagnostics 확장은 게스트 운영 체제에서 모니터링 데이터를 수집하고 Azure 가상 머신 및 기타 계산 리소스의 워크로드를 수집하는 Azure Monitor의 에이전트입니다. 이 문서에서는 Azure WAD (Microsoft Azure 진단 확장)에서 azure [Event Hubs](https://azure.microsoft.com/services/event-hubs/) 로 데이터를 전송 하 여 azure 외부의 위치로 전달 하는 방법을 설명 합니다.

## <a name="supported-data"></a>지원 되는 데이터

Event Hubs로 전송할 수 있는 게스트 운영 체제에서 수집 된 데이터는 다음과 같습니다. IIS 로그 및 크래시 덤프를 비롯 하 여 WAD에 의해 수집 된 다른 데이터 원본은 Event Hubs로 보낼 수 없습니다.

* Windows (ETW) 이벤트에 대한 이벤트 추적
* 성능 카운터
* Windows 이벤트 로그 (Windows 이벤트 로그의 응용 프로그램 로그 포함)
* Azure Diagnostics 인프라 로그

## <a name="prerequisites"></a>사전 요구 사항

* Windows 진단 확장 1.6 이상. 지원 되는 리소스에 대 한 [Azure 진단 확장 구성 스키마 버전 및 기록](diagnostics-extension-versions.md) 에 대 한 자세한 내용 및 [Azure 진단 확장 개요](diagnostics-extension-overview.md) 를 참조 하세요.
* Event Hubs 네임 스페이스는 항상 프로 비전 되어야 합니다. 자세한 내용은 [Event Hubs 시작을](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) 참조 하세요.


## <a name="configuration-schema"></a>구성 스키마
진단 확장을 사용 하도록 설정 하 고 구성 하는 다른 옵션에 대해서는 [Windows Azure 진단 확장 설치 및 구성 (WAD)](diagnostics-extension-windows-install.md) Azure 진단과 구성 스키마 참조에 대 한 [구성 스키마](diagnostics-extension-schema-windows.md) 를 참조 하세요. 이 문서의 나머지 부분에서는이 구성을 사용 하 여 이벤트 허브로 데이터를 전송 하는 방법을 설명 합니다. 

Azure 진단는 항상 로그 및 메트릭을 Azure Storage 계정으로 보냅니다. 데이터를 추가 위치로 전송 하는 *데이터 싱크* 를 하나 이상 구성할 수 있습니다. 각 싱크는 개인 구성에서 중요 한 정보를 사용 하 여 공용 구성의 [SinksConfig 요소](diagnostics-extension-schema-windows.md#sinksconfig-element) 에 정의 됩니다. Event hubs에 대 한이 구성은 다음 표의 값을 사용 합니다.

| 속성 | 설명 |
|:---|:---|
| 속성 | 싱크에 대 한 설명이 포함 된 이름입니다. 구성에서 싱크로 보낼 데이터 원본을 지정 하는 데 사용 됩니다. |
| Url  | Servicebus.windows.net/형식의 이벤트 허브 Url입니다. \<event-hubs-namespace\> \<event-hub-name\>          |
| SharedAccessKeyName | 적어도 **전송** 권한이 있는 이벤트 허브에 대 한 공유 액세스 정책의 이름입니다. |
| SharedAccessKey     | 이벤트 허브에 대 한 공유 액세스 정책의 기본 또는 보조 키입니다. |

공용 및 개인 구성 예제는 다음과 같습니다. 이는 이벤트 허브 데이터 싱크를 구성 하 고 사용 하는 방법을 보여 주기 위해 단일 성능 카운터 및 이벤트 로그를 포함 하는 최소 구성입니다. 더 복잡 한 예는 [Azure 진단 구성 스키마](diagnostics-extension-schema-windows.md) 를 참조 하세요.

### <a name="public-configuration"></a>공용 구성

```JSON
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 5120,
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
            }
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
데이터 싱크에 데이터를 보내려면 데이터 원본의 노드에 **싱크** 특성을 지정 합니다. **싱크** 특성을 놓으면 할당 범위가 결정 됩니다. 다음 예에서는 **싱크** 특성이 **PerformanceCounters** 노드에 정의 되어 모든 자식 성능 카운터가 이벤트 허브로 전송 됩니다.

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


다음 예제에서 **싱크** 특성은 3 개의 카운터에 직접 적용 되어 해당 성능 카운터만 이벤트 허브로 전송 됩니다. 

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
다양 한 메서드를 사용 하 여 데이터가 이벤트 허브로 전송 되는지 유효성을 검사할 수 있습니다. [Azure Blob Storage 또는 Azure Data Lake Storage에서 Azure Event Hubs를 통해 이벤트 캡처](../../event-hubs/event-hubs-capture-overview.md)에서 설명한 대로 Event Hubs 캡처를 사용 하는 것이 가장 간단 합니다. 


## <a name="troubleshoot-event-hubs-sinks"></a>Event Hubs 싱크 문제 해결

- Azure 진단 자체에 대 한 로그 및 오류가 포함 된 Azure Storage 테이블 **WADDiagnosticInfrastructureLogsTable** 를 확인 합니다. 한 가지 옵션은 [Azure Storage Explorer](https://www.storageexplorer.com) 등의 도구를 사용하여 이 Storage 계정에 연결하고 이 테이블을 본 후 지난 24시간 동안의 타임스탬프에 대한 쿼리를 추가하는 것입니다. 이 도구를 사용하여 .csv 파일을 내보내고 Microsoft Excel과 같은 애플리케이션에서 열 수 있습니다. Excel를 통해 어떤 오류가 보고되는지 확인하는 **EventHubs**와 같은 전화 카드 문자열을 쉽게 검색할 수 있습니다.  

- 이벤트 허브가 성공적으로 프로비저닝되었는지 확인합니다. 구성의 **privateconfig.json** 섹션에 있는 모든 연결 정보는 포털에 표시 된 리소스의 값과 일치 해야 합니다. 포털에서 SAS 정책 (예제에서는*Sendrule* )을 정의 하 고 *Send* 권한이 부여 되어 있는지 확인 합니다.  

## <a name="next-steps"></a>다음 단계

* [Event Hubs 개요](../../event-hubs/event-hubs-about.md)
* [이벤트 허브 만들기](../../event-hubs/event-hubs-create.md)
* [Event Hubs FAQ](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png



