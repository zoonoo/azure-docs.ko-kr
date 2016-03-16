<properties 
   pageTitle="PowerShell을 사용하여 클래식 모드에서 인터넷 연결 부하 분산 장치 만들기 시작 | Microsoft Azure"
   description="PowerShell을 사용하여 클래식 모드에서 인터넷 연결 부하 분산 장치를 만드는 방법에 대해 알아봅니다."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carolz"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/21/2016"
   ms.author="joaoma" />

# PowerShell에서 인터넷 연결 부하 분산 장치(클래식) 만들기 시작

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]이 문서에서는 클래식 배포 모델에 대해 설명합니다. 또한 [Azure 리소스 관리자를 사용하여 인터넷 연결 부하 분산 장치를 만드는 방법을 배울 수 있습니다](load-balancer-get-started-internet-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]



## PowerShell을 사용하여 부하 분산 장치 설정

PowerShell을 사용하여 부하 분산 장치를 설정하려면 다음 단계를 수행합니다.

1. Azure PowerShell을 처음 사용하는 경우 [Azure PowerShell을 설치 및 구성하는 방법](powershell-install-configure.md)을 참조하고 지침을 끝까지 따르면서 Azure에 로그인하고 구독을 선택합니다.


2. 가상 컴퓨터를 만든 후 PowerShell cmdlet을 사용하여 동일한 클라우드 서비스 내에서 가상 컴퓨터로 부하 분산 장치를 추가할 수 있습니다.

다음 예제에서는 클라우드 서비스 "webfarm"라고 하는 부하 분산 장치 집합을 클라우드 서비스 "mytestcloud"(또는 myctestcloud.cloudapp.net)에 추가하여, 부하 분산 장치의 끝점을 "web1" 및 "web2"라는 가상 컴퓨터에 추가합니다. 부하 분산 장치는 포트 80에서 트래픽을 받고 TCP를 사용하여 로컬 끝점(이 경우 포트 80)에서 정의된 가상 컴퓨터들 간의 부하를 분산합니다.


### 1단계
첫 번째 VM "web1"을 위한 부하 분산 끝점 만들기

	Get-AzureVM -ServiceName "mytestcloud" -Name "web1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM

### 2단계 

동일한 부하 분산 장치 집합 이름을 사용하여 두 번째 VM "web2"를 위한 다른 끝점 만들기

	Get-AzureVM -ServiceName "mytestcloud" -Name "web2" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM

## 부하 분산 장치에서 가상 컴퓨터 제거

Remove-AzureEndpoint를 사용하여 부하 분산 장치에서 가상 컴퓨터 끝점을 제거할 수 있습니다.

	Get-azureVM -ServiceName mytestcloud  -Name web1 |Remove-AzureEndpoint -Name httpin| Update-AzureVM

## 다음 단계

[내부 부하 분산 장치를 시작](load-balancer-get-started-ilb-classic-ps.md)하고 특정 부하 분산 장치 네트워크 트래픽 동작에 대한 [배포 모드](load-balancer-distribution-mode.md) 유형을 구성할 수도 있습니다.

응용 프로그램이 부하 분산 장치 뒤의 서버에 대한 연결을 유지해야 하는 경우 [부하 분산 장치에 대한 유휴 TCP 시간 제한 설정](load-balancer-tcp-idle-timeout.md)에 대해 자세히 이해할 수 있습니다. Azure 부하 분산 장치를 사용하는 경우 유휴 연결 동작에 대해 알아보는 데 도움이 됩니다.

<!---HONumber=AcomDC_0128_2016-->