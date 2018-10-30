---
title: 암호화된 저장소 계정을 배포하기 위한 Azure Resource Manager 템플릿 만들기 | Microsoft Docs
description: Visual Studio Code를 사용하여 암호화된 저장소 계정을 배포하기 위한 템플릿을 만듭니다.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/18/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: a3fc3e0cc30b379c84ac0ba12f733d2db4e41587
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945793"
---
# <a name="tutorial-create-an-azure-resource-manager-template-for-deploying-an-encrypted-storage-account"></a>자습서: 암호화된 저장소 계정을 배포하기 위한 Azure Resource Manager 템플릿 만들기

Azure Resource Manager 템플릿을 완료하는 방법에 대한 정보를 찾는 방법을 알아봅니다.

이 자습서에서는 Azure 빠른 시작 템플릿의 기본 템플릿을 사용하여 Azure Storage 계정을 만듭니다.  템플릿 참조 설명서를 사용하고, 기본 템플릿을 사용자 지정하여 암호화된 저장소 계정을 만듭니다.

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 빠른 시작 템플릿 열기
> * 템플릿 이해
> * 템플릿 편집
> * 템플릿 배포

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 문서를 완료하려면 다음이 필요합니다.

* [Visual Studio Code](https://code.visualstudio.com/) 및 [Resource Manager 도구 확장](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)

## <a name="open-a-quickstart-template"></a>빠른 시작 템플릿 열기

이 빠른 시작에서 사용되는 템플릿은 [표준 저장소 계정 만들기](https://azure.microsoft.com/resources/templates/101-storage-account-create/)라고 합니다. 이 템플릿은 Azure Storage 계정 리소스를 정의합니다.

1. Visual Studio Code에서 **파일**>**파일 열기**를 차례로 선택합니다.
2. **파일 이름**에서 다음 URL을 붙여넣습니다.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. **열기**를 선택하여 파일을 엽니다.
4. **파일**>**이름으로 저장**을 차례로 선택하여 파일을 **azuredeploy.json**으로 저장합니다.

## <a name="understand-the-schema"></a>스키마 이해

VS Code에서 템플릿을 루트 수준으로 축소합니다. 다음 요소가 포함된 가장 간단한 구조가 표시됩니다.

![Resource Manager 템플릿의 가장 간단한 구조](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-simplest-structure.png)

* **$schema**: 템플릿 언어의 버전을 설명하는 JSON 스키마 파일의 위치입니다.
* **contentVersion**: 템플릿의 중대한 변화를 나타내도록 이 요소의 값을 지정합니다.
* **parameters**: 배포를 실행하여 리소스 배포를 사용자 지정할 때 제공되는 값을 지정합니다.
* **variables**: 템플릿에서 템플릿 언어 식을 단순화하는 JSON 조각으로 사용되는 값을 지정합니다.
* **resources**: 리소스 그룹에 배포 또는 업데이트되는 리소스 종류를 지정합니다.
* **outputs**: 배포 후 반환되는 값을 지정합니다.

## <a name="use-parameters"></a>매개 변수 사용

매개 변수를 사용하여 특정 환경에 맞게 조정되는 값을 제공함으로써 배포를 사용자 지정할 수 있습니다. 저장소 계정의 값을 설정할 때 템플릿에 정의된 매개 변수를 사용합니다.

![Resource Manager 템플릿 매개 변수](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-parameters.png)

이 템플릿에는 두 가지 매개 변수가 정의됩니다. 템플릿 함수는 location.defaultValue에 사용됩니다.

```json
"defaultValue": "[resourceGroup().location]",
```

resourceGroup() 함수는 현재 리소스 그룹을 나타내는 개체를 반환합니다. 템플릿 함수 목록을 보려면 [Azure Resource Manager 템플릿 함수](./resource-group-template-functions.md)를 참조하세요.

템플릿에 정의된 매개 변수를 사용하려면:

```json
"location": "[parameters('location')]",
"name": "[parameters('storageAccountType')]"
```

## <a name="use-variables"></a>변수 사용

변수를 사용하면 템플릿 전체에서 사용 가능한 값을 생성할 수 있습니다. 변수는 템플릿의 복잡성을 줄이는 데 도움이 됩니다.

![Resource Manager 템플릿 변수](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-variables.png)

이 템플릿은 한 가지 변수 *storageAccountName*을 정의합니다. 정의에는 다음과 같은 두 가지 템플릿 함수가 사용됩니다.

- **concat()**: 문자열을 연결합니다. 자세한 내용은 [concat](./resource-group-template-functions-string.md#concat)을 참조하세요.
- **uniqueString()**: 매개 변수로 제공된 값을 기반으로 결정 해시 문자열을 만듭니다. 각 Azure 저장소 계정에는 Azure 전체에서 고유한 이름이 있어야 합니다. 이 함수는 고유 문자열을 제공합니다. 더 많은 문자열 함수를 보려면 [문자열 함수](./resource-group-template-functions-string.md)를 참조하세요.

템플릿에 정의된 변수를 사용하려면:

```json
"name": "[variables('storageAccountName')]"
```

## <a name="edit-the-template"></a>템플릿 편집

이 자습서의 목표는 암호화된 저장소 계정을 만드는 템플릿을 정의하는 것입니다.  샘플 템플릿은 암호화되지 않은 기본 저장소 계정만 만듭니다. 암호화 관련 구성을 찾으려면 Azure Storage 계정의 템플릿 참조를 사용하면 됩니다.

1. [Azure 템플릿](https://docs.microsoft.com/azure/templates/)으로 이동합니다.
2. **제목으로 필터링**에 **저장소 계정**을 입력합니다.
3. 다음 스크린샷에 표시된 대로 **참조/템플릿 참조/저장소/저장소 계정**을 선택합니다.

    ![Resource Manager 템플릿 참조 저장소 계정](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts.png)

4. 암호화 관련 정의 정보를 탐색합니다.  

    ```json
    "encryption": {
        "keySource": "Microsoft.Storage",
        "services": {
            "blob": {
                "enabled": true
            }
        }
    }
    ```
5. Visual Studio Code에서 최종 리소스 요소가 다음과 같이 표시되도록 템플릿을 수정합니다.
    
    ![Resource Manager 템플릿 암호화된 저장소 계정 리소스](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resources.png)

## <a name="deploy-the-template"></a>템플릿 배포

배포 절차는 Visual Studio Code 빠른 시작의 [템플릿 배포](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) 섹션을 참조하세요.

다음 스크린샷에서는 새로 만든 저장소 계정을 나열하고 Blob 저장소에 암호화를 사용하도록 설정되었는지 여부를 나타내는 CLI 명령을 보여 줍니다.

![Azure Resource Manager 암호화된 저장소 계정](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-account.png)

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않은 경우 리소스 그룹을 삭제하여 배포한 리소스를 정리합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
3. 해당 리소스 그룹 이름을 선택합니다.  리소스 그룹에 총 6개의 리소스가 표시됩니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 템플릿 참조를 사용하여 기존 템플릿을 사용자 지정하는 방법을 알아보았습니다. 여러 저장소 계정 인스턴스를 만드는 방법을 알아보려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [여러 인스턴스 만들기](./resource-manager-tutorial-create-multiple-instances.md)
