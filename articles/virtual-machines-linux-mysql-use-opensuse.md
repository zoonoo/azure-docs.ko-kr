<properties linkid="virtual-machines-linux-mysql-use-opensuse" urlDisplayName="Install MongoDB" pageTitle="nstall MongoDB on a virtual machine running CentOS Linux in Azure" metaKeywords="Azure, MongoDB" description="Learn how to install Mongo DB on a virtual machine in Azure." metaCanonical="" services="" documentationCenter="" title="Install MongoDB on a virtual machine running CentOS Linux in Azure" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt" />

# Azure에서 OpenSUSE Linux를 실행하는 가상 컴퓨터에 MySQL 설치

[MySQL][MySQL]은 인기 있는 오픈 소스 SQL 데이터베이스입니다. [Azure 관리 포털][Azure 관리 포털]을 사용하여 이미지 갤러리에서 OpenSUSE Linux를 실행하는 가상 컴퓨터를 만들 수 있습니다. 그런 다음 가상 컴퓨터에서 MySQL 데이터베이스를 설치하고 구성할 수 있습니다.

이 자습서에서는 다음에 대해 알아봅니다.

-   관리 포털을 사용하여 갤러리에서 OpenSUSE Linux 가상 컴퓨터를 만드는 방법
-   SSH 또는 PuTTY를 사용하여 가상 컴퓨터에 연결하는 방법
-   가상 컴퓨터에 MySQL을 설치하는 방법

## Azure 구독 만들기

아직 Azure 구독이 없는 경우 [무료 평가판][무료 평가판]에 등록할 수 있습니다. 등록한 후 다음 단계에 따라 이 자습서를 계속합니다.

[WACOM.INCLUDE [antares-iaas-signup-iaas](../includes/antares-iaas-signup-iaas.md)]

## OpenSUSE Linux를 실행하는 가상 컴퓨터 만들기

[WACOM.INCLUDE [create-and-configure-opensuse-vm-in-portal](../includes/create-and-configure-opensuse-vm-in-portal.md)]

## 가상 컴퓨터에 MySQL 설치 및 실행

[WACOM.INCLUDE [install-and-run-mysql-on-opensuse-vm](../includes/install-and-run-mysql-on-opensuse-vm.md)]

## 요약

이 자습서에서는 OpenSUSE Linux를 실행하는 가상 컴퓨터를 만드는 방법 및 SSH 또는 PuTTY를 사용하여 가상 컴퓨터에 원격으로 연결하는 방법에 대해 알아보았습니다. 또한 Linux 가상 컴퓨터에서 MySQL을 설치하고 구성하는 방법에 대해서도 알아보았습니다. MySQL에 대한 자세한 내용은 [MySQL 설명서][MySQL 설명서](영문)를 참조하세요.

  [MySQL]: http://www.mysql.com
  [Azure 관리 포털]: http://manage.windowsazure.com
  [무료 평가판]: http://azure.microsoft.com
  [MySQL 설명서]: http://dev.mysql.com/doc/
