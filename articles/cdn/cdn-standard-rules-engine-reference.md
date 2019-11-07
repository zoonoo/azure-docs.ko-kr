---
title: Azure CDN 표준 규칙 엔진 참조 | Microsoft Docs
description: Azure CDN 표준 규칙 엔진 일치 조건 및 작업에 대 한 참조 설명서입니다.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 6fb7e704f3d33cff8c29386b8aba9d8289037cbb
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615937"
---
# <a name="azure-cdn-from-microsoft-rules-engine-reference"></a>Microsoft 규칙 엔진 참조의 Azure CDN

이 문서에는 CDN (Azure Content Delivery Network) [표준 규칙 엔진](cdn-standard-rules-engine.md)에 대 한 사용 가능한 일치 조건 및 기능에 대 한 자세한 설명이 나와 있습니다.

규칙 엔진은 특정 형식의 요청이 CDN에서 처리되는 방식을 최종적으로 결정하도록 설계되었습니다.

**일반적인 사용**:

- 사용자 지정 캐시 정책을 재정의하거나 정의합니다.
- 요청을 리디렉션합니다.
- HTTP 요청 및 응답 헤더 수정

## <a name="terminology"></a>용어

규칙은 [**일치 조건**](cdn-standard-rules-engine-match-conditions.md)및 [**작업**](cdn-standard-rules-engine-actions.md)을 사용 하 여 정의 됩니다. 이러한 요소는 다음 그림에 강조 표시되어 있습니다.

 ![CDN 규칙 구조](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

각 규칙에는 최대 4 개의 일치 조건 및 3 개의 작업이 있을 수 있습니다. CDN 끝점 당 규칙이 최대 5 개 있습니다. 또한 기본적으로 **전역 규칙**이라고 하는 규칙이 적용 됩니다. 이는에 정의 된 동작이 항상 트리거되는 일치 조건이 없는 규칙입니다. 이 규칙은 현재 5 개의 규칙 제한에 포함 됩니다.

## <a name="syntax"></a>구문

특수 문자가 처리 되는 방식은 일치 조건 또는 actopm에서 텍스트 값을 처리 하는 방법에 따라 달라 집니다. 일치 조건 또는 기능은 다음 방법 중 하나로 텍스트를 해석할 수 있습니다.

1. [**리터럴 값**](#literal-values)
2. [**와일드카드 값**](#wildcard-values)


### <a name="literal-values"></a>리터럴 값

리터럴 값으로 해석되는 텍스트는 % 기호를 제외한 모든 특수 문자를 일치되어야 하는 값의 일부로 취급합니다. 즉, `\'*'\`로 설정된 리터럴 일치 조건은 정확한 값(예: `\'*'\`)을 찾은 경우에만 충족됩니다.

백분율 기호는 URL 인코딩을 나타내는 데 사용 됩니다 (예: `%20`).

### <a name="wildcard-values"></a>와일드카드 값

와일드카드 값으로 해석되는 텍스트는 특수 문자에 추가적인 의미를 할당합니다. 다음 표에서는 다음 문자 집합이 해석되는 방식을 설명합니다.

문자 | 설명
----------|------------
\ | 백슬래시는 이 테이블에 지정된 문자를 이스케이프하는 데 사용됩니다. 백슬래시는 이스케이프해야 하는 특수 문자 바로 앞에 지정되어야 합니다.<br/>예를 들어 다음 구문은 별표를 이스케이프합니다.`\*`
% | 백분율 기호는 URL 인코딩을 나타내는 데 사용 됩니다 (예: `%20`).
\* | 별표는 하나 이상의 문자를 나타내는 와일드카드입니다.
공백 | 공백 문자는 지정된 값 또는 패턴에 의해 일치 조건이 충족될 수 있음을 나타냅니다.
'value' | 작은따옴표는 특별한 의미가 없습니다. 그러나 값을 리터럴 값으로 취급해야 함을 나타내기 위해 작은따옴표 쌍을 사용합니다. 다음과 같은 방법으로 사용할 수 있습니다.<br><br/>- 지정된 값이 비교 값의 일부와 일치할 때마다 일치 조건이 충족될 수 있습니다.  예를 들어 `'ma'`는 다음 문자열 중 하나와 일치합니다. <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />- 특수 문자를 리터럴 문자로 지정할 수 있습니다. 예를 들어 공백 문자를 작은따옴표 쌍으로 묶어 리터럴 공백 문자를 지정할 수 있습니다(즉, `' '` 또는 `'sample value'`).<br/>- 빈 값을 지정할 수 있습니다. 작은따옴표 쌍(예: '')을 지정하여 빈 값을 지정합니다.<br /><br/>**중요:**<br/>- 지정된 값이 와일드 카드를 포함하지 않으면 자동으로 리터럴 값으로 간주됩니다. 즉, 작은따옴표 집합을 지정할 필요가 없습니다.<br/>- 백슬래시가 이 표의 다른 문자를 이스케이프하지 않으면 작은따옴표 집합으로 묶어서 지정할 때 무시됩니다.<br/>- 특수 문자를 리터럴 문자로 지정하는 또 다른 방법은 백슬래시를 사용하여 이스케이프하는 것입니다(즉, `\`).

## <a name="next-steps"></a>다음 단계

- [표준 규칙 엔진 일치 조건](cdn-standard-rules-engine-match-conditions.md)
- [표준 규칙 엔진 작업](cdn-standard-rules-engine-actions.md)
- [표준 규칙 엔진을 사용 하 여 HTTPS 적용](cdn-standard-rules-engine.md)
- [Azure CDN 개요](cdn-overview.md)
