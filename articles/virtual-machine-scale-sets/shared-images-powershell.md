---
title: Azure에서 공유 VM 이미지를 사용하여 확장 집합 만들기 | Microsoft Docs
description: Azure PowerShell을 사용하여 Azure에서 가상 머신 확장 세트 배포에 사용할 공유 VM 이미지를 만드는 방법을 알아봅니다.
services: virtual-machine-scale-sets
documentationcenter: ''
author: axayjo
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2018
ms.author: akjosh; cynthn
ms.custom: ''
ms.openlocfilehash: 3e6baedc28805117962aa8dda8edc6ff3419280e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60188361"
---
# <a name="preview-create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-powershell"></a>미리 보기: Azure PowerShell을 사용하여 가상 머신 확장 세트용 공유 이미지 만들기 및 사용

확장 집합을 만들 때 VM 인스턴스 배포 시 사용할 이미지를 지정합니다. 공유 이미지 갤러리 서비스를 사용하면 조직 전체에서 사용자 지정 이미지를 매우 간편하게 공유할 수 있습니다. 사용자 지정 이미지는 Marketplace 이미지와 같지만 직접 만듭니다. 애플리케이션 사전 로드, 애플리케이션 구성 및 기타 OS 구성과 같은 부트스트랩 구성에 사용자 지정 이미지를 사용할 수 있습니다. 

공유 이미지 갤러리를 사용하면 조직, 지역, AAD 테넌트의 다른 사용자와 사용자 지정 VM 이미지를 공유할 수 있습니다. 공유할 이미지, 이미지를 제공할 지역, 이미지를 공유할 사람을 선택하세요. 여러 갤러리를 만들어서 공유 이미지 논리적으로 그룹화할 수 있습니다. 

갤러리는 완전한 RBAC(역할 기반 액세스 제어)를 제공하는 최상위 리소스입니다. 이미지 버전을 관리할 수 있으며, 각 이미지 버전을 여러 Azure 지역에 복제하도록 선택할 수 있습니다. 갤러리는 관리되는 이미지에서만 작동합니다. 

공유 이미지 갤러리 기능에는 여러 가지 리소스가 있습니다. 이 문서에서는 이러한 리소스를 사용하거나 작성하게 됩니다.

| 리소스 | 설명|
|----------|------------|
| **관리되는 이미지** | 이는 단독으로 사용하거나 이미지 갤러리에 **이미지 버전**을 만드는 데 사용할 수 있는 기본 이미지입니다. 관리되는 이미지는 일반화된 VM에서 생성됩니다. 관리되는 이미지는 여러 VM을 만드는 데 사용할 수 있는 특수한 유형의 VHD로, 이제 공유 이미지 버전을 만드는 데 사용할 수 있습니다. |
| **이미지 갤러리** | Azure Marketplace와 마찬가지로 **이미지 갤러리**는 이미지를 관리하고 공유하는 데 사용되는 리포지토리이지만 액세스할 수 있는 사람을 제어할 수 있습니다. |
| **이미지 정의** | 이미지는 갤러리 내에 정의되고, 내부적으로 사용하기 위해 충족해야 할 요구 사항과 이미지에 대한 정보를 전달합니다. 여기에는 이미지가 Windows인지, Linux인지 여부, 릴리스 정보, 최소 및 최대 메모리 요구 사항이 포함됩니다. 이미지의 형식 정의입니다. |
| **이미지 버전** | **이미지 버전**은 갤러리를 사용하는 경우 VM을 만들 때 사용합니다. 사용 환경에 필요한 만큼 여러 버전의 이미지를 가질 수 있습니다. 관리되는 이미지와 마찬가지로 **이미지 버전**을 사용하여 VM을 만들 때는 이미지 버전을 사용하여 VM의 새 디스크를 만듭니다. 이미지 버전은 여러 번 사용할 수 있습니다. |

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>시작하기 전에

아래 단계에서는 기존 VM을 가져와서 새 VM 인스턴스를 만드는 데 사용할 수 있는 재사용 가능 사용자 지정 이미지로 변환하는 방법을 자세히 설명합니다.

이 문서의 예제를 완료하려면 기존 관리 이미지가 있어야 합니다. 필요한 경우 [자습서: Azure PowerShell을 사용하여 가상 머신 확장 집합에 대한 사용자 지정 이미지 만들기 및 사용](tutorial-use-custom-image-powershell.md)에 따라 이미지를 만들 수 있습니다. 이 문서를 진행할 때 필요한 경우 리소스 그룹 및 VM 이름을 바꿉니다.


[!INCLUDE [virtual-machines-common-shared-images-ps](../../includes/virtual-machines-common-shared-images-powershell.md)]

## <a name="create-a-scale-set-from-the-shared-image-version"></a>공유 이미지 버전에서 확장 집합 만들기

[New-AzVmss](/powershell/module/az.compute/new-azvmss)를 사용하여 가상 머신 확장 집합을 만듭니다. 다음 예제에서는 미국 서부 데이터 센터의 새 이미지 버전에서 확장 집합을 만듭니다. 가상 네트워크, 공용 IP 주소 및 부하 분산 장치에 대한 Azure 네트워크 리소스가 자동으로 만들어집니다. 메시지가 표시되면 확장 집합에서 VM 인스턴스에 대해 사용자 고유의 관리 자격 증명을 설정합니다.

```azurepowershell-interactive
New-AzVmss `
  -ResourceGroupName myVMSSRG `
  -Location 'South Central US' `
  -VMScaleSetName 'myScaleSet' `
  -VirtualNetworkName 'myVnet' `
  -SubnetName 'mySubnet'`
  -PublicIpAddressName 'myPublicIPAddress' `
  -LoadBalancerName 'myLoadBalancer' `
  -UpgradePolicyMode 'Automatic' `
  -ImageName $imageVersion.Id
```

확장 집합 리소스와 VM을 모두 만들고 구성하는 데 몇 분 정도 걸립니다.

[!INCLUDE [virtual-machines-common-gallery-list-ps](../../includes/virtual-machines-common-gallery-list-ps.md)]


## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) cmdlet을 사용하여 리소스 그룹, VM 및 모든 관련 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myGalleryRG
Remove-AzResourceGroup -Name myVMSSRG
```


## <a name="next-steps"></a>다음 단계

또한 템플릿을 사용하여 공유 이미지 갤러리 리소스를 만들 수도 있습니다. 다음의 몇 가지 Azure 빠른 시작 템플릿을 사용할 수 있습니다. 

- [공유 이미지 갤러리 만들기](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [공유 이미지 갤러리에서 이미지 정의 만들기](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [공유 이미지 갤러리에서 이미지 버전 만들기](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [이미지 버전에서 VM 만들기](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

공유 이미지 갤러리에 대한 자세한 내용은 [개요](shared-image-galleries.md)를 참조하세요. 문제 발생 시에는 [공유 이미지 갤러리 문제 해결](troubleshooting-shared-images.md)을 참조하세요.
