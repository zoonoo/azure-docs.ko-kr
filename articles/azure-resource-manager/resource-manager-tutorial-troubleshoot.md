---
title: Resource Manager 배포 문제 해결 | Microsoft Docs
description: Resource Manager 배포를 모니터링하고 문제를 해결하는 방법에 대해 알아봅니다.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/15/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 89d1573ff03771e5229e1ce28bb4ee7ecec702e3
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54332975"
---
# <a name="tutorial-troubleshoot-resource-manager-template-deployments"></a>자습서: Resource Manager 템플릿 배포 문제 해결

Resource Manager 템플릿 배포 오류 문제를 해결하는 방법에 대해 알아봅니다. 이 자습서에서는 템플릿에서 두 가지 오류를 설정하고 활동 로그 및 배포 기록을 사용하여 문제를 해결하는 방법을 알아봅니다.

템플릿 배포와 관련된 오류는 다음과 같은 두 가지 유형이 있습니다.

- **유효성 검사 오류**는 배포 전에 확인할 수 있는 시나리오에서 발생합니다. 유효성 검사 오류에는 템플릿의 구문 오류나 구독 할당량을 초과하는 리소스를 배포하려는 구문 오류가 포함됩니다. 
- **배포 오류**는 배포 프로세스 중 발생하는 조건에서 발생합니다. 배포 오류에는 병렬로 배포 중인 리소스에 액세스하려는 시도가 포함됩니다.

두 가지 오류 유형에서 배포 문제를 해결하는 데 사용하는 오류 코드를 반환합니다. 두 가지 오류 유형 모두 활동 로그에 나타납니다. 하지만 배포가 시작된 것은 아니므로 유효성 검사 오류는 배포 기록에 나타나지 않습니다.

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 문제가 있는 템플릿 만들기
> * 유효성 검사 오류 문제 해결
> * 배포 오류 문제 해결
> * 리소스 정리

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 문서를 완료하려면 다음이 필요합니다.

- [Visual Studio Code](https://code.visualstudio.com/) 및 [Resource Manager 도구 확장](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)

## <a name="create-a-problematic-template"></a>문제가 있는 템플릿 만들기

[Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/)에서 [표준 스토리지 계정 만들기](https://azure.microsoft.com/resources/templates/101-storage-account-create/)라는 템플릿을 열고 두 가지 템플릿 문제를 설정합니다.

1. Visual Studio Code에서 **파일**>**파일 열기**를 차례로 선택합니다.
2. **파일 이름**에서 다음 URL을 붙여넣습니다.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. **열기**를 선택하여 파일을 엽니다.
4. **apiVersion** 줄을 다음 줄로 변경합니다.

    ```json
    "apiVersion1": "2018-07-02",
    ```
    - **apiVersion1**은 잘못된 요소 이름입니다. 유효성 검사 오류입니다.
    - API 버전은 "2018-07-01"이어야 합니다.  배포 오류입니다.

5. **파일**>**이름으로 저장**을 차례로 선택하여 파일을 **azuredeploy.json**으로 저장합니다.

## <a name="troubleshoot-the-validation-error"></a>유효성 검사 오류 문제 해결

템플릿을 배포하려면 [템플릿 배포](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) 섹션을 참조하세요.

셸에서 다음과 유사한 오류가 발생합니다.

```
New-AzureRmResourceGroupDeployment : 4:29:24 PM - Error: Code=InvalidRequestContent; Message=The request content was invalid and could not be deserialized: 'Could not find member 'apiVersion1' on object of type 'TemplateResource'. Path 'properties.template.resources[0].apiVersion1', line 36, position 24.'.
```

오류 메시지는 **apiVersion1**에 문제가 있음을 나타냅니다.

Visual Studio Code를 사용하여 **apiVersion1**을 **apiVersion**으로 변경한 다음, 템플릿을 저장하여 문제를 해결합니다.

## <a name="troubleshoot-the-deployment-error"></a>배포 오류 문제 해결

템플릿을 배포하려면 [템플릿 배포](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) 섹션을 참조하세요.

셸에서 다음과 유사한 오류가 발생합니다.

```
New-AzureRmResourceGroupDeployment : 4:48:50 PM - Resource Microsoft.Storage/storageAccounts 'storeqii7x2rce77dc' failed with message '{
  "error": {
    "code": "NoRegisteredProviderFound",
    "message": "No registered resource provider found for location 'centralus' and API version '2018-07-02' for type 'storageAccounts'. The supported api-versions are '2018-07-01, 2018-03-01-preview, 2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01, 2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'. The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast, japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast, australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth, francecentral'."
  }
}'
```

배포 오류는 다음 절차를 수행하여 Azure Portal에서 찾을 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **리소스 그룹**을 선택한 다음, 리소스 그룹 이름을 선택하여 리소스 그룹을 엽니다. **배포** 아래에서 **1 실패**가 표시됩니다.

    ![Resource Manager 자습서 문제 해결](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-error.png)
3. **오류 세부 정보**를 선택합니다.

    ![Resource Manager 자습서 문제 해결](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-error-details.png)

    오류 메시지는 앞에서 설명한 것과 같습니다.

    ![Resource Manager 자습서 문제 해결](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-error-summary.png)

또한 활동 로그에서 오류를 찾을 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모니터** > **활동 로그**를 선택합니다.
3. 필터를 사용하여 로그를 찾습니다.

    ![Resource Manager 자습서 문제 해결](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-activity-log.png)

Visual Studio Code를 사용하여 문제를 해결한 다음, 템플릿을 다시 배포합니다.

일반적인 오류 목록은 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](./resource-manager-common-deployment-errors.md)을 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않은 경우 리소스 그룹을 삭제하여 배포한 리소스를 정리합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
3. 해당 리소스 그룹 이름을 선택합니다.  리소스 그룹에 총 6개의 리소스가 표시됩니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Resource Manager 템플릿 배포 오류 문제를 해결하는 방법에 대해 알아봅니다.  자세한 내용은 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](./resource-manager-common-deployment-errors.md)을 참조하세요.