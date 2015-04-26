<properties 
	pageTitle="Azure의 Linux 소개 - Azure 자습서" 
	description="Azure에서 Linux 가상 컴퓨터를 사용하는 방법에 대해 알아봅니다." 
	services="virtual-machines" 
	documentationCenter="python" 
	authors="szarkos" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/13/2014" 
	ms.author="szark"/>





#Azure의 Linux 소개

이 항목에서는 Azure 클라우드에서 Linux 가상 컴퓨터를 사용하는 몇 가지 측면을 간략하게 설명합니다. 갤러리의 기존 이미지를 사용하여 Linux 가상 컴퓨터 배포는 간단한 프로세스입니다. 

## 목차 ##

* [인증: 사용자 이름, 암호 및 SSH 키](#authentication)
* [Linux 가상 컴퓨터 로그인용 SSH 키 생성 및 사용](#keygeneration)
* [sudo를 사용하여 superuser 권한 얻기](#superuserprivileges)
* [방화벽 구성](#firewallconfiguration)
* [호스트 이름 변경](#hostnamechanges)
* [가상 컴퓨터 이미지 캡처](#virtualmachine)
* [디스크 연결](#attachingdisks)

## <a id="authentication"></a>인증: 사용자 이름, 암호 및 SSH 키

Azure 관리 포털에서 Linux 가상 컴퓨터를 만들 경우 사용자 이름, 암호 및 SSH 공개 키(선택적으로)를 지정해야 합니다. Azure에 Linux 가상 컴퓨터를 배포할 때 선택하는 사용자 이름에는 다음과 같은 제약이 있습니다. 가상 컴퓨터에 이미 존재하던 시스템 계정의 이름(UID <100)(예:  'root')은 허용되지 않습니다.

 - [Azure에서 Linux와 함께 SSH를 사용하는 방법](linux-use-ssh-key.md) 참조


### <a id="keygeneration"></a>SSH 키 생성

현재 버전의 관리 포털은 X509 인증서에 캡슐화된 SSH 공개 키만 허용합니다. SSH 키를 생성하고 Azure에 사용하려면 아래 단계를 따르세요.

1. openssl을 사용하여 2048비트 RSA 키 쌍으로 X509 인증서를 생성합니다.
		
		openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

	openssl에서 확인하는 몇 가지 질문에 대답합니다(비워 둘 수도 있음). 이러한 필드의 콘텐츠는 플랫폼에서 사용되지 않습니다.

2. 개인 키의 사용 권한을 변경하여 보안을 설정합니다.

		chmod 600 myPrivateKey.key

3.  `myCert.pem`을  `myCert.cer`(DER 인코딩된 X509 인증서)로 변환합니다.

		openssl  x509 -outform der -in myCert.pem -out myCert.cer

4. Linux 가상 컴퓨터를 만드는 동안  `myCert.cer`을 업로드합니다. 프로비저닝 프로세스에서 가상 컴퓨터의 지정된 사용자에 대한 `~/.ssh/authorized_keys` 파일에 이 인증서의 공개 키를 자동으로 설치합니다.

5. ssh를 사용하여 Linux 가상 컴퓨터에 연결합니다.

		ssh -i  myPrivateKey.key -p port  username@servicename.cloudapp.net

	처음 로그인할 때 호스트의 공개 키 지문을 허용하라는 메시지가 표시됩니다.

6. 필요한 경우  `myPrivateKey.key`를 `~/.ssh/id_rsa`로 복사할 수 있습니다. 그러면 -i 옵션을 사용하지 않아도 openssh 클라이언트가 이 파일을 자동으로 선택할 수 있습니다.
   가상 컴퓨터의 섹션을 포함하도록 `~/.ssh/config`를 수정할 수도 있습니다.

        Host servicename.cloudapp.net
          IdentityFile %d/.ssh/myPrivateKey.key


### 기존 OpenSSH 호환 키에서 키 생성
이전 예제에서는 Azure와 함께 사용할 새 키를 만드는 방법을 설명합니다. 사용자에게 기존의 OpenSSH 호환 공용 키 및 개인 키 쌍이 이미 있고 동일한 키를 Azure에 사용하려는 경우도 있을 수 있습니다.

OpenSSH 개인 키는  `openssl` 유틸리티를 통해 직접 읽을 수 있습니다. 다음 명령은 기존 SSH 개인 키(아래 예제에서는 id_rsa)를 사용하고 Azure에 필요한  `.pem` 공개 키를 만듭니다.

	# openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

**myCert.pem** 파일은 Azure에 Linux 가상 컴퓨터를 프로비전하는 데 사용할 수 있는 공개 키입니다. 프로비전하는 동안  `.pem` 파일이  `openssh` 호환 공개 키로 변환되고 `~/.ssh/authorized_keys`에 저장됩니다.


## <a id="superuserprivileges"></a> `sudo`를 사용하여 Superuser 권한 얻기:

Azure에서 가상 컴퓨터 인스턴스를 배포하는 동안 지정한 사용자 계정이 권한 있는 계정입니다. Azure Linux 에이전트에서  `sudo` 유틸리티를 사용하여 루트(superuser 계정)로 권한을 상승하도록 이 계정을 구성할 수 있습니다. 이 사용자 계정을 사용하여 로그인한 후 다음 명령 구문을 사용하여 루트로 명령을 실행할 수 있습니다.

	# sudo <COMMAND>

선택적으로 **sudo -s**를 사용하여 루트 셸을 얻을 수 있습니다.

- [Azure의 Linux 가상 컴퓨터에서 루트 권한 사용](virtual-machines-linux-use-root-privileges.md) 참조


## <a id="firewallconfiguration"></a>방화벽 구성

Azure는 관리 포털에서 지정한 포트에 대한 연결을 제한하는 인바운드 패킷 필터를 제공합니다. 기본적으로 허용되는 유일한 포트는 SSH입니다. 관리 포털에서 끝점을 구성하여 Linux 가상 컴퓨터의 추가 포트에 액세스할 수 있습니다.

 - 다음을 참조하세요. [가상 컴퓨터에 끝점을 설정하는 방법](virtual-machines-set-up-endpoints.md)

Azure 갤러리의 Linux 이미지는 기본적으로  *iptables* 방화벽을 사용하도록 설정하지 않습니다. 원하는 경우 추가 필터링을 제공하도록 이 방화벽을 구성할 수 있습니다.


## <a id="hostnamechanges"></a>호스트 이름 변경

Linux 이미지의 인스턴스를 처음 배포할 때 가상 컴퓨터의 호스트 이름을 지정해야 합니다. 가상 컴퓨터를 실행하면 이 호스트 이름이 플랫폼 DNS 서버에 게시되므로 서로 연결된 여러 가상 컴퓨터에서 호스트 이름을 사용하여 IP 주소를 조회할 수 있습니다.

가상 컴퓨터를 배포한 후 호스트 이름을 변경해야 하는 경우 다음 명령을 사용하세요.

	# sudo hostname <newname>

Azure Linux 에이전트에는 이 이름 변경을 자동으로 검색하여 이 변경 내용을 기억하도록 가상 컴퓨터를 적절히 구성하고 플랫폼 DNS 서버에 이 변경 내용을 게시하는 기능이 포함되어 있습니다.

 - [Azure Linux 에이전트 사용자 가이드](virtual-machines-linux-agent-user-guide.md)

### Ubuntu 이미지
Ubuntu 이미지는 cloud-init를 활용하여 가상 컴퓨터를 부트스트랩하기 위한 추가 기능을 제공합니다.

 - [Microsoft Azure의 사용자 지정 데이터 및 Cloud-Init](http://azure.microsoft.com/blog/2014/04/21/custom-data-and-cloud-init-on-windows-azure/) 참조


## <a id="virtualmachine"></a>가상 컴퓨터 이미지 캡처

Azure는 기존 가상 컴퓨터의 상태를 이미지로 캡처하는 기능을 제공합니다. 이 이미지는 이후에 추가 가상 컴퓨터 인스턴스를 배포하는 데 사용할 수 있습니다. Azure Linux 에이전트는 프로비전 프로세스 중 수행된 일부 사용자 지정을 롤백하는 데 사용할 수 있습니다. 아래 단계를 따르면 가상 컴퓨터를 이미지로 캡처할 수 있습니다.

1. **waagent -deprovision**을 실행하여 사용자 지정 프로비전을 실행 취소합니다. 또는 선택적으로 프로비전 중 지정한 사용자 계정 및 연결된 모든 데이터를 삭제하려면 **waagent -deprovision+user**를 실행합니다.

2. 가상 컴퓨터를 종료합니다.

3. 관리 포털에서  *Capture*를 클릭하거나 Powershell 또는 CLI 도구를 사용하여 가상 컴퓨터를 이미지로 캡처합니다.

 - 다음을 참조하세요. [Linux 가상 컴퓨터를 캡처하여 템플릿으로 사용하는 방법](virtual-machines-linux-capture-image.md)


## <a id="attachingdisks"></a>디스크 연결

각 가상 컴퓨터에는 임시 로컬  *리소스 디스크*가 연결되어 있습니다. 리소스 디스크의 데이터는 다시 부팅 후 지속되지 않을 수도 있으므로 가상 컴퓨터에서 실행되는 응용 프로그램 및 프로세스에서 **임시** 데이터 저장소에 사용되는 경우가 많습니다. 또한 운영 체제의 페이지 또는 스왑 파일을 저장하는 데 사용됩니다.

Linux에서 리소스 디스크는 일반적으로 Azure Linux 에이전트에 의해 관리되며 **/mnt/resource**(또는 Ubuntu 이미지의 **/mnt**)에 자동으로 탑재됩니다.

>[AZURE.NOTE] 리소스 디스크는 **임시** 디스크이며 VM의 프로비전을 해제할 때 비워질 수 있습니다.

Linux에서 데이터 디스크 이름은 커널에 의해  `/dev/sdc`로 지정될 수 있으며 사용자는 해당 리소스를 파티셔닝, 형식 지정 및 마운트해야 합니다. 이 내용은 자습서의 다음 부분에서 단계별로 설명됩니다. [가상 컴퓨터에 데이터 디스크를 연결하는 방법](virtual-machines-linux-how-to-attach-disk.md).

 - 참고 항목: [Linux에서 소프트웨어 RAID 구성](virtual-machines-linux-configure-raid.md)




<!--HONumber=42-->
