---
title: Azure Defender for DNS - 이점 및 기능
description: Azure Defender for DNS의 이점 및 기능에 대한 자세한 정보
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 18d9cb3c64eb41b33a58b248f3826bd9a80c6d90
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754822"
---
# <a name="introduction-to-azure-defender-for-dns"></a>Azure Defender for DNS 소개

[Azure DNS](../dns/dns-overview.md)는 Microsoft Azure 인프라를 사용하여 이름 확인을 제공하는 DNS 도메인에 대한 호스팅 서비스입니다. Azure에 도메인을 호스트하면 다른 Azure 서비스와 동일한 자격 증명, API, 도구 및 대금 청구를 사용하여 DNS 레코드를 관리할 수 있습니다.

Azure Defender for DNS는 다음과 같은 방법으로 클라우드 리소스에 대한 추가 보호 계층을 제공합니다.

- Azure 리소스의 모든 DNS 쿼리를 지속적으로 모니터링
- 의심스러운 활동에 대해 경고하는 고급 보안 분석 실행

## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|미리 보기<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|가격 책정:|**Azure Defender for DNS** 는 [가격 책정 페이지](security-center-pricing.md)에 표시된 대로 요금이 청구됩니다.|
|클라우드:|![예](./media/icons/yes-icon.png) 상용 클라우드<br>![아니요](./media/icons/no-icon.png) 국가/소버린(미국 정부, 중국 정부, 기타 정부)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-dns"></a>Azure Defender for DNS의 이점은?

Azure Defender for DNS는 다음을 비롯한 문제를 방지합니다.

- DNS 터널링을 사용하여 Azure 리소스에서 데이터 반출
- C&C 서버와 통신하는 맬웨어
- 피싱 및 암호화 마이닝으로 악성 도메인과의 통신
- DNS 공격 - 악성 DNS 확인자와의 통신 

Azure Defender for DNS에서 제공하는 경고의 전체 목록은 [경고 참조 페이지](alerts-reference.md#alerts-dns)에 있습니다.

## <a name="dependencies"></a>종속성

Azure Defender for DNS는 에이전트를 사용하지 않습니다. 

DNS 계층을 보호하려면 [Azure Defender 사용](security-center-pricing.md)에 설명된 대로 각 구독에 대해 Azure Defender for DNS를 사용하도록 설정합니다.


## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Defender for DNS에 대해 알아보았습니다. 관련 자료는 다음 문서를 참조하세요. 

- 보안 경고는 Security Center에서 생성되거나 Security Center에서 다른 보안 제품으로부터 수신할 수 있습니다. 이러한 모든 경고를 Azure Sentinel, 타사 SIEM 또는 기타 외부 도구로 내보내려면 [SIEM으로 경고 내보내기](continuous-export.md)의 지침을 따릅니다.

- > [!div class="nextstepaction"]
    > [Azure Defender 사용](security-center-pricing.md)