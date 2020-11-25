---
title: Azure Attestation 문제 해결 가이드
description: 자주 관찰 되는 문제에 대 한 문제 해결 가이드
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: fe347983e2143c7a4bdf00ca0207356c881c66ac
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95891285"
---
# <a name="microsoft-azure-attestation-troubleshooting-guide"></a>Microsoft Azure 증명 문제 해결 가이드

Azure 증명의 오류 처리는 [Microsoft REST API 지침](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#7102-error-condition-responses)에 따라 구현 됩니다. Azure 증명 Api에서 반환 하는 오류 응답에는 이름이 "code" 및 "message" 인 HTTP 상태 코드 및 이름/값 쌍이 포함 되어 있습니다. "코드"의 값은 사람이 읽을 수 있으며 오류 유형을 나타내는 표시기입니다. "메시지" 값은 사용자에 게 도움을 주는 것 이며 오류 정보를 제공 합니다.

이 문서에서 문제가 해결 되지 않으면 azure [지원 페이지](https://azure.microsoft.com/support/options/)에서 azure 지원 요청을 제출할 수도 있습니다.

Azure 증명에서 반환 되는 오류에 대 한 몇 가지 예는 다음과 같습니다.

## <a name="1-http401--unauthorized-exception"></a>1. HTTP – 401: 권한이 없는 예외

### <a name="http-status-code"></a>HTTP 상태 코드
401

**오류 코드** 상태로

**시나리오 예**
  - 사용자가 증명 판독기 역할로 할당 되지 않은 경우 증명 실패
  - 사용자가 적절 한 역할을 사용 하 여 할당 되지 않았으므로 증명 정책을 관리할 수 없습니다.
  - 사용자가 적절 한 역할을 사용 하 여 할당 되지 않았으므로 증명 정책 서명자를 관리할 수 없습니다.

PowerShell에서 증명 정책을 편집 하려고 하는 읽기 권한자 역할의 사용자 

  ```powershell
  Set-AzAttestationPolicy : Operation returned HTTP Status Code 401
At line:1 char:1
+ Set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
  ```

**문제 해결 단계**

증명 정책/정책 서명자를 보려면 Azure AD 사용자에 게 "작업"에 대 한 권한이 필요 합니다.
- Microsoft.Attestation/attestationProviders/attestation/read

  이 권한은 "소유자" (와일드 카드 권한) 또는 "판독기" (와일드 카드 권한) 또는 "증명 판독기" (Azure 증명에 대 한 특정 사용 권한)와 같은 역할을 통해 AD 사용자에 게 할당할 수 있습니다.

정책 서명자를 추가/삭제 하거나 정책을 구성 하기 위해 Azure AD 사용자에 게 "작업"에 대 한 다음 권한이 필요 합니다.
- Microsoft.Attestation/attestationProviders/attestation/write
- Microsoft.Attestation/attestationProviders/attestation/delete

  이러한 권한은 "소유자" (와일드 카드 권한), "참가자" (와일드 카드 권한) 또는 "증명 참여자" (Azure 증명에 대 한 특정 사용 권한)와 같은 역할을 통해 AD 사용자에 게 할당할 수 있습니다.

고객은 기본 공급자를 증명에 사용하거나 사용자 지정 정책을 사용하여 자체 공급자를 만들 수 있습니다. 사용자 지정 증명 공급자에 게 증명 요청을 보내려면 사용자에 게 "소유자" (와일드 카드 권한) 또는 "판독기" (와일드 카드 권한) 또는 "증명 판독기" 역할이 필요 합니다. 모든 Azure AD 사용자가 기본 공급자에 액세스할 수 있습니다.

PowerShell에서 역할을 확인 하려면 다음을 실행 합니다.

a. PowerShell을 시작 하 고 "AzAccount" cmdlet을 통해 Azure에 로그인 합니다.

b. Azure 역할 할당 설정 확인


  ```powershell
  $c = Get-AzContext
  Get-AzRoleAssignment -ResourceGroupName $attestationResourceGroup -ResourceName $attestationProvider -ResourceType Microsoft.Attestation/attestationProviders -SignInName $c.Account.Id
  ```

  다음과 비슷한 결과가 표시됩니다.

  ```
  RoleAssignmentId   :/subscriptions/subscriptionId/providers/Microsoft.Authorization/roleAssignments/roleAssignmentId
  
  Scope              : /subscriptions/subscriptionId
  
  DisplayName        : displayName
  
  SignInName         : signInName
  
  RoleDefinitionName : Reader
  
  RoleDefinitionId   : roleDefinitionId
  
  ObjectId           : objectid
  
  ObjectType         : User
  
  CanDelegate        : False
 
  ```

다. 목록에서 적절 한 역할 할당을 찾지 못한 경우 [여기](/azure/role-based-access-control/role-assignments-powershell) 에 있는 지침을 따르세요.

## <a name="2-http--400-errors"></a>2. HTTP-400 오류

### <a name="http-status-code"></a>HTTP 상태 코드
400

요청에서 400를 반환할 수 있는 이유에는 여러 가지가 있습니다. 다음은 Azure 증명 Api에서 반환 되는 오류의 몇 가지 예입니다.

### <a name="21-attestation-failure-due-to-policy-evaluation-errors"></a>2.1. 정책 평가 오류로 인 한 증명 실패

증명 정책에는 권한 부여 규칙과 발급 규칙이 포함 되어 있습니다. Enclave 증명 정보는 권한 부여 규칙을 기반으로 평가 됩니다. 발급 규칙은 증명 토큰에 포함 될 클레임을 정의 합니다. Enclave 증명의 클레임이 권한 부여 규칙을 준수 하지 않는 경우에는 증명 호출 시 정책 평가 오류가 반환 됩니다. 

**오류 코드** Polica 오류

**시나리오 예** Enclave 견적의 클레임이 증명 정책의 권한 부여 규칙과 일치 하지 않는 경우

```
Native operation failed with 65518: G:\Az\security\Attestation\src\AttestationServices\Instance\NativePolicyWrapper\NativePolicyEngine.cpp(168)\(null)!00007FF801762308: (caller: 00007FF80143DCC8) Exception(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Policy Engine Exception: A Deny claim was issued, authorization failed.]

G:\Az\security\Attestation\src\AttestationServices\Instance\Enclave\api.cpp(840)\(null)!00007FF801739FF3: (caller: 00007FF801232801) LogHr(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Unhandled Enclave Exception: "Policy Evaluation Error has occurred"]

```

**문제 해결 단계** 사용자는 동일한를 구성 하기 전에 SGX 증명 정책에 대해 enclave 증명 정보를 평가할 수 있습니다.

"DraftPolicyForAttestation" 매개 변수에 정책 텍스트를 제공 하 여 증명 API에 요청을 보냅니다. AttestSgxEnclave API는 증명 호출 중에이 정책 문서를 사용 하며 증명 정책을 사용 하기 전에 증명 정책을 테스트 하는 데 사용할 수 있습니다. 이 필드가 있을 때 생성 된 증명 토큰은 보안이 적용 되지 않습니다.

[증명 정책 예제](/azure/attestation/policy-examples) 참조

### <a name="22-attestation-failure-due-to-invalid-input"></a>2.2. 입력이 잘못 되어 증명 실패

**오류 코드** Operationfailedtransient

**시나리오 예** 입력이 잘못 되어 SGX 증명에 오류가 발생 했습니다. 다음은 오류 메시지에 대 한 몇 가지 예입니다.
- 견적 참고 자료의 오류로 인해 지정 된 따옴표가 잘못 되었습니다. 
- 견적이 생성 된 장치가 Azure 기준 요구 사항을 충족 하지 않으므로 지정 된 따옴표가 잘못 되었습니다.
- PCK Cache Service 제공 된 TCBInfo 또는 QEID가 잘못 되었으므로 지정 된 따옴표가 잘못 되었습니다.

**문제 해결 단계**

Microsoft Azure 증명은 Intel SDK에서 생성 된 SGX 따옴표의 증명을 지원 하 고 Enclave SDK를 엽니다.

Open Enclave SDK/Intel SDK를 사용 하 여 증명을 수행 하는 [코드 샘플](/samples/browse/?expanded=azure&terms=attestation) 을 참조 하세요.

### <a name="23-invalid-certificate-chain-error-while-uploading-policypolicy-signer"></a>2.3. 정책/정책 서명자를 업로드 하는 동안 잘못 된 인증서 체인 오류

**오류 코드** Operationfailedtransient

**시나리오 예** 서명 된 정책을 구성 하거나 잘못 된 인증서 체인으로 서명 된 정책 서명자를 추가/삭제 합니다. 예를 들어 루트 인증서의 기본 제약 조건 확장이 주체 유형 = CA로 설정 되어 있지 않은 경우에 해당 합니다.

```
Native operation failed with 65529: C:\source\src\AttestationServices\Instance\SgxPal\sgxcert.cpp(1074)\(null)!00007FFA285CDAED: (caller: 00007FFA285C36E8) Exception(0) 83FFFFF9 The requested item is not found    Msg:[Unable to find issuer certificate CN=attestationsigningcert]
C:\source\src\AttestationServices\Instance\Enclave\api.cpp(618)\(null)!00007FFA286DCBF8: (caller: 00007FFA285860D3) LogHr(0) 83FFFFF9 The requested item is not found    Msg:[Unhandled Enclave Exception: "The requested item is not found"]
At line:1 char:1
+ Set-AzAttestationPolicy -Name "testpolicy1" -ResourceGroupName "BugBa ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy

```

**문제 해결 단계** 루트 인증서는 CA (x.509 basic 제약 조건)에서 발급 된 것으로 플래그가 지정 되어야 하며, 그렇지 않은 경우에는 유효한 인증서로 간주 되지 않습니다. 

루트 인증서의 기본 제약 조건 확장이 주체 유형 = CA를 나타내도록 설정 되었는지 확인 합니다.

그렇지 않으면 인증서 체인이 잘못 된 것으로 간주 됩니다.

[정책 서명자](/azure/attestation/policy-signer-examples) 및 [정책](/azure/attestation/policy-examples) 예제 참조 

### <a name="24-adddelete-policy-signer-failure"></a>2.4. 정책 서명자 추가/삭제 실패

**오류 코드** InvalidOperation

**시나리오 예**

사용자가 "maa-policyCertificate" 클레임 없이 JWS를 업로드 하는 경우

```
Add-AzAttestationPolicySigner : Operation returned HTTP Status Code 400
Code: InvalidOperation
Message: Native operation failed with 74: ..\Enclave\enclave.cpp(2213)\(null)!: (caller: ) Exception(0) 83FF004A Bad
message    Msg:[Could not find "maa-policyCertificate" claim in policy token]
..\Enclave\api.cpp(496)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad
message"]
At line:1 char:1
+ Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGro ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Add-AzAttestationPolicySigner], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.AddAzureAttestationPolicySigner

```

사용자가 JWS 형식으로 인증서를 업로드 하지 않는 경우

```
Add-AzAttestationPolicySigner : Operation returned HTTP Status Code 400
Code: InvalidOperation
Message: Native operation failed with 74: ..\JsonWebToken\jsonwebtoken.cpp(375)\(null)!: (caller: ) LogHr(0) 83FF004A
Bad message    Msg:[RETURN_IF_TRUE('(firstPeriod == std::string::npos)') failed with 0x4a: Malformed JWT: Could not
find first period in the token.]
..\Enclave\enclave.cpp(2106)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message
Msg:[THROW_IF_ERROR('DecomposeJsonWebSignature(&policyJws, encodedJoseHeader, encodedJwsBody, jwsSignature)') failed
with 0x4a: 'Bad message']
..\Enclave\enclave.cpp(2106)\(null)!: (caller: ) Exception(0) 83FF004A Bad message
..\Enclave\api.cpp(496)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad
message"]
At line:1 char:1
+ Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGro ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Add-AzAttestationPolicySigner], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.AddAzureAttestationPolicySigner
```

**문제 해결 단계** 새 정책 서명자 인증서를 추가/삭제 하려면 "JSON Web Token RFC7519" (JWT) 클레임에 "x-m-policyCertificate"를 사용 합니다. 클레임의 값은 추가할 인증서를 포함 하는 RFC7517 JSON 웹 키입니다. JWT는 공급자와 연결 된 유효한 정책 서명자 인증서의 개인 키로 서명 해야 합니다. [정책 서명자 예](/azure/attestation/policy-signer-examples)를 참조 하세요.

### <a name="25-attestation-policy-configuration-failure"></a>2.5. 증명 정책 구성 오류

**오류 코드** PolicyParsingError

**시나리오 예** 잘못 된 구문 (예: 세미콜론이 누락 됨)과 함께 제공 된 정책 (예: 세미콜론이 누락 됨)/유효한 JWT 정책

```
Native operation failed with 65526: ..\NativePolicyWrapper\NativePolicyEngine.cpp(31)\(null)!: (caller: ) Exception(0) 83FFFFF6 Invalid policy was specified    Msg:[Policy Parser Exception Thrown: Offending
symbol: '['
Line: '2', Column: '1'
Failure message: 'mismatched input '[' expecting ';''
Failing rule: 'policy > versionInfo']
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FFFFF6 Invalid policy was specified    Msg:[Unhandled Enclave Exception: "Invalid policy was specified"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**오류 코드** InvalidOperation

**시나리오 예** 제공 된 잘못 된 콘텐츠 (예: 정책 서명이 필요한 경우 정책/서명 되지 않은 정책 업로드)

```
Native operation failed with 74: ..\Shared\base64url.h(226)\(null)!: (caller: ) Exception(0) 83FF004A Bad message    Msg:[Unknown base64 character: 41 (')')]
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad message"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**문제 해결 단계** 텍스트 형식의 정책이 u t f-8로 인코딩 되었는지 확인 합니다.

정책 서명이 필요한 경우 증명 정책은 JWT (RFC7519 JSON Web Token) 형식 으로만 구성 해야 합니다. 정책 서명이 필요 하지 않은 경우에는 정책을 텍스트 또는 JWT 형식으로 구성할 수 있습니다.

JWT 형식으로 정책을 구성 하려면 "AttestationPolicy" 라는 클레임에 JWT를 사용 합니다. 클레임의 값은 Base64URL 인코딩된 버전의 정책 텍스트입니다. 증명 공급자가 정책 서명자 인증서를 사용 하 여 구성 된 경우 JWT는 공급자와 연결 된 유효한 정책 서명자 인증서의 개인 키로 서명 되어야 합니다. 

텍스트 형식으로 정책을 구성 하려면 정책 텍스트를 직접 지정 합니다.

PowerShell에서 PolicyFormat을 jwt로 지정 하 여 JWT 형식으로 정책을 구성 합니다. 기본 정책 형식은 텍스트입니다.

증명 [정책 예제](/azure/attestation/policy-examples) 및 [증명 정책을 작성 하는 방법을](/azure/attestation/author-sign-policy) 참조 하세요. 

## <a name="3-azattestation-installation-issues-in-powershell"></a>3. Az. PowerShell의 증명 설치 문제

PowerShell에서 Az 또는 Az. 증명 모듈을 설치할 수 없습니다.

### <a name="error"></a>오류

경고: 패키지 원본 ' ' PackageManagement\Install-Package를 확인할 수 없습니다 https://www.powershellgallery.com/api/v2 . 지정 된 검색 조건 및 모듈 이름과 일치 하는 항목이 없습니다.

### <a name="troubleshooting-steps"></a>문제 해결 단계

PowerShell 갤러리는 더 이상 사용 되지 않는 TLS (Transport Layer Security) 버전 1.0 및 1.1입니다. 

TLS 버전 1.2 이상을 사용하는 것이 좋습니다. 

PowerShell 갤러리와 계속 상호 작용하려면 Install-Module 명령을 실행하기 전에 먼저 다음 명령을 실행합니다.

**[Net.tcp. ServicePointManager]:: SecurityProtocol = [Net.tcp. SecurityProtocolType]:: Tls12**

## <a name="4-policy-accessconfiguration-issues-in-powershell"></a>4. PowerShell의 정책 액세스/구성 문제

사용자에 게 적절 한 역할이 할당 되었습니다. 그러나 PowerShell을 통해 증명 정책을 관리 하는 동안 권한 부여 문제가 발생 했습니다.

### <a name="error"></a>오류
개체 id가 있는 클라이언트에 게 <object Id>  작업을 수행할 수 있는 권한이 없습니다. 구독/ <subscriptionId> resourcegroups/secure_enclave_poc/providers/microsoft.authorization/roleassignments/' 범위에 대 한 authorization/roleassignments/write <role assignmentId> 또는 범위가 잘못 되었습니다. 액세스 권한이 최근에 부여 된 경우 자격 증명을 새로 고 치세요.

### <a name="troubleshooting-steps"></a>문제 해결 단계

증명 작업을 지 원하는 데 필요한 Az modules의 최소 버전은 다음과 같습니다. 

 **Az 4.5.0** 
 
 **Az.Accounts 1.9.2**
 
 **Az.Attestation 0.1.8** 

아래 명령을 실행하여 모든 Az 모듈의 설치된 버전을 확인합니다. 

```powershell
Get-InstalledModule 
```

버전이 최소 요구 사항과 일치 하지 않는 경우 Update-Module 명령을 실행 합니다.

예:-Update-Module-Name Az. 증명

