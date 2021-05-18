---
title: 마이그레이션 개요 - LUIS
description: 마이그레이션 경로에 있는 항목 이해
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/22/2020
ms.openlocfilehash: d6ecacf9aa1a7e650de74a412ed4f161ed0e0790
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91253805"
---
# <a name="migration-in-luis"></a>LUIS의 마이그레이션

마이그레이션 경로에 몇 가지 항목이 있습니다. 다음 표를 사용하여 영향을 받는 항목과 마이그레이션을 완료해야 하는 날짜를 파악합니다.

|영역|Description|마이그레이션 완료 날짜|
|--|--|--|
|[예측 API](luis-migration-api-v3.md)|V3 API로 마이그레이션합니다.|TBD|
|[API 작성](luis-migration-authoring-entities.md)|V3 API로 마이그레이션합니다.|TBD|
|[작성 리소스](luis-migration-authoring.md)|LUIS 포털에서 작성 리소스 없음에서 LUIS 작성 리소스 사용으로 마이그레이션합니다.|2020년 11월 2일 |
|[필수 기능](luis-migration-authoring-entities.md#api-change-constraint-replaced-with-required-feature)|이 변경 사항은 2020년 5월에 빌드 회의에서 적용되었으며, 앱이 제한된 기능을 사용하는 경우에만 v3 작성 API에 적용됩니다.|2020년 6월 19일|
|[복합 엔터티](migrate-from-composite-entity.md)|복합 엔터티를 기계 학습 엔터티로 업그레이드하여 엔터티를 디버깅하는 데 더 효율적인 분해능을 통해 더욱 완벽한 예측을 받는 엔터티를 빌드합니다.|TBD|
