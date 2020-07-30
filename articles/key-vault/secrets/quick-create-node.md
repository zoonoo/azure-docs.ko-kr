---
title: 빠른 시작 - Node.js용 Azure Key Vault 클라이언트 라이브러리(v4)
description: Node.js 클라이언트 라이브러리를 사용하여 Azure Key Vault에서 비밀을 생성, 검색 및 삭제하는 방법을 알아봅니다.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 1157fb69704af59a75989b22338b88a8576428ce
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076658"
---
# <a name="quickstart-azure-key-vault-client-library-for-nodejs-v4"></a>빠른 시작: Node.js용 Azure Key Vault 클라이언트 라이브러리(v4)

Node.js용 Azure Key Vault 클라이언트 라이브러리를 시작합니다. 아래 단계에 따라 패키지를 설치하고 기본 작업에 대한 예제 코드를 사용해 봅니다.

Azure Key Vault는 클라우드 애플리케이션 및 서비스에서 사용되는 암호화 키 및 비밀을 보호하는데 도움이 됩니다. Node.js용 Key Vault 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

- 키 및 암호에 대한 보안과 제어를 강화합니다.
- 몇 분 안에 암호화 키를 만들고 가져옵니다.
- 클라우드 규모와 글로벌 중복성을 통해 대기 시간을 줄입니다.
- TLS/SSL 인증서 작업을 간소화하고 자동화합니다.
- FIPS 140-2 수준 2 유효성이 검사된 HSM을 사용합니다.

[API 참조 설명서](https://docs.microsoft.com/javascript/api/overview/azure/key-vault-index?view=azure-node-latest) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault) | [패키지(npm)](https://www.npmjs.com/package/@azure/keyvault-secrets)

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- 현재 운영 체제의 [Node.js](https://nodejs.org)입니다.
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 또는 [Azure PowerShell](/powershell/azure/)

이 빠른 시작에서는 Linux 터미널 창에서 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)를 실행하고 있다고 가정합니다.

## <a name="setting-up"></a>설치

### <a name="install-the-package"></a>패키지 설치

콘솔 창에서 Node.js용 Azure Key Vault 비밀 라이브러리를 설치합니다.

```console
npm install @azure/keyvault-secrets
```

이 빠른 시작에서는 azure.identity 패키지도 설치해야 합니다.

```console
npm install @azure/identity
```

### <a name="create-a-resource-group-and-key-vault"></a>리소스 그룹 및 키 자격 증명 모음 만들기

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

### <a name="create-a-service-principal"></a>서비스 주체 만들기

[!INCLUDE [Create a service principal](../../../includes/key-vault-sp-creation.md)]

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>키 자격 증명 모음에 대한 액세스 권한을 서비스 주체에 부여

[!INCLUDE [Give the service principal access to your key vault](../../../includes/key-vault-sp-kv-access.md)]

#### <a name="set-environmental-variables"></a>환경 변수 설정

[!INCLUDE [Set environmental variables](../../../includes/key-vault-set-environmental-variables.md)]

## <a name="object-model"></a>개체 모델

Node.js용 Azure Key Vault 클라이언트 라이브러리를 사용하면 키 및 관련 자산(예: 인증서 및 비밀)을 관리할 수 있습니다. 아래의 코드 샘플에서는 클라이언트를 만들고, 비밀을 설정, 검색 및 삭제하는 방법을 보여 줍니다.

전체 콘솔 앱은 https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app 에서 사용할 수 있습니다.

## <a name="code-examples"></a>코드 예제

### <a name="add-directives"></a>지시문 추가

다음 지시문을 코드 위쪽에 추가합니다.

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");
```

### <a name="authenticate-and-create-a-client"></a>클라이언트 인증 및 만들기

키 자격 증명 모음을 인증하고 키 자격 증명 모음 클라이언트를 만드는 작업은 위 단계의 [환경 변수 설정](#set-environmental-variables) 및 [SecretClient 생성자](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#secretclient-string--tokencredential--pipelineoptions-)에 따라 달라집니다. 

키 자격 증명 모음 이름은 `https://<your-key-vault-name>.vault.azure.net` 형식의 Key Vault URI로 확장됩니다. 

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new SecretClient(KVUri, credential);
```

### <a name="save-a-secret"></a>비밀 저장

이제 애플리케이션이 인증되었으므로 [client.setSecret 메서드](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#setsecret-string--string--setsecretoptions-)를 사용하여 키 자격 증명 모음에 비밀을 넣을 수 있습니다. 이 작업을 위해 비밀 이름이 필요하며, 이 샘플에서는 "mySecret"을 사용합니다.  

```javascript
await client.setSecret(secretName, secretValue);
```

[az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) 명령을 사용하여 비밀이 설정되었는지 확인할 수 있습니다.

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>비밀 검색

이제 [client.getSecret 메서드](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#getsecret-string--getsecretoptions-)를 사용하여 앞에서 설정한 값을 검색할 수 있습니다.

```javascript
const retrievedSecret = await client.getSecret(secretName);
 ```

이제 비밀이 `retrievedSecret.value`로 저장됩니다.

### <a name="delete-a-secret"></a>비밀 삭제

마지막으로, [client.beginDeleteSecret 메서드](/javascript/api/@azure/keyvault-secrets/secretclient?view=azure-node-latest#begindeletesecret-string--begindeletesecretoptions-)를 사용하여 키 자격 증명 모음에서 비밀을 삭제해 보겠습니다.

```javascript
await client.beginDeleteSecret(secretName)
```

[az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) 명령을 사용하여 비밀이 삭제되었는지 확인할 수 있습니다.

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 Azure CLI 또는 Azure PowerShell을 사용하여 키 자격 증명 모음 및 해당 리소스 그룹을 제거할 수 있습니다.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>예제 코드

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { SecretClient } = require("@azure/keyvault-secrets");

const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {

  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new SecretClient(KVUri, credential);

  const secretName = "mySecret";
  var secretValue = await askQuestion("Input the value of your secret > ");

  console.log("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");
  await client.setSecret(secretName, secretValue);

  console.log("Done.");

  console.log("Forgetting your secret.");
  secretValue = "";
  console.log("Your secret is '" + secretValue + "'.");

  console.log("Retrieving your secret from " + keyVaultName + ".");

  const retrievedSecret = await client.getSecret(secretName);

  console.log("Your secret is '" + retrievedSecret.value + "'.");
  console.log("Deleting your secret from " + keyVaultName + " ...");

  await client.beginDeleteSecret(secretName);

  console.log("Done.");

}

main()

```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 키 자격 증명 모음을 만들고, 비밀을 저장하고, 해당 비밀을 검색했습니다. Key Vault 및 이를 애플리케이션과 통합하는 방법에 대해 자세히 알아보려면 아래 문서로 계속 진행하세요.

- [Azure Key Vault 개요](../general/overview.md) 참조
- [Azure Key Vault 개발자 가이드](../general/developers-guide.md) 참조
- [Azure Key Vault 모범 사례](../general/best-practices.md) 검토