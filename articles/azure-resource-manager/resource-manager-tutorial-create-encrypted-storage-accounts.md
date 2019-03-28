---
title: Azure Resource Manager 템플릿 참조 활용 | Microsoft Docs
description: Azure Resource Manager 템플릿 참조를 활용하여 암호화된 스토리지 계정을 배포하는 템플릿을 만듭니다.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/04/2019
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 68199083369018cab1c9f83dd6781653a8cae37c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57849298"
---
# <a name="tutorial-utilize-the-azure-resource-manager-template-reference"></a>자습서: Azure Resource Manager 템플릿 참조 활용

템플릿 스키마 정보를 찾고, 그 정보를 사용하여 Azure Resource Manager 템플릿을 만드는 방법을 알아봅니다.

이 자습서에서는 Azure 빠른 시작 템플릿의 기본 템플릿을 사용합니다. 템플릿 참조 설명서를 사용하고, 템플릿을 사용자 지정하여 암호화된 저장소 계정을 만듭니다.

![Resource Manager 템플릿 참조가 암호화된 스토리지 계정 배포](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-tutorial-deploy-encrypted-storage-account.png)

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 빠른 시작 템플릿 열기
> * 템플릿 이해
> * 템플릿 참조 찾기
> * 템플릿 편집
> * 템플릿 배포

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 문서를 완료하려면 다음이 필요합니다.

* [Visual Studio Code](https://code.visualstudio.com/) 및 [Resource Manager 도구 확장](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)

## <a name="open-a-quickstart-template"></a>빠른 시작 템플릿 열기

[Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/)은 Resource Manager 템플릿용 저장소입니다. 템플릿을 처음부터 새로 만드는 대신 샘플 템플릿을 찾아서 사용자 지정할 수 있습니다. 이 빠른 시작에서 사용되는 템플릿은 [표준 저장소 계정 만들기](https://azure.microsoft.com/resources/templates/101-storage-account-create/)라고 합니다. 이 템플릿은 Azure Storage 계정 리소스를 정의합니다.

1. Visual Studio Code에서 **파일**>**파일 열기**를 차례로 선택합니다.
2. **파일 이름**에서 다음 URL을 붙여넣습니다.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. **열기**를 선택하여 파일을 엽니다.
4. **파일**>**이름으로 저장**을 차례로 선택하여 파일을 **azuredeploy.json**으로 저장합니다.

## <a name="understand-the-schema"></a>스키마 이해

1. VS Code에서 템플릿을 루트 수준으로 축소합니다. 다음 요소가 포함된 가장 간단한 구조가 표시됩니다.

    ![Resource Manager 템플릿의 가장 간단한 구조](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-simplest-structure.png)

    * **$schema**: 템플릿 언어의 버전을 설명하는 JSON 스키마 파일의 위치입니다.
    * **contentVersion**: 템플릿의 중대한 변화를 나타내도록 이 요소의 값을 지정합니다.
    * **parameters**: 배포를 실행하여 리소스 배포를 사용자 지정할 때 제공되는 값을 지정합니다.
    * **variables**: 템플릿에서 템플릿 언어 식을 단순화하는 JSON 조각으로 사용되는 값을 지정합니다.
    * **resources**: 리소스 그룹에 배포 또는 업데이트되는 리소스 종류를 지정합니다.
    * **outputs**: 배포 후 반환되는 값을 지정합니다.

2. **리소스**를 확장합니다. `Microsoft.Storage/storageAccounts` 리소스가 정의되어 있습니다. 이 템플릿은 암호화되지 않은 저장소 계정을 만듭니다.

    ![Resource Manager 템플릿 저장소 계정 정의](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resource.png)

## <a name="find-the-template-reference"></a>템플릿 참조 찾기

1. [Azure 템플릿 참조](https://docs.microsoft.com/azure/templates/)로 이동합니다.
2. **제목으로 필터링**에 **스토리지 계정**을 입력합니다.
3. 다음 스크린샷과 같이 **참조/템플릿 참조/스토리지/&lt;버저>/스토리지 계정**을 차례로 선택합니다.

    ![Resource Manager 템플릿 참조 저장소 계정](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts.png)

    선택할 버전을 모르는 경우 최신 버전을 사용합니다.

4. 암호화 관련 정의 정보를 탐색합니다.  

    ```json
    "encryption": {
      "services": {
        "blob": {
          "enabled": boolean
        },
        "file": {
          "enabled": boolean
        }
      },
      "keySource": "string",
      "keyvaultproperties": {
        "keyname": "string",
        "keyversion": "string",
        "keyvaulturi": "string"
      }
    },
    ```

    같은 웹 페이지에서, 다음 설명은 `encryption` 개체가 암호화된 저장소 계정을 만드는 데 사용된다는 것을 확인합니다.

    ![Resource Manager 템플릿 참조 저장소 계정 암호화](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts-encryption.png)

    두 가지 방법으로 암호화 키를 관리할 수 있습니다. Storage 서비스 암호화를 사용하는 Microsoft 관리 암호화 키를 사용하거나 사용자 고유의 암호화 키를 사용할 수 있습니다. 이 자습서를 간단하게 유지하기 위해 `Microsoft.Storage` 옵션을 사용하겠습니다. 그러면 Azure Key Vault를 만들 필요가 없습니다.

    ![Resource Manager 템플릿 참조 저장소 계정 암호화 개체](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts-encryption-object.png)

    암호화 개체는 다음과 유사합니다.

    ```json
    "encryption": {
        "services": {
            "blob": {
                "enabled": true
            },
            "file": {
              "enabled": true
            }
        },
        "keySource": "Microsoft.Storage"
    }
    ```

## <a name="edit-the-template"></a>템플릿 편집

Visual Studio Code에서 리소스 요소가 다음과 같이 표시되도록 템플릿을 수정합니다.

![Resource Manager 템플릿 암호화된 저장소 계정 리소스](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resources.png)

## <a name="deploy-the-template"></a>템플릿 배포

배포 절차는 Visual Studio Code 빠른 시작의 [템플릿 배포](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) 섹션을 참조하세요.

다음 스크린샷에서는 새로 만든 스토리지 계정을 나열하고 Blob Storage에 암호화를 사용하도록 설정되었는지 여부를 나타내는 CLI 명령을 보여 줍니다.

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
