---
title: SaaS 주문 처리 API | Azure 마켓플레이스
description: SaaS 오퍼를 Azure 마켓플레이스와 통합할 수 있는 주문 처리 API 버전을 소개합니다.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: 92b1c52457fa92709381124480c05a5f636167f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275733"
---
# <a name="saas-fulfillment-apis"></a>SaaS 처리 API

SaaS 주문 처리 API를 사용하면 독립 소프트웨어 공급업체(ISV)가 SaaS 응용 프로그램을 Azure 마켓플레이스와 통합할 수 있습니다. 이러한 API를 사용하면 ISV 애플리케이션이 직접, 파트너 주도(리셀러) 및 현장 주도 채널과 같은 모든 상거래 지원 채널에 참여할 수 있습니다.  Azure 마켓플레이스에서 거래 가능한 SaaS 오퍼를 나열하기 위한 요구 사항입니다.

> [!WARNING]
> 이 API의 현재 버전은 버전 2이며 모든 새 SaaS 오퍼에 사용해야 합니다.  API의 버전 1은 더 이상 사용되지 않으며 기존 오퍼를 지원하도록 유지 관리되고 있습니다.


## <a name="business-model-support"></a>비즈니스 모델 지원

이 API는 다음 비즈니스 모델 기능을 지원합니다. 당신은 할 수 있어요:

* 오퍼에 대한 여러 계획을 지정합니다. 이러한 계획은 서로 다른 기능을 가지며 가격이 다르게 책정될 수 있습니다.
* 사이트별 또는 사용자당 청구 모델 기준으로 오퍼를 제공합니다.
* 월별 및 연간(선결제 선결제) 청구 옵션을 제공합니다.
* 협상된 비즈니스 계약을 기반으로 고객에게 개인 가격을 제공합니다.


## <a name="next-steps"></a>다음 단계

아직 등록하지 않은 경우 Azure AD 응용 프로그램 등록에 설명된 대로 [Azure 포털에](https://ms.portal.azure.com) SaaS [응용 프로그램을 등록합니다.](./pc-saas-registration.md)  그 후, 개발에 대 한이 인터페이스의 최신 버전을 사용: [SaaS 주문 API 버전 2](./pc-saas-fulfillment-api-v2.md).
