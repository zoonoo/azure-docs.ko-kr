---
title: SaaS Fulfillment Api | Azure Marketplace
description: Azure Marketplace을 통해 SaaS에 통합할 수 있도록 해 주는 Api 제공 fulfillment의 버전을 소개 합니다.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: pabutler
ms.openlocfilehash: e7a01af1eba865b0a088b0fa7226273527abd70e
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257400"
---
# <a name="saas-fulfillment-apis"></a>SaaS 처리 API

> [!IMPORTANT] 
> SaaS로 마이그레이션된 기능을 제공 합니다 [Microsoft Partner Center](https://partner.microsoft.com/dashboard/directory)합니다.  모든 새 게시자 새 SaaS 제품을 만들고 기존 제품을 관리 하기 위한 파트너 센터를 사용 해야 합니다.  SaaS 제공 서비스를 사용 하 여 현재 게시자 batchwise 마이그레이션될 클라우드 파트너 포털에서 파트너 센터에 있습니다.  클라우드 파트너 포털에는 특정 기존 제품 마이그레이션된 경우를 나타내기 위해 상태 메시지가 표시 됩니다.
> 자세한 내용은 [새 SaaS 제품을 만드는](../../partner-center-portal/create-new-saas-offer.md)합니다.

SaaS Fulfillment Api 사용 독립 소프트웨어 공급 업체 (Isv) Azure Marketplace SaaS 응용 프로그램을 통합할 수 있습니다. 이러한 Api를 모두 사용 하도록 설정 하는 전자 상거래 채널에 참여 하려면 ISV 응용 프로그램 사용: 직접 파트너 주도 (대리점) 필드 주도하 고 합니다.  Azure Marketplace에서 transactable SaaS 제품을 나열 하기 위한 요구 사항이 됩니다.

> [!WARNING]
> 이 API의 현재 버전이 버전 2를 제공 하는 모든 새 SaaS에 사용 해야 합니다.  API의 버전 1은 사용 되지 않으며 기존 제품을 지원 하기 위해 유지 됩니다.


## <a name="business-model-support"></a>비즈니스 모델 지원

이 API는 다음과 같은 비즈니스 모델 기능; 지원 당신은 할 수 있어요:

* 제품에 대 한 여러 계획을 지정 합니다. 이러한 계획에 다른 기능이 및 서로 다른 가격이 책정 될 수 있습니다.
* 제공 하는 서비스 제공을 사이트당 또는 모델 별로 청구 되는 사용자 당.
* 월간 및 연간 (미리 지불)를 제공 합니다. 청구 옵션입니다.
* 협상 된 비즈니스 계약에 따라 고객에 게 개인 가격을 제공 합니다.


## <a name="next-steps"></a>다음 단계

아직 수행 하는 경우에 SaaS 응용 프로그램을 등록 합니다 [Azure portal](https://ms.portal.azure.com) 에 설명 된 대로 [Azure AD 응용 프로그램을 등록](./cpp-saas-registration.md)합니다.  그런 다음 개발을 위해이 인터페이스의 최신 버전을 사용 합니다. [SaaS Fulfillment API 버전 2](./cpp-saas-fulfillment-api-v2.md)합니다.
