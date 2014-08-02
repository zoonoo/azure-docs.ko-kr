<properties linkid="manage-windows-common-task-mongodb-vm" urlDisplayName="Install MongoDB" pageTitle="Install MongoDB on a Windows Server virtual machine" metaKeywords="Azure vm, Azure MongoDB, Azure remote desktop" description="Learn how to create an Azure virtual machine with Windows Server 2008 R2, and then use Remote Desktop to install MongoDB." metaCanonical="" services="virtual-machines" documentationCenter="" title="Install MongoDB on a virtual machine running Windows Server in Azure" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

Azure에서 Windows Server를 실행하는 가상 컴퓨터에 MongoDB 설치
==============================================================

[MongoDB](http://www.mongodb.org/)는 대중적인 오픈 소스의 고성능 NoSQL 데이터베이스입니다. [Azure 관리 포털](http://manage.windowsazure.com)을 사용하면 Windows Server를 실행하는 가상 컴퓨터를 이미지 갤러리에서 만들 수 있습니다. 그런 다음, MongoDB 데이터베이스를 가상 컴퓨터에 설치하고 구성할 수 있습니다.

이 자습서에서는 다음에 대해 알아봅니다.

-   관리 포털을 사용하여 Windows Server 가상 컴퓨터를 갤러리에서 만드는 방법
-   원격 데스크톱을 사용하여 가상 컴퓨터에 연결하는 방법
-   가상 컴퓨터에 MongoDB를 설치하는 방법

Windows Server 2008 R2를 실행하는 가상 컴퓨터 만들기
----------------------------------------------------

[WACOM.INCLUDE [create-and-configure-windows-server-2008-vm-in-portal](../includes/create-and-configure-windows-server-2008-vm-in-portal.md)]

데이터 디스크 연결
------------------

[WACOM.INCLUDE [attach-data-disk-windows-server-2008-vm-in-portal](../includes/attach-data-disk-windows-server-2008-vm-in-portal.md)]

가상 컴퓨터에서 MongoDB 설치 및 실행
------------------------------------

[WACOM.INCLUDE [install-and-run-mongo-on-win2k8-vm](../includes/install-and-run-mongo-on-win2k8-vm.md)]

요약
----

이 자습서에서는 Windows Server 가상 컴퓨터를 만들고 가상 컴퓨터에 원격으로 연결하는 방법을 배웠습니다. 또한 MongoDB를 Windows 가상 컴퓨터에 설치하고 구성하는 방법도 배웠습니다. MongoDB에 대한 자세한 내용은 [MongoDB 설명서](http://www.mongodb.org/display/DOCS/Home)(영문)를 참조하십시오.

