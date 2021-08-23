---
title: Bring Your Own Key 사양 - Azure Key Vault | Microsoft Docs
description: 이 문서는 Bring Your Own Key 사양을 가져오는 것에 대해 설명합니다.
services: key-vault
author: mbaldwin
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5dd1e284080f95f961a17e775e8ccf37debc6a62
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114471721"
---
# <a name="bring-your-own-key-specification"></a>사용자 고유 키 사양 가져오기

이 문서에서는 HSM으로 보호되는 키를 고객의 온-프레미스 HSM에서 Key Vault로 가져오기 위한 사양을 설명합니다.

## <a name="scenario"></a>시나리오

Key Vault 고객은 Azure 외부의 온-프레미스 HSM에서 HSM 지원 Azure Key Vault로 키를 안전하게 전송하려고 합니다. Key Vault 외부에서 생성된 키를 가져오는 프로세스를 일반적으로 BYOK(Bring Your Own Key)라고 합니다.

다음은 요구 사항입니다.
* 전송할 키는 HSM 외부에서 절대 일반 텍스트 형식으로 존재하지 않습니다.
* HSM 외부에서, 전송할 키는 항상 Azure Key Vault HSM에 보관된 키로 보호됩니다.

## <a name="terminology"></a>용어

|키 이름|키 유형|원본|Description|
|---|---|---|---|
|KEK(키 교환 키)|RSA|Azure Key Vault HSM|Azure Key Vault에서 생성된 HSM 지원 RSA 키 쌍
래핑 키|AES|공급업체 HSM|HSM 온-프레미스에서 생성된 [임시] AES 키
대상 키|RSA, EC, AES(관리되는 HSM만 해당)|공급업체 HSM|Azure Key Vault HSM으로 전송할 키

**키 교환 키**: BYOK 키를 가져올 키 자격 증명 모음에서 고객이 생성하는 HSM 지원 키입니다. 이 KEK에는 다음 속성이 있어야 합니다.

* RSA-HSM 키(4096비트, 3072비트 또는 2048비트)
* 고정된 key_ops(‘import’만 해당)가 있어 BYOK 동안에만 사용할 수 있습니다.
* 대상 키를 가져올 동일한 자격 증명 모음에 있어야 합니다.

## <a name="user-steps"></a>사용자 단계

키 전송을 수행하기 위해 사용자는 다음 단계를 수행합니다.

1. KEK를 생성합니다.
2. KEK의 공개 키를 검색합니다.
3. HSM 공급업체에서 제공한 BYOK 도구 사용 - KEK를 대상 HSM으로 가져오고 KEK로 보호되는 대상 키를 내보냅니다.
4. 보호되는 대상 키를 Azure Key Vault로 가져옵니다.

고객은 HSM 공급업체에서 제공한 BYOK 도구 및 설명서를 사용하여 3단계를 완료합니다. 키 전송 Blob(".byok" 파일)을 생성합니다.


## <a name="hsm-constraints"></a>HSM 제약 조건

기존 HSM은 관리하는 키에 다음을 포함하여 제약 조건을 적용할 수 있습니다.
* 키 랩 기반 내보내기를 허용하도록 HSM을 구성해야 할 수 있습니다.
* 제어된 내보내기를 허용하려면 HSM에 대해 대상 키를 CKA_EXTRACTABLE로 표시해야 할 수 있습니다.
* 일부 경우에서 KEK 및 래핑 키를 CKA_TRUSTED로 표시해야 할 수도 있습니다. 이를 통해 이는 HSM에서 키를 래핑하는 데 사용할 수 있습니다.

원본 HSM의 구성은 일반적으로 이 사양의 범위를 벗어납니다. Microsoft는 HSM 공급업체가 이러한 구성 단계를 포함하는 BYOK 도구와 함께 설명서를 생성할 것으로 기대합니다.

> [!NOTE]
> 아래에 설명된 1, 2, 4단계는 Azure PowerShell 및 Azure Portal과 같은 다른 인터페이스를 사용하여 수행할 수 있습니다. Key Vault SDK에서 동등한 기능을 사용하여 프로그래밍 방식으로 수행할 수도 있습니다.

### <a name="step-1-generate-kek"></a>1단계: KEK 생성

**az keyvault key create** 명령을 사용하여 키 작업이 가져오기로 설정된 KEK를 만듭니다. 아래 명령에서 반환된 키 식별자 'kid'를 기록해 둡니다.

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-retrieve-the-public-key-of-the-kek"></a>2단계: KEK의 공개 키 검색

KEK의 공개 키 부분을 다운로드하고 PEM 파일에 저장합니다.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

### <a name="steps-3-generate-key-transfer-blob-using-hsm-vendor-provided-byok-tool"></a>3단계: HSM 공급업체에서 제공하는 BYOK 도구를 사용하여 키 전송 Blob 생성

고객은 HSM 공급업체에서 제공한 BYOK 도구를 사용하여 키 전송 Blob(".byok" 파일로 저장됨)을 만듭니다. KEK 공개 키(.pem 파일 형식)는 이 도구에 대한 입력 중 하나입니다.

#### <a name="key-transfer-blob"></a>키 전송 Blob
장기적으로 Microsoft는 PKCS#11 CKM_RSA_AES_KEY_WRAP 메커니즘을 사용하여 대상 키를 Azure Key Vault로 전송하려고 합니다. 이 메커니즘이 단일 Blob을 생성하고 더 중요하게는 임시임이 보장되며 중간 AES 키가 두 HSM에서 처리되기 때문입니다. 이 메커니즘은 현재 일부 HSM에서 사용할 수 없지만 AES 키를 사용하여 CKM_AES_KEY_WRAP_PAD로 대상 키를 보호하고 CKM_RSA_PKCS_OAEP로 AES 키를 보호하는 조합은 동등한 Blob을 생성합니다.

대상 키 일반 텍스트는 키 유형에 따라 다릅니다. 
* RSA 키의 경우 PKCS#8 [RFC5208]로 래핑된 프라이빗 키 ASN.1 DER 인코딩 [RFC3447] 
* EC 키의 경우 PKCS#8 [RFC5208]로 래핑된 프라이빗 키 ASN.1 DER 인코딩 [RFC5915]
* 옥텟 키의 경우 키의 원시 바이트

그런 다음 일반 텍스트 키의 바이트는 CKM_RSA_AES_KEY_WRAP 메커니즘을 사용하여 변환됩니다.
* 임시 AES 키가 생성되고 SHA1과 함께 RSA-OAEP를 사용하여 래핑 RSA 키로 암호화됩니다.
* 인코딩된 일반 텍스트 키는 AES 키(패딩으로 AES 키 매핑 사용)를 사용하여 암호화됩니다.
* 암호화된 AES 키와 암호화된 일반 텍스트 키를 연결하여 최종 암호문 Blob을 생성합니다.

전송 Blob의 형식은 기본적으로 올바른 암호 해독을 위해 필요한 메타데이터를 서비스에 전달하기 위한 수단으로 JSON 웹 암호화 압축 직렬화(RFC7516)를 사용합니다.

CKM_RSA_AES_KEY_WRAP_PAD를 사용하는 경우 전송 Blob의 JSON 직렬화는 다음과 같습니다.

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

* kid = KEK의 키 식별자. Key Vault 키의 경우에는 다음과 같습니다. https://ContosoKeyVaultHSM.vault.azure.net/keys/mykek/eba63d27e4e34e028839b53fac905621
* alg = algorithm. 
* dir = 직접 모드, 즉 참조된 kid는 CKM_RSA_AES_KEY_WRAP의 정확한 표현인 암호문을 직접 보호하는 데 사용됩니다.
* Generator = BYOK 도구의 이름과 버전, 원본 HSM 제조업체 및 모델을 나타내는 정보 필드입니다. 이 정보는 문제 해결 및 지원에 사용하기 위한 것입니다.

JSON Blob은 확장명이 ".byok"인 파일에 저장되므로 ‘Add-AzKeyVaultKey’(PSH) 또는 ‘az keyvault key import’(CLI) 명령이 사용될 때 Azure PowerShell/CLI 클라이언트가 올바르게 처리합니다.

### <a name="step-4-upload-key-transfer-blob-to-import-hsm-key"></a>4단계: 키 전송 Blob을 업로드하여 HSM 키 가져오기

고객은 키 전송 Blob(".byok" 파일)을 온라인 워크스테이션으로 전송한 다음 **az keyvault key import** 명령을 실행하여 이 Blob을 새 HSM 지원 키로 Key Vault에 가져옵니다. 

RSA 키를 가져오려면 다음 명령을 사용합니다.
```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok --ops encrypt decrypt
```
EC 키를 가져오려면 키 유형과 곡선 이름을 지정해야 합니다.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file --kty EC-HSM --curve-name "P-256" KeyTransferPackage-ContosoFirstHSMkey.byok --ops sign verify
```


위의 명령을 실행하면 다음과 같이 REST API 요청이 전송됩니다.

```
PUT https://contosokeyvaulthsm.vault.azure.net/keys/ContosoFirstHSMKey?api-version=7.0
```

RSA 키를 가져올 때 요청 본문:
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

EC 키를 가져올 때 요청 본문:
```json
{
  "key": {
    "kty": "EC-HSM",
    "crv": "P-256",
    "key_ops": [
      "sign",
      "verify"
    ],
    "key_hsm": "<Base64 encoded BYOK_BLOB>"
  },
  "attributes": {
    "enabled": true
  }
}
```

“key_hsm” 값은 Base64 형식으로 인코딩된 KeyTransferPackage-ContosoFirstHSMkey.byok의 콘텐츠 전체입니다.

## <a name="references"></a>참조
- [Key Vault 개발자 가이드](../general/developers-guide.md)

## <a name="next-steps"></a>다음 단계
* 단계별 BYOK 지침: [HSM으로 보호된 키를 Key Vault로 가져오기(BYOK)](hsm-protected-keys-byok.md)

