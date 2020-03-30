---
title: Azure DevTest 랩에서 공유 이미지 갤러리 구성 | 마이크로 소프트 문서
description: Azure DevTest 랩에서 공유 이미지 갤러리를 구성하는 방법에 대해 알아봅니다.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2019
ms.author: spelluru
ms.openlocfilehash: 9593d60f76802cd515ca85616bce028cf3aa0d49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77589320"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Azure DevTest Labs에서 공유 이미지 갤러리 구성
이제 DevTest 랩에서 [공유 이미지 갤러리](../virtual-machines/windows/shared-image-galleries.md) 기능을 지원합니다. 랩 사용자는 랩 리소스를 만드는 동안 공유 위치에서 이미지에 액세스할 수 있습니다. 또한 사용자 지정 관리 VM 이미지를 중심으로 구조 및 구성을 구축할 수 있습니다. 공유 이미지 갤러리 기능은 다음을 지원합니다.

- 이미지의 관리되는 전역 복제
- 보다 간편한 관리를 위한 이미지 버전 관리 및 그룹화
- 가용성 영역을 지원하는 지역의 ZRS(영역 중복 저장소) 계정을 사용하면 이미지를 더 쉽게 사용할 수 있습니다. ZRS는 구역 오류에 대한 더 나은 복원력을 제공합니다.
- RBAC(역할 기반 액세스 제어)를 사용하여 구독 간에, 심지어 테넌자 간에 공유합니다.

자세한 내용은 [공유 이미지 갤러리 설명서를](../virtual-machines/windows/shared-image-galleries.md)참조하십시오. 
 
유지 관리해야 하는 관리되는 이미지가 많고, 회사 전체에서 사용 가능하도록 하려면 공유 이미지 갤러리를 이미지를 쉽게 업데이트하고 공유할 수 있게 해주는 리포지토리로 사용할 수 있습니다. 랩 소유자는 기존 공유 이미지 갤러리를 랩에 첨부할 수 있습니다. 이 갤러리가 연결되면 랩 사용자는 이러한 최신 이미지에서 컴퓨터를 만들 수 있습니다. 이 기능의 주요 이점은 DevTest Labs가 이제 랩, 구독 및 지역 간에 이미지를 공유할 수 있다는 것입니다. 

> [!NOTE]
> 공유 이미지 갤러리 서비스와 관련된 비용에 대해 알아보려면 [공유 이미지 갤러리에 대한 청구를](../virtual-machines/windows/shared-image-galleries.md#billing)참조하십시오.

## <a name="considerations"></a>고려 사항
- 한 번에 하나의 공유 이미지 갤러리만 랩에 연결할 수 있습니다. 다른 갤러리를 첨부하려면 기존 갤러리를 분리하고 다른 갤러리를 연결해야 합니다. 
- DevTest Labs는 현재 공유 이미지 갤러리 일반화된 이미지만 지원합니다.
- DevTest Labs는 현재 랩을 통해 갤러리에 이미지를 업로드하는 것을 지원하지 않습니다. 
- 공유 이미지 갤러리 이미지를 사용하여 가상 컴퓨터를 만드는 동안 DevTest Labs는 항상 이 이미지의 최신 게시된 버전을 사용합니다. 그러나 이미지에 여러 버전이 있는 경우 가상 컴퓨터를 만드는 동안 고급 설정 탭으로 이동하여 이전 버전에서 컴퓨터를 만들도록 선택할 수 있습니다.  
- DevTest Labs는 공유 이미지 갤러리가 랩이 있는 지역에 이미지를 복제하도록 자동으로 최선의 시도를 하지만 항상 가능한 것은 아닙니다. 사용자가 이러한 이미지에서 VM을 만드는 데 문제가 발생하지 않도록 하려면 이미지가 이미 랩 의 영역에 복제되어 있는지 확인합니다."

## <a name="use-azure-portal"></a>Azure Portal 사용
1. [Azure 포털에](https://portal.azure.com)로그인합니다.
1. 왼쪽 탐색 메뉴에서 **모든 서비스를** 선택합니다.
1. 목록에서 **개발자 테스트 랩을 선택합니다.**
1. 랩 목록에서 **랩을**선택합니다.
1. 왼쪽 메뉴의 **설정** 섹션에서 **구성 및 정책을** 선택합니다.
1. 왼쪽 메뉴에서 **가상 시스템 베이스** 아래에 있는 공유 이미지 **갤러리를** 선택합니다.

    ![공유 이미지 갤러리 메뉴](./media/configure-shared-image-gallery/shared-image-galleries-menu.png)
1. **첨부** 단추를 클릭하고 드롭다운에서 갤러리를 선택하여 기존 공유 이미지 갤러리를 랩에 연결합니다.

    ![연결](./media/configure-shared-image-gallery/attach-options.png)
1. 연결된 갤러리로 이동하여 VM 생성을 위해 공유 이미지를 **활성화하거나 사용하지 않도록** 설정하도록 갤러리를 구성합니다. 목록에서 이미지 갤러리를 선택하여 구성합니다. 

    기본적으로 **모든 이미지를 가상 시스템 베이스로 사용할 수 있도록 허용하면** **예로**설정됩니다. 즉, 연결된 공유 이미지 갤러리에서 사용할 수 있는 모든 이미지는 새 랩 VM을 만들 때 랩 사용자가 사용할 수 있습니다. 특정 이미지에 대한 액세스를 제한해야 하는 경우 **모든 이미지를 가상 시스템 베이스로 사용할 수 있도록** 허용 을 **아니요로**변경하고 VM을 만들 때 허용할 이미지를 선택한 다음 **저장** 단추를 선택합니다.

    ![사용 또는 비활성화](./media/configure-shared-image-gallery/enable-disable.png)
1. 랩 사용자는 **+Add를** 클릭하고 기본 페이지에서 이미지를 찾아서 활성화된 이미지를 사용하여 가상 컴퓨터를 만들 **수 있습니다.**

    ![랩 사용자](./media/configure-shared-image-gallery/lab-users.png)
## <a name="use-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>랩에 공유 이미지 갤러리 첨부
Azure Resource Manager 템플릿을 사용하여 공유 이미지 갤러리를 랩에 연결하는 경우 다음 예제와 같이 리소스 관리자 템플릿의 리소스 섹션 아래에 추가해야 합니다.

```json
"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs",
    "name": "mylab",
    "location": "eastus",
    "resources": [
    {
        "apiVersion":"2018-10-15-preview",
        "name":"myGallery",
        "type":"sharedGalleries",
        "properties": {
            "galleryId":"/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/mySharedGalleryRg/providers/Microsoft.Compute/galleries/mySharedGallery",
            "allowAllImages": "Enabled"
        }
    }
    ]
}
```

전체 리소스 관리자 템플릿 예제는 공용 GitHub 리포지토리에서 이러한 리소스 관리자 템플릿 샘플을 [참조하십시오.](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured)

## <a name="use-api"></a>API 사용

### <a name="shared-image-galleries---create-or-update"></a>공유 이미지 갤러리 - 만들기 또는 업데이트

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}?api-version= 2018-10-15-preview
Body: 
{
    "properties":{
        "galleryId": "[Shared Image Gallery resource Id]",
        "allowAllImages": "Enabled"
    }
}

```

### <a name="shared-image-galleries-images---list"></a>공유 이미지 갤러리 이미지 - 목록 

```rest
GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
```




## <a name="next-steps"></a>다음 단계
첨부된 공유 이미지 갤러리의 이미지를 사용하여 VM을 만드는 방법에 대한 다음 문서 보기: [갤러리에서 공유 이미지를 사용하여 VM 만들기](add-vm-use-shared-image.md)
