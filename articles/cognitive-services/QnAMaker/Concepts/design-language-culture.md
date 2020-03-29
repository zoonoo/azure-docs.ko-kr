---
title: 언어 디자인 - QnA 메이커
description: QnA Maker 리소스와 해당 리소스 내부의 모든 기술 자료는 단일 언어를 지원합니다. 단일 언어는 쿼리에 가장 적합한 응답 결과를 제공하는 데 필요합니다.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 5cb1dcd35649debbafd2e234606ad4c9d6906ea6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76846355"
---
# <a name="design-knowledge-base-for-content-language"></a>콘텐츠 언어에 대한 설계 기술 자료

QnA Maker 리소스와 해당 리소스 내의 모든 기술 자료는 단일 언어를 지원합니다. 단일 언어는 쿼리에 가장 적합한 응답 결과를 제공하는 데 필요합니다.

## <a name="single-language-per-resource"></a>리소스당 단일 언어

언어 지원에 대한 QnA 메이커 고려 사항:

* QnA Maker 서비스와 모든 지식 기반은 하나의 언어만 지원합니다.
* 서비스의 첫 번째 기술 자료가 만들어지면 언어가 명시적으로 설정됩니다.
* 언어는 기술 자료가 생성될 때 추가된 파일 및 URL에서 결정됩니다.
* 서비스의 다른 기술 자료에 대해 언어를 변경할 수 없습니다.
* 언어는 코그너티브 #1(랭커 #1)과 QnA Maker 서비스(랭커 #2)에서 쿼리에 대한 최상의 답변을 생성하는 데 사용됩니다.

## <a name="supporting-multiple-languages"></a>여러 언어 지원

여러 언어가 포함된 기술 자료 시스템을 지원해야 하는 경우 다음 방법 중 하나를 선택할 수 있습니다.

* 번역 [텍스트 서비스를](../../translator/translator-info-overview.md) 사용하여 질문을 단일 언어로 번역한 후 기술 자료로 질문을 보냅니다. 이를 통해 단일 언어의 품질과 대체 질문 및 답변의 품질에 집중할 수 있습니다.
* 모든 언어에 대해 해당 리소스 내부에 QnA Maker 리소스 및 기술 자료가 생성됩니다. 이를 통해 별도의 대체 질문을 관리하고 각 언어에 대해 더 미묘한 텍스트를 답변할 수 있습니다. 이렇게 하면 유연성이 훨씬 높아지지만 모든 언어에서 질문이나 답변이 변경될 때 유지 관리 비용이 훨씬 더 많이 필요합니다.

QnA [메이커에 지원되는 언어를](../overview/language-support.md) 검토합니다.

### <a name="support-each-language-with-a-qna-maker-resource"></a>QnA 메이커 리소스로 각 언어 지원

* 모든 언어에 대한 QnA 메이커 리소스 만들기
* 해당 언어에 대한 파일 및 URL만 추가
* 리소스에 대한 명명 규칙을 사용하여 언어를 식별합니다. 프랑스어 문서에 `qna-maker-fr` 대한 모든 지식 자료에 대한 예입니다.

## <a name="next-steps"></a>다음 단계

답변에 대한 기술 기반을 쿼리하는 방법에 대한 [개념을](query-knowledge-base.md) 알아봅니다.