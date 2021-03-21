---
title: 참조 아키텍처 다이어그램 | Azure Marketplace
description: Microsoft 상업적 marketplace에서 제품에 대 한 참조 아키텍처 다이어그램을 만드는 방법입니다.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: stmummer
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 02/18/2021
ms.openlocfilehash: 66e4d498ff7584188d680e35c89c6f10cc43c9cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604494"
---
# <a name="reference-architecture-diagram"></a>참조 아키텍처 다이어그램

참조 아키텍처 다이어그램은 제품에서 사용 하는 인프라를 나타내는 모델입니다. Azure IP 솔루션의 경우,이 다이어그램은 제품에서 IP 공동 판매의 기술 요구 사항에 따라 Microsoft의 클라우드 서비스를 사용 하는 방법도 보여 줍니다. 아키텍처의 품질을 평가 하기 위한 것은 아닙니다. 솔루션에서 Microsoft 서비스를 사용 하는 방법을 보여 주도록 설계 되었습니다.

참조 아키텍처 다이어그램은 여러 도구를 통해 만들 수 있습니다. Azure 아키텍처 모델을 나타내는 여러 스텐실이 있으므로 Microsoft Visio를 권장 합니다.

참조 아키텍처 다이어그램을 작성 하는 데 도움이 되는 출발점은 [Azure 아키텍처 모델](/azure/architecture/browse/)을 활용 하는 것입니다.

## <a name="typical-components-of-a-reference-architecture-diagram"></a>참조 아키텍처 다이어그램의 일반적인 구성 요소

- Azure 리소스를 사용 하는 클라우드 서비스를 포함 하 여 제품을 호스트 하 고 상호 작용 하는 클라우드 서비스
- 제품에서 사용 하는 데이터 연결, 데이터 계층 및 데이터 서비스
- 제품의 보안, 인증 및 사용자를 제어 하는 데 사용 되는 클라우드 서비스
- 사용자에 게 제품을 제공 하는 사용자 인터페이스 및 기타 서비스
- 하이브리드 또는 온-프레미스 연결 및 통합 또는 둘 다의 조합

이 스크린샷은 참조 아키텍처 다이어그램의 예를 보여 줍니다.

[![이 이미지는 공동 판매 아키텍처 다이어그램의 예입니다.](./media/co-sell/co-sell-arch-diagram.png)](./media/co-sell/co-sell-arch-diagram.png#lightbox)

이 예제 참조 아키텍처 다이어그램은 기계 학습 알고리즘을 통해 수요 시나리오를 예측 하는 데 도움이 되도록 인트라넷 사이트와 통합 될 수 있는 수직 산업 추가 봇 용입니다. 이 솔루션은 서로 다른 ERP 시스템의 공급망 및 제조 일정 데이터를 사용 합니다. 봇은 판매 직원이 주문에 대해 가능한 배달 날짜를 커밋할 수 있는 시기에 대 한 질문을 해결 하도록 설계 되었습니다.

## <a name="next-steps"></a>다음 단계

- [상업적 marketplace 제품에 대 한 공동 판매 구성](commercial-marketplace-co-sell.md)
