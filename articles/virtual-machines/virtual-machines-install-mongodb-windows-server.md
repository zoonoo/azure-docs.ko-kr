<properties 
	pageTitle="Windows Server 가상 컴퓨터에 MongoDB 설치" 
	description="Windows Server를 실행하는 Azure VM에 MongoDB를 설치하는 방법에 대해 알아봅니다." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/06/2015" 
	ms.author="kathydav"/>

#Windows Server를 실행하는 가상 컴퓨터에 MongoDB 설치

[MongoDB][MongoDB]는 대중적인 오픈 소스의 고성능 NoSQL 데이터베이스입니다. [Azure 관리 포털][AzureManagementPortal]을 사용하면 Windows Server를 실행하는 가상 컴퓨터를 이미지 갤러리에서 만들 수 있습니다. 그런 다음, MongoDB 데이터베이스를 가상 컴퓨터에 설치하고 구성할 수 있습니다.

이 문서에서는 다음 방법에 대해 설명합니다.

- 관리 포털을 사용하여 갤러리에서 Windows Server 가상 컴퓨터 만들기
- 원격 데스크톱을 사용하여 가상 컴퓨터에 연결
- 가상 컴퓨터에 데이터 디스크 연결
- 가상 컴퓨터에서 MongoDB 설치

## Windows Server를 실행하는 가상 컴퓨터 만들기

다음은 일반적인 지침입니다. MongoDB에 원격으로 액세스할 수 있도록 끝점을 만들어 수정할 수 있습니다. (MongoDB 설치 지침 다음에 설명된 대로 나중에 만들 수도 있습니다.) 마법사 마지막 페이지에서 끝점을 추가하고 다음과 같이 구성합니다.

- **Mongo**로 이름 지정
- 프로토콜로 **TCP** 사용
- 공용 및 개인 포트를 둘 다 **27017**로 설정
 
[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-WindowsVM.md)]

## 데이터 디스크 연결
가상 컴퓨터에 대한 저장소를 제공하려면 데이터 디스크를 연결한 다음 Windows에서 사용할 수 있게 초기화합니다. 기존 디스크에 사용할 데이터가 아직 들어 있으면 기존 디스크를 사용할 수도 있고 빈 디스크를 연결할 수도 있습니다.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

디스크 초기화에 관한 지침은 [데이터 디스크를 Windows 가상 컴퓨터에 연결하는 방법](storage-windows-attach-disk.md)에서 "방법: Windows Server에서 새 데이터 디스크 초기화"를 참조하세요.

## 가상 컴퓨터에서 MongoDB 설치 및 실행 

[AZURE.INCLUDE [install-and-run-mongo-on-win2k8-vm](../../includes/install-and-run-mongo-on-win2k8-vm.md)]

##요약
이 자습서에서는 Windows Server 가상 컴퓨터를 만들고 가상 컴퓨터에 원격으로 연결하고 데이터 디스크를 연결하는 방법을 배웠습니다. 또한 MongoDB를 Windows 가상 컴퓨터에 설치하고 구성하는 방법도 배웠습니다. MongoDB에 대한 자세한 내용은 [MongoDB 설명서][MongoDocs](영문)를 참조하세요.

[MongoDocs]: http://www.mongodb.org/display/DOCS/Home
[MongoDB]: http://www.mongodb.org/
[AzureManagementPortal]: http://manage.windowsazure.com
 

<!---HONumber=58_postMigration-->