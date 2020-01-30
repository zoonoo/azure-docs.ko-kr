---
title: 언어에 대 한 디자인-QnA Maker
description: QnA Maker 리소스 및 해당 리소스 내의 모든 기술 자료는 단일 언어를 지원 합니다. 단일 언어는 쿼리에 가장 적합 한 답변을 제공 하는 데 필요 합니다.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 5cb1dcd35649debbafd2e234606ad4c9d6906ea6
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846355"
---
# <a name="design-knowledge-base-for-content-language"></a>콘텐츠 언어에 대 한 기술 자료 디자인

QnA Maker 리소스 및 해당 리소스 내의 모든 기술 자료는 단일 언어를 지원 합니다. 단일 언어는 쿼리에 가장 적합 한 답변을 제공 하는 데 필요 합니다.

## <a name="single-language-per-resource"></a>리소스 당 단일 언어

언어 지원에 대 한 QnA Maker 고려 사항:

* QnA Maker 서비스 및 모든 기술 자료는 한 가지 언어만 지원 합니다.
* 서비스의 첫 번째 기술 자료가 생성 될 때 언어가 명시적으로 설정 됩니다.
* 언어는 기술 자료가 만들어질 때 추가 된 파일 및 Url에서 결정 됩니다.
* 서비스의 다른 기술 자료에 대 한 언어를 변경할 수 없습니다.
* 언어는 Cognitive Search 서비스 (ranker #1) 및 QnA Maker 서비스 (ranker #2)에서 쿼리에 대 한 최상의 답을 생성 하는 데 사용 됩니다.

## <a name="supporting-multiple-languages"></a>여러 언어 지원

여러 언어를 포함 하는 기술 자료 시스템을 지원 해야 하는 경우 다음 방법 중 하나를 선택할 수 있습니다.

* 질문을 기술 자료에 보내기 전에 [번역 텍스트 서비스](../../translator/translator-info-overview.md) 를 사용 하 여 질문을 단일 언어로 번역 합니다. 이를 통해 단일 언어의 품질 및 대체 질문과 대답의 품질에 집중할 수 있습니다.
* 모든 언어에 대해 QnA Maker 리소스 및 해당 리소스 내에 기술 자료를 만듭니다. 이렇게 하면 별도의 대체 질문과 대답 텍스트를 관리할 수 있습니다 .이 텍스트는 각 언어에 대해 더 미묘한. 이는 훨씬 더 많은 유연성을 제공 하지만 모든 언어에서 질문이 나 답변이 변경 될 때 훨씬 높은 유지 관리 비용이 필요 합니다.

QnA Maker에 대해 [지원 되는 언어](../overview/language-support.md) 를 검토 합니다.

### <a name="support-each-language-with-a-qna-maker-resource"></a>QnA Maker 리소스를 사용 하 여 각 언어 지원

* 모든 언어에 대 한 QnA Maker 리소스 만들기
* 해당 언어에 대 한 파일 및 Url만 추가
* 리소스에 대 한 명명 규칙을 사용 하 여 언어를 식별 합니다. 예는 프랑스어 문서에 대 한 모든 기술 자료에 대 한 `qna-maker-fr`입니다.

## <a name="next-steps"></a>다음 단계

답변에 대 한 기술 자료를 쿼리 하는 방법에 대 한 [개념](query-knowledge-base.md) 을 알아봅니다.