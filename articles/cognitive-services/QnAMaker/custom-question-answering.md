---
title: QnA Maker 관리형의 이름이 이제 사용자 지정 질문 답변으로 변경됨
titleSuffix: Azure Cognitive Services
description: 이 문서에는 QnA Maker 기능 변경에 대한 소식이 포함되어 있습니다.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/11/2021
ms.custom: references_regions
ms.openlocfilehash: da6d19a20a31e4cc4c4fac5bb704cb193611dbbc
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110382652"
---
# <a name="qna-maker-managed-is-now-renamed-to-custom-question-answering"></a>QnA Maker 관리형의 이름이 이제 사용자 지정 질문 답변으로 변경됨

[QnA Maker 관리형(미리 보기)](https://techcommunity.microsoft.com/t5/azure-ai/introducing-qna-maker-managed-now-in-public-preview/ba-p/1845575)은 2020년 11월에 무료 공개 미리 보기 제품으로 출시되었습니다. 딥 러닝 순위매기기를 사용하여 향상된 관련성, 정확한 답변 및 엔드투엔드 지역 지원을 비롯한 몇 가지 새로운 기능이 도입되었습니다. 

Cognitive Services의 언어 제품을 통합하는 과정에서 QnA Maker 관리형이 이제 Text Analytics 내 기능이 되었고, 사용자 지정 질문 답변으로 이름이 변경되었습니다.  

## <a name="creating-a-new-custom-question-answering-service"></a>새 사용자 지정 질문 답변 서비스 만들기

질문 답변 및 엔터티 인식, 감정 분석 등의 기타 기능을 사용하는 [Text Analytics 리소스](https://ms.portal.azure.com/?quickstart=true#create/Microsoft.CognitiveServicesTextAnalytics)를 만듭니다.  

이제 새 Text Analytics 리소스를 만들 때 포함하려는 기능을 선택할 수 있습니다. **사용자 지정 질문 답변(미리 보기)** 을 선택하고 계속해서 리소스를 만듭니다.  

> [!div class="mx-imgBorder"]
> [ ![사용자 지정 질문 답변 기능이 선택된 Text Analytics 리소스 만들기 UI 메뉴의 스크린샷]( ./media/select-feature.png) ]( ./media/select-feature.png#lightbox)


이제 더 이상 QnA Maker 만들기 흐름에서 QnA Maker 관리형 리소스를 만들 수 없습니다. 대신 Text Analytics 서비스로 리디렉션됩니다. QnA Maker 안정적 릴리스에 대한 변경 사항은 없습니다. 

> [!div class="mx-imgBorder"]
> [ ![리소스 만들기 메뉴의 스크린샷]( ./media/create-resource.png) ]( ./media/create-resource.png#lightbox)

## <a name="details"></a>세부 정보

- 기존의 모든 QnA Maker 관리형(미리 보기) 리소스는 이전과 동일하게 계속 작동합니다. 현재 이러한 리소스에 대해 필요한 작업은 없습니다.
- 사용자 지정 질문 답변(미리 보기)의 만들기 흐름이 주된 변경 내용입니다. 서비스, 포털, 엔드포인트, SDK 등은 이전과 동일하게 유지됩니다.
- 사용자 지정 질문 답변(미리 보기)은 무료 공개 미리 보기로 계속 제공됩니다.
- 사용자 지정 질문 답변(미리 보기)은 다음 지역에서 제공됩니다.
    - 미국 중남부
    - 북유럽
    - 오스트레일리아 동부

## <a name="next-steps"></a>다음 단계

* [QnA Maker 클라이언트 라이브러리 시작](./quickstarts/quickstart-sdk.md)
* [QnA Maker 포털 시작](./quickstarts/create-publish-knowledge-base.md)


