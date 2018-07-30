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
ms.date: 07/20/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7c78636a210ae90c5bfe1d0bfd35e4e05633f5cd
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188202"
---
# <a name="tutorial-create-an-azure-resource-manager-template-for-deploying-an-encrypted-storage-account"></a>자습서: 암호화된 저장소 계정을 배포하기 위한 Azure Resource Manager 템플릿 만들기

Azure Resource Manager 템플릿을 완료하는 방법에 대한 정보를 찾는 방법을 알아봅니다.

이 자습서에서는 Azure 빠른 시작 템플릿의 기본 템플릿을 사용하여 Azure Storage 계정을 만듭니다.  템플릿 참조 설명서를 사용하고, 기본 템플릿을 사용자 지정하여 암호화된 저장소 계정을 만듭니다.

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 빠른 시작 템플릿 열기
> * 템플릿 형식의 이해
> * 템플릿에서 매개 변수 사용
> * 템플릿에서 변수 사용
> * 템플릿 편집
> * 템플릿 배포

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 문서를 완료하려면 다음이 필요합니다.

* [Visual Studio Code](https://code.visualstudio.com/)
* Resource Manager 도구 확장. 설치하려면 [Resource Manager 도구 확장 설치](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)를 참조하세요.

## <a name="open-a-quickstart-template"></a>빠른 시작 템플릿 열기

이 빠른 시작에서 사용되는 템플릿은 [표준 저장소 계정 만들기](https://azure.microsoft.com/resources/templates/101-storage-account-create/)라고 합니다. 이 템플릿은 Azure Storage 계정 리소스를 정의합니다.

1. Visual Studio Code에서 **파일**>**파일 열기**를 차례로 선택합니다.
2. **파일 이름**에서 다음 URL을 붙여넣습니다.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. **열기**를 선택하여 파일을 엽니다.
4. **파일**>**이름으로 저장**을 차례로 선택하여 파일을 **azuredeploy.json**으로 저장합니다.

## <a name="understand-the-format"></a>형식의 이해

VS Code에서 템플릿을 루트 수준으로 축소합니다. 다음 요소가 포함된 가장 간단한 구조가 표시됩니다.

![Resource Manager 템플릿의 가장 간단한 구조](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-simplest-structure.png)

* **$schema**: 템플릿 언어의 버전을 설명하는 JSON 스키마 파일의 위치입니다.
* **contentVersion**: 템플릿의 중대한 변화를 나타내도록 이 요소의 값을 지정합니다.
* **parameters**: 배포를 실행하여 리소스 배포를 사용자 지정할 때 제공되는 값을 지정합니다.
* **variables**: 템플릿에서 템플릿 언어 식을 단순화하는 JSON 조각으로 사용되는 값을 지정합니다.
* **resources**: 리소스 그룹에 배포 또는 업데이트되는 리소스 종류를 지정합니다.
* **outputs**: 배포 후 반환되는 값을 지정합니다.

## <a name="use-parameters-in-template"></a>템플릿에서 매개 변수 사용

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

## <a name="use-variables-in-template"></a>템플릿에서 변수 사용

변수를 사용하면 템플릿 전체에서 사용 가능한 값을 생성할 수 있습니다. 변수는 템플릿의 복잡성을 줄이는 데 도움이 됩니다.

![Resource Manager 템플릿 변수](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-variables.png)

이 템플릿은 한 가지 변수 *storageAccountName*을 정의합니다. 정의에는 다음과 같은 두 가지 템플릿 함수가 사용됩니다.

- **concat()**: 문자열을 연결합니다. 자세한 내용은 [concat](./resource-group-template-functions-string.md#concat)을 참조하세요.
- **uniqueString()**: 매개 변수로 제공된 값을 기반으로 결정 해시 문자열을 만듭니다. 각 Azure 저장소 계정에는 Azure 전체에서 고유한 이름이 있어야 합니다. 이 함수는 고유한 문자열을 제공합니다. 더 많은 문자열 함수를 보려면 [문자열 함수](./resource-group-template-functions-string.md)를 참조하세요.

템플릿에 정의된 변수를 사용하려면:

```json
"name": "[variables('storageAccountName')]"
```

## <a name="edit-the-template"></a>템플릿 편집

저장소 계정 암호화와 관련된 구성을 찾으려면 Azure Storage 계정의 템플릿 참조를 사용하면 됩니다.

1. [Azure 템플릿](https://docs.microsoft.com/azure/templates/)으로 이동합니다.
2. 왼쪽의 TOC에서 **참조**->**저장소**->**저장소 계정**을 선택합니다. 이 페이지에는 저장소 계정 정보를 정의하기 위한 정보가 포함되어 있습니다.
3. 암호화 관련 정보를 탐색합니다.  
4. 저장소 계정 리소스 정의의 속성 요소 내에서 다음 json을 추가합니다.

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
    이 부분은 BLOB 저장소 서비스의 암호화 기능을 활성화합니다.

최종 리소스 요소는 다음과 같습니다.

![Resource Manager 템플릿 암호화된 저장소 계정 리소스](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resources.png)

## <a name="deploy-the-template"></a>템플릿 배포

템플릿을 배포하는 방법에는 여러 가지가 있습니다.  이 자습서에서는 Azure Portal에서 Cloud Shell을 사용합니다. Cloud Shell은 Azure CLI와 Azure PowerShell을 모두 지원합니다. 여기에 제공되는 지침에서는 CLI를 사용합니다.

1. [Azure 포털](https://portal.azure.com)
2. 다음 이미지와 같이 오른쪽 위 모서리에서 **Cloud Shell**을 선택합니다.

    ![Azure Portal - Cloud Shell](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell.png)

3. 아래쪽 화살표를 선택하고, Bash가 아닌 경우 **Bash**를 선택합니다. 이 자습서에서는 Azure CLI를 사용합니다.

    ![Azure Portal - Cloud Shell에서 CLI로 전환](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-choose-cli.png)
4. **다시 시작**을 선택하여 셸을 다시 시작합니다.
5. **파일 업로드/다운로드**를 선택한 다음, **업로드**를 선택합니다.

    ![Azure Portal - Cloud Shell 파일 업로드](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-upload-file.png)
6. 자습서의 앞부분에서 저장한 파일을 선택합니다. 기본 이름은 **azuredeploy.json**입니다.
7. Cloud Shell에서 **ls** 명령을 실행하여 파일이 성공적으로 업로드되었는지 확인합니다. **cat** 명령을 사용하여 템플릿 내용을 확인할 수도 있습니다.

    ![Azure Portal - Cloud Shell 파일 나열](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-list-file.png)
8. Cloud Shell에서 다음 명령을 실행합니다.

    ```cli
    az group create --name <ResourceGroupName> --location <AzureLocation>

    az group deployment create --name <DeploymentName> --resource-group <ResourceGroupName> --template-file azuredeploy.json
    ```
    샘플 배포의 스크린샷은 다음과 같습니다.

    ![Azure Portal - Cloud Shell 템플릿 배포](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-deploy-template.png)

    스크린샷에서 사용된 값은 다음과 같습니다.

    * **&lt;ResourceGroupName>**: myresourcegroup0719. 매개 변수에는 두 가지 표현이 있습니다.  동일한 값을 사용해야 합니다.
    * **&lt;AzureLocation>**: eastus2
    * **&lt;DeployName>**: mydeployment0719
    * **&lt;TemplateFile>**: azuredeploy.json

    스크린샷 출력에서 저장소 계정 이름은 *fhqbfslikdqdsstandardsa*입니다. 

9. 다음 PowerShell 명령을 실행하여 새로 만든 저장소 계정을 나열합니다.

    ```cli
    az storage account show --resource-group <ResourceGroupName> --name <StorageAccountName>
    ```

    BLOB 저장소에 암호화를 사용하도록 설정되었음을 나타내는 다음 스크린샷과 유사한 출력이 표시됩니다.

    ![Azure Resource Manager 암호화된 저장소 계정](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-account.png)

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않은 경우 리소스 그룹을 삭제하여 배포한 리소스를 정리합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
3. 해당 리소스 그룹 이름을 선택합니다.  리소스 그룹에 총 6개의 리소스가 표시됩니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 템플릿 참조를 사용하여 기존 템플릿을 사용자 지정하는 방법을 알아보았습니다. 이 자습서에 사용된 템플릿에는 하나의 Azure 리소스만 포함되어 있습니다.  다음 자습서에서는 여러 리소스가 있는 템플릿을 개발합니다.  일부 리소스에는 종속 리소스가 있습니다.

> [!div class="nextstepaction"]
> [여러 리소스 만들기](./resource-manager-tutorial-create-templates-with-dependent-resources.md)
