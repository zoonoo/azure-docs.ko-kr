---
title: Azure Key Vault에 HSM 보호 키를 생성하고 전송하는 방법 - Azure Key Vault | Microsoft Docs
description: 이 문서를 사용하면 Azure Key Vault에서 사용할 HSM 보호 키를 계획, 생성 및 전송할 수 있습니다. 자신의 키(BYOK)를 가져오라고도 합니다.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 77568928e50fb4398aa786dbbd4a44b9277a8d5b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429708"
---
# <a name="import-hsm-protected-keys-to-key-vault-preview"></a>Key Vault로 HSM 보호 키 가져오기(미리 보기)

> [!NOTE]
> 이 기능은 미리 보기로 제공되며 *Azure 지역인 미국 동부 2 EUAP* 및 *미국 중부 EUAP에서만*사용할 수 있습니다. 

Azure Key Vault를 사용할 때 추가 보증을 위해 하드웨어 보안 모듈(HSM)에서 키를 가져오거나 생성할 수 있습니다. 키는 HSM 경계를 벗어나지 않습니다. 이 시나리오를 종종 *BYOK(사용자 고유의 키 가져오기)라고 합니다.* 키 볼트는 nCipher nShield 제품군인 HSM(FIPS 140-2 레벨 2 검증)을 사용하여 키를 보호합니다.

이 문서의 정보를 사용하여 Azure Key Vault와 함께 사용할 HSM 보호 키를 계획, 생성 및 전송할 수 있습니다.

> [!NOTE]
> Azure China 21Vianet에는 이 기능을 사용할 수 없습니다. 
> 
> 이 가져오기 메서드는 [지원되는 HSM에서만](#supported-hsms)사용할 수 있습니다. 

자세한 내용 및 키 볼트(HSM 로 보호된 키에 대한 키 자격 증명 모음을 만드는 방법 포함)를 사용하여 시작하는 자습서에 대한 자세한 [내용은 Azure 키 자격 증명 모음이란 무엇입니까?](../general/overview.md)

## <a name="overview"></a>개요

프로세스의 개요는 다음과 같습니다. 완료할 구체적인 단계는 문서의 후반부에서 설명합니다.

* 키 볼트에서 키를 *생성합니다(KEK(키 교환 키)라고* 함). KEK는 키 작업만 있는 RSA-HSM `import` 키여야 합니다. 키 볼트 프리미엄 SKU만 RSA-HSM 키를 지원합니다.
* KEK 공개 키를 .pem 파일로 다운로드합니다.
* KEK 공개 키를 온-프레미스 HSM에 연결된 오프라인 컴퓨터로 전송합니다.
* 오프라인 컴퓨터에서 HSM 공급업체에서 제공하는 BYOK 도구를 사용하여 BYOK 파일을 만듭니다. 
* 대상 키는 KEK로 암호화되며 키 볼트 HSM로 전송될 때까지 암호화된 상태로 유지됩니다. 키의 암호화된 버전만 온-프레미스 HSM을 남깁니다.
* 키 볼트 HSM 내에서 생성된 KEK는 내보낼 수 없습니다. HSM은 키 볼트 HSM 외부에 KEK의 명확한 버전이 존재하지 않는다는 규칙을 적용합니다.
* KEK는 대상 키를 가져올 동일한 키 자격 증명 모음에 있어야 합니다.
* BYOK 파일이 키 볼트에 업로드되면 Key Vault HSM은 KEK 개인 키를 사용하여 대상 키 자료를 해독하고 HSM 키로 가져옵니다. 이 작업은 키 볼트 HSM 내에서 전적으로 발생합니다. 대상 키는 항상 HSM 보호 경계에 유지됩니다.

## <a name="prerequisites"></a>사전 요구 사항

다음 표에는 Azure 키 자격 증명 모음에서 BYOK를 사용하기 위한 필수 구성 조건이 나열되어 있습니다.

| 요구 사항 | 자세한 정보 |
| --- | --- |
| Azure 구독 |Azure 키 자격 증명 모음에서 키 자격 증명 모음을 만들려면 Azure 구독이 필요합니다. [평가판에 가입](https://azure.microsoft.com/pricing/free-trial/)합니다. |
| HSM 보호 키를 가져오는 키 볼트 프리미엄 SKU |Azure 키 자격 증명 모음의 서비스 계층 및 기능에 대한 자세한 내용은 [키 자격 증명 모음 가격 책정](https://azure.microsoft.com/pricing/details/key-vault/)을 참조하십시오. |
| 지원되는 HSM 목록의 HSM 및 BYOK 도구 및 HSM 공급업체에서 제공하는 지침 | HSM에 대한 사용 권한과 HSM 사용 방법에 대한 기본 지식이 있어야 합니다. [지원되는 HSM을](#supported-hsms)참조하십시오. |
| Azure CLI 버전 2.1.0 이상 | [Azure CLI 설치를](/cli/azure/install-azure-cli?view=azure-cli-latest)참조하십시오.|

## <a name="supported-hsms"></a>지원되는 HSM

|공급업체 이름|공급업체 유형|지원되는 HSM 모델|자세한 정보|
|---|---|---|---|
|탈 레 스|제조업체|펌웨어 버전 7.3 이상과 SafeNet 루나 HSM 7 제품군| [세이프넷 루나 BYOK 도구 및 문서](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|포르타인스 주|서비스로서의 HSM|셀프 디방어 키 관리 서비스(SDKMS)|[BYOK용 클라우드 공급자에게 SDKMS 키 내보내기 - Azure 키 볼트](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|


> [!NOTE]
> HSM의 nCipher nShield 제품군에서 HSM으로 보호된 키를 가져오려면 [레거시 BYOK 절차를](hsm-protected-keys-legacy.md)사용합니다.

## <a name="supported-key-types"></a>지원되는 키 유형

|키 이름|키 유형|키 크기|원본|Description|
|---|---|---|---|---|
|키 교환 키 (KEK)|RSA| 2,048비트<br />3,072비트<br />4,096비트|Azure 키 볼트 HSM|Azure 키 볼트에서 생성된 HSM 지원 RSA 키 쌍|
|대상 키|RSA|2,048비트<br />3,072비트<br />4,096비트|벤더 HSM|Azure 키 볼트 HSM으로 전송할 키|

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>키 볼트 HSM으로 키 생성 및 전송

키 볼트 HSM으로 키를 생성하고 전송하려면 다음을 수행하십시오.

* [1단계: KEK 생성](#step-1-generate-a-kek)
* [2단계: KEK 공개 키 다운로드](#step-2-download-the-kek-public-key)
* [3단계: 전송을 위해 키 생성 및 준비](#step-3-generate-and-prepare-your-key-for-transfer)
* [4단계: 키를 Azure 키 자격 증명 모음으로 이전](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>1단계: KEK 생성

KEK는 키 볼트 HSM에서 생성되는 RSA 키입니다. KEK는 가져올 *키(대상* 키)를 암호화하는 데 사용됩니다.

KEK는 다음과 같은 것이어야 합니다.
- RSA-HSM 키(2,048비트, 3,072비트 또는 4,096비트)
- 대상 키를 가져올 동일한 키 자격 증명 모음에서 생성
- 허용된 키 작업을 설정하여 생성됨`import`

> [!NOTE]
> KEK는 유일한 허용 키 작업으로 '가져오기'가 있어야합니다. '가져오기'는 다른 모든 키 작업과 상호 배타적입니다.

az [키볼트 만들기](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) 명령을 사용하여 키 작업이 로 설정된 `import`KEK를 만듭니다. 다음 명령에서 반환되는 키 식별자 ()를`kid`기록합니다. [(3단계에서](#step-3-generate-and-prepare-your-key-for-transfer)값을 `kid` 사용합니다.)

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>2단계: KEK 공개 키 다운로드

[az keyvault 키 다운로드를](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download) 사용하여 KEK 공개 키를 .pem 파일에 다운로드합니다. 가져오는 대상 키는 KEK 공개 키를 사용하여 암호화됩니다.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

KEKforBYOK.publickey.pem 파일을 오프라인 컴퓨터로 전송합니다. 다음 단계에서이 파일이 필요합니다.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>3단계: 전송을 위해 키 생성 및 준비

BYOK 도구를 다운로드하여 설치하려면 HSM 공급업체의 설명서를 참조하십시오. HSM 공급업체의 지침에 따라 대상 키를 생성한 다음 BYOK 파일(BYOK 파일)을 만듭니다. BYOK 도구는 `kid` [1단계에서](#step-1-generate-a-kek) 다운로드한 KEKforBYOK.publickey.pem 파일을 사용하여 [Step 2](#step-2-download-the-kek-public-key) BYOK 파일에서 암호화된 대상 키를 생성합니다.

BYOK 파일을 연결된 컴퓨터로 전송합니다.

> [!NOTE] 
> RSA 1,024비트 키 가져오기는 지원되지 않습니다. 현재 는 EC(타원 곡선) 키 를 가져오는 것이 지원되지 않습니다.
> 
> **알려진 문제**: SafeNet Luna HSM에서 RSA 4K 대상 키를 가져오는 것은 펌웨어 7.4.0 이상에서만 지원됩니다.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>4단계: 키를 Azure 키 자격 증명 모음으로 이전

키 가져오기를 완료하려면 연결이 끊긴 컴퓨터에서 인터넷에 연결된 컴퓨터로 키 전송 패키지(BYOK 파일)를 전송합니다. az [키볼트 키 가져오기](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) 명령을 사용하여 BYOK 파일을 키 볼트 HSM에 업로드합니다.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

업로드가 성공하면 Azure CLI는 가져온 키의 속성을 표시합니다.

## <a name="next-steps"></a>다음 단계

이제 주요 자격 증명 모음에서 이 HSM 보호된 키를 사용할 수 있습니다. 자세한 내용은 [이 가격 및 기능 비교를](https://azure.microsoft.com/pricing/details/key-vault/)참조하십시오.



