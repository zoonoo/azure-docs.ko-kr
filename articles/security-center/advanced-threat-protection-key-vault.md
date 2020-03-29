---
title: Azure 키 볼트에 대한 위협 보호
description: 이 문서에서는 Azure 보안 센터에서 Azure 키 자격 증명 모음에 대 한 고급 위협 보호를 설정 하는 방법에 대해 설명 합니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 449096590df6145c9f80dcf2c97726931909a2ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914809"
---
# <a name="threat-protection-for-azure-key-vault-preview"></a>Azure 키 볼트에 대한 위협 보호(미리 보기)

Azure Key Vault에 대한 고급 위협 보호는 추가 보안 인텔리전스 계층을 제공합니다. 이 도구는 Key Vault 계정에 액세스하거나 악용하려는 잠재적으로 유해한 시도를 감지합니다. Azure Security Center의 기본 고급 위협 보호를 사용하면 보안 전문가가 없고 추가 보안 모니터링 시스템을 학습하지 않고도 위협을 해결할 수 있습니다.

보안 센터에서 비정상적인 활동을 감지하면 경고가 표시됩니다. 또한 의심스러운 활동에 대한 세부 정보와 확인된 위협을 조사하고 해결하는 방법에 대한 권장 사항을 구독 관리자에게 이메일로 보냅니다.

## <a name="configuring-threat-protection-from-security-center"></a>보안 센터에서 위협 보호 구성

기본적으로 보안 센터의 표준 가격 책정 계층을 구독하면 모든 Key Vault 계정에 고급 위협 보호가 활성화됩니다. 자세한 내용은 [가격 책정](security-center-pricing.md)을 참조하세요.

특정 구독에 대한 보호를 사용 하거나 사용하지 않도록 설정하려면 다음을 수행하십시오.

1. 보안 센터의 왼쪽 창에서 **가격 & 설정을**선택합니다.

1. 위협 보호를 사용하거나 사용하지 않도록 설정하려는 저장소 계정으로 구독을 선택합니다.

1. **가격 책정 계층을**선택합니다.

1. 리소스 **유형 그룹별 가격 책정 계층 선택에서** **키 볼트 행을** 찾고 **사용 안 함** 또는 사용 안 **함**옵션을 선택합니다.

    [![Azure 보안 센터의 키 볼트에 대한 고급 위협 보호 사용 또는 비활성화](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)

1. **저장**을 선택합니다.


## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Key Vault에 대한 고급 위협 보호를 활성화하고 사용하지 않도록 설정하는 방법을 배웠습니다. 

관련 자료는 다음 문서를 참조하십시오.

- [Azure 보안 센터의 위협 보호](threat-protection.md)--이 문서에서는 Azure 보안 센터에서 보안 경고의 원본에 대해 설명합니다.
- [키 볼트 보안 경고](alerts-reference.md#alerts-azurekv)--모든 Azure 보안 센터 경고에 대 한 참조 테이블의 키 볼트 섹션