---
title: '빠른 시작: LUIS(Language Understanding) SDK 클라이언트 라이브러리'
description: C#, Python 또는 JavaScript를 통해 이 빠른 시작을 사용하여 LUIS SDK 클라이언트 라이브러리로 LUIS 앱을 만들고 쿼리합니다.
ms.topic: quickstart
ms.date: 09/01/2020
keywords: Azure, 인공 지능, ai, 자연어 처리, nlp, 자연어 인식, nlu, ai 대화, 대화형 ai, ai 챗봇, 챗봇 작성자, LUIS, nlp ai, luis ai, azure luis, 자연어 이해
ms.custom: devx-track-python, devx-track-javascript, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-diberry-3core
ms.openlocfilehash: 6bcdca85125d44475fadfd195c1dfda88f761f88
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89323116"
---
# <a name="quickstart-language-understanding-luis-sdk-client-libraries-to-create-and-query-your-luis-app"></a>빠른 시작: LUIS 앱을 만들고 쿼리하는 LUIS(Language Understanding) SDK 클라이언트 라이브러리

C#, Python 또는 JavaScript를 통해 이 빠른 시작을 사용하여 LUIS SDK 클라이언트 라이브러리로 LUIS 앱을 만들고 쿼리합니다.

LUIS(Language Understanding)를 사용하면 사용자 지정 기계 학습 인텔리전스를 사용자의 대화형 자연어 텍스트에 적용하여 전반적인 의미를 예측하고 관련된 세부 정보를 끌어올 수 있습니다.

* **작성 SDK** 클라이언트 라이브러리를 사용하면 LUIS 앱을 생성, 편집, 학습 및 게시할 수 있습니다. * **예측 런타임 SDK** 클라이언트 라이브러리를 사용하면 게시된 앱을 쿼리할 수 있습니다.

::: zone pivot="programming-language-csharp"
[!INCLUDE [LUIS development with C# SDK](./includes/sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [LUIS development with Node.js SDK](./includes/sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [LUIS development with Python SDK](./includes/sdk-python.md)]
::: zone-end

## <a name="clean-up-resources"></a>리소스 정리

[LUIS 포털](https://www.luis.ai)에서 앱을 삭제하고 [Azure Portal](https://portal.azure.com/)에서 Azure 리소스를 삭제할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

* 클라이언트 라이브러리에 대한 인증 - 인증 오류는 일반적으로 잘못된 키 및 엔드포인트가 사용되었음을 나타냅니다. 이 빠른 시작은 편의상 예측 런타임에 작성 키와 엔드포인트를 사용하지만 월별 할당량을 아직 사용하지 않은 경우에만 작동합니다. 작성 키와 엔드포인트를 사용할 수 없는 경우 예측 런타임 SDK 클라이언트 라이브러리에 액세스할 때 예측 런타임 키와 엔드포인트를 사용해야 합니다.
* 엔터티 만들기 - 이 자습서에 사용되는 중첩된 기계 학습 엔터티를 만드는 동안 오류가 발생하면 코드를 복사하고 다른 엔터티를 만들기 위해 코드를 변경하지 않았는지 확인합니다.
* 예제 발화 만들기 - 이 자습서에 사용되는 레이블이 지정된 예제 발화를 만드는 동안 오류가 발생하면 코드를 복사하고 다른 레이블이 지정된 예제를 만들기 위해 코드를 변경하지 않았는지 확인합니다.
* 학습 - 학습 오류가 발생하는 경우 이는 일반적으로 빈 앱(예: 예제 발화가 있는 의도 없음) 또는 형식이 잘못된 의도 또는 엔터티가 포함된 앱을 나타냅니다.
* 기타 오류 - 코드가 텍스트 및 JSON 개체를 사용하여 클라이언트 라이브러리를 호출하므로 코드를 변경하지 않았는지 확인합니다.

기타 오류 - 이전 목록에서 다루지 않은 오류가 발생하면 이 페이지의 하단에 피드백을 제공하여 알려주세요. 설치한 클라이언트 라이브러리의 프로그래밍 언어와 버전을 포함합니다. 

## <a name="next-steps"></a>다음 단계

* [LUIS(Language Understanding) API란?](what-is-luis.md)
* [새로운 기능](whats-new.md)
* [의도](luis-concept-intent.md), [엔터티](luis-concept-entity-types.md), [발화 예제](luis-concept-utterance.md) 및 [미리 작성된 엔터티](luis-reference-prebuilt-entities.md)
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code)에서 확인할 수 있습니다.
