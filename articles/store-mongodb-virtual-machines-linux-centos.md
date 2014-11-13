<properties urlDisplayName="Install MongoDB" pageTitle="Azure에서 CentOS Linux를 실행하는 가상 컴퓨터에 MongoDB 설치" metaKeywords="Azure, MongoDB" description="Azure에서 가상 컴퓨터에 MongoDB를 설치하는 방법에 대해 알아봅니다." metaCanonical="" services="" documentationCenter="" title="Azure에서 CentOS Linux를 실행하는 가상 컴퓨터에 MongoDB 설치" authors="bbenz, MSOpenTech" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="" ms.topic="article" ms.date="01/01/1900" ms.author="bbenz, MSOpenTech" />

# Azure에서 CentOS Linux를 실행하는 가상 컴퓨터에 MongoDB 설치

[MongoDB][MongoDB]는 대중적인 오픈 소스의 고성능 NoSQL 데이터베이스입니다. [Azure 관리 포털][Azure 관리 포털]을 사용하면 CentOS Linux를 실행하는 가상 컴퓨터를 이미지 갤러리에서 만들 수 있습니다. 그런 다음, MongoDB 데이터베이스를 가상 컴퓨터에 설치하고 구성할 수 있습니다.

다음 내용을 배웁니다.

-   관리 포털을 사용하여 CentOS Linux를 실행하는 가상 컴퓨터를 이미지 갤러리에서 만드는 방법
-   SSH 또는 PuTTY를 사용하여 가상 컴퓨터에 연결하는 방법
-   가상 컴퓨터에 MongoDB를 설치하는 방법

## CentOS Linux를 실행하는 가상 컴퓨터 만들기

[WACOM.INCLUDE [create-and-configure-centos-vm-in-portal](../includes/create-and-configure-centos-vm-in-portal.md)]

## 가상 컴퓨터에서 MongoDB 설치 및 실행

[WACOM.INCLUDE [install-and-run-mongo-on-centos-vm](../includes/install-and-run-mongo-on-centos-vm.md)]

## 요약

이 자습서에서는 Linux 가상 컴퓨터를 만들고 SSH 또는 PuTTY를 사용하여 가상 컴퓨터에 원격으로 연결하는 방법을 배웠습니다. 또한 MongoDB를 Linux 가상 컴퓨터에 설치하고 구성하는 방법도 배웠습니다. MongoDB에 대한 자세한 내용은 [MongoDB 설명서][MongoDB 설명서](영문)를 참조하세요.

  [MongoDB]: http://www.mongodb.org/
  [Azure 관리 포털]: http://manage.windowsazure.com
  [MongoDB 설명서]: http://www.mongodb.org/display/DOCS/Home
