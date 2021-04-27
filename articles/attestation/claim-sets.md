---
title: Azure Attestation 클레임 집합
description: Azure Attestation의 클레임 집합입니다.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: e82e9fc93bf8c816fcbfd5869156745dea630313
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517558"
---
# <a name="claim-sets"></a>클레임 집합

Microsoft Azure Attestation을 사용하여 enclave를 증명하는 프로세스에서 생성된 클레임은 아래 범주로 나눌 수 있습니다.

- **들어오는 클레임**: 증명 증거를 구문 분석한 후 Microsoft Azure Attestation에서 생성된 클레임이며, 정책 작성자가 사용자 지정 정책에서 권한 부여 규칙을 정의하는 데 사용할 수 있습니다.

- **나가는 클레임**: Azure Attestation에서 생성되고 증명 토큰에 포함된 클레임입니다.

- **속성 클레임**: Azure Attestation에서 출력으로 생성된 클레임입니다. 여기에는 보고서의 인코딩, 보고서의 유효 기간 등과 같은 증명 토큰의 속성을 나타내는 모든 클레임이 포함됩니다.

## <a name="incoming-claims"></a>들어오는 클레임 

### <a name="sgx-attestation"></a>SGX 증명

정책 작성자가 SGX 증명 정책에서 권한 부여 규칙을 정의하는 데 사용할 클레임:

- **x-ms-sgx-is-debuggable**: enclave 디버깅을 사용하도록 설정했는지 여부를 나타내는 부울 값입니다.
  
  SGX enclave는 디버깅을 사용하지 않도록 또는 사용하도록 설정하여 로드할 수 있습니다. enclave에서 플래그를 true로 설정하면 enclave 코드에 대한 디버깅 기능을 사용할 수 있습니다. 여기에는 enclave의 메모리에 액세스하는 기능이 포함됩니다. 따라서 개발 용도로만 플래그를 true로 설정하는 것이 좋습니다. 프로덕션 환경에서 사용하도록 설정된 경우에는 SGX 보안이 보장되지 않습니다.
  
  Azure Attestation 사용자는 증명 정책을 사용하여 SGX enclave에 대해 디버깅이 비활성화되었는지 여부를 확인할 수 있습니다. 정책 규칙이 추가된 후에는 악의적인 사용자가 enclave 콘텐츠에 대한 액세스 권한을 얻기 위해 디버깅 지원을 켜면 증명이 실패합니다.

- **x-ms-sgx-product-id**: SGX enclave의 제품 ID를 나타내는 정수 값입니다.

  enclave 작성자는 각 enclave에 제품 ID를 할당합니다. 제품 ID를 통해 enclave 작성자가 동일한 MRSIGNER를 사용하여 서명된 enclave를 분할할 수 있습니다. 증명 정책에서 유효성 검사 규칙을 추가하여 고객은 의도된 enclave를 사용하는지 여부를 확인할 수 있습니다. enclave의 제품 ID가 enclave 작성자에 의해 게시된 값과 일치하지 않으면 증명이 실패합니다.

- **x-ms-sgx-mrsigner**: SGX enclave의 작성자를 식별하는 문자열 값입니다.

  MRSIGNER는 enclave 이진에 서명하는 데 사용되는 enclave 작성자의 공개 키의 해시입니다. 고객은 증명 정책을 통해 MRSIGNER의 유효성을 검사하여 신뢰할 수 있는 이진 파일이 enclave 내에서 실행 중인지 여부를 확인할 수 있습니다. 정책 클레임이 enclave 작성자의 MRSIGNER와 일치하지 않는 경우 enclave 이진이 신뢰할 수 있는 원본에서 서명되지 않고 증명에 실패하는 것을 의미합니다.
  
  enclave 작성자가 보안상의 이유로 MRSIGNER를 회전하는 것을 선호하는 경우 이진을 업데이트하기 전에 새로운 MRSIGNER 값과 오래된 MRSIGNER 값을 지원하도록 Azure 증명 정책을 업데이트해야 합니다. 그러지 않으면 인증 검사가 실패하여 증명 실패가 발생합니다.
  
  다음 형식을 사용하여 증명 정책을 업데이트해야 합니다. 
 
  #### <a name="before-key-rotation"></a>키 회전 전
 
   ```
    version= 1.0;
    authorizationrules 
    {
    [ type=="x-ms-sgx-is-debuggable", value==false]&&
    [ type=="x-ms-sgx-mrsigner", value=="mrsigner1"] => permit(); 
    };
  ```

   #### <a name="during-key-rotation"></a>키 회전 중

    ```
      version= 1.0;
      authorizationrules 
      {
      [ type=="x-ms-sgx-is-debuggable", value==false]&&
      [ type=="x-ms-sgx-mrsigner", value=="mrsigner1"] => permit(); 
      [ type=="x-ms-sgx-is-debuggable", value==false ]&& 
      [ type=="x-ms-sgx-mrsigner", value=="mrsigner2"] => permit(); 
      };
    ```

   #### <a name="after-key-rotation"></a>키 회전 후

    ```
      version= 1.0;
      authorizationrules 
      { 
      [ type=="x-ms-sgx-is-debuggable", value==false]&& 
      [ type=="x-ms-sgx-mrsigner", value=="mrsigner2"] => permit(); 
      };
    ```

- **x-ms-sgx-mrenclave**: enclave 메모리에 로드된 코드 및 데이터를 식별하는 문자열 값입니다. 

  MRENCLAVE는 enclave 이진 파일을 확인하는 데 사용할 수 있는 enclave 측정값 중 하나입니다. enclave 내에서 실행되는 코드의 해시입니다. enclave 이진 코드를 변경할 때마다 측정이 변경됩니다. 고객은 증명 정책을 통해 MRENCLAVE의 유효성을 검사하여 의도된 이진 파일이 enclave 내에서 실행 중인지 여부를 확인할 수 있습니다. 그러나 MRENCLAVE는 기존 코드의 사소한 수정으로 자주 변경될 것으로 예상되므로 증명 정책에서 MRSIGNER 유효성 검사를 사용하여 enclave 이진 파일을 확인하는 것이 좋습니다.

- **x-ms-sgx-svn**: SGX enclave의 보안 버전 번호를 나타내는 정수 값입니다.

  enclave 작성자는 각 버전의 SGX enclave에 SVN(보안 버전 번호)을 할당합니다. enclave 코드에서 보안 문제가 발견되면 enclave 작성자는 취약성 픽스 후 SVN 값을 증가시킵니다. 안전하지 않은 enclave 코드와의 상호 작용을 방지하기 위해 고객은 증명 정책에 유효성 검사 규칙을 추가할 수 있습니다. enclave 코드의 SVN이 enclave 작성자가 권장하는 버전과 일치하지 않으면 증명은 실패합니다.

더 이상 사용되지 않는 것으로 간주되지만 완벽하게 지원되며 나중에도 계속 포함되는 클레임은 다음과 같습니다. 사용되지 않는 클레임 이름을 사용하는 것이 좋습니다.

사용되지 않는 클레임 | 추천되는 클레임
--- | --- 
$is-debuggable | x-ms-sgx-is-debuggable
$product-id | x-ms-sgx-product-id
$sgx-mrsigner | x-ms-sgx-mrsigner
$sgx-mrenclave | x-ms-sgx-mrenclave
$svn | x-ms-sgx-svn

### <a name="tpm-attestation"></a>TPM 증명

정책 작성자가 TPM 증명 정책에서 권한 부여 규칙을 정의하는 데 사용할 클레임:

- **aikValidated**: AIK(증명 ID 키) 인증서의 유효성이 검사되었는지 여부에 대한 정보가 포함된 부울 값
- **aikPubHash**:  base64(SHA256(DER 형식의 AIK 공개 키))가 포함된 문자열
- **tpmVersion**:   TPM(신뢰할 수 있는 플랫폼 모듈) 주 버전이 포함된 정수 값
- **secureBootEnabled**: 보안 부팅을 사용하도록 설정되는지 여부를 나타내는 부울 값
- **iommuEnabled**:  Iommu(입력-출력 메모리 관리 단위)를 사용하도록 설정되는지 여부를 나타내는 부울 값
- **bootDebuggingDisabled**: 부팅 디버깅을 사용하지 않도록 설정되는지 여부를 나타내는 부울 값
- **notSafeMode**:  Windows가 안전 모드에서 실행되지 않는지 여부를 나타내는 부울 값
- **notWinPE**:  Windows가 WinPE 모드에서 실행되지 않는지 여부를 나타내는 부울 값
- **vbsEnabled**:  VBS를 사용하도록 설정되는지 여부를 나타내는 부울 값
- **vbsReportPresent**:  VBS enclave 보고서를 사용할 수 있는지 여부를 나타내는 부울 값

### <a name="vbs-attestation"></a>VBS 증명

정책 작성자는 TPM 증명 정책 클레임 외에도 아래 클레임을 사용하여 VBS 증명 정책에서 권한 부여 규칙을 정의할 수 있습니다.

- **enclaveAuthorId**:  enclave 작성자 ID에 대해 Base64Url로 인코딩된 값이 포함된 문자열 값입니다. 이 ID는 enclave에 대한 주 모듈의 작성자 식별자입니다.
- **enclaveImageId**:  enclave 이미지 ID에 대해 Base64Url로 인코딩된 값이 포함된 문자열 값입니다. 이 ID는 enclave에 대한 주 모듈의 이미지 식별자입니다.
- **enclaveOwnerId**:  enclave 소유자 ID에 대해 Base64Url로 인코딩된 값이 포함된 문자열 값입니다. 이 ID는 enclave에 대한 주 모듈의 소유자 식별자입니다.
- **enclaveFamilyId**:  Base64Url로 인코딩된 enclave 패밀리 ID 값이 포함된 문자열 값입니다. enclave에 대한 기본 모듈의 패밀리 식별자
- **enclaveSvn**:  enclave에 대한 주 모듈의 보안 버전 번호가 포함된 정수 값
- **enclavePlatformSvn**:  enclave를 호스팅하는 플랫폼의 보안 버전 번호가 포함된 정수 값
- **enclaveFlags**:  enclaveFlags 클레임은 enclave에 대한 런타임 정책을 설명하는 플래그가 포함된 정수 값입니다.

## <a name="outgoing-claims"></a>나가는 클레임 

### <a name="common-for-all-attestation-types"></a>모든 증명 형식에 대한 공통 사항

Azure Attestation은 모든 증명 형식에 대해 증명 토큰에 아래 클레임을 포함합니다. 

- **x-ms-ver**: JWT 스키마 버전("1.0"이어야 함)
- **x-ms-attestation-type**: 증명 유형을 나타내는 문자열 값 
- **x-ms-policy-hash**: BASE64URL(SHA256(UTF8(BASE64URL(UTF8(정책 텍스트)))))로 계산된 Azure Attestation 평가 정책의 해시
- **x-ms-policy-signer**: 고객이 정책에 서명하는 데 사용한 키를 나타내는 “jwk” 멤버가 포함된 JSON 개체. 고객이 서명된 정책을 업로드하는 경우에 적용할 수 있습니다.

아래 클레임 이름은 [IETF JWT 사양](https://tools.ietf.org/html/rfc7519)에서 사용됩니다.

- **“jti”(JWT ID) 클레임** - JWT에 대한 고유 식별자
- **“iss”(발급자) 클레임** - JWT를 발급한 주체 
- **“iat”(발급 시간) 클레임** - JWT를 발급한 시간 
- **“exp”(만료 시간) 클레임** - 이후 시간에는 JWT가 처리를 허용하면 안 되는 만료 시간
- **“nbf”(이전 시간) 클레임** - 이전 시간에는 JWT가 처리를 허용하면 안 되는 시간을 식별합니다. 

아래 클레임 이름은 [IETF EAT 초안 사양](https://tools.ietf.org/html/draft-ietf-rats-eat-03#page-9)에서 사용됩니다.

- **“Nonce 클레임”(nonce)** - 클라이언트에서 제공하는 선택 사항 Nonce 값의 변형되지 않은 직접 복사본입니다. 

더 이상 사용되지 않는 것으로 간주되지만 완벽하게 지원되며 나중에도 계속 포함되는 클레임은 다음과 같습니다. 사용되지 않는 클레임 이름을 사용하는 것이 좋습니다.

사용되지 않는 클레임 | 추천되는 클레임
--- | --- 
ver | x-ms-ver
tee | x-ms-attestation-type
policy_hash | x-ms-policy-hash
maa-policyHash | x-ms-policy-hash
policy_signer  | x-ms-policy-signer

### <a name="sgx-attestation"></a>SGX 증명 

아래 클레임이 생성되어 SGX 증명에 대한 서비스에 의해 증명 토큰에 포함됩니다.

- **x-ms-sgx-is-debuggable**: 디버깅을 enclave에 사용하도록 설정하는지 여부를 나타내는 부울
- **x-ms-sgx-product-id**: SGX enclave의 제품 ID 값 
- **x-ms-sgx-mrsigner**: quote의 "mrsigner" 필드에 대해 16진수로 인코딩된 값
- **x-ms-sgx-mrenclave**: quote의 "mrenclave" 필드에 대해 16진수로 인코딩된 값
- **x-ms-sgx-svn**: quote로 인코딩된 보안 버전 번호 
- **x-ms-sgx-ehd**: BASE64URL(데이터 보유 enclave) 형식의 데이터를 보유한 enclave
- **x-ms-sgx-collateral**: 증명을 수행하는 데 사용되는 참고 자료를 설명하는 JSON 개체. x-ms-sgx-collateral 클레임의 값은 다음 키/값 쌍이 있는 중첩된 JSON 개체입니다.
    - **qeidcertshash**: QE(Quoting Enclave) ID 발급 인증서의 SHA256 값
    - **qeidcrlhash**: QE ID 발급 인증서 CRL 목록의 SHA256 값
    - **qeidhash**: QE ID 참고 자료의 SHA256 값
    - **quotehash**: 계산된 quote의 SHA256 값
    - **tcbinfocertshash**: TCB 정보 발급 인증서의 SHA256 값
    - **tcbinfocrlhash**: TCB 정보 발급 인증서 CRL 목록의 SHA256 값
    - **tcbinfohash**: TCB 정보 참고 자료의 SHA256 값

더 이상 사용되지 않는 것으로 간주되지만 완벽하게 지원되며 나중에도 계속 포함되는 클레임은 다음과 같습니다. 사용되지 않는 클레임 이름을 사용하는 것이 좋습니다.

사용되지 않는 클레임 | 추천되는 클레임
--- | --- 
$is-debuggable | x-ms-sgx-is-debuggable
$product-id | x-ms-sgx-product-id
$sgx-mrsigner | x-ms-sgx-mrsigner
$sgx-mrenclave | x-ms-sgx-mrenclave
$svn | x-ms-sgx-svn
$maa-ehd | x-ms-sgx-ehd
$aas-ehd | x-ms-sgx-ehd
$maa-attestationcollateral | x-ms-sgx-collateral

### <a name="tpm-and-vbs-attestation"></a>TPM 및 VBS 증명

- **cnf(확인)** : "cnf" 클레임은 소유 증명 키를 식별하는 데 사용됩니다. RFC 7800에 정의된 확인 클레임에는 JWK(JSON Web Key) 개체(RFC 7517)로 표현되는 증명된 enclave 키의 공개 부분이 포함됩니다.
- **rp_data(신뢰 당사자 데이터)** : 요청에 지정된 신뢰 당사자 데이터(있는 경우)는 보고서의 최신 상태를 보장하기 위해 해당 신뢰 당사자가 nonce로 사용합니다. rp_data는 rp_data가 있는 경우에만 추가됩니다.

## <a name="property-claims"></a>속성 클레임

### <a name="tpm-and-vbs-attestation"></a>TPM 및 VBS 증명

- **report_validity_in_minutes**: 토큰이 유효한 기간을 나타내는 정수 클레임입니다.
  - **기본값(시간)** : 1일(분 단위)입니다.
  - **최댓값(시간)** : 1년(분 단위)입니다.
- **omit_x5c**: Azure Attestation에서 서비스 인증 증명을 제공하는 데 사용되는 인증서를 생략해야 하는지 여부를 나타내는 부울 클레임입니다. true이면 x5t가 증명 토큰에 추가됩니다. false(기본값)이면 x5c가 증명 토큰에 추가됩니다.

## <a name="next-steps"></a>다음 단계
- [증명 정책을 작성하고 서명하는 방법](author-sign-policy.md)
- [PowerShell을 사용하여 Azure Attestation 설정](quickstart-powershell.md)
