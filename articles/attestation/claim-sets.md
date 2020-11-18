---
title: Azure Attestation 클레임 집합
description: Azure Attestation의 클레임 집합입니다.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: e17002534d35f477467f0c35833560a0267dd596
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2020
ms.locfileid: "92909780"
---
# <a name="claim-sets"></a>클레임 집합

Microsoft Azure Attestation을 사용하여 enclave를 증명하는 프로세스에서 생성된 클레임은 아래 범주로 나눌 수 있습니다.

- **들어오는 클레임**: 증명 증거를 구문 분석한 후 Microsoft Azure Attestation에서 생성된 클레임입니다.

- **나가는 클레임**: Azure Attestation에서 출력으로 생성된 클레임입니다. 여기에는 증명 토큰으로 끝나야 하는 모든 클레임이 포함됩니다.

- **속성 클레임**: Azure Attestation에서 출력으로 생성된 클레임입니다. 여기에는 보고서의 인코딩, 보고서의 유효 기간 등과 같은 증명 토큰의 속성을 나타내는 모든 클레임이 포함됩니다.

JWT RFC에서 정의되고 응답 개체의 Azure Attestation에서 사용되는 클레임은 다음과 같습니다.

- **"iss"(발급자) 클레임**: "iss"(발급자) 클레임은 JWT를 발급한 보안 주체를 식별합니다. 이 클레임을 처리하는 방법은 일반적으로 애플리케이션마다 다릅니다. "iss" 값은 StringOrURI 값을 포함하고 대/소문자를 구분하는 문자열입니다.
- **"iat"(발급 시간) 클레임**: "iat"(발급 시간) 클레임은 JWT를 발급한 시간을 식별합니다. 이 클레임은 JWT의 기간을 결정하는 데 사용할 수 있습니다. 해당 값은 NumericDate 값을 포함하는 숫자여야 합니다.
- **"exp"(만료 시간) 클레임**: "exp"(만료 시간) 클레임은 JWT가 그 이후에는 처리를 허용하지 않아야 하는 만료 시간을 식별합니다. "exp" 클레임을 처리하려면 현재 날짜/시간이 "exp" 클레임에 나열된 만료 날짜/시간 이전이어야 합니다.

  참고: 클록 스큐를 고려하여 5분의 여유 시간이 발급 시간(iat)에 추가됩니다.
- **"nbf"(이전이 아님) 클레임**: "nbf"(이전이 아님) 클레임은 JWT를 처리하는 데 허용되지 않는 시간 이전의 시간을 식별합니다. "nbf" 클레임을 처리하려면 현재 날짜/시간이 "nbf" 클레임에 나열된 날짜/시간 이전이 아니라 이후이거나 같아야 합니다.
  참고: 클록 스큐를 고려하여 5분의 여유 시간이 발급 시간(iat)에 추가됩니다.

## <a name="claims-issued-by-azure-attestation-in-sgx-enclaves"></a>SGX enclave에서 Azure Attestation을 통해 발급된 클레임

### <a name="incoming-claims"></a>들어오는 클레임 

- **$is-debuggable**: 디버깅을 enclave에 사용하도록 설정하는지 여부를 나타내는 부울
- **$sgx-mrsigner**: 따옴표의 "mrsigner" 필드에 대해 16진수로 인코딩된 값
- **$sgx-mrenclave**: 따옴표의 "mrenclave" 필드에 대해 16진수로 인코딩된 값
- **$product-id**
- **$svn**: 따옴표로 인코딩된 보안 버전 번호 
- **$tee**: enclave 형식 

### <a name="outgoing-claims"></a>나가는 클레임

- **is-debuggable**: 디버깅을 enclave에 사용하도록 설정하는지 여부를 나타내는 부울
- **sgx-mrsigner**: 따옴표의 "mrsigner" 필드에 대해 16진수로 인코딩된 값
- **sgx-mrenclave**: 따옴표의 "mrenclave" 필드에 대해 16진수로 인코딩된 값
- **product-id**
- **svn**: 따옴표로 인코딩된 보안 버전 번호 
- **tee**: enclave 형식 
- **maa-ehd**:  증명 요청에 지정된 "enclave 보유 데이터"에 대해 Base64Url로 인코딩된 버전 
- **aas-ehd**:  증명 요청에 지정된 "enclave 보유 데이터"에 대해 Base64Url로 인코딩된 버전 

## <a name="claims-issued-by-azure-attestation-in-vbs-enclaves"></a>VBS enclave에서 Azure Attestation을 통해 발급된 클레임

### <a name="incoming-claims-can-also-be-used-as-outgoing-claims"></a>들어오는 클레임(나가는 클레임으로도 사용 가능)

- **aikValidated**:  AIK(증명 ID 키) 인증서의 유효성이 검사되었는지 여부에 대한 정보가 포함된 부울 값입니다.
- **aikPubHash**:  base64(SHA256(DER 형식의 AIK 공개 키))가 포함된 문자열입니다.
- **tpmVersion**:   TPM(신뢰할 수 있는 플랫폼 모듈) 주 버전이 포함된 정수 값입니다.
- **secureBootEnabled**: 보안 부팅을 사용하도록 설정되는지 여부를 나타내는 부울 값입니다.
- **iommuEnabled**:  Iommu(입력-출력 메모리 관리 단위)를 사용하도록 설정되는지 여부를 나타내는 부울 값입니다.
- **bootDebuggingDisabled**: 부팅 디버깅을 사용하지 않도록 설정되는지 여부를 나타내는 부울 값입니다.
- **notSafeMode**:  Windows가 안전 모드에서 실행되지 않는지 여부를 나타내는 부울 값입니다.
- **notWinPE**:  Windows가 WinPE 모드에서 실행되지 않는지 여부를 나타내는 부울 값입니다.
- **vbsEnabled**:  VBS를 사용하도록 설정되는지 여부를 나타내는 부울 값입니다.
- **vbsReportPresent**:  VBS enclave 보고서를 사용할 수 있는지 여부를 나타내는 부울 값입니다.
- **enclaveAuthorId**:  enclave 작성자 ID에 대해 Base64Url로 인코딩된 값이 포함된 문자열 값입니다. 이 ID는 enclave에 대한 주 모듈의 작성자 식별자입니다.
- **enclaveImageId**:  enclave 이미지 ID에 대해 Base64Url로 인코딩된 값이 포함된 문자열 값입니다. 이 ID는 enclave에 대한 주 모듈의 이미지 식별자입니다.
- **enclaveOwnerId**:  enclave 소유자 ID에 대해 Base64Url로 인코딩된 값이 포함된 문자열 값입니다. 이 ID는 enclave에 대한 주 모듈의 소유자 식별자입니다.
- **enclaveFamilyId**:  enclave 패밀리 ID에 대해 Base64Url로 인코딩된 값이 포함된 문자열 값입니다. 이 ID는 enclave에 대한 주 모듈의 패밀리 식별자입니다.
- **enclaveSvn**:  enclave에 대한 주 모듈의 보안 버전 번호가 포함된 정수 값입니다.
- **enclavePlatformSvn**:  enclave를 호스팅하는 플랫폼의 보안 버전 번호가 포함된 정수 값입니다.
- **enclaveFlags**:  enclaveFlags 클레임은 enclave에 대한 런타임 정책을 설명하는 플래그가 포함된 정수 값입니다.
  
### <a name="outgoing-claims"></a>나가는 클레임

- **policy_hash**:  BASE64URL(SHA256(BASE64URL(UTF8(정책 텍스트))))로 계산된 정책 텍스트의 SHA256 해시가 포함된 문자열 값입니다.
- **policy_signer**:  서명된 정책 헤더에 있는 공개 키 또는 인증서 체인이 있는 JWK를 포함합니다.
- **ver(버전)** :  보고서 버전이 포함된 문자열 값입니다. 현재 1.0입니다.
- **cnf(확인) 클레임**:  "cnf" 클레임은 소유 증명 키를 식별하는 데 사용됩니다. RFC 7800에 정의된 확인 클레임에는 JWK(JSON Web Key) 개체(RFC 7517)로 표현되는 증명된 enclave 키의 공개 부분이 포함됩니다.
- **rp_data(신뢰 당사자 데이터)** :  요청에 지정된 신뢰 당사자 데이터(있는 경우)는 보고서의 최신 상태를 보장하기 위해 해당 신뢰 당사자가 nonce로 사용합니다.
- **"jti"(JWT ID) 클레임**: "jti"(JWT ID) 클레임은 JWT에 대한 고유 식별자를 제공합니다. 식별자 값은 동일한 값이 실수로 다른 데이터 개체에 할당될 가능성을 무시할 수 있도록 하는 방식으로 할당됩니다.

### <a name="property-claims"></a>속성 클레임

- **report_validity_in_minutes**: 토큰이 유효한 기간을 나타내는 정수 클레임입니다.
  - **기본값(시간)** : 1일(분 단위)입니다.
  - **최댓값(시간)** : 1년(분 단위)입니다.
- **omit_x5c**: Azure Attestation에서 서비스 인증 증명을 제공하는 데 사용되는 인증서를 생략해야 하는지 여부를 나타내는 부울 클레임입니다. true이면 x5t가 증명 토큰에 추가됩니다. false(기본값)이면 x5c가 증명 토큰에 추가됩니다.

## <a name="next-steps"></a>다음 단계
- [증명 정책을 작성하고 서명하는 방법](author-sign-policy.md)
- [PowerShell을 사용하여 Azure Attestation 설정](quickstart-powershell.md)
