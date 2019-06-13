---
title: 사전이란? - Custom Translator
titleSuffix: Azure Cognitive Services
description: 사전이란 사용자가 Microsoft Translator에서 항상 동일한 방식으로 번역하길 원하는 구 또는 문장(및 해당 번역)의 목록을 지정하는 정렬된 문서입니다. 사전은 종종 용어집 또는 용어 기반이라고도 합니다.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-pawal
ms.topic: conceptual
ms.openlocfilehash: 4bf112974befd7063b3da8e2b1c1dcbb7ad55608
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66385236"
---
# <a name="what-is-a-dictionary"></a>사전이란?

사전은 구 또는 문장의 목록과 해당 번역을 지정하는 문서의 정렬된 쌍입니다. 사용자가 항상 Microsoft Translator에서 사용자가 사전에 제공한 번역을 사용하여 원본 구 또는 문장의 인스턴스를 번역하길 원하는 경우 학습의 사전을 사용합니다. 사전은 종종 용어집 또는 용어 기반이라고도 합니다. 사전을 사용자가 나열한 모든 용어에 대해 무차별적인 “복사 및 바꾸기”로 생각할 수 있습니다.

사전은 그 뒤에 완벽하게 지원되는 Microsoft NMT(인공신경망 기계 번역) 시스템을 갖춘 언어 쌍의 프로젝트에서만 작동합니다. [언어의 전체 목록을 보려면](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization)합니다.

## <a name="phrase-dictionary"></a>구 사전
모델을 학습할 때 구 사전을 포함하면 나열된 단어 또는 구는 사용자가 지정한 방식으로 번역됩니다. 문장의 나머지는 일반적인 방법으로 번역됩니다. 구 사전을 사용하여 번역하면 안 되는 구를 지정할 수 있습니다. 사전의 원본 및 대상 파일에 번역되지 않은 동일한 구를 제공하면 됩니다.

## <a name="sentence-dictionary"></a>문장 사전
문장 사전을 사용하면 원본 문장에 대한 정확한 대상 번역을 지정할 수 있습니다. 문장 사전 일치가 발생하려면 제출된 전체 문장이 원본 사전 항목과 일치해야 합니다.  문장의 부분만 일치하는 경우에는 항목이 일치하지 않습니다.  일치하는 항목이 발견되면 문장 사전의 대상 항목이 반환됩니다.

## <a name="dictionary-only-trainings"></a>사전 전용 학습
사전 데이터만 사용하여 모델을 학습할 수 있습니다. 이를 위해 모델 만들기를 포함하고 탭하려는 사전 문서만 선택합니다(또는 여러 사전 문서). 이는 사전 전용 학습이므로 최소 학습 문장 수가 필요하지 않습니다. 일반적으로 모델은 표준 학습보다 훨씬 더 빠르게 학습을 완료합니다.  결과 모델은 사용자가 추가한 추가 사전을 사용한 번역에 대해 Microsoft 기준 모델을 사용합니다.  테스트 보고서를 가져올 수 없습니다.

>[!Note]
>Custom Translator에는 문장 정렬 사전 파일이 없으므로 사전 문서의 원본 및 대상 구/문장의 수가 동일해야 하며 정확하게 정렬되어야 합니다.

## <a name="recommendations"></a>권장 사항

- 사전은 학습 데이터를 사용하여 학습된 모델의 대용품이 아닙니다.  사전은 기본적으로 단어 또는 문장을 찾아 바꿉니다.  전체 문장으로 사용자의 교육 자료를 통해 시스템을 학습하는 것이 사전을 사용하는 것보다 일반적으로 더 나은 선택입니다.
- 구 사전은 꼭 필요할 때만 사용해야 합니다. 문장 내에서 구가 바뀌면 해당 문장 내 문맥이 사라지거나 문장의 나머지 부분을 번역하는 데 제한적이 됩니다. 그러면 문장 내 구 또는 단어가 구 사전에 따라 번역되고 문장의 전체 번역 품질이 자주 저하될 것입니다.
- 구 사전은 제품 이름(“Microsoft SQL Server”), 고유 명사(“City of Hamburg”) 또는 제품의 기능(“pivot table”)과 같이 복합 명사에 적합합니다. 동사 또는 형용사는 일반적으로 원본 또는 대상 언어에서 크게 굴절되기 때문에 동일하게 작동하지는 않습니다. 복합 명사 이외의 항목에 대해서는 구 사전 항목 사용을 피하십시오.
- 사전을 사용하는 경우 대/소문자 및 문장 부호는 대상 파일에 제공된 대/소문자 및 문장 부호를 반영합니다. 입력된 문장과 사전 파일의 원본 문장 간 일치 항목을 식별하려고 할 때는 대/소문자 및 문장 부호가 무시됩니다. 예를 들어, 원본 파일의 “City of Hamburg”와 대상 파일의 “Ciudad de hamburg”를 지정한 사전을 사용하는 영어-스페인어 시스템을 학습한다고 가정해 보겠습니다. “city of Hamburg”라는 구가 포함된 문장의 번역을 요청하는 경우 “city of Hamburg”는 내 사전 파일에서 “City of Hamburg”라는 항목에 일치하고, 내 최종 번역에서 “Ciudad de hamburg”로 매핑됩니다.
- 단어가 사전 파일에서 두 번 이상 나타나는 경우 시스템은 항상 마지막으로 제공된 항목을 사용합니다. 사전에는 동일한 단어의 여러 번역이 없어야 합니다.

## <a name="next-steps"></a>다음 단계

- [문서 형식에 대한 지침](document-formats-naming-convention.md)을 읽어보세요.
