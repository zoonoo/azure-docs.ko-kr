---
title: Azure Key Vault에 HSM 보호 키를 생성하고 전송하는 방법 - Azure Key Vault | Microsoft Docs
description: 이 문서를 통해 Azure Key Vault에서 사용할 고유의 HSM 보호 키를 생성하고 전송하는 데 필요한 계획을 세울 수 있습니다. BYOK, 즉 Bring Your Own Key라고도 합니다.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: 5433d9746cd64d0e942e056cfcd1940eba35c77d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84417925"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Key Vault로 HSM 보호 키 가져오기

보안을 강화하기 위해 Azure Key Vault 사용 시 HSM 경계를 절대로 벗어나지 않고 HSM(하드웨어 보안 모듈)에서 키를 가져오거나 생성할 수 있습니다. 이 시나리오를 흔히 BYOK( *Bring Your Own Key*)라고 합니다. Azure Key Vault는 nCipher nShield 집합 (FIPS 140-2 Level 2 유효성 검사)을 사용 하 여 키를 보호 합니다.

Azure 중국 21Vianet에는이 기능을 사용할 수 없습니다.

> [!NOTE]
> Azure Key Vault에 대한 자세한 내용은 [Azure Key Vault란?](../general/overview.md)  
> HSM 보호 키에 대한 자격 증명 모음을 만드는 내용이 포함된 자습서를 시작하려면 [Azure Key Vault란?](../general/overview.md)을 참조하세요.

## <a name="supported-hsms"></a>지원 되는 Hsm

HSM으로 보호 되는 키를 Key Vault으로 전송 하는 것은 사용 하는 Hsm에 따라 두 가지 방법을 통해 지원 됩니다. 아래 표를 사용 하 여 Hsm에서 생성 하는 데 사용 해야 하는 방법을 결정 한 다음 Azure Key Vault와 함께 사용 하기 위해 고유한 HSM 보호 키를 전송 합니다. 

|공급 업체 이름|공급 업체 유형|지원 되는 HSM 모델|지원 되는 HSM-키 전송 방법|
|---|---|---|---|
|[nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|Manufacturer<br/>HSM을 서비스로|<ul><li>nShield Hsm 제품군</li><li>nShield (서비스)</ul>|**방법 1:** [nCipher byok](hsm-protected-keys-ncipher.md) (키 가져오기 및 HSM 유효성 검사에 대 한 강력한 증명 포함)<br/>**방법 2:** [새 byok 메서드 사용](hsm-protected-keys-byok.md) |
|Thales|제조업체|<ul><li>Luna HSM 7 제품군 (펌웨어 버전 7.3 이상)</li></ul>| [새 BYOK 메서드 사용](hsm-protected-keys-byok.md)|
|Fortanix|Manufacturer<br/>HSM을 서비스로|<ul><li>자체 방어 키 관리 서비스 (SDKMS)</li><li>인 키</li></ul>|[새 BYOK 메서드 사용](hsm-protected-keys-byok.md)|
|Marvell|제조업체|모든 LiquidSecurity Hsm<ul><li>펌웨어 버전 2.0.4 이상을 이상</li><li>펌웨어 버전 3.2 이상</li></ul>|[새 BYOK 메서드 사용](hsm-protected-keys-byok.md)|
|Cryptomathic|ISV (엔터프라이즈 키 관리 시스템)|여러 HSM 브랜드 및 모델 포함<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>[자세한 내용은 Cryptomathic 사이트를](https://www.cryptomathic.com/azurebyok) 참조 하세요.|[새 BYOK 메서드 사용](hsm-protected-keys-byok.md)|


## <a name="next-steps"></a>다음 단계

* 키에 대 한 보안, 내구성 및 모니터링을 보장 하려면 [Key Vault 모범 사례](../general/best-practices.md) 를 따르세요.
* 새 BYOK 메서드에 대 한 전체 설명은 [byok 사양](https://docs.microsoft.com/azure/key-vault/keys/byok-specification) 을 참조 하세요.
