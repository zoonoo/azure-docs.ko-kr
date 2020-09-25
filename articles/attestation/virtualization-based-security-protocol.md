---
title: Azure 증명에 대 한 VBS (가상화 기반 보안) 프로토콜
description: VBS 증명 프로토콜
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: e5cc3b5fb7ca38df196119de12d346f5d0346b58
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91343782"
---
# <a name="virtualization-based-security-vbs-attestation-protocol"></a>VBS (가상화 기반 보안) 증명 프로토콜 

강력한 보안을 제공 하는 강력한 보안을 제공 하는 Microsoft Azure 증명은 보고 하는 데이터에 대 한 인증을 제공 하려면 펌웨어와 하이퍼바이저의 시작에 대 한 신뢰 체인을 구축 해야 합니다. 이 Azure 증명을 얻기 위해서는 보안 enclave 신뢰를 설정 하기 전에 컴퓨터의 부팅 상태를 증명 해야 합니다. 운영 체제, 하이퍼바이저 및 보안 커널 이진 파일은 올바른 공식 Microsoft 기관에서 서명 하 고 안전한 방식으로 구성 해야 합니다. 신뢰할 수 있는 플랫폼 모듈 (TPM)와 하이퍼바이저의 상태 간에 바인딩이 신뢰 되 면 측정 된 부팅 로그에 제공 된 VBS IDKS를 신뢰할 수 있습니다. 이를 통해 키 쌍이 enclave 및 mint에 의해 생성 되었는지 확인할 수 있습니다 .이는 해당 키의 트러스트를 바인딩하고 보안 수준 및 부팅 증명 속성과 같은 다른 클레임을 포함 하는 증명 보고서입니다.

## <a name="protocol-messages"></a>프로토콜 메시지

### <a name="init-message"></a>초기화 메시지

#### <a name="direction"></a>방향

클라이언트-> Azure 증명

#### <a name="payload"></a>Payload

```
{
  "type": "aikcert"
}
```

"type" (ASCII string): 요청 된 증명의 형식을 나타냅니다. 현재 "aikcert"만 지원 됩니다.

### <a name="challenge-message"></a>챌린지 메시지

#### <a name="direction"></a>방향

Azure 증명-> 클라이언트

#### <a name="payload"></a>Payload

```
{

  "challenge": "<BASE64URL(CHALLENGE)>",
  
  "service_context": "<BASE64URL(SERVICECONTEXT)>"
  
}
```

**챌린지** (BASE64URL (8 진수)): 서비스에서 발급 된 임의 값입니다.

**service_context** (BASE64URL (8 진수)): 서비스에 의해 생성 되는 불투명 하 고 암호화 된 컨텍스트로, 해당 챌린지의 챌린지 및 만료 시간을 포함 합니다.


### <a name="request-message"></a>메시지 요청

#### <a name="direction"></a>방향

클라이언트-> Azure 증명 

#### <a name="payload"></a>Payload

```
{

  "request": "<JWS>"
  
}
```

**요청** (jws): 요청은 JWS (JSON 웹 서명) 구조체로 구성 됩니다. JWS 보호 헤더 및 JWS 페이로드가 아래에 표시 됩니다. 모든 JWS 구조에서와 마찬가지로 최종 값은 다음으로 구성 됩니다.

BASE64URL (UTF8 (JWS Protected 헤더)) | | '.' ||

BASE64URL (JWS 페이로드) | | '.' ||

BASE64URL (JWS 서명)

##### <a name="jws-protected-header"></a>JWS Protected 헤더

```
{
  "alg": "PS256",
  "typ": "attReq"
  // no "kid" parameter as the key specified by attest_key MUST sign this JWS to prove possession.
}
```

##### <a name="jws-payload"></a>JWS 페이로드

JWS 페이로드는 basic 또는 VBS 형식일 수 있습니다. 기본은 증명 증명 정보에 VBS 데이터가 포함 되지 않은 경우에 사용 됩니다.

기본 예제

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

VBS 예

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
    "vbs_report": "<BASE64URL(REPORT)>"
  }
}
``` 

**rp_id** (stringoruri): 신뢰 당사자 식별자입니다. 서비스에서 컴퓨터 ID 클레임을 계산 하는 데 사용 됩니다.

**rp_data** (BASE64URL (8 진수)): 신뢰 당사자가 전달한 불투명 데이터입니다. 이는 일반적으로 신뢰 당사자가 보고서의 최신 유효성을 보장 하기 위해 nonce로 사용 됩니다.

**챌린지** (BASE64URL (8 진수)): 서비스에서 발급 한 임의의 값

**tpm_att_data**: tpm 관련 증명 데이터

- **srtm_boot_log (BASE64URL (8 진수))**: 함수 Tbsi_Get_TCG_Log_Ex에 의해 검색 된 srtm 부팅 로그 (log type = TBS_TCGLOG_SRTM_BOOT

- **srtm_resume_log (BASE64URL (8 진수))**: srtm resume 로그는 함수 Tbsi_Get_TCG_Log_Ex에 의해 검색 된 log type = TBS_TCGLOG_SRTM_RESUME

- **drtm_boot_log (BASE64URL (8 진수))**: 함수 Tbsi_Get_TCG_Log_Ex에 의해 검색 된 drtm 부팅 로그 (log type = TBS_TCGLOG_DRTM_BOOT

- **drtm_resume_log (BASE64URL (8 진수))**: 함수 Tbsi_Get_TCG_Log_Ex에서 검색 한 drtm resume log with log type = TBS_TCGLOG_DRTM_RESUME

- **aik_cert (BASE64URL (8 진수))**: 속성 = NCRYPT_CERTIFICATE_PROPERTY를 사용 하 여 NCryptGetProperty 함수에서 반환 된 aik의 x.509 인증서

- **aik_pub**: JWK (JSON 웹 키) 개체로 표현 된 aik의 공개 부분 (RFC 7517)

- **current_claim (BASE64URL (8 진수))**: dwclaimtype = NCRYPT_CLAIM_PLATFORM 인 NCryptCreateClaim 함수에서 반환 된 현재 PCR 상태에 대 한 증명 클레임 및 모든 PCRs 포함 하도록 설정 된 매개 변수 NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK. 이 클레임을 계산 하는 데 서비스에서 보낸 챌린지도 사용 해야 합니다.

- **boot_claim (BASE64URL (8 진수))**: dwclaimtype = NCRYPT_CLAIM_PLATFORM를 사용 하 여 함수 NCryptCreateClaim에서 반환 된 PCR 상태에 대 한 증명 클레임 및 모든 PCRs를 포함 하도록 설정 된 매개 변수 NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK

**vbs 보고서** (BASE64URL (8 진수)): EnclaveGetAttestationReport 함수에서 반환 되는 vbs enclave 증명 보고서입니다. EnclaveData 매개 변수는 report_signed 값의 SHA-512 해시 (여는 중괄호 및 닫는 중괄호 포함) 여야 합니다. 해시 함수 입력은 UTF8 (report_signed)입니다.

**attest_key**: JWK (JSON 웹 키) 개체로 표시 되는 enclave 키의 공개 부분 (RFC 7517)

**custom_claims**: 정책으로 평가할 수 있는 서비스에 전송 되는 사용자 지정 enclave 클레임의 배열입니다. 클레임

- **이름 (String)**: 클레임의 이름입니다. 이 이름은 증명 서비스에 의해 결정 되는 url에 추가 되며 (충돌을 방지 하기 위해) 연결 된 문자열은 정책에서 사용할 수 있는 클레임 형식이 됩니다.

- **값 (문자열)**: 클레임의 값입니다.

- **value_type (문자열)**: 클레임 값의 데이터 형식

**service_context** (BASE64URL (8 진수)): 서비스에 의해 생성 되는 불투명 하 고 암호화 된 컨텍스트로, 해당 챌린지의 챌린지 및 만료 시간을 포함 합니다.

### <a name="report-message"></a>보고서 메시지

#### <a name="direction"></a>방향

Azure 증명-> 클라이언트

#### <a name="payload"></a>Payload

```
{
  "report": "<JWT>"
}
```

**report** (jwt): 증명 보고서 (jwt) 형식의 JSON WEB TOKEN (RFC 7519).
