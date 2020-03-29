---
title: 문장 페어링 및 정렬 - Custom Translator
titleSuffix: Azure Cognitive Services
description: 훈련이 실행되는 동안, 병렬 문서에 포함된 문장들이 페어링 또는 정렬됩니다. Custom Translator는 하나의 문장과 이 문장의 번역을 읽어서 한 번에 한 문장씩 번역을 학습합니다. 그런 다음 두 문장의 단어와 구문을 서로 정렬합니다.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: cf5b2b84142c9104ea5b3afa3ad179fd0ec07449
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80370132"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>병렬 문서의 문장 페어링 및 정렬

훈련이 실행되는 동안, 병렬 문서에 포함된 문장들이 페어링 또는 정렬됩니다. Custom Translator는 페어링한 문장의 개수를 각 데이터 세트에서 정렬된 문장으로 보고합니다.

## <a name="pairing-and-alignment-process"></a>페어링 및 정렬 프로세스

Custom Translator는 한 번에 한 문장씩 문장의 번역을 학습합니다. 소스에서 문장을 읽고, 타깃에서 이 문장의 번역을 읽습니다. 그런 다음 두 문장의 단어와 구문을 서로 정렬합니다. 이 프로세스를 통해 하나의 문장에 포함된 단어와 구문이 이 문장의 번역에 포함된 동등한 단어와 구문으로 매핑됩니다. 정렬 과정에서는 시스템이 서로가 서로의 번역인 문장을 훈련하도록 확인하는 작업이 이루어집니다.

## <a name="pre-aligned-documents"></a>미리 정렬된 문서

이미 병렬 문서를 가지고 있다면 미리 정렬된 텍스트 파일을 입력하여 문장 정렬 과정을 무시할 수 있습니다. 두 문서에서 모든 문장을 텍스트 파일로 추출하고 한 줄에 한 문장씩 구성한 다음 `.align` 확장자로 업로드하면 됩니다. Custom Translator는 `.align` 확장자가 입력되면 문장 정렬을 건너뜁니다.

최상의 결과를 얻으려면 파일에서 한 줄에 한 문장이 들어가도록 하세요.문장 안에 줄 바꿈 문자가 있으면 정렬이 제대로 이루어지지 않으니 주의하세요.

## <a name="suggested-minimum-number-of-sentences"></a>제안된 최소 문장 수

교육이 성공하려면 아래 표에 각 문서 유형에 필요한 최소 문장 수가 표시됩니다.이 제한은 병렬 문장이 번역 모델을 성공적으로 학습하기에 충분한 고유 어휘를 포함하도록 보장하는 안전망입니다. 일반적인 지침은 더 높은 품질의 모델을 생산해야 인간의 번역 품질의 더 많은 도메인 병렬 문장을 가지고있다.

| 문서 형식   | 제안된 최소 문장 수 | 최대 문장 수 |
|------------|--------------------------------------------|--------------------------------|
| 학습   | 10000                                     | 상한 없음                 |
| 튜닝     | 500                                      | 2,500       |
| 테스트    | 500                                      | 2,500  |
| Dictionary | 0                                          | 상한 없음                 |

> [!NOTE]
> - 교육에 대한 최소 문장 수가 충족되지 않으면 교육이 시작되지 않고 실패합니다. 
> - 튜닝 및 테스트는 선택 사항입니다. 제공하지 않으면 시스템은 유효성 검사 및 테스트에 사용할 교육에서 적절한 백분율을 제거합니다. 
> - 사전 데이터만 사용하여 모델을 학습할 수 있습니다. [사전은 무엇을](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/what-is-dictionary)참조하십시오.

## <a name="next-steps"></a>다음 단계

- Custom Translator에서 [사전](what-is-dictionary.md)을 사용하는 방법을 알아봅니다.
