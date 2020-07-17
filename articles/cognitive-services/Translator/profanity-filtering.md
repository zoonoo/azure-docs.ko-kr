---
title: 불경 한 필터-번역기
titleSuffix: Azure Cognitive Services
description: 비속어 필터링을 사용 하 여 Azure Cognitive Services Translator의 텍스트에서 번역 된 사용 금지 수준을 결정 합니다.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 7ebfe766e6362a3f62e70db8bf2dcae370aceee3
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996162"
---
# <a name="add-profanity-filtering-with-the-translator"></a>변환기를 사용 하 여 사용 금지 필터링 추가

일반적으로 Translator 서비스는 원본에 있는 욕설을 번역에서 유지합니다. 욕설의 정도와 단어를 욕설로 만드는 컨텍스트는 문화권마다 다릅니다. 따라서 대상 언어에서 욕설의 정도가 증폭되거나 감소할 수 있습니다.

원본 텍스트에 욕설이 있는 경우에도 번역에서 욕설을 표시하지 않으려는 경우 Translate() 메서드에 제공되는 욕설 필터링 옵션을 사용할 수 있습니다. 이 옵션을 사용하면 욕설을 삭제할지, 적절한 태그로 표시할지 또는 아무 작업도 수행하지 않을지를 선택할 수 있습니다.

ProfanityAction () 메서드는 새 요소 ""를 포함 하는 "options" 매개 변수를 사용 합니다. ProfanityAction의 허용 되는 값은 "NoAction", "표시 된" 및 "삭제 됨"입니다.

## <a name="accepted-values-of-profanityaction-and-examples"></a>ProfanityAction에 허용되는 값과 예
|ProfanityAction 값 | 작업 | 예: 원본 - 일본어 | 예: 대상 - 영어|
| :---|:---|:---|:---|
| NoAction | 기본값 옵션을 설정하지 않는 것과 같습니다. 욕설이 원본에서 대상으로 전달됩니다. | 彼は変態です。 | He is a jerk. |
| Marked | 비 속한 단어가 \<profanity> XML 태그로 둘러싸여 있습니다. \</profanity> | 彼は変態です。 | \<profanity>Jerk \</profanity> 입니다. |
| 삭제됨 | 욕설 단어가 바뀌지 않고 출력에서 제거됩니다. | 彼は。 | He is a. |

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [변환기 호출을 사용 하 여 사용 금지 필터링 적용](reference/v3-0-translate.md)
