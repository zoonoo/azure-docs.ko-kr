---
title: Azure DevTest 랩에 중첩된 템플릿 환경 배포
description: 중첩된 Azure 리소스 관리자 템플릿을 배포하여 Azure DevTest 랩을 사용하여 환경을 제공하는 방법을 알아봅니다.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: e83bc4e77a44f20d55fa3b56bc81aefd1d25bb03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76168832"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>테스트 환경에 중첩된 Azure 리소스 관리자 템플릿 배포
중첩된 배포를 사용하면 기본 리소스 관리자 템플릿 내에서 다른 Azure 리소스 관리자 템플릿을 실행할 수 있습니다. 이를 통해 배포를 대상 및 목적별 템플릿 집합으로 분해할 수 있습니다. 테스트, 재사용 및 가독성 측면에서 이점을 제공합니다. Azure [리소스를 배포할 때 연결된 템플릿 사용](../azure-resource-manager/templates/linked-templates.md) 문서에서는 여러 코드 샘플을 사용하여 이 솔루션에 대한 좋은 개요를 제공합니다. 이 문서에서는 Azure DevTest 랩과 관련된 예제를 제공합니다. 

## <a name="key-parameters"></a>주요 매개 변수
처음부터 고유한 리소스 관리자 템플릿을 만들 수 있지만 템플릿을 쉽게 개발하고 디버깅할 수 있는 Visual Studio에서 [Azure 리소스 그룹 프로젝트를](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) 사용하는 것이 좋습니다. azuredeploy.json에 중첩 된 배포 리소스를 추가 하는 경우 Visual Studio 템플릿을 보다 유연 하 게 만들기 위해 여러 항목을 추가 합니다. 이러한 항목에는 보조 템플릿 및 매개 변수 파일이 있는 하위 폴더, 기본 템플릿 파일 내의 변수 이름 및 새 파일의 저장소 위치에 대한 두 개의 매개 변수가 포함됩니다. **_artifactsLocation** **및 _artifactsLocationSasToken** DevTest 연구소에서 사용하는 주요 매개 변수입니다. 

DevTest Labs가 환경에서 작동하는 방식에 익숙하지 않은 경우 [Azure 리소스 관리자 템플릿을 사용하여 다중 VM 환경 및 PaaS 리소스 만들기를](devtest-lab-create-environment-from-arm.md)참조하십시오. 템플릿은 DevTest Labs의 랩에 연결된 리포지토리에 저장됩니다. 이러한 템플릿을 사용하여 새 환경을 만들면 파일이 랩의 Azure Storage 컨테이너로 이동됩니다. 중첩된 파일을 식별하고 복사할 수 있도록 DevTest Labs는 _artifactsLocation 및 _artifactsLocationSasToken 매개 변수를 식별하고 하위 폴더를 저장소 컨테이너에 복사합니다. 그런 다음 위치 및 SaS(공유 액세스 서명) 토큰을 매개 변수에 자동으로 삽입합니다. 

## <a name="nested-deployment-example"></a>중첩된 배포 예제
다음은 중첩된 배포의 간단한 예입니다.

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

이 템플릿을 포함하는 저장소의 폴더에는 `nestedtemplates` **NestOne.json 및 NestOne.parameters.json** 파일이 있는 하위 **폴더가**있습니다. **azuredeploy.json에서**템플릿에 대한 URI는 아티팩트 위치, 중첩된 템플릿 폴더, 중첩된 템플릿 파일 이름을 사용하여 빌드됩니다. 마찬가지로 매개 변수에 대한 URI는 아티팩트 위치, 중첩된 템플릿 폴더 및 중첩된 템플릿에 대한 매개 변수 파일을 사용하여 빌드됩니다. 

다음은 Visual Studio에서 동일한 프로젝트 구조의 이미지입니다. 

![Visual Studio의 프로젝트 구조](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

기본 폴더에 폴더를 추가할 수 있지만 단일 수준보다 더 깊지는 않습니다. 

## <a name="next-steps"></a>다음 단계
환경에 대한 자세한 내용은 다음 문서를 참조하십시오. 

- [Azure Resource Manager 템플릿으로 다중 VM 환경 및 PaaS 리소스 만들기](devtest-lab-create-environment-from-arm.md)
- [Azure DevTest Labs에서 공용 환경 구성 및 사용](devtest-lab-configure-use-public-environments.md)
- [Azure DevTest 랩에서 환경을 랩의 가상 네트워크에 연결합니다.](connect-environment-lab-virtual-network.md)