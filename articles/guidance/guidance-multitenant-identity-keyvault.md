<properties
   pageTitle="키 자격 증명을 사용하여 응용 프로그램 암호 보호 | Microsoft Azure"
   description="키 자격 증명 모음 서비스를 사용하여 응용 프로그램 암호를 저장하는 방법"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# Azure 키 자격 증명을 사용하여 응용 프로그램 암호 보호

이 문서는 [시리즈의 일부]입니다. 이 시리즈와 함께 제공되는 완전한 [샘플 응용 프로그램]도 있습니다.

## 개요

다음과 같이 민감하고 보호되어야 하는 응용 프로그램 설정을 갖는 것이 일반적입니다.

- 데이터베이스 연결 문자열
- 암호
- 암호화 키

보안 모범 사례로 이 암호를 소스 제어에 저장해서는 안됩니다. 소스 코드 리포지토리가 전용이더라도 너무 쉽게 누수될 수 있습니다. 공용으로부터 암호를 유지하는 것 뿐만이 아닙니다. 대규모 프로젝트에서 프로덕션 암호에 액세스할 수 있는 개발자 및 운영자를 제한할 수 있습니다. (테스트 또는 개발 환경에 대한 설정은 서로 다릅니다.)

더욱 안전한 옵션은 이러한 암호를 [Azure 키 자격 증명 모음][KeyVault]에 저장하는 것입니다. 키 자격 증명 모음은 암호화 키 및 기타 암호를 관리하기 위한 클라우드 호스티드 서비스입니다. 이 문서는 키 자격 증명 모음을 사용하여 앱에 대한 구성 설정을 저장하는 방법을 보여 줍니다.

[Tailspin 설문 조사][Surveys] 응용 프로그램에서 다음 설정은 암호입니다.

- 데이터베이스 연결 문자열.
- Redis 연결 문자열.
- 웹 응용 프로그램에 대한 클라이언트 암호.

키 자격 증명 모음에 구성 암호를 저장하려면 설문 조사 응용 프로그램에서 ASP.NET Core 1.0 [구성 시스템][configuration]에 연결하는 사용자 지정 구성 공급자를 구현합니다. 사용자 지정 공급자는 시작 시에 키 자격 증명 모음에서 구성 설정을 읽습니다.

설문 조사 응용 프로그램은 다음 위치에서 구성 설정을 로드합니다.

- appsettings.json 파일
- [사용자 암호 저장소][user-secrets](테스트용으로 개발 환경에 사용합니다.)
- 호스팅 환경(Azure 웹앱에서 앱 설정)
- 키 자격 증명 모음

각각은 이전 것을 재정의하므로 키 자격 증명 모음에 저장된 모든 설정이 우선적으로 적용됩니다.

> [AZURE.NOTE] 기본적으로 키 자격 증명 모음 구성 공급자는 사용할 수 없습니다. 응용 프로그램을 로컬로 실행하는 데 필요하지 않습니다. 프로덕션 배포에 사용하도록 허용합니다.

> [AZURE.NOTE] 키 자격 증명 모음 공급자는 [Microsoft.Azure.KeyVault][Microsoft.Azure.KeyVault] 패키지를 필요로 하기 때문에 현재 .NET Core에서 지원되지 않습니다.

시작 시 응용 프로그램은 모든 등록된 구성 공급자에서 설정을 읽고 이를 사용하여 강력한 형식의 옵션 개체를 채웁니다. (자세한 내용은 [옵션 및 구성 개체 사용][options]을 참조하세요.)

## 구현

[KeyVaultConfigurationProvider][KeyVaultConfigurationProvider] 클래스는 ASP.NET Core 1.0 [구성 시스템][configuration]에 연결하는 구성 공급자입니다.

`KeyVaultConfigurationProvider`을(를) 사용하려면 시작 클래스에서 `AddKeyVaultSecrets` 확장 메서드를 호출합니다.

```csharp
    var builder = new ConfigurationBuilder()
        .SetBasePath(appEnv.ApplicationBasePath)
        .AddJsonFile("appsettings.json");

    if (env.IsDevelopment())
    {
        builder.AddUserSecrets();
    }
    builder.AddEnvironmentVariables();
    var config = builder.Build();

    // Add key vault configuration:
    builder.AddKeyVaultSecrets(config["AzureAd:ClientId"],
        config["KeyVault:Name"],
        config["AzureAd:Asymmetric:CertificateThumbprint"],
        Convert.ToBoolean(config["AzureAd:Asymmetric:ValidationRequired"]),
        loggerFactory);
```

`KeyVaultConfigurationProvider`은(는) 다른 구성 소스 중 하나에 저장되어야 하는 일부 구성 설정이 필요합니다.

응용 프로그램이 시작되면 `KeyVaultConfigurationProvider`은(는) 키 자격 증명 모음에 모든 암호를 열거합니다. 각 암호의 경우 'ConfigKey'라는 태그를 찾습니다. 태그의 값은 구성 설정의 이름입니다.

> [AZURE.NOTE] [Tags][키 태그]는 키와 함께 저장되는 선택적 메타데이터입니다. 키 이름은 콜론(:) 문자를 포함할 수 없기 때문에 태그가 사용됩니다.

```csharp
var kvClient = new KeyVaultClient(GetTokenAsync);
var secretsResponseList = await kvClient.GetSecretsAsync(_vault, MaxSecrets, token);
foreach (var secretItem in secretsResponseList.Value)
{
    //The actual config key is stored in a tag with the Key "ConfigKey"
    // because ':' is not supported in a shared secret name by Key Vault.
    if (secretItem.Tags != null && secretItem.Tags.ContainsKey(ConfigKey))
    {
        var secret = await kvClient.GetSecretAsync(secretItem.Id, token);
        Data.Add(secret.Tags[ConfigKey], secret.Value);
    }
}
```

> [AZURE.NOTE] [KeyVaultConfigurationProvider.cs]를 참조하세요.

## 설문 조사 앱에서 키 자격 증명 모음 설정

필수 조건:

- [Azure Resource Manager Cmdlet][azure-rm-cmdlets]을 설치합니다.
- [설문 조사 응용 프로그램 실행][readme]에 설명된 대로 설문 조사 응용 프로그램을 구성합니다.

대략적인 단계:

1. 테넌트에서 관리 사용자를 설정합니다.
2. 클라이언트 인증서를 설정합니다.
3. 키 자격 증명 모음을 만듭니다.
4. 키 자격 증명 모음에 구성 설정을 추가합니다.
5. 키 자격 증명 모음을 사용하는 코드의 주석 처리를 제거합니다.
6. 응용 프로그램의 사용자 암호를 업데이트합니다.

### 관리 사용자 설정

> [AZURE.NOTE] 키 자격 증명 모음을 만들려면 Azure 구독을 관리할 수 있는 계정을 사용해야 합니다. 또한 키 자격 증명 모음에서 읽을 수 있도록 허용하는 모든 응용 프로그램을 해당 계정으로 동일한 테넌트에 등록해야 합니다.

이 단계에서 설문 조사 앱이 등록된 테넌트에서 사용자로 로그인한 상태에서 키 자격 증명 모음을 만들 수 있는지 확인합니다.

먼저 Azure 구독과 연결된 디렉터리를 변경합니다.

1. [Azure 관리 포털][azure-management-portal]에 로그인합니다.

2. **설정**을 클릭합니다.

    ![설정](media/guidance-multitenant-identity/settings.png)

3. Azure 구독을 선택합니다.

4. 포털의 맨 아래에서 **디렉터리 편집**을 클릭합니다.

    ![설정](media/guidance-multitenant-identity/edit-directory.png)

5. "연결된 디렉터리 변경"에서 설문 조사 응용 프로그램이 등록된 Azure AD 테넌트를 선택합니다.

    ![설정](media/guidance-multitenant-identity/edit-directory2.png)

6. 화살표 단추를 클릭하고 대화 상자를 완료합니다.

설문 조사 응용 프로그램이 등록된 Azure AD 테넌트 내에서 관리 사용자를 만듭니다.

1. [Azure 관리 포털][azure-management-portal]에 로그인합니다.

2. 응용 프로그램이 등록된 Azure AD 테넌트를 선택합니다.

3. **사용자** > **사용자 추가**를 클릭합니다.

4. **사용자 추가** 대화 상자에서 전역 관리자 역할에 사용자를 할당합니다.

Azure 구독의 공동 관리자로 관리 사용자를 추가합니다.

1. [Azure 관리 포털][azure-management-portal]에 로그인합니다.

2. **설정**을 클릭하고 Azure 구독을 선택합니다.

3. **관리자**를 클릭합니다.

4. 포털의 맨 아래에 있는 **추가**를 클릭합니다.

5. 이전에 만든 관리 사용자의 전자 메일 주소를 입력합니다.

6. 구독에 대한 확인란을 선택합니다.

7. 확인 표시 단추를 클릭하여 대화 상자를 완료합니다.

![공동 관리자 추가](media/guidance-multitenant-identity/co-admin.png)


### 클라이언트 인증서 설정

1. 다음과 같이 PowerShell 스크립트 [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault]을 실행합니다.
    ```
    .\Setup-KeyVault.ps1 -Subject <<subject>>
    ```
    `Subject` 매개 변수의 경우 "surveysapp"과 같은 이름을 임의로 입력합니다. 스크립트는 자체 서명된 인증서를 생성하고 "현재 사용자/개인" 인증서 저장소에 저장합니다.

2. 스크립트의 출력은 JSON 조각입니다. 다음과 같이 이를 웹앱의 응용 프로그램 매니페스트에 추가합니다.

    1. [Azure 관리 포털][azure-management-portal]에 로그인하고 Azure AD 디렉터리로 이동합니다.

    2. **응용 프로그램**을 클릭합니다.

    3. 설문 조사 응용 프로그램을 선택합니다.

    4.	**매니페스트 관리**를 클릭하고 **매니페스트 다운로드**를 선택합니다.

    5.	텍스트 편집기에서 매니페스트 JSON 파일을 엽니다. 스크립트의 출력을 `keyCredentials` 속성에 붙여 넣습니다. 다음과 유사하게 나타납니다.
    ```
            "keyCredentials": [
                {
                  "type": "AsymmetricX509Cert",
                  "usage": "Verify",
                  "keyId": "29d4f7db-0539-455e-b708-....",
                  "customKeyIdentifier": "ZEPpP/+KJe2fVDBNaPNOTDoJMac=",
                  "value": "MIIDAjCCAeqgAwIBAgIQFxeRiU59eL.....
                }
              ],
    ```          
    6.	JSON 파일에 변경 내용을 저장합니다.

    7.	포털로 돌아갑니다. **매니페스트 관리** > **매니페스트 업로드**를 클릭하고 JSON 파일을 업로드합니다.

3. 동일한 JSON 조각을 웹 API(Surveys.WebAPI)의 응용 프로그램 매니페스트에 추가합니다.

4. 다음 명령을 실행하여 인증서의 지문을 가져옵니다.
    ```
    certutil -store -user my [subject]
    ```
    여기서 `[subject]`은(는) PowerShell 스크립트에서 주체에 대해 지정한 값입니다. 지문은 "Cert Hash(sha1)" 아래에 나열됩니다. 16진수 사이의 공백을 제거합니다.

나중에 지문을 사용합니다.

### 키 자격 증명 모음 만들기

1. 다음과 같이 PowerShell 스크립트 [/Scripts/Setup-KeyVault.ps1][Setup-KeyVault]을 실행합니다.

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name>> -ResourceGroupName <<resource group name>> -Location <<location>>
    ```

    자격 증명을 묻는 메시지가 나타나면 이전에 만든 Azure AD 사용자로 로그인합니다. 스크립트는 해당 리소스 그룹 내에 새 리소스 그룹 및 새 키 자격 증명 모음을 만듭니다.

    참고: -위치 매개 변수의 경우 다음 PowerShell 명령을 사용하여 유효한 지역의 목록을 가져올 수 있습니다.

    ```
    Get-AzureRmResourceProvider -ProviderNamespace "microsoft.keyvault" | Where-Object { $_.ResourceTypes.ResourceTypeName -eq "vaults" } | Select-Object -ExpandProperty Locations
    ```

2. 다음 매개 변수를 사용하여 SetupKeyVault.ps를 다시 실행합니다.

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name>> -ApplicationIds @("<<web app client ID>>", "<<web API client ID>>")
    ```

    여기서,

    - 키 자격 증명 모음 이름 = 이전 단계에서 키 자격 증명 모음에 제공한 이름입니다.
    - 웹앱 클라이언트 ID = 설문 조사 웹 응용 프로그램에 대한 클라이언트 ID입니다.
    - 웹 api 클라이언트 ID = Surveys.WebAPI 응용 프로그램에 대한 클라이언트 ID입니다.

    예제:
    ```
    .\Setup-KeyVault.ps1 -KeyVaultName tailspinkv -ApplicationIds @("f84df9d1-91cc-4603-b662-302db51f1031", "8871a4c2-2a23-4650-8b46-0625ff3928a6")
    ```

    > [AZURE.NOTE] [Azure 관리 포털][azure-management-portal]에서 클라이언트 ID를 가져올 수 있습니다. Azure AD 테넌트를 선택하고 응용 프로그램을 선택하고 **구성**을 클릭합니다.

    이 스크립트는 웹앱 및 웹 API에 사용자 키 자격 증명 모음에서 암호를 검색할 수 있는 권한을 부여합니다. 자세한 내용은 [Azure 키 자격 증명 모음 시작][authorize-app]을 참조하세요.

### 키 자격 증명 모음에 구성 설정 추가

1. 다음과 같이 SetupKeyVault.ps를 실행합니다.

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name> -KeyName RedisCache -KeyValue "<<Redis DNS name>>.redis.cache.windows.net,password=<<Redis access key>>,ssl=true" -ConfigName "Redis:Configuration"
    ```
    여기서,

    - 키 자격 증명 모음 이름 = 이전 단계에서 키 자격 증명 모음에 제공한 이름입니다.
    - Redis DNS 이름 = Redis 캐시 인스턴스의 DNS 이름입니다.
    - Redis 액세스 키 = Redis 캐시 인스턴스에 대한 액세스 키입니다.

    이 명령은 키 자격 증명 모음에 암호를 추가합니다. 암호는 이름/값 쌍과 태그입니다.

    -	키 이름은 응용 프로그램에서 사용되지 않지만 키 자격 증명 모음 내에서 고유해야 합니다.
    -	값은 구성 옵션의 값으로 이 경우 Redis 연결 문자열입니다.
    -	"ConfigKey" 태그는 구성 키의 이름을 보유합니다.

2. 이 시점에서 키 자격 증명 모음에 암호를 성공적으로 저장했는지 여부를 테스트하는 것이 좋습니다. 다음 PowerShell 명령을 실행합니다.

    ```
    Get-AzureKeyVaultSecret <<key vault name>> RedisCache | Select-Object *
    ```
    출력은 암호 값 및 일부 메타데이터를 표시해야 합니다.

    ![PowerShell 출력](media/guidance-multitenant-identity/get-secret.png)

3. SetupKeyVault.ps를 다시 실행하여 데이터베이스 연결 문자열을 추가합니다.

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName <<key vault name> -KeyName ConnectionString -KeyValue <<DB connection string>> -ConfigName "Data:SurveysConnectionString"
    ```

    여기에서 `<<DB connection string>>`은(는) 데이터베이스 연결 문자열의 값입니다.

    로컬 데이터베이스로 테스트하기 위해 Tailspin.Surveys.Web/appsettings.json 파일에서 연결 문자열을 복사합니다. 이를 수행하는 경우 이중 백슬래시('\\\')를 단일 백슬래시로 변경해야 합니다. 이중 백슬래시는 JSON 파일에서 이스케이프 문자입니다.

    예제:

    ```
    .\Setup-KeyVault.ps1 -KeyVaultName mykeyvault -KeyName ConnectionString -KeyValue "Server=(localdb)\MSSQLLocalDB;Database=Tailspin.SurveysDB;Trusted_Connection=True;MultipleActiveResultSets=true" -ConfigName "Data:SurveysConnectionString"
    ```

### 키 자격 증명 모음을 사용하는 코드의 주석 처리 제거

1. Tailspin.Surveys 솔루션을 엽니다.

2. [Tailspin.Surveys.Web/Startup.cs][web-startup]에서 다음 코드 블록을 찾고 주석 처리를 제거합니다.

    ```csharp
    //#if DNX451
    //            _configuration = builder.Build();
    //            builder.AddKeyVaultSecrets(_configuration["AzureAd:ClientId"],
    //                _configuration["KeyVault:Name"],
    //                _configuration["AzureAd:Asymmetric:CertificateThumbprint"],
    //                Convert.ToBoolean(_configuration["AzureAd:Asymmetric:ValidationRequired"]),
    //                loggerFactory);
    //#endif
    ```

3. [Tailspin.Surveys.WebAPI/Startup.cs][web-api-startup]에서 다음 코드 블록을 찾고 주석 처리를 제거합니다.

    ```csharp
    //#if DNX451
    //            var config = builder.Build();
    //            builder.AddKeyVaultSecrets(config["AzureAd:ClientId"],
    //                config["KeyVault:Name"],
    //                config["AzureAd:Asymmetric:CertificateThumbprint"],
    //                Convert.ToBoolean(config["AzureAd:Asymmetric:ValidationRequired"]),
    //                loggerFactory);
    //#endif
    ```

4. [Tailspin.Surveys.Web/Startup.cs][web-startup]에서 `ICredentialService`을(를) 등록하는 코드를 찾습니다. `CertificateCredentialService`을(를) 사용하는 줄의 주석 처리를 제거하고 `ClientCredentialService`을(를) 사용하는 줄을 주석으로 처리합니다.

    ```csharp
    // Uncomment this:
    services.AddSingleton<ICredentialService, CertificateCredentialService>();
    // Comment out this:
    //services.AddSingleton<ICredentialService, ClientCredentialService>();
    ```

    이 변경 내용은 웹앱이 [클라이언트 어설션][client-assertion]을 사용하여 OAuth 액세스 토큰을 가져올 수 있도록 합니다. 클라이언트 어설션을 사용하는 경우 OAuth 클라이언트 암호가 필요 없습니다. 또는 클라이언트 암호를 키 자격 증명 모음에 저장할 수 있습니다. 그러나 키 자격 증명 모음 및 클라이언트 어설션 모두에서 클라이언트 인증서를 사용하므로 키 자격 증명 모음을 활성화하는 경우 클라이언트 어설션도 활성화하는 것이 좋습니다.

### 사용자 암호 업데이트

솔루션 탐색기에서 Tailspin.Surveys.Web 프로젝트를 마우스 오른쪽 단추로 클릭하고 **사용자 암호 관리**를 선택합니다. secrets.json 파일에서 기존 JSON을 삭제하고 다음에 붙여 넣습니다.

    ```
    {
      "AzureAd": {
        "ClientId": "[Surveys web app client ID]",
        "PostLogoutRedirectUri": "https://localhost:44300/",
        "WebApiResourceId": "[App ID URI of your Surveys.WebAPI application]",
        "Asymmetric": {
          "CertificateThumbprint": "[certificate thumbprint. Example: 105b2ff3bc842c53582661716db1b7cdc6b43ec9]",
          "StoreName": "My",
          "StoreLocation": "CurrentUser",
          "ValidationRequired": "false"
        }
      },
      "KeyVault": {
        "Name": "[key vault name]"
      }
    }
    ```

[대괄호] 안에 있는 항목을 올바른 값으로 바꿉니다.

- `AzureAd:ClientId`: 설문 조사 앱의 클라이언트 ID입니다.
- `AzureAd:WebApiResourceId`: Azure AD에서 Surveys.WebAPI 응용 프로그램을 만들 때 지정한 앱 ID URI입니다.
- `Asymmetric:CertificateThumbprint`: 클라이언트 인증서를 만들 때 이전에 가져온 인증서 지문입니다.
- `KeyVault:Name`: 키 자격 증명 모음의 이름입니다.

> [AZURE.NOTE] `Asymmetric:ValidationRequired`은(는) 이전에 만든 인증서가 루트 CA 인증 기관에서 서명되지 않았기 때문에 false입니다. 프로덕션에서 CA 기관에서 서명된 인증서를 사용하고 `ValidationRequired`을(를) true로 설정합니다.

업데이트된 secrets.json 파일을 저장합니다.

다음으로 솔루션 탐색기에서 Tailspin.Surveys.WebApi 프로젝트를 마우스 오른쪽 단추로 클릭하고 **사용자 암호 관리**를 선택합니다. 기존 JSON을 삭제하고 다음에 붙여 넣습니다.

```
{
  "AzureAd": {
    "ClientId": "[Surveys.WebAPI client ID]",
    "WebApiResourceId": "https://tailspin5.onmicrosoft.com/surveys.webapi",
    "Asymmetric": {
      "CertificateThumbprint": "[certificate thumbprint]",
      "StoreName": "My",
      "StoreLocation": "CurrentUser",
      "ValidationRequired": "false"
    }
  },
  "KeyVault": {
    "Name": "[key vault name]"
  }
}
```

[대괄호] 안에 있는 항목의 이름을 바꾸고 secrets.json 파일을 저장합니다.

> [AZURE.NOTE] 웹 API의 경우 설문 조사 응용 프로그램이 아닌 Surveys.WebAPI 응용 프로그램에 대한 클라이언트 ID를 사용해야 합니다.


<!-- Links -->
[authorize-app]: ../key-vault/key-vault-get-started.md/#authorize
[azure-management-portal]: https://manage.windowsazure.com/
[azure-rm-cmdlets]: https://msdn.microsoft.com/library/mt125356.aspx
[client-assertion]: guidance-multitenant-identity-client-assertion.md
[configuration]: https://docs.asp.net/en/latest/fundamentals/configuration.html
[KeyVault]: https://azure.microsoft.com/services/key-vault/
[KeyVaultConfigurationProvider]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Configuration.KeyVault/KeyVaultConfigurationProvider.cs
[키 태그]: https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_Keytags
[Microsoft.Azure.KeyVault]: https://www.nuget.org/packages/Microsoft.Azure.KeyVault/
[options]: https://docs.asp.net/en/latest/fundamentals/configuration.html#using-options-and-configuration-objects
[readme]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md
[Setup-KeyVault]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/scripts/Setup-KeyVault.ps1
[Surveys]: guidance-multitenant-identity-tailspin.md
[user-secrets]: http://go.microsoft.com/fwlink/?LinkID=532709
[web-startup]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs
[web-api-startup]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.WebAPI/Startup.cs
[시리즈의 일부]: guidance-multitenant-identity.md
[KeyVaultConfigurationProvider.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Configuration.KeyVault/KeyVaultConfigurationProvider.cs
[샘플 응용 프로그램]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->