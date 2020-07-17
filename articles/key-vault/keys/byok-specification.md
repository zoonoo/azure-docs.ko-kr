---
title: 사용자 고유의 키 사양 가져오기-Azure Key Vault | Microsoft Docs
description: 이 문서에서는 사용자 고유의 키 사양을 설명 합니다.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: 80796d852c07952b7100c6dd7802bc9279f3218c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84198790"
---
# <a name="bring-your-own-key-specification"></a>사용자 고유 키 사양 가져오기

이 문서에서는 고객의 온-프레미스 Hsm에서 Key Vault로 HSM 보호 된 키를 가져오기 위한 사양을 설명 합니다.

## <a name="scenario"></a>시나리오

Key Vault 고객은 Azure 외부의 온-프레미스 HSM에서 HSM 지원 Azure Key Vault 키를 안전 하 게 전송 하려고 합니다. Key Vault 외부에서 생성 된 키를 가져오는 프로세스는 일반적으로 BYOK (Bring Your Own Key) 라고 합니다.

요구 사항은 다음과 같습니다.
* 전송할 키는 HSM 외부에 있는 일반 텍스트 형식으로 존재 하지 않습니다.
* HSM 외부에서 전송할 키는 항상 Azure Key Vault HSM에 보관 된 키로 보호 됩니다.

## <a name="terminology"></a>용어

|키 이름|키 유형|원본|설명|
|---|---|---|---|
|키 교환 키 (KEK)|RSA|Azure Key Vault HSM|Azure Key Vault에서 생성 된 HSM 지원 RSA 키 쌍
키 래핑|AES|공급 업체 HSM|HSM에서 생성 된 [임시] AES 키-프레미스
대상 키|RSA, EC, AES|공급 업체 HSM|Azure Key Vault HSM으로 전송할 키입니다.

**키 교환 키**: 고객이 byok 키를 가져올 주요 자격 증명 모음에 생성 하는 HSM 지원 키입니다. 이 KEK에는 다음과 같은 속성이 있어야 합니다.

* RSA HSM 키 (4096 비트 또는 3072 비트 또는 2048 비트)입니다.
* BYOK 중에만 사용할 수 있도록 하는 고정 key_ops (' 가져오기 '만)이 (가) 있습니다.
* 대상 키를 가져올 동일한 자격 증명 모음에 있어야 합니다.

## <a name="user-steps"></a>사용자 단계

키 전송을 수행 하기 위해 사용자는 다음 단계를 수행 합니다.

1. KEK를 생성 합니다.
2. KEK의 공개 키를 검색 합니다.
3. HSM 공급 업체에서 BYOK 도구를 사용 하 여 KEK를 대상 HSM으로 가져오고 KEK로 보호 되는 대상 키를 내보냅니다.
4. Azure Key Vault 보호 된 대상 키를 가져옵니다.

고객은 HSM 공급 업체에서 제공한 BYOK 도구 및 설명서를 사용 하 여 3 단계를 완료 합니다. 키 전송 Blob ("byok" 파일)을 생성 합니다.


## <a name="hsm-constraints"></a>HSM 제약 조건

기존 HSM은 다음을 비롯 하 여 관리 하는 키에 제약 조건을 적용할 수 있습니다.
* 키 래핑 기반 내보내기를 허용 하도록 HSM을 구성 해야 할 수 있습니다.
* 제어 된 내보내기를 허용 하도록 HSM에 대해 대상 키를 CKA_EXTRACTABLE로 표시 해야 할 수 있습니다.
* KEK 및 래핑 키를 CKA_TRUSTED으로 표시 해야 하는 경우도 있습니다. 이를 통해 HSM에서 키를 래핑하는 데 사용할 수 있습니다.

원본 HSM의 구성은 일반적으로이 사양의 범위를 벗어납니다. Microsoft는 HSM 공급 업체가 이러한 구성 단계를 포함 하도록 BYOK 도구를 함께 제공 하는 것으로 예상 합니다.

> [!NOTE]
> 아래에서 설명 하는 1, 2, 4 단계는 Azure PowerShell 및 Azure Portal과 같은 다른 인터페이스를 사용 하 여 수행할 수 있습니다. Key Vault SDK에서 동일한 함수를 사용 하 여 프로그래밍 방식으로 수행할 수도 있습니다.

### <a name="step-1-generate-kek"></a>1 단계: KEK 생성

**Az keyvault key create** 명령을 사용 하 여 키 작업이 가져오기로 설정 된 KEK를 만듭니다. 아래 명령에서 반환 된 키 식별자 ' kid '를 적어둡니다.

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-retrieve-the-public-key-of-the-kek"></a>2 단계: KEK의 공개 키 검색

KEK의 공개 키 부분을 다운로드 하 고 PEM 파일에 저장 합니다.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

### <a name="steps-3-generate-key-transfer-blob-using-hsm-vendor-provided-byok-tool"></a>3 단계: HSM 공급 업체 제공 BYOK 도구를 사용 하 여 키 전송 blob 생성

고객은 HSM 공급 업체 제공 BYOK 도구를 사용 하 여 키 전송 blob ("byok" 파일로 저장 됨)를 만듭니다. KEK 공개 키 (pem 파일)는이 도구에 대 한 입력 중 하나입니다.

#### <a name="key-transfer-blob"></a>키 전송 Blob
Microsoft는 PKCS # 11 CKM_RSA_AES_KEY_WRAP 메커니즘을 사용 하 여 대상 키를 Azure Key Vault 전송 하는 데 사용 하려고 합니다 .이 메커니즘은 단일 blob을 생성 하 고, 더 중요 한 것은 중간 AES 키가 두 Hsm에 의해 처리 되 고 임시 상태가 되도록 보장 되기 때문입니다. 이 메커니즘은 현재 일부 Hsm에서 사용할 수 없지만, AES 키를 사용 하 고 AES CKM_RSA_PKCS_OAEP 키를 사용 하 여 CKM_AES_KEY_WRAP_PAD와 대상 키를 보호 하는 조합은 동일한 blob을 생성 합니다.

대상 키 일반 텍스트는 키 유형에 따라 달라 집니다. 
* RSA 키의 경우 PKCS # 8로 래핑된 개인 키 ASN RFC3447 DER 인코딩 [RFC5208] 
* EC 키의 경우 PKCS # 8로 래핑된 개인 키 ASN RFC5915 DER 인코딩 [RFC5208]
* 옥텟 키의 경우 키의 원시 바이트

일반 텍스트 키에 대 한 바이트는 CKM_RSA_AES_KEY_WRAP 메커니즘을 사용 하 여 변환 됩니다.
* 임시 AES 키가 생성 되 고 OAEP를 사용 하는 SHA1 RSA 키로 암호화 됩니다.
* 인코딩된 일반 텍스트 키는 안쪽 여백과 함께 AES 키 래핑을 사용 하 여 AES 키를 사용 하 여 암호화 됩니다.
* 암호화 된 AES 키 및 암호화 된 일반 텍스트 키가 연결 되어 최종 암호 텍스트 blob을 생성 합니다.

전송 blob의 형식은 주로 JSON RFC7516 (Web Encryption compact serialization)를 사용 하 여 올바른 암호 해독을 위해 서비스에 필요한 메타 데이터를 제공 합니다.

CKM_RSA_AES_KEY_WRAP_PAD를 사용 하는 경우 전송 blob의 JSON 직렬화는 다음과 같습니다.

```json
{
  "schema_version": "1.0.0",
  "header":
  {
    "kid": "<key identifier of the KEK>",
    "alg": "dir",
    "enc": "CKM_RSA_AES_KEY_WRAP"
  },
  "ciphertext":"BASE64URL(<ciphertext contents>)",
  "generator": "BYOK tool name and version; source HSM name and firmware version"
}

```

* kid = KEK의 키 식별자입니다. Key Vault 키의 경우 다음과 같습니다.https://ContosoKeyVaultHSM.vault.azure.net/keys/mykek/eba63d27e4e34e028839b53fac905621
* alg = 알고리즘. 
* dir = Direct 모드입니다. 즉, 참조 되는 kid는의 정확한 표현인 텍스트 텍스트를 직접 보호 하는 데 사용 CKM_RSA_AES_KEY_WRAP
* 생성기 = BYOK 도구와 원본 HSM 제조업체 및 모델의 이름 및 버전을 나타내는 정보를 제공 하는 필드입니다. 이 정보는 문제 해결 및 지원에 사용 하기 위한 것입니다.

JSON blob은 "AzKeyVaultKey ' (PSH) 또는 ' az keyvault key import ' (CLI) 명령이 사용 될 때 Azure PowerShell/CLI 클라이언트가 올바르게 처리 하도록". byok "확장을 포함 하는 파일에 저장 됩니다.

### <a name="step-4-upload-key-transfer-blob-to-import-hsm-key"></a>4 단계: 키 전송 blob을 업로드 하 여 HSM 키 가져오기

고객은 키 전송 Blob ("byok" 파일)를 온라인 워크스테이션으로 전송한 다음 **az keyvault key import** 명령을 실행 하 여이 blob을 Key Vault에 새 HSM 지원 키로 가져옵니다. 

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok --ops encrypt decrypt
```

위의 명령을 실행 하면 다음과 같이 REST API 요청을 보냅니다.

```
PUT https://contosokeyvaulthsm.vault.azure.net/keys/ContosoFirstHSMKey?api-version=7.0
```

본문 요청:
```json
{
  "key": {
    "kty": "RSA-HSM",
    "key_ops": [
      "decrypt",
      "encrypt"
    ],
    "key_hsm": "<Base64 encoded BYOK_BLOB>"
  },
  "attributes": {
    "enabled": true
  }
}
```
"key_hsm" 값은 Base64 형식으로 인코딩된 KeyContosoFirstHSMkey의 전체 내용입니다.

## <a name="references"></a>참조

### <a name="azure-key-vault-rest-api"></a>Azure Key Vault REST API

* [키 만들기](https://docs.microsoft.com/rest/api/keyvault/createkey/createkey)
* [키 가져오기 (키 특성 및 공개 키만 해당)](https://docs.microsoft.com/rest/api/keyvault/getkey/getkey)
* [키 가져오기](https://docs.microsoft.com/rest/api/keyvault/importkey/importkey)


### <a name="azure-cli-commands"></a>Azure CLI 명령
* [az keyvault key create](https://docs.microsoft.com/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create)
* [az keyvault key 다운로드](https://docs.microsoft.com/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download)
* [az keyvault key import](https://docs.microsoft.com/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import)


## <a name="next-steps"></a>다음 단계
* 단계별 BYOK 지침: [HSM 보호 된 키를 Key Vault으로 가져오기 (BYOK)](hsm-protected-keys-byok.md)

