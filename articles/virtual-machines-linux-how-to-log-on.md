<properties 
	pageTitle="Azure에서 Linux를 실행하는 가상 컴퓨터에 로그온" 
	description="SSH(Secure Shell) 클라이언트를 사용하여 Linux를 실행하는 Azure 가상 컴퓨터에 로그온하는 방법에 대해 알아봅니다." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/12/2014" 
	ms.author="kathydav"/>




#Linux를 실행하는 가상 컴퓨터에 로그온하는 방법 #

Linux 운영 체제를 실행하는 가상 컴퓨터의 경우 SSH(보안 셸) 클라이언트를 사용하여 로그온합니다.

가상 컴퓨터에 로그온하는 데 사용할 컴퓨터에 SSH 클라이언트를 설치해야 합니다. 선택할 수 있는 많은 SSH 클라이언트 프로그램이 있습니다. 가능한 선택 항목은 다음과 같습니다.

- Windows 운영 체제를 실행하는 컴퓨터를 사용하는 경우 PuTTY 등의 SSH 클라이언트를 사용하는 것이 좋습니다. 자세한 내용은 [PuTTY 다운로드 페이지](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)를 참조하세요.
- Linux 운영 체제를 실행하는 컴퓨터를 사용하는 경우 OpenSSH 등의 SSH 클라이언트를 사용하는 것이 좋습니다. 자세한 내용은 [OpenSSH](http://www.openssh.org/)를 참조하세요.

>[AZURE.NOTE] 자세한 요구 사항 및 문제 해결 팁은 [RDP 또는 SSH를 사용하여 Azure 가상 컴퓨터에 연결](http://go.microsoft.com/fwlink/p/?LinkId=398294)을 참조하세요. 

다음 절차는 PuTTY 프로그램을 사용하여 가상 컴퓨터에 액세스하는 방법을 보여 줍니다.

1. [관리 포털](http://manage.windowsazure.com)에서 **호스트 이름** 및 **포트 정보**를 찾습니다. 가상 컴퓨터 대시보드에서 필요한 정보를 찾을 수 있습니다. 가상 컴퓨터 이름을 클릭하고 대시보드의 **간략 상태** 섹션에서 **SSH 세부 정보**를 찾습니다.

	![Obtain SSH details](./media/virtual-machines-linux-how-to-log-on/sshdetails.png)

2. PuTTY 프로그램을 엽니다.

3. 대시보드에서 수집한 호스트 이름 및 포트 정보를 입력하고 **열기**를 클릭합니다.

	![Open PuTTY](./media/virtual-machines-linux-how-to-log-on/putty.png)

4. 컴퓨터를 만들 때 지정한 계정을 사용하여 가상 컴퓨터에 로그온합니다. 사용자 이름 및 암호를 사용하여 가상 컴퓨터를 만드는 방법에 대한 자세한 내용은 [Linux를 실행하는 가상 컴퓨터 만들기](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-tutorial/)를 참조하세요. 

	![Log on to the virtual machine](./media/virtual-machines-linux-how-to-log-on/sshlogin.png)

>[AZURE.NOTE] VMAccess 확장은 SSH 키 또는 암호를 잊어버린 경우 이를 재설정하는 데 도움이 될 수 있습니다. 사용자 이름을 잊어버린 경우 이 확장을 사용하여 sudo 인증 기관을 통해 새로 만들 수 있습니다. 지침에 대해서는 [Linux 가상 컴퓨터에 대한 암호 또는 SSH를 다시 설정하는 방법]을 참조하세요. 
	
이제 다른 서버처럼 가상 컴퓨터에서 작업할 수 있습니다.

<!-- LINKS -->
[Linux 가상 컴퓨터에 대한 암호 또는 SSH를 다시 설정하는 방법]: http://go.microsoft.com/fwlink/p/?LinkId=512138

<!--HONumber=45--> 
