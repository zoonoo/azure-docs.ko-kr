
<properties
   pageTitle="인터넷 연결 부하 분산 장치 시작 | Microsoft Azure"
   description="가상 컴퓨터 또는 클라우드 서비스에 대한 첫 번째 인터넷 연결 부하 분산 장치 집합을 설정합니다."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/01/2015"
   ms.author="joaoma" />

# 인터넷 연결 부하 분산 장치 구성 시작

Microsoft Azure의 부하 분산 서비스는 모든 테넌트 유형(PaaS 또는 IaaS) 및 모든 OS 버전(지원되는 Windows 또는 Linux 기반 OS)에서 작동합니다.


## 가상 컴퓨터에 대한 인터넷 연결 부하 분산 장치 설정

인터넷에서 전송된 네트워크 트래픽을 클라우드 서비스의 가상 컴퓨터 간에 부하 분산하려면 부하 분산 집합을 만들어야 합니다. 이 절차에서는 가상 컴퓨터를 이미 만들었으며 모두 동일한 클라우드 서비스 내에 있다고 가정합니다.

가상 컴퓨터에 대한 부하 분산 장치 집합을 구성하려면 아래 단계를 따르세요.

1. Azure 관리 포털에서 가상 컴퓨터를 클릭한 다음 부하 분산 집합에서 가상 컴퓨터의 이름을 클릭합니다.
2.	끝점을 클릭한 후 Add를 클릭합니다.

4.	가상 컴퓨터에 끝점 추가 페이지에서 오른쪽 화살표를 클릭합니다.

4.	Specify the details of the endpoint 페이지에서 다음을 수행합니다.
	- 이름에 끝점 이름을 입력하거나 일반 프로토콜용으로 사전 정의된 끝점 목록에서 선택합니다.
	-  Protocol에서 끝점 유형에 따라 필요한 프로토콜을 TCP 또는 UDP 중에 선택합니다.
 	-  Public Port 및 Private Port에 가상 컴퓨터에서 사용할 포트 번호를 필요에 따라 입력합니다. 가상 컴퓨터에서 개인 포트와 방화벽 규칙을 사용하여 응용 프로그램에 맞추어 트래픽을 리디렉션할 수 있습니다. 개인 포트는 공용 포트와 같게 설정해도 됩니다. 예를 들어 웹(HTTP) 트래픽의 끝점의 경우 공용 포트와 개인 포트 모두에 포트 80을 할당할 수 있습니다.

5.	Create a load-balanced set를 선택한 후 오른쪽 화살표를 클릭합니다.

6.	Configure the load-balanced set 페이지에서 부하 분산 집합의 이름을 입력한 후 Azure 부하 분산 장치 프로브 동작의 값을 할당합니다. 부하 분산 장치는 프로브를 사용하여 부하 분산 집합의 가상 컴퓨터가 들어오는 트래픽을 수신할 수 있는지 여부를 결정합니다.

7.	확인 표시를 클릭하여 부하 분산 끝점을 만듭니다. 가상 컴퓨터의 끝점 페이지에 있는 Load-balanced set name 열에 예가 표시됩니다.

8.	관리 포털에서 가상 컴퓨터를 클릭하고 부하 분산 집합에서 추가 가상 컴퓨터의 이름을 클릭한 다음 끝점, 추가를 차례로 클릭합니다.

9.	Add an endpoint to a virtual machine 페이지에서 Add endpoint to an existing load-balanced set를 클릭하고 부하 분산 집합 이름을 선택한 후 오른쪽 화살표를 클릭합니다.

10.	Specify the details of the endpoint 페이지에서 끝점 이름을 입력한 후 확인 표시를 클릭합니다. 부하 분산 집합의 추가 가상 컴퓨터에 대해 8-10단계를 반복합니다.

다음과 같은 Windows PowerShell cmdlet을 통해 끝점을 구성할 수도 있습니다.

- Add-AzureEndpoint

[Add-AzureEndpoint](https://msdn.microsoft.com/library/windowsazure/dn495300)

- Get-AzureEndpoint

[Get-AzureEndpoint](https://msdn.microsoft.com/library/windowsazure/dn495158)

- Remove-AzureEndpoint

[Remove-AzureEndpoint](https://msdn.microsoft.com/library/windowsazure/dn495161)


## 클라우드 서비스에 대한 인터넷 연결 부하 분산 장치 설정


클라우드 서비스는 자동으로 부하 분산 장치를 사용하여 구성되며, 서비스 모델을 통해 사용자 지정할 수 있습니다.

Azure SDK for .NET 2.5를 사용하여 클라우드 서비스를 업데이트할 수 있습니다. 클라우드 서비스에 대한 끝점 설정은 [service definition](https://msdn.microsoft.com/library/azure/gg557553.aspx).csdef에서 수행됩니다.

다음 예제에서는 클라우드 배포에 대한 servicedefinition.csdef 파일이 구성되는 방식을 보여 줍니다.

클라우드 배포에 의해 생성된 .csdef 파일의 코드 조각을 검사하면 포트 10000, 10001 및 10002에서 HTTP 포트를 사용하도록 구성된 외부 끝점을 확인할 수 있습니다.


	<ServiceDefinition name=“Tenant“>
   	<WorkerRole name=“FERole” vmsize=“Small“>
    <Endpoints>
        <InputEndpoint name=“FE_External_Http” protocol=“http” port=“10000“ />
        <InputEndpoint name=“FE_External_Tcp“  protocol=“tcp“  port=“10001“ />
        <InputEndpoint name=“FE_External_Udp“  protocol=“udp“  port=“10002“ />

        <InputEndpointname=“HTTP_Probe” protocol=“http” port=“80” loadBalancerProbe=“MyProbe“ />

        <InstanceInputEndpoint name=“InstanceEP” protocol=“tcp” localPort=“80“>
           <AllocatePublicPortFrom>
              <FixedPortRange min=“10110” max=“10120“  />
           </AllocatePublicPortFrom>
        </InstanceInputEndpoint>
        <InternalEndpoint name=“FE_InternalEP_Tcp” protocol=“tcp“ />
    </Endpoints>
  	</WorkerRole>
	</ServiceDefinition>




### 클라우드 서비스에 대한 부하 분산 장치 상태 확인


다음은 상태 프로브의 예입니다.

	 	<LoadBalancerProbes>
    	<LoadBalancerProbe name=“MyProbe” protocol=“http” path=“Probe.aspx” intervalInSeconds=“5” timeoutInSeconds=“100“ />
 	 	</LoadBalancerProbes>

부하 분산 장치는 끝점의 정보와 프로브의 정보를 결합하여 서비스 상태를 쿼리하는 데 사용되는 VM}:80/Probe.aspx의 http://{DIP 형식 URL을 만듭니다.

서비스에서 동일한 IP가 주기적으로 액세스하는 것을 확인합니다. 이는 VM이 실행되는 노드의 호스트에서 들어오는 상태 프로브 요청입니다. 부하 분산 장치에서 서비스가 정상이라고 가정하려면 서비스가 HTTP 200 상태 코드로 응답해야 합니다. 다른 HTTP 상태 코드(예: 503)가 반환되면 바로 VM이 순환에서 제거됩니다.

또한 프로브 정의는 프로브 빈도를 제어합니다. 위 사례에서 부하 분산 장치는 5초마다 끝점을 검색합니다. 10초(2개 프로브 간격) 동안 긍정적인 응답이 수신되지 않으면 프로브가 다운된 것으로 간주되며 VM이 순환에서 제거됩니다. 마찬가지로, 서비스가 순환에서 제거되고 긍정적인 응답이 수신되면 서비스가 즉시 순환에 다시 배치됩니다. 서비스가 정상/비정상 상태 간에 변동되는 경우 서비스가 많은 프로브 동안 정상 상태로 유지될 때까지 부하 분산 장치에서 서비스를 다시 순환에 배치하는 것을 지연할 수 있습니다.

자세한 내용은 [상태 프로브](https://msdn.microsoft.com/library/azure/jj151530.aspx)에 대한 서비스 정의 스키마를 참조하세요.

## 다음 단계

[내부 부하 분산 장치 구성 시작](load-balancer-internal-getstarted.md)

[부하 분산 장치 배포 모드 구성](load-balancer-distribution-mode.md)

[부하 분산 장치에 대한 유휴 TCP 시간 제한 설정 구성](load-balancer-tcp-idle-timeout.md)
 

<!---HONumber=July15_HO2-->