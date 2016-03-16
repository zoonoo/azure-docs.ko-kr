<properties
	pageTitle="Windows VM에 MongoDB 설치 | Microsoft Azure"
	description="클래식 배포 모델을 사용하여 만든, Windows Server를 실행하는 Azure VM에 MongoDB를 설치하는 방법을 알아봅니다."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/14/2015"
	ms.author="dkshir"/>

#Windows VM에 MongoDB 설치

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]리소스 관리자 모델.


[MongoDB][MongoDB]는 인기 있는 오픈 소스 고성능 NoSQL 데이터베이스입니다. [Azure 클래식 포털][AzurePortal]에서 클래식 배포 모델을 사용하여 이미지 갤러리에서 Windows Server를 실행하는 가상 컴퓨터를 만들 수 있습니다. 그런 다음, MongoDB 데이터베이스를 가상 컴퓨터에 설치하고 구성할 수 있습니다.


## Windows Server를 실행하는 가상 컴퓨터 만들기

가상 컴퓨터를 만들려면 다음 지침을 따르십시오.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

> [AZURE.NOTE]가상 컴퓨터를 만드는 동안 MongoDB의 끝점을 추가하고 다음과 같이 구성할 수 있습니다. 이름을 **Mongo**로 지정하고 **TCP**를 프로토콜로 사용하고 공용 및 개인 포트를 모두 **27017**로 설정합니다.

## 데이터 디스크 연결
가상 컴퓨터에 대한 저장소를 제공하려면 데이터 디스크를 연결한 다음 Windows에서 사용할 수 있도록 초기화합니다. 기존 디스크에 사용할 데이터가 이미 들어 있으면 기존 디스크를 연결할 수도 있고 빈 디스크를 연결할 수도 있습니다.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

디스크 초기화 지침은 [Windows 가상 컴퓨터에 데이터 디스크를 연결하는 방법](storage-windows-attach-disk.md)에서 "방법: Windows Server에서 새 데이터 디스크 초기화"를 참조하세요.

## 가상 컴퓨터에서 MongoDB 설치 및 실행

[AZURE.INCLUDE [install-and-run-mongo-on-win2k8-vm](../../includes/install-and-run-mongo-on-win2k8-vm.md)]

##요약
이 자습서에서는 Windows Server를 실행하는 가상 컴퓨터를 만들고 가상 컴퓨터에 원격으로 연결한 다음 데이터 디스크를 연결하는 방법을 배웠습니다. 또한 Windows 기반 가상 컴퓨터에 MongoDB를 설치 및 구성하는 방법을 배웠습니다. 이제 [MongoDB 설명서][MongoDocs](영문)의 고급 항목에 따라 Windows 기반 가상 컴퓨터에서 MongoDB에 액세스할 수 있습니다.

[MongoDocs]: http://docs.mongodb.org/manual/
[MongoDB]: http://www.mongodb.org/
[AzurePortal]: http://manage.windowsazure.com

<!---HONumber=AcomDC_1203_2015-->