---
title: 빠른 시작 - .NET용 Azure Key Vault 클라이언트 라이브러리(v4)
description: .NET 클라이언트 라이브러리(v4)를 사용하여 Azure Key Vault에서 암호를 생성, 검색 및 삭제하는 방법을 알아봅니다.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 05/20/2019
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: c789d48656173721432779aeaba0530950527fa1
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646918"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v4"></a>빠른 시작: .NET용 Azure Key Vault 클라이언트 라이브러리(SDK v4)

.NET용 Azure Key Vault 클라이언트 라이브러리를 시작합니다. 아래 단계에 따라 패키지를 설치하고 기본 작업에 대한 예제 코드를 사용해 봅니다.

Azure Key Vault는 클라우드 애플리케이션 및 서비스에서 사용되는 암호화 키 및 비밀을 보호하는데 도움이 됩니다. .NET 용 Key Vault 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

- 키 및 암호에 대한 보안과 제어를 강화합니다.
- 몇 분 안에 암호화 키를 만들고 가져옵니다.
- 클라우드 규모와 글로벌 중복성을 통해 대기 시간을 줄입니다.
- SSL/TLS 인증서 작업을 간소화하고 자동화합니다.
- FIPS 140-2 수준 2 유효성이 검사된 HSM을 사용합니다.

[API 참조 설명서](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [패키지(NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

## <a name="prerequisites"></a>필수 조건

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [.NET Core 2.1 SDK 이상](https://dotnet.microsoft.com/download/dotnet-core/2.1)
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 또는 [Azure PowerShell](/powershell/azure/overview)

이 빠른 시작에서는 Windows 터미널(예: [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6) 또는 [Azure Cloud Shell](https://shell.azure.com/))에서 `dotnet`, [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 및 Windows 명령을 실행한다고 가정합니다.

## <a name="setting-up"></a>설치

### <a name="create-new-net-console-app"></a>새 .NET 콘솔 앱 만들기

선호하는 편집기 또는 IDE에서 .NET Core 애플리케이션을 새로 만듭니다.

콘솔 창에서 `dotnet new` 명령을 사용하여 `akv-dotnet`이라는 새 콘솔 앱을 만듭니다.


```console
dotnet new console -n key-vault-console-app
```

새로 만든 앱 폴더로 디렉터리를 변경합니다. 다음을 통해 애플리케이션을 빌드할 수 있습니다.

```console
dotnet build
```

빌드 출력에 경고나 오류가 포함되지 않아야 합니다.

```console
Build succeeded.
 0 Warning(s)
 0 Error(s)
```

### <a name="install-the-package"></a>패키지 설치

콘솔 창에서 .NET용 Azure Key Vault 클라이언트 라이브러리를 설치합니다.

```console
dotnet add package Azure.Security.KeyVault.Secrets --version 4.0.0
```

이 빠른 시작에서는 다음 패키지도 설치해야 합니다.

```console
dotnet add package Azure.Identity --version 1.0.0
```

### <a name="create-a-resource-group-and-key-vault"></a>리소스 그룹 및 키 자격 증명 모음 만들기

이 빠른 시작에서는 미리 만든 Azure Key Vault를 사용합니다. [Azure CLI 빠른 시작](quick-create-cli.md), [Azure PowerShell 빠른 시작](quick-create-powershell.md) 또는 [Azure Portal 빠른 시작](quick-create-portal.md)의 단계에 따라 키 자격 증명 모음을 만들 수 있습니다. 또는 아래의 Azure CLI 명령을 실행하기만 하면 됩니다.

> [!Important]
> 각 Key Vault마다 고유한 이름이 있어야 합니다. 다음 예제에서는 <your-unique-keyvault-name>을 Key Vault의 이름으로 바꿉니다.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>서비스 주체 만들기

클라우드 기반 .NET 애플리케이션을 인증하는 가장 간단한 방법은 관리 ID를 사용하는 것입니다. 자세한 내용은 [App Service 관리 ID를 사용하여 Azure Key Vault에 액세스](managed-identity.md)를 참조하세요. 그러나 간단히 하기 위해 이 빠른 시작에서는 .NET 콘솔 애플리케이션을 만듭니다. Azure를 사용하여 데스크톱 애플리케이션을 인증하려면 서비스 주체 및 액세스 제어 정책을 사용해야 합니다.

서비스 주체는 [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) Azure CLI 명령을 사용하여 만듭니다.

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

이 작업을 수행하면 일련의 키/값 쌍이 반환됩니다. 

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

다음 단계에서 사용할 예정이므로 clientId, clientSecret 및 tenantId를 기록해 둡니다.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>키 자격 증명 모음에 대한 액세스 권한을 서비스 주체에 부여

[az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) 명령에 clientId를 전달하여 서비스 사용자에게 권한을 부여하는 키 자격 증명 모음에 대한 액세스 정책을 만듭니다. 서비스 사용자에게 키와 비밀에 대한 get, list 및 set 권한을 부여합니다.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

#### <a name="set-environmental-variables"></a>환경 변수 설정

애플리케이션의 DefaultAzureCredential 메서드는 `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET`및 `AZURE_TENANT_ID`의 세 가지 환경 변수를 사용합니다. 이러한 변수를 위의 [서비스 주체 만들기](#create-a-service-principal) 단계에서 적어 둔 clientId, clientSecret 및 tenantId 값으로 설정합니다.

또한 Key Vault 이름을 `KEY_VAULT_NAME`라는 환경 변수로 저장해야 합니다.

```console
setx AZURE_CLIENT_ID <your-clientID>

setx AZURE_CLIENT_SECRET <your-clientSecret>

setx AZURE_TENANT_ID <your-tenantId>

setx KEY_VAULT_NAME <your-key-vault-name>
````

`setx`를 호출할 때마다 "SUCCESS: 지정된 값이 저장되었습니다."라는 응답이 표시됩니다.

## <a name="object-model"></a>개체 모델

.NET용 Azure Key Vault 클라이언트 라이브러리를 사용하면 키 및 관련 자산(예: 인증서 및 비밀)을 관리할 수 있습니다. 아래의 코드 샘플에서는 클라이언트를 만들고, 비밀을 설정, 검색 및 삭제하는 방법을 보여 줍니다.

전체 콘솔 앱은 https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app 에서 사용할 수 있습니다.

## <a name="code-examples"></a>코드 예제

### <a name="add-directives"></a>지시문 추가

다음 지시문을 코드 위쪽에 추가합니다.

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>클라이언트 인증 및 만들기

Key Vault 인증을 받고 Key Vault 클라이언트를 생성하는 작업은 위에 나오는 [환경 변수 설정](#set-environmental-variables) 단계의 환경 변수에 따라 달라집니다. Key Vault의 이름은 "https://<your-key-vault-name>.vault.azure.net" 형식의 Key Vault URI로 확장됩니다.

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>비밀 저장

이제 애플리케이션이 인증되었으므로 [client.SetSecret 메서드](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync)를 사용하여 Key Vault에 비밀을 넣을 수 있습니다. 이 작업을 위해 비밀의 이름이 필요하며, 이 샘플에서는 "mySecret"을 사용하고 있습니다.  

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=setsecret)]

[az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) 명령을 사용하여 비밀이 설정되었는지 확인할 수 있습니다.

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>비밀 검색

이제 [client.GetSecret 메서드](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)를 사용하여 이전에 설정한 값을 검색할 수 있습니다.

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=getsecret)]

이제 비밀이 `secret.Value`로 저장됩니다.

### <a name="delete-a-secret"></a>비밀 삭제

마지막으로 [client.DeleteSecret 메서드](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)를 사용하여 Key Vault에서 비밀을 삭제해 보겠습니다.

[!code-csharp[Delete secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=deletesecret)]

[az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) 명령을 사용하여 비밀이 삭제되었는지 확인할 수 있습니다.

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 Azure CLI 또는 Azure PowerShell을 사용하여 키 자격 증명 모음 및 해당 리소스 그룹을 제거할 수 있습니다.

```azurecli
az group delete -g "myResourceGroup" -l "EastUS" 
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>샘플 코드

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;

namespace key_vault_console_app
{
    class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";

            string keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
            var kvUri = "https://" + keyVaultName + ".vault.azure.net";

            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());

            Console.Write("Input the value of your secret > ");
            string secretValue = Console.ReadLine();

            Console.Write("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");

            client.SetSecret(secretName, secretValue);

            Console.WriteLine(" done.");

            Console.WriteLine("Forgetting your secret.");
            secretValue = "";
            Console.WriteLine("Your secret is '" + secretValue + "'.");

            Console.WriteLine("Retrieving your secret from " + keyVaultName + ".");

            KeyVaultSecret secret = client.GetSecret(secretName);

            Console.WriteLine("Your secret is '" + secret.Value + "'.");

            Console.Write("Deleting your secret from " + keyVaultName + " ...");

            client.StartDeleteSecret(secretName);

            System.Threading.Thread.Sleep(5000);
            Console.WriteLine(" done.");

        }
    }
}
```


## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 키 자격 증명 모음을 만들고, 비밀을 저장하고, 해당 비밀을 검색했습니다. [GitHub의 전체 콘솔 앱](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app)을 참조하세요.

Key Vault 및 이를 애플리케이션과 통합하는 방법에 대해 자세히 알아보려면 아래 문서로 계속 진행하세요.

- [.NET을 사용하여 Azure Key Vault에 서비스 간 인증](service-to-service-authentication.md) 구현
- [Azure Key Vault 개요](key-vault-overview.md) 참조
- [Azure Key Vault 개발자 가이드](key-vault-developers-guide.md) 참조
- [키, 비밀 및 인증서](about-keys-secrets-and-certificates.md) 알아보기
- [Azure Key Vault 모범 사례](key-vault-best-practices.md) 검토
