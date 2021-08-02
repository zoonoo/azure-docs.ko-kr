---
title: 연결 모니터 만들기 - PowerShell
titleSuffix: Azure Network Watcher
description: PowerShell을 사용하여 연결 모니터를 만드는 방법을 알아봅니다.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1b9fe56663f49e7fc6a7068e57df6d20003d2330
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110662022"
---
# <a name="create-a-connection-monitor-by-using-powershell"></a>PowerShell을 사용하여 연결 모니터 만들기

> [!IMPORTANT]
> 2021년 7월 1일부터는 기존 작업 영역에 새 테스트를 추가하거나 네트워크 성능 모니터에서 새 작업 영역을 사용하도록 설정할 수 없습니다. 또한 연결 모니터(클래식)에서 새 연결 모니터를 추가할 수 없습니다. 2021년 7월 1일 이전에 만든 테스트 및 연결 모니터는 계속 사용할 수 있습니다. 현재 워크로드에 대한 서비스 중단을 최소화하려면 2024년 2월 29일 이전에 [네트워크 성능 모니터에서 테스트를 마이그레이션](migrate-to-connection-monitor-from-network-performance-monitor.md)하거나 [연결 모니터(클래식)에서 Azure Network Watcher의 새 연결 모니터로 마이그레이션](migrate-to-connection-monitor-from-connection-monitor-classic.md)합니다.


Azure Network Watcher의 연결 모니터 기능을 사용하여 리소스 간의 통신을 모니터링하는 방법을 알아봅니다.


## <a name="before-you-begin"></a>시작하기 전에

연결 모니터를 사용하여 만든 연결 모니터에서 온-프레미스 머신과 Azure VM(가상 머신)을 모두 원본으로 추가할 수 있습니다. 이러한 연결 모니터는 엔드포인트에 대한 연결을 모니터링할 수도 있습니다. 엔드포인트는 Azure 또는 다른 URL 또는 IP에 있을 수 있습니다.

연결 모니터에는 다음 엔터티가 포함됩니다.

* **연결 모니터 리소스**: 지역별 Azure 리소스입니다. 다음 엔터티는 모두 연결 모니터 리소스의 속성입니다.
* **엔드포인트**: 연결 검사에 참여하는 원본 또는 대상입니다. 엔드포인트의 예로는 Azure VM, 온-프레미스 에이전트, URL 및 IP가 있습니다.
* **테스트 구성**: 테스트를 위한 프로토콜 관련 구성입니다. 선택한 프로토콜에 따라 포트, 임계값, 테스트 빈도 및 기타 매개 변수를 정의할 수 있습니다.
* **테스트 그룹**: 원본 엔드포인트, 대상 엔드포인트 및 테스트 구성을 포함하는 그룹입니다. 연결 모니터는 둘 이상의 테스트 그룹을 포함할 수 있습니다.
* **테스트**: 원본 엔드포인트, 대상 엔드포인트 및 테스트 구성의 조합입니다. 테스트는 모니터링 데이터를 사용할 수 있는 가장 세분화된 수준입니다. 모니터링 데이터에는 실패한 검사 비율과 RTT(왕복 시간)가 포함됩니다.

    ![테스트 그룹과 테스트 간의 관계를 정의하는 연결 모니터를 보여 주는 다이어그램입니다.](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-a-connection-monitor"></a>연결 모니터를 만드는 단계

PowerShell을 사용하여 연결 모니터를 만들려면 다음 명령을 사용합니다.

```powershell

//Connect to your Azure account with the subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId <your-subscription>
//Select region
$nw = "NetworkWatcher_centraluseuap"
//Declare endpoints like Azure VM below. You can also give VNET,Subnet,Log Analytics workspace
$sourcevmid1 = New-AzNetworkWatcherConnectionMonitorEndpointObject -Name MyAzureVm -ResourceID /subscriptions/<your-subscription>/resourceGroups/<your resourceGroup>/providers/Microsoft.Compute/virtualMachines/<vm-name>
//Declare endpoints like URL, IPs
$bingEndpoint = New-AzNetworkWatcherConnectionMonitorEndpointObject -name Bing -Address www.bing.com # Destination URL
//Create test configuration.Choose Protocol and parametersSample configs below.

$IcmpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -IcmpProtocol
$TcpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -TcpProtocol -Port 80
$httpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -HttpProtocol -Port 443 -Method GET -RequestHeader @{Allow = "GET"} -ValidStatusCodeRange 2xx, 300-308 -PreferHTTPS
$httpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name http-tc -TestFrequencySec 60 -ProtocolConfiguration $httpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
$icmpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name icmp-tc -TestFrequencySec 30 -ProtocolConfiguration $icmpProtocolConfiguration -SuccessThresholdChecksFailedPercent 5 -SuccessThresholdRoundTripTimeMs 500
$tcpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name tcp-tc -TestFrequencySec 60 -ProtocolConfiguration $TcpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
//Create Test Group
$testGroup1 = New-AzNetworkWatcherConnectionMonitorTestGroupObject -Name testGroup1 -TestConfiguration $httpTestConfiguration, $tcpTestConfiguration, $icmpTestConfiguration -Source $sourcevmid1 -Destination $bingEndpoint,
$testname = "cmtest9"
//Create Connection Monitor
New-AzNetworkWatcherConnectionMonitor -NetworkWatcherName $nw -ResourceGroupName NetworkWatcherRG -Name $testname -TestGroup $testGroup1

```

## <a name="description-of-properties"></a>속성 설명

* **ConnectionMonitorName**: 연결 모니터 리소스의 이름입니다.

* **SUB**: 연결 모니터를 만들려는 구독의 구독 ID입니다.

* **NW**: 연결 모니터가 만들어진 Network Watcher 리소스 ID입니다.

* **위치**: 연결 모니터가 만들어진 지역입니다.

* **엔드포인트**
    * **이름**: 각 엔드포인트에 대한 고유한 이름입니다.
    * **리소스 ID**: Azure 엔드포인트의 경우 리소스 ID는 VM에 대한 Azure Resource Manager 리소스 ID를 나타냅니다. 비 Azure 엔드포인트의 경우 리소스 ID는 비 Azure 에이전트에 연결된 Log Analytics 작업 영역에 대한 Azure Resource Manager 리소스 ID를 나타냅니다.
    * **주소**: 리소스 ID가 지정되지 않았거나 Log Analytics 작업 영역에 리소스 ID가 있는 경우에만 적용됩니다. 리소스 ID 없이 사용하는 경우 이는 모든 퍼블릭 엔드포인트의 URL 또는 IP일 수 있습니다. Log Analytics 리소스 ID와 함께 사용하는 경우 모니터링 에이전트의 FQDN을 나타냅니다.
    * **필터**: 비 Azure 엔드포인트의 경우 필터를 사용하여 연결 모니터 리소스의 Log Analytics 작업 영역에서 모니터링 에이전트를 선택합니다. 필터를 설정하지 않으면 Log Analytics 작업 영역에 속하는 모든 에이전트를 모니터링에 사용할 수 있습니다.
        * **형식**: **에이전트 주소** 로 설정합니다.
        * **주소**: 온-프레미스 에이전트의 FQDN으로 설정합니다.

* **테스트 그룹**
    * **이름**: 테스트 그룹의 이름을 지정합니다.
    * **원본**: 이전에 만든 엔드포인트에서 선택합니다. Azure 기반 원본 엔드포인트에는 Azure Network Watcher 확장이 설치되어 있어야 합니다. 비 Azure 기반 원본 엔드포인트에는 Azure Log Analytics 에이전트가 설치되어 있어야 합니다. 원본에 대한 에이전트를 설치하려면 [모니터링 에이전트 설치](./connection-monitor-overview.md#install-monitoring-agents)를 참조하세요.
    * **대상**: 앞에서 만든 엔드포인트에서 선택합니다. 대상으로 지정하여 Azure VM 또는 엔드포인트(공용 IP, URL 또는 FQDN)에 대한 연결을 모니터링할 수 있습니다. 단일 테스트 그룹에서 Azure VM, Office 365 URL, Dynamics 365 URL 및 사용자 지정 엔드포인트를 추가할 수 있습니다.
    * **사용하지 않음**: 테스트 그룹이 지정하는 모든 원본 및 대상에 대해 모니터링을 사용하지 않습니다.

* **테스트 구성**
    * **이름**: 테스트 구성의 이름입니다.
    * **TestFrequencySec**: 지정된 프로토콜 및 포트에서 원본에서 대상으로 ping을 수행하는 빈도를 지정합니다. 30초, 1분, 5분, 15분 또는 30분을 선택할 수 있습니다. 원본은 선택한 값을 기준으로 대상에 대한 연결을 테스트합니다. 예를 들어 30초를 선택하는 경우 원본은 30초 동안 한 번 이상 대상에 대한 연결을 검사합니다.
    * **프로토콜**: TCP, ICMP, HTTP 또는 HTTPS를 선택합니다. 프로토콜에 따라 다음과 같은 프로토콜 관련 구성을 선택할 수도 있습니다.
        * **preferHTTPS**: HTTP 대신 HTTPS를 사용할지 여부를 지정합니다.
        * **포트**: 선택한 대상 포트를 지정합니다.
        * **disableTraceRoute**: 원본에서 토폴로지 및 홉 단위 RTT를 검색하지 못하도록 합니다. 이는 TCP 또는 ICMP를 사용하는 테스트 그룹에 적용됩니다.
        * **method**: HTTP 요청 메서드(GET 또는 POST)를 선택합니다. 이는 HTTP를 사용하는 테스트 구성에 적용됩니다.
        * **path**: URL에 추가할 경로 매개 변수를 지정합니다.
        * **validStatusCodes**: 적용 가능한 상태 코드를 선택합니다. 응답 코드가 일치하지 않으면 진단 메시지가 표시됩니다.
        * **requestHeaders**: 대상에 전달되는 사용자 지정 요청 헤더 문자열을 지정합니다.
    * **성공 임계값**: 다음 네트워크 매개 변수에 대한 임계값을 설정합니다.
        * **checksFailedPercent**: 지정된 조건을 사용하여 원본이 대상에 대한 연결을 검사할 때 실패할 수 있는 검사 비율을 설정합니다. TCP 또는 ICMP 프로토콜의 경우 실패한 검사의 비율은 패킷 손실의 비율과 같을 수 있습니다. HTTP 프로토콜의 경우 이 필드는 응답을 받지 않은 HTTP 요청의 비율을 나타냅니다.
        * **roundTripTimeMs**: 테스트 구성에서 원본을 대상에 연결하는 데 걸리는 시간(밀리초)을 설정합니다.

## <a name="scale-limits"></a> 확장 한도

연결 모니터의 규모 한도는 다음과 같습니다.

* 지역별 구독당 최대 연결 모니터 수: 100개
* 연결 모니터당 최대 테스트 그룹 수: 20개
* 연결 모니터당 최대 원본 및 대상 수: 100개
* 연결 모니터당 최대 테스트 구성 수: 20개

## <a name="next-steps"></a>다음 단계

* [모니터링 데이터를 분석하고 경고를 설정하는 방법](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts)을 알아봅니다.
* [네트워크에서 문제를 진단하는 방법](./connection-monitor-overview.md#diagnose-issues-in-your-network)을 알아봅니다.
