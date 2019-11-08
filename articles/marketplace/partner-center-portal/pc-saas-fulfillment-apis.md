---
title: SaaS 처리 Api | Azure Marketplace
description: SaaS 제품을 Azure Marketplace와 통합할 수 있도록 지 원하는 Api 버전을 소개 합니다.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: ebfc278d09c244970df5807df1505295fe7016c4
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819113"
---
# <a name="saas-fulfillment-apis"></a>SaaS 처리 API

SaaS 처리 Api는 Isv (독립 소프트웨어 공급 업체)가 SaaS 응용 프로그램을 Azure Marketplace와 통합할 수 있도록 합니다. 이러한 Api를 통해 ISV 응용 프로그램은 direct, 파트너-led (대리점) 및 현장 led와 같은 모든 상거래 사용 채널에 참여할 수 있습니다.  Azure Marketplace에 불가능 SaaS 제품을 나열 하기 위한 요구 사항입니다.

> [!WARNING]
> 이 API의 현재 버전은 모든 새 SaaS 제품에 사용 되는 버전 2입니다.  API의 버전 1은 더 이상 사용 되지 않으며 기존 제안을 지원 하기 위해 유지 관리 되 고 있습니다.


## <a name="business-model-support"></a>비즈니스 모델 지원

이 API는 다음과 같은 비즈니스 모델 기능을 지원 합니다. 당신은 할 수 있어요:

* 제안에 대 한 여러 계획을 지정 합니다. 이러한 계획은 다양 한 기능을 가지 며 가격이 다르게 책정 될 수 있습니다.
* 사이트별 또는 사용자 단위 청구 모델을 기준으로 제품을 제공 합니다.
* 매월 및 연간 (유료) 청구 옵션을 제공 합니다.
* 협상 된 비즈니스 계약에 따라 고객에 게 개인 가격을 제공 합니다.


## <a name="next-steps"></a>다음 단계

아직 수행 하지 않은 경우 [AZURE AD 응용 프로그램 등록](./pc-saas-registration.md)에 설명 된 대로 [Azure Portal](https://ms.portal.azure.com) 에서 SaaS 응용 프로그램을 등록 합니다.  그런 다음 개발용이 인터페이스의 최신 버전인 [SaaS 처리 API 버전 2](./pc-saas-fulfillment-api-v2.md)를 사용 합니다.
