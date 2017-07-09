---
title: "Azure에서 Windows VM에 MongoDB 설치 | Microsoft Docs"
description: "클래식 배포 모델을 사용하여 만든, Windows Server를 실행하는 Azure VM에 MongoDB를 설치하는 방법을 알아봅니다."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 4095df41-bb69-4bbe-9c1c-70923b0d84ba
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 6b5af18d02fd508a21cdc21b38b1c16e79f07ecb
ms.contentlocale: ko-kr
ms.lasthandoff: 06/26/2017


---
# <a name="install-mongodb-on-a-windows-vm-in-azure"></a>Azure에서 Windows VM에 MongoDB 설치
> [!IMPORTANT]
> Azure에는 리소스를 만들고 작업하기 위한 [리소스 관리자 및 클래식](../../../resource-manager-deployment-model.md)라는 두 가지 배포 모델이 있습니다.  이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다. Resource Manager 배포 모델을 사용하여 MongoDB를 설치하고 구성하려면 [이 문서](../install-mongodb.md)를 참조하세요.

[MongoDB][MongoDB]는 인기 있는 고성능 오픈 소스 NoSQL 데이터베이스입니다. 이 문서에서는[Azure Portal][AzurePortal]을 사용하여 Windows Server VM(가상 컴퓨터)을 만드는 과정을 안내합니다. 그런 다음 MongoDB를 설치하고 구성하기 전에 데이터 디스크를 만든 후 VM에 연결합니다. Azure에 사용하려는 기존 VM이 있는 경우 [MongoDB 설치 및 구성](#install-and-run-mongodb-on-the-virtual-machine)단계로 바로 건너뛸 수 있습니다.

## <a name="create-a-virtual-machine-running-windows-server"></a>Windows Server를 실행하는 가상 컴퓨터 만들기
가상 컴퓨터를 만들려면 다음 지침을 따르십시오.

[!INCLUDE [virtual-machines-create-WindowsVM](../../../../includes/virtual-machines-create-windowsvm.md)]

> [!NOTE]
> 가상 컴퓨터를 만드는 동안 MongoDB의 끝점을 추가하고 다음과 같이 구성할 수 있습니다. 이름을 **Mongo**로 지정하고 **TCP**를 프로토콜로 사용하고 공용 및 개인 포트를 모두 **27017**로 설정합니다.
>
>

## <a name="attach-a-data-disk"></a>데이터 디스크 연결
가상 컴퓨터에 대한 저장소를 제공하려면 데이터 디스크를 연결한 다음 Windows에서 사용할 수 있도록 초기화합니다. 데이터 디스크가 이미 있는 경우 해당 기존 디스크를 연결할 수도 있고 빈 디스크를 연결할 수도 있습니다.

[!INCLUDE [howto-attach-disk-windows-linux](../../../../includes/howto-attach-disk-windows-linux.md)]

디스크 초기화 지침은 [Windows 가상 컴퓨터에 데이터 디스크를 연결하는 방법](attach-disk.md)에서 "방법: Windows Server에서 새 데이터 디스크 초기화"를 참조하세요.

## <a name="install-and-run-mongodb-on-the-virtual-machine"></a>가상 컴퓨터에서 MongoDB 설치 및 실행
[!INCLUDE [install-and-run-mongo-on-win2k8-vm](../../../../includes/install-and-run-mongo-on-win2k8-vm.md)]

## <a name="summary"></a>요약
이 자습서에서는 Windows Server를 실행하는 가상 컴퓨터를 만들고 가상 컴퓨터에 원격으로 연결한 다음 데이터 디스크를 연결하는 방법을 배웠습니다.  또한 Windows 기반 가상 컴퓨터에 MongoDB를 설치 및 구성하는 방법을 배웠습니다. 이제 [MongoDB 설명서][MongoDocs](영문)의 고급 항목에 따라 Windows 기반 가상 컴퓨터에서 MongoDB에 액세스할 수 있습니다.

[MongoDocs]: http://docs.mongodb.org/manual/
[MongoDB]: http://www.mongodb.org/
[AzurePortal]: https://portal.azure.com/


