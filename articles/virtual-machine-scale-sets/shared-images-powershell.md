---
title: 공유 VM 이미지를 사용 하 여 Azure PowerShell에서 확장 집합 만들기
description: Azure PowerShell을 사용하여 Azure에서 가상 머신 확장 세트 배포에 사용할 공유 VM 이미지를 만드는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 55ca80296bfdfde162ca5a4df348fd80328dd184
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86494924"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-powershell"></a>Azure PowerShell을 사용하여 가상 머신 확장 세트용 공유 이미지 만들기 및 사용

확장 집합을 만들 때 VM 인스턴스 배포 시 사용할 이미지를 지정합니다. 공유 이미지 갤러리 서비스를 사용하면 조직 전체에서 사용자 지정 이미지를 매우 간편하게 공유할 수 있습니다. 사용자 지정 이미지는 Marketplace 이미지와 같지만 직접 만듭니다. 애플리케이션 사전 로드, 애플리케이션 구성 및 기타 OS 구성과 같은 부트스트랩 구성에 사용자 지정 이미지를 사용할 수 있습니다. 

공유 이미지 갤러리를 사용하면 조직, 지역, AAD 테넌트의 다른 사용자와 사용자 지정 VM 이미지를 공유할 수 있습니다. 공유할 이미지, 이미지를 제공할 지역, 이미지를 공유할 사람을 선택하세요. 여러 갤러리를 만들어서 공유 이미지 논리적으로 그룹화할 수 있습니다. 

갤러리는 완전한 RBAC(역할 기반 액세스 제어)를 제공하는 최상위 리소스입니다. 이미지 버전을 관리할 수 있으며, 각 이미지 버전을 여러 Azure 지역에 복제하도록 선택할 수 있습니다. 갤러리는 관리되는 이미지에서만 작동합니다. 

공유 이미지 갤러리 기능에는 여러 가지 리소스가 있습니다. 


[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../includes/virtual-machines-shared-image-gallery-resources.md)]


## <a name="before-you-begin"></a>시작하기 전에

아래 단계에서는 기존 VM을 가져와서 새 VM 인스턴스를 만드는 데 사용할 수 있는 재사용 가능 사용자 지정 이미지로 변환하는 방법을 자세히 설명합니다.

이 문서의 예제를 완료하려면 기존 관리 이미지가 있어야 합니다. [Azure PowerShell를 사용 하 여 가상 머신 확장 집합에 대 한 사용자 지정 이미지 만들기 및 사용 자습서](tutorial-use-custom-image-powershell.md) 를 수행 하 여 필요한 경우 만들 수 있습니다. 관리 되는 이미지에 데이터 디스크가 포함 되어 있는 경우 데이터 디스크 크기는 1TB를 넘을 수 없습니다.

이 문서를 진행할 때 필요한 경우 리소스 그룹 및 VM 이름을 바꿉니다.


[!INCLUDE [virtual-machines-common-shared-images-ps](../../includes/virtual-machines-common-shared-images-powershell.md)]




## <a name="next-steps"></a>다음 단계

또한 템플릿을 사용하여 공유 이미지 갤러리 리소스를 만들 수도 있습니다. 다음의 몇 가지 Azure 빠른 시작 템플릿을 사용할 수 있습니다. 

- [공유 이미지 갤러리 만들기](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [공유 이미지 갤러리에서 이미지 정의 만들기](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [공유 이미지 갤러리에서 이미지 버전 만들기](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [이미지 버전에서 VM 만들기](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

공유 이미지 갤러리에 대한 자세한 내용은 [개요](shared-image-galleries.md)를 참조하세요. 문제 발생 시에는 [공유 이미지 갤러리 문제 해결](troubleshooting-shared-images.md)을 참조하세요.
