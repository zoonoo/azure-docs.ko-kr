---
title: 연결된 Azure Resource Manager 템플릿 만들기 | Microsoft Docs
description: 가상 머신을 만들기 위해 연결된 Azure Resource Manager 템플릿을 만드는 방법 알아보기
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/30/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: b08013941c1cf83b3eb006543d699eb7e1356ff0
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50239987"
---
# <a name="tutorial-create-linked-azure-resource-manager-templates"></a>자습서: 연결된 Azure Resource Manager 템플릿 만들기

연결된 Azure Resource Manager 템플릿을 만드는 방법을 알아봅니다. 연결된 템플릿을 사용하여 하나의 템플릿이 다른 템플릿을 호출하도록 할 수 있습니다. 템플릿 모듈화에 적합합니다. 이 자습서에서는 가상 머신, 가상 네트워크 및 저장소 계정을 포함한 다른 종속 리소스를 만드는 [자습서: Resource Manager 템플릿을 사용하여 여러 리소스 인스턴스 만들기](./resource-manager-tutorial-create-multiple-instances.md)에서 사용되는 동일한 템플릿을 사용합니다. 저장소 계정 리소스를 연결된 템플릿으로 구분합니다.

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 빠른 시작 템플릿 열기
> * 연결된 템플릿 만들기
> * 연결된 템플릿 업로드
> * 연결된 템플릿에 연결
> * 종속성 구성
> * 템플릿 배포

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 문서를 완료하려면 다음이 필요합니다.

* [Visual Studio Code](https://code.visualstudio.com/) 및 [Resource Manager 도구 확장](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)
* 보안을 강화하려면 가상 머신 관리자 계정에 생성된 암호를 사용합니다. 암호를 생성하는 방법에 대한 샘플은 다음과 같습니다.

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault는 암호화 키 및 기타 비밀을 보호하기 위한 것입니다. 자세한 내용은 [자습서: Resource Manager 템플릿 배포에 Azure Key Vault 통합](./resource-manager-tutorial-use-key-vault.md)을 참조하세요. 또한 3개월 마다 암호를 업데이트하는 것도 좋습니다.

## <a name="open-a-quickstart-template"></a>빠른 시작 템플릿 열기

Azure 퀵 스타트 템플릿은 Resource Manager 템플릿용 저장소입니다. 템플릿을 처음부터 새로 만드는 대신 샘플 템플릿을 찾아서 사용자 지정할 수 있습니다. 이 자습서에 사용되는 템플릿의 이름은 [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)입니다. [자습서: Resource Manager 템플릿을 사용하여 여러 리소스 인스턴스 만들기](./resource-manager-tutorial-create-multiple-instances.md)에서 사용된 동일한 템플릿입니다. 다음으로 사용될 동일한 두 개의 템플릿 복사본을 저장합니다.

* **기본 템플릿**: 저장소 계정을 제외하고 모든 리소스를 만듭니다.
* **연결된 템플릿**: 저장소 계정을 만듭니다.

1. Visual Studio Code에서 **파일**>**파일 열기**를 차례로 선택합니다.
2. **파일 이름**에서 다음 URL을 붙여넣습니다.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. **열기**를 선택하여 파일을 엽니다.
4. 템플릿에 5개 리소스가 정의되어 있습니다.

    * `Microsoft.Storage/storageAccounts` [템플릿 참조](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)를 참조하세요. 
    * `Microsoft.Network/publicIPAddresses` [템플릿 참조](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)를 참조하세요. 
    * `Microsoft.Network/virtualNetworks` [템플릿 참조](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)를 참조하세요. 
    * `Microsoft.Network/networkInterfaces` [템플릿 참조](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)를 참조하세요. 
    * `Microsoft.Compute/virtualMachines` [템플릿 참조](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)를 참조하세요.

    템플릿을 사용자 지정하기 전에 템플릿의 몇 가지 기본적인 내용을 이해하면 유용합니다.
5. **파일**>**다른 이름으로 저장**을 선택하여 파일 복사본을 로컬 컴퓨터에 **azuredeploy.json**이라는 이름으로 저장합니다.
6. **파일**>**이름으로 저장**을 선택하여 **linkedTemplate.json**이라는 이름으로 다른 파일의 복사본을 만듭니다.

## <a name="create-the-linked-template"></a>연결된 템플릿 만들기

연결된 템플릿은 저장소 계정을 만듭니다. 연결된 템플릿은 저장소 계정을 만드는 독립 실행형 템플릿과 거의 동일합니다. 이 자습서에서 연결된 템플릿은 값을 기본 템플릿에 다시 전달해야 합니다. 이 값은 `outputs` 요소에서 정의됩니다.

1. 열려 있지 않은 경우 Visual Studio Code에서 linkedTemplate.json을 엽니다.
2. 다음과 같이 변경합니다.

    * 저장소 계정을 제외하고 모든 리소스를 제거합니다. 총 4개의 리소스를 제거합니다.
    * **출력** 요소를 업데이트하면 다음과 같이 표시됩니다.

        ```json
        "outputs": {
            "storageUri": {
                "type": "string",
                "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
              }
        }
        ```
        **storageUri**는 기본 템플릿에서 가상 머신 리소스 정의에 필요합니다.  출력 값으로 기본 템플릿에 값을 다시 전달합니다.
    * 사용되지 않는 매개 변수를 제거합니다. 이러한 매개 변수는 아래에 녹색 물결선을 가집니다. **위치**라는 매개 변수 하나만 남아 있어야 합니다.
    * **변수** 요소를 제거합니다. 이 자습서에서는 필요하지 않습니다.
    * **storageAccountName**이라는 매개 변수를 추가합니다. 기본 템플릿에서 연결된 템플릿으로 저장소 계정 이름이 매개 변수로 전달됩니다.

    완료되면 템플릿은 다음과 같습니다.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "storageAccountName":{
            "type": "string",
            "metadata": {
              "description": "Azure Storage account name."
            }
          },
          "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
              "description": "Location for all resources."
            }
          }
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('storageAccountName')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "sku": {
              "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
          }
        ],
        "outputs": {
            "storageUri": {
                "type": "string",
                "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
              }
        }
    }
    ```
3. 변경 내용을 저장합니다.

## <a name="upload-the-linked-template"></a>연결된 템플릿 업로드

배포를 실행하는 위치에서 템플릿에 액세스할 수 있어야 합니다. 이 위치는 Azure 저장소 계정, Github 또는 Dropbox일 수 있습니다. 템플릿에 중요한 정보가 포함되는 경우 액세스를 보호해야 합니다. 이 자습서에서는 [자습서: Resource Manager 템플릿을 사용하여 여러 리소스 인스턴스 만들기](./resource-manager-tutorial-create-multiple-instances.md)에서 사용한 Cloud shell 배포 메서드를 사용합니다. 기본 템플릿(azuredeploy.json)이 셸에 업로드됩니다. 연결된 템플릿(linkedTemplate.json)은 어딘가에 공유되어야 합니다.  이 자습서의 작업을 줄이기 위해 이전 섹션에서 정의된 연결된 템플릿은 [Azure 저장소 계정](https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json)에 업로드되었습니다.

## <a name="call-the-linked-template"></a>연결된 템플릿 호출

기본 템플릿은 azuredeploy.json이라고 합니다.

1. 열려 있지 않은 경우 Visual Studio Code에서 azuredeploy.json을 엽니다.
2. 템플릿에서 저장소 계정 리소스 정의를 삭제합니다.
3. 저장소 계정 정의가 있던 위치로 다음 json 코드 조각을 추가합니다.

    ```json
    {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri":"https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json"
          },
          "parameters": {
              "storageAccountName":{"value": "[variables('storageAccountName')]"},
              "location":{"value": "[parameters('location')]"}
          }
      }
    },
    ```

    다음 세부 사항에 주의하세요.

    * 기본 템플릿의 `Microsoft.Resources/deployments` 리소스는 다른 템플릿에 연결하는 데 사용됩니다.
    * `deployments` 리소스는 `linkedTemplate`이라는 이름을 갖습니다. 이 이름은 [종속성 구성](#configure-dependency)에 사용됩니다.  
    * 연결된 템플릿을 호출할 때 [증분](./deployment-modes.md) 배포 모드만 사용할 수 있습니다.
    * `templateLink/uri`는 연결된 템플릿 URI를 포함합니다. 연결된 템플릿이 공유 저장소 계정에 업로드되었습니다. 인터넷의 다른 위치에 템플릿을 업로드하는 경우 URI를 업데이트할 수 있습니다.
    * `parameters`를 사용하여 기본 템플릿의 값을 연결된 템플릿에 전달합니다.
4. 변경 내용을 저장합니다.

## <a name="configure-dependency"></a>종속성 구성

[자습서: Resource Manager 템플릿을 사용하여 여러 리소스 인스턴스 만들기](./resource-manager-tutorial-create-multiple-instances.md)에서 설명한 대로 가상 머신 리소스는 저장소 계정에 따라 달라집니다.

![Azure Resource Manager 템플릿 종속성 다이어그램](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-visual-studio-code-dependency-diagram.png)

저장소 계정은 이제 연결된 템플릿에서 정의되므로 `Microsoft.Compute/virtualMachines` 리소스의 다음 두 요소를 업데이트해야 합니다.

* `dependOn` 요소를 다시 구성합니다. 저장소 계정 정의가 연결된 템플릿으로 이동합니다.
* `properties/diagnosticsProfile/bootDiagnostics/storageUri` 요소를 다시 구성합니다. [연결된 템플릿 만들기](#create-the-linked-template)에서 출력 값을 추가했습니다.

    ```json
    "outputs": {
        "storageUri": {
            "type": "string",
            "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
    }
    ```
    기본 템플릿에서 이 값은 필수입니다.

1. 열려 있지 않은 경우 Visual Studio Code에서 azuredeploy.json을 엽니다.
2. 가상 머신 리소스 정의를 확장하고, 다음 스크린샷에 표시된 대로 **dependsOn**을 업데이트합니다.

    ![종속성을 구성하는 Azure Resource Manager 연결된 템플릿 ](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-linked-templates-configure-dependency.png)

    *linkedTemplate*은 배포 리소스의 이름입니다.  
3. 이전 스크린샷에 표시된 대로 **properties/diagnosticsProfile/bootDiagnostics/storageUri**를 업데이트합니다.

자세한 내용은 [Azure 리소스를 배포할 때 연결된 템플릿 및 중첩된 템플릿 사용](./resource-group-linked-templates.md)을 참조하세요.

## <a name="deploy-the-template"></a>템플릿 배포

배포 절차는 [템플릿 배포](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template) 섹션을 참조하세요. 보안을 강화하려면 가상 머신 관리자 계정에 생성된 암호를 사용합니다. [필수 조건](#prerequisites)을 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않은 경우 리소스 그룹을 삭제하여 배포한 리소스를 정리합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
3. 해당 리소스 그룹 이름을 선택합니다.  리소스 그룹에 총 6개의 리소스가 표시됩니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 연결된 템플릿을 개발하고 배포했습니다. 가상 머신 확장을 사용하여 배포 후 작업을 수행하는 방법은 다음 항목을 참조하세요.

> [!div class="nextstepaction"]
> [가상 머신 확장 배포](./deployment-manager-tutorial.md)
