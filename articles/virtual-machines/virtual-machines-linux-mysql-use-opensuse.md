<properties
	pageTitle="Azure에서 OpenSUSE Linux를 실행하는 가상 컴퓨터에 MySQL 설치"
	description="Azure에서 가상 컴퓨터에 MySQL을 설치하는 방법에 대해 알아봅니다."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""
	tags="mysql"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/22/2015"
	ms.author="kathydav"/>

# Azure에서 OpenSUSE Linux를 실행하는 가상 컴퓨터에 MySQL 설치

[MySQL][MySQL]은 인기 있는 오픈 소스 SQL 데이터베이스입니다. 이 자습서에서는 다음을 보여 줍니다.

- [Azure 관리 포털][AzurePortal]을 사용하여 Azure를 통해 사용할 수 있는 이미지에서 OpenSUSE Linux 가상 컴퓨터를 만드는 방법
- SSH 또는 PuTTY를 사용하여 가상 컴퓨터에 연결하는 방법
- 가상 컴퓨터에 MySQL을 설치하는 방법

[AZURE.INCLUDE [antares-iaas-signup-iaas](../../includes/antares-iaas-signup-iaas.md)]

## OpenSUSE Linux를 실행하는 가상 컴퓨터 만들기

[AZURE.INCLUDE [create-and-configure-opensuse-vm-in-portal](../../includes/create-and-configure-opensuse-vm-in-portal.md)]

##가상 컴퓨터에 MySQL 설치 및 실행

[AZURE.INCLUDE [install-and-run-mysql-on-opensuse-vm](../../includes/install-and-run-mysql-on-opensuse-vm.md)]

##요약
이 자습서에서는 OpenSUSE Linux를 실행하는 가상 컴퓨터를 만드는 방법 및 SSH 또는 PuTTY를 사용하여 가상 컴퓨터에 원격으로 연결하는 방법에 대해 알아보았습니다. 또한 Linux 가상 컴퓨터에서 MySQL을 설치하고 구성하는 방법에 대해서도 알아보았습니다. MySQL에 대한 자세한 정보는 [MySQL Documentation][MySQLDocs](영문)을 참조하십시오.

[MySQLDocs]: http://dev.mysql.com/doc/
[MySQL]: http://www.mysql.com
[AzurePortal]: http://manage.windowsazure.com
 

<!---HONumber=July15_HO1-->