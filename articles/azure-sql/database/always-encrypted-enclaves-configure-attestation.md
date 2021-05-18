---
title: Azure SQL 논리 서버의 Azure Attestation 구성
description: Azure SQL Database에서 보안 enclave를 사용하여 Always Encrypted에 대한 Azure Attestation을 구성합니다.
keywords: 데이터 암호화, SQL 암호화, 데이터베이스 암호화, 중요한 데이터, Always Encrypted, 보안 enclave, SGX, 증명
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: a51aa15e1338380d4b4179e7fb8899273750c374
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106107183"
---
# <a name="configure-azure-attestation-for-your-azure-sql-logical-server"></a>Azure SQL 논리 서버의 Azure Attestation 구성

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL Database용 보안 enclave를 사용한 Always Encrypted는 현재 **공개 미리 보기** 상태입니다.

[Microsoft Azure Attestation](../../attestation/overview.md)은 Intel Software Guard Extensions(Intel SGX) enclave를 포함하는 TEE(신뢰 실행 환경) 증명을 위한 솔루션입니다. 

Azure SQL Database에서 [보안 enclave를 사용한 Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-enclaves)에 사용되는 증명 Intel SGX enclave에 대해 Azure Attestation을 사용하려면 다음을 수행해야 합니다.

1. [증명 공급자](../../attestation/basic-concepts.md#attestation-provider)를 만들고 권장 증명 정책으로 구성합니다.

2. Azure SQL 논리 서버에 증명 공급자에 대한 액세스 권한을 부여합니다.

> [!NOTE]
> 증명을 구성하는 것은 증명 관리자의 책임입니다. [SGX enclave 및 증명을 구성할 때의 역할 및 책임](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation)을 참조하세요.

## <a name="requirements"></a>요구 사항

Azure SQL 논리 서버와 증명 공급자는 동일한 Azure Active Directory 테넌트에 속해야 합니다. 테넌트 간 상호 작용은 지원되지 않습니다. 

Azure SQL 논리 서버에 Azure AD ID가 할당되어 있어야 합니다. 증명 관리자는 해당 서버의 Azure SQL Database 관리자로부터 서버의 Azure AD ID를 얻어야 합니다. ID를 사용하여 증명 공급자에 대한 서버 액세스 권한을 부여합니다. 

PowerShell 및 Azure CLI를 사용하여 ID가 있는 서버를 만들거나 기존 서버에 ID를 할당하는 방법에 대한 자세한 내용은 [서버에 Azure AD ID 할당](transparent-data-encryption-byok-configure.md#assign-an-azure-active-directory-azure-ad-identity-to-your-server)을 참조하세요.

## <a name="create-and-configure-an-attestation-provider"></a>증명 공급자 만들기 및 구성

[증명 공급자](../../attestation/basic-concepts.md#attestation-provider)는 [증명 정책](../../attestation/basic-concepts.md#attestation-request)에 대해 [증명 요청](../../attestation/basic-concepts.md#attestation-request)을 평가하고 [증명 토큰](../../attestation/basic-concepts.md#attestation-token)을 발급하는 Azure Attestation의 리소스입니다. 

증명 정책은 [클레임 규칙 문법](../../attestation/claim-rule-grammar.md)을 사용하여 지정됩니다.

Azure SQL Database에서 Always Encrypted에 사용되는 증명 Intel SGX enclave에 대해 다음 정책을 권장합니다.

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

위의 정책은 다음을 확인합니다.

- Azure SQL Database 내부의 enclave는 디버깅을 지원하지 않습니다. 
  > enclave는 디버깅을 사용하지 않도록 또는 사용하도록 설정하여 로드할 수 있습니다. 디버깅 지원은 개발자가 enclave에서 실행되는 코드의 문제를 해결할 수 있도록 설계되었습니다. 프로덕션 시스템에서 디버깅을 사용하면 관리자가 enclave의 콘텐츠를 검사하여 enclave가 제공하는 보호 수준을 낮출 수 있습니다. 권장되는 정책은 악의적인 관리자가 enclave 컴퓨터를 넘겨받아 디버깅 지원을 켜려고 하면 증명에 실패하도록 디버깅을 사용하지 않도록 설정하는 것입니다. 
- enclave의 제품 ID는 Always Encrypted에 할당된 제품 ID와 보안 enclave가 일치합니다.
  > 각 enclave에는 다른 enclave의 enclave를 차별화하는 고유한 제품 ID가 있습니다. Always Encrypted enclave에 할당된 제품 ID는 4639입니다. 
- 라이브러리의 SVN(보안 버전 번호)이 0보다 큽니다.
  > SVN을 통해 Microsoft는 enclave 코드에서 식별된 잠재적 보안 버그에 응답할 수 있습니다. 보안 문제가 발견되고 해결되는 경우 Microsoft는 새(증분) SVN이 포함된 새 버전의 enclave를 배포합니다. 위의 권장 정책은 새 SVN을 반영하도록 업데이트됩니다. 권장 정책과 일치하도록 정책을 업데이트하면 악의적인 관리자가 오래되고 안전하지 않은 enclave를 로드하려고 하면 증명이 실패합니다.
- enclave의 라이브러리는 Microsoft 서명 키(x-ms-sgx-mrsigner 클레임의 값은 서명 키의 해시)를 사용하여 서명되었습니다.
  > 증명의 주요 목표 중 하나는 enclave에서 실행 중인 이진이 실행되어야 하는 이진임을 클라이언트에게 확신시키는 것입니다. 증명 정책은 이러한 용도로 두 가지 메커니즘을 제공합니다. 하나는 enclave에서 실행되어야 하는 이진의 해시인 **mrenclave** 클레임입니다. **mrenclave** 의 문제는 이진 해시가 코드의 사소한 변경에도 불구하고 변경되기 때문에 enclave에서 실행 중인 코드를 수정하기가 어렵다는 것입니다. 따라서 enclave 이진에 서명하는 데 사용되는 키의 해시인 **mrsigner** 를 사용하는 것이 좋습니다. Microsoft가 enclave를 수정할 때 서명 키가 변경되지 않는 한 **mrsigner** 가 동일하게 유지됩니다. 이러한 방식으로 고객의 애플리케이션을 중단하지 않고 업데이트된 이진을 배포하는 것이 실현됩니다. 

> [!IMPORTANT]
> 증명 공급자는 Intel SGX enclave에 대한 기본 정책으로 생성되며, 이 정책은 enclave 내에서 실행되는 코드의 유효성을 검사하지 않습니다. Microsoft는 보안 enclave를 사용하는 Always Encrypted에 대해 위의 권장 정책을 설정하고 기본 정책은 사용하지 않는 것을 강력하게 권고합니다.

다음을 사용하여 증명 공급자를 만들고 증명 정책을 구성하는 방법에 대한 자세한 내용을 참조하세요.

- [빠른 시작: Azure Portal을 사용하여 Azure Attestation 설정](../../attestation/quickstart-portal.md)
    > [!IMPORTANT]
    > Azure Portal을 사용하여 증명 정책을 구성하는 경우 증명 형식을 `SGX-IntelSDK`로 설정합니다.
- [빠른 시작: Azure PowerShell을 사용하여 Azure Attestation 설정](../../attestation/quickstart-powershell.md)
    > [!IMPORTANT]
    > Azure PowerShell를 사용하여 증명 정책을 구성하는 경우 `Tee` 매개 변수를 `SgxEnclave`로 설정합니다.
- [빠른 시작: Azure CLI를 사용하여 Azure Attestation 설정](../../attestation/quickstart-azure-cli.md)
    > [!IMPORTANT]
    > Azure CLI를 사용하여 증명 정책을 구성하는 경우 `attestation-type` 매개 변수를 `SGX-IntelSDK`로 설정합니다.

## <a name="determine-the-attestation-url-for-your-attestation-policy"></a>증명 정책의 증명 URL 확인

증명 정책을 구성한 후에는 Azure SQL Database의 보안 enclave를 사용하여 Always Encrypted를 사용하는 애플리케이션의 관리자를 참조하는 증명 URL을 공유해야 합니다. 애플리케이션 관리자 또는/및 애플리케이션 사용자는 보안 enclave를 사용하는 문을 실행할 수 있도록 증명 URL을 사용하여 앱을 구성해야 합니다.

### <a name="use-powershell-to-determine-the-attestation-url"></a>PowerShell을 사용하여 증명 URL 확인

다음 스크립트를 사용하여 증명 URL을 확인합니다.

```powershell
$attestationProvider = Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName 
$attestationUrl = $attestationProvider.AttestUri + "/attest/SgxEnclave"
Write-Host "Your attestation URL is: " $attestationUrl 
```

### <a name="use-azure-portal-to-determine-the-attestation-url"></a>Azure Portal을 사용하여 증명 URL 확인

1. 증명 공급자의 개요 창에서 증명 URI 속성의 값을 클립보드에 복사합니다. 증명 URI는 다음과 같이 표시됩니다. `https://MyAttestationProvider.us.attest.azure.net`.

2. 증명 URI에 다음을 추가합니다. `/attest/SgxEnclave`. 

결과 증명 URL은 다음과 같습니다. `https://MyAttestationProvider.us.attest.azure.net/attest/SgxEnclave`

## <a name="grant-your-azure-sql-logical-server-access-to-your-attestation-provider"></a>Azure SQL 논리 서버에 증명 공급자에 대한 액세스 권한 부여

증명 워크플로 중에 데이터베이스를 포함하는 Azure SQL 논리 서버는 증명 공급자를 호출하여 증명 요청을 제출합니다. Azure SQL 논리 서버가 증명 요청을 제출할 수 있도록 하려면 서버에 증명 공급자의 `Microsoft.Attestation/attestationProviders/attestation/read` 작업에 대한 권한이 있어야 합니다. 권한을 부여하는 권장 방법은 증명 공급자의 관리자가 서버의 Azure AD ID를 증명 공급자 또는 해당 리소스 그룹의 증명 판독기 역할에 할당하는 것입니다.

### <a name="use-azure-portal-to-assign-permission"></a>Azure Portal을 사용하여 권한 할당

증명 공급자의 증명 판독기 역할에 Azure SQL 서버의 ID를 할당하려면 [Azure Portal을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)의 일반 지침을 수행합니다. **역할 할당 추가** 창에 있는 경우:

1. **역할** 드롭다운에서 **증명 판독기** 역할을 선택합니다.
1. **선택** 필드에 검색할 Azure SQL 서버의 이름을 입력합니다.

예제를 보려면 아래 스크린샷을 참조하세요.

![증명 판독기 역할 할당](./media/always-encrypted-enclaves/attestation-provider-role-assigment.png)

> [!NOTE]
> **역할 할당 추가** 창에 서버를 표시하려면 서버에 Azure AD ID가 할당되어 있어야 합니다. [요구 사항](#requirements)을 참조하세요.

### <a name="use-powershell-to-assign-permission"></a>PowerShell을 사용하여 권한 할당

1. Azure SQL 논리 서버를 찾습니다.

```powershell
$serverResourceGroupName = "<server resource group name>"
$serverName = "<server name>" 
$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $serverResourceGroupName 
```
 
2. 증명 공급자를 포함하는 리소스 그룹의 증명 판독기 역할에 서버를 할당합니다.

```powershell
$attestationResourceGroupName = "<attestation provider resource group name>"
New-AzRoleAssignment -ObjectId $server.Identity.PrincipalId -RoleDefinitionName "Attestation Reader" -ResourceGroupName $attestationResourceGroupName
```

자세한 내용은 [Azure PowerShell을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-powershell.md#assign-role-examples)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [보안 Enclave를 사용한 Always Encrypted 키 관리](/sql/relational-databases/security/encryption/always-encrypted-enclaves-manage-keys)

## <a name="see-also"></a>참고 항목

- [자습서: Azure SQL Database의 보안 Enclave를 사용한 Always Encrypted 시작](always-encrypted-enclaves-getting-started.md)
