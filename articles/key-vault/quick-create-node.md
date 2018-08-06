---
title: Key Vault에서 비밀을 읽도록 Azure 웹 응용 프로그램 구성 자습서 | Microsoft Docs
description: '자습서: Key Vault에서 비밀을 읽도록 Node.js 응용 프로그램 구성'
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: sumedhb
ms.service: key-vault
ms.workload: identity
ms.topic: quickstart
ms.date: 08/01/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: cc43081463667eba06af6538f3d78f16544ed2a5
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412245"
---
# <a name="quickstart-how-to-set-and-read-a-secret-from-key-vault-in-a-node-web-app"></a>빠른 시작: 노드 웹앱의 Key Vault에서 비밀을 설정하고 읽는 방법 

이 빠른 시작에서는 Key Vault에 비밀을 저장하는 방법 및 웹앱을 사용하여 비밀을 검색하는 방법을 보여줍니다. 이 웹앱은 로컬로 또는 Azure에서 실행할 수 있습니다. 이 빠른 시작에서는 Node.js 및 MSI(관리 서비스 ID)를 사용합니다.

> [!div class="checklist"]
> * Key Vault를 만듭니다.
> * Key Vault에 비밀을 저장합니다.
> * Key Vault에서 비밀을 검색합니다.
> * Azure 웹 응용 프로그램을 만듭니다.
> * [관리되는 서비스 ID를 사용하도록 설정합니다](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview).
> * 웹 응용 프로그램이 Key Vault에서 데이터를 읽기 위해 필요한 권한을 부여합니다.

계속 진행하기 전에 [기본 개념](key-vault-whatis.md#basic-concepts)을 숙지하시기 바랍니다.

## <a name="prerequisites"></a>필수 조건

* [Node JS](https://nodejs.org/en/)
* [Git](https://www.git-scm.com/)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4 이상
* Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="login-to-azure"></a>Azure에 로그인

CLI를 사용하여 Azure에 로그인하려면 다음을 입력합니다.

```azurecli
az login
```

## <a name="create-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#az_group_create) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

리소스 그룹 이름을 선택하고 자리 표시자를 입력하세요.
다음 예에서는 *eastus* 위치에 *<YourResourceGroupName>* 이라는 리소스 그룹을 만듭니다.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

방금 만든 리소스 그룹은 이 자습서 전체에서 사용됩니다.

## <a name="create-an-azure-key-vault"></a>Azure Key Vault 만들기

다음으로 이전 단계에서 만든 리소스 그룹을 사용하여 Key Vault를 만듭니다. 이 문서에서는 Key Vault의 이름으로 "ContosoKeyVault"를 사용하고 있지만, 여러분은 각자 고유한 이름을 사용해야 합니다. 다음 정보를 지정합니다.

* 자격 증명 모음 이름 - **여기서 Key Vault 이름을 선택하세요**.
* 리소스 그룹 이름 - **여기서 리소스 그룹 이름을 선택하세요**.
* 위치 - **미국 동부**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

이때 사용자의 Azure 계정은 이 새 자격 증명 모음에서 모든 작업을 수행할 권한이 있는 유일한 계정입니다.

## <a name="add-a-secret-to-key-vault"></a>키 자격 증명 모음에 비밀 추가

이 작업을 설명하기 위한 비밀을 추가하고 있습니다. 안전하게 보관해야 하면서도 응용 프로그램에 제공해야 하는 SQL 연결 문자열 또는 기타 정보를 저장할 수 있습니다. 이 자습서에서 암호는 **AppSecret**이고 그 안에 **MySecret** 값이 저장됩니다.

아래 명령을 입력하여 값 **MySecret**을 저장하는 **AppSecret**이라는 비밀을 Key Vault에 만듭니다.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

비밀에 들어 있는 값을 일반 텍스트로 보려면:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

이 명령은 URI를 포함한 비밀 정보를 표시합니다. 이러한 단계를 완료하면 Azure Key Vault의 비밀에 대한 URI이 생깁니다. 이 정보를 적어 둡니다. 나중에 필요합니다.

## <a name="clone-the-repo"></a>리포지토리 복제

소스를 편집할 로컬 복사본을 만들려면 다음 명령을 실행하여 리포지토리를 복제합니다.

```
git clone https://github.com/Azure-Samples/key-vault-node-quickstart.git
```

## <a name="install-dependencies"></a>종속성 설치

여기서 종속성을 설치합니다. cd key-vault-node-quickstart npm install 명령을 실행합니다.

이 프로젝트에서는 2노드 모듈을 사용합니다.

* [ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure) 
* [azure-keyvault](https://www.npmjs.com/package/azure-keyvault)

## <a name="publish-the-web-application-to-azure"></a>Azure에 웹 응용 프로그램 게시

아래는 우리가 해야 할 몇 가지 단계입니다.

- 첫 번째 단계는 [Azure App Service](https://azure.microsoft.com/services/app-service/) 계획을 만드는 것입니다. 이 계획에 여러 웹앱을 저장할 수 있습니다.

    ```
    az appservice plan create --name myAppServicePlan --resource-group myResourceGroup
    ```
- 다음으로 웹앱을 만듭니다. 다음 예제에서 <app_name>을 글로벌하게 고유한 앱 이름으로 바꿉니다(유효한 문자는 a-z, 0-9 및 -). 런타임은 NODE|6.9로 설정됩니다. 지원되는 모든 런타임을 보려면 az webapp list-runtimes 명령을 실행합니다.
    ```
    # Bash
    az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "NODE|6.9" --deployment-local-git
    # PowerShell
    az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "NODE|6.9"
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
    새로 만든 웹앱으로 이동하면 웹앱이 작동하는 것을 볼 수 있습니다. <app_name>을 고유한 앱 이름으로 바꿉니다.

    ```
    http://<app name>.azurewebsites.net
    ```
    또한 위의 명령은 로컬 git에서 azure에 배포할 수 있는 Git 지원 앱을 만듭니다. 
    로컬 git는 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git' url을 사용하여 구성됩니다.

- 이전 명령이 완료된 후 배포 사용자를 만들면 로컬 Git 리포지토리에 Azure 원격을 추가할 수 있습니다. <url>을 앱에 대해 Git 사용에서 가져온 Git 원격의 URL로 바꿉니다.

    ```
    git remote add azure <url>
    ```

## <a name="enable-managed-service-identity"></a>관리되는 서비스 ID 사용

Azure Key Vault를 사용하면 자격 증명과 기타 키 및 비밀을 안전하게 저장할 수 있습니다. 하지만 이러한 자격 증명/키/비밀을 검색하려면 코드가 Key Vault에 인증해야 합니다. MSI(관리 서비스 ID)를 사용하면 Azure AD(Azure Active Directory)에서 자동으로 관리되는 ID를 Azure 서비스에 제공함으로써 이 문제를 보다 간편하게 해결할 수 있습니다. 이 ID를 사용하면 Key Vault를 비롯하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다.

이 응용 프로그램에 대한 ID를 만들려면 assign-identity 명령을 실행합니다.

```azurecli
az webapp identity assign --name <app_name> --resource-group "<YourResourceGroupName>"
```

이 명령은 포털로 이동하여 웹 응용 프로그램 속성에서 **관리되는 서비스 ID**를 **켜기**로 전환하는 것과 동일합니다.

### <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>응용 프로그램에 Key Vault에서 비밀을 읽을 수 있는 권한 할당

위 명령의 출력을 적거나 복사해 둡니다. 다음과 같은 형식이어야 합니다.
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
그런 다음, Key Vault의 이름 및 위에서 복사한 PrincipalId 값을 사용하여 이 명령을 실행합니다.

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get
```

## <a name="deploy-the-node-app-to-azure-and-retrieve-the-secret-value"></a>Azure에 노드 앱을 배포하고 비밀 값 검색

이제 모든 것이 설정되었습니다. 다음 명령을 실행하여 Azure에 앱을 배포합니다.

```
git push azure master
```

그리고 https://<app_name>.azurewebsites.net을 검색하면 비밀 값을 볼 수 있습니다.
<YourKeyVaultName> 이름을 해당 자격 증명 모음 이름으로 바꿔야 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Key Vault 홈 페이지](https://azure.microsoft.com/services/key-vault/)
* [Azure Key Vault 설명서](https://docs.microsoft.com/azure/key-vault/)
* [Node용 Azure SDK](https://docs.microsoft.com/javascript/api/overview/azure/key-vault)
* [Azure REST API 참조](https://docs.microsoft.com/rest/api/keyvault/)