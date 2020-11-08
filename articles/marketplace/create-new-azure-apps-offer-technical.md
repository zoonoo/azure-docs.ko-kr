---
title: Azure 애플리케이션 제품에 대 한 기술 세부 정보를 추가 하는 방법
description: 파트너 센터에서 Azure 응용 프로그램 제품에 대 한 기술 세부 정보를 추가 하는 방법에 대해 알아봅니다.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: a8658242bca48ee490e7fd44582f46e7c9c78e5b
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2020
ms.locfileid: "94370236"
---
# <a name="how-to-add-technical-details-for-your-azure-application-offer"></a>Azure 애플리케이션 제품에 대 한 기술 세부 정보를 추가 하는 방법

이 문서에서는 Microsoft 상업적 marketplace에서 솔루션에 연결 하는 데 도움이 되는 기술 세부 정보를 입력 하는 방법을 설명 합니다. 이 연결을 통해 고객에 게 제품을 프로 비전 하 고 관리 하는 경우 고객에 게 제품을 프로 비전 할 수 있습니다.

[Marketplace에서 요금제 청구 api](partner-center-portal/marketplace-metering-service-apis.md) 를 사용 하 여 계량 이벤트를 내보내고 Azure AD 보안 토큰을 사용 하 여 인증 하는 서비스를 보유 하는 관리 되는 응용 프로그램이 제품에 포함 되어 있는 경우에만이 섹션을 완료 합니다. 자세한 내용은 다양 한 인증 옵션에서 [Marketplace 계량 서비스 인증 전략](partner-center-portal/marketplace-metering-service-authentication.md) 을 참조 하세요.

## <a name="technical-configuration-offer-level"></a>기술 구성 (제안 수준)

**기술 구성** 탭은 [Marketplace 요금제 청구 api](partner-center-portal/marketplace-metering-service-apis.md)를 사용 하 여 계량 이벤트를 내보내는 관리 되는 응용 프로그램을 만드는 경우에만 적용 됩니다. 그렇다면 다음 단계를 완료 합니다. 그렇지 않으면 [다음 단계로](#next-steps)이동 합니다. 

이러한 필드에 대 한 자세한 내용은 [상용 marketplace에 대 한 Azure 애플리케이션 제품 계획](plan-azure-application-offer.md#technical-configuration)을 참조 하세요.

1. **기술 구성** 탭에서 두 서비스 간의 연결에 대 한 유효성을 검사 하는 데 사용 되는 **Azure Active Directory 테 넌 트 id** 및 **Azure Active Directory 응용 프로그램 id** 를 제공 합니다.

1. 다음 탭을 계속 하기 전에 **초안 저장** 을 선택 합니다. 계획 개요.

## <a name="next-steps"></a>다음 단계

- [Azure 애플리케이션 제품에 대 한 계획을 만드는 방법](create-new-azure-apps-offer-plans.md)
