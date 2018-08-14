---
title: Azure 빠른 시작 - Key Vault에서 비밀을 설정하고 검색하도록 Azure 웹 응용 프로그램 구성 | Microsoft Docs
description: Key Vault에서 비밀을 설정하고 검색하도록 ASP.Net core 응용 프로그램 구성하는 방법을 보여주는 빠른 시작
services: key-vault
author: prashanthyv
manager: sumedhb
ms.service: key-vault
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 8b5624ae3083d92213b4ee919dc0860bf5ff4ab7
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/03/2018
ms.locfileid: "39480205"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-a-net-web-app"></a>빠른 시작: .NET 웹앱을 사용하여 Azure Key Vault에서 비밀을 설정하고 검색

이 빠른 시작에서는 관리되는 서비스 ID를 사용하여 Key Vault에서 정보를 읽는 Azure 웹 응용 프로그램을 만드는 단계를 살펴봅니다. 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Key Vault를 만듭니다.
> * Key Vault에 비밀을 저장합니다.
> * Key Vault에서 비밀을 검색합니다.
> * Azure 웹 응용 프로그램을 만듭니다.
> * [관리되는 서비스 ID를 사용하도록 설정합니다](../active-directory/managed-service-identity/overview.md).
> * 웹 응용 프로그램이 Key Vault에서 데이터를 읽기 위해 필요한 권한을 부여합니다.

본론으로 들어가기 전에 [기본 개념](key-vault-whatis.md#basic-concepts)의 [관리 서비스 ID](../active-directory/managed-service-identity/overview.md)를 집중적으로 읽어보세요.

## <a name="prerequisites"></a>필수 조건

* Windows에서:
  * 다음 워크로드를 지원하는 [Visual Studio 2017 버전 15.7.3 이상](https://www.microsoft.com/net/download/windows):
    * ASP.NET 및 웹 개발
    * .NET Core 플랫폼 간 개발
  * [.NET Core 2.1 SDK 이상](https://www.microsoft.com/net/download/windows)

* Mac에서:
  * https://visualstudio.microsoft.com/vs/mac/

* 모든 플랫폼:
  * [여기](https://git-scm.com/downloads)에서 GIT를 다운로드하세요.
  * Azure 구독. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Azure CLI 버전 2.0.4 이상이 필요합니다. Windows, Mac 및 Linux에 사용 가능합니다.

## <a name="login-to-azure"></a>Azure에 로그인

   CLI를 사용하여 Azure에 로그인하려면 다음을 입력합니다.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#az-group-create) 명령을 사용하여 리소스 그룹을 만듭니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

리소스 그룹 이름을 선택하고 자리 표시자를 입력하세요.
다음 예에서는 *eastus* 위치에 *<YourResourceGroupName>* 이라는 리소스 그룹을 만듭니다.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

방금 만든 리소스 그룹은 이 문서 전체에서 사용됩니다.

## <a name="create-an-azure-key-vault"></a>Azure Key Vault 만들기

다음으로 이전 단계에서 만든 리소스 그룹에 Key Vault를 만듭니다. 다음 정보를 지정합니다.

* 자격 증명 모음 이름 - **여기에서 Key Vault 이름을 선택하세요**. Key Vault 이름은 0~9, a~z, A~Z 및 -만 포함된 3-24자 길이의 문자열이어야 합니다.
* 리소스 그룹 이름 - **여기에서 리소스 그룹 이름을 선택하세요**.
* 위치 - **미국 동부**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

이때 사용자의 Azure 계정은 이 새 자격 증명 모음에서 모든 작업을 수행할 권한이 있는 유일한 계정입니다.

## <a name="add-a-secret-to-key-vault"></a>Key Vault에 비밀 추가

이 작업을 설명하기 위한 비밀을 추가하고 있습니다. 안전하게 보관해야 하면서도 응용 프로그램에 제공해야 하는 SQL 연결 문자열 또는 기타 정보를 저장할 수 있습니다. 이 자습서에서 암호는 **AppSecret**이고 그 안에 **MySecret** 값이 저장됩니다.

아래 명령을 입력하여 값 **MySecret**을 저장하는 **AppSecret**이라는 비밀을 Key Vault에 만듭니다.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

비밀에 들어 있는 값을 일반 텍스트로 보려면:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

이 명령은 URI를 포함한 비밀 정보를 표시합니다. 이러한 단계를 완료하면 Azure Key Vault의 비밀에 대한 URI이 생깁니다. 이 정보를 기록해 둡니다. 나중에 필요합니다.

## <a name="clone-the-repo"></a>리포지토리 복제

소스를 편집할 로컬 복사본을 만들려면 다음 명령을 실행하여 리포지토리를 복제합니다.

```
git clone https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart.git
```

## <a name="open-and-edit-the-solution"></a>솔루션 열기 및 편집

특정 키 자격 증명 모음 이름으로 샘플을 실행하려면 program.cs 파일을 편집합니다.

1. key-vault-dotnet-core-quickstart 폴더로 이동합니다.
2. Visual Studio 2017에서 key-vault-dotnet-core-quickstart.sln 파일을 엽니다.
3. Program.cs 파일을 열고 앞서 만든 Key Vault의 이름으로 자리 표시자 <YourKeyVaultName>을 업데이트합니다.

이 솔루션에서는 [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) 및 [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet 라이브러리를 사용합니다.

## <a name="run-the-app"></a>앱 실행

Visual Studio 2017의 주 메뉴에서 디버그 > 디버깅하지 않고 시작을 선택합니다. 브라우저가 나타나면 정보 페이지로 이동합니다. AppSecret의 값이 표시됩니다.

## <a name="publish-the-web-application-to-azure"></a>Azure에 웹 응용 프로그램 게시

이 앱을 Azure에 게시하여 웹앱으로 작동하는지 확인하고 가져온 비밀 값도 확인합니다.

1. Visual Studio에서 **key-vault-dotnet-core-quickstart** 프로젝트를 선택합니다.
2. **게시**, **시작**을 차례로 선택합니다.
3. 새 **App Service**를 만들고, **게시**를 선택합니다.
4. 앱 이름을 "keyvaultdotnetcorequickstart"로 변경합니다.
5. **만들기**를 선택합니다.

>[!VIDEO https://sec.ch9.ms/ch9/e93d/a6ac417f-2e63-4125-a37a-8f34bf0fe93d/KeyVault_high.mp4]

## <a name="enable-managed-service-identities-msi"></a>MSI(관리 서비스 ID) 사용

Azure Key Vault를 사용하면 자격 증명과 기타 키 및 비밀을 안전하게 저장할 수 있습니다. 하지만 이러한 자격 증명/키/비밀을 검색하려면 코드가 Azure Key Vault에 인증해야 합니다. MSI(관리 서비스 ID)를 사용하면 Azure AD(Azure Active Directory)에서 자동으로 관리되는 ID를 Azure 서비스에 제공함으로써 이 문제를 보다 간편하게 해결할 수 있습니다. 이 ID를 사용하면 Key Vault를 비롯하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다.

1. Azure CLI로 돌아갑니다.
2. 이 응용 프로그램에 대한 ID를 만들려면 assign-identity 명령을 실행합니다.

```azurecli
az webapp identity assign --name "keyvaultdotnetcorequickstart" --resource-group "<YourResourceGroupName>"
```

>[!NOTE]
>이 명령은 포털로 이동하여 웹 응용 프로그램 속성에서 **관리되는 서비스 ID**를 **켜기**로 전환하는 것과 동일합니다.

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>응용 프로그램에 Key Vault에서 비밀을 읽을 수 있는 권한 할당

[Azure에 응용 프로그램을 게시][]할 때 출력을 메모하세요. 형식은 다음과 같아야 합니다.
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
그런 다음, Key Vault의 이름 및 위에서 복사한 PrincipalId 값을 사용하여 이 명령을 실행합니다.

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get

```

## <a name="next-steps"></a>다음 단계

* [Azure Key Vault 홈 페이지](https://azure.microsoft.com/services/key-vault/)
* [Azure Key Vault 설명서](https://docs.microsoft.com/azure/key-vault/)
* [Azure SDK For .NET](https://github.com/Azure/azure-sdk-for-net)
* [Azure REST API 참조](https://docs.microsoft.com/rest/api/keyvault/)
