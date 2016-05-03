<properties
	pageTitle="Azure VM에 원격 데스크톱 연결 문제 해결 | Microsoft Azure"
	description="Windows VM에 대한 원격 데스크톱 연결 문제를 해결합니다. 빠른 마이그레이션 단계, 오류 메시지별 도움말, 자세한 네트워크 문제 해결을 가져옵니다."
	keywords="원격 데스크톱 오류,원격 데스크톱 연결 오류,VM에 연결할 수 없습니다,원격 데스크톱 문제 해결"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="support-article"
	ms.date="04/12/2016"
	ms.author="iainfou"/>

# Windows를 실행하는 Azure 가상 컴퓨터에 대한 원격 데스크톱 연결 문제 해결

Windows 기반 Azure 가상 컴퓨터에 원격 데스크톱(RDP) 연결은 여러 이유로 인해 실패할 수 있습니다. 이러한 문제는 VM의 원격 데스크톱 서비스, 네트워크 연결 또는 호스트 컴퓨터의 원격 데스크톱 클라이언트에서 발생할 수 있습니다. 이 문서는 원인을 알아내고 문제를 해결하는 데 도움이 됩니다.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

이 문서는 Windows를 실행하는 Azure 가상 컴퓨터에 적용됩니다. Linux를 실행하는 Azure 가상 컴퓨터의 경우 [Azure VM에 SSH 연결 문제 해결](virtual-machines-linux-troubleshoot-ssh-connection.md)을 참조하세요.

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 스택 오버플로 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수도 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 이동한 다음 **지원 받기**를 클릭합니다.


<a id="quickfixrdp"></a>
## 일반적인 원격 데스크톱 오류 해결

이 섹션에는 일반적인 원격 데스크톱 연결 문제에 대한 빠른 해결 단계가 나와 있습니다.

### 클래식 배포 모델을 사용하여 만든 가상 컴퓨터

이 단계는 클래식 배포 모델을 통해 만든 Azure 가상 컴퓨터에서 대부분의 원격 데스크톱 연결 오류를 해결할 수 있습니다. 각 단계 후 VM에 다시 연결을 시도합니다.

- [Azure 포털](https://portal.azure.com)에서 원격 데스크톱 서비스를 다시 설정하여 RDP 서버와 시작 문제를 해결합니다.<br> **찾아보기** > **가상 컴퓨터(클래식)** > Windows 가상 컴퓨터 > **원격 액세스 다시 설정**을 클릭합니다.

- 가상 컴퓨터를 다시 시작하여 다른 시작 문제를 해결합니다.<br> **찾아보기** > **가상 컴퓨터(클래식)** > Windows 가상 컴퓨터 > **다시 시작**을 클릭합니다.

- VM 크기를 조정하여 모든 호스트 문제를 해결합니다.<br> **찾아보기** > **가상 컴퓨터(클래식)** > Windows 가상 컴퓨터 > **설정** > **크기**를 클릭합니다. 자세한 단계는 [가상 컴퓨터 크기 조정](https://msdn.microsoft.com/library/dn168976.aspx)을 참조하세요.

- VM의 콘솔 로그 또는 스크린샷을 검토하여 부팅 문제를 해결합니다.<br> **찾아보기** > **가상 컴퓨터(클래식)** > Windows 가상 컴퓨터 > **설정** > **진단 부팅**을 클릭합니다.

- VM 리소스 상태에 플랫폼 문제가 있는지 확인합니다.<br> **찾아보기** > **가상 컴퓨터(클래식)** > Windows 가상 컴퓨터 > **설정** > **상태 검사**를 클릭합니다.

### 리소스 관리자 배포 모델을 사용하여 만든 가상 컴퓨터

이 단계는 리소스 관리자 배포 모델을 통해 만든 Azure 가상 컴퓨터에서 대부분의 원격 데스크톱 연결 오류를 해결할 수 있습니다. 각 단계 후 VM에 다시 연결을 시도합니다.

- Powershell<br>을 사용한 _원격 액세스 다시 설정_ a. 아직 설치되지 않은 경우 Azure AD 명령을 사용하여 [Azure PowerShell를 설치하고 Azure 구독에 연결](../powershell-install-configure.md)합니다. 새 Azure PowerShell 버전 1.0. x에서는 리소스 관리자 모드로 전환할 필요가 없습니다.

	b. 다음 Azure PowerShell 명령 중 하나를 사용하여 RDP 연결을 다시 설정합니다. `myRG`, `myVM`, `myVMAccessExtension` 및 위치를 설치에 관련된 값으로 대체합니다.

	```
	Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccessExtension" -ExtensionType "VMAccessAgent" -Publisher "Microsoft.Compute" -typeHandlerVersion "2.0" -Location Westus
	```
	또는<br>

  ```
  Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" -Location Westus
  ```

- 가상 컴퓨터를 다시 시작하여 다른 시작 문제를 해결합니다.<br> **찾아보기** > **가상 컴퓨터** > Windows 가상 컴퓨터 > **다시 시작**을 클릭합니다.

- VM 크기를 조정하여 모든 호스트 문제를 해결합니다.<br> **찾아보기** > **가상 컴퓨터** > Windows 가상 컴퓨터 > **설정** > **크기**를 클릭합니다.

- VM의 콘솔 로그 또는 스크린샷을 검토하여 부팅 문제를 해결합니다.<br> **찾아보기** > **가상 컴퓨터** > Windows 가상 컴퓨터 > **설정** > **진단 부팅**을 클릭합니다.


위의 단계로 원격 데스크톱 연결 오류를 해결하지 못하면 다음 단계로 이동합니다.

## 특정한 원격 데스크톱 연결 오류 문제 해결

다음은 Azure 가상 컴퓨터에 원격 데스크톱 연결을 하려고 할 때 발생할 수 있는 가장 일반적인 오류입니다.

1. [원격 데스크톱 연결 오류: 라이선스를 제공할 수 있는 원격 데스크톱 라이선스 서버가 없으므로 원격 세션이 끊겼습니다.](#rdplicense)

2. [원격 데스크톱 연결 오류: 원격 데스크톱이 컴퓨터 "name"을(를) 찾을 수 없습니다.](#rdpname)

3. [원격 데스크톱 연결 오류: 인증 오류가 발생했습니다. 로컬 보안 기관에 연결할 수 없습니다.](#rdpauth)

4. [Windows 보안 오류: 자격 증명이 작동하지 않습니다](#wincred).

5. [원격 데스크톱 연결 오류: 이 컴퓨터에서 원격 컴퓨터에 연결할 수 없습니다.](#rdpconnect)

<a id="rdplicense"></a>
### 원격 데스크톱 연결 오류: 라이선스를 제공할 수 있는 원격 데스크톱 라이선스 서버가 없으므로 원격 세션이 끊겼습니다.

원인: 원격 데스크톱 서버 역할에 대한 120일 라이선스 유예 기간이 만료되었고 라이선스를 설치해야 합니다.

대안으로 포털에서 RDP 파일의 로컬 복사본을 저장하고 Windows PowerShell 명령 프롬프트에서 이 명령을 실행하여 연결합니다. 이렇게 하면 해당 연결에만 라이선스를 사용할 수 없게 됩니다.

		mstsc <File name>.RDP /admin

VM에 실제로 두 개 이상의 동시 원격 데스크톱 연결이 필요하지 않을 경우 서버 관리자를 사용하여 원격 데스크톱 서버 역할을 제거할 수 있습니다.

또한 ["사용 가능하 원격 데스크톱 라이선스 서버가 없음"과 함께 Azure VM이 실패하는 경우](http://blogs.msdn.com/b/wats/archive/2014/01/21/rdp-to-azure-vm-fails-with-quot-no-remote-desktop-license-servers-available-quot.aspx) 블로그 게시물을 참조하십시오.

<a id="rdpname"></a>
### 원격 데스크톱 연결 오류: 원격 데스크톱이 컴퓨터 "name"을(를) 찾을 수 없습니다.

원인: 컴퓨터의 원격 데스크톱 클라이언트가 RDP 파일의 설정에 있는 컴퓨터의 이름을 확인할 수 없습니다.

가능한 해결 방법:

- 조직 인트라넷을 사용하는 경우 컴퓨터가 프록시 서버에 액세스할 수 있고 HTTPS 트래픽을 보낼 수 있는지 확인합니다.
- 로컬 컴퓨터에 저장된 RDP 파일을 사용하는 경우 포털에서 생성한 파일을 사용해 보십시오. 이때 가상 컴퓨터 또는 클라우드 서비스 및 가상 컴퓨터의 끝점 포트에 대한 올바른 DNS 이름을 사용해야 합니다. 포털에서 생성된 RDP 파일의 샘플은 다음과 같습니다.

		full address:s:tailspin-azdatatier.cloudapp.net:55919
		prompt for credentials:i:1

이 RDP 파일의 주소 부분에는 가상 컴퓨터(이 예에서는 tailspin azdatatier.cloudapp.net) 및 원격 데스크톱 트래픽 끝점의 외부 TCP 포트(55919)를 포함하는 클라우드 서비스의 정규화된 도메인 이름이 포함되어 있습니다.

<a id="rdpauth"></a>
### 원격 데스크톱 연결 오류: 인증 오류가 발생했습니다. 로컬 보안 기관에 연결할 수 없습니다.

원인: 대상 가상 컴퓨터가 사용자의 자격 증명의 사용자 이름 부분에서 보안 기관을 찾지 못했습니다.

사용자 이름이 *SecurityAuthority*\*UserName* (example: CORP\\User1) 형식인 경우, *SecurityAuthority* 부분은 가상 컴퓨터 이름(로컬 보안 기관)이거나 Active Directory 도메인 이름입니다.

가능한 해결 방법:

- 사용자의 사용자 계정이 가상 컴퓨터에 대한 로컬 계정인 경우 가상 컴퓨터 이름의 철자가 올바른지 확인합니다.
- 계정이 Active Directory 도메인에 있는 경우, 도메인 이름의 철자를 확인합니다.
- 사용자 계정이 Active Directory 도메인 계정이고 해당 도메인 이름 철자가 올바르게 입력된 경우, 해당 도메인에서 도메인 컨트롤러를 사용할 수 있는지 확인합니다. 도메인 컨트롤러를 포함한 Azure 가상 네트워크에 일반적인 문제가 생길 수 있으며, 도메인 컨트롤러 컴퓨터가 시작되지 않을 수 있습니다. 해결 방법으로 도메인 계정 대신 로컬 관리자 계정을 사용할 수 있습니다.

<a id="wincred"></a>
### Windows 보안 오류: 자격 증명이 작동하지 않습니다.

원인: 대상 가상 컴퓨터가 사용자의 계정 이름 및 암호의 유효성을 확인하지 못했습니다.

Windows 기반 컴퓨터는 로컬 계정 또는 도메인 계정 자격 증명의 유효성을 검사할 수 있습니다.

- 로컬 계정의 경우 *ComputerName*\*UserName* 구문(예: SQL1\\Admin4798)을 사용합니다.
- 도메인 계정의 경우 *DomainName*\*UserName* 구문(예: CONTOSO\\johndoe)을 사용합니다.

또한 VM을 새 Active Directory 포리스트의 도메인 컨트롤러로 승격한 경우 사용자가 로그인할 때 사용한 로컬 관리자 계정이 새 포리스트 및 도메인과 같은 암호를 가진 동일한 계정으로 변환됩니다. 그러면 로컬 계정이 삭제됩니다. 예를 들어 DC1\\DCAdmin 로컬 계정으로 로그인하고 가상 컴퓨터를 corp.contoso.com 도메인에 대한 새 포리스트의 도메인 컨트롤러로 승격하는 경우 DC1\\DCAdmin 로컬 계정이 삭제되고 같은 암호를 가진 새 도메인 계정 CORP\\DCAdmin이 생성됩니다.

계정 이름이 가상 컴퓨터가 유효한 계정으로 확인할 수 있는 이름이고 암호가 올바른지 확인하십시오.

로컬 관리자 계정의 암호를 변경해야 하는 경우 [Windows 가상 컴퓨터에 대한 원격 데스크톱 서비스 또는 암호를 다시 설정하는 방법](virtual-machines-windows-reset-rdp.md)을 참조하세요.

<a id="rdpconnect"></a>
### 원격 데스크톱 연결 오류: 이 컴퓨터에서 원격 컴퓨터에 연결할 수 없습니다.

원인: 연결에 사용한 계정에 원격 데스크톱 로그온 권한이 없습니다.

모든 Windows 컴퓨터에는 로그온할 수 있는 계정 및 그룹을 포함하는 원격 데스크톱 사용자 로컬 그룹이 있습니다. 로컬 관리자 그룹의 구성원도 액세스 권한을 갖고 있지만 원격 데스크톱 사용자 로컬 그룹에 표시되지는 않습니다. 도메인에 가입된 컴퓨터의 경우 해당 도메인의 도메인 관리자도 로컬 관리자 그룹에 포함됩니다.

연결하는 데 사용하는 계정에 원격 데스크톱 로그온 권한이 있는지 확인하세요. 해결 방법으로, 도메인 또는 로컬 관리자 계정을 사용하여 원격 데스크톱에 대해 연결한 후 컴퓨터 관리 스냅인(**시스템 도구 > 로컬 사용자 및 그룹 > 그룹 > 원격 데스크톱 사용자**)을 사용하여 원격 데스크톱 사용자 로컬 그룹에 원하는 계정을 추가합니다.

## 일반 원격 데스크톱 오류 문제 해결

이러한 오류가 발생하지 않았는데도 여전히 원격 데스크톱을 통해 VM에 연결할 수 없는 경우 [원격 데스크톱에 대한 자세한 문제 해결 가이드](virtual-machines-windows-detailed-troubleshoot-rdp.md)를 읽어보세요.


## 추가 리소스

[Azure IaaS(Windows) 진단 패키지](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Windows 가상 컴퓨터에 대한 원격 데스크톱 서비스 또는 암호를 다시 설정하는 방법](virtual-machines-windows-reset-rdp.md)

[Azure PowerShell 설치 및 구성하는 방법](../powershell-install-configure.md)

[Linux 기반 Azure 가상 컴퓨터에 SSH(보안 셸) 연결 문제 해결](virtual-machines-linux-troubleshoot-ssh-connection.md)

[Azure 가상 컴퓨터에서 실행 중인 응용 프로그램에 대한 액세스 문제 해결](virtual-machines-linux-troubleshoot-app-connection.md)

<!---HONumber=AcomDC_0420_2016-->