---
title: 템플릿 참조 활용
description: Azure Resource Manager 템플릿 참조를 활용하여 템플릿을 만듭니다.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: b742982121a20a2b057eba4211584b0386dde411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373179"
---
# <a name="tutorial-utilize-the-arm-template-reference"></a>자습서: ARM 템플릿 참조 활용

템플릿 스키마 정보를 찾고, 그 정보를 사용하여 ARM(Azure Resource Manager) 템플릿을 만드는 방법을 알아봅니다.

이 자습서에서는 Azure 빠른 시작 템플릿의 기본 템플릿을 사용합니다. 템플릿 참조 설명서를 사용하여 템플릿을 사용자 지정합니다.

![Resource Manager 템플릿 참조를 스토리지 계정에 배포](./media/template-tutorial-use-template-reference/resource-manager-template-tutorial-deploy-storage-account.png)

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 빠른 시작 템플릿 열기
> * 템플릿 이해
> * 템플릿 참조 찾기
> * 템플릿 편집
> * 템플릿 배포

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서를 완료하려면 다음이 필요합니다.

* Resource Manager 도구 확장이 포함된 Visual Studio Code. [Visual Studio Code를 사용하여 ARM 템플릿 만들기](use-vs-code-to-create-template.md)를 참조하세요.

## <a name="open-a-quickstart-template"></a>빠른 시작 템플릿 열기

[Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/)은 ARM 템플릿용 리포지토리입니다. 템플릿을 처음부터 새로 만드는 대신 샘플 템플릿을 찾아서 사용자 지정할 수 있습니다. 이 빠른 시작에서 사용되는 템플릿은 [표준 스토리지 계정 만들기](https://azure.microsoft.com/resources/templates/101-storage-account-create/)라고 합니다. 이 템플릿은 Azure Storage 계정 리소스를 정의합니다.

1. Visual Studio Code에서 **파일**>**파일 열기**를 차례로 선택합니다.
1. **파일 이름**에서 다음 URL을 붙여넣습니다.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

1. **열기**를 선택하여 파일을 엽니다.
1. **파일**>**이름으로 저장**을 차례로 선택하여 파일을 **azuredeploy.json**으로 저장합니다.

## <a name="understand-the-schema"></a>스키마 이해

1. VS Code에서 템플릿을 루트 수준으로 축소합니다. 다음 요소가 포함된 가장 간단한 구조가 표시됩니다.

    ![Resource Manager 템플릿의 가장 간단한 구조](./media/template-tutorial-use-template-reference/resource-manager-template-simplest-structure.png)

    * **$schema**: 템플릿 언어의 버전을 설명하는 JSON 스키마 파일의 위치입니다.
    * **contentVersion**: 템플릿의 중대한 변화를 나타내도록 이 요소의 값을 지정합니다.
    * **parameters**: 배포를 실행하여 리소스 배포를 사용자 지정할 때 제공되는 값을 지정합니다.
    * **variables**: 템플릿에서 템플릿 언어 식을 단순화하는 JSON 조각으로 사용되는 값을 지정합니다.
    * **resources**: 리소스 그룹에 배포 또는 업데이트되는 리소스 종류를 지정합니다.
    * **outputs**: 배포 후 반환되는 값을 지정합니다.

1. **리소스**를 확장합니다. `Microsoft.Storage/storageAccounts` 리소스가 정의되어 있습니다.

    ![Resource Manager 템플릿 스토리지 계정 정의](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resource.png)

## <a name="find-the-template-reference"></a>템플릿 참조 찾기

1. [Azure 템플릿 참조](https://docs.microsoft.com/azure/templates/)로 이동합니다.
1. **제목별 필터링** 상자에 **스토리지 계정**을 입력하고 **참조 > 스토리지**에서 첫 번째 **스토리지 계정**을 선택합니다.

    ![Resource Manager 템플릿 참조 스토리지 계정](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts.png)

    리소스 공급자에는 일반적으로 다음과 같은 몇 가지 API 버전이 있습니다.

    ![Resource Manager 템플릿 참조 스토리지 계정 버전](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-versions.png)

1. 왼쪽 창에서 **스토리지** 아래에 **모든 리소스**를 선택합니다. 이 페이지에는 스토리지 리소스 공급자의 리소스 유형 및 버전이 나열되어 있습니다. 템플릿에 정의된 리소스 유형에 대해서는 최신 API 버전을 사용하는 것이 좋습니다.

    ![Resource Manager 템플릿 참조 스토리지 계정 유형 버전](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-types-versions.png)

1. **storageAccount** 리소스 종류의 최신 버전을 선택합니다.  이 문서를 작성할 때 최신 버전은 **2019-06-01**입니다.

1. 이 페이지에는 storageAccount 리소스 종류의 세부 정보가 나열되어 있습니다.  예를 들어 **Sku** 개체에 대해 허용되는 값을 나열합니다. 이전에 열었던 빠른 시작 템플릿에 나열된 것보다 많은 SKU가 있습니다. 사용 가능한 모든 스토리지 유형을 포함하도록 빠른 시작 템플릿을 사용자 지정할 수 있습니다.

    ![Resource Manager 템플릿 참조 스토리지 계정 SKU](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-skus.png)

## <a name="edit-the-template"></a>템플릿 편집

Visual Studio Code에서 다음 스크린샷에 표시된 대로 추가 스토리지 계정 유형을 추가합니다.

![Resource Manager 템플릿 스토리지 계정 리소스](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus.png)

## <a name="deploy-the-template"></a>템플릿 배포

배포 절차는 Visual Studio Code 빠른 시작의 [템플릿 배포](quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) 섹션을 참조하세요. 템플릿을 배포할 때 새로 추가된 값을 사용하여 **storageAccountType** 매개 변수를 지정합니다(예: **Premium_ZRS**). **Premium_ZRS**가 허용되는 값이 아니므로 원래 빠른 시작 템플릿을 사용하면 배포가 실패합니다.

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않은 경우 리소스 그룹을 삭제하여 배포한 리소스를 정리합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
3. 해당 리소스 그룹 이름을 선택합니다.  리소스 그룹에 총 6개의 리소스가 표시됩니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 템플릿 참조를 사용하여 기존 템플릿을 사용자 지정하는 방법을 알아보았습니다. 여러 스토리지 계정 인스턴스를 만드는 방법을 알아보려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [여러 인스턴스 만들기](./template-tutorial-create-multiple-instances.md)
