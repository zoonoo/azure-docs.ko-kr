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
ms.openlocfilehash: 58cf3358a9e908070ce9003d05dd0b576b1d2d3f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81429695"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Key Vault로 HSM 보호 키 가져오기

보안을 강화하기 위해 Azure Key Vault 사용 시 HSM 경계를 절대로 벗어나지 않고 HSM(하드웨어 보안 모듈)에서 키를 가져오거나 생성할 수 있습니다. 이 시나리오를 흔히 BYOK( *Bring Your Own Key*)라고 합니다. Azure Key Vault는 nCipher nShield 집합 (FIPS 140-2 Level 2 유효성 검사)을 사용 하 여 키를 보호 합니다.

Azure 중국 21Vianet에는이 기능을 사용할 수 없습니다.

> [!NOTE]
> Azure Key Vault에 대 한 자세한 내용은 [Azure Key Vault 항목](../general/overview.md) 을 참조 하세요.  
> HSM 보호 키에 대한 자격 증명 모음을 만드는 내용이 포함된 자습서를 시작하려면 [Azure Key Vault란?](../general/overview.md)을 참조하세요.

## <a name="supported-hsms"></a>지원 되는 Hsm

HSM으로 보호 되는 키를 Key Vault으로 전송 하는 것은 사용 하는 Hsm에 따라 두 가지 방법을 통해 지원 됩니다. 아래 표를 사용 하 여 Hsm에서 생성 하는 데 사용 해야 하는 방법을 결정 한 다음 Azure Key Vault와 함께 사용 하기 위해 고유한 HSM 보호 키를 전송 합니다. 

|공급 업체 이름|공급 업체 유형|지원 되는 HSM 모델|지원 되는 HSM-키 전송 방법|
|---|---|---|---|
|nCipher|제조업체|<ul><li>nShield Hsm 제품군</li></ul>|[레거시 BYOK 메서드 사용](hsm-protected-keys-legacy.md)|
|Thales|제조업체|<ul><li>펌웨어 버전 7.3 이상을 사용 하는 Luna HSM 7 제품군</li></ul>| [새 BYOK 메서드 사용 (미리 보기)](hsm-protected-keys-vendor-agnostic-byok.md)|
|Fortanix|HSM을 서비스로|<ul><li>자체 방어 키 관리 서비스 (SDKMS)</li></ul>|[새 BYOK 메서드 사용 (미리 보기)](hsm-protected-keys-vendor-agnostic-byok.md)|


## <a name="next-steps"></a>다음 단계

키에 대 한 보안, 내구성 및 모니터링을 보장 하려면 [Key Vault 모범 사례](../general/best-practices.md) 를 따르세요.
