---
title: Azure DevTest 랩에서 공유 이미지를 사용하여 VM 추가 | 마이크로 소프트 문서
description: Azure DevTest Labs에서 첨부된 공유 이미지 갤러리의 이미지를 사용하여 가상 시스템(VM)을 추가하는 방법을 알아봅니다.
services: devtest-lab,virtual-machines
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
ms.date: 08/02/2019
ms.author: spelluru
ms.openlocfilehash: c13b2c3d00a0ab0af0f7785feae645d907ffdaa3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68775592"
---
# <a name="add-a-vm-using-an-image-from-the-attached-shared-image-gallery"></a>첨부된 공유 이미지 갤러리의 이미지를 사용하여 VM 추가
Azure DevTest Labs를 사용하면 공유 이미지 갤러리를 랩에 연결한 다음 갤러리의 이미지를 랩에서 만든 VM의 기반으로 사용할 수 있습니다. 랩에 공유 이미지 갤러리를 연결하는 방법을 알아보려면 [공유 이미지 갤러리 구성을](configure-shared-image-gallery.md)참조하십시오. 이 문서에서는 첨부된 공유 이미지 갤러리의 이미지를 기반으로 사용하여 랩에 VM을 추가하는 방법을 보여 줍니다. 

## <a name="azure-portal"></a>Azure portal
이 섹션에서는 Azure 포털을 사용하여 연결된 공유 이미지 갤러리의 이미지를 기반으로 랩에 VM을 추가하는 방법을 알아봅니다. 이 섹션에서는 Azure 포털을 사용하여 VM을 만들기 위한 자세한 단계별 지침을 제공하지 않습니다. 이러한 자세한 내용은 [VM - Azure 포털 만들기를](devtest-lab-add-vm.md)참조하십시오. 첨부된 공유 이미지 갤러리에서 이미지를 선택하고 사용할 이미지 버전을 선택하는 단계만 강조 표시합니다. 

랩에 VM을 추가할 때 첨부된 공유 이미지 갤러리에서 이미지를 기본 이미지로 선택할 수 있습니다. 

![베이스에 대한 공유 이미지 선택](./media/add-vm-use-shared-image/select-shared-image-for-base.png)

그런 다음 **랩 리소스 만들기** 페이지의 고급 **설정** 탭에서 기본 이미지로 사용할 이미지 버전을 선택할 수 있습니다.

![이미지 버전 선택](./media/add-vm-use-shared-image/select-version-shared-image.png)

VM을 만든 후 다른 버전의 이미지를 사용하여 전환할 수 있습니다. 

## <a name="resource-manager-template"></a>Resource Manager 템플릿
Azure 리소스 관리자 템플릿을 사용하여 공유 이미지 갤러리 이미지를 사용하여 가상 컴퓨터를 만드는 경우 **속성** 섹션에서 **sharedImageId에** 대한 값을 지정합니다. 다음 예제를 참조하십시오. 

```json
"resources": [
{
    ...
    "properties": {
         "sharedImageId": "/subscriptions/111111111-1111-1111-1111-111111111111/resourcegroups/mydtlrg/providers/microsoft.devtestlab/labs/mydtllab/sharedgalleries/spsig/sharedimages/myimagefromgallery",
        "sharedImageVersion": "1.0.1",
        ...
    }
}
],
```

전체 리소스 관리자 템플릿 예제는 GitHub 리포지토리에서 공유 이미지 갤러리 이미지 샘플을 [사용하여 가상 컴퓨터 만들기를](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage) 참조하십시오. 

## <a name="rest-api"></a>REST API

1. 먼저 공유 이미지 갤러리에서 이미지의 ID를 얻어야 합니다. 한 가지 방법은 다음 GET 명령을 사용하여 첨부된 공유 이미지 갤러리의 모든 이미지를 나열하는 것입니다. 

    ```rest
    GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
    ```
2. 이전 호출에서 받은 공유 이미지의 ID를 에 전달하여 가상 컴퓨터에서 PUT `properties.SharedImageId`메서드를 호출합니다.

## <a name="next-steps"></a>다음 단계
공유 이미지 갤러리를 랩에 연결하고 구성하는 방법을 알아보려면 [공유 이미지 갤러리 구성을](configure-shared-image-gallery.md)참조하십시오.