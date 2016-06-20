<properties
	pageTitle="Azure에서 Linux VM에 로그온 | Microsoft Azure"
	description="SSH(Secure Shell) 클라이언트를 사용하여 Linux를 실행하는 Azure 가상 컴퓨터에 로그온하는 방법에 대해 알아봅니다."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="squillace"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/18/2016"
	ms.author="rasquill"/>


#Linux를 실행하는 가상 컴퓨터에 로그온하는 방법 #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager deployment model](virtual-machines-linux-quick-create-portal.md).

가상 컴퓨터에 로그온하는 데 사용할 컴퓨터에 SSH 클라이언트를 설치해야 합니다. 선택할 수 있는 많은 SSH 클라이언트 프로그램이 있습니다. 가능한 선택 항목은 다음과 같습니다.

- Linux 운영 체제를 실행하는 가상 컴퓨터에서 경우 SSH(보안 셸) 클라이언트를 사용하여 로그온해야 합니다. 기본적으로 SSH 클라이언트가 이미 설치되어 있지 않으면 배포가 어렵습니다. Linux에 대한 자세한 내용은 [SSH를 사용하는 방법](virtual-machines-linux-ssh-from-linux.md)을 참조하세요.
- Windows 운영 체제를 실행하는 컴퓨터를 사용하는 경우 PuTTY 등의 SSH 클라이언트를 사용하는 것이 좋습니다. 자세한 내용은 [PuTTY 다운로드 페이지](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)를 참조하십시오.


>[AZURE.NOTE] 자세한 요구 사항 및 문제 해결 팁은 [RDP 또는 SSH를 사용하여 Azure 가상 컴퓨터에 연결](http://go.microsoft.com/fwlink/p/?LinkId=398294)을 참조하세요.

다음 절차는 OS X에서 SSH 클라이언트를 사용하여 가상 컴퓨터에 액세스하는 방법을 보여 줍니다.

1. **관리 포털**에서 **호스트 이름** 및 [포트 정보](http://manage.windowsazure.com)를 찾습니다. 가상 컴퓨터 대시보드에서 필요한 정보를 찾을 수 있습니다. 가상 컴퓨터 이름을 클릭하고 대시보드의 **간략 상태** 섹션에서 **SSH 세부 정보**를 찾습니다.

	![SSH 세부 정보 가져오기](./media/virtual-machines-linux-classic-log-on/portalsshdetails.png)

2. 적절할 호스트 이름과 포트와 함께 컴퓨터를 만들 때 지정한 계정을 사용하여 가상 컴퓨터에 로그온합니다. 사용자 이름 및 암호를 사용하여 가상 컴퓨터를 만드는 방법에 대한 자세한 내용은 [Linux를 실행하는 가상 컴퓨터 만들기](virtual-machines-linux-classic-createportal.md)를 참조하세요.

	![가상 컴퓨터에 로그온](./media/virtual-machines-linux-classic-log-on/sshport.png)

>[AZURE.NOTE] VMAccess 확장은 SSH 키 또는 암호를 잊어버린 경우 이를 재설정하는 데 도움이 될 수 있습니다. 사용자 이름을 잊어버린 경우 이 확장을 사용하여 sudo 인증 기관을 통해 새로 만들 수 있습니다. 지침은 [Linux 가상 컴퓨터에 대한 암호 또는 SSH를 재설정하는 방법]을 참조하세요.

이제 다른 서버처럼 가상 컴퓨터에서 작업할 수 있습니다.

<!-- LINKS -->
[Linux 가상 컴퓨터에 대한 암호 또는 SSH를 재설정하는 방법]: http://go.microsoft.com/fwlink/p/?LinkId=512138

<!---HONumber=AcomDC_0608_2016-->