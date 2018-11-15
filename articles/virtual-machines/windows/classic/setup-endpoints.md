---
title: 클래식 Windows VM에서 엔드포인트 설정 | Microsoft Docs
description: Azure에서 Windows 가상 머신과 통신을 허용하도록 Azure Portal에서 클래식 Windows VM에 대한 엔드포인트를 설정하는 방법에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 8afc21c2-d3fb-43a3-acce-aa06be448bb6
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/23/2018
ms.author: cynthn
ms.openlocfilehash: 373003eb8be0482ed96d7d11ecd879237f69b47a
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50957168"
---
# <a name="set-up-endpoints-on-a-windows-virtual-machine-by-using-the-classic-deployment-model"></a>클래식 배포 모델을 사용하여 Windows 가상 머신에서 엔드포인트 설정
클래식 배포 모델을 사용하여 Azure에서 만든 모든 Windows VM(가상 머신)은 개인 네트워크 채널을 통해 동일한 클라우드 서비스 또는 가상 네트워크에 있는 다른 VM과 자동으로 통신할 수 있습니다. 그러나 인터넷이나 다른 가상 네트워크의 컴퓨터가 VM으로 인바운드 네트워크 트래픽을 전달하려면 엔드포인트가 필요합니다. 

또한 [Linux 가상 머신](../../linux/classic/setup-endpoints.md)에서 엔드포인트를 설정할 수 있습니다.

> [!IMPORTANT]
> Azure에는 리소스를 만들고 작업하는 [Resource Manager와 클래식](../../../resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다.  
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

**Resource Manager** 배포 모델에서는 **NSG(네트워크 보안 그룹)** 를 사용하여 엔드포인트를 구성합니다. 자세한 내용은 [Azure Portal을 사용하여 VM에 대한 외부 액세스 허용](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.

Azure Portal에서 Windows VM을 만들 때 원격 데스크톱, Windows PowerShell 원격 등에 대한 일반적인 엔드포인트는 일반적으로 자동으로 만들어집니다. 나중에 필요에 따라 추가 엔드포인트를 구성할 수 있습니다.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>다음 단계
* Azure PowerShell cmdlet을 사용하여 VM 엔드포인트를 설정하려면 [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx)를 참조하세요.
* Azure PowerShell cmdlet을 사용하여 엔드포인트에서 ACL을 관리하려면 [PowerShell을 사용하여 엔드포인트에 대한 ACL(액세스 제어 목록) 관리](../../../virtual-network/virtual-networks-acl-powershell.md)를 참조하세요.
* 리소스 관리자 배포 모델에서 가상 머신을 만들면, Azure PowerShell을 사용하여 VM에 대한 트래픽을 제어하는 [네트워크 보안 그룹을 만들 수 있습니다](../../../virtual-network/tutorial-filter-network-traffic.md) .
