---
title: "로그인 및 토큰 획득에 대한 Azure VM 관리 서비스 ID를 사용하는 방법"
description: "로그인에 Azure VM MSI 서비스 주체를 사용하고 액세스 토큰을 획득하는 단계별 지침입니다."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/17/2017
ms.author: bryanla
ms.openlocfilehash: 905e7b0d8a0c45c98a86882a8c8f387be0950f9f
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2017
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-sign-in-and-token-acquisition"></a>로그인 및 토큰 획득에 대한 Azure VM MSI(관리 서비스 ID)를 사용하는 방법 
[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]Azure VM에서 MSI를 설정한 후에 로그인에 MSI를 사용하여 액세스 토큰을 요청할 수 있습니다. 이 문서에서는 로그인에 MSI [서비스 주체](develop/active-directory-dev-glossary.md#service-principal-object)를 사용하고 다른 리소스에 액세스하기 위해 [앱 전용 액세스 토큰](develop/active-directory-dev-glossary.md#access-token)을 획득하는 다음과 같은 다양한 방법을 보여줍니다.

- PowerShell 또는 Azure CLI의 자동/무인 로그인
- .NET, PowerShell, Bash/CURL, REST 등 다양한 클라이언트의 토큰 획득
- .NET, Java, Node.js, Python, Ruby 등 Azure SDK를 사용하여 로그인

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

이 문서에서 PowerShell 예제를 사용하려는 경우 [Azure PowerShell 버전 4.3.1](https://www.powershellgallery.com/packages/AzureRM) 이상을 설치해야 합니다. 이 문서에서 Azure CLI 예제를 사용하려는 경우 다음과 같은 세 가지 옵션이 있습니다.
- Azure Portal에서 [Azure Cloud Shell](../cloud-shell/overview.md)을 사용합니다.
- Azure CLI 코드 블록의 오른쪽 위에 있는 "사용해 보세요." 단추를 통해 포함된 Azure Cloud Shell을 사용합니다.
- 로컬 명령 프롬프트에서 CLI를 사용하려는 경우 [CLI 2.0의 최신 버전(2.0.13 이상)을 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)합니다. 


> [!IMPORTANT]
> - 이 문서의 모든 샘플 코드/스크립트에서는 MSI 사용 가상 컴퓨터에서 클라이언트를 실행한다고 가정합니다. VM에서 MSI 사용에 대한 자세한 내용은 [Azure Portal을 사용하여 VM MSI(관리 서비스 ID) 구성](msi-qs-configure-portal-windows-vm.md) 또는 (PowerShell, CLI, 템플릿 또는 Azure SDK를 사용하는) 변형 문서 중 하나를 참조하세요. 
> - 코드/스크립트 예제에서 권한 부여 오류(403/AuthorizationFailed)를 방지하려면 VM의 ID는 VM에서 Azure Resource Manager 작업을 허용하는 VM 범위에서 "판독기" 액세스 권한이어야 합니다. 자세한 내용은 [Azure Portal을 사용하여 MSI(관리 서비스 ID)에 리소스 액세스 권한 할당](msi-howto-assign-access-portal.md)을 참조하세요.
> - 다음 섹션 중 하나를 계속하기 전에 Azure Portal에서 VM "연결" 기능을 사용하여 MSI 사용 VM에 원격으로 연결합니다.

## <a name="how-to-sign-in-from-powershell-or-cli-using-msi"></a>MSI를 사용하여 PowerShell 또는 CLI에서 로그인하는 방법

이전에는 고유한 ID 스크립트에서 스크립트를 실행하는 경우 다음과 같은 작업을 수행했습니다.
- Azure AD에서 기밀/웹 클라이언트 응용 프로그램으로 등록
- 응용 프로그램의 클라이언트 ID/비밀 자격 증명을 사용하여 로그인

MSI를 사용하면 스크립트 클라이언트는 더 이상 Azure AD에 등록하거나 자격 증명을 제공하지 않아도 됩니다. 다음 예에서는 로그인에 VM의 MSI 서비스 주체를 사용하는 방법을 설명합니다.

### <a name="azure-powershell"></a>Azure PowerShell

다음 스크립트에서는 다음과 같은 방법을 설명합니다.

- Azure VM의 MSI 액세스 토큰 획득
- 액세스 토큰을 사용하여 해당하는 MSI 서비스 주체 하의 Azure AD에 로그인
- MSI 서비스 주체를 사용하여 Azure Resource Manager를 호출하고 서비스 주체의 ID를 가져옵니다.

```powershell
# Get an access token from MSI
$response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                              -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token

# Use the access token to sign in under the MSI service principal
Login-AzureRmAccount -AccessToken $access_token -AccountId “CLIENT”

# The MSI service principal is now signed in for this session.
# Next, a call to Azure Resource Manager is made to get the service principal ID for the VM's MSI. 
$spID = (Get-AzureRMVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>).identity.principalid
echo "The MSI service principal ID is $spID"
```
   
### <a name="azure-cli"></a>Azure CLI

다음 스크립트에서는 다음과 같은 방법을 설명합니다.

- VM의 MSI 서비스 주체 하의 Azure AD에 로그인
- MSI 서비스 주체를 사용하여 Azure Resource Manager를 호출하고 서비스 주체의 ID를 가져옵니다.

```azurecli-interactive
az login --msi
spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
echo The MSI service principal ID is $spID
```

## <a name="how-to-acquire-an-access-token-from-msi"></a>MSI의 액세스 토큰을 획득하는 방법

MSI를 사용하면 클라이언트 응용 프로그램/스크립트는 더 이상 Azure AD에 등록하거나 액세스 토큰을 가져오기 위해 해당 클라이언트 ID와 비밀을 제공하지 않아도 됩니다. 클라이언트는 응용 프로그램 자격 증명을 제공하지 않고 액세스 토큰의 로컬 MSI 끝점을 쉽게 요청할 수 있습니다. 앱 전용 액세스 토큰이 MSI 서비스 주체에 대해 발급되면 [서비스 간 호출 요청 클라이언트 자격 증명](active-directory-protocols-oauth-service-to-service.md)에서 전달자 토큰으로 사용하기에 적합합니다.

다음 예에서는 토큰을 획득하기 위해 VM의 MSI 서비스 주체를 사용하는 방법을 설명합니다.

### <a name="net"></a>.NET

> [!IMPORTANT]
> ADAL(Azure AD 인증 라이브러리)은 MSI와 통합되지 않습니다. MSI를 사용하여 액세스 토큰을 가져오려면 VM에서 로컬 MSI 끝점에 요청합니다. 자세한 내용은 [MSI FAQ 및 알려진 문제](msi-known-issues.md#frequently-asked-questions-faqs)를 참조하세요.

```csharp
// Build request to acquire MSI token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://localhost:50342/oauth2/token?resource=https://management.azure.com/");
request.Headers["Metadata"] = "true";
request.Method = "GET";

try
{
    // Call /token endpoint
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader, and extract access token
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    string accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

```

### <a name="azure-powershell-token"></a>Azure PowerShell

```powershell
# Get an access token from MSI
$response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                              -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The MSI access token is $access_token"
```

### <a name="bashcurl"></a>Bash/CURL

```bash
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

### <a name="rest"></a>REST (영문)

샘플 요청:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| 요소 | 설명 |
| ------- | ----------- |
| `GET` | HTTP 동사는 끝점에서 데이터를 검색한다는 것을 나타냅니다. 이 경우에는 OAuth 액세스 토큰입니다. | 
| `http://localhost:50342/oauth2/token` | MSI 끝점에서 50342는 기본 포트이며 구성 가능합니다. |
| `resource` | 쿼리 문자열 매개 변수는 대상 리소스의 앱 ID URI를 나타냅니다. 또한 발급된 토큰의 `aud` (대상) 클레임에서 표시됩니다. 이 예제에서는 Azure Resource Manager에 액세스할 수 있는 토큰을 요청합니다. 여기에는 https://management.azure.com/의 앱 ID URI가 포함됩니다. |
| `Metadata` | SSRF(서버 쪽 요청 위조) 공격에 대한 완화 수단으로 MSI에서 HTTP 요청 헤더 필드가 필요합니다. 이 값은 모두 소문자이며 "true"로 설정되어야 합니다.

샘플 응답:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| 요소 | 설명 |
| ------- | ----------- |
| `access_token` | 요청된 액세스 토큰입니다. REST API를 호출할 때 토큰은 호출자를 인증하는 API를 허용하는 "전달자" 토큰으로 `Authorization` 요청 헤더 필드에 포함됩니다. | 
| `refresh_token` | MSI에서 사용하지 않습니다. |
| `expires_in` | 액세스 토큰의 발급 시간부터 만료 이전까지 계속 유효한 시간(초)입니다. 발급 시간은 토큰의 `iat` 클레임에서 확인할 수 있습니다. |
| `expires_on` | 액세스 토큰이 만료되는 시간 범위입니다. 날짜는 "1970-01-01T0:0:0Z UTC"부터 시간(초)으로 표시됩니다(토큰의 `exp` 클레임에 해당함). |
| `not_before` | 액세스 토큰이 적용되고 허용될 수 있는 시간 범위입니다. 날짜는 "1970-01-01T0:0:0Z UTC"부터 시간(초)으로 표시됩니다(토큰의 `nbf` 클레임에 해당함). |
| `resource` | 액세스 토큰이 요청되는 리소스는 요청의 `resource` 쿼리 문자열 매개 변수와 일치합니다. |
| `token_type` | 토큰의 형식은 "전달자" 액세스 토큰입니다. 즉, 리소스가 이 토큰의 전달자에 액세스 권한을 제공할 수 있습니다. |

## <a name="how-to-use-msi-with-azure-sdk-libraries"></a>Azure SDK 라이브러리와 MSI를 사용하는 방법

Azure는 일련의 [Azure SDK](https://azure.microsoft.com/downloads)를 통해 여러 프로그래밍 플랫폼을 지원합니다. 일부는 MSI를 사용하는 로그인을 지원하도록 업데이트되었으며 사용법을 보여주는 예제를 제공합니다. 지원이 추가됨에 따라 이 목록이 업데이트됩니다.

| SDK) | 샘플 |
| --- | ------ | 
| .NET | [관리 서비스 ID를 사용하여 Windows VM에서 ARM 템플릿 배포](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core | [관리 서비스 ID를 사용하여 Linux VM에서 Azure 서비스 호출](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js| [관리 서비스 ID를 사용하여 리소스 관리](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python | [MSI를 사용하여 VM 내부에서 쉽게 인증](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| 루비   | [MSI가 활성화된 VM에서 리소스 관리](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) | 

## <a name="additional-information"></a>추가 정보

### <a name="token-expiration"></a>토큰 만료

로컬 MSI 하위 시스템은 토큰을 캐시합니다. 따라서 원하는 대로 자주 호출할 수 있으며 다음과 같은 경우에 Azure AD에 대한 실시간 호출이 발생합니다.
- 캐시에서 토큰 부재로 인해 캐시 누락이 발생하는 경우
- 토큰이 만료된 경우

코드에서 토큰을 캐시하는 경우 토큰이 만료되었음을 리소스가 가리키는 시나리오를 처리하도록 준비해야 합니다.

### <a name="resource-ids-for-azure-services"></a>Azure 서비스의 리소스 ID

MSI 및 해당하는 리소스 ID를 지원하는 서비스의 목록은 [Azure AD 인증을 지원하는 Azure 서비스](msi-overview.md#azure-services-that-support-azure-ad-authentication)를 참조하세요.

## <a name="troubleshooting"></a>문제 해결

### <a name="sign-in-or-token-acquisition-fails"></a>로그인 또는 토큰 획득 실패

MSI가 올바르게 설정되어 있는지 확인합니다. [Azure Portal](https://portal.azure.com)에서 Azure VM으로 돌아가서 다음을 수행합니다.

- "구성" 페이지에서 MSI enabled = "Yes"인지 확인합니다.
- "확장" 페이지에서 MSI 확장이 올바르게 배포되었는지 확인합니다.

이 두 항목 중 하나가 올바르지 않으면 리소스에서 MSI를 재배포하거나 배포 오류 관련 문제를 해결해야 할 수 있습니다.

### <a name="http-request-error-responses"></a>HTTP 요청 오류 응답

- *bad_request_102: 필수 메타데이터 헤더가 지정되지 않음*

  `Metadata` 요청 헤더 필드가 요청에서 누락되거나 형식이 잘못되었습니다. 값은 모두 소문자이며 `true`으로 지정해야 합니다. 예제는 [이전 REST 섹션](#rest)에서 "샘플 요청"을 참조하세요.

- *알 수 없음: Active directory에서 토큰을 검색하지 못했습니다. 자세한 내용은 \<파일 경로\>*에서 로그를 참조하세요.

  HTTP GET 요청 URI의 형식, 특히 쿼리 문자열에서 지정된 리소스 URI가 올바르게 지정되었는지 확인합니다. 예제는 [이전 REST 섹션](#rest)에서 "샘플 요청"을 참조하세요. 또는 서비스 및 각 리소스 ID의 목록은 [Azure AD 인증을 지원하는 Azure 서비스](msi-overview.md#azure-services-that-support-azure-ad-authentication)를 참조하세요.

- *unknown_source: 알 수 없는 소스 \<URI\>*

  HTTP GET 요청 URI의 형식이 올바른지 확인합니다. `scheme:host/resource-path` 부분은 `http://localhost:50342/oauth2/token`으로 지정해야 합니다. 예제는 [이전 REST 섹션](#rest)에서 "샘플 요청"을 참조하세요.

## <a name="related-content"></a>관련 콘텐츠

- MSI의 개요는 [관리 서비스 ID 개요](msi-overview.md)를 참조하세요.
- Azure VM에서 MSI를 사용하도록 설정하려면 [PowerShell을 사용하여 Azure VM MSI(관리 서비스 ID) 구성](msi-qs-configure-powershell-windows-vm.md)을 참조하세요.

다음 설명 섹션을 사용하여 피드백을 제공하고 콘텐츠를 구체화하고 모양을 갖출 수 있습니다.

