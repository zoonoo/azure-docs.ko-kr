<properties
   pageTitle="부하 분산 장치 배포 모드 구성 | Microsoft Azure"
   description="원본 IP 선호도를 지원하도록 Azure 부하 분산 장치 배포 모드를 구성하는 방법입니다."
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/05/2016"
   ms.author="sewhee" />


# 부하 분산 장치용 배포 모드(원본 IP 선호도)

## 소스 IP 선호도 모드
원본 IP 선호도(세션 선호도 또는 클라이언트 IP 선호도라고도 함)라는 새로운 배포 모드가 도입되었습니다. 2개 튜플(원본 IP, 대상 IP) 또는 3개 튜플(원본 IP, 대상 IP, 프로토콜)을 사용하도록 Azure 부하 분산 장치를 구성하여 사용 가능한 서버에 트래픽을 매핑할 수 있습니다. 원본 IP 선호도를 사용하면 동일한 클라이언트 컴퓨터에서 시작된 연결이 동일한 DIP 끝점으로 이어집니다.

![세션 선호도](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

원본 IP 선호도는 Azure 부하 분산 장치와 RD 게이트웨이 간의 비호환성을 해결합니다. 이제 단일 클라우드 서비스에서 RD 게이트웨이 팜을 빌드할 수 있습니다. 또 다른 사용 시나리오로는 실제 데이터 업로드는 UDP를 통해 수행되지만 제어 영역은 TCP를 통해 구현되는 미디어 업로드가 있습니다.

- 클라이언트는 먼저 부하 분산 공용 주소에 대한 TCP 세션을 시작하고 특정 DIP로 전송됩니다. 이 채널은 연결 상태를 모니터링하기 위해 활성 상태로 유지됩니다.
- 동일한 클라이언트 컴퓨터에서 동일한 부하 분산 공용 끝점으로 새 UDP 세션이 시작됩니다. 여기서 예상은 TCP를 통해 제어 채널을 유지하는 동시에 높은 처리량으로 미디어 업로드를 실행할 수 있도록 이 연결도 이전 TCP 연결과 동일한 DIP 끝점으로 전송되는 것입니다.

부하 분산 집합이 변경되면(가상 컴퓨터 추가 또는 제거) 클라이언트 요청 배포가 다시 계산됩니다. 기존 클라이언트 세션의 새 연결이 동일한 서버로 수행된다고 확신할 수 없습니다. 또한 원본 IP 선호도를 사용하는 경우 트래픽이 불규칙하게 배포될 수 있습니다. 프록시 뒤에서 실행되는 클라이언트는 하나의 고유한 클라이언트 응용 프로그램으로 표시될 수 있습니다.

## 해시 기반 배포 모드

사용되는 배포 알고리즘은 트래픽을 사용 가능한 서버에 매핑하는 5개 튜플(원본 IP, 원본 포트, 대상 IP, 대상 포트, 프로토콜 종류) 해시입니다. 전송 세션 내에서만 연결이 유지됩니다. 동일한 TCP 또는 UDP 세션의 패킷은 부하 분산 끝점 뒤의 동일한 DIP(데이터 센터 IP) 인스턴스로 전송됩니다. 클라이언트가 연결을 닫았다가 다시 열거나 동일한 원본 IP에서 새 세션을 시작하는 경우 원본 포트가 변경되어 트래픽이 다른 DIP 끝점으로 이동합니다.

![해시 기반 부하 분산 장치](./media/load-balancer-distribution-mode/load-balancer-distribution.png)


## 부하 분산 장치에 대한 원본 IP 선호도 설정 구성

가상 컴퓨터의 경우 PowerShell을 사용하여 시간 제한 설정을 변경할 수 있습니다.

가상 컴퓨터에 Azure 끝점을 추가하고 부하 분산 장치 배포 모드 설정

    Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM

>[AZURE.NOTE] LoadBalancerDistribution은 2개 튜플(원본 IP, 대상 IP) 부하 분산의 경우 sourceIP로, 3개 튜플(원본 IP, 대상 IP, 프로토콜) 부하 분산의 경우 sourceIPProtocol로 설정할 수 있으며 5개 튜플 부하 분산의 기본 동작을 원하는 경우 none으로 설정할 수 있습니다.


끝점 부하 분산 장치 배포 모드 구성 검색

    PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

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

부하 분산 끝점 집합에 끝점이 포함되어 있으면 부하 분산 끝점 집합에 대해 배포 모드를 설정해야 합니다.

    Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocol TCP -ProbePort 8080 –LoadBalancerDistribution sourceIP

### 배포 모드를 변경하는 클라우드 서비스 구성

Azure SDK for .NET 2.5(11월 출시 예정)를 활용하여 .csdef에서 지정된 클라우드 서비스에 대한 클라우드 서비스 끝점 설정을 업데이트할 수 있습니다. 클라우드 서비스 배포의 부하 분산 장치 배포 모드를 업데이트하려면 배포를 업그레이드해야 합니다. 아래에 .csdef에서 끝점 설정을 변경하는 예제가 나와 있습니다.

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

service management API를 사용하여 부하 분산 장치 배포를 구성할 수 있습니다. 추가하는 `x-ms-version` 헤더는 버전 `2014-09-01` 이상으로 설정해야 합니다.

배포에서 지정한 부하 분산된 집합의 구성 업데이트

요청 예제

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

    Response

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## 다음 단계

[내부 부하 분산 장치 개요](load-balancer-internal-overview.md)

[인터넷 연결 부하 분산 장치 구성 시작](load-balancer-get-started-internet-arm-ps.md)

[부하 분산 장치에 대한 유휴 TCP 시간 제한 설정 구성](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0831_2016-->