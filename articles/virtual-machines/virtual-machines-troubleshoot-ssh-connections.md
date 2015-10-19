<properties
	pageTitle="SSH를 통해 Azure VM에 연결할 수 없음 | Microsoft Azure"
	description="Linux를 실행하는 Azure 가상 컴퓨터에 대한 SSH(보안 셸) 연결 문제 해결"
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/05/2015"
	ms.author="dkshir"/>

# Linux 기반 Azure 가상 컴퓨터에 SSH(보안 셸) 연결 문제 해결

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]이 문서에서는 클래식 배포 모델 또는 리소스 관리자 배포 모델을 사용하여 만든 가상 컴퓨터에서 SSH 연결 문제를 해결하는 방법을 설명합니다.


Linux 기반 Azure 가상 컴퓨터에서 SSH 실패가 발생하는 원인은 여러 가지가 있을 수 있습니다. 이 문서는 원인을 알아내고 문제를 해결하는 데 도움이 됩니다.

> [AZURE.NOTE]이 문서는 Linux를 실행하는 Azure 가상 컴퓨터에만 적용됩니다. Windows를 실행하는 Azure 가상 컴퓨터에 대한 연결 문제를 해결하는 방법은 [이 문서](virtual-machines-troubleshoot-remote-desktop-connections.md)를 참조하세요.

## Azure 고객 지원 서비스에 문의

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 스택 오버플로 포럼](http://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다.

또는 Azure 기술 지원 인시던트를 제출할 수도 있습니다. [Azure 지원 사이트](http://azure.microsoft.com/support/options/)로 이동한 다음 **지원 받기**를 클릭합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](http://azure.microsoft.com/support/faq/)를 참조하세요.


## 기본 단계


### 클래식 배포 모델

클래식 배포 모델을 사용하여 만든 가상 컴퓨터의 보다 일반적인 SSH 연결 오류를 해결하려면 다음 단계를 시도합니다.

1. [Azure 포털](https://portal.azure.com)에서 원격 액세스를 다시 설정합니다. **모두 찾아보기** > **가상 컴퓨터(클래식)** > Windows 가상 컴퓨터 > **원격 액세스 다시 설정**을 클릭합니다.

	![원격 액세스 다시 설정](./media/virtual-machines-troubleshoot-ssh-connections/Portal-SSH-Reset-Windows.png)

2. 위와 마찬가지로 가상 컴퓨터를 다시 시작합니다. [Azure preview 포털](https://portal.azure.com)에서 **모두 찾아보기** > **가상 컴퓨터(클래식)** > Windows 가상 컴퓨터 > **다시 시작**을 클릭합니다. [Azure 관리 포털](https://manage.windowsazure.com)에서 **가상 컴퓨터** > **인스턴스**를 열고 **다시 시작**을 클릭합니다.

3. [가상 컴퓨터의 크기를 조정](https://msdn.microsoft.com/library/dn168976.aspx)합니다.

4. 가상 컴퓨터에서 [Linux 기반 가상 컴퓨터의 암호 또는 SSH를 다시 설정하는 방법](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md)의 지침을 따라 다음 작업을 수행합니다.

	- 암호 또는 SSH 키를 재설정합니다.
	- 새 sudo 사용자 계정을 만듭니다.
	- SSH 구성을 재설정합니다.


### 리소스 관리자 배포 모델

리소스 관리자 배포 모델을 사용하여 만든 가상 컴퓨터에 대한 일반적인 SSH 문제를 해결하려면 다음 단계를 수행합니다.

1. 명령줄에서 Linux VM에 SSH 연결을 다시 설정합니다. [Microsoft Azure Linux Agent](virtual-machines-linux-agent-user-guide.md) 버전 2.0.5 이상이 설치되어 있는지 확인합니다.

	[A] Azure CLI 사용:

	1단계: 아직 설치되어 있지 않은 경우 `azure login` 명령을 사용하여 [Azure CLI를 설치하고 Azure 구독에 연결](../xplat-cli-install.md)합니다.

	2단계: 리소스 관리자 모드로 전환합니다.

		azure config mode arm

	3단계: 다음 방법 중 하나를 사용하여 SSH 연결을 재설정할 수 있습니다.

	(i) 다음 예처럼 `vm reset-access` 명령을 사용합니다.

		azure vm reset-access -g TestRgV2 -n TestVmV2 -r

	그러면 가상 컴퓨터에 `VMAccessForLinux` 확장이 설치됩니다.

	(ii) 또는 다음과 같은 내용으로 PrivateConf.json이라는 파일을 만들 수 있습니다.

		{
			"reset_ssh":"True"
		}

	그런 다음 `VMAccessForLinux` 확장을 수동으로 실행하여 SSH 연결을 다시 설정합니다.

		azure vm extension set "testRG" "testVM" VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json

	[B] Azure PowerShell 사용:

	1단계: 아직 설치되어 있지 않은 경우 Azure AD 명령을 사용하여 [Azure PowerShell를 설치하고 Azure 구독에 연결](../powershell-install-configure.md)합니다.

	2단계: 리소스 관리자 모드로 전환합니다.

		Switch-AzureMode -Name AzureResourceManager

	3단계: 아래 예처럼 `VMAccessForLinux` 확장을 실행하여 SSH 연결을 다시 설정합니다.

		Set-AzureVMExtension -ResourceGroupName "testRG" -VMName "testVM" -Location "West US" -Name "VMAccessForLinux" -Publisher "Microsoft.OSTCExtensions" -ExtensionType "VMAccessForLinux" -TypeHandlerVersion "1.2" -SettingString "{}" -ProtectedSettingString '{"reset_ssh":true}'

2. 포털에서 Linux VM을 다시 시작합니다. [Azure preview 포털](https://portal.azure.com)에서 **모두 찾아보기** > **가상 컴퓨터** > Windows 가상 컴퓨터 > **다시 시작**을 클릭합니다.

	![V2 다시 시작](./media/virtual-machines-troubleshoot-ssh-connections/Portal-SSH-Restart-V2-Windows.png)

3. 명령줄에서 Linux VM에 대한 암호 및/또는 SSH 키를 다시 설정합니다. 다음 예와 같이 sudo 권한으로 새 사용자 이름/암호를 만들 수도 있습니다.

	[A] Azure CLI 사용:

	위의 설명대로 Azure CLI를 설치 및 구성합니다. 리소스 관리자 모드로 전환한 후 다음 방법 중 하나를 사용하여 확장을 실행합니다.

	(i) `vm reset-access` 명령을 실행하여 SSH 자격 증명을 설정합니다.

		azure vm reset-access TestRgV2 TestVmV2 -u NewUser -p NewPassword

	명령줄에 `azure vm reset-access -h`를 입력하여 이에 대한 자세한 정보를 확인합니다.

	(ii) 또는 다음과 같은 내용으로 PrivateConf.json이라는 파일을 만들 수 있습니다.

		{
			"username":"NewUsername", "password":"NewPassword", "expiration":"2016-01-01", "ssh_key":"", "reset_ssh":false, "remove_user":""
		}

	그런 다음 위의 파일을 사용하여 Linux 확장을 실행합니다.

		$azure vm extension set "testRG" "testVM" VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json

	[Linux 기반 가상 컴퓨터의 암호 또는 SSH를 재설정하는 방법](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md)과 유사한 단계를 따라 다르게 변형해 봅니다. 리소스 관리자 모드에 대한 Azure CLI 지침을 수정해야 합니다.

	[B] Azure PowerShell 사용:

	위의 설명대로 Azure PowerShell을 설치 및 구성합니다. 리소스 관리자 모드로 전환한 후 다음을 사용하여 확장을 실행합니다.

		$RGName = 'testRG'
		$VmName = 'testVM'
		$Location = 'West US'

		$ExtensionName = 'VMAccessForLinux'
		$Publisher = 'Microsoft.OSTCExtensions'
		$Version = '1.2'

		$PublicConf = '{}'
		$PrivateConf = '{"username":"NewUsername", "password":"NewPassword", "ssh_key":"", "reset_ssh":false, "remove_user":""}'

		Set-AzureVMExtension -ResourceGroupName $RGName -VMName $VmName -Location $Location -Name $ExtensionName -Publisher $Publisher -ExtensionType $ExtensionName -TypeHandlerVersion $Version -SettingString $PublicConf -ProtectedSettingString $PrivateConf

	설치에 특정 값을 가진 $RGName, $VmName, $Location 및 SSH 자격 증명의 값을 교체할 수 있는지 확인합니다.

## 자세한 문제 해결

그래도 SSH 클라이언트에서 가상 컴퓨터의 SSH 서비스에 연결할 수 없는 경우 그 원인은 다양할 수 있습니다. 다음은 관련된 구성 요소입니다.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot1.png)

다음 섹션을 참조하여 문제의 원인을 격리하고 해결 방법을 찾을 수 있습니다.

### 문제 해결 전 단계

가장 먼저, Azure 포털에서 가상 컴퓨터의 상태를 확인합니다.

[Azure 관리 포털](https://manage.windowsazure.com)에서 클래식 배포 모델의 가상 컴퓨터에 대해 다음을 수행합니다.

1. **가상 컴퓨터** > *VM 이름*을 클릭합니다.
2. VM의 **대시보드**를 클릭하여 상태를 확인합니다.
3. **모니터**를 클릭하여 계산, 저장소 및 네트워크 리소스에 대한 최근 활동을 봅니다.
4. **끝점**을 클릭하여 SSH 트래픽의 끝점이 있는지 확인합니다.

[Azure preview 포털에서](https://portal.azure.com) 다음을 수행합니다.

1. 클래식 배포 모델에서 만든 가상 컴퓨터의 경우 **찾아보기** > **가상 컴퓨터(클래식)** > *VM 이름*을 클릭합니다. 리소스 관리자를 사용하여 만든 가상 컴퓨터의 경우, **찾아보기** > **가상 컴퓨터(v2)** > *VM 이름*을 클릭합니다. 가상 컴퓨터에 대한 상태 창에 **실행 중**이 표시됩니다. 아래로 스크롤하여 계산, 저장소 및 네트워크 리소스에 대한 최근 활동을 표시합니다.
2. **설정**을 클릭하여 끝점, IP 주소 및 기타 설정을 확인합니다. 리소스 관리자로 만든 가상 컴퓨터에서 끝점을 식별하려면 [네트워크 보안 그룹](../traffic-manager/virtual-networks-nsg.md)이 정의되어 있고 여기에 규칙이 적용되었으며 서브넷에서 참조되는지 확인합니다.

네트워크 연결 상태를 확인하려면 구성된 끝점을 점검하고 HTTP와 같은 다른 프로토콜 또는 다른 서비스를 통해 VM에 연결할 수 있는지 여부를 확인합니다.

이 단계 후 SSH 연결을 다시 시도해 보세요.


### 문제 해결 단계

컴퓨터의 SSH 클라이언트가 Azure 가상 컴퓨터의 SSH 서비스에 연결할 수 없는 이유는 다음과 같은 문제 또는 잘못된 구성 때문일 수 있습니다.

- SSH 클라이언트 컴퓨터
- 조직 에지 장치
- 클라우드 서비스 끝점 및 액세스 제어 목록(ACL)
- 네트워크 보안 그룹
- Linux 기반 Azure 가상 컴퓨터

#### 발생지 1: SSH 클라이언트 컴퓨터

사용자의 컴퓨터가 문제의 원인인지 확인하기 위해 사용자의 컴퓨터가 다른 온-프레미스, Linux 기반 컴퓨터에 SSH 연결을 설정할 수 있는지 확인합니다.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot2.png)

연결이 실패할 경우 컴퓨터에서 다음 사항을 확인합니다.

- 인바운드 또는 아웃 바운드 SSH 트래픽(TCP 22)을 차단하는 로컬 방화벽 설정
- SSH 연결을 방지하는 로컬에 설치된 클라이언트 프록시 소프트웨어
- SSH 연결을 방지하는 로컬에 설치된 네트워크 모니터링 소프트웨어
- 트래픽을 모니터링하거나 특정 유형의 트래픽을 허용하거나 허용하지 않는 다른 유형의 보안 소프트웨어

이러한 모든 경우에 일시적으로 소프트웨어를 사용하지 않도록 설정하고 온-프레미스 컴퓨터에 SSH 연결을 시도하여 원인을 파악합니다. 그런 다음 네트워크 관리자와 협력하여 SSH 연결을 허용하도록 소프트웨어의 설정을 수정합니다.

인증서 인증을 사용하는 경우 홈 디렉터리의 .ssh 폴더에 대해 다음 권한이 있는지 확인하세요.

- Chmod 700 ~/.ssh
- Chmod 644 ~/.ssh/*.pub
- Chmod 600 ~/.ssh/id\_rsa(또는 개인 키가 저장되어 있는 기타 파일)
- Chmod 644 ~/.ssh/known\_hosts(SSH를 통해 연결한 호스트 포함)

#### 발생지 2: 조직 에지 장치

조직의 에지 장치가 문제의 원인인지 확인하기 위해 인터넷에 직접 연결된 컴퓨터가 Azure 가상 컴퓨터에 SSH 연결을 설정할 수 있는지 확인합니다. 사이트 간 VPN 또는 Express 경로 연결을 통해 VM에 액세스하는 경우 [발생지 4: 네트워크 보안 그룹](#nsg)으로 건너뜁니다.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot3.png)

인터넷에 직접 연결된 컴퓨터가 없는 경우 자체 리소스 그룹 또는 클라우드 서비스에서 새 Azure 가상 컴퓨터를 손쉽게 만들고 사용할 수 있습니다. 자세한 내용은 [Azure에서 Linux를 실행하는 가상 컴퓨터 만들기](virtual-machines-linux-tutorial.md)를 참조하세요. 테스트가 완료되면 리소스 그룹 또는 가상 컴퓨터 및 클라우드 서비스를 삭제합니다.

인터넷에 직접 연결된 컴퓨터에 대한 SSH 연결을 설정할 수 있는 경우 조직 에지 장치에서 다음을 확인합니다.

- 인터넷을 사용하여 SSH 트래픽을 차단하는 내부 방화벽
- SSH 연결을 방지하는 프록시 서버
- 경계 네트워크의 장치에서 실행되는, SSH 연결을 방지하는 침입 탐지 또는 네트워크 모니터링 소프트웨어

네트워크 관리자와 협력하여 인터넷에서 SSH 트래픽을 허용하도록 조직 에지 장치 설정을 수정합니다.

#### 발생지 3: 클라우드 서비스 끝점 및 ACL

> [AZURE.NOTE]이 발생지는 클래식 배포 모델을 사용하여 만든 가상 컴퓨터에 대해서만 적용됩니다. 리소스 관리자를 사용하여 만든 가상 컴퓨터의 경우 [발생지 4: 네트워크 보안 그룹](#nsg)으로 건너뜁니다.

문제의 발생지인 클라우드 서비스 끝점 및 ACL을 제거하기 위해 [클래식 배포 모델](../resource-manager-deployment-model.md)을 사용하여 생성된 VM의 경우 동일한 가상 네트워크의 다른 Azure VM이 사용자의 VM에 SSH 연결을 설정할 수 있는지 확인합니다.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot4.png)

동일한 가상 네트워크에 다른 VM이 없는 경우 새 가상 컴퓨터를 손쉽게 만들 수 있습니다. 자세한 내용은 [Azure에서 Linux를 실행하는 가상 컴퓨터 만들기](virtual-machines-linux-tutorial.md)를 참조하세요. 테스트를 마치면 추가한 VM을 삭제합니다.

동일한 가상 네트워크의 VM에 SSH 연결을 만들 수 있는 경우 다음을 확인합니다.

- 대상 VM의 SSH 트래픽에 대한 끝점 구성. 끝점의 개인 TCP 포트는 VM에서 SSH 서비스가 수신 대기 중인 TCP 포트(기본값 22)와 일치해야 합니다. 템플릿을 사용하여 리소스 관리자 배포 모델로 만든 VM의 경우 **찾아보기** > **가상 컴퓨터(v2)** > *VM 이름* > **설정** > **끝점**을 사용하여 Azure Preview 포털의 SSH TCP 포트 번호를 확인합니다.
- 대상 가상 컴퓨터의 SSH 트래픽 끝점에 대한 ACL. ACL은 인터넷에서 들어오는 트래픽을 원본 IP 주소에 따라 허용 또는 거부하도록 지정하는 데 사용됩니다. ACL이 잘못 구성될 경우 끝점에 SSH 트래픽이 들어오지 못할 수 있습니다. ACL을 확인하고 프록시 또는 다른 에지 서버의 공용 IP 주소에서 들어오는 트래픽이 허용되어 있는지 확인합니다. 자세한 내용은 [네트워크 ACL(액세스 제어 목록) 정보](../virtual-network/virtual-networks-acl.md)를 참조하세요.

문제의 발생지인 끝점을 제거하려면 현재 끝점을 제거하고 새 끝점을 만든 다음 **SSH** 이름(공용 및 개인 포트 번호에 TCP 포트 22)을 지정합니다. 자세한 내용은 [Azure의 가상 컴퓨터에 끝점 설정](virtual-machines-set-up-endpoints.md)을 참조하세요.

<a id="nsg"></a>
#### 발생지 4: 네트워크 보안 그룹

네트워크 보안 그룹을 사용하면 허용되는 인바운드 및 아웃바운드 트래픽을 더 세부적으로 제어할 수 있습니다. Azure 가상 네트워크의 서브넷 및 클라우드 서비스에 적용되는 규칙을 만들 수 있습니다. 네트워크 보안 그룹 규칙을 확인하고 인터넷으로 나가고 들어오는 SSH 트래픽이 허용되어 있는지 확인합니다. 자세한 내용은 [네트워크 보안 그룹 정보](../traffic-manager/virtual-networks-nsg.md)를 참조하세요.

#### 발생지 5: Linux 기반 Azure 가상 컴퓨터

마지막 가능한 문제 발생지는 Azure 가상 컴퓨터 자체입니다.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot5.png)

아직 그렇게 하지 않은 경우 가상 컴퓨터에서 [Linux 기반 가상 컴퓨터의 암호 또는 SSH를 다시 설정하는 방법](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md)의 지침을 따르세요.

컴퓨터에서 다시 연결을 시도하세요. 그래도 실패할 경우 다음과 같은 문제 때문일 수 있습니다.

- SSH 서비스가 대상 가상 컴퓨터에서 실행되고 있지 않습니다.
- SSH 서비스가 TCP 포트 22에서 수신 대기하고 있지 않습니다. 이를 테스트하려면 로컬 컴퓨터에서 텔넷 클라이언트를 설치하고 "telnet *cloudServiceName*. cloudapp.net 22"를 실행하세요. 그렇게 하면 가상 컴퓨터가 SSH 끝점에 대한 인바운드 및 아웃바운드 통신을 허용하는지 여부를 알 수 있습니다.
- 대상 가상 컴퓨터의 로컬 방화벽에 인바운드 또는 아웃바운드 SSH 트래픽을 방지하는 규칙이 있습니다.
- Azure 가상 컴퓨터에서 실행 중인 침입 탐지 또는 네트워크 모니터링 소프트웨어가 SSH 연결을 방지하고 있습니다.


## 추가 리소스

클래식 배포 모델의 가상 컴퓨터에서 [Linux 기반 가상 컴퓨터의 암호 또는 SSH를 다시 설정하는 방법](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md)을 따르세요.

[Windows 기반 Azure 가상 컴퓨터에 대한 Windows 원격 데스크톱 연결 문제 해결](virtual-machines-troubleshoot-remote-desktop-connections.md)

[Azure 가상 컴퓨터에서 실행 중인 응용 프로그램에 대한 액세스 문제 해결](virtual-machines-troubleshoot-access-application.md)

<!---HONumber=Oct15_HO2-->