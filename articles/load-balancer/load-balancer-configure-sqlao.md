<properties 
   pageTitle="SQL Always On에 대해 부하 분산 장치 구성 | Microsoft Azure"
   description="SQL Always On에서 작동하도록 부하 분산 장치를 구성하며, PowerShell을 활용하여 SQL 구현에 대한 부하 분산 장치를 만드는 방법입니다."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/01/2015"
   ms.author="joaoma" />

# SQL Always On에 대해 부하 분산 장치 구성

이제 ILB에서 SQL Server AlwaysOn 가용성 그룹을 실행할 수 있습니다. 가용성 그룹은 고가용성 및 재해 복구를 위한 SQL Server의 주력 솔루션입니다. 가용성 그룹 수신기를 통해 클라이언트 응용 프로그램은 구성의 복제본 수에 관계없이 주 복제본에 매끄럽게 연결할 수 있습니다.

수신기(DNS) 이름이 부하 분산 IP 주소에 매핑되고, Azure 부하 분산 장치가 들어오는 트래픽을 복제본 세트의 주 서버로만 보냅니다.


SQL Server AlwaysOn(수신기) 끝점에 대해 ILB 지원을 사용할 수 있습니다. 이제 수신기의 접근성을 제어할 수 있으며, VNet(가상 네트워크)의 특정 서브넷에서 부하 분산 IP 주소를 선택할 수 있습니다.

수신기에서 ILB를 사용하면 다음 항목만 SQL Server 끝점(예: Server=tcp:ListenerName,1433;Database=DatabaseName)에 액세스할 수 있습니다.

동일한 가상 네트워크 서비스에 있는 서비스 및 VM, 연결된 온-프레미스 네트워크 서비스의 VM, 상호 연결된 VNet의 VM

![ILB\_SQLAO\_NewPic](./media/load-balancer-configure-sqlao/sqlao1.jpg)


내부 부하 분산 장치는 PowerShell을 통해서만 구성할 수 있습니다.


## 서비스에 내부 부하 분산 장치 추가 

### 1단계.

다음 예제에서는 'Subnet-1'이라는 서브넷을 포함하는 가상 네트워크를 구성합니다.

	Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc

2단계.

## 각 VM에서 ILB에 대한 부하 분산 끝점 추가

	Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 –
	DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

 	Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 –DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM


## 참고 항목

[인터넷 연결 부하 분산 장치 구성 시작](load-balancer-internet-getstarted.md)

[내부 부하 분산 장치 구성 시작](load-balancer-internal-getstarted.md)

[부하 분산 장치 배포 모드 구성](load-balancer-distribution-mode.md)

[부하 분산 장치에 대한 유휴 TCP 시간 제한 설정 구성](load-balancer-tcp-idle-timeout.md)
 

<!---HONumber=July15_HO5-->