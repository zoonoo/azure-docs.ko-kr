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
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 048e5072c592cf2de32e533014c99034572a1c47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082900"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>HSM으로 보호된 키를 키 볼트로 가져오기

보안을 강화하기 위해 Azure Key Vault 사용 시 HSM 경계를 절대로 벗어나지 않고 HSM(하드웨어 보안 모듈)에서 키를 가져오거나 생성할 수 있습니다. 이 시나리오를 흔히 BYOK( *Bring Your Own Key*)라고 합니다. Azure 키 볼트는 nCipher nShield 제품군인 HSM(FIPS 140-2 수준 2 검증)을 사용하여 키를 보호합니다.

Azure China 21Vianet에는 이 기능을 사용할 수 없습니다.

> [!NOTE]
> Azure 키 자격 증명 모음에 대한 자세한 내용은 [Azure 키 자격 증명 모음이란 무엇입니까?](key-vault-overview.md)  
> HSM 보호 키에 대한 자격 증명 모음을 만드는 내용이 포함된 자습서를 시작하려면 [Azure Key Vault란?](key-vault-overview.md)을 참조하세요.

## <a name="supported-hsms"></a>지원되는 HSM

HSM으로 보호된 키를 키 볼트로 전송하는 것은 사용하는 HSM에 따라 두 가지 방법을 통해 지원됩니다. 아래 표를 사용하여 HSM에서 생성할 방법을 결정한 다음 Azure Key Vault에서 사용할 HSM 보호 키를 직접 전송합니다. 

|공급업체 이름|공급업체 유형|지원되는 HSM 모델|지원되는 HSM 키 전송 방법|
|---|---|---|---|
|Ncipher|제조업체|<ul><li>hsm의 nShield 제품군</li></ul>|[레거시 BYOK 방법 사용](hsm-protected-keys-legacy.md)|
|탈 레 스|제조업체|<ul><li>펌웨어 버전 7.3 이상과 SafeNet 루나 HSM 7 제품군</li></ul>| [새 BYOK 방법 사용(미리 보기)](hsm-protected-keys-vendor-agnostic-byok.md)|
|포르타인스 주|서비스로서의 HSM|<ul><li>셀프 디방어 키 관리 서비스(SDKMS)</li></ul>|[새 BYOK 방법 사용(미리 보기)](hsm-protected-keys-vendor-agnostic-byok.md)|










## <a name="next-steps"></a>다음 단계

[키 볼트 모범 사례를](key-vault-best-practices.md) 따라 키에 대한 보안, 내구성 및 모니터링을 보장합니다.
