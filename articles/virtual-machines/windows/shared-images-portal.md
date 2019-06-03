---
title: 포털을 사용 하 여 Windows에 대 한 공유 Azure 가상 머신 이미지 만들기 | Microsoft Docs
description: Azure portal을 사용 하 여 만들고 가상 머신 이미지를 공유 하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/06/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: f63b705f7967eeddaf522941d6db9be5d9f2719d
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245013"
---
# <a name="create-a-shared-image-gallery-using-the-azure-portal"></a>Azure portal을 사용 하 여 공유 이미지 갤러리 만들기

[공유 이미지 갤러리](shared-image-galleries.md)는 조직 내 사용자 지정 이미지 공유를 간소화합니다. 사용자 지정 이미지는 Marketplace 이미지와 같지만 직접 만듭니다. 애플리케이션 사전 로드, 애플리케이션 구성 및 기타 OS 구성과 같은 부트스트랩 배포 작업에 사용자 지정 이미지를 사용할 수 있습니다. 

공유 이미지 갤러리를 사용하면 조직, 지역, AAD 테넌트의 다른 사용자와 사용자 지정 VM 이미지를 공유할 수 있습니다. 공유할 이미지, 이미지를 제공할 지역, 이미지를 공유할 사람을 선택하세요. 여러 갤러리를 만들어서 공유 이미지 논리적으로 그룹화할 수 있습니다. 

갤러리는 완전한 RBAC(역할 기반 액세스 제어)를 제공하는 최상위 리소스입니다. 이미지 버전을 관리할 수 있으며, 각 이미지 버전을 여러 Azure 지역에 복제하도록 선택할 수 있습니다. 갤러리는 관리되는 이미지에서만 작동합니다.

공유 이미지 갤러리 기능에는 여러 가지 리소스가 있습니다. 이 문서에서는 이러한 리소스를 사용하거나 작성하게 됩니다.

| Resource | 설명|
|----------|------------|
| **관리되는 이미지** | 이는 단독으로 사용하거나 이미지 갤러리에 **이미지 버전**을 만드는 데 사용할 수 있는 기본 이미지입니다. 관리되는 이미지는 일반화된 VM에서 생성됩니다. 관리되는 이미지는 여러 VM을 만드는 데 사용할 수 있는 특수한 유형의 VHD로, 이제 공유 이미지 버전을 만드는 데 사용할 수 있습니다. |
| **이미지 갤러리** | Azure Marketplace와 마찬가지로 **이미지 갤러리**는 이미지를 관리하고 공유하는 데 사용되는 리포지토리이지만 액세스할 수 있는 사람을 제어할 수 있습니다. |
| **이미지 정의** | 이미지는 갤러리 내에 정의되고, 내부적으로 사용하기 위해 충족해야 할 요구 사항과 이미지에 대한 정보를 전달합니다. 여기에는 이미지가 Windows인지, Linux인지 여부, 릴리스 정보, 최소 및 최대 메모리 요구 사항이 포함됩니다. 이미지의 형식 정의입니다. |
| **이미지 버전** | **이미지 버전**은 갤러리를 사용하는 경우 VM을 만들 때 사용합니다. 사용 환경에 필요한 만큼 여러 버전의 이미지를 가질 수 있습니다. 관리되는 이미지와 마찬가지로 **이미지 버전**을 사용하여 VM을 만들 때는 이미지 버전을 사용하여 VM의 새 디스크를 만듭니다. 이미지 버전은 여러 번 사용할 수 있습니다. |


## <a name="before-you-begin"></a>시작하기 전에

이 문서의 예제를 완료하려면 기존 관리 이미지가 있어야 합니다. 필요한 경우 [자습서: Azure PowerShell을 사용하여 Azure VM의 사용자 지정 이미지 만들기](tutorial-custom-images.md)에 따라 이미지를 하나 만듭니다. 데이터 디스크를 포함 하는 관리 되는 이미지, 데이터 디스크 크기가 1TB 보다 더 지정할 수 없습니다.

이 문서를 진행할 때 필요한 경우 리소스 그룹 및 VM 이름을 바꿉니다.


[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]
 
## <a name="create-vms-from-an-image"></a>이미지에서 VM 만들기

이미지 버전이 완료되면 하나 이상의 새 VM을 만들 수 있습니다. 

이 예제에서는 *미국 동부* 데이터 센터에서 *myResourceGroup*에 *myVMfromImage*라는 VM을 만듭니다.

1. 이미지 버전에 대 한 페이지에서 선택 **VM 만들기** 페이지의 맨 위에 있는 메뉴에서.
1. 에 대 한 **리소스 그룹**를 선택 **새로 만들기** 유형과 *myResourceGroup* 이름입니다.
1. **가상 머신 이름을**, 형식 *myVM*합니다.
1. 에 대 한 **지역**를 선택 *미국 동부*합니다.
1. 에 대 한 **가용성 옵션**을 기본값인 *필수 인프라 중복 되지 않은*합니다.
1. 에 대 한 값 **이미지** 이미지 버전에 대 한 페이지에서 시작 하는 경우 자동으로 입력 해야 합니다.
1. 에 대 한 **크기**, 사용 가능한 크기의 목록에서 VM 크기를 선택 하 고 "선택"을 클릭 합니다.
1. **관리자 계정**에서 *azureuser*와 같은 사용자 이름 및 암호를 입력합니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](faq.md#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다.
1. 아래에 있는 VM에 원격 액세스를 허용 하려는 경우 **공용 인바운드 포트**, 선택 **선택한 포트를 허용** 선택한 후 **RDP (3389)** 드롭다운 목록에서. VM에 원격 액세스를 허용 하지 않으려는 경우 둡니다 **None** 에 대 한 선택한 **공용 인바운드 포트**합니다.
1. 작업을 완료 하는 경우 선택 합니다 **검토 + 만들기** 페이지의 맨 위에 있는 단추입니다.
1. VM이 유효성 검사를 통과 한 후 선택 **만들기** 배포를 시작 하려면 페이지 맨 아래에 있습니다.



## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹, 가상 머신 및 모든 관련 리소스가 더 이상 필요 없는 경우 삭제해도 됩니다. 삭제하려면 가상 머신의 리소스 그룹을 선택하고 **삭제**를 선택한 다음, 삭제할 리소스 그룹의 이름을 확인합니다.

개별 리소스를 삭제 하려는 경우에 반대 순서로 삭제 해야 합니다. 예를 들어 이미지 정의 삭제 하려면 해야 해당 이미지에서 만든 이미지 버전을 모두 삭제 합니다.

## <a name="next-steps"></a>다음 단계

또한 템플릿을 사용하여 공유 이미지 갤러리 리소스를 만들 수도 있습니다. 다음의 몇 가지 Azure 빠른 시작 템플릿을 사용할 수 있습니다. 

- [공유 이미지 갤러리 만들기](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [공유 이미지 갤러리에서 이미지 정의 만들기](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [공유 이미지 갤러리에서 이미지 버전 만들기](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [이미지 버전에서 VM 만들기](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

공유 이미지 갤러리에 대한 자세한 내용은 [개요](shared-image-galleries.md)를 참조하세요. 문제 발생 시에는 [공유 이미지 갤러리 문제 해결](troubleshooting-shared-images.md)을 참조하세요.

