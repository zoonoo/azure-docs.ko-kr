---
title: 연결 모니터 (미리 보기) | 마이크로 소프트 문서
description: 연결 모니터(미리 보기)를 사용하여 분산 환경에서 네트워크 통신을 모니터링하는 방법을 알아봅니다.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: d926a9f686f0f4c39203b8a217a7c608cfad926e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548111"
---
# <a name="network-connectivity-monitoring-with-connection-monitor-preview"></a>연결 모니터를 통한 네트워크 연결 모니터링(미리 보기)

연결 모니터(미리 보기)는 Azure 네트워크 감시기에서 통합된 종단 간 연결 모니터링을 제공합니다. 연결 모니터(미리 보기) 기능은 하이브리드 및 Azure 클라우드 배포를 지원합니다. Network Watcher는 Azure 배포에 대한 연결 관련 메트릭을 모니터링, 진단 및 볼 수 있는 도구를 제공합니다.

다음은 연결 모니터(미리 보기)의 몇 가지 사용 사례입니다.

- 프런트 엔드 웹 서버 VM은 다중 계층 응용 프로그램에서 데이터베이스 서버 VM과 통신합니다. 두 VM 간의 네트워크 연결을 확인하려고 합니다.
- 미국 동부 지역의 VM이 미국 중부 지역의 VM을 ping하도록 하고 지역 간 네트워크 대기 시간을 비교하려고 합니다.
- 워싱턴 주 시애틀과 버지니아 주 애슈번에 여러 온-프레미스 오피스 사이트가 있습니다. 사무실 사이트는 Office 365 URL에 연결됩니다. Office 365 URL 사용자의 경우 시애틀과 애쉬번 간의 대기 시간을 비교합니다.
- 하이브리드 응용 프로그램에Azure 저장소 끝점에 연결해야 합니다. 온-프레미스 사이트와 Azure 응용 프로그램은 동일한 Azure Storage 끝점에 연결됩니다. 온-프레미스 사이트의 대기 시간을 Azure 응용 프로그램의 대기 시간과 비교하려고 합니다.
- 온-프레미스 설정과 클라우드 응용 프로그램을 호스트하는 Azure VM 간의 연결을 확인하려고 합니다.

미리 보기 단계에서 연결 모니터는 네트워크 감시자 [연결 모니터](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint) 기능과 NPM(네트워크 성능 모니터) 서비스 [연결 모니터](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity) 기능의 두 가지 기능을 결합합니다.

다음은 연결 모니터(미리 보기)의 몇 가지 이점입니다.

* Azure 및 하이브리드 모니터링 요구 사항에 대한 통합되고 직관적인 환경
* 지역 간, 작업 공간 간 연결 모니터링
* 더 높은 프로빙 주파수와 네트워크 성능에 대한 가시성 향상
* 하이브리드 배포에 대한 신속한 경고
* HTTP, TCP 및 ICMP를 기반으로 하는 연결 검사 지원 
* Azure 및 비Azure 테스트 설정모두에 대한 메트릭 및 로그 분석 지원

![연결 모니터가 Azure VM, Azure 이외의 호스트, 끝점 및 데이터 저장소 위치와 상호 작용하는 방법을 보여 주다](./media/connection-monitor-2-preview/hero-graphic.png)

모니터링을 위해 연결 모니터(미리 보기)를 사용하려면 다음 단계를 따르십시오. 

1. 모니터링 에이전트를 설치합니다.
1. 구독에서 네트워크 감시자 사용.
1. 연결 모니터를 만듭니다.
1. 데이터 분석 및 경고를 설정합니다.
1. 네트워크의 문제를 진단합니다.

다음 섹션에서는 이러한 단계에 대한 세부 정보를 제공합니다.

## <a name="install-monitoring-agents"></a>모니터링 에이전트 설치

연결 모니터는 연결 검사를 실행하기 위해 경량 실행 파일을 사용합니다.  Azure 환경과 온-프레미스 환경에서 모두 연결 검사를 지원합니다. 사용하는 실행 파일은 VM이 Azure또는 온-프레미스에서 호스팅되는지 여부에 따라 달라집니다.

### <a name="agents-for-azure-virtual-machines"></a>Azure 가상 시스템에 대한 에이전트

연결 모니터가 Azure VM을 모니터링 소스로 인식하도록 하려면 네트워크 감시자 에이전트 가상 시스템 확장을 설치합니다. 이 확장을 *네트워크 감시자 확장이라고도*합니다. Azure 가상 시스템에는 종단 간 모니터링 및 기타 고급 기능을 트리거하기 위해 확장이 필요합니다. 

VM 을 만들 때 네트워크 감시자 확장을 [설치할](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm)수 있습니다. Linux 및 Windows용 네트워크 감시자 확장을 별도로 설치, [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) 구성 및 해결할 수도 [있습니다.](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows)

NSG(네트워크 보안 그룹) 또는 방화벽에 대한 규칙은 원본과 대상 간의 통신을 차단할 수 있습니다. 연결 모니터는 이 문제를 감지하고 토폴로지에서 진단 메시지로 표시합니다. 연결 모니터링을 사용하려면 NSG 및 방화벽 규칙이 원본과 대상 간에 TCP 또는 ICMP를 통해 패킷을 허용하도록 합니다.

### <a name="agents-for-on-premises-machines"></a>온-프레미스 머신용 에이전트

연결 모니터가 온-프레미스 컴퓨터를 모니터링의 소스로 인식하도록 하려면 컴퓨터에 Log Analytics 에이전트를 설치합니다. 그런 다음 네트워크 성능 모니터 솔루션을 사용하도록 설정합니다. 이러한 에이전트는 Log Analytics 작업 영역에 연결되어 있으므로 에이전트가 모니터링을 시작하기 전에 작업 영역 ID와 기본 키를 설정해야 합니다.

Windows 컴퓨터에 대한 로그 분석 에이전트를 설치하려면 [Windows용 Azure Monitor 가상 컴퓨터 확장을](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows)참조하십시오.

경로에 방화벽 또는 NV(네트워크 가상 어플라이언스)가 포함된 경우 대상에 연결할 수 있는지 확인합니다.

## <a name="enable-network-watcher-on-your-subscription"></a>구독에서 네트워크 감시자 사용

가상 네트워크가 있는 모든 구독은 네트워크 감시자에서 사용할 수 있습니다. 구독에서 가상 네트워크를 만들면 네트워크 감시자가 가상 네트워크의 리전 및 구독에서 자동으로 활성화됩니다. 이 자동 사용 설정은 리소스에 영향을 주거나 요금이 부과되지 않습니다. 구독에서 네트워크 감시자가 명시적으로 비활성화되지 않았는지 확인합니다. 

자세한 내용은 [네트워크 감시자 사용 을](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)참조하십시오.

## <a name="create-a-connection-monitor"></a>연결 모니터 만들기 

연결 모니터는 정기적으로 통신을 모니터링합니다. 도달 가능성 및 대기 시간의 변경 사항을 알려줍니다. 원본 에이전트와 대상 끝점 간의 현재 및 기록 네트워크 토폴로지를 확인할 수도 있습니다.

원본은 Azure VM 또는 설치된 모니터링 에이전트가 있는 온-프레미스 컴퓨터일 수 있습니다. 대상 끝점은 Office 365 URL, Dynamics 365 URL, 사용자 지정 URL, Azure VM 리소스 아이디, IPv4, IPv6, FQDN 또는 도메인 이름일 수 있습니다.

### <a name="access-connection-monitor-preview"></a>액세스 연결 모니터(미리 보기)

1. Azure 포털 홈 페이지에서 **네트워크 감시자**로 이동합니다.
1. 왼쪽에서 **모니터링** 섹션에서 **연결 모니터(미리 보기)를**선택합니다.
1. 연결 모니터(미리 보기)에서 만든 모든 연결 모니터가 표시됩니다. 연결 모니터의 고전적인 환경에서 만든 연결 **모니터를** 보려면 연결 모니터 탭으로 이동하십시오.

    ![연결 모니터에서 만든 연결 모니터를 보여주는 스크린샷(미리 보기)](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="create-a-connection-monitor"></a>연결 모니터 만들기

연결 모니터(미리 보기)에서 만드는 연결 모니터에서 온-프레미스 컴퓨터와 Azure VM을 모두 소스로 추가할 수 있습니다. 이러한 연결 모니터는 엔드포인트에 대한 연결을 모니터링할 수도 있습니다. 끝점은 Azure 또는 다른 URL 또는 IP에 있을 수 있습니다.

연결 모니터(미리 보기)에는 다음 엔터티가 포함됩니다.

* **연결 모니터 리소스** - 지역별 Azure 리소스입니다. 다음 엔터티는 모두 연결 모니터 리소스의 속성입니다.
* **끝점** - 연결 검사에 참여하는 소스 또는 대상입니다. 끝점의 예로는 Azure VM, 온-프레미스 에이전트, URL 및 IP가 있습니다.
* **테스트 구성** - 테스트를 위한 프로토콜별 구성입니다. 선택한 프로토콜에 따라 포트, 임계값, 테스트 빈도 및 기타 매개 변수를 정의할 수 있습니다.
* **테스트 그룹** - 소스 끝점, 대상 끝점 및 테스트 구성을 포함하는 그룹입니다. 연결 모니터에는 두 개 이상의 테스트 그룹이 포함될 수 있습니다.
* **테스트** - 소스 끝점, 대상 끝점 및 테스트 구성의 조합입니다. 테스트는 모니터링 데이터를 사용할 수 있는 가장 세분화된 수준입니다. 모니터링 데이터에는 실패한 검사의 백분율과 RTT(왕복 시간)가 포함됩니다.

 ![연결 모니터를 보여 테스트 그룹과 테스트 간의 관계를 정의하는 다이어그램](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="create-a-connection-monitor-from-the-azure-portal"></a>Azure 포털에서 연결 모니터 만들기

Azure 포털에서 연결 모니터를 만들려면 다음 단계를 따르십시오.

1. 연결 **모니터(미리 보기)** 대시보드에서 왼쪽 위 모서리에서 **에서 만들기를**선택합니다.
1. **기본** 탭에서 연결 모니터에 대한 정보를 입력합니다.
   * **연결 모니터 이름** - 연결 모니터의 이름을 추가합니다. Azure 리소스에 대한 표준 명명 규칙을 사용합니다.
   * **구독** - 연결 모니터에 대한 구독을 선택합니다.
   * **지역** - 연결 모니터의 지역을 선택합니다. 이 리전에서 생성된 소스 VM만 선택할 수 있습니다.
   * **작업 영역 구성** - 작업 영역에 모니터링 데이터가 저장됩니다. 사용자 지정 작업 영역 또는 기본 작업 영역을 사용할 수 있습니다. 
       * 기본 작업 영역을 사용하려면 확인란을 선택합니다. 
       * 사용자 지정 작업 영역을 선택하려면 확인란을 선택합니다. 그런 다음 사용자 지정 작업 영역의 구독 및 지역을 선택합니다. 
1. 탭 하단에서 **다음: 테스트 그룹을**선택합니다.

   ![연결 모니터의 기본 탭을 보여주는 스크린샷](./media/connection-monitor-2-preview/create-cm-basics.png)

1. 테스트 **그룹** 탭에서 **+ 테스트 그룹을**선택합니다. 테스트 그룹을 설정하려면 [연결 모니터의 테스트 그룹 만들기를](#create-test-groups-in-a-connection-monitor)참조하십시오. 
1. 탭 하단에서 **다음: 검토 + 만들기를** 선택하여 연결 모니터를 검토합니다.

   ![테스트 그룹 탭과 테스트 그룹 세부 정보를 추가하는 창을 보여주는 스크린샷](./media/connection-monitor-2-preview/create-tg.png)

1. 검토 **+ 만들기** 탭에서 연결 모니터를 만들기 전에 기본 정보 및 테스트 그룹을 검토합니다. 연결 모니터를 편집해야 하는 경우:
   * 기본 세부 정보를 편집하려면 연필 아이콘을 선택합니다.
   * 테스트 그룹을 편집하려면 테스트 그룹을 선택합니다.

   > [!NOTE] 
   > **검토 + 만들기** 탭에는 연결 모니터 미리 보기 단계에서 월별 비용이 표시됩니다. 현재 **비용** 열에는 요금이 부과되지 않습니다. 연결 모니터를 일반적으로 사용할 수 있게 되면 이 열에는 월별 요금이 표시됩니다. 
   > 
   > 연결 모니터 미리 보기 단계에서도 로그 분석 수집 요금이 부과됩니다.

1. 연결 모니터를 만들 준비가 되면 **검토 + 만들기** 탭 하단에서 **을 선택합니다.**

   ![연결 모니터의 스크린샷, 검토 + 만들기 탭 표시](./media/connection-monitor-2-preview/review-create-cm.png)

연결 모니터(미리 보기)는 백그라운드에서 연결 모니터 리소스를 만듭니다.

#### <a name="create-a-connection-monitor-by-using-armclient"></a>ARMClient를 사용하여 연결 모니터 만들기

다음 코드를 사용하여 ARMClient를 사용하여 연결 모니터를 만듭니다.

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "https://management.azure.com"

$SUB = "subscriptions/<subscription id 1>;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_<region>"

$body =

"{

location: '<region>',

properties: {

endpoints: [{

name: 'workspace',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

filter: {

 items: [{

type: 'AgentAddress',

address: '<FQDN of your on-premises agent>'

}]

}

          },

 {

name: 'vm1',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

},

 {

name: 'vm2',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

   },

{

name: 'azure portal'

address: '<URL>'

   },

 {

    name: 'ip',

     address: '<IP>'

 }

  ],

  testGroups: [{

    name: 'Connectivity to Azure Portal and Public IP',

    testConfigurations: ['http', 'https', 'tcpEnabled', 'icmpEnabled'],

    sources: ['vm1', 'workspace'],

    destinations: ['azure portal', 'ip']

   },

{

    name: 'Connectivty from Azure VM 1 to Azure VM 2',

   // Choose your protocol
   
    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: <threshold for checks failed %>,

     roundTripTimeMs: <threshold for RTT>

    }

   }, {

    name: 'https',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    httpConfiguration: {

     preferHTTPS: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }

  ]

 }

} "
```

배포 명령은 다음과 같습니다.
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

### <a name="create-test-groups-in-a-connection-monitor"></a>연결 모니터에서 테스트 그룹 만들기

연결 모니터의 각 테스트 그룹에는 네트워크 매개 변수에서 테스트되는 원본과 대상이 포함됩니다. 실패한 검사의 백분율과 테스트 구성에 대한 RTT에 대해 테스트됩니다.

Azure 포털에서 연결 모니터에서 테스트 그룹을 만들려면 다음 필드에 대한 값을 지정합니다.

* **테스트 그룹 사용 안 함** - 이 필드를 선택하여 테스트 그룹에서 지정한 모든 소스 및 대상에 대한 모니터링을 비활성화할 수 있습니다. 이 선택 영역은 기본적으로 지워집니다.
* **이름** - 테스트 그룹의 이름을 지정합니다.
* **원본** - 에이전트가 설치된 경우 Azure VM과 온-프레미스 컴퓨터를 모두 소스로 지정할 수 있습니다. 원본에 대한 에이전트를 설치하려면 [모니터링 에이전트 설치를](#install-monitoring-agents)참조하십시오.
   * Azure 에이전트를 선택하려면 **Azure 에이전트** 탭을 선택합니다. 연결 모니터를 만들 때 지정한 영역에 바인딩된 VM만 표시됩니다. 기본적으로 VM은 속한 구독으로 그룹화됩니다. 이러한 그룹은 축소됩니다. 
   
       구독 수준에서 계층 구조의 다른 수준으로 드릴다운할 수 있습니다.

      **에이전트가** > **Subnets** > **있는** >  > 서브넷**VM** 구독**리소스 그룹**

      필드별로 **그룹** 값을 변경하여 다른 수준에서 트리를 시작할 수도 있습니다. 예를 들어 가상 네트워크별로 그룹화하는 경우 계층 구조VNETs **VNETs** > **서브넷** > **VM에 에이전트가 있는 VM이 에이전트와 함께**표시됩니다.

      ![소스 추가 패널 및 Azure 에이전트 탭을 표시하는 연결 모니터의 스크린샷](./media/connection-monitor-2-preview/add-azure-sources.png)

   * 온-프레미스 에이전트를 선택하려면 **비-Azure 에이전트 탭을** 선택합니다. 기본적으로 에이전트는 지역별로 작업 영역으로 그룹화됩니다. 이러한 모든 작업 영역에는 네트워크 성능 모니터 솔루션이 구성됩니다. 
   
       작업 영역에 네트워크 성능 모니터를 추가해야 하는 경우 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview)에서 가져옵니다. 네트워크 성능 모니터를 추가하는 방법에 대한 자세한 내용은 [Azure Monitor의 모니터링 솔루션을](https://docs.microsoft.com/azure/azure-monitor/insights/solutions)참조하십시오. 
   
       연결 **모니터 만들기** 보기에서 **기본** 탭에서 기본 영역이 선택됩니다. 지역을 변경하는 경우 새 지역의 작업 영역에서 에이전트를 선택할 수 있습니다. 필드별로 **그룹** 값을 서브넷별로 그룹화할 수도 있습니다.

      ![연결 모니터의 스크린샷, 소스 추가 패널 및 비Azure 에이전트 탭 표시](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   * 선택한 Azure 및 비Azure 에이전트를 검토하려면 **검토** 탭으로 이동하십시오.

      ![소스 추가 패널 및 검토 탭을 표시하는 연결 모니터의 스크린샷](./media/connection-monitor-2-preview/review-sources.png)

   * 소스 설정이 완료되면 **소스 추가** 패널 하단에서 **완료 를**선택합니다.

* **대상** - Azure VM 또는 모든 끝점(공용 IP, URL 또는 FQDN)에 대한 연결을 대상으로 지정하여 모니터링할 수 있습니다. 단일 테스트 그룹에서 Azure VM, Office 365 URL, Dynamics 365 URL 및 사용자 지정 끝점을 추가할 수 있습니다.

    * Azure VM을 대상으로 선택하려면 **Azure VM** 탭을 선택합니다. 기본적으로 Azure VM은 기본 탭에서 **연결 모니터 만들기** 보기에서 선택한 것과 동일한 지역에 있는 구독 계층 구조로 그룹화됩니다. **Basics** 지역을 변경하고 새로 선택한 리전에서 Azure VM을 선택할 수 있습니다. 그런 다음 구독 수준에서 Azure 에이전트 수준과 같은 계층 구조의 다른 수준으로 드릴다운할 수 있습니다.

       ![Azure VM 탭을 표시하는 대상 추가 창의 스크린샷](./media/connection-monitor-2-preview/add-azure-dests1.png)

       ![구독 수준을 보여주는 대상 추가 창의 스크린샷](./media/connection-monitor-2-preview/add-azure-dests2.png)

    * 끝점을 대상으로 선택하려면 **끝점** 탭을 선택합니다. 끝점 목록에는 이름으로 그룹화된 Office 365 테스트 URL 및 Dynamics 365 테스트 URL이 포함됩니다. 이러한 끝점 외에도 동일한 연결 모니터의 다른 테스트 그룹에서 만든 끝점을 선택할 수 있습니다. 
    
        새 끝점을 추가하려면 오른쪽 위 모서리에서 **+ 끝점을**선택합니다. 그런 다음 끝점 이름과 URL, IP 또는 FQDN을 제공합니다.

       ![연결 모니터에서 끝점을 대상으로 추가할 위치를 보여주는 스크린샷](./media/connection-monitor-2-preview/add-endpoints.png)

    * 선택한 Azure VM 및 끝점을 검토하려면 **검토** 탭을 선택합니다.
    * 목적지 선택을 마치면 **완료를**선택합니다.

* **테스트 구성** - 테스트 그룹에서 테스트 구성을 연결할 수 있습니다. Azure 포털은 테스트 그룹당 하나의 테스트 구성만 허용하지만 ARMClient를 사용하여 추가를 추가할 수 있습니다.

    * **이름** - 테스트 구성의 이름을 지정합니다.
    * **프로토콜** - TCP, ICMP 또는 HTTP를 선택합니다. HTTP를 HTTPS로 변경하려면 **HTTP를** 프로토콜로 선택하고 **443을** 포트로 선택합니다.
        * **네트워크 테스트 구성 만들기** - **프로토콜** 필드에서 **HTTP를** 선택한 경우에만 이 확인란이 나타납니다. 이 상자를 선택하여 구성의 다른 위치에 지정한 것과 동일한 소스 및 대상을 사용하는 다른 테스트 구성을 만듭니다. 새로 만든 테스트 구성의 이름이 지정됩니다. `<the name of your test configuration>_networkTestConfig`
        * **추적 경로 사용 안 함** - 이 필드는 프로토콜이 TCP 또는 ICMP인 테스트 그룹에 적용됩니다. 소스가 토폴로지 및 홉별 RTT를 검색하지 못하도록 하려면 이 상자를 선택합니다.
    * **대상 포트** - 원하는 대상 포트를 통해 이 필드를 사용자 지정할 수 있습니다.
    * **테스트 빈도** – 이 필드를 사용하여 소스가 지정한 프로토콜 및 포트에서 대상을 ping하는 빈도를 선택합니다. 30초, 1분, 5분, 15분 또는 30분을 선택할 수 있습니다. 소스는 선택한 값에 따라 대상에 대한 연결을 테스트합니다.  예를 들어 30초를 선택하면 소스는 30초 동안 적어도 한 번은 대상에 대한 연결을 확인합니다.
    * **성공 임계값** - 다음 네트워크 매개 변수에 대한 임계값을 설정할 수 있습니다.
       * **실패한 검사** – 소스가 지정한 조건을 사용하여 대상에 대한 연결을 확인할 때 실패할 수 있는 검사의 백분율을 설정합니다. TCP 또는 ICMP 프로토콜의 경우 실패한 검사의 백분율은 패킷 손실 의 백분율과 동일시될 수 있습니다. HTTP 프로토콜의 경우 이 필드는 응답을 받지 못한 HTTP 요청의 백분율을 나타냅니다.
       * **왕복 시간** – 테스트 구성을 통해 대상에 연결하는 데 소스가 걸리는 시간을 밀리초단위로 설정합니다.
    
       ![연결 모니터에서 테스트 구성을 설정할 위치를 보여주는 스크린샷](./media/connection-monitor-2-preview/add-test-config.png)

테스트 그룹에 추가하는 모든 소스, 대상 및 테스트 구성은 개별 테스트로 세분화됩니다. 다음은 소스와 대상을 세분화하는 방법의 예입니다.

* 테스트 그룹: TG1
* 출처: 3 (A, B, C)
* 목적지: 2 (D, E)
* 테스트 구성: 2 (구성 1, 구성 2)
* 생성된 총 테스트: 12

| 테스트 번호 | 원본 | 대상 | 테스트 구성 |
| --- | --- | --- | --- |
| 1 | A | D | 구성 1 |
| 2 | A | D | 구성 2 |
| 3 | A | E | 구성 1 |
| 4 | A | E | 구성 2 |
| 5 | b | D | 구성 1 |
| 6 | b | D | 구성 2 |
| 7 | b | E | 구성 1 |
| 8 | b | E | 구성 2 |
| 9 | C | D | 구성 1 |
| 10 | C | D | 구성 2 |
| 11 | C | E | 구성 1 |
| 12 | C | E | 구성 2 |

### <a name="scale-limits"></a> 확장 한도

연결 모니터에는 다음과 같은 확장 제한이 있습니다.

* 지역별 구독당 최대 연결 모니터: 100
* 연결 모니터당 최대 테스트 그룹: 20
* 연결 모니터당 최대 소스 및 대상: 100
* 연결 모니터당 최대 테스트 구성: 
    * ARM클라이언트를 통해 20
    * 2 Azure 포털을 통해

## <a name="analyze-monitoring-data-and-set-alerts"></a>모니터링 데이터 분석 및 경고 설정

연결 모니터를 만든 후 소스는 테스트 구성에 따라 대상에 대한 연결을 확인합니다.

### <a name="checks-in-a-test"></a>테스트에서 검사

테스트 구성에서 선택한 프로토콜에 따라 연결 모니터(미리 보기)는 소스 대상 쌍에 대한 일련의 검사를 실행합니다. 검사는 선택한 테스트 빈도에 따라 실행됩니다.

HTTP를 사용하는 경우 서비스는 응답 코드를 반환한 HTTP 응답 수를 계산합니다. 결과는 실패한 검사의 백분율을 결정합니다. RTT를 계산하기 위해 서비스는 HTTP 호출과 응답 사이의 시간을 측정합니다.

TCP 또는 ICMP를 사용하는 경우 서비스는 패킷 손실 비율을 계산하여 실패한 검사의 백분율을 결정합니다. RTT를 계산하기 위해 서비스는 전송된 패킷에 대한 승인(ACK)을 받는 데 걸린 시간을 측정합니다. 네트워크 테스트에 대한 추적 경로 데이터를 사용하도록 설정한 경우 온-프레미스 네트워크의 홉별 손실 및 대기 시간을 볼 수 있습니다.

### <a name="states-of-a-test"></a>테스트 의 상태

검사가 반환하는 데이터에 따라 테스트에는 다음과 같은 상태가 있을 수 있습니다.

* **Pass** - 실패한 검사 및 RTT의 백분율에 대한 실제 값이 지정된 임계값 내에 있습니다.
* **실패** - 실패한 검사 또는 RTT의 백분율에 대한 실제 값이 지정된 임계값을 초과했습니다. 임계값을 지정하지 않으면 실패한 검사의 백분율이 100이면 테스트가 Fail 상태에 도달합니다.
* **경고** - 실패한 검사의 백분율에 대한 기준이 지정되지 않았습니다. 지정된 조건이 없는 경우 연결 모니터(미리 보기)가 자동으로 임계값을 할당합니다. 해당 임계값을 초과하면 테스트 상태가 경고로 변경됩니다.

### <a name="data-collection-analysis-and-alerts"></a>데이터 수집, 분석 및 경고

연결 모니터(미리 보기)가 수집하는 데이터는 로그 분석 작업 영역에 저장됩니다. 연결 모니터를 만들 때 이 작업 영역을 설정합니다. 

모니터링 데이터는 Azure 모니터 메트릭에서도 사용할 수 있습니다. Log Analytics를 사용하여 원하는 기간 동안 모니터링 데이터를 유지할 수 있습니다. Azure Monitor는 기본적으로 30일 동안만 메트릭을 저장합니다. 

데이터에 [메트릭 기반 경고를 설정할](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/)수 있습니다.

#### <a name="monitoring-dashboards"></a>대시보드 모니터링

모니터링 대시보드에는 구독, 지역, 타임스탬프, 소스 및 대상 유형에 액세스할 수 있는 연결 모니터 목록이 표시됩니다.

네트워크 감시자의 연결 모니터(미리 보기)로 이동하면 다음을 통해 데이터를 볼 수 있습니다.

* **연결 모니터** - 구독, 지역, 타임스탬프, 소스 및 대상 유형에 대해 생성된 모든 연결 모니터 목록입니다. 이 보기는 기본값입니다.
* **테스트 그룹** - 구독, 지역, 타임스탬프, 소스 및 대상 유형에 대해 만든 모든 테스트 그룹 목록입니다. 이러한 테스트 그룹은 연결 모니터에 의해 필터링되지 않습니다.
* **테스트** - 구독, 지역, 타임스탬프, 소스 및 대상 유형에 대해 실행되는 모든 테스트 목록입니다. 이러한 테스트는 연결 모니터 또는 테스트 그룹에서 필터링되지 않습니다.

다음 이미지에서 세 가지 데이터 뷰는 화살표 1로 표시됩니다.

대시보드에서 각 연결 모니터를 확장하여 테스트 그룹을 볼 수 있습니다. 그런 다음 각 테스트 그룹을 확장하여 테스트에서 실행되는 테스트를 볼 수 있습니다. 

다음을 기반으로 목록을 필터링할 수 있습니다.

* **최상위 필터** - 구독, 지역, 타임스탬프 소스 및 대상 유형을 선택합니다. 다음 이미지의 상자 2를 참조하십시오.
* **상태 기반 필터** - 연결 모니터, 테스트 그룹 또는 테스트의 상태에 따라 필터링합니다. 다음 이미지에서 화살표 3을 참조하십시오.
* **사용자 지정 필터** - 일반 검색을 수행하려면 **모두 선택을** 선택합니다. 특정 엔터티에서 검색하려면 드롭다운 목록에서 선택합니다. 다음 이미지에서 화살표 4를 참조하십시오.

![연결 모니터에서 연결 모니터, 테스트 그룹 및 테스트의 보기를 필터링하는 방법을 보여 주었던 스크린샷(미리 보기)](./media/connection-monitor-2-preview/cm-view.png)

예를 들어 소스 IP가 10.192.64.56인 연결 모니터(미리 보기)의 모든 테스트를 살펴보는 경우:
1. 보기를 **테스트로**변경합니다.
1. 검색 필드에서 *10.192.64.56을* 입력합니다.
1. 드롭다운 목록에서 **소스**를 선택합니다.

소스 IP가 10.192.64.56인 연결 모니터(미리 보기)에서 실패한 테스트만 표시하려면 다음을 수행합니다.
1. 보기를 **테스트로**변경합니다.
1. 상태 기반 필터의 경우 실패 를 **선택합니다.**
1. 검색 필드에서 *10.192.64.56을* 입력합니다.
1. 드롭다운 목록에서 **소스**를 선택합니다.

대상이 outlook.office365.com 연결 모니터(미리 보기)에서 실패한 테스트만 표시하려면 다음을 수행합니다.
1. 보기를 **테스트로**변경합니다.
1. 상태 기반 필터의 경우 실패 를 **선택합니다.**
1. 검색 필드에 *outlook.office365.com*
1. 드롭다운 목록에서 **대상을 선택합니다.**

   ![Outlook.Office365.com 대상에 대한 실패한 테스트만 표시하도록 필터링된 보기를 보여주는 스크린샷](./media/connection-monitor-2-preview/tests-view.png)

RTT의 추세와 연결 모니터에 대한 실패한 검사의 백분율을 보려면 다음을 수행하십시오.
1. 조사할 연결 모니터를 선택합니다. 기본적으로 모니터링 데이터는 테스트 그룹별로 구성됩니다.

   ![테스트 그룹에서 표시되는 연결 모니터에 대한 메트릭을 보여주는 스크린샷](./media/connection-monitor-2-preview/cm-drill-landing.png)

1. 조사할 테스트 그룹을 선택합니다.

   ![테스트 그룹을 선택할 위치를 보여주는 스크린샷](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

    RTT 또는 실패한 검사의 백분율을 기준으로 테스트 그룹의 상위 5개 실패한 테스트가 표시됩니다. 각 테스트에 대해 실패한 검사의 백분율에 대한 RTT 및 추세선이 표시됩니다.
1. 목록에서 테스트를 선택하거나 조사할 다른 테스트를 선택합니다. 시간 간격과 실패한 검사의 백분율에 대해 임계값과 실제 값이 표시됩니다. RTT의 경우 임계값, 평균, 최소 값 및 최대 값이 표시됩니다.

   ![RTT에 대한 테스트 결과 및 실패한 검사의 백분율을 보여주는 스크린샷](./media/connection-monitor-2-preview/cm-drill-charts.png)

1. 더 많은 데이터를 보려면 시간 간격을 변경합니다.
1. 소스를 보려면 변경, 대상 또는 테스트 구성을 확인합니다. 
1. 실패한 테스트를 기반으로 소스를 선택하고 실패한 상위 5개 테스트를 조사합니다. 예를 들어 **소스별** > **Sources** 보기 및 **대상별** > 보기를 선택하여 연결 모니터에서 관련 테스트를**조사합니다.**

   ![실패한 상위 5개 테스트의 성능 메트릭을 보여주는 스크린샷](./media/connection-monitor-2-preview/cm-drill-select-source.png)

RTT의 추세와 테스트 그룹에 대한 실패한 검사의 백분율을 보려면 다음을 수행하십시오.

1. 조사할 테스트 그룹을 선택합니다. 

    기본적으로 모니터링 데이터는 소스, 대상 및 테스트 구성(테스트)별로 정렬됩니다. 나중에 뷰를 테스트 그룹에서 소스, 대상 또는 테스트 구성으로 변경할 수 있습니다. 그런 다음 실패한 상위 5개 테스트를 조사할 엔터티를 선택합니다. 예를 들어 보기를 원본 및 대상으로 변경하여 선택한 연결 모니터에서 관련 테스트를 조사합니다.
1. 조사할 테스트를 선택합니다.

   ![테스트를 선택할 위치를 보여주는 스크린샷](./media/connection-monitor-2-preview/tg-drill.png)

    시간 간격과 실패한 검사의 백분율에 대해 임계값 과 실제 값이 표시됩니다. RTT의 경우 임계값, 평균, 최소 값 및 최대 값이 표시됩니다. 선택한 테스트에 대해 경고가 발생했습니다.
1. 더 많은 데이터를 보려면 시간 간격을 변경합니다.

RTT의 추세와 테스트에 대한 실패한 검사의 백분율을 보려면 다음을 수행하십시오.
1. 조사할 소스, 대상 및 테스트 구성을 선택합니다.

    시간 간격과 실패한 검사의 백분율에 대해 임계값 과 실제 값이 표시됩니다. RTT의 경우 임계값, 평균, 최소 값 및 최대 값이 표시됩니다. 선택한 테스트에 대해 경고가 발생했습니다.

   ![테스트에 대한 메트릭을 보여주는 스크린샷](./media/connection-monitor-2-preview/test-drill.png)

1. 네트워크 토폴로지보기를 보려면 **토폴로지**를 선택합니다.

   ![네트워크 토폴로지 탭을 보여주는 스크린샷](./media/connection-monitor-2-preview/test-topo.png)

1. 식별된 문제를 보려면 토폴로지에서 경로의 홉을 선택합니다. 이러한 홉은 Azure 리소스입니다. 현재 온-프레미스 네트워크에서는 이 기능을 사용할 수 없습니다.

   ![토폴로지 탭에서 선택한 홉 링크를 보여주는 스크린샷](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-log-analytics"></a>로그 분석에서 쿼리 로그

로그 분석을 사용하여 모니터링 데이터의 사용자 지정 보기를 생성합니다. UI에 표시되는 모든 데이터는 Log Analytics에서 수집한 것입니다. 리포지토리의 데이터를 대화식으로 분석할 수 있습니다. 에이전트 상태 또는 Log Analytics에 기반한 다른 솔루션의 데이터를 상관 관계합니다. 데이터를 Excel 또는 Power BI로 내보내거나 공유 가능한 링크를 만듭니다.

#### <a name="metrics-in-azure-monitor"></a>Azure Monitor의 메트릭

연결 모니터(미리 보기) 환경 전에 생성된 연결 모니터에서 검색 실패% , 평균 왕복, 검사실패퍼센트(미리 보기) 및 왕복 타임(미리 보기)의 네 가지 메트릭을 모두 사용할 수 있습니다. 연결 모니터(미리 보기) 환경에서 생성된 연결 모니터에서 는 *(미리 보기)로*태그가 지정된 메트릭에 대해서만 데이터를 사용할 수 있습니다.

![연결 모니터에서 메트릭을 보여주는 스크린샷(미리 보기)](./media/connection-monitor-2-preview/monitor-metrics.png)

메트릭을 사용하는 경우 리소스 유형을 Microsoft.Network/networkWatchers/연결모니터로 설정합니다.

| 메트릭 | 표시 이름 | 단위 | 집계 유형 | Description | 차원 |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | 실패한 프로브 % | 백분율 | 평균 | 연결 모니터링 프로브의 백분율이 실패했습니다. | 치수 없음 |
| AverageRoundtripMs | 평균. 왕복 시간 (ms) | 밀리초 | 평균 | 소스와 대상 간에 전송되는 연결 모니터링 프로브에 대한 평균 네트워크 RTT입니다. |             치수 없음 |
| 검사 실패 퍼센트 (미리 보기) | 검사 실패율(미리 보기) | 백분율 | 평균 | 테스트에 대한 실패한 검사의 백분율입니다. | 커넥션모니터리소스Id <br>소스 주소 지정 <br>SourceName <br>소스리소스 ID <br>SourceType <br>프로토콜 <br>DestinationAddress <br>DestinationName <br>대상자원 <br>DestinationType <br>DestinationPort <br>테스트 그룹 이름 <br>테스트 구성 이름 <br>지역 |
| 왕복 타임텀(미리 보기) | 왕복 시간(ms) (미리 보기) | 밀리초 | 평균 | 원본과 대상 간에 전송되는 검사에 대한 RTT입니다. 이 값은 평균이 아닙니다. | 커넥션모니터리소스Id <br>소스 주소 지정 <br>SourceName <br>소스리소스 ID <br>SourceType <br>프로토콜 <br>DestinationAddress <br>DestinationName <br>대상자원 <br>DestinationType <br>DestinationPort <br>테스트 그룹 이름 <br>테스트 구성 이름 <br>지역 |

#### <a name="metric-alerts-in-azure-monitor"></a>Azure 모니터의 메트릭 경고

Azure 모니터에서 경고를 만들려면 다음을 수행합니다.

1. 연결 모니터(미리 보기)에서 만든 연결 모니터 리소스를 선택합니다.
1. **메트릭이** 연결 모니터의 신호 유형으로 표시되는지 확인합니다.
1. **조건 추가에서** **신호 이름에**대해 **확인실패%(미리 보기)** 또는 **왕복 타임(미리 보기)을 선택합니다.**
1. **신호 유형의**경우 **메트릭을 선택합니다.** 예를 들어 **확인 실패%(미리 보기)를 선택합니다.**
1. 측정항목의 모든 측정기준이 나열됩니다. 치수 이름과 치수 값을 선택합니다. 예를 들어 **소스 주소를** 선택한 다음 연결 모니터에 있는 모든 소스의 IP 주소를 입력합니다.
1. **경고 논리에서**다음 세부 정보를 입력합니다.
   * **조건 유형**: **정적**.
   * **조건** 및 **임계값**.
   * **집계 세분성 및 평가 빈도**: 연결 모니터(미리 보기)는 매분마다 데이터를 업데이트합니다.
1. **작업에서**작업 그룹을 선택합니다.
1. 경고 세부 정보를 제공합니다.
1. 경고 규칙을 만듭니다.

   ![Azure 모니터에서 규칙 만들기 영역을 보여 주면 됩니다. "소스 주소" 및 "소스 끝점 이름" 강조 표시](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="diagnose-issues-in-your-network"></a>네트워크 문제 진단

연결 모니터(미리 보기)를 사용하면 연결 모니터와 네트워크의 문제를 진단할 수 있습니다. 하이브리드 네트워크의 문제는 이전에 설치한 Log Analytics 에이전트에서 감지됩니다. Azure의 문제는 네트워크 감시자 확장에서 검색됩니다. 

네트워크 토폴로지에서 Azure 네트워크에서 문제를 볼 수 있습니다.

온-프레미스 VM에 소스가 있는 네트워크의 경우 다음과 같은 문제를 검색할 수 있습니다.

* 요청 시간이 초과되었습니다.
* DNS에 의해 해결되지 않은 끝점 - 일시적 또는 지속적. URL이 잘못되었습니다.
* 호스트를 찾을 수 없습니다.
* 대상에 연결할 수 없는 소스입니다. ICMP를 통해 연결할 수 없는 대상입니다.
* 인증서 관련 문제: 
    * 에이전트를 인증하는 데 필요한 클라이언트 인증서입니다. 
    * 인증서 재배치 목록에 액세스할 수 없습니다. 
    * 끝점의 호스트 이름이 인증서의 제목 또는 주체 대체 이름과 일치하지 않습니다. 
    * 원본의 로컬 컴퓨터 신뢰할 수 있는 인증 기관 저장소에 루트 인증서가 없습니다. 
    * SSL 인증서가 만료되었거나 유효하지 않거나 취소되었거나 호환되지 않습니다.

원본이 Azure VM인 네트워크의 경우 다음 문제를 검색할 수 있습니다.

* 에이전트 문제:
    * 에이전트가 중지되었습니다.
    * DNS 확인에 실패했습니다.
    * 대상 포트에서 수신 대기하는 응용 프로그램이나 수신기가 없습니다.
    * 소켓을 열 수 없습니다.
* VM 상태 문제: 
    * 시작 중
    * 중지 중
    * 중지됨
    * 할당 취소 중
    * 할당 취소됨
    * 재부팅
    * 할당되지 않음
* ARP 테이블 항목이 없습니다.
* 로컬 방화벽 문제 또는 NSG 규칙으로 인해 트래픽이 차단되었습니다.
* 가상 네트워크 게이트웨이 문제: 
    * 누락된 경로입니다.
    * 두 게이트웨이 사이의 터널연결이 끊어지거나 누락되었습니다.
    * 터널에서 두 번째 게이트웨이를 찾을 수 없습니다.
    * 피어링 정보를 찾을 수 없습니다.
* 마이크로 소프트 에지에서 경로가 누락되었습니다.
* 시스템 경로 또는 UDR로 인해 트래픽이 중지되었습니다.
* 게이트웨이 연결에서 BGP가 활성화되지 않습니다.
* DIP 프로브가 부하 분산기에서 다운되었습니다.
