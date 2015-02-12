<properties urlDisplayName="Install MySQL" pageTitle="Azure에서 OpenSUSE Linux를 실행하는 가상 컴퓨터에 MySQL 설치" metaKeywords="Azure, MySQL" description="Azure에서 가상 컴퓨터에 MySQL을 설치하는 방법에 대해 알아봅니다." metaCanonical="" services="" documentationCenter="" title="Install MongoDB on a virtual machine running CentOS Linux in Azure" authors="kathydav" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="12/11/2014" ms.author="kathydav" />

# Azure에서 OpenSUSE Linux를 실행하는 가상 컴퓨터에 MySQL 설치

[MySQL][MySQL]은 인기 있는 오픈 소스 SQL 데이터베이스입니다.  [Azure 관리 포털][AzurePortal]을 사용하면 OpenSUSE Linux를 실행하는 가상 컴퓨터를 만들 수 있습니다.  그런 다음 가상 컴퓨터에서 MySQL 데이터베이스를 설치하고 구성할 수 있습니다.

이 자습서에서는 다음을 보여 줍니다.

- 관리 포털을 사용하여 Azure를 통해 사용할 수 있는 이미지에서 OpenSUSE Linux 가상 컴퓨터를 만드는 방법
- SSH 또는 PuTTY를 사용하여 가상 컴퓨터에 연결하는 방법
- 가상 컴퓨터에 MySQL을 설치하는 방법

[WACOM.INCLUDE [antares-iaas-signup-iaas](../includes/antares-iaas-signup-iaas.md)]

## OpenSUSE Linux를 실행하는 가상 컴퓨터 만들기

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal](../includes/create-and-configure-opensuse-vm-in-portal.md)]

## 가상 컴퓨터에 MySQL 설치 및 실행

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm](../includes/install-and-run-mysql-on-opensuse-vm.md)]

## 요약
이 자습서에서는 OpenSUSE Linux를 실행하는 가상 컴퓨터를 만드는 방법 및 SSH 또는 PuTTY를 사용하여 가상 컴퓨터에 원격으로 연결하는 방법에 대해 알아보았습니다.  또한 Linux 가상 컴퓨터에서 MySQL을 설치하고 구성하는 방법에 대해서도 알아보았습니다.  MySQL에 대한 자세한 내용은 [MySQL 설명서][MySQLDocs](영문)를 참조하세요.

[MySQLDocs]: http://dev.mysql.com/doc/
[MySQL]: http://www.mysql.com
[AzurePortal]: http://manage.windowsazure.com

<!--HONumber=35.1-->
