---
title: Azure Key Vault에 HSM 보호 키를 생성하고 전송하는 방법 - Azure Key Vault | Microsoft Docs
description: 이 문서를 통해 Azure Key Vault에서 사용할 고유의 HSM 보호 키를 생성하고 전송하는 데 필요한 계획을 세울 수 있습니다. BYOK, 즉 Bring Your Own Key라고도 합니다.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: bd70cfb58c9d89f1d454537721e22f36b1fd3d3e
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77429294"
---
# <a name="import-hsm-protected-keys-to-key-vault-preview"></a>Key Vault (미리 보기)에 대 한 HSM 보호 된 키 가져오기

> [!NOTE]
> 이 기능은 미리 보기 상태 이며 **미국 동부 2 EUAP** 및 **미국 중부 EUAP** 지역 에서만 사용할 수 있습니다. 

Azure Key Vault를 사용할 때 보증을 추가 하려면 HSM 경계를 떠나지 않는 Hsm (하드웨어 보안 모듈)에서 키를 가져오거나 생성할 수 있습니다. 이 시나리오를 흔히 BYOK( *Bring Your Own Key*)라고 합니다. Azure Key Vault는 nCipher nShield 집합 (FIPS 140-2 Level 2 유효성 검사)을 사용 하 여 키를 보호 합니다.

이 토픽의 내용을 통해 Azure Key Vault에서 사용할 고유의 HSM 보호된 키를 생성하고 전송하는 데 필요한 계획을 세울 수 있습니다.

> [!NOTE]
> Azure 중국 21Vianet에는이 기능을 사용할 수 없습니다. 
> 
> 이 가져오기 방법은 [지원 되는 hsm](#supported-hsms)에 대해서만 사용할 수 있습니다. 

Azure Key Vault에 대한 자세한 내용은 [Azure Key Vault란?](key-vault-overview.md)  HSM 보호 키에 대한 자격 증명 모음을 만드는 내용이 포함된 자습서를 시작하려면 [Azure Key Vault란?](key-vault-overview.md)을 참조하세요.

## <a name="overview"></a>개요

* 키 자격 증명 모음에서 키 (키 교환 키 또는 KEK 라고도 함)를 생성 합니다. 유일한 키 작업으로 ' 가져오기 '가 포함 된 RSA-HSM 키 여야 합니다. Key vault premium SKU만 RSA-HSM 키를 지원 합니다.
* KEK의 공개 키를 pem 파일로 다운로드 합니다.
* KEK 공개 키를 온-프레미스 HSM에 연결 된 오프 라인 워크스테이션으로 전송 합니다.
* 오프 라인 워크스테이션에서 HSM 공급 업체에서 제공한 BYOK 도구를 사용 하 여 BYOK 파일을 만듭니다. 
* 대상 키는 Azure Key Vault Hsm으로 전송 될 때까지 암호화 상태를 유지 하는 KEK로 암호화 됩니다. 암호화 된 버전의 키만 온-프레미스 HSM을 벗어납니다.
* Azure Key Vault Hsm 내에서 생성 된 KEK는 내보낼 수 없습니다. Hsm은 Key Vault Hsm 외부에 KEK의 일반 버전이 있을 수 없도록 합니다.
* KEK는 대상 키를 가져올 동일한 키 자격 증명 모음에 있어야 합니다.
* BYOK 파일이 Key Vault에 업로드 되 면 Hsm Key Vault KEK 개인 키를 사용 하 여 대상 키 자료를 해독 하 고 HSM 키로 가져옵니다. 이 작업은 전적으로 Key Vault Hsm 내에서 수행 되며 대상 키는 항상 HSM 보호 경계에 남아 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure Key Vault에 대해 BYOK(Bring Your Own Key)를 위한 필수 조건 목록은 다음 표를 참조하세요.

| 요구 사항 | 자세한 정보 |
| --- | --- |
| Azure 구독 |Azure Key Vault를 만들려면 Azure 구독이 필요합니다([무료 평가판 가입](https://azure.microsoft.com/pricing/free-trial/)). |
| HSM 보호 키를 가져오기 위한 주요 자격 증명 모음 (프리미엄 SKU) |Azure Key Vault에 대한 서비스 계층 및 기능에 대한 자세한 내용은 [Azure Key Vault 가격 책정](https://azure.microsoft.com/pricing/details/key-vault/) 웹 사이트를 참조하세요. |
| BYOK 도구 및 HSM 공급 업체에서 제공 하는 지침과 함께 지원 되는 Hsm 목록의 HSM | 하드웨어 보안 모듈에 대 한 액세스 권한 및 Hsm의 기본적인 작동 지식이 있어야 합니다. [지원 되는 hsm](#supported-hsms)을 참조 하세요. |
| Azure CLI 버전 2.0.82 이상 | 자세한 내용은 [Azure CLI 설치를](/cli/azure/install-azure-cli?view=azure-cli-latest) 참조 하세요.|

## <a name="supported-hsms"></a>지원 되는 Hsm

|HSM 공급 업체 이름|지원 되는 HSM 모델|추가 정보|
|---|---|---|
|Thales|펌웨어 버전 7.3 이상을 사용 하는 Luna HSM 7 제품군| [Luna BYOK BYOK 도구 및 설명서](https://safenet.gemalto.com/blah-blah)|


> [!NOTE]
> NCipher nShield 집합에서 HSM 보호 된 키를 가져오려면 [레거시 BYOK 절차를 사용](hsm-protected-keys-legacy.md) 합니다.


## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>키 생성 및 Azure Key Vault에 전송

다음 단계를 사용 하 여 키를 생성 하 고 Azure Key Vault HSM으로 전송 합니다.

* [1 단계: KEK 생성](#step-1-generate-a-kek)
* [2 단계: KEK 공개 키 다운로드](#step-2-download-kek-public-key)
* [3 단계: 전송할 키 생성 및 준비](#step-3-generate-and-prepare-your-key-for-transfer)
* [4 단계: Azure Key Vault에 키 전송](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>1 단계: KEK 생성

KEK (키 교환 키)는 Key Vault의 HSM에서 생성 되는 RSA 키입니다. 이 키는 가져올 키 (대상 키)를 암호화 하는 데 사용 됩니다.

KEK는 다음과 같아야 합니다.
1. **RSA HSM** 키 (2048 비트 또는 3072 비트 또는 4096 비트)
2. 대상 키를 가져오려는 동일한 키 자격 증명 모음에 생성 됩니다.
3. 허용 된 키 작업이 **가져오기** 로 설정 된 상태에서 만들어짐

[Az keyvault key create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) 명령을 사용 하 여 키 작업이 가져오기로 설정 된 KEK를 만듭니다. 아래 명령에서 반환 된 키 식별자 ' kid '를 적어둡니다. [3 단계](#step-3-generate-and-prepare-your-key-for-transfer)에서 필요 합니다.


```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-kek-public-key"></a>2 단계: KEK 공개 키 다운로드

[Az keyvault key download](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download) 를 사용 하 여 KEK 공개 키를 pem 파일로 다운로드 합니다. 가져오는 대상 키는 KEK 공개 키를 사용 하 여 암호화 됩니다.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

KEKforBYOK. publickey 파일을 오프 라인 워크스테이션으로 전송 합니다. 이 파일은 다음 단계에서 필요 합니다.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>3 단계: 전송할 키 생성 및 준비

BYOK 도구를 다운로드 하 고 설치 하려면 HSM 공급 업체의 설명서를 참조 하세요. HSM 공급 업체의 지침에 따라 대상 키를 생성 한 다음 키 전송 패키지 (BYOK 파일)를 만듭니다. BYOK 도구는 [2 단계](#step-2-download-kek-public-key) 에서 다운로드 한 [1 단계](#step-1-generate-a-kek) 및 kekforbyok. publickey 파일의 키 식별자를 사용 하 여 byok 파일에 암호화 된 대상 키를 생성 합니다.

BYOK 파일을 연결 된 워크스테이션으로 전송 합니다.

> [!NOTE] 
> 대상 키는 2048 비트 또는 3072 비트 또는 4096 비트의 RSA 키 여야 합니다. 지금은 타원 Curve 키 가져오기가 지원 되지 않습니다.
> <br/><strong>알려진 문제:</strong> Luna Hsm에서 RSA 4K 대상 키를 가져오는 작업이 실패 합니다. 문제가 해결 되 면이 문서가 업데이트 됩니다.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>4 단계: Azure Key Vault에 키 전송

이 마지막 단계에서는 연결이 끊어진 워크스테이션에서 인터넷에 연결 된 워크스테이션으로 키 전송 패키지 (BYOK 파일)를 전송 하 고 [az keyvault Key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) 명령을 사용 하 여 byok 파일 Azure Key Vault HSM을 업로드 하 고 키 가져오기를 완료 합니다.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

업로드가 성공적으로 완료 되 면 방금 가져온 키의 속성을 표시 하는 것을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 주요 자격 증명 모음에서 이 HSM 보호된 키를 사용할 수 있습니다. 자세한 내용은이 가격 및 기능 [비교](https://azure.microsoft.com/pricing/details/key-vault/)를 참조 하세요.
