---
title: Form Recognizer의 새로운 기능
titleSuffix: Azure Cognitive Services
description: 양식 인식기 API의 최신 변경 내용을 이해합니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 5f8c66db491b93278fedf1378d3df86e7ce5fdbf
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531085"
---
# <a name="whats-new-in-form-recognizer"></a>Form Recognizer의 새로운 기능

양식 인식기 서비스는 지속적으로 업데이트됩니다. 이 문서를 사용하여 기능 개선, 수정 및 문서 업데이트를 최신 상태로 유지하십시오.

> [!NOTE]
> 양식 인식기의 빠른 시작 및 안내는 지정하지 않는 한 항상 최신 버전의 API를 사용합니다.

## <a name="march-2020"></a>2020년 3월 

### <a name="new-features"></a>새로운 기능

* **레이블 지정값 유형** 이제 양식 인식기 샘플 레이블 지정 도구를 사용하여 레이블을 지정하는 값 유형을 지정할 수 있습니다. 다음 값 유형 및 변형은 현재 지원됩니다.
  * `string`
    * 기본값, `no-whitespaces`"`alphanumeric`
  * `number`
    * 기본`currency`
  * `date` 
    * `dmy`기본값, `mdy`"`ymd`
  * `time`
  * `integer`

  이 기능을 사용하는 방법에 대해 알아보려면 [샘플 라벨링 도구](./quickstarts/label-tool.md#specify-tag-value-types) 가이드를 참조하십시오.


* **표 시각화** 이제 샘플 레이블 지정 도구에 문서에서 인식된 테이블이 표시됩니다. 이렇게 하면 레이블을 지정하고 분석하기 전에 문서에서 인식되고 추출된 테이블을 볼 수 있습니다. 이 기능은 레이어 옵션을 사용하여 켜기/끄기를 전환할 수 있습니다.

  다음은 테이블을 인식하고 추출하는 방법의 예입니다.

  > [!div class="mx-imgBorder"]
  > ![샘플 라벨링 도구를 사용한 테이블 시각화](./media/whats-new/formre-table-viz.png)

    추출된 테이블은 아래의 `"pageResults"`JSON 출력에서 사용할 수 있습니다.

  > [!IMPORTANT]
  > 테이블 에 레이블지정은 지원되지 않습니다. 테이블이 자동으로 인식되지 않고 추가되는 경우 테이블은 키/값 쌍으로만 레이블을 지정할 수 있습니다. 테이블에 키/값 쌍으로 레이블을 지정할 때 각 셀에 고유한 값으로 레이블을 지정합니다.

### <a name="extraction-enhancements"></a>추출 개선 사항

이 릴리스에는 추출 향상 및 정확도 향상, 특히 동일한 텍스트 줄에서 여러 키/값 쌍에 레이블을 지정하고 추출하는 기능이 포함됩니다. 
 
### <a name="sample-labeling-tool-is-now-open-source"></a>샘플 라벨링 도구가 이제 오픈 소스가 되었습니다.

양식 인식기 샘플 라벨링 도구를 이제 오픈 소스 프로젝트로 사용할 수 있습니다. 솔루션 내에 통합하고 고객별 변경을 통해 필요에 맞게 변경할 수 있습니다.

양식 인식기 샘플 레이블 지정 도구에 대한 자세한 내용은 [GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md)에서 사용할 수 있는 설명서를 참조하십시오.

### <a name="tls-12-enforcement"></a>TLS 1.2 적용

이제 TLS 1.2는 이 서비스에 대한 모든 HTTP 요청에 적용됩니다. 자세한 내용은 [Azure Cognitive Services 보안](../cognitive-services-security.md)을 참조하세요.

## <a name="january-2020"></a>2020년 1월

이 릴리스에서는 양식 인식기 2.0(미리 보기)을 소개합니다. 아래 섹션에서는 새로운 기능, 향상된 기능 및 변경 사항에 대한 자세한 정보를 확인할 수 있습니다. 

### <a name="new-features"></a>새로운 기능

* **사용자 지정 모델**
  * **레이블이 있는 학습** 이제 수동으로 레이블이 지정된 데이터로 사용자 지정 모델을 학습할 수 있습니다. 이 방법은 모델 성능이 향상되며, 복잡한 양식 또는 키 없는 값을 포함하는 양식과 함께 작동하는 모델을 생성할 수 있습니다.
  * **비동기 API** 비동기 API 호출을 사용하여 대용량 데이터 세트 및 파일을 학습하고 분석할 수 있습니다.
  * **TIFF 파일 지원** 이제 TIFF 문서에서 데이터를 학습하고 추출할 수 있습니다.
  * **추출 정확도 향상**

* **미리 작성된 영수증 모델**
  * **팁 금액** 이제 팁 금액 및 기타 필기 값을 추출할 수 있습니다.
  * **광고 항목 추출** 영수증에서 광고 항목 값을 추출할 수 있습니다.
  * **신뢰도 값** 추출된 각 값에 대한 모델의 신뢰도를 볼 수 있습니다.
  * **추출 정확도 향상**

* **레이아웃 추출** 이제 레이아웃 API를 사용하여 양식에서 텍스트 데이터 및 테이블 데이터를 추출할 수 있습니다.

### <a name="custom-model-api-changes"></a>사용자 지정 모델 API 변경

사용자 지정 모델을 교육하고 사용하기 위한 모든 API의 이름이 바뀌었으며 일부 동기 메서드는 이제 비동기적입니다. 주요 변경 사항은 다음과 같습니다.

* 모델을 학습하는 프로세스는 이제 비동기입니다. **/custom/model** API 호출을 통해 교육을 시작합니다. 이 호출은 **사용자 지정/모델/{modelID}에** 전달하여 학습 결과를 반환할 수 있는 작업 ID를 반환합니다.
* 이제 키/값 추출은 **/custom/모델/{modelID}/분석** API 호출에 의해 시작됩니다. 이 호출은 **사용자 지정/모델/{modelID}/analyzeResults/{resultID}에** 전달하여 추출 결과를 반환할 수 있는 작업 ID를 반환합니다.
* 이제 열차 작업에 대한 작업 아이디는 **작업 위치** 헤더가 아닌 HTTP 응답의 **위치** 헤더에서 찾을 수 있습니다.

### <a name="receipt-api-changes"></a>영수증 API 변경 사항

판매 영수증을 읽는 API의 이름이 변경되었습니다.

* 이제 수신 데이터 추출은 **/미리 빌드/수신/분석** API 호출에 의해 시작됩니다. 이 호출은 **/prebuilt/receipt/analyzeResults/{resultID}로** 전달하여 추출 결과를 반환할 수 있는 작업 ID를 반환합니다.

### <a name="output-format-changes"></a>출력 형식 변경

모든 API 호출에 대한 JSON 응답에는 새로운 형식이 있습니다. 일부 키와 값이 추가, 제거 또는 이름이 바뀌었습니다. 현재 JSON 형식의 예는 빠른 시작을 참조하십시오.

## <a name="next-steps"></a>다음 단계

[빠른 시작](quickstarts/curl-train-extract.md)을 수행하여 [Form Recognizer API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)를 시작합니다.