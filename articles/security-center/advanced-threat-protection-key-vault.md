---
title: Azure Key Vault에 대 한 advanced threat protection을 설정 하는 방법 | Microsoft Docs
description: 이 문서에서는 Azure Key Vault에 대 한 고급 위협 방지를 설정 하는 방법을 설명 Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: b28f03f0997b6bb2c494c35cee9695a478906c47
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521879"
---
# <a name="how-to-set-up-advanced-threat-protection-for-azure-key-vault-preview"></a>Azure Key Vault에 대 한 advanced threat protection을 설정 하는 방법 (미리 보기)

Azure Key Vault에 대 한 Advanced threat protection은 보안 인텔리전스의 추가 계층을 제공 합니다. 이 도구는 Key Vault 계정에 액세스 하거나 악용 하려는 잠재적으로 유해한 시도를 검색 합니다. Security Center의 기본 advanced threat protection을 사용 하 여 보안 전문가가 아니더라도 추가 보안 모니터링 시스템을 학습 하지 않고도 위협을 해결할 수 있습니다.

Security Center은 비정상적인 활동을 검색할 때 경고를 표시 합니다. 또한 구독 관리자에 게 의심 스러운 활동에 대 한 세부 정보 및 식별 된 위협을 조사 하 고 수정 하는 방법에 대 한 권장 사항을 전자 메일로 보냅니다. 

> [!NOTE]
> Azure Key Vault에 대 한 Advanced threat protection은 현재 북아메리카 지역 에서만 사용할 수 있습니다.

## <a name="to-set-up-advanced-threat-protection-from-azure-security-center"></a>Azure Security Center에서 advanced threat protection을 설정 하려면

기본적으로 Security Center의 표준 계층을 구독할 때 모든 Key Vault 계정에 대해 advanced threat protection이 사용 되도록 설정 됩니다 ( [가격 책정](security-center-pricing.md)참조). 

특정 구독에 대 한 보호를 사용 하거나 사용 하지 않도록 설정 하려면:

1. Security Center의 사이드바에서 **가격 책정 & 설정**을 클릭 합니다.
1. 위협 방지를 사용 하거나 사용 하지 않도록 설정할 저장소 계정을 사용 하 여 구독을 선택 합니다.
1. **가격 책정 계층**을 클릭합니다.
1. **리소스 유형별 가격 책정 계층 선택** 그룹에서 키 자격 증명 모음 행을 찾고 **사용** 또는 **사용 안 함**을 클릭 합니다.
    [Azure Security Center의 Key Vault에 대해 advanced threat protection을 사용 하거나 사용 하지 않도록 설정 ![](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)
1. **Save**를 클릭합니다.


## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Key Vault에 대 한 advanced threat protection을 사용 하거나 사용 하지 않도록 설정 하는 방법을 알아보았습니다. 

기타 관련 자료는 다음 문서를 참조 하세요.

- [Security Center에서 Azure 서비스 계층에 대 한 위협 검색](security-center-alerts-service-layer.md) -이 문서에서는 고급 위협 방지와 관련 된 경고에 대해 설명 Azure Key Vault