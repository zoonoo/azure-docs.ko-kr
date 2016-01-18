<properties
   pageTitle="관리: 부하 분산 장치 배포 모드(원본 IP 선호도)"
   description="Azure 부하 분산 장치 배포 모드용 관리 기능"
   services="virtual-network"
   documentationCenter=""
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   />

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="telmos"
   />

# 가상 네트워크 관리: 부하 분산 장치 배포 모드(원본 IP 선호도)
**원본 IP 선호도**는 **세션 선호도** 또는 **클라이언트 IP 선호도**라고도 하며, 각 클라이언트 연결을 여러 Azure 호스팅 서버에 동적으로 분산시키는 기본 부하 분산 장치 동작을 사용하는 대신 단일 클라이언트의 연결을 단일 Azure 호스팅 서버에 연결하는 Azure 부하 분산 장치 배포 모드입니다.

원본 IP 선호도를 사용하는 경우 Azure 부하 분산 장치가 2개 튜플 조합(원본 IP, 대상 IP) 또는 3개 튜플 조합(원본 IP, 대상 IP, 프로토콜)을 사용하도록 구성하여, 사용 가능한 Azure 호스팅 서버 풀에 트래픽을 매핑할 수 있습니다. 원본 IP 선호도를 사용할 때는 같은 클라이언트 컴퓨터에서 시작된 연결이 단일 DIP 끝점(단일 Azure 호스팅 서버)에서 처리됩니다.

## 서비스 원점

원본 IP 선호도는 이전 [Azure 부하 분산 장치 및 RD 게이트웨이(DOC) 간의 비호환성](http://go.microsoft.com/fwlink/p/?LinkId=517389) 문제를 해결했습니다.

## 구현

다음에 대해 원본 IP 선호도를 구성할 수 있습니다.

* [가상 컴퓨터 끝점](../virtual-machines/virtual-machines-set-up-endpoints.md)
* [부하 분산된 끝점 집합](../load-balancer/load-balancer-overview.md)
* [웹 역할](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)
* [작업자 역할](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)

## 시나리오
1. 단일 클라우드 서비스를 사용하는 원격 데스크톱 게이트웨이 클러스터
2. 미디어 업로드(예: 데이터용 UDP, 컨트롤용 TCP)
  * 클라이언트가 부하 분산된 Azure 호스팅 공용 IP 주소에 대한 TCP 세션을 시작합니다.
  * 클라이언트 요청이 부하 분산 장치에 의해 DIP로 전송되며 이 채널은 연결 상태를 모니터링하기 위해 활성 상태로 유지됩니다.
  * 클라이언트가 동일한 부하 분산된 Azure 호스팅 공용 IP 주소에 대한 UDP 세션을 시작합니다.
  * Azure 부하 분산 장치가 같은 DIP 끝점에 대한 요청을 TCP 연결로 전송합니다.
  * 클라이언트가 UDP 처리량이 높은 미디어를 업로드하는 동시에 안정성을 유지하기 위해 TCP를 통해 제어 채널을 유지 관리합니다.

## 주의 사항
* 가상 컴퓨터를 추가하거나 제거하는 등 부하 분산된 집합을 변경하는 경우에는 클라이언트 채널 배포가 다시 계산됩니다. 따라서 원래 사용하던 것과는 다른 서버가 기존 클라이언트로부터의 새 연결을 처리할 수 있습니다.
* 원본 IP 선호도를 사용하는 경우 Azure 호스팅 서버 전체에서 트래픽이 불규칙하게 배포될 수 있습니다.
* 프록시를 통해 트래픽을 라우팅하는 클라이언트는 Azure 부하 분산 장치에서 단일 클라이언트로 표시될 수 있습니다.

## PowerShell 예제
최상의 결과를 얻으려면 [최신 Azure PowerShell 릴리스](https://github.com/Azure/azure-sdk-tools/releases)를 다운로드하세요.

### 가상 컴퓨터에 Azure 끝점을 추가하고 부하 분산 장치 배포 모드 설정

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution “sourceIP”| Update-AzureVM  

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -LoadBalancerDistribution "sourceIP"| Update-AzureVM  

LoadBalancerDistribution의 값은 2개 튜플 선호도의 경우 sourceIP로, 3개 튜플 선호도의 경우에는 sourceIPProtocol로 설정할 수 있으며 선호도가 없는 경우에는 none으로 설정할 수 있습니다(기본값인 5개 튜플이 사용됨).

### 끝점 부하 분산 장치 배포 모드 구성 검색
    PS C:\> Get-AzureVM –ServiceName "mySvc" -Name "MyVM1" | Get-AzureEndpoint

    VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
    LBSetName : MyLoadBalancedSet
    LocalPort : 80
    Name : HTTP
    Port : 80
    Protocol : tcp
    Vip : 65.52.xxx.xxx
    ProbePath :
    ProbePort : 80
    ProbeProtocol : tcp
    ProbeIntervalInSeconds : 15
    ProbeTimeoutInSeconds : 31
    EnableDirectServerReturn : False
    Acl : {}
    InternalLoadBalancerName :
    IdleTimeoutInMinutes : 15
    LoadBalancerDistribution : sourceIP

LoadBalancerDistribution 요소가 없으면 Azure 부하 분산 장치는 기본값인 5개 튜플 알고리즘을 사용합니다.

### 부하 분산된 끝점 집합에 대한 배포 모드 설정

    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution "sourceIP"

    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 â€“LoadBalancerDistribution "sourceIP"

부하 분산된 끝점 집합에 끝점이 포함되어 있으면 부하 분산된 끝점 집합에 대해 배포 모드를 설정해야 합니다.

## 클라우드 서비스 예제

Azure SDK for .NET을 사용하여 클라우드 서비스를 업데이트할 수 있습니다.

.csdef에서 클라우드 서비스용 끝점 설정을 지정합니다. 클라우드 서비스 배포의 부하 분산 장치 배포 모드를 업데이트하려면 배포를 업그레이드해야 합니다.

아래에 .csdef에서 끝점 설정을 변경하는 예제가 나와 있습니다.

    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
      </Endpoints>
    </WorkerRole>
    <NetworkConfiguration>
      <VirtualNetworkSite name="VNet"/>
      <AddressAssignments>
        <InstanceAddress roleName="VMRolePersisted">
          <PublicIPs>
            <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
          </PublicIPs>
        </InstanceAddress>
      </AddressAssignments>
    </NetworkConfiguration>

## API 예제

개발자는 서비스 관리 API를 사용하여 부하 분산 장치 배포를 구성할 수 있습니다. 이때 x-ms-version 헤더는 버전 2014-09-01 이상으로 설정해야 합니다.

### 배포에서 지정한 부하 분산된 집합의 구성 업데이트

#### 요청

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet

    x-ms-version: 2014-09-01

    Content-Type: application/xml

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName>
        <LocalPort> local-port-number </LocalPort>
        <Port> external-port-number </Port>
        <LoadBalancerProbe>
          <Port> port-assigned-to-probe </Port>
          <Protocol> probe-protocol </Protocol>
          <IntervalInSeconds> interval-of-probe </IntervalInSeconds>
          <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds>
        </LoadBalancerProbe>
        <Protocol> endpoint-protocol </Protocol>
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn>
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes>
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution>
      </InputEndpoint>
    </LoadBalancedEndpointList>

LoadBalancerDistribution의 값은 2개 튜플 선호도의 경우 sourceIP로, 3개 튜플 선호도의 경우에는 sourceIPProtocol로 설정할 수 있으며 선호도가 없는 경우에는 none으로 설정할 수 있습니다(기본값인 5개 튜플이 사용됨).

#### 응답

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

<!---HONumber=AcomDC_0107_2016-->