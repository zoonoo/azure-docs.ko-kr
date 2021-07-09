---
title: 질문과 대답 - Translator
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services의 Translator API에 대한 질문과 대답의 대답을 가져옵니다.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/18/2021
ms.author: lajanuar
ms.openlocfilehash: db1d981305ee62267f72f3050475c88dbcd5cc54
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110116647"
---
# <a name="frequently-asked-questionstranslator-api"></a>질문과 대답 - Translator API

## <a name="how-does-translator-count-characters"></a>Translator는 문자를 어떻게 계산하나요?

Translator는 유니코드에 정의된 모든 코드 포인트를 문자로 계산합니다. 여러 언어로 번역되는 단일 API 호출에서 요청이 이루어진 경우라도 각 번역은 별도의 번역으로 계산됩니다. 응답의 길이는 중요하지 않으며 요청, 단어, 바이트 또는 문장의 수는 문자 수와 관련이 없습니다.

Translator는 다음 입력을 계산합니다.

* 요청 본문에서 Translator에 전달된 텍스트
  * `Text`: [번역](reference/v3-0-translate.md), [변환](reference/v3-0-transliterate.md) 및 [사전 조회 메서드](reference/v3-0-dictionary-lookup.md)를 사용하는 경우
  * `Text` 및 `Translation`: [사전 예제 메서드](reference/v3-0-dictionary-examples.md)를 사용하는 경우

* 모든 마크업: 요청 본문 텍스트 필드 내 HTML, XML 태그 등 요청을 빌드하는 데 사용되는 JSON 표기법(예: 키 “텍스트:”)은 계산되지 **않습니다**.
* 개별 문자
* 문장 부호
* 공백, 탭, 태그 또는 공백 문자
* 이전에 동일한 텍스트를 번역한 경우라도 반복되는 번역 변환 함수에 제출된 모든 문자는 내용이 변경되지 않았거나 원본 및 대상 언어가 동일한 경우에도 계산됩니다.

중국어 및 일본어 간지와 같은 그래픽 기호를 기반으로 하는 스크립트의 경우 Translator 서비스는 유니코드 코드 포인트의 수를 계산합니다. 기호당 한 문자 예외: 유니코드 서로게이트 쌍은 두 문자로 계산됩니다.

**Detect** 및 **BreakSentence** 메서드 호출은 문자 사용에 계산되지 않습니다. 그러나 Detect 및 BreakSentence 메서드 호출은 개수에 포함되는 다른 함수의 사용과 적절한 비율을 유지해야 합니다. Detect 또는 BreakSentence 호출 수가 다른 메서드의 수를 100배 이상 초과하는 경우 Microsoft는 사용자의 Detect 및 BreakSentence 메서드의 사용을 제한할 권리가 있습니다.

## <a name="where-can-i-see-my-monthly-usage"></a>월간 사용량은 어디에서 볼 수 있나요?

[Azure 가격 책정 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용하여 비용을 예상할 수 있습니다. Azure Portal의 사용자 계정에서 Azure 서비스에 대한 Azure 경고를 모니터링하고, 보고, 추가할 수도 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Translator 리소스 개요 페이지로 이동합니다.
1. Translator 리소스에 대한 **구독** 을 선택합니다.

    :::image type="content" source="media/azure-portal-overview.png" alt-text="Azure Portal 개요 페이지의 구독 링크 스크린샷":::

2. 왼쪽 레일의 **Cost Management** 아래에서 원하는 항목을 선택합니다.

    :::image type="content" source="media/azure-portal-cost-management.png" alt-text="Azure Portal에서 비용 관리 리소스 링크의 스크린샷":::

## <a name="is-attribution-required-when-using-translator"></a>Translator를 사용할 때 특성이 필요한가요?

텍스트 및 음성 번역에 Translator를 사용하는 경우 특성은 필요하지 않습니다. 사용자가 보고 있는 콘텐츠가 머신에서 번역되고 있음을 사용자에게 알리는 것이 좋습니다.

특성이 있으면 [Translator 특성 지침](https://www.microsoft.com/translator/business/attribution/)을 따라야 합니다.

## <a name="is-translator-a-replacement-for-human-translator"></a>Translator가 휴먼 번역기를 대체하나요?

아니요, 둘 다 통신을 위한 필수 도구로 자리하고 있습니다. 콘텐츠 수량, 생성 속도 및 예산 제약 조건으로 인해 휴먼 번역을 사용할 수 없는 경우 머신 번역을 사용합니다.

머신 번역은 휴먼 번역을 사용하기 전에 여러 [LSP(언어 서비스 공급자)](https://www.microsoft.com/translator/business/partners/) 파트너가 첫 번째 통과로 사용했으며 생산성을 최대 50%까지 향상시킬 수 있습니다. LSP 파트너 목록은 Translator 파트너 페이지를 방문하세요.

---
> [!TIP]
> 여기 FAQ에서 질문에 대한 답변을 찾을 수 없는 경우에는 [StackOverflow](https://stackoverflow.com/search?q=%5Bmicrosoft-cognitive%5D+or+%5Bmicrosoft-cognitive%5D+translator&s=34bf0ce2-b6b3-4355-86a6-d45a1121fe27)에서 Translator API 커뮤니티에 문의해 보세요.