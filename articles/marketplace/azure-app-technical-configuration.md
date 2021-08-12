---
title: Azure 애플리케이션 제품에 대한 기술 세부 정보 추가
description: 파트너 센터(Azure Marketplace)에서 Azure 애플리케이션 제품에 대한 기술 세부 정보를 추가합니다.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 373dd2c3f0b107b0a910af138570c9cf1782e68c
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2021
ms.locfileid: "111542816"
---
# <a name="add-technical-details-for-an-azure-application-offer"></a>Azure 애플리케이션 제품에 대한 기술 세부 정보 추가

이 문서에서는 Microsoft의 상업용 Marketplace를 솔루션에 연결하는 데 도움이 되는 기술 세부 정보를 입력하는 방법을 설명합니다. 고객이 제품을 구매하고 관리하기로 선택한 경우 이 연결을 통해 고객을 위한 제품을 프로비전할 수 있습니다.

[Marketplace 요금제 청구 API](marketplace-metering-service-apis.md)를 사용하여 계량 이벤트를 내보내고 Azure AD 보안 토큰을 사용하여 인증하는 서비스가 있는 관리형 애플리케이션이 제품에 포함되어 있는 경우에만 이 섹션을 완료합니다. 자세한 내용은 다양한 인증 옵션에 대한 [Marketplace 계량 서비스 인증 전략](marketplace-metering-service-authentication.md)을 참조하세요.

## <a name="technical-configuration-offer-level"></a>기술 구성(제품 수준)

**기술 구성** 탭은 [Marketplace 요금제 청구 API](marketplace-metering-service-apis.md)를 사용하여 계량 이벤트를 내보내는 관리형 애플리케이션을 만드는 경우에만 적용됩니다. 해당되는 경우 다음 단계를 완료합니다. 그렇지 않으면 [다음 단계](#next-steps)로 이동합니다. 

이러한 필드에 대한 자세한 내용은 [상업용 Marketplace에 대한 Azure 애플리케이션 제품 계획](plan-azure-application-offer.md#technical-configuration)을 참조하세요.

1. **기술 구성** 탭에서 두 서비스 간의 연결에 대한 유효성을 검사하는 데 사용되는 **Azure Active Directory 테넌트 ID** 및 **Azure Active Directory 애플리케이션 ID** 를 제공합니다.

1. 다음 탭인 플랜 개요로 이동하기 전에 **초안 저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

- [이 제품에 대한 플랜 만들기](azure-app-plans.md)
