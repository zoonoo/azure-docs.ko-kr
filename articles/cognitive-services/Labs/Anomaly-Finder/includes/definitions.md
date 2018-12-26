---
title: 포함 파일
description: 포함 파일
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: a49df0c18ef9db9d0d41ca2e714474e6386ae7d1
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2018
ms.locfileid: "48904463"
---
<a name="definitions"></a>
## <a name="definitions"></a>정의

<a name="point"></a>
### <a name="point"></a>Point

|이름|설명|스키마|
|---|---|---|
|**Timestamp**  <br>*선택 사항*|데이터 요소의 타임스탬프입니다. 자정에 맞게 조정하고 UTC 날짜 시간 문자열을 사용합니다(예: 2017-08-01T00:00:00Z).|string (date-time)|
|**값**  <br>*선택 사항*|데이터 측정값입니다.|숫자(double)|


<a name="request"></a>
### <a name="request"></a>요청

|이름|설명|스키마|
|---|---|---|
|**기간**  <br>*선택 사항*|데이터 요소의 기간입니다. 값이 Null이거나 없는 경우 API가 자동으로 기간을 결정합니다.|숫자(double)|
|**Points**  <br>*선택 사항*|시계열 데이터 요소입니다. 이상 결과와 일치하도록 데이터를 타임스탬프 오름차순으로 정렬해야 합니다. 데이터가 올바르게 정렬되지 않았거나 중복 타임스탬프가 있는 경우, API가 이상 요소를 올바르게 감지하지만 반환된 요소를 입력과 올바르게 일치시킬 수 없습니다. 이러한 경우 경고 메시지가 응답에 추가됩니다.|< [요소](#point) > 배열|


<a name="response"></a>
### <a name="response"></a>response

|이름|설명|스키마|
|---|---|---|
|**ExpectedValues**  <br>*선택 사항*|학습 기반 모델에서 예측된 값입니다. 입력 데이터 요소가 타임스탬프 오름차순으로 정렬된 경우 배열의 인덱스를 사용하여 예상 값과 원래 값을 매핑할 수 있습니다.|< 숫자(double) > 배열|
|**IsAnomaly**  <br>*선택 사항*|데이터 요소가 양방향(급증 또는 급감)에서 모두 이상인지 여부에 대한 결과입니다. true는 요소가 이상임을 의미하고, false는 요소가 정상임을 의미합니다. 입력 데이터 요소가 타임스탬프 오름차순으로 정렬된 경우 배열의 인덱스를 사용하여 예상 값과 원래 값을 매핑할 수 있습니다.|< 부울 > 배열|
|**IsAnomaly_Neg**  <br>*선택 사항*|데이터 요소가 음수 방향(급감)에서 이상인지 여부에 대한 결과입니다. true는 이상의 방향이 음수임을 의미합니다. 입력 데이터 요소가 타임스탬프 오름차순으로 정렬된 경우 배열의 인덱스를 사용하여 예상 값과 원래 값을 매핑할 수 있습니다.|< 부울 > 배열|
|**IsAnomaly_Pos**  <br>*선택 사항*|데이터 요소가 양수 방향(급증)에서 이상인지 여부에 대한 결과입니다. true는 이상의 방향이 양수임을 의미합니다. 입력 데이터 요소가 타임스탬프 오름차순으로 정렬된 경우 배열의 인덱스를 사용하여 예상 값과 원래 값을 매핑할 수 있습니다.|< 부울 > 배열|
|**LowerMargin**  <br>*선택 사항*|(ExpectedValue - LowerMargin) 데이터 요소가 여전히 정상으로 간주되는 하한을 결정합니다. 입력 데이터 요소가 타임스탬프 오름차순으로 정렬된 경우 배열의 인덱스를 사용하여 예상 값과 원래 값을 매핑할 수 있습니다.|< 숫자(double) > 배열|
|**기간**  <br>*선택 사항*|API가 이상 요소를 감지하는 데 사용한 기간입니다.|숫자(float)|
|**UpperMargin**  <br>*선택 사항*|ExpectedValue 및 UpperMargin의 합계는 데이터 요소가 여전히 정상으로 간주되는 상한을 결정합니다. 입력 데이터 요소가 타임스탬프 오름차순으로 정렬된 경우 배열의 인덱스를 사용하여 예상 값과 원래 값을 매핑할 수 있습니다.|< 숫자(double) > 배열|
|**WarningText**  <br>*선택 사항*|제공된 입력 데이터 요소가 API에 필요한 규칙을 따르지 않고 API에서 데이터를 계속 검색할 수 있는 경우 API는 데이터를 분석하고 이 필드에 경고 정보를 추가합니다.|string|



