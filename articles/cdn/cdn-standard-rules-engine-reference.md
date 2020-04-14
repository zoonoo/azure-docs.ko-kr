---
title: Azure CDN에 대한 표준 규칙 엔진 참조 | 마이크로 소프트 문서
description: Azure CDN(Azure CDN)에 대한 표준 규칙 엔진의 일치 조건 및 작업에 대한 참조 설명서입니다.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: 6d4fa4451c3db3d6f2a506eabd5676d18b0219f4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259904"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Azure CDN에 대한 표준 규칙 엔진 참조

Azure CDN(Azure CDN)에 대한 [표준 규칙 엔진에서](cdn-standard-rules-engine.md) 규칙은 하나 이상의 일치 조건과 작업으로 구성됩니다. 이 문서에서는 Azure CDN에 대한 표준 규칙 엔진에서 사용할 수 있는 일치 조건 및 기능에 대한 자세한 설명을 제공합니다.

규칙 엔진은 표준 Azure CDN에서 특정 유형의 요청을 처리하는 방법에 대한 최종 권한으로 설계되었습니다.

**규칙에 대한 일반적인 용도**:

- 사용자 지정 캐시 정책을 재정의하거나 정의합니다.
- 요청을 리디렉션합니다.
- HTTP 요청 및 응답 헤더를 수정합니다.

## <a name="terminology"></a>용어

규칙 엔진에서 규칙을 정의하려면 [일치 조건](cdn-standard-rules-engine-match-conditions.md) 및 [작업을](cdn-standard-rules-engine-actions.md)설정합니다.

 ![Azure CDN 규칙 구조](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

각 규칙에는 최대 4개의 일치 조건과 3개의 작업이 있을 수 있습니다. 각 Azure CDN 끝점에는 최대 5개의 규칙이 있을 수 있습니다. 

Azure CDN 끝점에 대한 현재 5개 규칙 제한에 포함된 기본 *전역 규칙입니다.* 전역 규칙에는 일치하는 조건이 없으며 전역 규칙에 정의된 작업은 항상 트리거됩니다.

## <a name="syntax"></a>구문

특수 문자를 규칙에서 처리하는 방법은 서로 다른 일치 조건과 동작이 텍스트 값을 처리하는 방법에 따라 다릅니다. 일치 조건 또는 작업은 다음 방법 중 하나로 텍스트를 해석할 수 있습니다.

- [리터럴 값](#literal-values)
- [와일드카드 값](#wildcard-values)


### <a name="literal-values"></a>리터럴 값

리터럴 값으로 해석되는 텍스트는 *% 기호를 제외한* 모든 특수 문자를 규칙에서 일치해야 하는 값의 일부로 처리합니다. 예를 들어, 정확한 값이 `'*'` `'*'` 발견된 경우에만 설정된 리터럴 일치 조건이 충족됩니다.

백분율 기호는 URL 인코딩(예: `%20`)을 나타내는 데 사용됩니다.

### <a name="wildcard-values"></a>와일드카드 값

와일드카드 값으로 해석되는 텍스트는 특수 문자에 추가 의미를 할당합니다. 다음 표에서는 표준 규칙 엔진에서 특정 특수 문자를 해석하는 방법을 설명합니다.

문자 | 설명
----------|------------
\ | 백슬래시는 이 테이블에 지정된 문자를 이스케이프하는 데 사용됩니다. 백슬래시는 이스케이프해야 하는 특수 문자 바로 앞에 지정되어야 합니다. 예를 들어 다음 구문은 별표를 이스케이프합니다.`\*`
% | 백분율 기호는 URL 인코딩(예: `%20`)을 나타내는 데 사용됩니다.
\* | 별표는 하나 이상의 문자를 나타내는 와일드카드입니다.
space | 공백 문자는 지정된 값이나 패턴 중 하나에 의해 일치 조건을 충족할 수 있음을 나타냅니다.
작은따옴표 | 단일 따옴표에는 특별한 의미가 없습니다. 그러나 단일 따옴표 집합은 값을 리터럴 값으로 처리해야 한다는 것을 나타냅니다. 단일 따옴표는 다음과 같은 방법으로 사용할 수 있습니다.<ul><li>지정된 값이 비교 값의 모든 부분과 일치할 때마다 일치 조건을 충족할 수 있도록 합니다.  예를 들어 `'ma'`는 다음 문자열 중 하나와 일치합니다. <ul><li>/business/**ma**rathon/asset.htm</li><li>**ma**p.gif</li><li>/business/template.**ma**p</li></ul><li>특수 문자를 리터럴 문자로 지정할 수 있도록 합니다. 예를 들어 단일 따옴표(또는)의`' '` `'<sample value>'`집합에 공백 문자를 둘러싸서 리터럴 공간 문자를 지정할 수 있습니다.</li><li>빈 값을 지정할 수 있도록 합니다. 단일 따옴표**집합('')을**지정하여 빈 값을 지정합니다.</li></ul>**중요**:<br /><ul><li>지정된 값에 와일드카드가 포함되어 있지 않으면 이 값은 자동으로 리터럴 값으로 간주됩니다. 리터럴 값에 대한 단일 따옴표 집합을 지정할 필요가 없습니다.</li><li>이 테이블의 다른 문자를 이스케이프하는 데 백슬래시가 사용되지 않으면 단일 따옴표 집합에 지정하면 백슬래시가 무시됩니다.</li><li>특수 문자를 리터럴 문자로 지정하는 또 다른 방법은 백슬래시()를`\`사용하여 이스케이프하는 것입니다.</li></ul>

## <a name="next-steps"></a>다음 단계

- [표준 규칙 엔진의 조건 일치](cdn-standard-rules-engine-match-conditions.md)
- [표준 규칙 엔진의 작업](cdn-standard-rules-engine-actions.md)
- [표준 규칙 엔진을 사용하여 HTTPS 적용](cdn-standard-rules-engine.md)
- [Azure CDN 개요](cdn-overview.md)
