<properties 
   authors="danielceckert" 
   documentationCenter="dev-center-name" 
   editor=""
   manager="jefco" 
   pageTitle="관리: 부하 분산 장치 유휴 시간 제한" 
   description="Azure 부하 분산 장치 유휴 시간 제한 관리 기능" 
   services="virtual-network" 
   />

<tags
   ms.author="danecke"
   ms.date="05/27/2015"
   ms.devlang="na"
   ms.service="virtual-network"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   />
   
# 가상 네트워크 관리: 부하 분산 장치 TCP 유휴 시간 제한

개발자는 **TCP 유휴 시간 제한**을 사용하여 Azure 부하 분산 장치와 관련된 클라이언트-서버 세션 중의 비활성 시간에 대해 보장된 임계값을 지정할 수 있습니다. TCP 유휴 시간 제한 값이 4분(Azure 부하 분산 장치의 기본값)인 경우 Azure 부하 분산 장치를 사용하는 클라이언트-서버 세션 중에 비활성 시간이 4분 넘게 지속되면 연결이 닫힙니다.

클라이언트-서버 연결이 닫히면 클라이언트 응용 프로그램에는 “기본 연결이 닫혔습니다. 활성 상태로 유지될 것으로 예상된 연결이 서버에서 닫혔습니다.”

[TCP 연결 유지](http://tools.ietf.org/html/rfc1122#page-101)는 오랜 시간 연결을 유지하는 데 일반적으로 사용되는 방식입니다[(MSDN 예제)](http://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). 이러한 방식을 사용하지 않는 경우 연결이 비활성화됩니다. TCP 연결 유지를 사용하는 경우 클라이언트에서 대개 서버의 유휴 시간 제한 임계값보다 짧은 빈도로 단순 패킷을 주기적으로 전송합니다. 서버는 다른 활동이 수행되지 않더라도 이러한 전송이 계속되면 연결을 활성 상태라고 간주하기 때문에 유휴 시간 제한 값에 도달하지 않으므로 오랫동안 연결을 유지할 수 있습니다.

TCP 연결 유지는 효율적으로 작동하기는 하지만 모바일 응용 프로그램은 모바일 장치에서 제한적인 전원 리소스를 사용하기 때문에 일반적으로 TCP 연결 유지를 사용하지 않습니다. 모바일 응용 프로그램이 TCP 연결 유지를 사용하는 경우 네트워크용으로 전원을 계속 사용하므로 장치 배터리가 더 빨리 소진됩니다.

모바일 장치 시나리오를 지원하기 위해 Azure 부하 분산 장치는 구성 가능한 TCP 유휴 시간 제한을 지원합니다. 개발자는 인바운드 연결의 TCP 유휴 시간 제한을 4분~30분 사이로 설정할 수 있습니다. 아웃바운드 연결에는 구성 가능한 TCP 유휴 시간 제한을 적용할 수 없습니다. 그러므로 클라이언트는 비활성 시간이 길더라도 서버와의 세션을 훨씬 오랫동안 유지할 수 있습니다. 모바일 장치의 응용 프로그램도 TCP 연결 유지 기술을 사용하여 비활성 상태인 시간이 30분보다 긴 연결을 유지할 수 있습니다. 이처럼 TCP 유휴 시간 제한이 길면 응용 프로그램이 TCP 연결 유지 요청을 보내는 빈도가 이전보다 훨씬 낮아지므로 모바일 장치 전원 리소스에 대한 부담이 크게 줄어듭니다.

## 구현

다음에 대해 TCP 유휴 시간 제한을 구성할 수 있습니다.

* [인스턴스 수준 공용 IP](http://msdn.microsoft.com/library/azure/dn690118.aspx)
* [부하 분산된 끝점 집합](http://msdn.microsoft.com/library/azure/dn655055.aspx)
* [가상 컴퓨터 끝점](virtual-machines-set-up-endpoints.md)
* [웹 역할](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)
* [작업자 역할](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)

## 다음 단계
* TBD

## PowerShell 예제
최상의 결과를 얻으려면 [최신 Azure PowerShell 릴리스](https://github.com/Azure/azure-sdk-tools/releases)를 다운로드하세요.

### 인스턴스 수준 공용 IP의 TCP 유휴 시간 제한을 15분으로 구성

    Set-AzurePublicIP –PublicIPName webip –VM MyVM -IdleTimeoutInMinutes 15

IdleTimeoutInMinutes는 선택 사항이며 설정하지 않으면 기본 시간 제한은 4분으로 설정됩니다. 이제는 해당 값을 4분~30분 사이로 설정할 수 있습니다.

### 가상 컴퓨터에서 Azure 끝점을 만들 때 유휴 시간 제한 설정

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM

### 유휴 시간 제한 구성 검색

    PS C:> Get-AzureVM –ServiceName “MyService” –Name “MyVM” | Get-AzureEndpoint
    
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
    
### 부하 분산된 끝점 집합에 대한 TCP 시간 제한 설정

부하 분산된 끝점 집합에 끝점이 포함되어 있으면 부하 분산된 끝점 집합에 대해 TCP 시간 제한을 설정해야 합니다.

    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15

## 클라우드 서비스 예제

Azure SDK for .NET을 사용하여 클라우드 서비스를 업데이트할 수 있습니다.

.csdef에서 클라우드 서비스용 끝점 설정을 지정합니다. 클라우드 서비스 배포의 TCP 시간 제한을 업데이트하려면 배포를 업그레이드해야 합니다. 단, 공용 IP에 대해서만 TCP 시간 제한을 지정하는 경우는 예외입니다. 공용 IP 설정은 .cscfg에 포함되어 있으므로 배포 업데이트 및 업그레이드를 통해 업데이트할 수 있습니다.

아래에 .csdef에서 끝점 설정을 변경하는 예제가 나와 있습니다.

    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
      </Endpoints>
    </WorkerRole>The .cscfg changes for the timeout setting on Public IPs are:
    
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

개발자는 서비스 관리 API를 사용하여 부하 분산 장치 배포를 구성할 수 있습니다. 이때 x-ms-version 헤더는 버전 2014-06-01 이상으로 설정해야 합니다.

### 배포의 모든 가상 컴퓨터에서 지정한 부하 분산된 입력 끝점의 구성 업데이트

#### 요청

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>

LoadBalancerDistribution의 값은 2개 튜플 선호도의 경우 sourceIP로, 3개 튜플 선호도의 경우에는 sourceIPProtocol로 설정할 수 있으며 선호도가 없는 경우에는 none으로 설정할 수 있습니다(기본값인 5개 튜플이 사용됨).

#### 응답

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName>endpoint-set-name</LoadBalancedEndpointSetName>
        <LocalPort>local-port-number</LocalPort>
        <Port>external-port-number</Port>
        <LoadBalancerProbe>
          <Path>path-of-probe</Path>
          <Port>port-assigned-to-probe</Port>
          <Protocol>probe-protocol</Protocol>
          <IntervalInSeconds>interval-of-probe</IntervalInSeconds>
          <TimeoutInSeconds>timeout-for-probe</TimeoutInSeconds>
        </LoadBalancerProbe>
        <LoadBalancerName>name-of-internal-loadbalancer</LoadBalancerName>
        <Protocol>endpoint-protocol</Protocol>
        <IdleTimeoutInMinutes>15</IdleTimeoutInMinutes>
        <EnableDirectServerReturn>enable-direct-server-return</EnableDirectServerReturn>
        <EndpointACL>
          <Rules>
            <Rule>
              <Order>priority-of-the-rule</Order>
              <Action>permit-rule</Action>
              <RemoteSubnet>subnet-of-the-rule</RemoteSubnet>
              <Description>description-of-the-rule</Description>
            </Rule>
          </Rules>
        </EndpointACL>
      </InputEndpoint>
    </LoadBalancedEndpointList>

<!---HONumber=58-->