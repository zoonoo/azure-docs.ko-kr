---
title: 빠른 시작 - Node 웹앱을 사용하여 Azure Key Vault에서 비밀 설정 및 검색 | Microsoft Docs
description: 빠른 시작에서 Node 웹앱을 사용하여 Azure Key Vault에서 비밀을 설정하고 검색합니다.
services: key-vault
author: msmbaldwin
manager: sumedhb
ms.service: key-vault
ms.topic: quickstart
ms.date: 09/05/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 5e8c29e033d895e24047754e686420fb4db86142
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236650"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-by-using-a-node-web-app"></a>빠른 시작: Node 웹앱을 사용하여 Azure Key Vault에서 비밀을 설정하고 검색 

이 빠른 시작에서는 Azure Key Vault에 비밀을 저장하는 방법 및 웹앱을 사용하여 비밀을 검색하는 방법을 보여줍니다. Key Vault를 사용하여 정보를 안전하게 보호할 수 있습니다. 비밀 값을 확인하려면 Azure에서 이 빠른 시작을 실행해야 합니다. 빠른 시작에서는 Node.js 및 Azure 리소스의 관리 ID를 사용합니다. 다음 방법에 대해 알아봅니다.

* 키 자격 증명 모음을 만듭니다.
* 키 자격 증명 모음에 비밀을 저장합니다.
* 키 자격 증명 모음에서 비밀을 검색합니다.
* Azure 웹 애플리케이션을 만듭니다.
* 웹앱의 [관리 ID](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)를 사용하도록 설정합니다.
* 웹 애플리케이션이 키 자격 증명 모음에서 데이터를 읽는 데 필요한 권한을 부여합니다.

계속 진행하기 전에 [Key Vault에 대한 기본 개념](key-vault-whatis.md#basic-concepts)을 숙지하시기 바랍니다.

> [!NOTE]
> Key Vault는 프로그래밍 방식으로 비밀을 저장하는 중앙 리포지토리입니다. 하지만 이렇게 하려면 애플리케이션과 사용자가 먼저 Key Vault에 인증해야 합니다. 즉, 비밀을 제공해야 합니다. 보안 모범 사례에 따라 이 첫 번째 비밀을 정기적으로 회전시켜야 합니다. 
>
> [Azure 리소스의 관리 서비스 ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용하면 Azure에서 실행되는 애플리케이션에 Azure에서 자동으로 관리하는 ID가 부여됩니다. 이렇게 하면 *비밀 소개 문제*를 해결할 수 있으므로 사용자와 애플리케이션에서 모범 사례를 따를 수 있고 첫 번째 비밀을 회전시켜야 하는 것에 대해 걱정할 필요가 없습니다.

## <a name="prerequisites"></a>필수 조건

* [Node.JS](https://nodejs.org/en/)
* [Git](https://www.git-scm.com/)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4 이상. 이 빠른 시작에서는 Azure CLI를 로컬에서 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. CLI를 설치하거나 업그레이드해야 하는 경우에는 [Azure CLI 2.0 설치](https://review.docs.microsoft.com/en-us/cli/azure/install-azure-cli?branch=master&view=azure-cli-latest)를 참조하세요.
* Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

Azure CLI를 사용하여 Azure에 로그인하려면 다음을 입력합니다.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#az-group-create) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

리소스 그룹 이름을 선택하고 자리 표시자를 입력합니다.
다음 예제에서는 미국 동부 위치에 리소스 그룹을 만듭니다.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

방금 만든 리소스 그룹은 이 문서 전체에서 사용됩니다.

## <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기

다음으로, 이전 단계에서 만든 리소스 그룹을 사용하여 Key Vault를 만듭니다. 이 문서에서는 "ContosoKeyVault"를 이름으로 사용하지만 고유한 이름을 사용해야 합니다. 다음 정보를 지정합니다.

* Key Vault 이름
* 리소스 그룹 이름 3-24자의 문자열이어야 하며 0-9, a-z, A-Z 및 하이픈(-)만 포함해야 합니다.
* 위치: **미국 동부**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

이 시점에서 Azure 계정은 이 새 자격 증명 모음에서 모든 작업을 수행할 권한이 있는 유일한 계정입니다.

## <a name="add-a-secret-to-the-key-vault"></a>키 자격 증명 모음에 비밀 추가

이 작업을 설명하기 위한 비밀을 추가하고 있습니다. 안전하게 유지하면서 애플리케이션에서 사용할 수 있도록 하는 데 필요한 SQL 연결 문자열 또는 기타 정보를 저장할 수 있습니다. 이 자습서에서 암호는 **AppSecret**이고 그 안에 **MySecret** 값이 저장됩니다.

다음 명령을 입력하여 키 자격 증명 모음에 **AppSecret**라고 하는 비밀을 만듭니다. 이 비밀에는 **MySecret** 값이 저장됩니다.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

비밀에 들어 있는 값을 일반 텍스트로 보려면:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

이 명령은 URI를 포함한 비밀 정보를 표시합니다. 이러한 단계가 완료되면 비밀에 대한 URI가 키 자격 증명 모음에 있어야 합니다. 이 정보를 기록해 둡니다. 이후 단계에서 필요합니다.

## <a name="clone-the-repo"></a>리포지토리 복제

리포지토리를 복제하여 원본을 편집할 수 있는 로컬 복사본을 만듭니다. 다음 명령 실행:

```
git clone https://github.com/Azure-Samples/key-vault-node-quickstart.git
```

## <a name="install-dependencies"></a>종속성 설치

다음 명령을 실행하여 종속성을 설치합니다.

```
cd key-vault-node-quickstart
npm install
```

이 프로젝트는 두 개의 Node 모듈 [ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure) 및 [azure-keyvault](https://www.npmjs.com/package/azure-keyvault)를 사용합니다.

## <a name="publish-the-web-app-to-azure"></a>Azure에 웹앱 게시

[Azure App Service](https://azure.microsoft.com/services/app-service/) 계획을 만듭니다. 이 계획에 여러 웹앱을 저장할 수 있습니다.

    ```
    az appservice plan create --name myAppServicePlan --resource-group myResourceGroup
    ```
다음으로, 웹앱을 만듭니다. 다음 예제에서 `<app_name>`을 전역적으로 고유한 앱 이름으로 바꿉니다(유효한 문자는 a-z, 0-9 및 -). 런타임은 NODE|6.9로 설정됩니다. 지원되는 모든 런타임을 보려면 `az webapp list-runtimes`를 실행합니다.

    ```
    # Bash
    az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "NODE|6.9" --deployment-local-git
    ```
웹앱이 만들어지면 Azure CLI에서 다음 예제와 비슷한 출력을 표시합니다.

    ```
    {
      "availabilityState": "Normal",
      "clientAffinityEnabled": true,
      "clientCertEnabled": false,
      "cloningInfo": null,
      "containerSize": 0,
      "dailyMemoryTimeQuota": 0,
      "defaultHostName": "<app_name>.azurewebsites.net",
      "enabled": true,
      "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
      < JSON data removed for brevity. >
    }
    ```
새로 만든 웹앱으로 이동하면 웹앱이 작동하는 것을 볼 수 있습니다. `<app_name>`을 고유한 앱 이름으로 바꿉니다.

    ```
    http://<app name>.azurewebsites.net
    ```
또한 이전 명령은 로컬 Git 리포지토리에서 Azure에 배포할 수 있는 Git 지원 앱을 만듭니다. 로컬 Git 리포지토리는 `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git` URL로 구성됩니다.

이전 명령을 완료한 후에는 로컬 Git 리포지토리에 Azure 원격을 추가할 수 있습니다. `<url>`을 Git 리포지토리의 URL로 바꿉니다.

    ```
    git remote add azure <url>
    ```

## <a name="enable-a-managed-identity-for-the-web-app"></a>웹앱의 관리 ID를 사용하도록 설정

Azure Key Vault를 사용하면 자격 증명과 기타 키 및 비밀을 안전하게 저장할 수 있습니다. 하지만 이러한 자격 증명/키/비밀을 검색하려면 코드가 Key Vault에 인증해야 합니다. [Azure 리소스에 대한 관리 ID 개요](../active-directory/managed-identities-azure-resources/overview.md)를 통해 Azure AD(Azure Active Directory)에서 자동으로 관리되는 ID를 Azure 서비스에 제공함으로써 이 문제를 보다 간편하게 해결할 수 있습니다. 이 ID를 사용하면 Key Vault를 비롯하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다.

이 애플리케이션에 대한 ID를 만들려면 assign-identity 명령을 실행합니다.

```azurecli
az webapp identity assign --name <app_name> --resource-group "<YourResourceGroupName>"
```

이 명령은 포털로 이동하여 웹 애플리케이션 속성에서 **ID/시스템 할당됨** 설정을 **켜기**로 전환하는 것과 동일합니다.

### <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>애플리케이션에 Key Vault에서 비밀을 읽을 수 있는 권한 할당

이전 명령의 결과를 기록해 둡니다. 다음과 같은 형식이어야 합니다.
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
그런 다음, Key Vault의 이름 및 **principalId** 값을 사용하여 다음 명령을 실행합니다.

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get set
```

## <a name="deploy-the-node-app-to-azure-and-retrieve-the-secret-value"></a>Azure에 Node 앱을 배포하고 비밀 값 검색

다음 명령을 실행하여 Azure에 앱을 배포합니다.

```
git push azure master
```

이 후에 `https://<app_name>.azurewebsites.net`으로 이동하면 비밀값을 볼 수 있습니다. `<YourKeyVaultName>` 이름을 해당 자격 증명 모음 이름으로 바꿔야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Node용 Azure SDK](https://docs.microsoft.com/javascript/api/overview/azure/key-vault)
