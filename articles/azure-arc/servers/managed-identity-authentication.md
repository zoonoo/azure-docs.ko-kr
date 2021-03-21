---
title: Arc 사용 서버를 사용 하 여 Azure 리소스에 대해 인증
description: 이 문서에서는 Arc 사용 서버에 대 한 Azure Instance Metadata Service 지원 및 암호를 사용 하 여 Azure 리소스 및 로컬에 대해 인증할 수 있는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 49b70928ae972da8e0a0d14d711e4b6f246cca6a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96939157"
---
# <a name="authenticate-against-azure-resources-with-arc-enabled-servers"></a>Arc 사용 서버를 사용 하 여 Azure 리소스에 대해 인증

Azure Arc 사용 서버에서 직접 실행 되는 응용 프로그램 또는 프로세스는 관리 되는 id를 활용 하 여 Azure Active Directory 기반 인증을 지 원하는 다른 Azure 리소스에 액세스할 수 있습니다. 응용 프로그램은 Arc 사용 서버에 대해 시스템에서 할당 된 id를 나타내는 [액세스 토큰](../../active-directory/develop/developer-glossary.md#access-token) 을 가져올 수 있으며,이 토큰을 ' 전달자 ' 토큰으로 사용 하 여 다른 서비스에 자신을 인증할 수 있습니다.

관리 되는 id에 대 한 자세한 설명 및 시스템 할당 id와 사용자 할당 id를 구분 하는 방법은 [관리 id 개요](../../active-directory/managed-identities-azure-resources/overview.md) 설명서를 참조 하세요.

이 문서에서는 서버에서 시스템 할당 관리 id를 사용 하 여 Azure [Key Vault](../../key-vault/general/overview.md)에 액세스 하는 방법을 보여 줍니다. 부트스트랩 역할을 하는 Key Vault를 사용하면 클라이언트 애플리케이션에서 비밀을 사용하여 Azure AD(Active Directory)로 보호되지 않는 리소스에 액세스할 수 있습니다. 예를 들어 IIS 웹 서버에서 사용 되는 TLS/SSL 인증서를 Azure Key Vault에 저장 하 고, Azure 외부의 Windows 또는 Linux 서버에 인증서를 안전 하 게 배포할 수 있습니다.

## <a name="security-overview"></a>보안 개요

서버를 Azure Arc 사용 서버에 등록 하는 동안 Azure VM에 대해 수행 되는 것과 비슷한 방식으로 관리 되는 id를 사용 하 여 구성 하기 위해 몇 가지 작업이 수행 됩니다.

- Azure Resource Manager는 Arc 사용 서버에서 시스템 할당 관리 id를 사용 하도록 설정 하는 요청을 받습니다.

- Azure Resource Manager는 Azure AD에서 서버 id에 대 한 서비스 주체를 만듭니다. 서비스 주체는 구독이 신뢰하는 Azure AD 테넌트에 생성됩니다.

- Azure Resource Manager은 서비스 주체 클라이언트 ID 및 인증서를 사용 하 여 [Windows](../../virtual-machines/windows/instance-metadata-service.md) 또는 [Linux](../../virtual-machines/linux/instance-metadata-service.md) 용 AZURE Instance Metadata Service (imds) id 끝점을 업데이트 하 여 서버에서 id를 구성 합니다. 끝점은 잘 알려진 라우팅할 수 없는 IP 주소를 사용 하 여 서버 내 에서만 액세스할 수 있는 REST 끝점입니다. 이 서비스는 Arc 사용 서버에 대 한 메타 데이터 정보의 하위 집합을 제공 하 여 관리 및 구성을 지원 합니다.

Windows Arc 사용 서버에서 다음 변수를 사용 하 여 관리 되는 id 사용 서버의 환경이 구성 됩니다.

- **IMDS_ENDPOINT**: `http://localhost:40342` Arc 사용 서버에 대 한 IMDS 끝점 IP 주소입니다.

- **IDENTITY_ENDPOINT**: 서비스의 관리 id에 해당 하는 localhost 끝점 `http://localhost:40342/metadata/identity/oauth2/token` 입니다.

서버에서 실행 되는 코드는 Azure 인스턴스 메타 데이터 서비스 끝점에서 토큰을 요청할 수 있으며 서버 내 에서만 액세스할 수 있습니다.

시스템 환경 변수 **IDENTITY_ENDPOINT** 는 응용 프로그램에서 id 끝점을 검색 하는 데 사용 됩니다. 응용 프로그램은 **IDENTITY_ENDPOINT** 및 **IMDS_ENDPOINT** 값을 검색 하 여 사용 해야 합니다. 모든 액세스 수준의 응용 프로그램은 끝점에 대 한 요청을 수행할 수 있습니다. 메타 데이터 응답은 정상적으로 처리 되 고 컴퓨터의 모든 프로세스에 제공 됩니다. 그러나 토큰을 노출 하는 요청이 이루어지면 클라이언트는 더 높은 권한 있는 사용자만 사용할 수 있는 데이터에 액세스할 수 있음을 증명 하는 암호를 제공 해야 합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 관리 ID에 대한 이해.
- 서버를 Arc 사용 서버에 연결 하 고 등록 했습니다.
- 필요한 리소스 생성 및 역할 관리 단계를 수행 하기 위해 구독 또는 리소스 그룹의 [소유자 그룹](../../role-based-access-control/built-in-roles.md#owner)* *의 멤버입니다.
- 자격 증명을 저장 하 고 검색 하 Azure Key Vault입니다. Azure Arc id 액세스 권한을 KeyVault에 할당 합니다.

    - Key Vault 생성 되지 않은 경우 [Key Vault 만들기](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#create-a-key-vault-)를 참조 하세요.
    - 서버에서 사용 하는 관리 id를 사용 하 여 액세스를 구성 하려면 [Linux에 대 한 액세스 권한 부여](../../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-nonaad.md#grant-access) 또는 [Windows에 대 한 액세스](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#grant-access)권한 부여를 참조 하세요. 5 단계에 대해 Arc 사용 서버 이름을 입력 합니다. PowerShell을 사용 하 여이를 완료 하려면 [powershell을 사용 하 여 액세스 정책 할당](../../key-vault/general/assign-access-policy-powershell.md)을 참조 하세요.

## <a name="acquiring-an-access-token-using-rest-api"></a>REST API를 사용 하 여 액세스 토큰 가져오기

Azure 리소스를 인증 하기 위해 시스템 할당 관리 id를 가져오고 사용 하는 방법은 Azure VM에서 수행 하는 방법과 비슷합니다.

Arc를 사용 하는 Windows server의 경우 PowerShell을 사용 하 여 웹 요청을 호출 하 여 특정 포트의 로컬 호스트에서 토큰을 가져옵니다. IP 주소 또는 환경 변수 **IDENTITY_ENDPOINT** 를 사용 하 여 요청을 지정 합니다.

```powershell
$apiVersion = "2020-06-01"
$resource = "https://management.azure.com/"
$endpoint = "{0}?resource={1}&api-version={2}" -f $env:IDENTITY_ENDPOINT,$resource,$apiVersion
$secretFile = ""
try
{
    Invoke-WebRequest -Method GET -Uri $endpoint -Headers @{Metadata='True'} -UseBasicParsing
}
catch
{
    $wwwAuthHeader = $_.Exception.Response.Headers["WWW-Authenticate"]
    if ($wwwAuthHeader -match "Basic realm=.+")
    {
        $secretFile = ($wwwAuthHeader -split "Basic realm=")[1]
    }
}
Write-Host "Secret file path: " $secretFile`n
$secret = cat -Raw $secretFile
$response = Invoke-WebRequest -Method GET -Uri $endpoint -Headers @{Metadata='True'; Authorization="Basic $secret"} -UseBasicParsing
if ($response)
{
    $token = (ConvertFrom-Json -InputObject $response.Content).access_token
    Write-Host "Access token: " $token
}
```

다음 응답은 반환 되는 예입니다.

:::image type="content" source="media/managed-identity-authentication/powershell-token-output-example.png" alt-text="PowerShell을 사용 하 여 액세스 토큰을 성공적으로 검색 했습니다.":::

Bash를 사용 하는 Linux 서버의 경우 Bash를 사용 하 여 웹 요청을 호출 하 여 특정 포트의 로컬 호스트에서 토큰을 가져옵니다. **IDENTITY_ENDPOINT** IP 주소 또는 환경 변수를 사용 하 여 다음 요청을 지정 합니다. 이 단계를 완료 하려면 SSH 클라이언트가 필요 합니다.

```bash
ChallengeTokenPath=$(curl -s -D - -H Metadata:true "http://127.0.0.1:40342/metadata/identity/oauth2/token?api-version=2019-11-01&resource=https%3A%2F%2Fmanagement.azure.com" | grep Www-Authenticate | cut -d "=" -f 2 | tr -d "[:cntrl:]")
ChallengeToken=$(cat $ChallengeTokenPath)
if [ $? -ne 0 ]; then
    echo "Could not retrieve challenge token, double check that this command is run with root privileges."
else
    curl -s -H Metadata:true -H "Authorization: Basic $ChallengeToken" "http://127.0.0.1:40342/metadata/identity/oauth2/token?api-version=2019-11-01&resource=https%3A%2F%2Fmanagement.azure.com"
fi
```

다음 응답은 반환 되는 예입니다.

:::image type="content" source="media/managed-identity-authentication/bash-token-output-example.png" alt-text="Bash를 사용 하 여 액세스 토큰을 성공적으로 검색 했습니다.":::

응답에는 Azure의 모든 리소스에 액세스 하는 데 필요한 액세스 토큰이 포함 되어 있습니다. Azure Key Vault을 인증 하는 구성을 완료 하려면 [Windows를 사용 하 여 Key Vault 액세스](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md#access-data) 또는 [Linux로 Key Vault 액세스](../../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-nonaad.md#access-data)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- Azure Key Vault에 대 한 자세한 내용은 [Key Vault 개요](../../key-vault/general/overview.md)를 참조 하세요.

- [PowerShell을 사용](../../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) 하거나 [Azure CLI](../../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)를 사용 하 여 리소스에 관리 id 액세스 권한을 할당 하는 방법에 대해 알아봅니다.