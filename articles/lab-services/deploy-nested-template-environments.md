---
title: Azure DevTest Labs에서 중첩 리소스 관리자 템플릿 환경 배포 Microsoft Docs
description: Azure DevTest Labs를 사용 하 여 환경을 제공 하기 위해 중첩 된 Azure Resource Manager 템플릿을 배포 하는 방법을 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: spelluru
ms.openlocfilehash: 675d2c670f5bc11c1d8b61bc96313e408f788dc3
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75976545"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>테스트 환경을 위한 중첩 된 Azure Resource Manager 템플릿 배포
중첩 된 배포를 사용 하면 주 리소스 관리자 템플릿 내에서 다른 Azure Resource Manager 템플릿을 실행할 수 있습니다. 이를 통해 배포를 대상 및 용도에 맞는 템플릿 집합으로 분해할 수 있습니다. 테스트, 재사용 및 가독성 측면에서 이점을 제공 합니다. [Azure 리소스를 배포할 때 연결 된 템플릿 사용](../azure-resource-manager/templates/linked-templates.md) 문서에서는 몇 가지 코드 샘플을 사용 하 여이 솔루션에 대 한 유용한 개요를 제공 합니다. 이 문서에서는 Azure DevTest Labs와 관련 된 예제를 제공 합니다. 

## <a name="key-parameters"></a>키 매개 변수
사용자 고유의 리소스 관리자 템플릿을 처음부터 만들 수 있지만, Visual Studio에서 [Azure 리소스 그룹 프로젝트](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) 를 사용 하 여 템플릿을 쉽게 개발 하 고 디버그할 수 있습니다. 중첩 된 배포 리소스를 azuredeploy에 추가 하면 Visual Studio에서 템플릿을 더 유연 하 게 만들기 위해 여러 항목을 추가 합니다. 이러한 항목에는 보조 템플릿 및 매개 변수 파일, 기본 템플릿 파일 내의 변수 이름, 새 파일의 저장소 위치에 대 한 두 개의 매개 변수를 포함 하는 하위 폴더가 포함 됩니다. **_ArtifactsLocation** 및 **_ArtifactsLocationSasToken** 는 DevTest Labs에서 사용 하는 키 매개 변수입니다. 

DevTest Labs가 환경에서 작동 하는 방식에 익숙하지 않은 경우 [Azure Resource Manager 템플릿으로 다중 VM 환경 및 PaaS 리소스 만들기](devtest-lab-create-environment-from-arm.md)를 참조 하세요. 템플릿은 DevTest Labs의 랩에 연결 된 리포지토리에 저장 됩니다. 이러한 템플릿을 사용 하 여 새 환경을 만들 때 파일은 랩에서 Azure Storage 컨테이너로 이동 합니다. DevTest Labs는 중첩 된 파일을 식별 하 고 복사할 수 있도록 _artifactsLocation 및 _artifactsLocationSasToken 매개 변수를 식별 하 고 하위 폴더를 저장소 컨테이너에 복사 합니다. 그런 다음 위치 및 SaS (공유 액세스 서명) 토큰을 매개 변수에 자동으로 삽입 합니다. 

## <a name="nested-deployment-example"></a>중첩 된 배포 예
다음은 중첩 된 배포에 대 한 간단한 예제입니다.

```json

"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": {
    "_artifactsLocation": {
        "type": "string"
    },
    "_artifactsLocationSasToken": {
        "type": "securestring"
    }},
"variables": {
    "NestOneTemplateFolder": "nestedtemplates",
    "NestOneTemplateFileName": "NestOne.json",
    "NestOneTemplateParametersFileName": "NestOne.parameters.json"},
    "resources": [
    {
        "name": "NestOne",
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2016-09-01",
        "dependsOn": [ ],
        "properties": {
            "mode": "Incremental",
            "templateLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            },
            "parametersLink": {
                "uri": "[concat(parameters('_artifactsLocation'), '/', variables('NestOneTemplateFolder'), '/', variables('NestOneTemplateParametersFileName'), parameters('_artifactsLocationSasToken'))]",
                "contentVersion": "1.0.0.0"
            }
        }    
    }],
"outputs": {}
```

이 템플릿을 포함 하는 리포지토리의 폴더에는 **Nestone. json** 및 **nestone**. `nestedtemplates` 파일을 포함 하는 하위 폴더가 있습니다. **Azuredeploy json**에서 템플릿의 URI는 아티팩트 위치, 중첩 된 템플릿 폴더, 중첩 된 템플릿 파일 이름을 사용 하 여 빌드됩니다. 마찬가지로 매개 변수에 대 한 URI는 아티팩트 위치, 중첩 된 템플릿 폴더 및 중첩 된 템플릿에 대 한 매개 변수 파일을 사용 하 여 빌드됩니다. 

다음은 Visual Studio에서 동일한 프로젝트 구조의 이미지입니다. 

![Visual Studio의 프로젝트 구조](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

기본 폴더에는 폴더를 추가할 수 있지만 단일 수준 보다는 더 이상 추가할 수 없습니다. 

## <a name="next-steps"></a>다음 단계
환경에 대 한 세부 정보는 다음 문서를 참조 하세요. 

- [Azure Resource Manager 템플릿으로 다중 VM 환경 및 PaaS 리소스 만들기](devtest-lab-create-environment-from-arm.md)
- [Azure DevTest Labs에서 공용 환경 구성 및 사용](devtest-lab-configure-use-public-environments.md)
- [Azure DevTest Labs에서 랩의 가상 네트워크에 환경 연결](connect-environment-lab-virtual-network.md)