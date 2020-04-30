---
title: Form Recognizer의 새로운 기능
titleSuffix: Azure Cognitive Services
description: 양식 인식기 API에 대 한 최신 변경 내용을 이해 합니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 5f8c66db491b93278fedf1378d3df86e7ce5fdbf
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81531085"
---
# <a name="whats-new-in-form-recognizer"></a>Form Recognizer의 새로운 기능

양식 인식기 서비스는 지속적으로 업데이트 됩니다. 이 문서를 사용 하면 향상 된 기능, 수정 사항 및 설명서 업데이트를 최신 상태로 유지할 수 있습니다.

> [!NOTE]
> 폼 인식기에 대 한 빠른 시작 및 가이드는 지정 되지 않은 경우 항상 최신 버전의 API를 사용 합니다.

## <a name="march-2020"></a>2020년 3월 

### <a name="new-features"></a>새 기능

* **레이블 지정을 위한 값 형식** 이제 폼 인식기 샘플 레이블 지정 도구를 사용 하 여 레이블을 지정 하는 값의 형식을 지정할 수 있습니다. 현재 지원되는 값 형식 및 변형은 다음과 같습니다.
  * `string`
    * 기본값, `no-whitespaces`, `alphanumeric`
  * `number`
    * 기본값, `currency`
  * `date` 
    * 기본값, `dmy`, `mdy`, `ymd`
  * `time`
  * `integer`

  이 기능을 사용 하는 방법에 대 한 자세한 내용은 [샘플 레이블 도구](./quickstarts/label-tool.md#specify-tag-value-types) 가이드를 참조 하세요.


* **테이블 시각화** 이제 샘플 레이블 지정 도구는 문서에서 인식 된 테이블을 표시 합니다. 이렇게 하면 레이블 지정 및 분석 전에 문서에서 인식 되 고 추출 된 테이블을 볼 수 있습니다. 계층 옵션을 사용 하 여이 기능을 설정/해제할 수 있습니다.

  다음은 테이블을 인식 하 고 추출 하는 방법의 예입니다.

  > [!div class="mx-imgBorder"]
  > ![샘플 레이블 지정 도구를 사용 하는 테이블 시각화](./media/whats-new/formre-table-viz.png)

    추출 된 테이블은의 JSON 출력에서 사용할 수 `"pageResults"`있습니다.

  > [!IMPORTANT]
  > 테이블 레이블 지정은 지원 되지 않습니다. 테이블이 인식 되지 않고 자동으로 extrated 키/값 쌍 으로만 레이블을 지정할 수 있습니다. 테이블에 키/값 쌍으로 레이블을 지정 하는 경우 각 셀에 고유한 값으로 레이블을 지정 합니다.

### <a name="extraction-enhancements"></a>향상 된 압축 풀기

이 릴리스에는 추출 향상 및 정확성 향상, 특히 동일한 텍스트 줄에서 여러 키/값 쌍에 대 한 레이블을 만들고 추출 하는 기능이 포함 되어 있습니다. 
 
### <a name="sample-labeling-tool-is-now-open-source"></a>이제 샘플 레이블 지정 도구는 오픈 소스입니다.

이제 폼 인식기 샘플 레이블 지정 도구를 오픈 소스 프로젝트로 사용할 수 있습니다. 솔루션 내에서 통합 하 고 요구 사항에 맞게 고객 관련 변경을 수행할 수 있습니다.

폼 인식기 샘플 레이블 지정 도구에 대 한 자세한 내용은 [GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md)에서 제공 되는 설명서를 참조 하세요.

### <a name="tls-12-enforcement"></a>TLS 1.2 적용

이제 TLS 1.2는 이 서비스에 대한 모든 HTTP 요청에 적용됩니다. 자세한 내용은 [Azure Cognitive Services 보안](../cognitive-services-security.md)을 참조하세요.

## <a name="january-2020"></a>2020년 1월

이 릴리스에는 폼 인식기 2.0 (미리 보기)가 도입 되었습니다. 아래 섹션에는 새로운 기능, 향상 된 기능 및 변경 내용에 대 한 자세한 내용이 나와 있습니다. 

### <a name="new-features"></a>새 기능

* **사용자 지정 모델**
  * **레이블로 학습** 이제 수동으로 레이블이 지정 된 데이터를 사용 하 여 사용자 지정 모델을 학습 시킬 수 있습니다. 이 방법은 모델 성능이 향상되며, 복잡한 양식 또는 키 없는 값을 포함하는 양식과 함께 작동하는 모델을 생성할 수 있습니다.
  * **비동기 API** 비동기 API 호출을 사용 하 여 대량 데이터 집합 및 파일을 학습 하 고 분석할 수 있습니다.
  * **TIFF 파일 지원** 이제 TIFF 문서에서 데이터를 학습 하 고 추출할 수 있습니다.
  * **추출 정확도 향상**

* **미리 작성된 영수증 모델**
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