---
title: "CLI를 사용하여 클래식 Linux VM 만들기 | Microsoft Docs"
description: "Azure CLI에서 클래식 배포 모델을 사용하여 Linux 가상 컴퓨터를 만드는 방법을 알아봅니다."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: f8071a2e-ed91-4f77-87d9-519a37e5364f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/14/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: f556fd0318accc19f0fa56fa7f2a8716ee6f1c02
ms.openlocfilehash: cfb86b803e2c74dfff567b0a9920735ddb9e69f4


---
# <a name="how-to-create-a-linux-vm-with-the-azure-cli"></a>Azure CLI를 사용하여 Linux VM을 만드는 방법
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Resource Manager 버전은 [여기](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

이 항목에서는 Azure CLI에서 클래식 배포 모델을 사용하여 Linux VM(가상 컴퓨터)을 만드는 방법을 설명합니다. Azure에서 제공되는 **이미지** 의 Linux 이미지를 사용합니다. Azure CLI 명령은 특히 다음과 같은 구성 선택 항목을 제공합니다.

* 가상 네트워크에 VM 연결
* 기존 클라우드 서비스에 VM 추가
* 기존 저장소 계정에 VM 추가
* 가용성 집합 또는 위치에 VM 추가

> [!IMPORTANT]
> VM에서 가상 네트워크를 사용하여 호스트 이름으로 VM에 직접 연결하거나 프레미스 간 연결을 설정할 수 있게 하려는 경우 VM을 만들 때 가상 네트워크를 지정해야 합니다. VM을 만드는 경우에만 가상 네트워크에 가입하도록 VM을 구성할 수 있습니다. 가상 네트워크에 대한 자세한 내용은 [Azure 가상 네트워크 개요](http://go.microsoft.com/fwlink/p/?LinkID=294063)를 참조하세요.
> 
> 

## <a name="how-to-create-a-linux-vm-using-the-classic-deployment-model"></a>클래식 배포 모델을 사용하여 Linux VM을 만드는 방법
[!INCLUDE [virtual-machines-create-LinuxVM](../../includes/virtual-machines-create-linuxvm.md)]




<!--HONumber=Nov16_HO3-->


