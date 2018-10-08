---
title: 고객 데이터 내보내기 및 삭제 - Language Understanding - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS(Language Understanding Intelligent Service)는 서비스 작동을 위해 고객 콘텐츠를 보존하지만, LUIS 사용자가 데이터 보기, 내보내기 및 삭제에 대한 모든 권한을 갖습니다. 이 작업은 LUIS 웹 포털 또는 LUIS Programmatic API를 통해 수행할 수 있습니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.technology: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 103307e9657f183ed6876ef6ff499666c928690f
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47037493"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Cognitive Services의 LUIS(Language Understanding)에서 고객 데이터 내보내기 및 삭제

## <a name="summary-of-customer-data-request-features"></a>고객 데이터 요청 기능 요약
LUIS(Language Understanding Intelligent Service)는 서비스 작동을 위해 고객 콘텐츠를 보존하지만, LUIS 사용자가 데이터 보기, 내보내기 및 삭제에 대한 모든 권한을 갖습니다. 이 작업은 LUIS 웹 [포털](luis-reference-regions.md) 또는 [LUIS Programmatic API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)를 통해 수행할 수 있습니다.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

고객 콘텐츠는 Microsoft 지역별 Azure Storage에 암호화되어 저장되며 다음을 포함합니다.

- 등록 시 수집된 사용자 계정 콘텐츠
- 모델 빌드에 필요한 학습 데이터(예: 의도 및 엔터티)
- 사용자 모델을 개선하는 데 도움이 되는 런타임에 로깅된 사용자 쿼리
  - 사용자는 요청에 `&log=false`를 추가하여 쿼리 로깅을 해제할 수 있습니다. 자세한 내용은 [여기](luis-resources-faq.md#how-can-i-disable-the-logging-of-utterances)를 참조하세요.

## <a name="deleting-customer-data"></a>고객 데이터 삭제
LUIS 사용자는 LUIS 웹 포털 또는 LUIS Programmatic API를 통해 모든 사용자 콘텐츠를 삭제할 모든 권한이 있습니다. 다음 표에는 두 작업에 도움이 되는 링크가 나와 있습니다.

| | **사용자 계정** | **응용 프로그램** | **발언** | **최종 사용자 쿼리** |
| --- | --- | --- | --- | --- |
| **포털** | [링크](luis-how-to-account-settings.md) | [링크](luis-how-to-start-new-app.md#delete-app) | [링크](luis-how-to-start-new-app.md#delete-app) | [링크](luis-how-to-start-new-app.md#delete-app) |
| **API** | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>고객 데이터 내보내기
LUIS 사용자는 포털에서 데이터를 보기 위한 모든 권한이 있지만 LUIS Programmatic API를 통해 내보내야 합니다. 다음 표에는 LUIS Programmatic API를 통한 데이터 내보내기에 도움이 되는 링크가 나와 있습니다.

| | **사용자 계정** | **응용 프로그램** | **발언** | **최종 사용자 쿼리** |
| --- | --- | --- | --- | --- |
| **API** | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [링크](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [LUIS 지역 참조](./luis-reference-regions.md)
