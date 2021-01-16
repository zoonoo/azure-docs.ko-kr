---
title: Azure SQL 논리 서버에 대 한 Azure 증명 구성
description: Azure SQL Database에서 secure enclaves를 사용 하 여 Always Encrypted에 대 한 Azure 증명을 구성 합니다.
keywords: 데이터 암호화, sql 암호화, 데이터베이스 암호화, 중요 한 데이터, Always Encrypted, secure enclaves, SGX, 증명
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: e8cb423d4d700c4b6b6caa30a02eac3e7ef10cb6
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253477"
---
# <a name="configure-azure-attestation-for-your-azure-sql-logical-server"></a>Azure SQL 논리 서버에 대 한 Azure 증명 구성

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted secure enclaves for Azure SQL Database는 현재 **공개 미리 보기로** 제공 됩니다.

[Microsoft Azure 증명](../../attestation/overview.md) 은 intel SGX (Software Guard Extensions) enclaves를 포함 하 여 TEEs (증명 Trusted Execution environment)를 위한 솔루션입니다. 

Azure SQL Database에서 [보안 enclaves의 Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-enclaves) 에 사용 되는 증명 Intel SGX enclaves에 대해 Azure 증명을 사용 하려면 다음을 수행 해야 합니다.

1. [증명 공급자](../../attestation/basic-concepts.md#attestation-provider) 를 만들고 권장 증명 정책으로 구성 합니다.

2. Azure SQL 논리 서버에 증명 공급자에 대 한 액세스 권한을 부여 합니다.

> [!NOTE]
> 증명을 구성 하는 것은 증명 관리자의 책임입니다. [SGX enclaves 및 증명을 구성할 때의 역할 및 책임](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation)을 참조 하세요.

## <a name="requirements"></a>요구 사항

Azure SQL 논리 서버와 증명 공급자는 동일한 Azure Active Directory 테 넌 트에 속해야 합니다. 교차 테 넌 트 상호 작용은 지원 되지 않습니다. 

Azure SQL 논리 서버에 Azure AD id가 할당 되어 있어야 합니다. 증명 관리자는 해당 서버에 대 한 Azure SQL Database 관리자 로부터 서버의 Azure AD id를 가져와야 합니다. Id를 사용 하 여 서버에 증명 공급자에 대 한 액세스 권한을 부여 합니다. 

Id를 사용 하 여 서버를 만들거나 PowerShell 및 Azure CLI를 사용 하 여 기존 서버에 id를 할당 하는 방법에 대 한 지침은 [서버에 AZURE AD Id 할당](transparent-data-encryption-byok-configure.md#assign-an-azure-active-directory-azure-ad-identity-to-your-server)을 참조 하세요.

## <a name="create-and-configure-an-attestation-provider"></a>증명 공급자 만들기 및 구성

[증명 공급자](../../attestation/basic-concepts.md#attestation-provider) 는 증명 [정책](../../attestation/basic-concepts.md#attestation-request) 에 대해 증명 [요청](../../attestation/basic-concepts.md#attestation-request) 을 평가 하 고 [증명 토큰](../../attestation/basic-concepts.md#attestation-token)을 발급 하는 Azure 증명의 리소스입니다. 

증명 정책은 [클레임 규칙 문법을](../../attestation/claim-rule-grammar.md)사용 하 여 지정 됩니다.

Azure SQL Database에서 Always Encrypted에 사용 되는 증명 Intel SGX enclaves에 대해 다음 정책을 권장 합니다.

```output
version= 1.0;
authorizationrules 
{
       [ type=="x-ms-sgx-is-debuggable", value==false ]
        && [ type=="x-ms-sgx-product-id", value==4639 ]
        && [ type=="x-ms-sgx-svn", value>= 0 ]
        && [ type=="x-ms-sgx-mrsigner", value=="e31c9e505f37a58de09335075fc8591254313eb20bb1a27e5443cc450b6e33e5"] 
    => permit();
};
```

위의 정책은 다음을 확인 합니다.

- Azure SQL Database 내부의 enclave는 디버깅을 지원 하지 않습니다 .이는 enclave가 제공 하는 보호 수준을 줄입니다.
- Enclave 내 라이브러리의 제품 ID는 보안 enclaves (4639)를 사용 하 여 Always Encrypted에 할당 된 제품 ID입니다.
- 라이브러리의 svn (버전 ID)이 0 보다 큽니다.
- Enclave의 라이브러리가 Microsoft 서명 키를 사용 하 여 서명 되었습니다. mrsigner 클레임의 값은 서명 키의 해시입니다.

> [!IMPORTANT]
> Enclave 내에서 실행 되는 코드의 유효성을 검사 하지 않는 Intel SGX enclaves에 대 한 기본 정책을 사용 하 여 증명 공급자를 만듭니다. Microsoft는 보안 enclaves을 사용 하는 Always Encrypted에 대해 위의 권장 정책을 설정 하 고 기본 정책은 사용 하지 않는 것을 강력 하 게 권고 합니다.

증명 공급자를 만들고를 사용 하 여 증명 정책을 구성 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

- [빠른 시작: Azure Portal을 사용 하 여 Azure 증명 설정](../../attestation/quickstart-portal.md)
    > [!IMPORTANT]
    > Azure Portal를 사용 하 여 증명 정책을 구성 하는 경우 증명 형식을로 설정 `SGX-IntelSDK` 합니다.
- [빠른 시작: Azure PowerShell을 사용하여 Azure Attestation 설정](../../attestation/quickstart-powershell.md)
    > [!IMPORTANT]
    > Azure PowerShell를 사용 하 여 증명 정책을 구성 하는 경우 `Tee` 매개 변수를로 설정 `SgxEnclave` 합니다.
- [빠른 시작: Azure CLI를 사용하여 Azure Attestation 설정](../../attestation/quickstart-azure-cli.md)
    > [!IMPORTANT]
    > Azure CLI를 사용 하 여 증명 정책을 구성 하는 경우 `attestation-type` 매개 변수를로 설정 `SGX-IntelSDK` 합니다.

## <a name="determine-the-attestation-url-for-your-attestation-policy"></a>증명 정책에 대 한 증명 URL 확인

증명 정책을 구성한 후에는 보안 enclaves Azure SQL Database에서 Always Encrypted를 사용 하는 응용 프로그램의 정책, 정책 참조를 참조 하는 증명 URL을 공유 해야 합니다. 응용 프로그램 관리자 또는/및 응용 프로그램 사용자는 보안 enclaves를 사용 하는 문을 실행할 수 있도록 증명 URL을 사용 하 여 앱을 구성 해야 합니다.

### <a name="use-powershell-to-determine-the-attestation-url"></a>PowerShell을 사용 하 여 증명 URL 확인

다음 스크립트를 사용 하 여 증명 URL을 결정 합니다.

```powershell
$attestationProvider = Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName 
$attestationUrl = $attestationProvider.AttestUri + “/attest/SgxEnclave”
Write-Host "Your attestation URL is: " $attestationUrl 
```

### <a name="use-azure-portal-to-determine-the-attestation-url"></a>Azure Portal를 사용 하 여 증명 URL 확인

1. 증명 공급자의 개요 창에서 증명 URI 속성의 값을 클립보드에 복사 합니다. 증명 URI는와 같이 표시 됩니다 `https://MyAttestationProvider.us.attest.azure.net` .

2. 증명 URI:에 다음을 추가 합니다 `/attest/SgxEnclave` . 

결과 증명 URL은 다음과 같습니다. `https://MyAttestationProvider.us.attest.azure.net/attest/SgxEnclave`

## <a name="grant-your-azure-sql-logical-server-access-to-your-attestation-provider"></a>증명 공급자에 대 한 Azure SQL 논리 서버 액세스 권한 부여

증명 워크플로 중에 데이터베이스를 포함 하는 Azure SQL 논리 서버는 증명 공급자를 호출 하 여 증명 요청을 제출 합니다. Azure SQL 논리 서버가 증명 요청을 제출할 수 있도록 하려면 서버에 증명 공급자에 대 한 작업에 대 한 권한이 있어야 합니다 `Microsoft.Attestation/attestationProviders/attestation/read` . 사용 권한을 부여 하는 권장 방법은 증명 공급자의 관리자가 증명 공급자 또는 포함 하는 리소스 그룹의 증명 판독기 역할에 서버의 Azure AD id를 할당 하는 것입니다.

### <a name="use-azure-portal-to-assign-permission"></a>Azure Portal를 사용 하 여 사용 권한 할당

증명 공급자의 증명 판독기 역할에 Azure SQL server의 id를 할당 하려면 [Azure Portal을 사용 하 여 azure 역할 할당 추가 또는 제거](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)의 일반 지침을 따릅니다. **역할 할당 추가** 창에 있는 경우:

1. **역할** 드롭다운에서 **증명 판독기** 역할을 선택 합니다.
1. **Select** 필드에서 검색할 Azure SQL server의 이름을 입력 합니다.

예제는 아래 스크린샷을 참조 하세요.

![증명 판독기 역할 할당](./media/always-encrypted-enclaves/attestation-provider-role-assigment.png)

> [!NOTE]
> **역할 할당 추가** 창에 서버를 표시 하려면 서버에 Azure AD id가 할당 되어 있어야 합니다. [요구 사항](#requirements)을 참조 하세요.

### <a name="use-powershell-to-assign-permission"></a>PowerShell을 사용 하 여 사용 권한 할당

1. Azure SQL 논리 서버를 찾습니다.

```powershell
$serverResourceGroupName = "<server resource group name>"
$serverName = "<server name>" 
$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName
```
 
2. 증명 공급자를 포함 하는 리소스 그룹의 증명 판독기 역할에 서버를 할당 합니다.

```powershell
$attestationResourceGroupName = "<attestation provider resource group name>"
New-AzRoleAssignment -ObjectId $server.Identity.PrincipalId -RoleDefinitionName "Attestation Reader" -ResourceGroupName $attestationResourceGroupName
```

자세한 내용은 [Azure PowerShell를 사용 하 여 Azure 역할 할당 추가 또는 제거](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell#add-a-role-assignment)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [보안 Enclave를 사용한 Always Encrypted 키 관리](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-enclaves-manage-keys)

## <a name="see-also"></a>참고 항목

- [자습서: Azure SQL Database에서 secure enclaves를 사용 하 여 Always Encrypted 시작](always-encrypted-enclaves-getting-started.md)