---
title: Azure DevTest Labs에서 중첩 된 Resource Manager 템플릿을 환경에 배포 | Microsoft Docs
description: Azure DevTest Labs를 사용 하 여 환경을 제공 하려면 중첩 된 Azure Resource Manager 템플릿을 배포 하는 방법을 알아봅니다.
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
ms.openlocfilehash: eec0cde4a36449f85998bfb04d16f1d52c68bb19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65835287"
---
# <a name="deploy-nested-azure-resource-manager-templates-for-testing-environments"></a>테스트 환경에 대 한 중첩 된 Azure Resource Manager 템플릿 배포
중첩 된 배포를 사용 하면 주 Resource Manager 템플릿 내에서 다른 Azure Resource Manager 템플릿을 실행할 수 있습니다. 대상 및 특정 용도의 템플릿 집합으로 배포를 분해할 수 있습니다. 테스트, 재사용 및 가독성 측면에서 이점을 제공합니다. 이 문서 [Azure 리소스를 배포 하는 경우 연결 된 템플릿을 사용 하 여](../azure-resource-manager/resource-group-linked-templates.md) 몇 가지 코드 샘플을 사용 하 여이 솔루션의 개요를 제공 합니다. 이 문서에서는 Azure DevTest Labs에 관련 된 예제를 제공 합니다. 

## <a name="key-parameters"></a>키 매개 변수
처음부터 고유한 Resource Manager 템플릿을 만들 수를 사용 하는 것이 좋습니다 합니다 [Azure 리소스 그룹 프로젝트](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) Visual Studio에서 쉽게 개발 하 고 템플릿 디버그 합니다. Azuredeploy.json를 중첩 된 배포 리소스를 추가 하면 Visual Studio 서식 파일을 보다 유연 하 게 여러 항목을 추가 합니다. 이러한 항목에는 보조 템플릿 및 매개 변수 파일, 주 템플릿 파일에서 변수 이름 및 새 파일에 대 한 저장소 위치에 대 한 두 개의 매개 변수를 사용 하 여 하위 폴더 포함 됩니다. 합니다 **_artifactsLocation** 하 고 **_artifactsLocationSasToken** 는 DevTest Labs를 사용 하는 키 매개 변수입니다. 

DevTest Labs 환경과 작동 하는 방법을 모르는 경우 참조 [Azure Resource Manager 템플릿으로 다중 VM 환경 및 PaaS 리소스 만들기](devtest-lab-create-environment-from-arm.md)합니다. 템플릿을은 DevTest Labs에서 랩에 연결 된 저장소에 저장 됩니다. 이러한 템플릿을 사용 하 여 새 환경을 만들면 랩에서 Azure Storage 컨테이너에 파일을 이동 됩니다. 식별 및 중첩 된 파일을 복사 하는 일을 할 수, DevTest Labs _artifactsLocation 및 _artifactsLocationSasToken 매개 변수를 식별 하 고 저장소 컨테이너까지 하위 폴더를 복사 합니다. 그런 다음이 자동으로 삽입 위치 및 공유 액세스 서명 (SaS) 토큰 매개 변수입니다. 

## <a name="nested-deployment-example"></a>중첩 된 배포 예제
중첩 된 배포의 간단한 예는 다음과 같습니다.

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

이 템플릿이 포함 된 저장소의 폴더에 하위 폴더 `nestedtemplates` 파일을 사용 하 여 **NestOne.json** 하 고 **NestOne.parameters.json**합니다. 에 **azuredeploy.json**, URI 템플릿 아티팩트 위치, 중첩 된 템플릿 폴더를 사용 하 여 빌드됩니다.에 대 한 중첩 된 템플릿 파일 이름입니다. 마찬가지로, 아티팩트 위치, 중첩 된 템플릿 폴더 및 매개 변수 파일을 사용 하 여 중첩 된 템플릿에 대 한 URI 매개 변수에 대해 빌드됩니다. 

Visual Studio에서 동일한 프로젝트 구조의 이미지는 다음과 같습니다. 

![Visual Studio의 프로젝트 구조](./media/deploy-nested-template-environments/visual-studio-project-structure.png)

기본 폴더에 있지만 단일 수준 보다 심층적인 되지 모든 추가 폴더를 추가할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
환경에 대 한 자세한 내용은 다음 문서를 참조 하세요. 

- [Azure Resource Manager 템플릿으로 다중 VM 환경 및 PaaS 리소스 만들기](devtest-lab-create-environment-from-arm.md)
- [구성 및 Azure DevTest Labs에서 공용 환경 사용](devtest-lab-configure-use-public-environments.md)
- [Azure DevTest Labs에서 랩의 가상 네트워크에는 환경 연결](connect-environment-lab-virtual-network.md)