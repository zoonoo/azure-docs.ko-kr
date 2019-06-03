---
title: '빠른 시작: .NET 웹앱을 사용하여 Azure Key Vault에서 비밀 설정 및 검색 - Azure Key Vault | Microsoft Docs'
description: 빠른 시작에서 .NET 웹앱을 사용하여 Azure Key Vault에서 비밀을 설정하고 검색합니다.
services: key-vault
author: msmbaldwin
manager: sumedhb
ms.service: key-vault
ms.topic: quickstart
ms.date: 01/02/2019
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 132711249ffde4a9c49bc997d8c4ebe4d9c74948
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65872533"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-by-using-a-net-web-app"></a>빠른 시작: .NET 웹앱을 사용하여 Azure Key Vault에서 비밀 설정 및 검색

이 빠른 시작에서는 Azure 리소스의 관리 ID를 사용하여 Azure Key Vault에서 정보를 읽는 Azure 웹 애플리케이션을 가져오는 단계를 수행합니다. Key Vault를 사용하여 정보를 안전하게 보호할 수 있습니다. 다음 방법에 대해 알아봅니다.

* 키 자격 증명 모음을 만듭니다.
* 키 자격 증명 모음에 비밀을 저장합니다.
* 키 자격 증명 모음에서 비밀을 검색합니다.
* Azure 웹 애플리케이션을 만듭니다.
* 웹앱의 [관리 서비스 ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용하도록 설정합니다.
* 웹 애플리케이션이 키 자격 증명 모음에서 데이터를 읽는 데 필요한 권한을 부여합니다.

본론으로 들어가기 전에 [Key Vault의 기본 개념](key-vault-whatis.md#basic-concepts)을 읽어보세요.

>[!NOTE]
>Key Vault는 프로그래밍 방식으로 비밀을 저장하는 중앙 리포지토리입니다. 하지만 이렇게 하려면 애플리케이션과 사용자가 먼저 Key Vault에 인증해야 합니다. 즉, 비밀을 제공해야 합니다. 보안 모범 사례에 따라 이 첫 번째 비밀을 정기적으로 회전시켜야 합니다. 
>
>[Azure 리소스의 관리 서비스 ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용하면 Azure에서 실행되는 애플리케이션에 Azure에서 자동으로 관리하는 ID가 부여됩니다. 이렇게 하면 *비밀 소개 문제*를 해결할 수 있으므로 사용자와 애플리케이션에서 모범 사례를 따를 수 있고 첫 번째 비밀을 회전시켜야 하는 것에 대해 걱정할 필요가 없습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>필수 조건

* Windows에서:
  * 다음 워크로드가 포함된 [Visual Studio 2019](https://www.microsoft.com/net/download/windows):
    * ASP.NET 및 웹 개발
    * .NET Core 플랫폼 간 개발
  * [.NET Core 2.1 SDK 이상](https://www.microsoft.com/net/download/windows)

* Mac에서:
  * [Mac용 Visual Studio의 새로운 기능](https://visualstudio.microsoft.com/vs/mac/)을 참조하세요.

* 모든 플랫폼:
  * Git([다운로드](https://git-scm.com/downloads)).
  * Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 버전 2.0.4 이상. Windows, Mac 및 Linux에서 사용할 수 있습니다.

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

다음으로, 이전 단계에서 만든 리소스 그룹에 키 자격 증명 모음을 만듭니다. 다음 정보를 지정합니다.

* 키 자격 증명 모음 이름: 3-24자의 문자열이어야 하며 0-9, a-z, A-Z 및 하이픈(-)만 포함해야 합니다.
* 리소스 그룹 이름
* 위치: **미국 동부**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

이 시점에서 Azure 계정은 이 새 자격 증명 모음에서 모든 작업을 수행할 권한이 있는 유일한 계정입니다.

## <a name="add-a-secret-to-the-key-vault"></a>키 자격 증명 모음에 비밀 추가

이 작업을 설명하기 위한 비밀을 추가하고 있습니다. 안전하게 유지하면서 응용 프로그램에서 사용할 수 있도록 하는 데 필요한 SQL 연결 문자열 또는 기타 정보를 저장할 수 있습니다.

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
git clone https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart.git
```

## <a name="open-and-edit-the-solution"></a>솔루션 열기 및 편집

특정 키 자격 증명 모음 이름으로 샘플을 실행하도록 program.cs 파일을 편집합니다.

1. key-vault-dotnet-core-quickstart 폴더로 이동합니다.
2. Visual Studio 2019에서 key-vault-dotnet-core-quickstart.sln 파일을 엽니다.
3. Program.cs 파일을 열고, *YourKeyVaultName* 자리 표시자를 앞에서 만든 키 자격 증명 모음의 이름으로 업데이트합니다.

이 솔루션에서는 [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) 및 [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet 라이브러리를 사용합니다.

## <a name="run-the-app"></a>앱 실행

Visual Studio 2019의 주 메뉴에서 **디버그** > **디버깅하지 않고 시작**을 차례로 선택합니다. 브라우저가 표시되면 **정보** 페이지로 이동합니다. **AppSecret**에 대한 값이 표시됩니다.

## <a name="publish-the-web-application-to-azure"></a>Azure에 웹 애플리케이션 게시

이 앱을 Azure에 게시하여 웹앱으로 라이브로 표시하고 비밀 값을 가져올 수 있는지 확인합니다.

1. Visual Studio에서 **key-vault-dotnet-core-quickstart** 프로젝트를 선택합니다.
2. **게시** > **시작**을 차례로 선택합니다.
3. 새 **App Service**를 만든 다음, **게시**를 선택합니다.
4. 앱 이름을 **keyvaultdotnetcorequickstart**로 변경합니다.
5. **만들기**를 선택합니다.

>[!VIDEO https://sec.ch9.ms/ch9/e93d/a6ac417f-2e63-4125-a37a-8f34bf0fe93d/KeyVault_high.mp4]

## <a name="enable-a-managed-identity-for-the-web-app"></a>웹앱의 관리 ID를 사용하도록 설정

Azure Key Vault를 사용하면 자격 증명과 기타 키 및 비밀을 안전하게 저장할 수 있습니다. 하지만 이러한 자격 증명/키/비밀을 검색하려면 코드가 Key Vault에 인증해야 합니다. [Azure 리소스에 대한 관리 ID 개요](../active-directory/managed-identities-azure-resources/overview.md)를 통해 Azure AD(Azure Active Directory)에서 자동으로 관리되는 ID를 Azure 서비스에 제공함으로써 이 문제를 보다 간편하게 해결할 수 있습니다. 이 ID를 사용하면 Key Vault를 비롯하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다.

Azure CLI에서 이 애플리케이션에 대한 ID를 만들려면 assign-identity 명령을 실행합니다.

   ```azurecli
   az webapp identity assign --name "keyvaultdotnetcorequickstart" --resource-group "<YourResourceGroupName>"
   ```

>[!NOTE]
>이 절차의 명령은 포털로 이동하여 웹 애플리케이션 속성에서 **ID/시스템 할당됨** 설정을 **켜기**로 전환하는 것과 동일합니다.

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>애플리케이션에 Key Vault에서 비밀을 읽을 수 있는 권한 할당

Azure에 애플리케이션을 게시할 때 출력을 적어 둡니다. 형식은 다음과 같아야 합니다.
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
그런 다음, 키 자격 증명 모음의 이름 및 **PrincipalId** 값을 사용하여 다음 명령을 실행합니다.

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list

```

이제 애플리케이션이 실행되면 검색된 비밀 값이 표시됩니다. 앞의 명령에서는 Key Vault에서 **가져오기** 및 **나열** 작업을 수행할 App Service 사용 권한의 ID를 제공합니다.

## <a name="clean-up-resources"></a>리소스 정리
더 이상 필요하지 않을 때 리소스 그룹, 가상 머신 및 모든 관련 리소스를 삭제합니다. 이렇게 하려면 VM에 대한 리소스 그룹을 선택하고 **삭제**를 선택합니다.

[az keyvault delete](https://docs.microsoft.com/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-delete) 명령을 사용하여 키 자격 증명 모음을 삭제합니다.

```azurecli
az keyvault delete --name
                   [--resource-group]
                   [--subscription]
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Key Vault에 대한 자세한 정보](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)
