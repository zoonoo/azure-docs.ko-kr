---
title: Azure Key Vault 키 정보 - Azure Key Vault
description: 키에 대한 Azure Key Vault REST 인터페이스 및 개발자 세부 정보의 개요입니다.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: f96ec80b529c594a383be8d668fd28b77372cd80
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82900926"
---
# <a name="about-azure-key-vault-keys"></a>Azure Key Vault 키 정보

Azure Key Vault는 여러 키 유형과 알고리즘을 지원하며, 고부가 가치 키에 HSM(하드웨어 보안 모듈)을 사용할 수 있습니다.

Key Vault의 암호화 키는 JWK[JSON 웹 키] 개체로 표현됩니다. JSON(JavaScript Object Notation) 및 JOSE(JavaScript Object Signing and Encryption) 사양은 다음과 같습니다.

-   [JWK(JSON 웹 키)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JWE(JSON 웹 암호화)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JWA(JSON 웹 알고리즘)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JWS(JSON 웹 서명)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

또한 기본 JWK/JWA 사양을 확장하여 Key Vault 구현에 고유한 키 유형을 지원할 수 있습니다. 예를 들어 HSM 공급업체별 패키징을 사용하여 키를 가져오면 Key Vault HSM에서만 사용할 수 있는 키를 안전하게 전송할 수 있습니다. 

Azure Key Vault는 소프트 키와 하드 키를 모두 지원합니다.

- **“소프트” 키**: 소프트웨어에서 Key Vault를 통해 처리되지만, 사용되지 않을 때에는 HSM에 있는 시스템 키를 사용하여 암호화되는 키입니다. 클라이언트는 기존 RSA 또는 EC(타원 곡선) 키를 가져오거나 Key Vault에서 생성하도록 요청할 수 있습니다.
- **“하드” 키**: HSM(하드웨어 보안 모듈)에서 처리되는 키입니다. 이러한 키는 Key Vault HSM 보안 권역 중 하나에서 보호됩니다(격리를 유지하기 위해 지역별로 보안 권역이 하나씩 있음). 클라이언트는 소프트 형식으로 또는 호환되는 HSM 디바이스에서 내보내는 방식으로 RSA 또는 EC 키를 가져올 수 있습니다. 클라이언트는 Key Vault에 키를 생성하라고 요청할 수도 있습니다. 이 키 유형은 HSM 키 자료를 전송하기 위해 가져오는 JWK에 key_hsm 특성을 추가합니다.

지리적 경계에 대한 자세한 내용은 [Microsoft Azure 보안 센터](https://azure.microsoft.com/support/trust-center/privacy/)를 참조하세요.  

## <a name="cryptographic-protection"></a>암호화 보호

Key Vault는 RSA 및 타원 곡선 키만 지원합니다. 

-   **EC**: “소프트” 타원 곡선 키입니다.
-   **EC-HSM**: “하드” 타원 곡선 키입니다.
-   **RSA**: “소프트” RSA 키입니다.
-   **RSA-HSM**: “하드” RSA 키입니다.

Key Vault는 크기가 2048, 3072, 4096인 RSA 키를 지원합니다. Key Vault는 타원 곡선 키 유형 p-256, p-384, p-521 및 P-256K(SECP256K1)를 지원합니다.

Key Vault에서 사용하는 암호화 모듈(HSM 또는 소프트웨어)은 FIPS에 유효합니다. FIPS 모드에서 실행하기 위해 특별한 작업을 수행할 필요가 없습니다. HSM으로 보호되는 키로 **만들어진** 또는 **가져온** 키는 FIPS 140-2 Level 2에서 유효한 HSM 내부에서 처리됩니다. 소프트웨어로 보호되는 키로 **만들어진** 또는 **가져온** 키는 FIPS 140-2 Level 1에서 유효한 암호화 모듈 내부에서 처리됩니다.

###  <a name="ec-algorithms"></a>EC 알고리즘
 Key Vault의 EC 및 EC-HSM 키에서 지원되는 알고리즘 식별자는 다음과 같습니다. 

#### <a name="curve-types"></a>곡선 유형

-   **P-256** - [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf)에 정의되는 NIST 곡선 P-256.
-   **P-256K** - [SEC 2: Recommended Elliptic Curve Domain Parameters](https://www.secg.org/sec2-v2.pdf)에 정의되는 SEC 곡선 SECP256K1.
-   **P-384** - [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf)에 정의되는 NIST 곡선 P-384.
-   **P-521** - [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf)에 정의되는 NIST 곡선 P-521.

#### <a name="signverify"></a>SIGN/VERIFY

-   **ES256** - 곡선 P-256을 사용하여 생성된 SHA-256 메시지 다이제스트 및 키에 대한 ECDSA입니다. 이 알고리즘은 [RFC7518](https://tools.ietf.org/html/rfc7518)에 설명되어 있습니다.
-   **ES256K** - 곡선 P-256K를 사용하여 생성된 SHA-256 메시지 다이제스트 및 키에 대한 ECDSA입니다. 이 알고리즘 표준화 보류 중입니다.
-   **ES384** - 곡선 P-384를 사용하여 생성된 SHA-384 메시지 다이제스트 및 키에 대한 ECDSA입니다. 이 알고리즘은 [RFC7518](https://tools.ietf.org/html/rfc7518)에 설명되어 있습니다.
-   **ES512** - 곡선 P-521을 사용하여 생성된 SHA-512 메시지 다이제스트 및 키에 대한 ECDSA입니다. 이 알고리즘은 [RFC7518](https://tools.ietf.org/html/rfc7518)에 설명되어 있습니다.

###  <a name="rsa-algorithms"></a>RSA 알고리즘  
 Key Vault의 RSA 및 RSA-HSM 키에서 지원되는 알고리즘 식별자는 다음과 같습니다.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, ENCRYPT/DECRYPT

-   **RSA1_5** - RSAES-PKCS1-V1_5[RFC3447] 키 암호화입니다.  
-   **RSA-OAEP** - OAEP(Optimal Asymmetric Encryption Padding)[RFC3447]를 사용하는 RSAES입니다(A.2.1 섹션의 RFC 3447에 명시된 기본 매개 변수 포함). 이러한 기본 매개 변수는 SHA-1의 해시 함수와 SHA-1이 포함된 MGF1의 마스크 생성 함수를 사용합니다.  

#### <a name="signverify"></a>SIGN/VERIFY

-   **PS256** - [RFC7518](https://tools.ietf.org/html/rfc7518)에 설명된 대로 SHA-256과 함께 SHA-256 및 MGF1을 사용하는 RSASSA-PSS.
-   **PS384** - [RFC7518](https://tools.ietf.org/html/rfc7518)에 설명된 대로 SHA-384와 함께 SHA-384 및 MGF1을 사용하는 RSASSA-PSS.
-   **PS512** - [RFC7518](https://tools.ietf.org/html/rfc7518)에 설명된 대로 SHA-512와 함께 SHA-512 및 MGF1을 사용하는 RSASSA-PSS.
-   **RS256** - SHA-256을 사용하는 RSASSA-PKCS-v1_5입니다. 애플리케이션에서 제공하는 다이제스트 값은 SHA-256을 사용하여 계산되어야 하며, 길이는 32바이트여야 합니다.  
-   **RS384** - SHA-384를 사용하는 RSASSA-PKCS-v1_5입니다. 애플리케이션에서 제공하는 다이제스트 값은 SHA-384를 사용하여 계산되어야 하며, 길이는 48바이트여야 합니다.  
-   **RS512** - SHA-512를 사용하는 RSASSA-PKCS-v1_5입니다. 애플리케이션에서 제공하는 다이제스트 값은 SHA-512를 사용하여 계산되어야 하며, 길이는 64바이트여야 합니다.  
-   **RSNULL** - 특정 TLS 시나리오를 사용하려면 특수 사용 사례인 [RFC2437]을 참조하세요.  

##  <a name="key-operations"></a>키 작업

Key Vault에서 키 개체에 대해 지원하는 작업은 다음과 같습니다.  

-   **만들기**: 클라이언트가 Key Vault에 키를 만들 수 있습니다. 키 값은 Key Vault에서 생성되고 저장되며 클라이언트에 릴리스되지 않습니다. Key Vault에 비대칭 키를 만들 수 있습니다.  
-   **Import**: 클라이언트가 기존 키를 Key Vault로 가져올 수 있습니다. JWK 구조 내에서 다양한 패키징 메서드를 사용하여 Key Vault로 비대칭 키를 가져올 수 있습니다. 
-   **업데이트**: 충분한 권한이 있는 클라이언트는 이전에 Key Vault에 저장된 키와 연결된 메타데이터(키 특성)를 수정할 수 있습니다.  
-   **삭제**: 충분한 권한이 있는 클라이언트는 Key Vault에서 키를 삭제할 수 있습니다.  
-   **나열**: 클라이언트가 지정된 Key Vault의 모든 키를 나열할 수 있습니다.  
-   **버전 나열**: 클라이언트가 지정된 Key Vault의 지정된 키 버전을 모두 나열할 수 있습니다.  
-   **가져오기**: 클라이언트가 Key Vault에서 지정된 키의 공개 부분을 검색할 수 있습니다.  
-   **백업**: 키를 보호된 형식으로 내보냅니다.  
-   **복원**: 이전에 백업한 키를 가져옵니다.  

자세한 내용은 [Key Vault REST API 참조에서 키 작업](/rest/api/keyvault)을 참조하세요.  

키가 Key Vault에 만들어지면 해당 키를 사용하여 수행할 수 있는 암호화 작업은 다음과 같습니다.  

-   **서명 및 확인**: 엄격히 말해서, Key Vault는 서명 만들기의 일부로 콘텐츠의 해싱을 지원하지 않으므로 이 작업은 “해시 서명” 또는 “해시 확인”입니다. 애플리케이션은 로컬로 서명할 데이터를 해시한 다음, Key Vault에서 이 해시에 서명하도록 요청해야 합니다. [공개] 키 자료에 액세스할 수 없는 애플리케이션을 편리하게 사용할 수 있도록 서명된 해시 확인이 지원됩니다. 애플리케이션 성능을 최적화하려면 VERIFY 작업을 로컬로 수행해야 합니다.  
-   **키 암호화/래핑**: Key Vault에 저장된 키는 다른 키(일반적으로 대칭 CEK(콘텐츠 암호화 키))를 보호하는 데 사용할 수 있습니다. Key Vault의 키가 비대칭이면 키 암호화가 사용됩니다. 예를 들어 RSA-OAEP 및 WRAPKEY/UNWRAPKEY 작업은 ENCRYPT/DECRYPT와 동일합니다. Key Vault의 키가 대칭이면 키 래핑이 사용됩니다. 예: AES-KW. [공개] 키 자료에 액세스할 수 없는 애플리케이션을 편리하게 사용할 수 있도록 WRAPKEY 작업이 지원됩니다. 애플리케이션 성능을 최적화하려면 WRAPKEY 작업을 로컬로 수행해야 합니다.  
-   **암호화 및 암호 해독**: Key Vault에 저장된 키는 데이터의 단일 블록을 암호화 또는 암호 해독하는 데 사용할 수 있습니다. 블록의 크기는 키 유형과 선택한 암호화 알고리즘에 따라 결정됩니다. [공개] 키 자료에 액세스할 수 없는 애플리케이션을 간편하게 사용할 수 있도록 암호화 작업이 제공됩니다. 애플리케이션 성능을 최적화하려면 ENCRYPT 작업을 로컬로 수행해야 합니다.  

비대칭 키를 사용하는 WRAPKEY/UNWRAPKEY가 필요 없는 것처럼 보일 수도 있지만(작업이 ENCRYPT/DECRYPT와 동일하므로), 구분되는 작업을 사용하는 것이 중요합니다. 구분을 통해 이러한 작업의 의미 체계 및 권한 부여를 분리하고, 서비스에서 다른 키 유형을 지원할 때 일관성을 유지할 수 있습니다.  

Key Vault는 EXPORT 작업을 지원하지 않습니다. 일단 키가 시스템에 프로비전되면 키를 추출하거나 키 자료를 수정할 수 없습니다. 그러나 Key Vault 사용자가 다른 사용 사례에 사용하기 위해 키가 필요할 수 있습니다(예: 키가 삭제된 후). 이 경우 BACKUP 및 RESTORE 작업을 사용하여 키를 보호되는 형식으로 내보낼/가져올 수 있습니다. BACKUP 작업으로 만든 키는 Key Vault 외부에서 사용할 수 없습니다. 또는 IMPORT 작업을 여러 Key Vault 인스턴스에 사용할 수 있습니다.  

사용자는 Key Vault에서 JWK 개체의 key_ops 속성을 사용하여 키별로 지원하는 암호화 작업을 제한할 수 있습니다.  

JWK 개체에 대한 자세한 내용은 [JWK(JSON 웹 키)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)를 참조하세요.  

## <a name="key-attributes"></a>키 특성

키 자료 외에도 지정할 수 있는 특성은 다음과 같습니다. JSON 요청에서 특성이 지정되지 않은 경우에도 특성 키워드 및 중괄호 '{' '}'가 필요합니다.  

- *enabled*: 부울, 선택 사항, 기본값은 **true**입니다. 암호화 작업을 위해 사용 설정 및 사용 가능한 키인지 여부를 지정합니다. *enabled* 특성은 *nbf* 및 *exp*와 함께 사용됩니다. *nbf* 및 *exp* 간에 작업이 발생하면 *enabled*가 **true**로 설정된 경우에만 해당 작업이 허용됩니다. [특정 조건](#date-time-controlled-operations)에 따른 특정 작업 유형을 제외하고는 *nbf* / *exp* 시간 범위에 속하지 않은 작업이 자동으로 허용되지 않습니다.
- *nbf*: IntDate, 선택 사항, 기본값은 now입니다. *nbf*(이전이 아님) 특성은 [특정 조건](#date-time-controlled-operations)에 따른 특정 작업 유형을 제외하고는 암호화 작업에 키를 사용하지 않아야 하는 시간을 식별합니다. *nbf* 특성을 처리하려면 현재 날짜/시간이 *nbf* 특성에 나열된 날짜/시간 이전이 아닌 시간 이후이거나 같아야 합니다. Key Vault는 클록 오차를 처리하기 위해 일반적으로 약간(몇 분 이내)의 여유를 제공해야 합니다. 이 값은 IntDate 값이 포함된 숫자여야 합니다.  
- *exp*: IntDate, 선택 사항, 기본값은 “forever”입니다. *exp*(만료 시간) 특성은 [특정 조건](#date-time-controlled-operations)에 따른 특정 작업 유형을 제외하고는 암호화 작업에 키를 사용하지 않아야 하는 만료 시간 또는 그 이후를 식별합니다. *exp* 특성을 처리하려면 현재 날짜/시간이 *exp* 특성에 나열된 만료 날짜/시간 이전이어야 합니다. Key Vault는 클록 오차를 처리하기 위해 일반적으로 약간(몇 분 이내)의 여유를 제공해야 합니다. 이 값은 IntDate 값이 포함된 숫자여야 합니다.  

키 특성이 포함된 모든 응답에 포함되는 추가 읽기 전용 특성이 있습니다.  

- *created*: IntDate, 선택 사항입니다. *created* 특성은 이 버전의 키를 만든 시점을 나타냅니다. 이 특성을 추가하기 전에 만든 키의 값은 null입니다. 이 값은 IntDate 값이 포함된 숫자여야 합니다.  
- *updated*: IntDate, 선택 사항입니다. *updated* 특성은 이 버전의 키를 업데이트한 시점을 나타냅니다. 이 특성을 추가하기 전에 마지막으로 업데이트한 키의 값은 null입니다. 이 값은 IntDate 값이 포함된 숫자여야 합니다.  

IntDate 및 기타 데이터 형식에 대한 자세한 내용은 [키, 비밀 및 인증서 정보]를 참조하세요. [데이터 형식](../general/about-keys-secrets-certificates.md#data-types).

### <a name="date-time-controlled-operations"></a>날짜 및 시간 제어 작업

*nbf* / *exp* 시간 범위에 속하지 않은 아직 유효하지 않은 키와 만료된 키는 **decrypt**, **unwrap** 및 **verify** 작업에 대해 작동합니다(403, 금지됨 오류를 반환하지 않음). 아직 유효하지 않은 상태를 사용하는 이유는 프로덕션에서 사용하기 전에 키를 테스트할 수 있도록 하기 위한 것입니다. 만료된 상태를 사용하는 이유는 키가 유효할 때 만들어진 데이터에 대한 복구 작업을 허용하기 위한 것입니다. 또한 Key Vault 정책을 사용하거나 *enabled* 키 특성을 **false**로 업데이트하여 키에 대한 액세스를 사용하지 않도록 설정할 수 있습니다.

데이터 형식에 대한 자세한 내용은 [데이터 형식](../general/about-keys-secrets-certificates.md#data-types)을 참조하세요.

사용 가능한 다른 특성에 대한 자세한 내용은 [JWK(JSON 웹 키)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)를 참조하세요.

## <a name="key-tags"></a>키 태그

애플리케이션 관련 메타데이터를 태그 형식으로 추가로 지정할 수 있습니다. Key Vault는 최대 15개의 태그를 지원하며, 각 태그는 256자 이름과 256자의 값을 가질 수 있습니다.  

>[!Note]
>해당 개체 형식(키, 비밀 또는 인증서)에 대한 *list* 또는 *get* 권한이 태그에 있는 경우 호출자에서 해당 태그를 읽을 수 있습니다.

##  <a name="key-access-control"></a>키 액세스 제어

Key Vault에서 관리하는 키에 대한 액세스 제어는 키 컨테이너 역할을 하는 Key Vault 수준에서 제공됩니다. 키에 대한 액세스 제어 정책은 동일한 Key Vault의 비밀에 대한 액세스 제어 정책과 다릅니다. 사용자는 키를 보관할 하나 이상의 자격 증명 모음을 만들 수 있으며, 시나리오에 따라 키를 적절하게 세분화하고 관리해야 합니다. 키 액세스 제어는 비밀 액세스 제어와 무관합니다.  

자격 증명 모음의 키 액세스 제어 항목에 대해 사용자/서비스 사용자별로 부여할 수 있는 권한은 다음과 같습니다. 이러한 권한은 키 개체에 허용되는 작업을 밀접하게 미러링합니다.  키 자격 증명 모음에서 서비스 주체에 대한 액세스 권한을 부여하는 작업은 일회성 작업이며 모든 Azure 구독에 대해 동일하게 유지됩니다. 이를 사용하여 원하는 만큼의 인증서를 배포할 수 있습니다. 

- 키 관리 작업에 필요한 권한
  - *get*: 키의 공개 부분 및 해당 특성 읽기
  - *list*: 키 자격 증명 모음에 저장된 키 또는 키 버전 나열
  - *update*: 키 특성 업데이트
  - *create*: 새 키 만들기
  - *import*: 키 자격 증명 모음으로 키 가져오기
  - *delete*: 키 개체 삭제
  - *recover*: 삭제된 키 복구
  - *backup*: 키를 키 자격 증명 모음에 백업
  - *restore*: 키 자격 증명 모음에 백업된 키 복원

- 암호화 작업에 필요한 권한
  - *decrypt*: 키를 사용하여 바이트 시퀀스 보호 해제
  - *encrypt*: 키를 사용하여 임의의 바이트 시퀀스 보호
  - *unwrapKey*: 키를 사용하여 래핑된 대칭 키 보호 해제
  - *wrapKey*: 키를 사용하여 대칭 키 보호
  - *verify*: 키를 사용하여 다이제스트 확인  
  - *sign*: 키를 사용하여 다이제스트에 서명
    
- 권한 있는 작업에 필요한 권한
  - *purge*: 삭제된 키 제거(영구적으로 삭제)

키 사용에 대한 자세한 내용은 [Key Vault REST API 참조에서 키 작업](/rest/api/keyvault)을 참조하세요. 권한 설정에 대한 내용은 [자격 증명 모음 - 만들기 또는 업데이트](/rest/api/keyvault/vaults/createorupdate) 및 [자격 증명 모음 - 액세스 정책 업데이트](/rest/api/keyvault/vaults/updateaccesspolicy)를 참조하세요. 

## <a name="next-steps"></a>다음 단계

- [Key Vault 정보](../general/overview.md)
- [키, 비밀 및 인증서에 대한 정보](../general/about-keys-secrets-certificates.md)
- [비밀 정보](../secrets/about-secrets.md)
- [인증서 정보](../certificates/about-certificates.md)
- [인증, 요청 및 응답](../general/authentication-requests-and-responses.md)
- [Key Vault 개발자 가이드](../general/developers-guide.md)
