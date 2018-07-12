---
title: Microsoft Translator Text API를 사용한 욕설 필터링 | Microsoft Docs
description: Microsoft Translator Text API에서 욕설 필터링을 사용합니다.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: a7172e1e8aa336c011fb06e93fc5c4b54d26a3cd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374350"
---
# <a name="how-to-add-profanity-filtering-with-the-microsoft-translator-text-api"></a>Microsoft Translator Text API를 사용한 욕설 필터링을 추가하는 방법

일반적으로 Translator 서비스는 원본에 있는 욕설을 번역에서 유지합니다. 욕설의 정도와 단어를 욕설로 만드는 컨텍스트는 문화권마다 다릅니다. 따라서 대상 언어에서 욕설의 정도가 증폭되거나 감소할 수 있습니다.

원본 텍스트에 욕설이 있는지 여부에 관계없이 번역에서 욕설을 표시하지 않으려면 Translate() 메서드의 욕설 필터링 옵션을 사용할 수 있습니다. 이 옵션을 사용하면 욕설을 삭제할지, 적절한 태그로 표시할지 또는 아무 작업도 수행하지 않을지를 선택할 수 있습니다.

Translate() 메서드는 새 요소인 “ProfanityAction”이 포함된 “options” 매개 변수를 사용합니다. ProfanityAction에 허용되는 값은 “NoAction”, “Marked” 및 “Deleted”입니다.

## <a name="accepted-values-of-profanityaction-and-examples"></a>ProfanityAction에 허용되는 값과 예
|ProfanityAction 값 | 조치 | 예: 원본 - 일본어 | 예: 대상 - 영어|
| :---|:---|:---|:---|
| NoAction | 기본값 옵션 설정 안 함과 동일합니다. 욕설이 원본에서 대상으로 전달됩니다. | 彼は変態です。 | He is a jerk. |
| Marked | 욕설 단어가 XML 태그 \<profanity> … \</profanity>로 묶입니다. | 彼は変態です。 | He is a \<profanity>jerk\</profanity>. |
| Deleted | 욕설 단어가 바뀌지 않고 출력에서 제거됩니다. | 彼は。 | He is a. |

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Translator API 호출을 사용하여 욕설 필터링 적용](reference/v3-0-translate.md)

