---
title: Azure Attestation 정책을 작성하고 서명하는 방법
description: 증명 정책을 작성하고 서명하는 방법의 설명입니다.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: c8ffdcd0615913649e80b20f6873d005f4ad4410
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675997"
---
# <a name="how-to-author-and-sign-an-attestation-policy"></a>증명 정책을 작성하고 서명하는 방법

증명 정책은 Microsoft Azure Attestation에 업로드된 파일입니다. Azure Attestation은 증명 특정 정책 형식으로 정책을 업로드하는 유연성을 제공합니다. 또는 JSON 웹 서명에서 인코딩된 버전의 정책을 업로드할 수도 있습니다. 정책 관리자는 증명 정책을 작성해야 합니다. 대부분의 증명 시나리오에서 신뢰 당사자는 정책 관리자 역할을 합니다. 증명 호출을 수행하는 클라이언트는 증명 증거를 보냅니다. 서비스에서 이를 구문 분석하고 들어오는 클레임(속성, 값 집합)으로 변환합니다. 그런 다음, 서비스는 정책에 정의된 항목을 기반으로 클레임을 처리하고, 계산된 결과를 반환합니다.

정책에는 인증 조건, 속성 및 증명 토큰의 콘텐츠를 결정하는 규칙이 포함되어 있습니다. 샘플 정책 파일은 아래와 같습니다.

```
version=1.0;
authorizationrules
{
   c:[type="secureBootEnables", issuer=="AttestationService"]=> permit()
};

issuancerules
{
  c:[type="secureBootEnables", issuer=="AttestationService"]=> issue(claim=c)
  c:[type="notSafeMode", issuer=="AttestationService"]=> issue(claim=c)
};
```
 
정책 파일에는 위에 표시된 것처럼 3개의 세그먼트가 있습니다.

- **버전** :  버전은 뒤따르는 문법의 버전 번호입니다. 

    ```
    version=MajorVersion.MinorVersion   
    ```

    현재 지원되는 유일한 버전은 1.0입니다.

- **authorizationrules** : Azure Attestation이 **issuancerules** 로 진행되는지 확인하기 위해 먼저 확인되는 클레임 규칙의 컬렉션입니다. 클레임 규칙은 정의된 순서대로 적용됩니다.

- **issuancerules** : 정책에 정의된 대로 증명 결과에 추가 정보를 추가하기 위해 평가되는 클레임 규칙의 컬렉션입니다. 클레임 규칙은 정의된 순서대로 적용되며 선택 사항이기도 합니다.

자세한 내용은 [클레임 및 클레임 규칙](claim-rule-grammar.md)을 참조하세요.
   
## <a name="drafting-the-policy-file"></a>정책 파일 초안 작성

1. 새 파일을 만듭니다.
1. 버전을 파일에 추가합니다.
1. **authorizationrules** 및 **issuancerules** 에 대한 섹션을 추가합니다.

  ```
  version=1.0;
  authorizationrules
  {
  =>deny();
  };
  
  issuancerules
  {
  };
  ```

  권한 부여 규칙에는 아무런 조건 없이 거부() 작업을 포함하여 발급 규칙이 처리되지 않도록 합니다. 또는 권한 부여 규칙이 발급 규칙의 처리를 허용하는 허용() 작업을 포함할 수도 있습니다.
  
4. 권한 부여 규칙에 클레임 규칙 추가

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  };
  ```

  들어오는 클레임 집합에 유형, 값 및 발급자와 일치하는 클레임이 포함된 경우 허용() 작업은 **issuancerules** 를 처리하도록 정책 엔진에 지시합니다.
  
5. **issuancerules** 에 클레임 규칙을 추가합니다.

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  => issue(type="SecurityLevelValue", value=100);
  };
  ```
  
  나가는 클레임 집합에는 다음이 포함된 클레임이 포함됩니다.

  ```
  [type="SecurityLevelValue", value=100, valueType="Integer", issuer="AttestationPolicy"]
  ```

  비슷한 방식으로 복잡한 정책을 만들 수 있습니다. 자세한 내용은 [증명 정책 예제](policy-examples.md)를 참조하세요.
  
6. 파일을 저장합니다.

## <a name="creating-the-policy-file-in-json-web-signature-format"></a>JSON 웹 서명 형식으로 정책 파일 만들기

정책 파일을 만든 후에 JWS 형식으로 정책을 업로드하려면 아래 단계를 수행합니다.

1. 페이로드로 정책(utf-8 인코딩)을 사용하여 JWS, RFC 7515를 생성합니다.
     - Base64Url 인코딩된 정책의 페이로드 식별자는 "AttestationPolicy"여야 합니다.
     
     샘플 JWT:
     ```
     Header: {"alg":"none"}
     Payload: {"AttestationPolicy":" Base64Url (policy)"}
     Signature: {}

     JWS format: eyJhbGciOiJub25lIn0.XXXXXXXXX.
     ```

2. (선택 사항) 정책에 서명합니다. Azure Attestation은 다음과 같은 알고리즘을 지원합니다.
     - **없음** : 정책 페이로드에 서명하지 않습니다.
     - **RS256** : 정책 페이로드를 서명하는 데 지원되는 알고리즘

3. JWS를 업로드하고 정책의 유효성을 검사합니다.
     - 정책 파일에 구문 오류가 없는 경우 정책 파일은 서비스에서 허용됩니다.
     - 정책 파일에 구문 오류가 있는 경우 정책 파일은 서비스에서 거부됩니다.

## <a name="signing-the-policy"></a>정책에 서명

다음은 정책 서명 작업을 수행하는 방법에 대한 샘플 Python 스크립트입니다.

```python
from OpenSSL import crypto
import jwt
import getpass
       
def cert_to_b64(cert):
              cert_pem = crypto.dump_certificate(crypto.FILETYPE_PEM, cert)
              cert_pem_str = cert_pem.decode('utf-8')
              return ''.join(cert_pem_str.split('\n')[1:-2])
       
print("Provide the path to the PKCS12 file:")
pkcs12_path = str(input())
pkcs12_password = getpass.getpass("\nProvide the password for the PKCS12 file:\n")
pkcs12_bin = open(pkcs12_path, "rb").read()
pkcs12 = crypto.load_pkcs12(pkcs12_bin, pkcs12_password.encode('utf8'))
ca_chain = pkcs12.get_ca_certificates()
ca_chain_b64 = []
for chain_cert in ca_chain:
   ca_chain_b64.append(cert_to_b64(chain_cert))
   signing_cert_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM, pkcs12.get_privatekey())
signing_cert_b64 = cert_to_b64(pkcs12.get_certificate())
ca_chain_b64.insert(0, signing_cert_b64)

print("Provide the path to the policy text file:")
policy_path = str(input())
policy_text = open(policy_path, "r").read()
encoded = jwt.encode({'text': policy_text }, signing_cert_pkey, algorithm='RS256', headers={'x5c' : ca_chain_b64})
print("\nAttestation Policy JWS:")
print(encoded.decode('utf-8'))
```

## <a name="next-steps"></a>다음 단계
- [PowerShell을 사용하여 Azure Attestation 설정](quickstart-powershell.md)
- [코드 샘플을 사용하여 SGX enclave 증명](/samples/browse/?expanded=azure&terms=attestation)