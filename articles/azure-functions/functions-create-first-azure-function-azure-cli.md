---
title: "Azure CLI에서 첫 번째 함수 만들기 | Microsoft Docs"
description: "Azure CLI를 사용하여 서버를 사용하지 않는 실행을 위해 첫 번째 Azure Function을 만드는 방법을 알아봅니다."
services: functions
keywords: 
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 05/02/2017
ms.topic: hero-article
ms.service: functions
ms.devlang: azure-cli
manager: erikre
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 3dc0e1b26c95ac6583dd3b1068b36deb54f7ac5a
ms.contentlocale: ko-kr
ms.lasthandoff: 05/18/2017

---

# <a name="create-your-first-function-using-the-azure-cli"></a>Azure CLI를 사용하여 첫 번째 함수 만들기

빠른 시작 자습서는 Azure Functions를 사용하여 첫 번째 함수를 만드는 방법을 설명합니다. Azure CLI를 사용하여 함수를 호스트하고 서버를 사용하지 않는 인프라인 함수 앱을 만듭니다. 함수 코드 자체는 GitHub 샘플 리포지토리에서 배포됩니다.    

Mac, Windows 또는 Linux 컴퓨터를 사용하여 아래 단계를 따르면 됩니다. 

## <a name="prerequisites"></a>필수 조건 

이 샘플을 실행하기 전에 다음이 있어야 합니다.

+ 활성 [GitHub](https://github.com) 계정 
+ [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli) 
+ 활성 Azure 구독.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Azure에 로그인

[az login](/cli/azure/#login) 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다. 

```azurecli
az login
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#create)를 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 함수 앱, 데이터베이스, 저장소 계정이 관리되었는지 등 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

다음 예제에서는 `myResourceGroup`이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location westeurope
```
## <a name="create-an-azure-storage-account"></a>Azure 저장소 계정 만들기

함수는 Azure Storage 계정을 사용하여 함수에 대한 상태 및 기타 정보를 유지 관리합니다. [az storage account create](/cli/azure/storage/account#create) 명령을 사용하여 만든 리소스 그룹에 저장소 계정을 만듭니다.

다음 명령에서 `<storage_name>` 자리 표시자를 전역적으로 고유한 저장소 계정 이름으로 바꿉니다. 저장소 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 포함할 수 있습니다.

```azurecli
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```

저장소 계정을 만들었으면 Azure CLI는 다음 예와 비슷한 정보를 표시합니다.

```json
{
  "creationTime": "2017-04-15T17:14:39.320307+00:00",
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myresourcegroup/...",
  "kind": "Storage",
  "location": "westeurope",
  "name": "myfunctionappstorage",
  "primaryEndpoints": {
    "blob": "https://myfunctionappstorage.blob.core.windows.net/",
    "file": "https://myfunctionappstorage.file.core.windows.net/",
    "queue": "https://myfunctionappstorage.queue.core.windows.net/",
    "table": "https://myfunctionappstorage.table.core.windows.net/"
  },
     ....
    // Remaining output has been truncated for readability.
}
```

## <a name="create-a-function-app"></a>함수 앱 만들기

함수 실행을 호스트하는 함수 앱이 있어야 합니다. 함수 앱은 서버를 사용하지 않는 함수 코드 실행을 위한 환경을 제공합니다. 이를 통해 함수를 논리 단위로 그룹화하여 더욱 쉽게 리소스를 관리, 배포 및 공유할 수 있습니다. [az functionapp create](/cli/azure/functionapp#create) 명령을 사용하여 함수 앱을 만듭니다. 

다음 명령에서 `<app_name>` 자리 표시자 및 `<storage_name>`의 저장소 계정 이름을 고유한 함수 앱 이름으로 바꿉니다. `<app_name>`은 함수 앱의 기본 DNS 도메인으로 사용되므로 이름이 Azure의 모든 앱에서 고유해야 합니다. 

```azurecli
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup --consumption-plan-location westeurope
```
기본적으로 함수 앱은 소비 호스팅 계획에 따라 만들어지므로 함수에서 요구하는 대로 동적으로 리소스가 추가되고 함수가 실행될 때만 비용이 청구됩니다. 자세한 내용은 [올바른 호스팅 계획 선택](functions-scale.md)을 참조하세요. 

함수 앱을 만들었으면 Azure CLI는 다음 예와 비슷한 정보를 표시합니다.

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

이제 함수 앱이 있고 GitHub 샘플 리포지토리에서 실제 함수 코드를 배포할 수 있습니다.

## <a name="deploy-your-function-code"></a>함수 코드 배포  

새 함수 앱에서 사용자의 함수 코드를 만드는 여러 가지 방법이 있습니다. 이 항목은 GitHub의 샘플 리포지토리에 연결됩니다. 이전처럼, 다음 코드는 `<app_name>` 자리 표시자를 작성한 함수 앱의 이름으로 바꿉니다. 

```azurecli
az functionapp deployment source config --name <app_name> --resource-group myResourceGroup --repo-url https://github.com/Azure-Samples/functions-quickstart --branch master --manual-integration
```
배포 소스가 설정되었으면 Azure CLI는 다음 예와 비슷한 정보를 표시합니다(가독성을 위해 null 값은 제거).

```json
{
  "branch": "master",
  "deploymentRollbackEnabled": false,
  "id": "/subscriptions/bbbef702-e769-477b-9f16-bc4d3aa97387/resourceGroups/myResourceGroup/...",
  "isManualIntegration": true,
  "isMercurial": false,
  "location": "West Europe",
  "name": "quickstart",
  "repoUrl": "https://github.com/Azure-Samples/functions-quickstart",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.Web/sites/sourcecontrols"
}
```

## <a name="test-the-function"></a>함수 테스트

cURL을 사용하여 Mac 또는 Linux 컴퓨터나 Windows의 Bash를 사용하여 배포된 함수를 테스트합니다. 다음 cURL 명령을 실행하고 `<app_name>` 자리 표시자를 함수 앱의 이름으로 바꿉니다. 쿼리 문자열 `&name=<yourname>`을 URL에 추가합니다.

```bash
curl http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
```  

![브라우저에 표시된 함수 응답.](./media/functions-create-first-azure-function-azure-cli/functions-azure-cli-function-test-curl.png)  

명령줄에 사용할 수 있는 cURL이 없는 경우 웹 브라우저의 주소에 동일한 URL을 입력하면 됩니다. 다시, `<app_name>` 자리 표시자를 함수 앱의 이름으로 바꾸고 쿼리 문자열 `&name=<yourname>`을 URL에 추가하고 요청을 실행합니다. 

    http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
   
![브라우저에 표시된 함수 응답.](./media/functions-create-first-azure-function-azure-cli/functions-azure-cli-function-test-browser.png)  

## <a name="clean-up-resources"></a>리소스 정리

이 컬렉션의 다른 빠른 시작은 이 빠른 시작을 기반으로 구성됩니다. 다음 빠른 시작 또는 자습서를 사용하여 계속하려는 경우 이 빠른 시작에서 만든 리소스를 정리하지 않습니다. 계속하지 않으려는 경우 다음 명령을 사용하여 이 빠른 시작에서 만든 모든 리소스를 삭제합니다.

```azurecli
az group delete --name myResourceGroup
```
메시지가 표시되면 `y`를 입력합니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

