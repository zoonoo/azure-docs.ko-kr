---
title: "Azure에서 Linux VM에 예정된 이벤트 | Microsoft Docs"
description: "Linux 가상 컴퓨터에서 Azure 메타데이터 서비스를 사용하여 예정된 이벤트입니다."
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2017
ms.author: zivr
ms.openlocfilehash: 75e509a7e39f5b268ce550d0c4dea2261d4fe56f
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2017
---
# <a name="azure-metadata-service-scheduled-events-preview-for-linux-vms"></a>Azure 메타데이터 서비스: Linux VM에 예정된 이벤트(미리 보기)

> [!NOTE] 
> 사용 약관에 동의하게 되면 미리 보기를 사용할 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 Microsoft Azure 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>

예약된 이벤트는 Azure 메타데이터 서비스의 하위 서비스 중 하나입니다. 응용 프로그램에서 이벤트를 준비하고 중단을 제한할 수 있도록 예정된 이벤트(예: 다시 부팅)에 대한 정보를 나타냅니다. 이 기능은 PaaS 및 IaaS를 포함한 모든 Azure Virtual Machine 유형에 사용할 수 있습니다. 예약된 이벤트는 가상 컴퓨터에 예방 작업을 수행하여 이벤트의 영향을 최소화할 수 있는 시간을 제공합니다. 

예약된 이벤트를 Windows 및 Linux VM 모두에서 사용할 수 있습니다. Windows에서 예약된 이벤트에 대한 자세한 내용은 [Windows VM에 예약된 이벤트](../windows/scheduled-events.md)를 참조하세요.

## <a name="why-scheduled-events"></a>예정된 이벤트 의의

예약된 이벤트를 사용하면 플랫폼에서 시작된 유지 관리 또는 사용자가 시작한 서비스 작업의 영향을 제한하는 단계를 수행할 수 있습니다. 

복제 기술을 사용하여 상태를 유지 관리하는 다중 인스턴스 워크로드는 여러 인스턴스에서 발생하는 중단에 취약할 수 있습니다. 이러한 중단으로 인해 비용이 많이 드는 작업(예: 인덱스 다시 빌드) 또는 복제본 손실이 발생할 수 있습니다. 

그 밖에 많은 경우, 진행 중인 트랜잭션을 완료(또는 취소)하거나, 클러스터의 다른 VM에 작업을 다시 할당(수동 장애 조치)하거나, 네트워크 부하 분산 장치 풀에서 가상 컴퓨터를 제거하는 등 정상적인 종료 시퀀스를 수행하여 전체 서비스 가용성을 개선할 수 있습니다. 

예정된 이벤트에 대해 관리자에게 알리거나 그러한 이벤트를 로깅하는 것만으로도 클라우드에 호스팅된 응용 프로그램의 서비스 효율성을 개선하는 데 도움이 되는 경우가 있습니다.

Azure 메타데이터 서비스는 다음 사용 사례에서 예정된 이벤트를 나타냅니다.
-   플랫폼 시작 유지 관리(예: 호스트 OS 롤아웃)
-   사용자 시작 호출(예: 사용자에 의한 VM 다시 시작 또는 다시 배포)


## <a name="the-basics"></a>기본 사항  

Azure 메타데이터 서비스는 VM 내에서 액세스할 수 있는 REST 끝점을 사용하여 Virtual Machines 실행에 대한 정보를 공개합니다. 이 정보는 라우팅할 수 없는 IP를 통해 사용할 수 있으므로 VM 외부에 공개되지 않습니다.

### <a name="scope"></a>범위
Scheduled Events는 클라우드 서비스의 모든 Virtual Machines 또는 가용성 집합의 모든 Virtual Machines에 나타납니다. 따라서 이벤트의 `Resources` 필드를 확인하여 영향을 받을 VM을 식별해야 합니다. 

### <a name="discovering-the-endpoint"></a>끝점 검색
가상 컴퓨터가 VNet(가상 네트워크) 내에 생성된 경우, 라우팅할 수 없는 고정 IP(`169.254.169.254`)에서 메타데이터 서비스를 사용할 수 있습니다.
클라우드 서비스 및 클래식 VM의 기본 사례처럼 가상 컴퓨터가 가상 네트워크에 생성되지 않은 경우 사용할 끝점을 검색하려면 추가 논리가 필요합니다. [호스트 끝점을 검색](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm)하는 방법은 이 샘플을 참조하세요.

### <a name="versioning"></a>버전 관리 
인스턴스 메타데이터 서비스에는 버전이 있습니다. 버전은 필수이며 최신 버전은 `2017-03-01`입니다.

> [!NOTE] 
> 예약된 이벤트의 이전 미리 보기 릴리스는 api-version으로 {최신 버전}을 지원했습니다. 이 형식은 더 이상 지원되지 않으며 향후 사용되지 않을 예정입니다.

### <a name="using-headers"></a>헤더 사용
메타데이터 서비스를 쿼리할 때 요청이 실수로 리디렉션되지 않도록 `Metadata: true` 헤더를 제공해야 합니다.

### <a name="enabling-scheduled-events"></a>예약된 이벤트 사용
처음으로 예약된 이벤트를 요청하면 Azure는 가상 컴퓨터의 기능을 암시적으로 사용하도록 설정합니다. 결과적으로 최대 2분인 첫 번째 호출에서 지연된 응답을 예상해야 합니다.

### <a name="user-initiated-maintenance"></a>사용자 시작 유지 관리
사용자가 예정된 이벤트에서 Azure Portal, API, CLI 또는 PowerShell을 통해 가상 컴퓨터 유지 관리를 시작했습니다. 그러면 응용 프로그램의 유지 관리 준비 논리를 테스트할 수 있으며, 응용 프로그램에서 사용자 시작 유지 관리를 준비할 수 있습니다.

가상 컴퓨터를 다시 시작하면 `Reboot` 유형의 이벤트가 예약됩니다. 가상 컴퓨터를 다시 배포하면 `Redeploy` 유형의 이벤트가 예약됩니다.

> [!NOTE] 
> 현재는 사용자 시작 유지 관리 작업을 최대 10개까지 동시 예약할 수 있습니다. 이 제한은 예정된 이벤트 일반 공급 이전에 완화될 예정입니다.

> [!NOTE] 
> 현재는 예약된 이벤트를 실행하는 사용자 시작 유지 관리를 구성할 수 없습니다. 구성 기능은 이후 버전에 추가될 계획입니다.

## <a name="using-the-api"></a>API 사용

### <a name="query-for-events"></a>이벤트 쿼리
다음과 같이 호출하여 예약된 이벤트를 쿼리할 수 있습니다.

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01
```

응답에는 예약된 이벤트의 배열이 포함됩니다. 빈 배열은 현재 예약된 이벤트가 없음을 의미합니다.
예약된 이벤트가 있는 경우 응답에 다음과 같은 이벤트의 배열이 포함됩니다. 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},              
        }
    ]
}
```

### <a name="event-properties"></a>이벤트 속성
|속성  |  설명 |
| - | - |
| EventId | 이 이벤트의 GUID(Globally Unique Identifier)입니다. <br><br> 예제: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | 이 이벤트로 인해 발생하는 결과입니다. <br><br> 값 <br><ul><li> `Freeze`: 몇 초 동안 가상 컴퓨터를 일시 중지하도록 예약됩니다. CPU가 일시 중단되지만 메모리, 열려 있는 파일 또는 네트워크 연결에는 영향을 미치지 않습니다. <li>`Reboot`: 가상 컴퓨터를 다시 부팅하도록 예약합니다(비영구 메모리가 손실됨). <li>`Redeploy`: 가상 컴퓨터를 다른 노드로 이동하도록 예약합니다(임시 디스크가 손실됨). |
| ResourceType | 이 이벤트가 영향을 주는 리소스 형식입니다. <br><br> 값 <ul><li>`VirtualMachine`|
| 리소스| 이 이벤트가 영향을 주는 리소스 목록입니다. 최대 하나의 [업데이트 도메인](manage-availability.md)에 있는 컴퓨터를 포함하지만 UD의 모든 컴퓨터를 포함할 수는 없습니다. <br><br> 예제: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| 이벤트 상태 | 이 이벤트의 상태입니다. <br><br> 값 <ul><li>`Scheduled`: `NotBefore` 속성에 지정된 시간 이후 시작하도록 이 이벤트를 예약합니다.<li>`Started`: 이 이벤트가 시작되었습니다.</ul> `Completed` 또는 유사한 상태가 제공된 적이 없습니다. 이벤트가 완료되면 더 이상 이벤트가 반환되지 않습니다.
| NotBefore| 이 시간이 지난 후 이 이벤트가 시작될 수 있습니다. <br><br> 예제: <br><ul><li> 2016-09-19T18:29:47Z  |

### <a name="event-scheduling"></a>이벤트 예약
각 이벤트는 이벤트 유형에 따라 향후 최소한의 시간으로 예약됩니다. 이 시간은 이벤트의 `NotBefore` 속성에 반영됩니다. 

|EventType  | 최소 공지 |
| - | - |
| 중지| 15분 |
| Reboot | 15분 |
| 재배포 | 10분 |

### <a name="starting-an-event"></a>이벤트 시작 

예정된 이벤트에 대해 알게 되고 정상 종료를 위한 논리를 완료하면 `EventId`로 메타데이터 서비스에 대한 `POST` 호출을 실행하여 처리 중인 이벤트를 승인할 수 있습니다. 이는 Azure에 최소 알림 시간을 단축할 수 있음(가능한 경우)을 나타냅니다. 

```
curl -H Metadata:true -X POST -d '{"DocumentIncarnation":"5", "StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01
```

> [!NOTE] 
> 이벤트를 승인하면 해당 이벤트를 승인한 가상 컴퓨터뿐만 아니라 이벤트의 모든 `Resources`에 대해 이벤트가 진행됩니다. 따라서 승인을 조정할 리더를 선택할 수 있으며, 이는 `Resources` 필드의 첫 번째 컴퓨터처럼 간단할 수 있습니다.




## <a name="python-sample"></a>Python 샘플 

다음 샘플은 예약된 이벤트에 대한 메타데이터 서비스를 쿼리하고 처리 중인 각 이벤트를 승인합니다.

```python
#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01"
headers = "{Metadata:true}"
this_host = socket.gethostname()

def get_scheduled_events():
   req = urllib2.Request(metadata_url)
   req.add_header('Metadata', 'true')
   resp = urllib2.urlopen(req)
   data = json.loads(resp.read())
   return data

def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid = evt['EventId']
        status = evt['EventStatus']
        resources = evt['Resources']
        eventtype = evt['EventType']
        resourcetype = evt['ResourceType']
        notbefore = evt['NotBefore'].replace(" ","_")
        if this_host in resources:
            print "+ Scheduled Event. This host is scheduled for " + eventype + " not before " + notbefore
            # Add logic for handling events here

def main():
   data = get_scheduled_events()
   handle_scheduled_events(data)
   
if __name__ == '__main__':
  main()
  sys.exit(0)
```

## <a name="next-steps"></a>다음 단계 

- [인스턴스 메타데이터 서비스](instance-metadata-service.md)에서 사용 가능한 API에 대해 자세히 알아봅니다.
- [Azure에서 Linux 가상 컴퓨터에 대한 계획된 유지 관리](planned-maintenance.md)에 대해 알아봅니다.
