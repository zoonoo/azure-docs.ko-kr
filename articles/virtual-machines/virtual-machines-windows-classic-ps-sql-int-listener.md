<properties
	pageTitle="AlwaysOn 가용성 그룹에 대한 ILB 수신기 구성 | Microsoft Azure"
	description="이 자습서에서는 클래식 배포 모델을 사용하여 만든 리소스를 사용하며, ILB(내부 부하 분산 장치)를 사용하여 Azure에서 AlwaysOn 가용성 그룹 수신기를 만듭니다."
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management"/>
<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="02/03/2016"
	ms.author="jroth" />

# Azure에서 AlwaysOn 가용성 그룹에 대한 ILB 수신기 구성

> [AZURE.SELECTOR]
- [내부 수신기](virtual-machines-windows-classic-ps-sql-int-listener.md)
- [외부 수신기](virtual-machines-windows-classic-ps-sql-ext-listener.md)

## 개요

이 항목에서는 **ILB(내부 부하 분산장치)**를 사용하여 AlwaysOn 가용성 그룹에 대해 수신기를 구성하는 방법을 보여줍니다.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]리소스 관리자 모델.


가용성 그룹은 온-프레미스 전용, Azure 전용 또는 하이브리드 구성에 대한 온-프레미스와 Azure 모두에 걸쳐 있는 복제본을 포함할 수 있습니다. Azure 복제본은 동일한 지역 내 또는 여러 Vnet(가상 네트워크)을 사용하 여 여러 지역에 걸쳐 있을 수 있습니다. 다음 단계에서는 [가용성 그룹을 구성](virtual-machines-windows-classic-portal-sql-availability.md)했지만 수신기는 구성하지 않았다고 가정합니다.

## 내부 수신기에 대한 지침 및 제한 사항
ILB를 사용하는 Azure에서는 가용성 그룹 수신기에 다음과 같은 지침이 적용됩니다.

- 가용성 그룹 수신기는 Windows Server 2008 R2, Windows Server 2012 및 Windows Server 2012 R2에서 지원됩니다.

- 수신기는 ILB로 구성되고 ILB는 클라우드 서비스당 하나만 있기 때문에 하나의 내부 가용성 그룹 수신기만 지원됩니다. 그러나 외부 수신기는 여러 개를 만들 수 있습니다. 자세한 내용은 [Azure에서 AlwaysOn 가용성 그룹에 대한 외부 수신기 구성](virtual-machines-windows-classic-ps-sql-ext-listener.md)을 참조하세요.

- 클라우드 서비스의 공용 VIP를 사용하여 외부 수신기도 있는 동일한 클라우드 서비스에서 내부 수신기를 만들 수는 없습니다.

## 수신기의 액세스 가능 여부 확인

[AZURE.INCLUDE [ag-listener-accessibility](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

이 문서에서는 **ILB(내부 부하 분산 장치)**를 사용하는 수신기를 만드는 데 중점을 둡니다. 공용/외부 수신기가 필요한 경우 [외부 수신기](virtual-machines-windows-classic-ps-sql-ext-listener.md) 설정 단계를 제공하는 이 문서의 다른 버전을 참조하세요.

## 직접 서버 반환이 있는 부하 분산 VM 끝점 만들기

ILB의 경우 먼저 내부 부하 분산기를 만들어야 합니다. 이 작업은 아래 스크립트로 수행됩니다.

[AZURE.INCLUDE [load-balanced-endpoints](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. **ILB**에 대해 고정 IP 주소를 할당해야 합니다. 먼저, 다음 명령을 실행하여 현재 VNet 구성의 검사합니다.

		(Get-AzureVNetConfig).XMLConfiguration

1. 복제본을 호스트하는 VM이 포함된 서브넷의 **서브넷** 이름을 적어 둡니다. 이 이름은 스크립트의 **$SubnetName** 매개 변수에서 사용됩니다.

1. 그런 다음 복제본을 호스트하는 VM이 포함된 서브넷의 **VirtualNetworkSite** 이름과 시작 **AddressPrefix**를 적어 둡니다. 두 값을 **Test-AzureStaticVNetIP** 명령에 전달하고 **AvailableAddresses**를 검사하여 사용 가능한 IP 주소를 찾습니다. 예를 들어 VNet 이름이 *MyVNet*이고 서브넷 주소 범위가 *172.16.0.128*에서 시작한다면 다음 명령을 통해 사용 가능한 주소를 나열할 수 있습니다.

		(Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses

1. 사용 가능한 주소 중 하나를 선택하고 다음 스크립트의 **$ILBStaticIP** 매개 변수에 사용합니다.

3. 아래의 PowerShell 스크립트를 텍스트 편집기에 복사하고 사용자 환경에 맞게 변수 값을 설정합니다(일부 매개 변수에 대한 기본값 제공). 선호도 그룹을 사용하는 기존 배포 그룹은 ILB를 추가할 수 없습니다. ILB 요구 사항에 대한 자세한 내용은 [내부 부하 분산 장치](../load-balancer/load-balancer-internal-overview.md)를 참조하세요. 또한 가용성 그룹에 Azure 지역에 걸쳐 있는 경우, 데이터센터에 있는 클라우드 서비스 및 노드에 대해 각각의 데이터센터에서 한 번씩 스크립트를 실행해야 합니다.

		# Define variables
		$ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
		$AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
		$SubnetName = "<MySubnetName>" # subnet name that the replicas use in the VNet
		$ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
		$ILBName = "AGListenerLB" # customize the ILB name or use this default value

		# Create the ILB
		Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP

		# Configure a load balanced endpoint for each node in $AGNodes using ILB
		ForEach ($node in $AGNodes)
		{
			Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM
		}

1. 변수를 설정한 후에는 텍스트 편집기에서 해당 스크립트를 Azure PowerShell 세션에 복사하여 실행합니다. 프롬프트에 >>가 계속 표시되면 Enter를 다시 입력하여 스크립트 실행이 시작되도록 합니다. 참고:

>[AZURE.NOTE] Azure 클래식 포털은 현재 내부 부하 분산 장치를 지원하지 않으므로 Azure 클래식 포털에서 ILB나 끝점이 표시되지 않습니다. 그러나 포털에서 부하 분산 장치를 실행하는 경우 **Get-AzureEndpoint**에서 내부 IP주소가 반환됩니다. 그렇지 않은 경우 null을 반환합니다.

## 필요한 경우 KB2854082가 설치되었는지 확인합니다.

[AZURE.INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## 가용성 그룹 노드에서 방화벽 포트 열기

[AZURE.INCLUDE [방화벽](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## 가용성 그룹 수신기 만들기

[AZURE.INCLUDE [방화벽](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. ILB의 경우 앞서 만든 ILB(내부 부하 분산 장치)의 IP 주소를 사용해야 합니다. 다음 스크립트를 사용하여 PowerShell에서 이 IP 주소를 가져옵니다.

		# Define variables
		$ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
		(Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

1. VM 중 하나에서 아래의 PowerShell 스크립트를 텍스트 편집기에 복사하 고 앞에서 기록한 값으로 변수를 설정합니다.

		# Define variables
		$ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
		$IPResourceName = "<IPResourceName>" # the IP Address resource name
		$ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB)

		Import-Module FailoverClusters

		# If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.

		# Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
		# cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

1. 변수를 설정한 후에는 앞으로 온 Windows PowerShell 창을 열고 텍스트 편집기의 스크립트를 복사하여 Azure PowerShell 세션에 붙여넣어 실행합니다. 프롬프트에 >>가 계속 표시되면 Enter를 다시 입력하여 스크립트 실행이 시작되도록 합니다.

2. 각 VM에서 이 작업을 반복합니다. 이 스크립트는 클라우드 서비스의 IP 주소로 IP 주소 리소스를 구성하고 프로프 포트 등의 다른 매개 변수를 설정합니다. IP 주소 리소스를 온라인으로 불러올 때 이 자습서의 앞 부분에서 부하 분산된 끝점으로부터 프로브 포트에 대한 폴링에 응답할 수 있습니다.

## 수신기를 온라인 상태로 만들기

[AZURE.INCLUDE [Bring-Listener-Online](../../includes/virtual-machines-ag-listener-bring-online.md)]

## 추가 작업 항목

[AZURE.INCLUDE [Follow-up](../../includes/virtual-machines-ag-listener-follow-up.md)]

## (동일한 VNet 내)가용성 그룹 수신기 테스트

[AZURE.INCLUDE [Test-Listener-Within-VNET](../../includes/virtual-machines-ag-listener-test.md)]

## 다음 단계

[AZURE.INCLUDE [Listener-Next-Steps](../../includes/virtual-machines-ag-listener-next-steps.md)]

<!---HONumber=AcomDC_0323_2016-->