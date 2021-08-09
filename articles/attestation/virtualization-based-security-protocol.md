---
title: Azure Attestation용 VBS(가상화 기반 보안) 프로토콜
description: VBS 증명 프로토콜
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 38012c5b4bb9338c1200d9583256193ee8402c98
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99507916"
---
# <a name="trusted-platform-module-tpm-and-virtualization-based-securityvbs-enclave-attestation-protocol"></a>TPM(신뢰할 수 있는 플랫폼 모듈) 및 VBS(가상화 기반 보안) enclave 증명 프로토콜 

강력한 보안을 보장하는 Microsoft Azure Attestation은 신뢰 루트(TPM)에서 하이퍼바이저 및 보안 커널의 시작에 이르기까지 신뢰 체인이 유지 관리되는지 확인하는 작업을 기반으로 합니다. 이러한 목적을 달성하려면 먼저 Azure Attestation에서 머신의 부팅 상태를 증명해야 보안 enclave에 대한 신뢰를 설정할 수 있습니다. 운영 체제, 하이퍼바이저 및 보안 커널 이진 파일은 올바른 공식 Microsoft 기관의 서명을 받고 안전한 방식으로 구성되어야 합니다. TPM(신뢰할 수 있는 플랫폼 모듈)과 하이퍼바이저 상태 간에 신뢰를 바인딩하면 Measured Boot Log에서 제공되는 VBS(가상화 기반 보안) enclave IDK를 신뢰할 수 있습니다. 이를 통해 키 쌍이 enclave에서 생성된 것임을 확인한 다음, 해당 키에 대한 신뢰를 바인딩하고 보안 수준 및 부팅 증명 속성과 같은 다른 클레임이 포함된 증명 보고서를 발급할 수 있습니다. 

VBS enclave에는 보안 기반의 유효성을 검사하기 위한 측정값을 제공하는 TPM이 있어야 합니다. VBS enclave는 프로토콜의 요청 개체에 더하여 TPM 엔드포인트에서 증명됩니다. 

## <a name="protocol-messages"></a>프로토콜 메시지

### <a name="init-message"></a>Init message

#### <a name="direction"></a>Direction

클라이언트 -> Azure Attestation

#### <a name="payload"></a>Payload

```
{ 
  "type": "aikcert" 
} 
```

“type”(ASCII 문자열): 요청된 증명의 유형을 나타냅니다. 현재는 “aikcert”만 지원됩니다.

### <a name="challenge-message"></a>챌린지 메시지

#### <a name="direction"></a>Direction

Azure Attestation -> 클라이언트

#### <a name="payload"></a>Payload

```
{ 
  "challenge": "<BASE64URL(CHALLENGE)>", 
  "service_context": "<BASE64URL(SERVICECONTEXT)>" 
} 
```

**challenge**(BASE64URL(OCTETS)): 서비스에서 발급한 임의의 값입니다.

**service_context**(BASE64URL(OCTETS)): 서비스에서 만든 불투명 컨텍스트입니다.


### <a name="request-message"></a>메시지 요청

#### <a name="direction"></a>Direction

클라이언트 -> Azure Attestation 

#### <a name="payload"></a>Payload

```
{
  "request": "<JWS>"
}
```

**request**(JWS): JWS(JSON 웹 서명) 구조로 구성된 요청입니다. JWS 보호 헤더 및 JWS 페이로드는 아래와 같습니다. JWS 구조와 마찬가지로 최종값은 다음과 같이 구성됩니다.

BASE64URL(UTF8(JWS Protected Header)) || '.' ||

BASE64URL(JWS Payload) || '.' ||

BASE64URL(JWS Signature)

##### <a name="jws-protected-header"></a>JWS 보호 헤더

```
{
  "alg": "PS256",
  "typ": "attReq"
  // no "kid" parameter as the key specified by attest_key MUST sign this JWS to prove possession.
}
```

##### <a name="jws-payload"></a>JWS 페이로드

JWS 페이로드는 기본 또는 VBS 형식일 수 있습니다. 기본 형식은 증명 증거에 VBS 데이터가 포함되지 않은 경우에 사용됩니다. 

TPM만 포함된 경우의 샘플: 

``` 
{ 
  "att_type": "basic", 
  "att_data": { 
    "rp_id": "<URL>", 
    "rp_data": "<BASE64URL(RPCUSTOMDATA)>", 
    "challenge": "<BASE64URL(CHALLENGE)>", 

    "tpm_att_data": { 
      "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>", 
      "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>", 
      "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>", 
      "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>", 
      "aik_cert": "<BASE64URL(AIKCERTIFICATE)>", 

      // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517). 

      "aik_pub": { 
        "kty": "RSA", 
        "n": "<Base64urlUInt(MODULUS)>", 
        "e": "<Base64urlUInt(EXPONENT)>" 
      }, 
      "current_claim": "<BASE64URL(CURRENTCLAIM)>", 
      "boot_claim": "<BASE64URL(BOOTCLAIM)>" 
    }, 

    // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517). 

    "attest_key": { 
      "kty": "RSA", 
      "n": "<Base64urlUInt(MODULUS)>", 
      "e": "<Base64urlUInt(EXPONENT)>" 
    }, 
    "custom_claims": [ 
      { 
        "name": "<name>", 
        "value": "<value>", 
        "value_type": "<value_type>" 
      }, 
      { 
        "name": "<name>", 
        "value": "<value>", 
        "value_type": "<value_type>" 
      } 
    ], 
    "service_context": "<BASE64URL(SERVICECONTEXT)>" 
  } 
} 
```

TPM + VBS enclave 샘플: 

``` 
{ 
  "att_type": "vbs", 
  "att_data": { 
    "report_signed": { 
      "rp_id": "<URL>", 
      "rp_data": "<BASE64URL(RPCUSTOMDATA)>", 
      "challenge": "<BASE64URL(CHALLENGE)>", 
      "tpm_att_data": { 
        "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>", 
        "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>", 
        "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>", 
        "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>", 
        "aik_cert": "<BASE64URL(AIKCERTIFICATE)>", 

        // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517). 

        "aik_pub": { 
          "kty": "RSA", 
          "n": "<Base64urlUInt(MODULUS)>", 
          "e": "<Base64urlUInt(EXPONENT)>" 
        }, 
        "current_claim": "<BASE64URL(CURRENTCLAIM)>", 
        "boot_claim": "<BASE64URL(BOOTCLAIM)>" 
      }, 

      // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517). 

      "attest_key": { 
        "kty": "RSA", 
        "n": "<Base64urlUInt(MODULUS)>", 
        "e": "<Base64urlUInt(EXPONENT)>" 
      }, 
      "custom_claims": [ 
        { 
          "name": "<name>", 
          "value": "<value>", 
          "value_type": "<value_type>" 
        }, 
        { 
          "name": "<name>", 
          "value": "<value>", 
          "value_type": "<value_type>" 
        } 
      ], 
      "service_context": "<BASE64URL(SERVICECONTEXT)>" 
    }, 
    "vsm_report": "<BASE64URL(REPORT)>" 
  } 
} 
``` 

**rp_id**(StringOrURI): 신뢰 당사자 식별자. 서비스에서 머신 ID 클레임을 계산하는 데 사용됩니다.

**rp_data**(BASE64URL(OCTETS)): 신뢰 당사자가 전달한 불투명 데이터. 일반적으로 신뢰 당사자에 의해 보고서가 최신 상태임을 보장하는 nonce로 사용됩니다.

**challenge**(BASE64URL(OCTETS)): 서비스에서 발급한 임의의 값

**tpm_att_data**: TPM 관련 증명 데이터

- **srtm_boot_log(BASE64URL(OCTETS))** : 함수 Tbsi_Get_TCG_Log_Ex(로그 유형 = TBS_TCGLOG_SRTM_BOOT)로 검색된 SRTM 부팅 로그

- **srtm_resume_log(BASE64URL(OCTETS))** : 함수 Tbsi_Get_TCG_Log_Ex(로그 유형 = TBS_TCGLOG_SRTM_RESUME)로 검색된 SRTM 계속하기 로그

- **drtm_boot_log(BASE64URL(OCTETS))** : 함수 Tbsi_Get_TCG_Log_Ex(로그 유형 = TBS_TCGLOG_DRTM_BOOT)로 검색된 DRTM 부팅 로그

- **drtm_resume_log(BASE64URL(OCTETS))** : 함수 Tbsi_Get_TCG_Log_Ex(로그 유형 = TBS_TCGLOG_DRTM_RESUME)로 검색된 DRTM 계속하기 로그

- **aik_cert(BASE64URL(OCTETS))** : 함수 NCryptGetProperty(속성 = NCRYPT_CERTIFICATE_PROPERTY)에서 반환한 AIK에 대한 X.509 인증서

- **aik_pub**: JWK(JSON 웹 키) 개체(RFC 7517)로 표시된 AIK의 퍼블릭 부분

- **current_claim(BASE64URL(OCTETS))** : 함수 NCryptCreateClaim(dwClaimType = NCRYPT_CLAIM_PLATFORM) 및 모든 PCR을 포함하도록 설정된 매개 변수 NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK에서 반환한 현재 PCR 상태에 대한 증명 클레임. 서비스에서 보낸 챌린지는 이 클레임의 계산에도 사용해야 합니다.

- **boot_claim(BASE64URL(OCTETS))** : 함수 NCryptCreateClaim(dwClaimType = NCRYPT_CLAIM_PLATFORM) 및 모든 PCR을 포함하도록 설정된 매개 변수 NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK에서 반환한 부팅 시 PCR 상태에 대한 증명 클레임

**vsm_report** (BASE64URL(OCTETS)): 함수 EnclaveGetAttestationReport에서 반환한 VBS enclave 증명 보고서. EnclaveData 매개 변수는 report_signed 값의 SHA-512 해시여야 합니다(여는 중괄호 및 닫는 중괄호 포함). 해시 함수 입력은 UTF8(report_signed)입니다.

**attest_key**: JWK(JSON 웹 키) 개체(RFC 7517)로 표시되는 enclave 키의 퍼블릭 부분

**custom_claims**: 정책으로 평가할 수 있는 서비스에 전송된 사용자 지정 enclave 클레임 서비스의 배열 다음과 같은 클레임 항목이 있습니다.

- **name(문자열)** : 클레임의 이름. 이 이름은 증명 서비스에서 결정된 URL에 추가되고(충돌을 방지하기 위해) 연결된 문자열은 정책에서 사용 가능한 클레임 형식이 됩니다.

- **value(문자열)** : 클레임의 값

- **value_type(문자열)** : 클레임 값의 데이터 형식

**service_context**(BASE64URL(OCTETS)): 서비스에서 만든 불투명 컨텍스트입니다.

### <a name="report-message"></a>보고서 메시지

#### <a name="direction"></a>Direction

Azure Attestation -> 클라이언트

#### <a name="payload"></a>Payload

```
{
  "report": "<JWT>"
}
```

**report**(JWT): JWT(JSON Web Token) 형식(RFC 7519)의 증명 보고서

## <a name="next-steps"></a>다음 단계

- [Azure Attestation 워크플로](workflow.md)
