<properties
	pageTitle="Linux VM의 이미지 캡처 | Microsoft Azure"
	description="클래식 배포 모델을 사용하여 만든, Linux 기반 Azure VM(가상 컴퓨터)의 이미지를 캡처하는 방법을 알아봅니다."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/16/2015"
	ms.author="dkshir"/>


# Linux 가상 컴퓨터를 캡처하여 템플릿으로 사용하는 방법

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]이 문서에서는 클래식 배포 모델을 사용하여 이미지를 캡처하는 방법을 설명합니다.

이 문서에서는 Linux가 실행되는 Azure 가상 컴퓨터를 캡처하여 다른 가상 컴퓨터를 만들 때 템플릿처럼 사용하는 방법을 소개합니다. 이 템플릿에는 OS 디스크를 비롯해 가상 컴퓨터에 연결된 데이터 디스크가 포함됩니다. 네트워킹 구성은 포함되지 않으므로 템플릿을 사용하는 다른 가상 컴퓨터를 만들 때 구성해야 합니다.

Azure에서는 이 템플릿을 이미지로 처리하고 **이미지**에 저장합니다. 이 위치에 업로드한 이미지도 저장됩니다. 이미지에 대한 자세한 내용은 [Azure의 가상 컴퓨터 이미지 정보][]를 참조하세요.

## 시작하기 전에

이러한 단계는 이미 클래식 배포 모델을 사용하여 Azure 가상 컴퓨터를 만들었으며 데이터 디스크 연결을 비롯해 운영 체제 구성을 완료했다는 것을 전제로 합니다. 아직 완료하지 못한 경우 다음 지침을 참조하세요.

- [Linux를 실행하는 가상 컴퓨터를 만드는 방법][]


## 가상 컴퓨터 캡처

1. 선택한 SSH 클라이언트를 사용하여 가상 컴퓨터에 연결합니다. 자세한 내용은 [Linux를 실행하는 가상 컴퓨터에 로그온하는 방법][]을 참조하십시오.

2. SSH 창에서 다음 명령을 입력합니다. `waagent`의 출력은 이 유틸리티의 버전에 따라 약간씩 다를 수 있습니다.

	`sudo waagent -deprovision`

	이 명령은 시스템을 정리하여 다시 프로비전하기에 적합하도록 만듭니다. 이 작업은 다음 작업을 수행합니다.

	- SSH 호스트 키를 제거합니다(구성 파일에서 Provisioning.RegenerateSshHostKeyPair가 'y'인 경우).
	- /etc/resolv.conf의 Nameserver 구성을 지웁니다.
	- /etc/shadow에서 `root` 사용자의 암호를 제거합니다(구성 파일에서 Provisioning.DeleteRootPassword가 'y'인 경우).
	- 캐시된 DHCP 클라이언트 임대 제거
	- 호스트 이름을 localhost.localdomain으로 다시 설정
	- 마지막으로 프로비전된 사용자 계정 (/var/lib/waagent에서 얻은) **및 관련 데이터**를 삭제합니다.

	>[AZURE.NOTE]프로비저닝은 파일 및 데이터를 삭제하여 이미지를 "일반화"합니다. 새 이미지 서식 파일로 캡처할 가상 컴퓨터에서만 이 명령을 실행합니다. 하지만 이미지에서 중요한 정보가 모두 지워지고 이미지가 제3자에게 다시 배포하기에 적합하다고 보증할 수는 없습니다.


3. 계속하려면 **y**를 입력합니다. `-force` 매개 변수를 추가하여 이 확인 단계를 피할 수 있습니다.

4. **Exit**를 입력하여 SSH 클라이언트를 닫습니다.


	>[AZURE.NOTE]다음 단계에서는 클라이언트 컴퓨터에 이미 [Azure CLI를 설치](../xplat-cli-install.md)했다고 가정합니다. 아래의 모든 단계는 [관리 포털][]에서도 수행할 수 있습니다.

5. 클라이언트 컴퓨터에서 Azure CLI를 열고 Azure 구독에 로그인합니다. 자세한 내용은 [Azure CLI에서 Azure 구독에 연결](../xplat-cli-connect.md)을 참조하세요.

6. 서비스 관리 모드에 있는지 확인합니다.

	`azure config mode asm`

7. 다음을 사용하여 위 단계에서 이미 프로비전 해제된 가상 컴퓨터를 종료합니다.

	`azure vm shutdown <your-virtual-machine-name>`

	>[AZURE.NOTE]`azure vm list`를 사용하여 구독에서 만든 모든 가상 컴퓨터를 확인할 수 있습니다.

8. 가상 컴퓨터가 중지되면 다음 명령을 사용하여 이미지를 캡처합니다.

	`azure vm capture -t <your-virtual-machine-name> <new-image-name>`

	_new-image-name_ 대신 원하는 이미지 이름을 입력합니다. 이 명령은 일반화된 OS 이미지를 만듭니다. `-t` 하위 명령은 원래 가상 컴퓨터를 삭제합니다.

9.	이제 새 가상 컴퓨터를 구성하는 데 사용할 수 있는 이미지 목록에서 새 이미지를 사용할 수 있습니다. 다음 명령을 사용하여 볼 수 있습니다.

	`azure vm image list`

	[관리 포털][]의 **이미지**목록에 표시됩니다.

	![이미지 캡처 성공](./media/virtual-machines-linux-capture-image/VMCapturedImageAvailable.png)


## 다음 단계
가상 컴퓨터를 만들기 위한 템플릿으로 사용할 이미지가 마련됩니다. Azure CLI 명령 `azure vm create`를 사용하고 방금 만든 이미지 이름을 제공할 수 있습니다. 명령에 대한 자세한 내용은 [서비스 관리 API에서 Azure CLI 사용](virtual-machines-command-line-tools.md)을 참조하세요. 또는 [관리 포털][]에서 **갤러리에서** 방법을 사용하여 사용자 지정 가상 컴퓨터를 만들고 위에서 만든 이미지를 선택할 수 있습니다. 자세한 내용은 [사용자 지정 가상 컴퓨터를 만드는 방법][]을 참조하세요.

**참고 항목:** [Azure Linux 에이전트 사용자 가이드](virtual-machines-linux-agent-user-guide.md)

[관리 포털]: http://manage.windowsazure.com
[Linux를 실행하는 가상 컴퓨터에 로그온하는 방법]: virtual-machines-linux-how-to-log-on.md
[Azure의 가상 컴퓨터 이미지 정보]: http://msdn.microsoft.com/library/azure/dn790290.aspx
[사용자 지정 가상 컴퓨터를 만드는 방법]: virtual-machines-create-custom.md
[How to Attach a Data Disk to a Virtual Machine]: storage-windows-attach-disk.md
[Linux를 실행하는 가상 컴퓨터를 만드는 방법]: virtual-machines-linux-tutorial.md

<!---HONumber=Oct15_HO2-->