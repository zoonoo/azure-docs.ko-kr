---
title: 포함 파일
description: 포함 파일
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: ee430241173a6c19e2a32e176f28411631d9cb19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "80272925"
---
구독 내에서 여러 개의 서비스를 만들 수 있습니다. 각 서비스는 특정 계층에서 프로비저닝될 수 있습니다. 각 계층에서 허용되는 서비스 수에 의해서만 제한됩니다. 예를 들어, 기본 계층에 최대 12개의 서비스를 만들고, 동일한 구독 내의 S1 계층에 12개의 다른 서비스를 만들 수 있습니다. 계층에 대한 자세한 내용은 [Azure Cognitive Search에 대한 SKU 또는 계층 선택](../articles/search/search-sku-tier.md)을 참조하세요.

최대 서비스 제한은 요청 시 설정할 수 있습니다. 동일한 구독 내에서 더 많은 서비스가 필요한 경우 Azure 지원에 문의하세요.

| 리소스            | 무료<sup>1</sup> | Basic | S1  | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| 최대 서비스    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| SU(검색 단위)의 최대 규모<sup>2</sup> |N/A |3 SU |36 SU |36 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> 체험 계층은 전용이 아닌 공유 리소스를 기반으로 합니다. 강화는 공유 리소스에서 지원되지 않습니다.

<sup>2</sup> 검색 단위는 *복제본* 또는 *파티션*으로 할당되는 청구 단위입니다. 스토리지, 인덱싱 및 쿼리 작업에 대해 두 리소스가 모두 필요합니다. SU 계산에 대한 자세한 내용은 [쿼리 및 인덱스 작업을 위한 리소스 수준 확장](../articles/search/search-capacity-planning.md)을 참조하세요. 