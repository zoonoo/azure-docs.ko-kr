---
title: Azure DevTest Labs에서 공유 이미지 갤러리 구성 | Microsoft Docs
description: Azure DevTest Labs에서 공유 이미지 갤러리를 구성 하는 방법 알아보기
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
ms.date: 05/30/2019
ms.author: spelluru
ms.openlocfilehash: 51b394043f88789865edea5be6376ae536f88848
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420440"
---
# <a name="configure-a-shared-image-gallery-in-azure-devtest-labs"></a>Azure DevTest Labs에서 공유 이미지 갤러리 구성
DevTest Labs 이제 지원 합니다 [공유 이미지 갤러리](/virtual-machines/windows/shared-image-galleries.md) 기능입니다. 랩 사용자를가 랩 리소스를 만드는 동안 공유 위치에서 이미지를 액세스할 수 있습니다. 또한 구조와 구성을 사용자 지정 관리 되는 VM 이미지 주위를 빌드할 수 있습니다. 공유 이미지 갤러리 기능을 지원합니다.

- 이미지의 관리 되는 전역 복제
- 버전 관리 및 관리 간소화 하기 위해 이미지의 그룹화
- 가용성 영역을 지 원하는 지역에서 영역 중복 저장소 (ZRS) 계정으로 항상 사용 가능한 이미지를 확인 합니다. ZRS는 영역 오류에 대 한 더 나은 복원 력을 제공합니다.
- 구독 간에 및 역할 기반 액세스 제어 (RBAC)를 사용 하 여 테 넌 트 간에 공유 합니다.

자세한 내용은 [공유 이미지 갤러리 설명서](../virtual-machines/windows/shared-image-galleries.md)합니다. 
 
유지 관리해야 하는 관리되는 이미지가 많고, 회사 전체에서 사용 가능하도록 하려면 공유 이미지 갤러리를 이미지를 쉽게 업데이트하고 공유할 수 있게 해주는 리포지토리로 사용할 수 있습니다. 랩 소유자는 랩에 기존 공유 이미지 갤러리를 연결할 수 있습니다. 이 갤러리 연결 되 면 랩 사용자는 이러한 최신 이미지에서 컴퓨터를 만들 수 있습니다. 이 기능의 주요 혜택은 DevTest Labs는 lab에서, 구독 전체의 및 지역에 걸쳐 이미지를 공유의 이점은 사용할 수 있습니다. 

## <a name="considerations"></a>고려 사항
- 한 번에 하나의 공유 이미지 갤러리 랩에만 연결할 수 있습니다. 다른 갤러리를 연결 하려는 경우에 기존을 분리 및 다른 연결을 해야 합니다. 
- 현재 DevTest Labs는 랩 통해 갤러리로 업로드 이미지를 지원 하지 않습니다. 
- 공유 이미지 갤러리 이미지를 사용 하 여 가상 컴퓨터를 만드는 동안 DevTest Labs이이 이미지의 최신 게시 된 버전을 항상 사용 합니다.
- DevTest Labs에는 자동으로 공유 이미지 갤러리 이미지를 랩이 존재 하는 지역에 복제를 확인 하려고 최대한가, 있지만 항상 가능한 것은 아닙니다. 이러한 이미지에서 Vm을 만드는 데 문제가 발생 하는 사용자를 방지 하려면 확인 이미지는 이미 랩의 지역에 복제 됩니다. "

## <a name="use-azure-portal"></a>Azure Portal 사용
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 선택 **모든 서비스** 왼쪽된 탐색 메뉴에서.
1. 선택 **DevTest Labs** 목록에서.
1. 랩 목록에서 선택 하 **랩**합니다.
1. 선택 **구성 및 정책** 에 **설정** 왼쪽 메뉴의 섹션입니다.
1. 선택 **공유 이미지 갤러리** 아래에서 **가상 머신 기반** 왼쪽된 메뉴에서.
1. 클릭 하 여 랩에 기존 공유 이미지 갤러리를 연결 합니다 **연결** 단추 및 드롭다운 목록에서 갤러리를 선택 합니다.
1. 연결 된 갤러리로 이동 하 고 구성 하려면 갤러리 **사용할지** VM 생성에 대 한 이미지를 공유 합니다.
1. 랩 사용자가 클릭 하 여 설정 된 이미지를 사용 하 여 가상 머신을 만들 수 **+ 추가** 이미지를 찾아 합니다 **베이스가 선택** 페이지입니다.

## <a name="use-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용

### <a name="attach-a-shared-image-gallery-to-your-lab"></a>공유 이미지 갤러리를 랩에 연결
Azure Resource Manager 템플릿을 공유 이미지 갤러리를 랩 연결할를 사용 하는 경우 다음 예와에서 같이 Resource Manager 템플릿의 리소스 섹션 아래에 추가 해야 있습니다.

```json
"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs",
    "name": "[parameters('newLabName')]",
    "location": "[resourceGroup (). location]",
    "resources": [
    {
        "apiVersion": "2018-10-15-preview",
        "name": "[variables('labVirtualNetworkName')]",
        "type": "virtualNetworks",
        "dependsOn": [
            "[resourceId('Microsoft.DevTestLab/labs', parameters('newLabName'))]"
        ]
    },
    {
        "apiVersion":"2018-10-15-preview",
        "name":"myGallery",
        "type":"sharedGalleries",
        "properties": {
            "galleryId":"[parameters('existingSharedGalleryId')]",
            "allowAllImages": "Enabled"
        },
        "dependsOn":[
            "[resourceId('Microsoft.DevTestLab/labs', parameters('newLabName'))]"
        ]
    }
    ]
} 

```

전체 Resource Manager 템플릿 예제를 공용 GitHub 리포지토리에서 이러한 Resource Manager 템플릿 샘플을 참조 하세요. [공유 이미지 갤러리에서 랩을 만드는 동안 구성](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-lab-shared-gallery-configured)합니다.

### <a name="create-a-vm-using-an-image-from-the-shared-image-gallery"></a>공유 이미지 갤러리에서 이미지를 사용 하 여 VM 만들기
Azure Resource Manager 템플릿을 공유 이미지 갤러리 이미지를 사용 하 여 가상 머신을 만들려면를 사용 하는 경우 다음 샘플을 사용 합니다.

```json

"resources": [
{
    "apiVersion": "2018-10-15-preview",
    "type": "Microsoft.DevTestLab/labs/virtualMachines",
    "name": "[variables('resourceName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "sharedImageId": "[parameters('existingSharedImageId')]",
        "size": "[parameters('newVMSize')]",
        "isAuthenticationWithSshKey": false,
        "userName": "[parameters('userName')]",
        "sshKey": "",
        "password": "[parameters('password')]",
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "labSubnetName": "[variables('labSubnetName')]"
    }
}
],

```

자세한 내용은 공용 GitHub에서 이러한 Resource Manager 템플릿 샘플을 참조 하세요.
[공유 이미지 갤러리 이미지를 사용 하 여 가상 머신 만들기](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-sharedimage)합니다.

## <a name="use-api"></a>API 사용

- API 버전 2018-10-15-미리 보기를 사용 합니다.
- 갤러리를 연결 하려면 다음 코드 조각과에서 같이 요청을 보냅니다.
    
    ``` 
    PUT [Lab Resource Id]/SharedGalleries/[newGalleryName]
    Body: 
    {
        “properties”:{
            “galleryId”: “[Shared Image Gallery resource Id]”,
            “allowAllImages”:”Enabled”
        }
    }
    ```
- 공유 이미지 갤러리에서 모든 이미지를 보려면 해당 리소스 Id에서 모든 공유 이미지를 나열할 수 있습니다.

    ```
    GET [Lab Resource Id]/SharedGalleries/mySharedGallery/SharedImages
    ````
- 공유 이미지를 사용 하 여 VM을 만들려면 가상 컴퓨터 속성 및 virtual machines에서 PUT을 수행, 공유에 대 한 이전 호출에서 가져온 이미지의 ID를 전달할 수 있습니다. 속성입니다. SharedImageId


## <a name="next-steps"></a>다음 단계
아티팩트에 다음 문서를 참조 하세요.

- [랩에 대 한 필수 아티팩트를 지정 합니다.](devtest-lab-mandatory-artifacts.md)
- [사용자 지정 아티팩트 만들기](devtest-lab-artifact-author.md)
- [랩에 아티팩트 리포지토리 추가](devtest-lab-artifact-author.md)
- [아티팩트 실패 진단](devtest-lab-troubleshoot-artifact-failure.md)
