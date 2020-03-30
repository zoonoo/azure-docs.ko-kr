---
title: Azure에서 Windows VM에 대해 예약된 이벤트를 모니터링합니다.
description: 예약된 이벤트에 대한 Azure 가상 컴퓨터를 모니터링하는 방법에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: mysarn
manager: gwallace
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.date: 08/20/2019
ms.author: sarn
ms.topic: conceptual
ms.openlocfilehash: 1cda07c18e4f5ef2a8c00b6a275f22ecc0935751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073307"
---
# <a name="monitoring-scheduled-events"></a>예약된 이벤트 모니터링

업데이트는 매일 Azure의 다른 부분에 적용되어 서비스를 안전하게 실행하며 최신 상태로 유지합니다. 계획된 업데이트 외에도 계획되지 않은 이벤트가 발생할 수도 있습니다. 예를 들어 하드웨어 성능 저하 또는 오류가 감지되면 Azure 서비스가 계획되지 않은 유지 관리를 수행해야 할 수 있습니다. 라이브 마이그레이션, 메모리 업데이트 보존 및 일반적으로 업데이트의 영향에 엄격한 막대를 유지, 대부분의 경우 이러한 이벤트는 고객에 게 거의 투명, 그리고 그들은 영향을 미치지 않습니다 또는 대부분 가상 컴퓨터 동결의 몇 초 발생. 그러나 일부 응용 프로그램의 경우 몇 초 동안 가상 시스템이 정지해도 영향을 줄 수 있습니다. 이러한 응용 프로그램에 가장 적합한 환경을 보장하려면 향후 Azure 유지 관리에 대해 미리 아는 것이 중요합니다. [예약된 이벤트 서비스는](scheduled-events.md) 예정된 유지 관리에 대해 알림을 받을 수 있는 프로그래밍 인터페이스를 제공하며 유지 관리를 정상적으로 처리할 수 있도록 합니다. 

이 문서에서는 예약된 이벤트를 사용하여 VM에 영향을 줄 수 있는 유지 관리 이벤트에 대한 알림을 받고 모니터링 및 분석에 도움이 되는 몇 가지 기본 자동화를 구축하는 방법을 보여 줍니다.


## <a name="routing-scheduled-events-to-log-analytics"></a>로그 분석에 예약된 이벤트를 라우팅

예약된 이벤트는 모든 Azure 가상 컴퓨터에서 사용할 수 있는 [Azure 인스턴스 메타데이터 서비스의](instance-metadata-service.md)일부로 사용할 수 있습니다. 고객은 자동화를 작성하여 가상 시스템의 끝점을 쿼리하여 예약된 유지 관리 알림을 찾고 상태를 저장하고 가상 컴퓨터를 회전에서 제외하는 등의 완화 를 수행할 수 있습니다. Azure 유지 관리 이벤트의 감사 로그를 가질 수 있도록 예약된 이벤트를 기록하기 위해 자동화를 구축하는 것이 좋습니다. 

이 문서에서는 로그 분석에 예약된 이벤트를 유지 보수 를 캡처하는 방법을 안내합니다. 그런 다음 팀에 전자 메일을 보내고 가상 컴퓨터에 영향을 미친 모든 이벤트에 대한 기록 보기를 가져오는 등 몇 가지 기본 알림 작업을 트리거합니다. 이벤트 집계 및 자동화의 경우 [Log Analytics를](/azure/azure-monitor/learn/quick-create-workspace)사용하지만 모니터링 솔루션을 사용하여 이러한 로그를 수집하고 자동화를 트리거할 수 있습니다.

![이벤트 수명 주기를 보여주는 다이어그램](./media/notifications/events.png)

## <a name="prerequisites"></a>사전 요구 사항

이 예제에서는 [가용성 집합에서 Windows 가상 컴퓨터를](tutorial-availability-sets.md)만들어야 합니다. 예약된 이벤트는 가용성 집합, 클라우드 서비스, 가상 시스템 규모 집합 또는 독립 실행형 VM의 가상 컴퓨터에 영향을 줄 수 있는 변경 사항에 대한 알림을 제공합니다. 가용성 집합의 다른 모든 VM에 대한 이벤트를 얻기 위해 수집기 역할을 하는 VM 중 하나에서 예약된 이벤트를 폴방식으로 폴링을 하는 [서비스를](https://github.com/microsoft/AzureScheduledEventsService) 실행합니다.    

자습서의 끝에 그룹 리소스 그룹을 삭제 하지 마십시오.

또한 가용성 집합에서 VM의 정보를 집계하는 데 사용할 [Log Analytics 작업 영역을 만들어야](/azure/azure-monitor/learn/quick-create-workspace) 합니다.

## <a name="set-up-the-environment"></a>환경 설정

이제 가용성 집합에 2개의 초기 VM이 있어야 합니다. 이제 동일한 가용성 집합에서 myCollectorVM이라는 3번째 VM을 만들어야 합니다. 

```azurepowershell-interactive
New-AzVm `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Name "myCollectorVM" `
   -Location "East US" `
   -VirtualNetworkName "myVnet" `
   -SubnetName "mySubnet" `
   -SecurityGroupName "myNetworkSecurityGroup" `
   -OpenPorts 3389 `
   -PublicIpAddressName "myPublicIpAddress3" `
   -AvailabilitySetName "myAvailabilitySet" `
   -Credential $cred
```
 

[GitHub에서](https://github.com/microsoft/AzureScheduledEventsService/archive/master.zip)프로젝트의 설치 .zip 파일을 다운로드합니다.

**myCollectorVM에** 연결하고 .zip 파일을 가상 컴퓨터에 복사하고 모든 파일을 추출합니다. VM에서 PowerShell 프롬프트를 엽니다. 예를 들어 `SchService.ps1` `PS C:\Users\azureuser\AzureScheduledEventsService-master\AzureScheduledEventsService-master\Powershell>`: 를 포함하는 폴더로 프롬프트를 이동하고 서비스를 설정합니다.

```powershell
.\SchService.ps1 -Setup
```

서비스를 시작합니다.

```powershell
.\SchService.ps1 -Start
```

이제 서비스는 예약된 모든 이벤트에 대해 10초마다 폴링을 시작하고 유지 관리를 신속하게 진행하기 위해 이벤트를 승인합니다.  동결, 재부팅, 재배포 및 선점 예약은 일정 이벤트에 의해 캡처된 이벤트입니다.   이벤트를 승인하기 전에 스크립트를 확장하여 일부 완화 작업을 트리거할 수 있습니다.

서비스 상태를 확인하고 실행 중인지 확인합니다.

```powershell
.\SchService.ps1 -status  
```

이렇게 하면 `Running`반환됩니다.

이제 서비스는 예약된 모든 이벤트에 대해 10초마다 폴링을 시작하고 유지 관리를 신속하게 진행하기 위해 이벤트를 승인합니다.  동결, 재부팅, 재배포 및 선점 일정 이벤트에 의해 캡처 된 이벤트입니다. 이벤트를 승인하기 전에 스크립트를 확장하여 몇 가지 완화 를 트리거할 수 있습니다.

위의 이벤트가 Schedule 이벤트 서비스에 의해 캡처되면 응용 프로그램 이벤트 로그 이벤트 상태, 이벤트 유형, 리소스(가상 시스템 이름) 및 NotBefore(최소 통지 기간)에 기록됩니다. 응용 프로그램 이벤트 로그에서 ID 1234를 사용하여 이벤트를 찾을 수 있습니다.

서비스가 설정되고 시작되면 Windows 응용 프로그램 로그에 이벤트를 기록합니다.   이 작업을 확인하려면 가용성 집합에서 가상 컴퓨터 중 하나를 다시 시작하면 VM이 다시 시작된 것을 보여주는 Windows Logs > 응용 프로그램 로그의 이벤트 뷰어에 기록되는 이벤트가 표시됩니다. 

![이벤트 뷰어의 스크린샷입니다.](./media/notifications/event-viewer.png)

일정 이벤트 서비스에 의해 이벤트가 캡처되면 이벤트 상태, 이벤트 유형, 리소스(VM 이름) 및 NotBefore(최소 통지 기간)가 있는 로그도 응용 프로그램에 기록됩니다. 응용 프로그램 이벤트 로그에서 ID 1234를 사용하여 이벤트를 찾을 수 있습니다.

> [!NOTE] 
> 이 예제에서는 가상 시스템이 가용성 집합에 있었기 때문에 예약된 이벤트를 수신 및 라우팅하기 위해 단일 가상 컴퓨터를 수집기로 지정하여 로그 분석 작업 공간으로 라우팅할 수 있었습니다. 독립 실행형 가상 시스템이 있는 경우 모든 가상 컴퓨터에서 서비스를 실행한 다음 로그 분석 작업 영역에 개별적으로 연결할 수 있습니다.
>
> 우리의 설정에 대 한, 우리는 윈도우를 선택, 하지만 리눅스에서 비슷한 솔루션을 디자인할 수 있습니다.

언제든지 스위치 `–stop` 및 `–remove`을 사용하여 예약된 이벤트 서비스를 중지/제거할 수 있습니다.

## <a name="connect-to-the-workspace"></a>작업 영역에 연결


이제 로그 분석 작업 영역을 수집기 VM에 연결하려고 합니다. Log Analytics 작업 영역은 리포지토리 역할을 하며 수집기 VM에서 응용 프로그램 로그를 캡처하도록 이벤트 로그 컬렉션을 구성합니다. 

 예약된 이벤트를 이벤트 로그로 라우팅하려면 서비스에서 응용 프로그램 로그로 저장되며 가상 컴퓨터를 Log Analytics 작업 영역에 연결해야 합니다.  
 
1. 만든 작업 영역에 대한 페이지를 엽니다.
1. **데이터 원본에 연결에서** **Azure 가상 시스템(VM)을**선택합니다.

    ![데이터 원본으로 VM에 연결](./media/notifications/connect-to-data-source.png)

1. **myCollectorVM을**검색하고 선택합니다. 
1. **myCollectorVM의**새 페이지에서 **연결을**선택합니다.

이렇게 하면 가상 컴퓨터에 [Microsoft 모니터링 에이전트가](/azure/virtual-machines/extensions/oms-windows) 설치됩니다. VM을 작업 영역에 연결하고 확장을 설치하는 데 몇 분 정도 걸릴 수 있습니다. 

## <a name="configure-the-workspace"></a>작업 영역 구성

1. 작업 영역의 페이지를 열고 **고급 설정을**선택합니다.
1. 왼쪽 메뉴에서 **데이터를** 선택한 다음 **Windows 이벤트 로그를 선택합니다.**
1. **다음 이벤트 로그에서 수집에서** *응용 프로그램을* 입력한 다음 목록에서 응용 **프로그램을** 선택합니다.

    ![고급 설정 선택](./media/notifications/advanced.png)

1. **오류,** **경고**및 **정보를** 선택한 다음 **저장을** 선택하여 설정을 저장합니다.


> [!NOTE]
> 약간의 지연이 있을 수 있으며 로그를 사용할 수 있게 되기까지 최대 10분이 걸릴 수 있습니다. 


## <a name="creating-an-alert-rule-with-azure-monitor"></a>Azure 모니터를 통해 경고 규칙 만들기 


이벤트가 Log Analytics로 푸시되면 다음 [쿼리를](/azure/azure-monitor/log-query/get-started-portal) 실행하여 일정 이벤트를 찾을 수 있습니다.

1. 페이지 상단에서 **로그를** 선택하고 다음을 텍스트 상자에 붙여넣습니다.

    ```
    Event
    | where EventLog == "Application" and Source contains "AzureScheduledEvents" and RenderedDescription contains "Scheduled" and RenderedDescription contains "EventStatus" 
    | project TimeGenerated, RenderedDescription
    | extend ReqJson= parse_json(RenderedDescription)
    | extend EventId = ReqJson["EventId"]
    ,EventStatus = ReqJson["EventStatus"]
    ,EventType = ReqJson["EventType"]
    ,NotBefore = ReqJson["NotBefore"]
    ,ResourceType = ReqJson["ResourceType"]
    ,Resources = ReqJson["Resources"]
    | project-away RenderedDescription,ReqJson
    ```

1. **저장을**선택한 다음 이름에 대해 *logQuery를* 입력하고 **쿼리를** 유형으로 두고 *VMLog를* **범주로**입력한 다음 **저장을**선택합니다. 

    ![쿼리 저장](./media/notifications/save-query.png)

1. **새 경고 규칙**을 선택합니다. 
1. 규칙 **만들기** 페이지에서 `collectorworkspace` **리소스로**둡니다.
1. **조건에서**고객 로그 검색이 될 때마다 항목을 *선택합니다. <login undefined> * **신호 논리 구성** 페이지가 열립니다.
1. **임계값**아래에서 *0을* 입력한 다음 **완료를 선택합니다.**
1. **작업**에서 작업 **그룹 만들기를**선택합니다. **작업 그룹 추가** 페이지가 열립니다.
1. **작업 그룹 이름에서** *myActionGroup을*입력합니다.
1. **짧은 이름으로** **myActionGroup을**입력합니다.
1. **리소스 그룹에서** **myResourceGroup가용성을**선택합니다.
1. 작업에서 **작업 이름** 유형 **전자**메일에서 **전자 메일/SMS/푸시/음성을**선택합니다. **이메일/SMS/푸시/음성** 페이지가 열립니다.
1. **전자 메일**, 입력을 선택한 다음 **확인을**선택합니다.
1. 작업 그룹 추가 페이지에서 **확인을** **선택합니다.** 
1. 규칙 **만들기** 페이지에서 **경고 세부 정보**아래에서 경고 규칙 **이름에**대해 *myAlert를* 입력한 다음 **설명에**대한 *전자 메일 경고 규칙을* 입력합니다.
1. 완료되면 경고 규칙 **만들기를**선택합니다.
1. 가용성 집합에서 VM 중 하나를 다시 시작합니다. 몇 분 내에 경고가 트리거된 전자 메일을 받아야 합니다.

경고 규칙을 관리하려면 리소스 그룹으로 이동하여 왼쪽 메뉴에서 **경고를** 선택한 다음 페이지 상단에서 **경고 규칙 관리를** 선택합니다.

     
## <a name="next-steps"></a>다음 단계

자세한 내용은 GitHub의 [예약된 이벤트 서비스](https://github.com/microsoft/AzureScheduledEventsService) 페이지를 참조하십시오.
