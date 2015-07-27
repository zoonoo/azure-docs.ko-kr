<properties 
	pageTitle="Linux 기반 Azure 가상 컴퓨터에 SSH(보안 셸) 연결 문제 해결" 
	description="Linux 기반 Azure 가상 컴퓨터에 액세스 할수 없는 경우, 문제의 원인을 분리해 내기 위해 다음 단계를 사용하세요."
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager"/>


<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/07/2015" 
	ms.author="josephd"/>

# Linux 기반 Azure 가상 컴퓨터에 SSH(보안 셸) 연결 문제 해결

Linux 기반 Azure 가상 컴퓨터에 액세스 할 수 없는 경우, 이 문서는 문제를 가려내고 수정하기 위한 체계적인 방법을 설명합니다.

## 1단계: SSH 구성, 키 또는 암호 재설정

가상 컴퓨터에서 [Linux 가상 컴퓨터의 암호 또는 SSH를 재설정하는 방법](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md)의 지침을 따릅니다. 이러한 지침을 사용하면 다음과 같은 작업을 수행할 수 있습니다.

- 암호 또는 SSH 키를 재설정합니다.
- 새 sudo 사용자 계정을 만듭니다.
- SSH 구성을 재설정합니다.

SSH 클라이언트에서 여전히 가상 컴퓨터의 SSH 서비스에 연결할 수 없는 경우 그 원인은 많을 수 있습니다. 다음은 관련된 구성 요소 집합입니다.
 
![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot1.png)

다음 섹션에서는 단계별로 이 문제의 다양한 원인을 개별적으로 확인하고 솔루션 및 해결 방법을 제공합니다.

## 2단계: 자세한 문제 해결 전 준비 단계

먼저, Azure 관리 포털 또는 Azure Preview 포털에서 가상 컴퓨터의 상태를 확인합니다.

Azure 관리 포털에서 다음을 수행합니다.

1. **가상 컴퓨터** > *VM 이름*을 클릭합니다.
2. 상태를 확인할 가상 컴퓨터의 **대시보드**를 클릭합니다.
3. **모니터**를 클릭하여 계산, 저장소 및 네트워크 리소스에 대한 최근 활동을 봅니다. 
4. **끝점**을 클릭하여 SSH 트래픽의 끝점이 있는지 확인합니다.

Azure Preview 포털에서 다음을 수행합니다.

1. **찾아보기** > **가상 컴퓨터** > *VM 이름*을 클릭합니다. 리소스 관리자에서 만들어진 가상 컴퓨터의 경우, **찾아보기** > **가상 컴퓨터(v2)** > *VM 이름*을 클릭합니다. 가상 컴퓨터에 대한 상태 창에 **실행 중**이 표시됩니다. 아래로 스크롤하여 계산, 저장소 및 네트워크 리소스에 대한 최근 활동을 표시합니다.
2. **설정**을 클릭하여 끝점, IP 주소 및 기타 설정을 확인합니다.

네트워크 연결을 확인하려면 구성된 끝점을 분석하고 HTTP 또는 알려진 다른 서비스와 같은 다른 프로토콜을 통해 가상 컴퓨터에 연결할 수 있는지 여부를 확인합니다.

필요한 경우 [가상 컴퓨터를 다시 시작](https://msdn.microsoft.com/library/azure/dn763934.aspx)하거나 [가상 컴퓨터의 크기를 조정](https://msdn.microsoft.com/library/dn168976.aspx)(영문)합니다.

이 단계 후 SSH 연결을 다시 시도해 보세요.

## 3단계: 자세한 문제 해결 

SSH 클라이언트가 Azure 가상 컴퓨터의 SSH 서비스에 연결할 수 없는 이유는 다음과 같은 문제 또는 잘못된 구성의 발생지 때문일 수 있습니다.

- SSH 클라이언트 컴퓨터
- 조직 에지 장치
- 클라우드 서비스 끝점 및 액세스 제어 목록(ACL)
- 네트워크 보안 그룹
- Linux 기반 Azure 가상 컴퓨터

### 발생지 1: SSH 클라이언트 컴퓨터

문제 또는 잘못된 구성의 발생지인 컴퓨터를 제거하려면 사용자의 컴퓨터가 다른 온-프레미스, Linux 기반 컴퓨터에 SSH 연결을 설정할 수 있는지 확인합니다.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot2.png)
 
연결할 수 없는 경우 컴퓨터에서 다음 사항을 확인합니다.

- 인바운드 또는 아웃 바운드 SSH 트래픽(TCP 22)을 차단하는 로컬 방화벽 설정
- SSH 연결을 방지하는 로컬에 설치된 클라이언트 프록시 소프트웨어
- SSH 연결을 방지하는 로컬에 설치된 네트워크 모니터링 소프트웨어
- 트래픽을 모니터링하거나 SSH 연결을 방지하는 특정 유형의 트래픽을 허용하거나 허용하지 않는 다른 유형의 보안 소프트웨어

이러한 모든 경우에 원인을 파악하기 위해 일시적으로 소프트웨어를 사용하지 않도록 설정하고 온-프레미스 컴퓨터에 SSH 연결을 시도합니다. 그런 다음 네트워크 관리자와 협력하여 SSH 연결을 허용하도록 소프트웨어의 설정을 수정합니다.

인증서 인증을 사용하는 경우 홈 디렉터리의 .ssh 폴더에 대해 다음 권한이 있는지 확인하세요.

- Chmod 700 ~/.ssh
- Chmod 644 ~/.ssh/*.pub
- Chmod 600 ~/.ssh/id_rsa(또는 개인 키를 저장할 수 있는 기타 파일)
- Chmod 644 ~/.ssh/known_hosts(SSH를 통해 연결한 호스트 포함)

### 발생지 2: 조직 에지 장치

문제 또는 잘못된 구성의 발생지인 조직 에지 장치를 제거하려면 인터넷에 직접 연결된 컴퓨터가 Azure 가상 컴퓨터에 SSH 연결을 설정할 수 있는지 확인합니다. 사이트 간 VPN 또는 Express 경로 연결을 통해 가상 컴퓨터에 액세스할 경우 [발생지 4: 네트워크 보안 그룹](#nsg)을 건너뜁니다.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot3.png)
 
인터넷에 직접 연결된 컴퓨터가 없는 경우 자체 리소스 그룹 또는 클라우드 서비스에서 새 Azure 가상 컴퓨터를 손쉽게 만들고 사용할 수 있습니다. 자세한 내용은 [Azure에서 Linux를 실행하는 가상 컴퓨터 만들기](virtual-machines-linux-tutorial.md)를 참조하세요. 테스트가 완료되면 리소스 그룹 또는 가상 컴퓨터 및 클라우드 서비스를 삭제합니다.

인터넷에 직접 연결된 컴퓨터에 대한 SSH 연결을 설정할 수 있는 경우 조직 에지 장치에서 다음을 확인합니다.

- 인터넷을 사용하여 SSH 트래픽을 차단하는 내부 방화벽
- SSH 연결을 방지하는 프록시 서버
- 경계 네트워크의 장치에서 실행되는, SSH 연결을 방지하는 침입 탐지 또는 네트워크 모니터링 소프트웨어

네트워크 관리자와 협력하여 인터넷에서 SSH 트래픽을 허용하도록 조직 에지 장치 설정을 수정합니다.

### 발생지 3: 클라우드 서비스 끝점 및 ACL

서비스 관리자에서 만들어진 가상 컴퓨터에 대한 문제 또는 잘못된 구성의 발생지인 클라우드 서비스 끝점 및 ACL을 제거하려면 동일한 가상 네트워크에 있는 다른 Azure 가상 컴퓨터가 Azure 가상 컴퓨터에 대한 SSH 연결을 설정할 수 있는지 확인합니다.
 
![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot4.png)

> [AZURE.NOTE]리소스 관리자에서 만든 가상 컴퓨터의 경우, [소스 4: 네트워크 보안 그룹](#nsg)으로 건너뜁니다.

동일한 가상 네트워크에 다른 가상 컴퓨터가 없는 경우 새 가상 컴퓨터를 손쉽게 만들 수 있습니다. 자세한 내용은 [Azure에서 Linux를 실행하는 가상 컴퓨터 만들기](virtual-machines-linux-tutorial.md)를 참조하세요. 테스트가 완료되면 추가한 가상 컴퓨터를 삭제합니다.

동일한 가상 네트워크의 가상 컴퓨터에 대한 SSH 연결을 만들 수 있는 경우 다음을 확인합니다.

- 대상 가상 컴퓨터의 SSH 트래픽에 대한 끝점 구성. 끝점의 개인 TCP 포트는 가상 컴퓨터에서 SSH 서비스를 수신하는 TCP 포트(기본값 22)와 일치해야 합니다. 템플릿으로 리소스 관리자 내부에서 만들어진 가상 컴퓨터의 경우, Azure Preview 포털에서 **찾아보기** > **가상 컴퓨터(v2)** > *VM 이름* > **설정** > **끝점**을 사용하여 SSH TCP 포트 번호를 확인합니다.
- 대상 가상 컴퓨터의 SSH 트래픽 끝점에 대한 ACL. ACL은 인터넷에서 들어오는 트래픽을 원본 IP 주소에 따라 허용 또는 거부하도록 지정하는 데 사용됩니다. ACL이 잘못 구성될 경우 끝점에 SSH 트래픽이 들어오지 못할 수 있습니다. ACL을 살펴보고 프록시 또는 다른 에지 서버의 공용 IP 주소에서 들어오는 트래픽이 허용되어 있는지 확인하세요. 자세한 내용은 [네트워크 ACL(액세스 제어 목록) 정보](https://msdn.microsoft.com/library/azure/dn376541.aspx)를 참조하세요.

문제의 발생지인 끝점을 제거하려면 현재 끝점을 제거하고 **SSH** 이름(공용 및 개인 포트 번호에 TCP 포트 22)을 지정하면서 새 끝점을 만듭니다. 자세한 내용은 [Azure의 가상 컴퓨터에 끝점 설정](virtual-machines-set-up-endpoints.md)을 참조하세요.

### <a id="nsg"></a>발생지 4: 네트워크 보안 그룹

네트워크 보안 그룹은 허용되는 인바운드 및 아웃바운드 트래픽을 더 세부적으로 제어하는 데 사용됩니다. Azure 가상 네트워크의 서브넷 및 클라우드 서비스에 적용되는 규칙을 만들 수 있습니다. 네트워크 보안 그룹 규칙을 살펴보고 인터넷으로 나가고 들어오는 SSH 트래픽이 허용되어 있는지 확인하세요. 자세한 내용은 [네트워크 보안 그룹 정보](../traffic-manager/virtual-networks-nsg.md)를 참조하세요.

### 발생지 5: Linux 기반 Azure 가상 컴퓨터 

마지막 가능한 문제 발생지는 Azure 가상 컴퓨터 자체입니다.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot5.png)
 
아직 그렇게 하지 않은 경우 가상 컴퓨터에서 [Linux 가상 컴퓨터의 암호 또는 SSH를 재설정하는 방법](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md)의 지침을 따르세요.

컴퓨터에서 연결을 다시 시도하세요. 실패할 경우 다음과 같은 문제 때문일 수 있습니다.

- SSH 서비스가 대상 가상 컴퓨터에서 실행되고 있지 않습니다.
- SSH 서비스가 TCP 포트 22에서 수신 대기하고 있지 않습니다. 이를 테스트하려면 로컬 컴퓨터에서 텔넷 클라이언트를 설치하고 "telnet *cloudServiceName*. cloudapp.net 22"를 실행하세요. 그렇게 하면 가상 컴퓨터가 SSH 끝점에 대한 인바운드 및 아웃바운드 통신을 허용하는지 여부가 확인됩니다.
- 대상 가상 컴퓨터의 로컬 방화벽에 인바운드 또는 아웃바운드 SSH 트래픽을 방지하는 규칙이 있습니다.
- Azure 가상 컴퓨터에서 실행 중인 침입 탐지 또는 네트워크 모니터링 소프트웨어가 SSH 연결을 방지하고 있습니다.


## 4단계: Azure 지원 포럼에 문제 제출

MSDN Azure 또는 스택 오버플로 포럼에 문제를 제출하면 전 세계 Azure 전문가로부터 도움을 받을 수 있습니다. 자세한 내용은 [Microsoft Azure 포럼](http://azure.microsoft.com/support/forums/)을 참조하세요.

## 5단계: Azure 기술 지원 인시던트 제출

이 문서의 1~4 단계를 수행하고 귀하의 문제를 Azure 지원 포럼에 제출했지만 여전히 SSH 연결을 만들 수 없는 경우 가상 컴퓨터를 다시 만들 수 있는지 여부를 고려해 볼 수 있습니다.

가상 컴퓨터를 다시 만들 수 없는 경우 Azure 기술 지원 인시던트를 제출하는 것이 좋을 수 있습니다.

인시던트를 제출하려면 [Azure 지원 사이트](http://azure.microsoft.com/support/options/)로 가서 **지원 받기**를 클릭합니다.

Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](http://azure.microsoft.com/support/faq/)를 참조하세요.

## 추가 리소스

[Linux 가상 컴퓨터의 암호 또는 SSH를 재설정하는 방법](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md)

[Windows 기반 Azure 가상 컴퓨터에 대한 원격 데스크톱 연결 문제 해결](virtual-machines-troubleshoot-remote-desktop-connections.md)

[Azure 가상 컴퓨터에서 실행 중인 응용 프로그램에 대한 액세스 문제 해결](virtual-machines-troubleshoot-access-application.md)
 

<!---HONumber=July15_HO2-->