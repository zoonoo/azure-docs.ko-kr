<properties
	pageTitle="Azure 포털에서 Linux를 실행하는 Azure 가상 컴퓨터 만들기"
	description="Azure 포털을 사용하여 Azure 리소스 그룹으로 Linux를 실행하는 Azure 가상 컴퓨터(VM)을 만듭니다."
	services="virtual-machines"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/13/2015"
	ms.author="rasquill"/>

# Azure Preview 포털에서 Linux를 실행하는 가상 컴퓨터 만들기

> [AZURE.SELECTOR]
- [Azure CLI](virtual-machines-linux-tutorial.md)
- [Azure Preview Portal](virtual-machines-linux-tutorial-portal-rm.md)

Linux를 실행하는 Azure 가상 컴퓨터(VM) 만들기는 쉽습니다. 이 자습서는 Azure Preview 포털을 사용하여 신속하게 VM을 만들고 `~/.ssh/id_rsa.pub` 공개 키 파일을 사용하여 VM에 대한 **SSH** 연결의 보안을 유지하는 방법을 보여줍니다. [사용자 고유의 이미지를 템플릿](virtual-machines-linux-create-upload-vhd.md)으로 사용하여 Linux VM을 만들 수도 있습니다.

> [AZURE.NOTE]이 자습서에서는 Azure 리소스 그룹 API에 의해 관리되는 Azure 가상 컴퓨터를 만듭니다. 자세한 내용은 [ Azure 리소스 그룹 개요](resource-group-overview.md)를 참조하세요.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## 이미지 선택

Preview 포털의 Azure 마켓플레이스로 이동하여 원하는 Windows Server VM 이미지를 찾습니다.

1. [Preview 포털](https://portal.azure.com)에 로그인합니다.

2. 허브 메뉴에서 **새로 만들기** > **계산** > **Ubuntu Server 14.04 LTS**를 클릭합니다.

	![VM 이미지 선택](media/virtual-machines-linux-tutorial-portal-rm/chooseubuntuvm.png)

	> [AZURE.TIP]다른 이미지를 찾으려면 **마켓플레이스**를 클릭하고 사용 가능한 항목을 검색하거나 필터링합니다.

3. **Ubuntu Server 14.04 LTS** 페이지의 맨 아래에서 **리소스 관리자 스택 사용**을 선택하여 Azure 리소스 관리자에서 VM을 만듭니다. 대부분의 새 작업에 대해 리소스 관리자 스택을 사용하는 것이 좋습니다. 고려 사항은 [Azure 리소스 관리자의 Azure 계산, 네트워크 및 저장소 공급자](virtual-machines-azurerm-versus-azuresm.md)를 참조하세요.

4. 그런 다음 ![만들기 단추](media/virtual-machines-linux-tutorial-portal-rm/createbutton.png) 단추를 클릭합니다.

	![리소스 관리자 계산 스택으로 변경](media/virtual-machines-linux-tutorial-portal-rm/changetoresourcestack.png)

## 가상 컴퓨터 만들기

이미지를 선택한 후에는 대부분의 구성에서 Azure 기본 설정을 사용할 수 있고 신속하게 VM을 만들 수 있습니다.

1. **가상 컴퓨터 만들기** 블레이드에서 **기본 사항**을 클릭합니다. VM에 사용할 **이름**과 공개 키 파일(이 경우 `~/.ssh/id_rsa.pub` 파일에서 **ssh-rsa** 형식으로)을 입력합니다. 구독이 둘 이상인 경우 새 VM에 대해 하나를 지정하고 새로운 또는 기존 **리소스 그룹** 및 Azure 데이터 센터 **위치**를 지정합니다.

	![](media/virtual-machines-linux-tutorial-portal-rm/step-1-thebasics.png)

	> [AZURE.NOTE]여기에서 사용자 이름/암호 인증을 선택할 수 있고 공개 및 개인 키 교환으로 **ssh** 세션의 보안을 설정하지 않으려는 경우 해당 정보를 입력할 수 있습니다.

2. **크기**를 클릭하고 요구에 적합한 VM 크기를 선택합니다. 각각의 크기는 계산 코어, 메모리 및 기타 기능(예: 프리미엄 저장소에 대한 지원)의 수를 지정하며 가격에 영향을 미칩니다. Azure는 선택하는 이미지에 따라 특정 크기를 자동으로 권장합니다. 완료하면 ![선택 단추](media/virtual-machines-linux-tutorial-portal-rm/selectbutton-size.png) 단추를 클릭합니다.

	>[AZURE.NOTE]프리미엄 저장소는 특정 지역에서 DS 시리즈 가상 컴퓨터에 대해 사용할 수 있습니다. 프리미엄 저장소는 데이터베이스와 같은 데이터 집약적인 작업에 대해 최상의 저장소 옵션입니다. 자세한 내용은 [프리미엄 저장소: Azure 가상 컴퓨터 작업을 위한 고성능 저장소](storage-premium-storage-preview-portal.md)를 참조하세요.

3. **설정**을 클릭하여 새 VM에 대한 저장소 및 네트워킹 설정을 봅니다. 첫 번째 VM에 대해 일반적으로 기본 설정을 적용할 수 있습니다. 지원하는 VM 크기를 선택한 경우 **디스크 유형** 아래에서 **프리미엄(SSD)**을 선택하여 프리미엄 저장소를 사용해 볼 수 있습니다. 완료하면 ![확인 단추](media/virtual-machines-linux-tutorial-portal-rm/okbutton.png) 단추를 클릭합니다.

	![](media/virtual-machines-linux-tutorial-portal-rm/step-3-settings.png)

6. **요약**을 클릭하여 구성 선택 사항을 검토합니다. 설정 검토 또는 업데이트가 완료되면 ![확인 단추](media/virtual-machines-linux-tutorial-portal-rm/createbutton.png) 단추를 클릭합니다.

	![만들기 요약](media/virtual-machines-linux-tutorial-portal-rm/summarybeforecreation.png)

8. Azure에서 VM을 만드는 동안 허브 메뉴의 **알림**에서 진행 상황을 확인할 수 있습니다. Azure에서 VM을 만든 후에, **가상 컴퓨터 만들기** 블레이드에서 **시작 보드에 고정**을 해제하지 않으면, 시작 보드에서 해당 VM을 볼 수 있습니다.

	> [AZURE.NOTE]요약에는 서비스 관리 계산 스택을 사용하여 클라우드 서비스 내부에 VM이 만들어진 때와 같은 방식으로 공용 DNS 이름이 포함되지 않습니다.

## **ssh**를 사용하여 Azure Linux VM에 연결

이제 표준 방식으로 **ssh**를 사용하여 Ubuntu VM에 연결할 수 있습니다. 하지만 VM에 대한 타일과 리소스를 열어서 Azure VM에 할당된 IP 주소를 검색해야 합니다. 이것은 **찾아보기**를 클릭한 후 **최근**을 선택하고 만들어 놓은 VM을 찾거나 시작 보드에서 만들어 놓은 타일을 클릭하여 수행할 수 있습니다. 두 가지 경우 모두 다음 그림에 표시된 **공용 IP 주소** 값을 찾아서 복사합니다.

![성공적인 만들기 요약](media/virtual-machines-linux-tutorial-portal-rm/successresultwithip.png)

이제 Azure VM에 대해 **ssh**를 실행할 수 있으며, 준비가 되었습니다.

	ssh ops@23.96.106.1 -p 22
	The authenticity of host '23.96.106.1 (23.96.106.1)' can't be established.
	ECDSA key fingerprint is bc:ee:50:2b:ca:67:b0:1a:24:2f:8a:cb:42:00:42:55.
	Are you sure you want to continue connecting (yes/no)? yes
	Warning: Permanently added '23.96.106.1' (ECDSA) to the list of known hosts.
	Welcome to Ubuntu 14.04.2 LTS (GNU/Linux 3.16.0-43-generic x86_64)

	 * Documentation:  https://help.ubuntu.com/

	  System information as of Mon Jul 13 21:36:28 UTC 2015

	  System load: 0.31              Memory usage: 5%   Processes:       208
	  Usage of /:  42.1% of 1.94GB   Swap usage:   0%   Users logged in: 0

	  Graph this data and manage this system at:
	    https://landscape.canonical.com/

	  Get cloud support with Ubuntu Advantage Cloud Guest:
	    http://www.ubuntu.com/business/services/cloud

	0 packages can be updated.
	0 updates are security updates.

	The programs included with the Ubuntu system are free software;
	the exact distribution terms for each program are described in the
	individual files in /usr/share/doc/*/copyright.

	Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
	applicable law.

	ops@ubuntuvm:~$


## 다음 단계

Azure의 Linux에 대한 자세한 내용은 다음 을 참조하세요.

- [Azure에서 Linux 및 오픈 소스 컴퓨팅](virtual-machines-linux-opensource.md)

- [Mac 및 Linux용 Azure 명령줄 도구를 사용하는 방법](virtual-machines-command-line-tools.md)

- [Linux용 Azure CustomScript 확장을 사용하여 LAMP 앱 배포](virtual-machines-linux-script-lamp.md)

- [Azure의 Linux용 Docker 가상 컴퓨터 확장](virtual-machines-docker-vm-extension.md)

<!---HONumber=July15_HO4-->