---
title: .NET을 사용하여 Azure Key Vault에 서비스 간 인증
description: Microsoft.Azure.Services.AppAuthentication 라이브러리를 사용하여 .NET으로 Azure Key Vault에 인증합니다.
keywords: Azure Key Vault 인증 로컬 자격 증명
author: msmbaldwin
manager: rkarlin
services: key-vault
ms.author: mbaldwin
ms.date: 08/28/2019
ms.topic: conceptual
ms.service: key-vault
ms.subservice: general
ms.openlocfilehash: d6ac5961cbecf4e81c0b6bcc25c39aad42b18416
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429838"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>.NET을 사용하여 Azure Key Vault에 서비스 간 인증

Azure 키 볼트를 인증하려면 공유 비밀 또는 인증서중 하나인 Azure Active Directory(Azure AD) 자격 증명이 필요합니다.

이러한 자격 증명을 관리하는 것은 어려울 수 있습니다. 원본 또는 구성 파일에 자격 증명을 포함시켜 앱에 자격 증명을 번들로 묶는 것은 유혹적인 것입니다. .NET 라이브러리에 대한 `Microsoft.Azure.Services.AppAuthentication`은 이 문제를 단순화합니다. 개발자의 자격 증명을 사용하여 로컬 개발 동안 인증합니다. 나중에 Azure에 솔루션이 배포되면 라이브러리는 애플리케이션 자격 증명으로 자동으로 전환됩니다. Azure AD 자격 증명을 만들거나 개발자 간에 자격 증명을 공유할 필요가 없으므로 로컬 개발 중에 개발자 자격 증명을 사용하는 것이 더 안전합니다.

라이브러리는 `Microsoft.Azure.Services.AppAuthentication` 인증을 자동으로 관리하므로 자격 증명이 아닌 솔루션에 집중할 수 있습니다. Microsoft Visual Studio, Azure CLI 또는 Azure AD 통합 인증을 통해 로컬 개발을 지원합니다. 관리 ID를 지원하는 Azure 리소스에 배포된 라이브러리는 [Azure 리소스용 관리 ID](../../active-directory/msi-overview.md)를 자동으로 사용합니다. 코드 또는 구성을 변경할 필요가 없습니다. 또한 라이브러리는 관리되는 ID를 사용할 수 없거나 로컬 개발 중에 개발자의 보안 컨텍스트를 확인할 수 없는 경우 Azure AD [클라이언트 자격 증명의](../../azure-resource-manager/resource-group-authenticate-service-principal.md) 직접 사용을 지원합니다.

## <a name="prerequisites"></a>사전 요구 사항

- [비주얼 스튜디오 2019](https://www.visualstudio.com/downloads/) 또는 [비주얼 스튜디오 2017 v15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/).

- Visual Studio용 앱 인증 확장은 Visual Studio 2017 업데이트 5에 대한 별도의 확장으로 제공되며 업데이트 6 이후의 제품과 함께 번들로 제공됩니다. 업데이트 6 이상에서는 Visual Studio 설치 관리자 내에서 Azure 개발 도구를 선택하여 앱 인증 확장의 설치를 확인할 수 있습니다.

## <a name="using-the-library"></a>라이브러리 사용

.NET 애플리케이션의 경우 `Microsoft.Azure.Services.AppAuthentication` 패키지를 통해 관리 ID를 사용하는 것이 가장 간단한 방법입니다. 시작 방법은 다음과 같습니다.

1. **NuGet** > **패키지 관리자** > 를 선택 NuGet 패키지**관리 솔루션에 대 한 참조를** 추가 하는 솔루션에 대 한 [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) 및 [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet 패키지 프로젝트에 추가 합니다.

1. 다음 코드를 추가합니다.

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // Instantiate a new KeyVaultClient object, with an access token to Key Vault
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));

    // Optional: Request an access token to other Azure services
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider2.GetAccessTokenAsync("https://management.azure.com/").ConfigureAwait(false);
    ```

`AzureServiceTokenProvider` 클래스는 메모리에서 토큰을 캐시하여 만료 직전에 Azure AD에서 검색합니다. 따라서 `GetAccessTokenAsync` 메서드를 호출하기 전에 만료를 더 이상 확인할 필요가 없습니다. 토큰을 사용하려면 메서드를 호출하기만 하면 됩니다.

`GetAccessTokenAsync` 메서드는 리소스 식별자가 필요합니다. Microsoft Azure 서비스에 대해 자세히 알아보려면 [Azure 리소스에 대해 관리되는 ID를](../../active-directory/msi-overview.md)참조하십시오.

## <a name="local-development-authentication"></a>로컬 개발 인증

로컬 개발의 경우 Azure 서비스에 [대한 인증](#authenticating-to-azure-services)및 사용자 [지정 서비스에 대한 인증이라는](#authenticating-to-custom-services)두 가지 기본 인증 시나리오가 있습니다.

### <a name="authenticating-to-azure-services"></a>Azure 서비스에 인증

로컬 컴퓨터는 Azure 리소스에 대해 관리되는 ID를 지원하지 않습니다. 따라서 `Microsoft.Azure.Services.AppAuthentication` 라이브러리가 개발자 자격 증명을 사용하여 로컬 개발 환경에서 실행됩니다. Azure에 솔루션이 배포되면 라이브러리가 관리 ID를 사용하여 OAuth 2.0 클라이언트 자격 증명 부여 흐름으로 전환합니다. 이 방법을 사용하면 걱정 없이 로컬 및 원격으로 동일한 코드를 테스트할 수 있습니다.

로컬 개발의 경우 `AzureServiceTokenProvider`는 **Visual Studio**, **Azure CLI**(명령줄 인터페이스) 또는 **Azure AD 통합 인증**을 사용하여 토큰을 페치합니다. 각 옵션을 순차적으로 시도하여 라이브러리가 성공하는 첫 번째 옵션을 사용합니다. 작동하는 옵션이 없는 경우 자세한 정보와 함께 `AzureServiceTokenProviderException` 예외가 throw됩니다.

#### <a name="authenticating-with-visual-studio"></a>Visual Studio를 사용하여 인증

Visual Studio를 사용하여 인증하려면 다음단계를 수행하십시오.

1. Visual Studio에 로그인하고 **도구**&nbsp;>&nbsp;**옵션을** 사용하여 **옵션을**엽니다.

1. **Azure 서비스 인증을**선택하고 로컬 개발을 위한 계정을 선택하고 **확인을**선택합니다.

토큰 공급자 파일과 관련된 오류와 같이 Visual Studio를 사용하는 데 문제가 있는 경우 이전 단계를 주의 깊게 검토합니다.

개발자 토큰을 다시 인증해야 할 수 있습니다. 이렇게 하려면 **도구**&nbsp;>&nbsp;**옵션을**선택한 다음 **&nbsp;Azure&nbsp;서비스 인증을**선택합니다. 선택한 계정에서 **다시 인증** 링크를 찾습니다. 링크를 선택하여 인증합니다.

#### <a name="authenticating-with-azure-cli"></a>Azure CLI를 사용하여 인증

로컬 개발에 Azure CLI를 사용하려면 [Azure CLI v2.0.12](/cli/azure/install-azure-cli) 이상 버전이 있는지 확인하십시오.

Azure CLI를 사용하려면 다음을 수행합니다.

1. Windows 작업 표시줄에서 Azure CLI를 검색하여 **Microsoft Azure 명령 프롬프트를 엽니다.**

1. Azure 포털에 로그인: *az 로그인하여* Azure에 로그인합니다.

1. az 계정 *get-access-token --resource를 https://vault.azure.net *입력하여 액세스를 확인합니다. 오류가 발생하면 올바른 버전의 Azure CLI가 올바르게 설치되었는지 확인합니다.

   Azure CLI가 기본 디렉터리에 설치되지 않은 경우 Azure `AzureServiceTokenProvider` CLI에 대한 경로를 찾을 수 없는 오류 보고가 나타날 수 있습니다. **AzureCLIPath** 환경 변수를 사용하여 Azure CLI 설치 폴더를 정의합니다. `AzureServiceTokenProvider`는 필요한 경우 **AzureCLIPath** 환경 변수에 지정된 디렉터리를 **Path** 환경 변수에 추가합니다.

1. 여러 계정을 사용하여 Azure CLI에 로그인했거나 계정이 여러 구독에 액세스할 수 있는 경우 사용할 구독을 지정해야 합니다. *명령 az 계정 집합 --구독 <구독 id>* 입력합니다.

이 명령은 실패한 경우에만 출력을 생성합니다. 현재 계정 설정을 확인하려면 명령을 `az account list`입력합니다.

#### <a name="authenticating-with-azure-ad-authentication"></a>Azure AD 인증으로 인증

Azure AD 인증을 사용하려면 다음을 확인합니다.

- 온-프레미스 활성 디렉터리 Azure AD에 동기화됩니다. 자세한 내용은 [Azure Active Directory를 사용한 하이브리드 ID란 무엇입니까?](../../active-directory/connect/active-directory-aadconnect.md)

- 도메인 에 가입된 컴퓨터에서 코드가 실행되고 있습니다.

### <a name="authenticating-to-custom-services"></a>사용자 지정 서비스에 인증

Azure 서비스를 사용하면 사용자와 애플리케이션에 모두 액세스할 수 있으므로 서비스에서 Azure 서비스를 호출하는 경우 이전 단계가 작동합니다.

사용자 지정 서비스를 호출하는 서비스를 만들 경우 로컬 개발 인증에 Azure AD 클라이언트 자격 증명을 사용합니다. 옵션에는

- 서비스 주체를 사용하여 Azure에 로그인:

    1. 서비스 주체를 생성합니다. 자세한 내용은 [Azure CLI을 사용하여 Azure 서비스 주체 만들기](/cli/azure/create-an-azure-service-principal-azure-cli)를 참조하세요.

    1. Azure CLI를 사용하여 다음 명령으로 로그인합니다.

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        서비스 주체에 구독에 대한 액세스 권한이 없을 수 있으므로 `--allow-no-subscriptions` 인수를 사용합니다.

- 환경 변수를 사용하여 서비스 주체 세부 사항을 지정합니다. 자세한 내용은 [서비스 주체를 사용하여 응용 프로그램 실행을](#running-the-application-using-a-service-principal)참조하십시오.

Azure에 로그인한 후 서비스 `AzureServiceTokenProvider` 주체를 사용하여 로컬 개발을 위한 토큰을 검색합니다.

이 방법은 로컬 개발에만 적용됩니다. Azure에 솔루션이 배포되면 라이브러리가 관리 ID 인증으로 전환됩니다.

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>관리되는 ID 또는 사용자 할당 ID를 사용하여 응용 프로그램 실행

Azure App Service 또는 활성화된 관리 ID를 사용하는 Azure VM에서 코드를 실행하는 경우 라이브러리는 자동으로 관리 ID를 사용합니다. 코드를 변경할 필요는 없지만 관리되는 *ID에는* 키 자격 증명 모음에 대한 사용 권한이 있어야 합니다. 키 자격 증명 모음의 *액세스 정책을*통해 관리되는 ID get 권한을 *부여할* 수 있습니다.

또는 사용자 할당된 ID로 인증할 수 있습니다. 사용자 할당ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리되는 ID 정보를](../../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work)참조하십시오. 사용자 할당된 ID로 인증하려면 연결 문자열에서 사용자 할당ID의 클라이언트 ID를 지정해야 합니다. 연결 문자열은 연결 문자열 지원에 지정되어 [있습니다.](#connection-string-support)

## <a name="running-the-application-using-a-service-principal"></a>서비스 주체를 사용하여 애플리케이션 실행

인증할 Azure AD 클라이언트 자격 증명을 만들어야 할 수 있습니다. 이 상황은 다음 예제에서 발생할 수 있습니다.

- 코드가 로컬 개발 환경에서 실행되지만 개발자의 ID 아래에서는 실행되지 않습니다. 예를 들어 Service Fabric은 로컬 개발에 [NetworkService 계정](../../service-fabric/service-fabric-application-secret-management.md)을 사용합니다.

- 로컬 개발 환경에서 코드가 실행되고 사용자 지정 서비스에 인증하므로 개발자 ID를 사용할 수 없습니다.

- 코드는 Azure Batch와 같은 Azure 리소스에 대해 관리되는 ID를 아직 지원하지 않는 Azure 계산 리소스에서 실행되고 있습니다.

서비스 보안 주체를 사용하여 응용 프로그램을 실행하는 세 가지 기본 방법이 있습니다. 이들 중 한 가지를 사용하려면 먼저 서비스 주체를 만들어야 합니다. 자세한 내용은 [Azure CLI을 사용하여 Azure 서비스 주체 만들기](/cli/azure/create-an-azure-service-principal-azure-cli)를 참조하세요.

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>로컬 키 저장소의 인증서를 사용하여 Azure AD에 로그인

1. Azure CLI [az 광고 sp 만들기-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 명령을 사용 하 여 서비스 주체 인증서를 만듭니다.

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    이 명령은 홈 디렉터리에 저장된 .pem 파일(개인 키)을 만듭니다. 이 인증서를 *LocalMachine* 또는 *CurrentUser* 저장소에 배포합니다.

    > [!Important]
    > CLI 명령은 .pem 파일을 생성하지만 Windows는 PFX 인증서에 대해서만 기본 지원을 제공합니다. 대신 PFX 인증서를 생성하려면 여기에 표시된 PowerShell 명령을 [사용합니다.](../../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate) 이러한 명령은 인증서도 자동으로 배포합니다.

1. **AzureServicesAuthConnectionString이라는** 환경 변수를 다음 값으로 설정합니다.

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```

    *{AppId}*, *{TenantId}* 및 *{Thumbprint}* 를 1단계에서 생성된 값으로 바꿉니다. *{CertificateStore}를* 배포 계획에 따라 *LocalMachine*' 또는 *CurrentUser로*바꿉습니다.

1. 애플리케이션을 실행합니다.

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>공유 비밀 자격 증명을 사용하여 Azure AD에 로그인

1. --sdk-auth 매개 변수를 사용하여 Azure CLI [az 광고 sp 만들기-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 명령을 사용하여 암호가 있는 서비스 주체 인증서를 만듭니다.

    ```azurecli
    az ad sp create-for-rbac --sdk-auth
    ```

1. **AzureServicesAuthConnectionString이라는** 환경 변수를 다음 값으로 설정합니다.

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}
    ```

    _{AppId}_, _{TenantId}_ 및 _{ClientSecret}_ 을 1단계에서 생성된 값으로 바꿉니다.

1. 애플리케이션을 실행합니다.

모두가 올바로 설정되면 더 이상 코드를 변경할 필요가 없습니다. `AzureServiceTokenProvider`는 환경 변수 및 인증서를 사용하여 Azure AD에 인증합니다.

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>키 볼트의 인증서를 사용하여 Azure AD에 로그인

이 옵션을 사용하면 서비스 주체의 클라이언트 인증서를 Key Vault에 저장하고 서비스 주체 인증에 사용할 수 있습니다. 다음 시나리오에 이 옵션을 사용할 수 있습니다.

- 명시적 서비스 주체를 사용하여 인증하려는 로컬 인증과 서비스 주체 자격 증명을 키 자격 증명에 안전하게 유지하려는 경우 개발자 계정은 키 자격 증명 모음에 액세스할 수 있어야 합니다.

- 명시적 자격 증명을 사용하고 서비스 주체 자격 증명을 키 자격 증명에 안전하게 유지하려는 Azure의 인증입니다. 테넌트 간 시나리오에 이 옵션을 사용할 수 있습니다. 관리되는 ID는 키 자격 증명 모음에 액세스할 수 있어야 합니다.

관리되는 ID 또는 개발자 ID에는 Key Vault에서 클라이언트 인증서를 검색할 수 있는 권한이 있어야 합니다. AppAuthentication 라이브러리는 검색된 인증서를 서비스 주체의 클라이언트 자격 증명으로 사용합니다.

서비스 주체 인증에 클라이언트 인증서를 사용하려면 다음을 수행하십시오.

1. 서비스 주체 인증서를 만들고 키 자격 증명 모음에 자동으로 저장합니다. Azure CLI [az 광고 sp \<만들기-for-rbac--keyvault keyvaultname \<>--cert 인증서 이름>--create-cert--건너뛰기 할당](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 명령:

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```

    인증서 식별자는 형식의 URL이 됩니다.`https://<keyvaultname>.vault.azure.net/secrets/<certificatename>`

1. 이 `{KeyVaultCertificateSecretIdentifier}` 연결 문자열에서 인증서 식별자로 바꿉습니다.

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
    ```

    예를 들어 키 자격 증명 모음이 *myKeyVault라고* 호출되어 *myCert라는*인증서를 만든 경우 인증서 식별자는 다음과 같은 것입니다.

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert
    ```

## <a name="connection-string-support"></a>연결 문자열 지원

기본적으로 `AzureServiceTokenProvider`는 여러 메서드를 사용하여 토큰을 검색합니다.

프로세스를 제어하려면 `AzureServiceTokenProvider` 생성자에 전달되거나 *AzureServicesAuthConnectionString* 환경 변수에 지정된 연결 문자열을 사용합니다.

다음과 같은 옵션이 지원됩니다.

| 연결 문자열 옵션 | 시나리오 | 주석|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | 로컬 개발 | `AzureServiceTokenProvider`AzureCli를 사용하여 토큰을 얻습니다. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | 로컬 개발 | `AzureServiceTokenProvider`Visual Studio를 사용하여 토큰을 얻습니다. |
| `RunAs=CurrentUser` | 로컬 개발 | `AzureServiceTokenProvider`Azure AD 통합 인증을 사용하여 토큰을 가져옵니다. |
| `RunAs=App` | [Azure 리소스에 대한 관리 ID](../../active-directory/managed-identities-azure-resources/index.yml) | `AzureServiceTokenProvider`관리되는 ID를 사용하여 토큰을 가져옵니다. |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Azure 리소스에 대해 사용자 할당된 ID](../../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) | `AzureServiceTokenProvider`사용자 할당된 ID를 사용하여 토큰을 가져옵니다. |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | 사용자 지정 서비스 인증 | `KeyVaultCertificateSecretIdentifier`은 인증서의 비밀 식별자입니다. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| 서비스 주체 | `AzureServiceTokenProvider`는 인증서를 사용하여 Azure AD에서 토큰을 가져옵니다. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | 서비스 주체 | `AzureServiceTokenProvider`는 인증서를 사용하여 Azure AD에서 토큰을 가져옵니다.|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | 서비스 주체 |`AzureServiceTokenProvider`는 암호를 사용하여 Azure AD에서 토큰을 가져옵니다. |

## <a name="samples"></a>샘플

라이브러리가 `Microsoft.Azure.Services.AppAuthentication` 작동하는 것을 보려면 다음 코드 샘플을 참조하십시오.

- [관리 ID를 사용하여 런타임 시 Azure Key Vault에서 암호 검색](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

- [관리 ID를 사용하여 Azure VM에서 프로그래밍 방식으로 Azure Resource Manager 템플릿 배포](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet)

- [.NET Core 샘플 및 관리 ID를 사용하여 Azure Linux VM에서 Azure 서비스 호출](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/)

## <a name="appauthentication-troubleshooting"></a>앱 인증 문제 해결

### <a name="common-issues-during-local-development"></a>지역 개발 중 공통 문제

#### <a name="azure-cli-is-not-installed-youre-not-logged-in-or-you-dont-have-the-latest-version"></a>Azure CLI가 설치되지 않았거나, 로그인하지 않았거나, 최신 버전이 없습니다.

*az 계정 get-access-토큰을* 실행하여 Azure CLI에 토큰이 표시되는지 확인합니다. **이러한 프로그램이 발견되지 않으면**Azure [CLI의 최신 버전을](/cli/azure/install-azure-cli?view=azure-cli-latest)설치합니다. 로그인하라는 메시지가 표시될 수 있습니다.

#### <a name="azureservicetokenprovider-cant-find-the-path-for-azure-cli"></a>AzureServiceTokenProvider Azure CLI에 대 한 경로를 찾을 수 없습니다.

AzureServiceTokenProvider기본 설치 위치에서 Azure CLI를 찾습니다. Azure CLI를 찾을 수 없는 경우 환경 변수 **AzureCLIPath를** Azure CLI 설치 폴더로 설정합니다. AzureServiceTokenProvider 경로 환경 변수에 환경 변수를 추가 합니다.

#### <a name="youre-logged-into-azure-cli-using-multiple-accounts-the-same-account-has-access-to-subscriptions-in-multiple-tenants-or-you-get-an-access-denied-error-when-trying-to-make-calls-during-local-development"></a>여러 계정을 사용하여 Azure CLI에 로그인했거나, 동일한 계정이 여러 테넌트의 구독에 액세스할 수 있거나, 로컬 개발 중에 전화를 걸 때 액세스 거부 오류가 발생합니다.

Azure CLI를 사용하여 사용할 계정이 있는 구독으로 기본 구독을 설정합니다. 구독은 액세스하려는 리소스와 동일한 테넌트에 있어야 합니다: **az 계정 집합 --구독 [구독 id]**. 출력이 보이지 않으면 성공했습니다. **이제 az**계정 목록을 사용하여 올바른 계정이 기본인지 확인합니다.

### <a name="common-issues-across-environments"></a>환경 전반의 일반적인 문제

#### <a name="unauthorized-access-access-denied-forbidden-or-similar-error"></a>무단 액세스, 액세스 거부, 금지 또는 이와 유사한 오류

사용된 보안 주체가 액세스하려는 리소스에 대한 액세스 권한이 없습니다. 사용자 계정 또는 앱 서비스의 MSI "기여자" 리소스에 대한 액세스 권한을 부여합니다. 로컬 컴퓨터에서 샘플을 실행 중인지 아니면 Azure에서 앱 서비스에 배포되는지여부에 따라 달라집니다. 키 자격 증명 모음과 같은 일부 리소스에는 사용자, 앱 및 그룹과 같은 보안 주체에 대한 권한 부여 액세스를 사용하는 자체 [액세스 정책도](https://docs.microsoft.com/azure/key-vault/secure-your-key-vault#data-plane-and-access-policies) 있습니다.

### <a name="common-issues-when-deployed-to-azure-app-service"></a>Azure 앱 서비스에 배포할 때의 일반적인 문제

#### <a name="managed-identity-isnt-set-up-on-the-app-service"></a>앱 서비스에 관리되는 ID가 설정되지 않았습니다.

[Kudu 디버그 콘솔을](https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/)사용하여 환경 변수 MSI_ENDPOINT MSI_SECRET 있는지 확인합니다. 이러한 환경 변수가 없으면 관리되는 ID는 앱 서비스에서 활성화되지 않습니다.

### <a name="common-issues-when-deployed-locally-with-iis"></a>IIS와 함께 로컬로 배포할 때의 일반적인 문제

#### <a name="cant-retrieve-tokens-when-debugging-app-in-iis"></a>IIS에서 앱을 디버깅할 때 토큰을 검색할 수 없습니다.

기본적으로 AppAuth는 IIS의 다른 사용자 컨텍스트에서 실행됩니다. 그렇기 때문에 개발자 ID를 사용하여 액세스 토큰을 검색할 수 있는 액세스 권한이 없습니다. 다음 두 단계로 IIS를 사용자 컨텍스트와 함께 실행하도록 구성할 수 있습니다.
- 현재 사용자 계정으로 실행되도록 웹 앱에 대한 응용 프로그램 풀을 구성합니다. 자세한 내용은 [여기](https://docs.microsoft.com/iis/manage/configuring-security/application-pool-identities#configuring-iis-application-pool-identities)를 참조하세요.
- "setProfileEnvironment"를 "True"로 구성합니다. 자세한 내용은 [여기를](https://docs.microsoft.com/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration)참조하십시오. 

    - %윈디어%\System32\inetsrv\config\applicationHost.config로 이동
    - "프로필 환경 설정"을 검색합니다. "False"로 설정된 경우 "True"로 변경합니다. 없는 경우 processModel 요소()에/configuration/system.applicationHost/applicationPools/applicationPoolDefaults/processModel/@setProfileEnvironment특성으로 추가하고 "True"로 설정합니다.

- [Azure 리소스에 대한 관리 ID](../../active-directory/managed-identities-azure-resources/index.yml)에 대해 자세히 알아보세요.
- [Azure AD 인증 시나리오](../../active-directory/develop/active-directory-authentication-scenarios.md)에 대해 자세히 알아보기.
