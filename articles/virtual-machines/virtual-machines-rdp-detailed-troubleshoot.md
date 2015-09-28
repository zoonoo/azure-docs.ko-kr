<properties
	pageTitle="원격 데스크톱 상세 문제 해결 | Microsoft Azure"
	description="Windows를 실행하는 Azure 가상 컴퓨터에 RDP 연결에 대한 상세 문제 해결 단계."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2015"
	ms.author="dkshir"/>

# Windows 기반 Azure 가상 컴퓨터에 원격 데스크톱 연결 상세 문제 해결

이 문서에서는 복잡한 원격 데스크톱 오류에 대한 상세 문제 해결 단계를 제공합니다.

> [AZURE.IMPORTANT]일반적인 원격 데스크톱 오류를 제거하려면 계속하기 전에 [원격 데스크톱에 대한 기본적인 문제 해결](virtual-machines-troubleshoot-remote-desktop-connections.md)을 읽어야 합니다.

## Azure 고객 지원 서비스에 문의

이 문서의 어느 지점에서 도움이 필요한 경우 [MSDN Azure 및 스택 오버플로 포럼](http://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다.

또는 Azure 기술 지원 인시던트를 제출할 수도 있습니다. [Azure 지원 사이트](http://azure.microsoft.com/support/options/)로 가서 **지원 받기**를 클릭합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](http://azure.microsoft.com/support/faq/)를 읽으세요.


## 일반 원격 데스크톱 오류 메시지

원격 데스크톱 연결 메시지 창에서 오류 메시지 _이러한 이유 중 하나로 인해 원격 데스크톱을 원격 컴퓨터에 연결할 수 없습니다_가 가끔 나타날 수 있습니다.

이 오류는 원격 데스크톱 클라이언트가 가상 컴퓨터의 원격 데스크톱 서비스에 연결할 수 없는 경우에 발생합니다. 이 오류에 대한 다양한 원인이 있을 수 있습니다.

다음은 관련된 구성 요소 집합입니다.

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_0.png)

단계별 문제 해결 프로세스를 살펴보기 전에 마지막으로 원격 데스크톱 연결을 성공적으로 설정한 이후로 변경된 사항을 마음속으로 생각해보고 이를 바탕으로 문제를 해결하면 유용합니다. 예:

- 원격 데스크톱 연결을 설정한 후 가상 컴퓨터를 포함하는 가상 컴퓨터 또는 클라우드 서비스의 공용 IP 주소(가상 IP 주소 [VIP]라고도 함)를 변경한 경우 DNS 클라이언트 캐시에는 DNS 이름 및 *이전 IP 주소*에 대한 항목이 있을 수 있습니다. DNS 클라이언트 캐시를 플러시하고 다시 시도하세요. 또는 새 VIP를 사용하여 연결을 설정해 보세요.
- Azure 포털 또는 Azure Preview 포털을 사용하다가 응용 프로그램을 사용하여 원격 데스크톱 연결을 관리하는 경우 응용 프로그램 구성에 원격 데스크톱 트래픽에 대한 무작위 지정 TCP 포트가 포함되어 있는지 확인하세요.

다음 섹션에서는 단계별로 이 문제의 다양한 근본 원인을 별도로 확인하고 솔루션 및 해결 방법을 제공합니다.


## 준비 단계

자세한 문제 해결을 계속하기 전에 다음 단계를 수행합니다.

- Azure 포털 또는 Azure Preview 포털에서 가상 컴퓨터의 상태 확인
- [가상 컴퓨터 다시 시작](https://msdn.microsoft.com/library/azure/dn763934.aspx)
- [가상 컴퓨터 크기 조정](https://msdn.microsoft.com/library/dn168976.aspx)

이 단계를 수행한 후 원격 데스크톱 연결을 다시 시도합니다.


## 자세한 문제 해결

다음과 같은 문제 또는 잘못된 구성의 발생지 때문에 원격 데스크톱 클라이언트가 Azure 가상 컴퓨터의 원격 데스크톱 서비스에 연결할 수 없습니다.

- 원격 데스크톱 클라이언트 컴퓨터
- 조직 인트라넷 에지 장치
- 클라우드 서비스 끝점 및 액세스 제어 목록(ACL)
- 네트워크 보안 그룹
- Windows 기반 Azure 가상 컴퓨터

### 발생지 1: 원격 데스크톱 클라이언트 컴퓨터

문제 또는 잘못된 구성의 발생지인 컴퓨터를 제거하려면 사용자의 컴퓨터가 다른 온-프레미스, Windows 기반 컴퓨터에 원격 데스크톱 연결을 설정할 수 있는지 확인합니다.

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_1.png)

연결할 수 없는 경우 컴퓨터에서 다음 사항을 확인합니다.

- 원격 데스크톱 트래픽을 차단하는 로컬 방화벽 설정
- 원격 데스크톱 연결을 방지하는 로컬에 설치된 클라이언트 프록시 소프트웨어
- 원격 데스크톱 연결을 방지하는 로컬에 설치된 네트워크 모니터링 소프트웨어
- 트래픽을 모니터링하거나 특정 유형의 트래픽을 허용/비허용하며 원격 데스크톱 연결을 방지하는 다른 유형의 보안 소프트웨어

이 경우 근본 원인을 파악하기 위해 일시적으로 소프트웨어를 사용하지 않도록 설정하고 온-프레미스 컴퓨터에 원격 데스크톱 연결을 시도합니다. 그런 다음 네트워크 관리자와 협력하여 원격 데스크톱 연결을 허용하도록 소프트웨어 설정을 수정합니다.

### 발생지 2: 조직 인트라넷 에지 장치

문제 또는 잘못된 구성의 발생지인 조직 인트라넷 에지 장치를 제거하려면 인터넷에 직접 연결 된 컴퓨터가 Azure 가상 컴퓨터에 원격 데스크톱 연결을 설정할 수 있는지 확인합니다.

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_2.png)

인터넷에 직접 연결된 컴퓨터가 없는 경우 자체 리소스 그룹 또는 클라우드 서비스에서 새 Azure 가상 컴퓨터를 손쉽게 만들고 사용할 수 있습니다. 자세한 내용은 [Azure에서 Windows를 실행하는 가상 컴퓨터 만들기](virtual-machines-windows-tutorial.md)를 참조하세요. 테스트가 완료되면 리소스 그룹 또는 가상 컴퓨터 및 클라우드 서비스를 삭제합니다.

인터넷에 직접 연결된 컴퓨터로 원격 데스크톱에 연결할 수 있는 경우 조직 인트라넷 에지 장치에서 다음을 확인합니다.

- 인터넷에 대한 HTTPS 연결을 차단하는 내부 방화벽
- 원격 데스크톱 연결을 방지하는 프록시 서버
- 경계 네트워크의 장치에서 실행되는, 원격 데스크톱 연결을 방지하는 침입 탐지 또는 네트워크 모니터링 소프트웨어

네트워크 관리자와 협력하여 인터넷과 HTTPS 기반 원격 데스크톱의 연결을 허용하도록 조직 인트라넷 에지 장치 설정을 수정합니다.

### 발생지 3: 클라우드 서비스 끝점 및 ACL

서비스 관리 API를 사용하여 만든 가상 컴퓨터에 대한 문제 또는 잘못된 구성의 원인이 되는 클라우드 서비스 끝점 및 ACL을 제거하려면 동일한 클라우드 서비스 또는 가상 네트워크에 있는 다른 Azure 가상 컴퓨터가 사용자의 Azure 가상 컴퓨터로 원격 데스크톱 연결이 가능한지 확인합니다.

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_3.png)

> [AZURE.NOTE]리소스 관리자에서 만든 가상 컴퓨터의 경우, [소스 4: 네트워크 보안 그룹](#nsgs)으로 건너뜁니다.

동일한 클라우드 서비스 또는 가상 네트워크에 다른 가상 컴퓨터가 없는 경우, 새 가상 컴퓨터를 손쉽게 만들 수 있습니다. 자세한 내용은 [Azure에서 Windows를 실행하는 가상 컴퓨터 만들기](virtual-machines-windows-tutorial.md)를 참조하세요. 테스트가 완료되면 추가한 가상 컴퓨터를 삭제합니다.

동일한 클라우드 서비스 또는 가상 네트워크에 가상 컴퓨터와 원격 데스크톱 연결을 설정할 수 있는 경우 다음을 확인합니다.

- 대상 가상 컴퓨터의 원격 데스크톱 트래픽에 대 한 끝점 구성. 끝점의 개인 TCP 포트는 가상 컴퓨터에서 원격 데스크톱 서비스를 수신하는 TCP 포트(기본값 3389)와 일치해야 합니다.
- 대상 가상 컴퓨터의 원격 데스크톱 트래픽 끝점에 대한 ACL. ACL은 인터넷에서 들어오는 트래픽을 원본 IP 주소에 따라 허용 또는 거부하도록 지정하는 데 사용됩니다. ACL이 잘못 구성될 경우 끝점에 원격 데스크톱 트래픽이 들어오지 못할 수 있습니다. ACL을 살펴보고 프록시 또는 다른 에지 서버의 공용 IP 주소에서 들어오는 트래픽이 허용되어 있는지 확인하세요. 자세한 내용은 [네트워크 ACL(액세스 제어 목록)이란?](../virtual-network/virtual-networks-acl.md)을 참조하세요.

문제의 발생지인 끝점을 제거하려면 현재 끝점을 제거하고 새 끝점을 만든 후 외부 포트 번호에 49152-65535 범위의 임의 포트를 선택합니다. 자세한 내용은 [가상 컴퓨터로 끝점을 설정하는 방법](virtual-machines-set-up-endpoints.md)을 참조하세요.

### <a id="nsgs"></a>발생지 4: 네트워크 보안 그룹

네트워크 보안 그룹은 허용되는 인바운드 및 아웃바운드 트래픽을 더 세부적으로 제어하는 데 사용됩니다. Azure 가상 네트워크의 서브넷 및 클라우드 서비스에 적용되는 규칙을 만들 수 있습니다. 네트워크 보안 그룹 규칙을 살펴보고 인터넷에서 들어오는 원격 데스크톱 트래픽이 허용되어 있는지 확인하세요.

자세한 내용은 [네트워크 보안 그룹(NSG)은 무엇입니까?](../virtual-network/virtual-networks-nsg.md)를 참조하세요.

### 발생지 5: Windows 기반 Azure 가상 컴퓨터

마지막 문제 발생지는 Azure 가상 컴퓨터 자체입니다.

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_5.png)

[기본적인 원격 데스크톱 문제 해결 문서](virtual-machines-troubleshoot-remote-desktop-connections.md)에서는 [Azure IaaS(Windows) 진단 패키지](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)를 사용하는 방법을 설명합니다. 이 진단 패키지로 **Azure VM에 대한 RDP 연결(다시 부팅 필요)** 문제를 해결할 수 없는 경우 [이 문서](virtual-machines-windows-reset-password.md)의 지침에 따라 가상 컴퓨터에서 원격 데스크톱 서비스 서비스를 다시 설정합니다. 그러면

- "원격 데스크톱" Windows 방화벽 기본 규칙(TCP 포트 3389)이 활성화됩니다.
- HKLM\\System\\CurrentControlSet\\Control\\Terminal Server\\fDenyTSConnections 레지스트리 값이 0으로 설정되어 원격 데스크톱 연결이 활성화됩니다.

컴퓨터에서 연결을 다시 시도하세요. 실패할 경우 다음과 같은 문제 때문일 수 있습니다.

- 원격 데스크톱 서비스가 대상 가상 컴퓨터에서 실행되고 있지 않습니다.
- 원격 데스크톱 서비스가 TCP 포트 3389에서 수신 대기하고 있지 않습니다.
- Windows 방화벽 또는 다른 로컬 방화벽에 원격 데스크톱 트래픽을 방지하는 아웃바운드 규칙이 있습니다.
- Azure 가상 컴퓨터에서 실행 중인 침입 탐지 또는 네트워크 모니터링 소프트웨어가 원격 데스크톱 연결을 방지하고 있습니다.

서비스 관리자 API를 사용하여 만든 가상 컴퓨터에 대한 이러한 잠재적 문제를 해결하기 위해 Azure 가상 컴퓨터에 대한 원격 Azure PowerShell 세션을 사용할 수 있습니다. 먼저 가상 컴퓨터의 호스팅 클라우드 서비스에 대 인증서를 설치해야 합니다. [Azure 가상 컴퓨터에 대한 보안 원격 PowerShell 액세스 구성](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe)으로 이동하고 **InstallWinRMCertAzureVM.ps1** 스크립트 파일을 로컬 컴퓨터의 폴더에 다운로드합니다.

다음으로, 아직 없는 경우 Azure PowerShell을 설치합니다. [Azure PowerShell 설치 및 구성 방법](../install-configure-powershell.md)을 참조하세요.

다음으로, Azure PowerShell 명령 프롬프트를 열고 현재 폴더를 **InstallWinRMCertAzureVM.ps1** 스크립트 파일 위치로 변경합니다. Azure PowerShell 스크립트를 실행하려면 올바른 실행 정책을 설정해야 합니다. 현재 정책 수준을 지정하려면 **Get-ExecutionPolicy** 명령을 실행합니다. 적절한 수준을 설정하는 방법에 대한 자세한 내용은 [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx)를 참조하세요.

다음으로, Azure 구독 이름, 클라우드 서비스 이름 및 해당 가상 컴퓨터 이름(< and > 문자 제거)을 입력한 다음 이러한 명령을 실행합니다.

	$subscr="<Name of your Azure subscription>"
	$serviceName="<Name of the cloud service that contains the target virtual machine>"
	$vmName="<Name of the target virtual machine>"
	.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName

**Get-AzureSubscription** 명령 표시의 _SubscriptionName_ 속성에서 올바른 구독 이름을 가져올 수 있습니다. **Get-AzureVM** 명령 표시의 _ServiceName_ 열에서 가상 컴퓨터의 클라우드 서비스 이름을 가져올 수 있습니다.

이 새 인증서가 있는지 확인하려면 현재 사용자에 대한 인증서 스냅인을 열고 **Trusted Root Certification Authorities\\Certificates** 폴더를 살펴봅니다. 인증서 발급 대상 열에 클라우드 서비스의 DNS 이름을 가진 인증서가 표시되어야 합니다(예: cloudservice4testing.cloudapp.net).

다음으로, 이러한 명령을 사용하여 원격 Azure PowerShell 세션을 시작합니다.

	$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
	$creds = Get-Credential
	Enter-PSSession -ConnectionUri $uri -Credential $creds

유효한 관리자 자격 증명을 입력한 후 Azure PowerShell 프롬프트에 다음과 같은 내용이 표시되어야 합니다.

	[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>

프롬프트의 첫 번째 부분은 사용자가 현재 대상 가상 컴퓨터를 포함하는 클라우드 서비스에 대한 Azure PowerShell 명령을 실행하고 있음을 나타냅니다. 사용자의 클라우드 서비스 이름은 "cloudservice4testing.cloudapp.net"과 다른 이름입니다.

이제 Azure PowerShell 명령을 실행하여 위에 언급된 추가 문제를 조사하고 구성을 수정할 수 있습니다.

### TCP 포트에서 수신 대기하는 원격 데스크톱 서비스를 수동으로 수정하려면

**Azure VM에 대한 RDP 연결(다시 부팅 필요)** 문제로 인해 [Azure IaaS(Windows) 진단 패키지](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)를 실행할 수 없는 경우 원격 Azure PowerShell 세션 프롬프트에서 이 명령을 실행합니다.

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

PortNumber 속성은 현재 포트 번호를 보여줍니다. 필요한 경우 이 명령을 사용하여 원격 데스크톱 포트 번호를 다시 기본값(3389)으로 변경합니다.

	Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389

이 명령을 사용하여 포트가 3389로 변경되었는지 확인합니다.

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

이 명령을 사용하여 원격 Azure PowerShell 세션을 종료합니다.

	Exit-PSSession

Azure 가상 컴퓨터에 대한 원격 데스크톱 끝점도 TCP 포트 3398을 내부 포트로 사용하고 있는지 확인합니다. 그런 다음 Azure 가상 컴퓨터를 다시 시작하고 원격 데스크톱 연결을 다시 시도합니다.


## 추가 리소스

[Azure IaaS(Windows) 진단 패키지](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Windows 가상 컴퓨터에 대한 원격 데스크톱 서비스 또는 암호를 다시 설정하는 방법](virtual-machines-windows-reset-password.md)

[Azure PowerShell을 설치 및 구성하는 방법](../install-configure-powershell.md)

[Linux 기반 Azure 가상 컴퓨터에 SSH(보안 셸) 연결 문제 해결](virtual-machines-troubleshoot-ssh-connections.md)

[Azure 가상 컴퓨터에서 실행 중인 응용 프로그램에 대한 액세스 문제 해결](virtual-machines-troubleshoot-access-application.md)

<!---HONumber=Sept15_HO3-->