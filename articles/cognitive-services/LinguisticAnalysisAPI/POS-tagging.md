---
title: 음성 부분 태그 지정 - Linguistic Analysis API
description: Linguistic Analysis API의 음성 부분 태그 지정이 텍스트에서 각 단어의 범주 또는 음성 부분을 식별하는 방법을 알아봅니다.
services: cognitive-services
author: RichardSunMS
manager: cgronlun
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 09/27/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 41383b5bbad0eb5c32b98c57f3ea35e6018993e8
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55218124"
---
# <a name="part-of-speech-tagging"></a>음성 부분 태그 지정

> [!IMPORTANT]
> Linguistic Analysis 미리 보기는 2018년 8월 9일부로 서비스 해제되었습니다. 텍스트 처리 및 분석에는 [Azure Machine Learning 텍스트 분석 모듈](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics)을 사용하는 것이 좋습니다.

## <a name="background-and-motivation"></a>배경 및 동기

텍스트를 문장 및 토큰으로 구분하고 나면 분석의 다음 단계는 각 단어의 범주 또는 음성 부분을 식별하는 것입니다.
여기에는 *명사*(일반적으로 사람, 장소, 사물, 아이디어 등을 나타냄) 및 *동사*(일반적으로 동작, 상태의 변화 등을 나타냄)와 같은 범주가 포함됩니다. 일부 단어의 경우 음성 부분이 모호하지 않지만(예를 들어, *quagmire(수렁)* 은 명사로만 사용됨), 많은 경우 알기 어렵습니다.
*Table(테이블)* 은 사람이 앉는 위치(또는 숫자의 2-D 레이아웃)일 수 있지만, “table a discussion(논의를 상정하다)”라고도 할 수 있습니다.

## <a name="list-of-part-of-speech-tags"></a>음성 부분 태그 지정의 목록

| 태그 | 설명 | 예제 단어 |
|-----|-------------|---------------|
| $ | 달러 | $ |
| \`\` | 여는 따옴표 | \` \`\` |
| '' | 닫는 따옴표 | ' '' |
| ( | 여는 괄호 | ( [ { |
| ) | 닫는 괄호 | ) ] } |
| . | 쉼표 | . |
| -- | 대시 | -- |
| . | 문장 종결자 | . ! ? |
| : | 콜론 또는 줄임표 | : ; ... |
| CC | 접속사 | and but or yet|
| CD | 숫자, 기수 | nine 20 1980 '96 |
| DT | 한정사 |a the an all both neither|
| EX | 존재에 관한 there | there |
| FW | 외래어 | enfant terrible hoi polloi je ne sais quoi |
| IN | 전치사 또는 종속 접속사| in inside if upon whether |
| JJ | 형용사 또는 숫자, 서수 | ninth pretty execrable multimodal |
| JJR | 비교급 형용사 | better faster cheaper |
| JJS | 최상급 형용사 | best fastest cheapest |
| LS | 목록 항목 표식 | (a) (b) 1 2 A B A. B. |
| MD | 조동사 | can may shall will could might should ought |
| NN | 보통 명사, 단수 명사 또는 복수 명사 | potato money shoe |
| NNP | 단수형 고유 명사 | Kennedy Roosevelt Chicago Weehauken |
| NNPS | 복수형 고유 명사 | Springfields Bushes |
| NNS | 복수형 일반 명사 | pieces mice fields |
| PDT | 전치 한정사 | all both half many quite such sure this |
| POS | 소유격 표식 | ' 's |
| PRP | 인칭 대명사 | she he it I we they you |
| PRP$ | 소유대명사 | hers his its my our their your |
| RB | 부사 | clinically only |
| RBR | 비교급 부사 | further gloomier grander graver greater grimmer harder harsher healthier heavier higher however larger later leaner lengthier less-perfectly lesser lonelier longer louder lower more ... |
| RBS | 최상급 부사 | best biggest bluntest earliest farthest first furthest hardest heartiest highest largest least less most nearest second tightest worst |
| RP | 소사 | on off up out about |
| SYM | 기호 | % & |
| TO | 전치사 또는 부정사로서의 “to” | to |
| UH | 감탄사 | uh hooray howdy hello |
| VB | 기본형 동사 | give assign fly |
| VBD | 과거 시제 동사 | gave assigned flew |
| VBG | 현재 분사 동사 또는 동명사 | giving assigning flying |
| VBN | 과거 분사 동사 | given assigned flown |
| VBP | 현재 시제 동사, 삼인칭 단수 아님 | give assign fly |
| VBZ | 현재 시제 동사, 삼인칭 단수 | gives assigns flies |
| WDT | WH 한정사 | that what which |
| WP | WH 대명사 | who whom |
| WP$ | WH 소유대명사 | whose |
| WRB | Wh 부사 | how however whenever where |

## <a name="specification"></a>사양

토큰화의 경우 [Penn Treebank](https://catalog.ldc.upenn.edu/LDC99T42)의 사양을 사용합니다.
