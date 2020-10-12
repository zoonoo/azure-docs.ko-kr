---
title: Azure DevTest Labs에서 공유 이미지를 사용 하 여 VM 추가 | Microsoft Docs
description: Azure DevTest Labs에서 연결 된 공유 이미지 갤러리의 이미지를 사용 하 여 VM (가상 머신)을 추가 하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 9421a1e21be9446b0e59328bd9a3730b57655274
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85483859"
---
# <a name="add-a-vm-using-an-image-from-the-attached-shared-image-gallery"></a>연결 된 공유 이미지 갤러리의 이미지를 사용 하 여 VM 추가
Azure DevTest Labs를 사용 하면 랩에 공유 이미지 갤러리를 연결한 다음 랩에서 만든 Vm의 기반으로 갤러리의 이미지를 사용할 수 있습니다. 공유 이미지 갤러리를 랩에 연결 하는 방법을 알아보려면 [공유 이미지 갤러리 구성](configure-shared-image-gallery.md)을 참조 하세요. 이 문서에서는 연결 된 공유 이미지 갤러리의 이미지를 기본으로 사용 하 여 랩에 VM을 추가 하는 방법을 보여 줍니다. 

## <a name="azure-portal"></a>Azure portal
이 섹션에서는 Azure Portal 사용 하 여 연결 된 공유 이미지 갤러리의 이미지를 기반으로 랩에 VM을 추가 하는 방법에 대해 알아봅니다. 이 섹션에서는 Azure Portal를 사용 하 여 VM을 만드는 방법에 대 한 자세한 단계별 지침을 제공 하지 않습니다. 이러한 세부 정보는 [VM 만들기-Azure Portal](devtest-lab-add-vm.md)를 참조 하세요. 연결 된 공유 이미지 갤러리에서 이미지를 선택 하 고 사용 하려는 이미지의 버전을 선택 하는 단계만 강조 표시 됩니다. 

랩에 VM을 추가 하는 경우 연결 된 공유 이미지 갤러리에서 기본 이미지로 이미지를 선택할 수 있습니다. 

![기본의 공유 이미지를 선택 합니다.](./media/add-vm-use-shared-image/select-shared-image-for-base.png)

그런 다음 **랩 리소스 만들기** 페이지의 **고급 설정** 탭에서 기본 이미지로 사용할 이미지의 버전을 선택할 수 있습니다.

![이미지 버전 선택](./media/add-vm-use-shared-image/select-version-shared-image.png)

VM을 만든 후에 다른 버전의 이미지를 사용 하도록 전환할 수 있습니다. 

## <a name="resource-manager-template"></a>Resource Manager 템플릿
Azure Resource Manager 템플릿을 사용 하 여 공유 이미지 갤러리 이미지를 사용 하 여 가상 컴퓨터를 만드는 경우 **속성** 섹션에서 **sharedImageId** 에 대 한 값을 지정 합니다. 다음 예제를 참조하십시오. 

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

전체 리소스 관리자 템플릿 예제는 GitHub 리포지토리에서 [공유 이미지 갤러리 이미지를 사용 하 여 가상 머신 만들기](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage) 샘플을 참조 하세요. 

## <a name="rest-api"></a>REST API

1. 먼저 공유 이미지 갤러리에서 이미지의 ID를 가져와야 합니다. 한 가지 방법은 다음 GET 명령을 사용 하 여 연결 된 공유 이미지 갤러리의 모든 이미지를 나열 하는 것입니다. 

    ```rest
    GET  https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DevTestLab/labs/{labName}/sharedgalleries/{name}/sharedimages?api-version= 2018-10-15-preview
    ```
2. 에 대 한 이전 호출에서 받은 공유 이미지의 ID를 전달 하 여 가상 컴퓨터에서 PUT 메서드를 호출 합니다 `properties.SharedImageId` .

## <a name="next-steps"></a>다음 단계
공유 이미지 갤러리를 랩에 연결 하 고 구성 하는 방법을 알아보려면 [공유 이미지 갤러리 구성](configure-shared-image-gallery.md)을 참조 하세요.