---
title: "클라우드 서비스에서 Linux VM 연결 | Microsoft Docs"
description: "클래식 배포 모델을 사용하여 만든 Linux 가상 컴퓨터를 Azure 클라우드 서비스 또는 가상 네트워크에 연결합니다."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 2fd23055-6b34-4ef0-88a8-fc19e32fb3c9
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/06/2017
ms.author: cynthn
ms.openlocfilehash: e222645509640b104410f87e4bcd22834c8d9ec1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-linux-virtual-machines-created-with-the-classic-deployment-model-with-a-virtual-network-or-cloud-service"></a>클래식 배포 모델을 사용하여 만든 Linux 가상 컴퓨터를 가상 네트워크 또는 클라우드 서비스에 연결
> [!IMPORTANT]
> Azure에는 리소스를 만들고 작업하기 위한 [리소스 관리자 및 클래식](../../../resource-manager-deployment-model.md)라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다.

클래식 배포 모델을 사용하여 만든 Linux 가상 컴퓨터는 항상 클라우드 서비스에 배치됩니다. 클라우드 컴퓨터는 컨테이너 역할을 하며 인터넷을 통해 가상 컴퓨터에 액세스할 수 있도록 고유한 공용 DNS 이름, 공용 IP 주소 및 끝점 집합을 제공합니다. 클라우드 서비스는 가상 네트워크에 있을 수 있지만 요구 사항은 아닙니다. 또한 [Windows 가상 컴퓨터를 가상 네트워크 또는 클라우드 서비스와 연결](../../windows/classic/connect-vms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)할 수도 있습니다.

가상 네트워크에 없는 클라우드 서비스를 *독립 실행형* 클라우드 서비스라고 합니다. 독립 실행형 클라우드 서비스의 가상 컴퓨터는 다른 가상 컴퓨터의 공용 DNS 이름을 사용하여 다른 가상 컴퓨터와 통신하고 인터넷을 통해 트래픽을 이동시킵니다. 클라우드 서비스가 가상 네트워크에 있는 경우 해당 클라우드 서비스의 가상 컴퓨터는 인터넷을 통해 트래픽을 보내지 않고도 가상 네트워크에 있는 다른 모든 가상 컴퓨터와 통신할 수 있습니다.

같은 독립 실행형 클라우드 서비스에 가상 컴퓨터를 배치하는 경우에는 계속 부하 분산 및 가용성 집합을 사용할 수 있습니다. 자세한 내용은 [가상 컴퓨터 부하 분산](../../virtual-machines-linux-load-balance.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 및 [가상 컴퓨터의 가용성 관리](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요. 그러나 서브넷에서 가상 컴퓨터를 구성하거나 독립 실행형 클라우드 서비스를 온-프레미스 네트워크에 연결할 수는 없습니다. 예를 들면 다음과 같습니다.

[!INCLUDE [virtual-machines-common-classic-connect-vms](../../../../includes/virtual-machines-common-classic-connect-vms.md)]

## <a name="next-steps"></a>다음 단계
가상 컴퓨터를 만들고 나서 서비스 및 워크로드에 데이터를 저장할 위치가 포함되도록 [데이터 디스크를 추가](attach-disk.md) 하는 것이 좋습니다.
