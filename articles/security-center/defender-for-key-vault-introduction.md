---
title: Azure Defender for Key Vault-혜택 및 기능
description: Key Vault Azure Defender의 이점 및 기능에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: conceptual
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 06818e443568918e2ee87bbfbec81836ea85648b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939880"
---
# <a name="introduction-to-azure-defender-for-key-vault"></a>Key Vault에 대 한 Azure Defender 소개

Azure Key Vault는 암호화 키와 비밀(예: 인증서, 연결 문자열 및 암호)을 보호하는 클라우드 서비스입니다. 

Azure에 대 한 **Key Vault Azure Defender** 를 사용 하도록 설정 하 여 Azure Key Vault에 대 한 고급 고급 위협 방지를 사용 하 여 추가 보안 인텔리전스 계층을 제공 합니다. 

## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|결정|**Azure Defender for Key Vault** 는 [가격 책정 페이지](security-center-pricing.md) 에 표시 된 대로 청구 됩니다.|
|클라우드:|![예](./media/icons/yes-icon.png) 상용 클라우드<br>![예](./media/icons/no-icon.png) 국가/소 버린 (US Gov, 중국 .Gov, 기타 .Gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-key-vault"></a>Key Vault Azure Defender의 이점은 무엇 인가요?

Azure Defender는 Key Vault 계정을 액세스 하거나 악용 하려는 비정상적이 고 잠재적으로 유해한 시도를 감지 합니다. 이 보호 계층을 사용하면 보안 전문가가 아니거나 타사 보안 모니터링 시스템을 관리할 필요 없이 위협을 처리할 수 있습니다.  

비정상적인 활동이 발생 하면 Azure Defender는 경고를 표시 하 고 필요에 따라 조직의 관련 멤버에 게 전자 메일을 통해 보냅니다. 이러한 경고에는 의심스러운 활동에 대한 세부 정보 및 위협을 조사하고 완화하는 방법에 대한 추천 사항이 포함됩니다. 

## <a name="azure-defender-for-key-vault-alerts"></a>Key Vault 용 Azure Defender 경고
Azure Defender에서 Key Vault에 대 한 경고를 수신 하는 경우 [Key Vault Azure defender에 응답](defender-for-key-vault-usage.md)에 설명 된 대로 경고를 조사 하 고이에 응답 하는 것이 좋습니다. Azure Defender for Key Vault는 응용 프로그램 및 자격 증명을 보호 하므로 경고를 트리거한 응용 프로그램이 나 사용자에 대해 잘 알고 있는 경우에도 모든 경고를 둘러싼 상황을 확인 하는 것이 중요 합니다.

경고는 Key Vault의 **보안** 페이지, Azure Defender 대시보드 및 Security Center의 경고 페이지에 표시 됩니다.

:::image type="content" source="./media/defender-for-key-vault-intro/key-vault-security-page.png" alt-text="Azure Key Vault의 보안 페이지":::

## <a name="next-steps"></a>다음 단계

이 문서에서는 Key Vault에 대 한 Azure Defender에 대해 알아보았습니다.

관련 자료는 다음 문서를 참조하세요. 

- [Key Vault 보안 경고](alerts-reference.md#alerts-azurekv)--모든 Azure Security Center 경고에 대 한 참조 테이블의 Key Vault 섹션
- [SIEM으로 경고 내보내기](continuous-export.md)
- [Azure Defender에서 경고 표시 안 함](alerts-suppression-rules.md)