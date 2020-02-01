---
title: Form Recognizer의 새로운 기능
titleSuffix: Azure Cognitive Services
description: 양식 인식기 API에 대 한 최신 변경 내용을 이해 합니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: pafarley
ms.openlocfilehash: 2109d25d3962063c711dcab491855d9ebf1cf694
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901889"
---
# <a name="whats-new-in-form-recognizer"></a>Form Recognizer의 새로운 기능

이 문서에서는 새 버전의 양식 인식기 API와 함께 제공 되는 주요 변경 내용을 중점적으로 설명 합니다.

> [!NOTE]
> 달리 지정 하지 않는 한이 문서 집합의 빠른 시작 및 가이드는 항상 최신 버전의 API를 사용 합니다.

## <a name="form-recognizer-20-preview"></a>양식 인식기 2.0 (미리 보기)

### <a name="new-features"></a>새로운 기능

* **사용자 지정 모델**
  * **레이블로 학습** 이제 수동으로 레이블이 지정 된 데이터를 사용 하 여 사용자 지정 모델을 학습 시킬 수 있습니다. 이 방법은 모델 성능이 향상되며, 복잡한 양식 또는 키 없는 값을 포함하는 양식과 함께 작동하는 모델을 생성할 수 있습니다.
  * **비동기 API** 비동기 API 호출을 사용 하 여 대량 데이터 집합 및 파일을 학습 하 고 분석할 수 있습니다.
  * **TIFF 파일 지원** 이제 TIFF 문서에서 데이터를 학습 하 고 추출할 수 있습니다.
  * **추출 정확도 향상**

* **미리 작성 한 수신 모델**
  * **팁 금액** 이제 팁 양과 기타 필기 값을 추출할 수 있습니다.
  * **줄 항목 추출** 수신 확인에서 품목 값을 추출할 수 있습니다.
  * **신뢰도 값** 추출 된 각 값에 대 한 모델의 신뢰도를 볼 수 있습니다.
  * **추출 정확도 향상**

* **레이아웃 추출** 이제 레이아웃 API를 사용 하 여 폼에서 텍스트 데이터 및 테이블 데이터를 추출할 수 있습니다.

### <a name="custom-model-api-changes"></a>사용자 지정 모델 API 변경

사용자 지정 모델을 학습 하 고 사용 하는 모든 Api의 이름이 변경 되었으며, 일부 동기 메서드는 비동기입니다. 주요 변경 내용은 다음과 같습니다.

* 모델 학습 프로세스는 이제 비동기식입니다. **/Dev/sd** API 호출을 통해 학습을 시작 합니다. 이 호출은 **사용자 지정/모델/{modelID}** 에 전달 하 여 학습 결과를 반환할 수 있는 작업 ID를 반환 합니다.
* 이제 키/값 추출이 **/Custom/models/{modelID}/analyze** API 호출에 의해 시작 됩니다. 이 호출은 **사용자 지정/모델/{modelID}/analyzeResults/{resultID}** 에 전달 하 여 추출 결과를 반환할 수 있는 작업 ID를 반환 합니다.
* 이제 학습 작업에 대 한 작업 Id가 **작업 위치** 헤더가 아니라 HTTP 응답의 **Location** 헤더에 있습니다.

### <a name="receipt-api-changes"></a>수신 API 변경

판매 영수증을 읽기 위한 Api의 이름이 변경 되었습니다.

* 이제 **/Prebuilt/receipt/analyze** API 호출을 통해 수신 데이터 추출을 시작 합니다. 이 호출은 **/prebuilt/receipt/analyzeResults/{resultID}** 에 전달 하 여 추출 결과를 반환할 수 있는 작업 ID를 반환 합니다.

### <a name="output-format-changes"></a>출력 형식 변경

모든 API 호출에 대 한 JSON 응답에는 새 형식이 있습니다. 일부 키 및 값이 추가, 제거 또는 이름이 변경 되었습니다. 현재 JSON 형식의 예제는 빠른 시작을 참조 하세요.

## <a name="next-steps"></a>다음 단계

[빠른 시작](quickstarts/curl-train-extract.md)을 수행하여 [Form Recognizer API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)를 시작합니다.