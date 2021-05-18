---
title: Azure Attestation 문제 해결 가이드
description: 자주 관찰되는 문제에 대한 문제 해결 가이드
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 3ae3e12c11f194b3efcc149382dc952bd74d38b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97704319"
---
# <a name="microsoft-azure-attestation-troubleshooting-guide"></a>Microsoft Azure Attestation 문제 해결 가이드

Azure 증명의 오류 처리는 [Microsoft REST API 지침](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#7102-error-condition-responses)에 따라 구현됩니다. Azure Attestation API에서 반환되는 오류 응답에는 이름이 "code" 및 "message"인 HTTP 상태 코드 및 이름/값 쌍이 포함되어 있습니다. "코드" 값은 사람이 읽을 수 있으며 오류 형식을 나타내는 표시기입니다. "메시지" 값의 목적은 사용자에게 도움을 주고 오류 세부 정보를 제공하는 것입니다.

이 문서에서 문제가 해결되지 않으면 [Azure 지원 페이지](https://azure.microsoft.com/support/options/)에서 Azure 지원 요청을 제출할 수도 있습니다.

다음은 Azure Attestation에서 반환되는 오류의 몇 가지 예제입니다.

## <a name="1-http401--unauthorized-exception"></a>1. HTTP–401: 권한 없는 예외

### <a name="http-status-code"></a>HTTP 상태 코드
401

**오류 코드** 권한 없음

**시나리오 예제**
  - 사용자가 증명 판독기 역할로 할당되지 않은 경우 증명 실패
  - 사용자가 적절한 역할로 할당되지 않았으므로 증명 정책을 관리할 수 없음
  - 사용자가 적절한 역할로 할당되지 않았으므로 증명 정책 서명자를 관리할 수 없음

PowerShell에서 증명 정책을 편집하려고 하는 판독기 역할의 사용자 

  ```powershell
  Set-AzAttestationPolicy : Operation returned HTTP Status Code 401
At line:1 char:1
+ Set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
  ```

**문제 해결 단계**

증명 정책/정책 서명자를 보려면 Azure AD 사용자에게 "작업"에 대한 권한이 필요합니다.
- Microsoft.Attestation/attestationProviders/attestation/read

  이 권한은 “소유자”(와일드카드 권한) 또는 “판독기”(와일드카드 권한) 또는 "증명 판독기"(Azure Attestation에 대한 특정           권한만 해당)와 같은 역할을 통해 AD 사용자에게 할당할 수 있습니다.

정책 서명자를 추가/삭제하거나 정책을 구성하려면 Azure AD 사용자에게 "작업"에 대한 다음 권한이 필요합니다.
- Microsoft.Attestation/attestationProviders/attestation/write
- Microsoft.Attestation/attestationProviders/attestation/delete

  이러한 권한은 "소유자"(와일드카드 권한), "기여자"(와일드카드 권한) 또는 "증명 기여자"        (Azure Attestation의 특정 권한만 해당)와 같은 역할을 통해 AD 사용자에게 할당할 수 있습니다.

고객은 기본 공급자를 증명에 사용하거나 사용자 지정 정책을 사용하여 자체 공급자를 만들 수 있습니다. 사용자 지정 증명 공급자에게 증명 요청을 보내려면 사용자에게 "Owner"(와일드카드 권한) 또는 "판독기"(와일드카드 권한) 또는 "증명 판독기" 역할이 필요합니다. 기본값 공급자는 모든 Azure AD 사용자가 액세스할 수 있습니다.

PowerShell에서 역할을 확인하려면 다음을 실행합니다.

a. PowerShell을 시작하여 “AzAccount” cmdlet을 통해 Azure에 로그인

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

다. 목록에서 적절한 역할 할당을 찾지 못한 경우 [여기](../role-based-access-control/role-assignments-powershell.md)에 있는 지침을 따르세요.

## <a name="2-http--400-errors"></a>2. HTTP-400 오류

### <a name="http-status-code"></a>HTTP 상태 코드
400

요청이 400을 반환하는 이유에는 여러 가지가 있습니다. 다음은 Azure Attestation API에서 반환되는 오류의 몇 가지 예제입니다.

### <a name="21-attestation-failure-due-to-policy-evaluation-errors"></a>2.1. 정책 평가 오류로 인한 증명 실패

증명 정책에는 권한 부여 규칙과 발급 규칙이 포함되어 있습니다. Enclave 증거는 권한 부여 규칙을 기반으로 평가됩니다. 발급 규칙은 증명 토큰에 클레임이 포함되도록 정의합니다. Enclave 증거의 클레임이 권한 부여 규칙을 준수하지 않는 경우에는 증명 호출 시 정책 평가 오류가 반환됩니다. 

**오류 코드** PolicyEvaluationError

**시나리오 예제** Enclave 견적의 클레임이 증명 정책의 권한 부여 규칙과 일치하지 않는 경우

```
Native operation failed with 65518: G:\Az\security\Attestation\src\AttestationServices\Instance\NativePolicyWrapper\NativePolicyEngine.cpp(168)\(null)!00007FF801762308: (caller: 00007FF80143DCC8) Exception(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Policy Engine Exception: A Deny claim was issued, authorization failed.]

G:\Az\security\Attestation\src\AttestationServices\Instance\Enclave\api.cpp(840)\(null)!00007FF801739FF3: (caller: 00007FF801232801) LogHr(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Unhandled Enclave Exception: "Policy Evaluation Error has occurred"]

```

**문제 해결 단계** 사용자는 동일하게 구성하기 전에 SGX 증명 정책에 대해 enclave 증거를 평가할 수 있습니다.

"DraftPolicyForAttestation" 매개 변수에 정책 텍스트를 제공하여 증명 API에 요청을 보냅니다. AttestSgxEnclave API는 증명 호출 중에 이 정책 문서를 사용하며 이는 증명 정책을 사용하기 전에 테스트하는 데 사용될 수 있습니다. 이 필드가 있는 경우 생성된 증명 토큰은 보안이 적용되지 않습니다.

[증명 정책 예제](./policy-examples.md) 참조

### <a name="22-attestation-failure-due-to-invalid-input"></a>2.2. 잘못된 입력으로 인한 증명 실패

**오류 코드** InvalidParameter

**시나리오 예제** 잘못된 입력으로 인해 SGX 증명에 오류가 발생했습니다. 다음은 오류 메시지에 대한 몇 가지 예제입니다.
- 견적 참고 자료의 오류로 인해 지정된 견적이 잘못됨 
- 견적이 생성된 디바이스가 Azure 기준 요구 사항을 충족하지 않으므로 지정된 견적이 잘못됨
- PCK Cache Service가 제공한 TCBInfo 또는 QEID가 잘못되었으므로 지정된 견적이 잘못됨

**문제 해결 단계**

Microsoft Azure Attestation은 Intel SDK 및 Open Enclave SDK에서 생성된 SGX 견적의 증명을 지원합니다.

Open Enclave SDK/Intel SDK를 사용하여 증명을 수행하는 방법은 [코드 샘플](/samples/browse/?expanded=azure&terms=attestation)을 참조하세요.

### <a name="23-invalid-certificate-chain-error-while-uploading-policypolicy-signer"></a>2.3. 정책/정책 서명자를 업로드하는 동안 잘못된 인증서 체인 오류

**오류 코드** InvalidParameter

**시나리오 예제** 서명된 정책을 구성하거나 잘못된 인증서 체인으로 서명된 정책 서명자를 추가/삭제합니다(예: 루트 인증서의 기본 제약 조건 확장이 Subject Type = CA로 설정되어 있지 않은 경우).

```
Native operation failed with 65529: C:\source\src\AttestationServices\Instance\SgxPal\sgxcert.cpp(1074)\(null)!00007FFA285CDAED: (caller: 00007FFA285C36E8) Exception(0) 83FFFFF9 The requested item is not found    Msg:[Unable to find issuer certificate CN=attestationsigningcert]
C:\source\src\AttestationServices\Instance\Enclave\api.cpp(618)\(null)!00007FFA286DCBF8: (caller: 00007FFA285860D3) LogHr(0) 83FFFFF9 The requested item is not found    Msg:[Unhandled Enclave Exception: "The requested item is not found"]
At line:1 char:1
+ Set-AzAttestationPolicy -Name "testpolicy1" -ResourceGroupName "BugBa ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy

```

**문제 해결 단계** 루트 인증서는 CA(x.509 기본 제약 조건)에서 발급된 것으로 플래그가 지정되어야 하며, 그렇지 않은 경우에는 유효한 인증서로 간주되지 않습니다. 

루트 인증서의 기본 제약 조건 확장이 주체 유형 = CA를 나타내도록 설정되었는지 확인합니다.

그렇지 않으면 인증서 체인이 잘못된 것으로 간주됩니다.

[정책 서명자](./policy-signer-examples.md) 및 [정책](./policy-examples.md) 예제 참조 

### <a name="24-adddelete-policy-signer-failure"></a>2.4. 정책 서명자 추가/삭제 실패

**오류 코드** InvalidOperation

**시나리오 예제**

사용자가 "maa-policyCertificate" 클레임 없이 JWS를 업로드하는 경우

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

사용자가 JWS 형식으로 인증서를 업로드하지 않는 경우

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

**문제 해결 단계** 새 정책 서명자 인증서를 추가/삭제하려면 RFC7519 JSON Web Token(JWT)을 “x-m-policyCertificate”라는 클레임과 함께 사용합니다. 클레임 값은 추가할 인증서를 포함하는 RFC7517 JSON Web Key입니다. JWT는 공급자와 연결된 유효한 정책 서명자 인증서의 프라이빗 키로 서명해야 합니다. [정책 서명자 예제](./policy-signer-examples.md)를 참조하세요.

### <a name="25-attestation-policy-configuration-failure"></a>2.5. 증명 정책 구성 실패

**오류 코드** PolicyParsingError

**시나리오 예제** 잘못된 구문이 함께 제공된 정책(예: 세미콜론 누락/유효한 JWT 정책)

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

**시나리오 예제** 잘못된 콘텐츠가 제공됨(예: 정책 업로드/정책 서명이 필요한 경우 서명되지 않은 정책 업로드)

```
Native operation failed with 74: ..\Shared\base64url.h(226)\(null)!: (caller: ) Exception(0) 83FF004A Bad message    Msg:[Unknown base64 character: 41 (')')]
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad message"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**문제 해결 단계** 텍스트 형식의 정책이 UTF-8로 인코딩되었는지 확인합니다.

정책 서명이 필요한 경우 증명 정책은 RFC7519 JWT(JSON Web Token) 형식으로만 구성해야 합니다. 정책 서명이 필요하지 않은 경우 정책을 텍스트 또는 JWT 형식으로 구성할 수 있습니다.

JWT 형식으로 정책을 구성하려면 “AttestationPolicy”라는 클레임에 JWT를 사용합니다. 클레임 값은 Base64URL 인코딩된 버전의 정책 텍스트입니다. 증명 공급자가 정책 서명자 인증서를 사용하여 구성된 경우 JWT는 공급자와 연결된 유효한 정책 서명자 인증서의 프라이빗 키로 서명되어야 합니다. 

텍스트 형식으로 정책을 구성하려면 정책 텍스트를 직접 지정합니다.

PowerShell에서 PolicyFormat을 JWT로 지정하여 정책을 JWT 형식으로 구성합니다. 정책 형식 기본값은 텍스트입니다.

증명 [정책 예제](./policy-examples.md) 및 [증명 정책을 작성하는 방법](./author-sign-policy.md)을 참조하세요. 

## <a name="3-azattestation-installation-issues-in-powershell"></a>3. PowerShell의 Az. 증명 설치 문제

PowerShell에서 Az 또는 Az. 증명 모듈을 설치할 수 없습니다.

### <a name="error"></a>Error

경고: 패키지 원본 'https://www.powershellgallery.com/api/v2 ' PackageManagement\Install-Package를 확인할 수 없습니다. 지정된 검색 조건 및 모듈 이름과 일치하는 항목이 없습니다.

### <a name="troubleshooting-steps"></a>문제 해결 단계

PowerShell 갤러리에는 더 이상 사용되지 않는 TLS(전송 계층 보안) 버전 1.0 및 1.1이 있습니다. 

TLS 버전 1.2 이상을 사용하는 것이 좋습니다. 

PowerShell 갤러리와 계속 상호 작용하려면 Install-Module 명령을 실행하기 전에 먼저 다음 명령을 실행합니다.

**[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12**

## <a name="4-policy-accessconfiguration-issues-in-powershell"></a>4. PowerShell의 정책 액세스/구성 문제

사용자에게 적절한 역할이 할당되었습니다. 그러나 PowerShell을 통해 증명 정책을 관리하는 동안 권한 부여 문제가 발생했습니다.

### <a name="error"></a>Error
개체 ID &lt;개체 ID&gt;가 있는 클라이언트에게 Microsoft.Authorization/roleassignments/write over scope ‘subcriptions/&lt;subscriptionId&gt;resourcegroups/secure_enclave_poc/providers/Microsoft.Authorization/roleassignments/&lt;role assignmentId&gt;’ 작업을 수행할 권한이 없거나 범위가 잘못되었습니다. 액세스 권한이 최근에 부여된 경우 자격 증명을 새로 고치세요.

### <a name="troubleshooting-steps"></a>문제 해결 단계

증명 작업을 지원하는 데 필요한 Az 모듈의 최소 버전은 다음과 같습니다. 

 **Az 4.5.0** 
 
 **Az.Accounts 1.9.2**
 
 **Az.Attestation 0.1.8** 

아래 명령을 실행하여 모든 Az 모듈의 설치된 버전을 확인합니다. 

```powershell
Get-InstalledModule 
```

버전이 최소 요구 사항과 일치하지 않으면 Update-Module 명령을 실행합니다.

예: - Update-Module -Name Az.Attestation
