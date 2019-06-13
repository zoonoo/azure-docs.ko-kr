---
title: 문장 페어링 및 정렬 - Custom Translator
titleSuffix: Azure Cognitive Services
description: 훈련이 실행되는 동안, 병렬 문서에 포함된 문장들이 페어링 또는 정렬됩니다. Custom Translator는 하나의 문장과 이 문장의 번역을 읽어서 한 번에 한 문장씩 번역을 학습합니다. 그런 다음 두 문장의 단어와 구문을 서로 정렬합니다.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-pawal
ms.topic: conceptual
ms.openlocfilehash: 99538fd18067377efcc9e744e9ec611e0f685273
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389610"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>병렬 문서의 문장 페어링 및 정렬

훈련이 실행되는 동안, 병렬 문서에 포함된 문장들이 페어링 또는 정렬됩니다. Custom Translator는 페어링한 문장의 개수를 각 데이터 세트에서 정렬된 문장으로 보고합니다.

## <a name="pairing-and-alignment-process"></a>페어링 및 정렬 프로세스

Custom Translator는 한 번에 한 문장씩 문장의 번역을 학습합니다. 소스에서 문장을 읽고, 타깃에서 이 문장의 번역을 읽습니다. 그런 다음 두 문장의 단어와 구문을 서로 정렬합니다. 이 프로세스를 통해 하나의 문장에 포함된 단어와 구문이 이 문장의 번역에 포함된 동등한 단어와 구문으로 매핑됩니다. 정렬 과정에서는 시스템이 서로가 서로의 번역인 문장을 훈련하도록 확인하는 작업이 이루어집니다.

## <a name="pre-aligned-documents"></a>미리 정렬된 문서

이미 병렬 문서를 가지고 있다면 미리 정렬된 텍스트 파일을 입력하여 문장 정렬 과정을 무시할 수 있습니다. 두 문서에서 모든 문장을 텍스트 파일로 추출하고 한 줄에 한 문장씩 구성한 다음 `.align` 확장자로 업로드하면 됩니다. Custom Translator는 `.align` 확장자가 입력되면 문장 정렬을 건너뜁니다.

최상의 결과를 얻으려면 파일에서 한 줄에 한 문장이 들어가도록 하세요. 문장 안에 줄 바꿈 문자가 있으면 정렬이 제대로 이루어지지 않으니 주의하세요.

## <a name="suggested-minimum-number-of-extracted-and-aligned-sentences"></a>추출된 문장과 정렬된 문장의 최소 권장 개수

아래 표에서는 훈련을 성공적으로 실시하기 위해 각 데이터 세트에 필요한 추출된 문장과 정렬된 문장의 최소 권장 개수를 보여 줍니다. 추출된 문장의 최소 권장 개수는 정렬된 문장의 최소 권장 개수보다 훨씬 큰데, 이는 문장 정렬 작업이 추출된 모든 문장을 성공적으로 정렬하지 못할 수 있다는 점을 고려한 것입니다.

| 데이터 집합   | 추출된 문장의 최소 권장 개수 | 정렬된 문장의 최소 권장 개수 | 정렬된 문장의 최대 개수 |
|------------|--------------------------------------------|------------------------------------------|--------------------------------|
| 교육   | 10000                                     | 2,000                                    | 상한 없음                 |
| Tuning     | 2,000                                      | 500                                      | 2,500                          |
| 테스트    | 2,000                                      | 500                                      | 2,500                          |
| Dictionary | 0                                          | 0                                        | 상한 없음                 |

## <a name="next-steps"></a>다음 단계

- Custom Translator에서 [사전](what-is-dictionary.md)을 사용하는 방법을 알아봅니다.
