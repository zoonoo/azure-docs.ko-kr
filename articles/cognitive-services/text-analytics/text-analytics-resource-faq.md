---
title: Text Analytics API에 대한 FAQ(질문과 대답)
titleSuffix: Azure Cognitive Services
description: Text Analytics API와 관련된 일반적인 질문에 대한 답변을 얻습니다.
services: cognitive-services
author: HeidiSteen
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: heidist
ms.openlocfilehash: aa1c5b048c3ef339d01a3a63fd1d565b888ffbbb
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/14/2018
ms.locfileid: "45603399"
---
# <a name="frequently-asked-questions-faq-about-the-text-analytics-cognitive-service"></a>Text Analytics Cognitive Service에 대한 FAQ(질문과 대답)

 Azure에서 Microsoft Cognitive Services용 Text Analytics API와 관련된 개념, 코드 및 시나리오에 대한 일반적인 질문에 대한 대답을 찾습니다.

## <a name="can-text-analytics-identify-sarcasm"></a>Text Analytics에서 비꼬는 표현을 식별할 수 있나요?

분위기 감지보다는 긍정적-부정적 감정이 분석됩니다.

항상 감정 분석이 어느 정도 부정확할 수 있지만 숨은 의미나 콘텐츠의 하위 텍스트가 없을 때 이 모델이 가장 유용합니다. 역설, 비꼬기, 유머, 미묘한 의미 차이가 있는 비슷한 콘텐츠는 문화적 상황 및 의도를 전달하는 규범에 좌우됩니다. 이러한 유형의 콘텐츠가 분석하기 가장 까다롭습니다. 일반적으로 분석기가 생성한 점수와 인간의 주관적 평가 사이에서 불일치가 가장 크게 나타나는 경우는 미묘한 의미 차이가 있는 콘텐츠입니다.

## <a name="can-i-add-my-own-training-data-or-models"></a>나만의 학습 데이터 또는 모델을 추가할 수 있나요?

아니요. 모델은 미리 학습됩니다. 업로드된 데이터에 대해 사용 가능한 유일한 작업은 점수 매기기, 핵심 구 추출 및 언어 감지입니다. 사용자 지정 모델은 호스트하지 않습니다. 사용자 지정 기계 학습 모델을 만들고 호스트하려면 [Microsoft R Server의 Machine Learning 기능](https://docs.microsoft.com/r-server/r/concept-what-is-the-microsoftml-package)을 고려하세요.

## <a name="can-i-request-additional-languages"></a>추가 언어를 요청할 수 있나요?

[선택한 언어 수](text-analytics-supported-languages.md)에 대해 감정 분석 및 핵심 구 추출을 사용할 수 있습니다. 자연어 처리는 복잡하며 새 기능이 출시되기 전에 실질적인 테스트가 필요합니다. 이러한 이유로 숙성 시간이 좀 더 필요한 기능에는 아무도 의존하지 않도록 지원 정보를 미리 발표하지 않으려고 합니다. 

다음에 제공할 언어 지원에 대한 순서를 지정하는 데 도움이 되도록 [사용자 의견](https://cognitive.uservoice.com/forums/555922-text-analytics)에서 특정 언어에 투표하세요. 

## <a name="why-does-key-phrase-extraction-return-some-words-but-not-others"></a>핵심 구 추출 시 일부 언어만 반환되는 이유는 무엇인가요?

핵심 구 추출은 필수적이지 않은 단어와 독립 형용사를 제거합니다. "spectacular views" 또는 "foggy weather"와 같은 형용사-명사 조합은 함께 반환됩니다.

일반적으로 출력은 문장의 명사 및 목적어로 구성되어 있습니다. 출력은 중요도 순서로 표시되며 첫 번째 구가 가장 중요합니다. 중요도는 특정 개념이 언급되는 횟수 또는 해당 요소와 텍스트의 다른 요소 간 관계로 측정됩니다.

## <a name="why-does-output-vary-given-identical-inputs"></a>동일한 입력이 제공되어도 출력이 다른 이유는 무엇인가요?

변경이 대폭 진행될 경우 모델 및 알고리즘 개선 사항이 발표되고, 업데이트가 경미한 경우에는 별다른 알림 없이 서비스에 추가됩니다. 시간이 지남에 따라, 같은 텍스트를 입력해도 감정 점수가 다르거나 핵심 구 출력이 다르다는 것을 알 수 있습니다. 이러한 현상은 클라우드에서 관리되는 기계 학습 리소스를 사용하게 되기 때문에 발생합니다.

## <a name="next-steps"></a>다음 단계

없는 기능 또는 특징에 대해 질문이 있으면 [사용자 의견 웹 사이트](https://cognitive.uservoice.com/forums/555922-text-analytics)에서 요청하거나 투표하세요.

## <a name="see-also"></a>참고 항목

 [StackOverflow: Text Analytics API](https://stackoverflow.com/questions/tagged/text-analytics-api)   
 [StackOverflow: Cognitive Services](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
