---
title: 포함 파일
description: 포함 파일
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: 80503ad154a9fc4d01614ffd2816f9d5fd497fdb
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228916"
---
<a name="paths"></a>
## <a name="paths"></a>경로

<a name="anomalydetection-post"></a>
### <a name="detect-anomaly-points-for-the-time-series-data-points-requested"></a>요청된 시계열 데이터 요소에 대한 이상 요소 감지
```
POST /anomalydetection
```


#### <a name="parameters"></a>매개 변수

|Type|Name|설명|스키마|
|---|---|---|---|
|**본문**|**body**  <br>*필수*|필요한 경우 시계열 데이터 요소 및 기간입니다.|[요청](#request)|


#### <a name="responses"></a>응답

|HTTP 코드|설명|스키마|
|---|---|---|
|**200**|작업이 성공했습니다.|< [응답](#response) > 배열|
|**400**|JSON 요청을 구문 분석할 수 없습니다.|내용 없음|
|**403**|제공한 인증서는 서버에서 허용되지 않습니다.|내용 없음|
|**405**|메서드를 사용할 수 없습니다.|내용 없음|
|**500**|내부 서버 오류.|내용 없음|


#### <a name="consumes"></a>사용

* `application/json`


#### <a name="produces"></a>생성

* `application/json`


#### <a name="tags"></a>태그들

* anomalydetection



