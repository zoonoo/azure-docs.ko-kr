<properties 
	pageTitle="Chef 및 Azure 가상 컴퓨터 정보" 
	description="Azure에서 VM에 Chef를 설치 및 구성하는 방법에 대해 설명합니다." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/20/2015" 
	ms.author="kathydav"/>

#Chef 및 Azure 가상 컴퓨터 정보

Chef는 인프라를 구축, 배포 및 관리하기 위한 자동화 시스템을 제공합니다. 리소스는 웹 서버 구성과 같은 작업에 대한 지침을 제공하는 재사용 가능한 정의인 레시피를 사용하여 관리됩니다.

Chef는 클라이언트-서버 시스템입니다. Chef 서버 사용을 위한 옵션을 확인하려면 [설치 선택](http://www.getchef.com/chef/choose-your-version/)(영문)을 참조하세요. 클라이언트를 설치하려면 Chef 서버에 대한 정보가 필요합니다.

Azure 가상 컴퓨터에 Chef 클라이언트를 설치하려면 다음 방법을 사용할 수 있습니다.

- Windows Server 2012 또는 Windows Server 2012 R2가 실행되는 가상 컴퓨터를 만들 때 관리 포털을 사용하여 Chef 클라이언트를 설치합니다. 지침은 [Microsoft Azure 포털](https://docs.chef.io/azure_portal.html)(영문)을 참조하세요.
- Azure PowerShell을 사용하여 기존 가상 컴퓨터에 Chef 클라이언트를 설치합니다. GitHub에서 샘플 [스크립트](https://gist.github.com/kaustubh-d/cea1aa75baebd3615609)를 사용할 수 있습니다.
- Chef 플러그 인, [knife-azure](http://docs.getchef.com/plugin_knife_azure.html)를 사용하여 가상 컴퓨터 인스턴스를 만들고 Chef 클라이언트를 설치합니다.   


##추가 리소스
[Chef 및 Microsoft Azure]

[Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법]

[Linux를 실행하는 가상 컴퓨터에 로그온하는 방법]

[확장 관리]

<!--Link references-->
[Chef 및 Microsoft Azure]: http://www.getchef.com/solutions/azure/
[Windows Server를 실행하는 가상 컴퓨터에 로그온하는 방법]: virtual-machines-log-on-windows-server.md
[Linux를 실행하는 가상 컴퓨터에 로그온하는 방법]: virtual-machines-linux-how-to-log-on.md
[확장 관리]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409


 

<!---HONumber=July15_HO4-->