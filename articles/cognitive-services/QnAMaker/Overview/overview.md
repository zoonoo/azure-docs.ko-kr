---
title: QnA Maker란?
titleSuffix: Azure Cognitive Services
description: QnA Maker를 사용하면 FAQ 문서 또는 URL 및 제품 설명서와 같은 반구조적 내용에서 질문과 대답 서비스를 사용할 수 있습니다.
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: overview
ms.date: 09/12/2018
ms.author: saneppal
ms.openlocfilehash: 16cb2d21094e70e7968e5ca45ec2582b7e60a430
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2018
ms.locfileid: "45541941"
---
# <a name="what-is-qna-maker"></a>QnA Maker란?

[QnA Maker](https://qnamaker.ai)를 사용하면 FAQ(질문과 대답) 문서 또는 URL 및 제품 설명서와 같은 반구조적 내용에서 질문과 대답 서비스를 사용할 수 있습니다. 사용자 쿼리에 유연한 질문과 대답 모델을 작성하여 자연스러운 대화형 방식으로 사용할 수 있도록 봇을 학습시킬 수 있습니다.

사용하기 쉬운 그래픽 사용자 인터페이스를 사용하면 개발자 환경 없이 서비스를 만들고, 관리하고, 학습하고, 가동 및 실행할 수 있습니다.

![개요](../media/qnamaker-overview-learnabout/overview.png)

## <a name="highlights"></a>주요 사항

- [FAQ 봇을 만드는](https://aka.ms/qnamaker-docs-create-faqbot) 완벽한 **무코드** 환경입니다.
- **더 이상 네트워크 제한이 없습니다**. 트랜잭션 수가 아니라 서비스 호스팅에 대한 비용을 지불합니다. 자세한 내용은 [가격 페이지](https://aka.ms/qnamaker-docs-pricing)를 참조하세요.
- **필요에 따라 크기를 조정합니다**. 시나리오에 맞게 개별 구성 요소에 대해 적절한 SKU를 선택합니다. QnA Maker 서비스에 대한 [용량을 선택하는 방법](https://aka.ms/qnamaker-docs-capacity)을 참조하세요.
- **전체 데이터 준수**가 적용됩니다. 데이터 및 런타임 구성 요소는 사용자의 Azure 구독 및 준수 경계 내에 배포됩니다. 자세한 내용은 아래를 참조하세요.

## <a name="key-qna-maker-processes"></a>키 QnA Maker 프로세스

QnA Maker에서 데이터에 대해 제공하는 두 가지 핵심 서비스는 다음과 같습니다.

* **추출**: 구조적 질문-대답 데이터는 FAQ 및 제품 설명서와 같은 반구조적 데이터 원본에서 추출됩니다. 이 추출은 기술 자료를 만들 때 수행됩니다. 기술 자료는 [여기서](https://aka.ms/qnamaker-docs-createkb) 만듭니다.

* **일치**: 기술 자료가 [학습되고 테스트](https://aka.ms/qnamaker-docs-trainkb)되면 [게시](https://aka.ms/qnamaker-docs-publishkb)합니다. 이렇게 하면 QnA Maker 기술 자료에 엔드포인트를 사용할 수 있으며,이에 따라 봇 또는 앱에서 사용할 수 있습니다. 이 엔드포인트에서는 사용자 질문을 허용하고, 일치 항목에 대한 신뢰도 점수와 함께 기술 자료에서 가장 일치하는 질문/대답을 사용하여 응답합니다.

## <a name="qna-maker-architecture"></a>QnA Maker 아키텍처

QnA Maker 스택을 구성하는 두 가지 요소는 다음과 같습니다.

1. **QnA Maker 관리 서비스(제어 평면)**: 초기 만들기, 업데이트, 학습 및 게시가 포함된 QnA Maker 기술 자료에 대한 관리 환경입니다. 이러한 작업은 [포털](https://qnamaker.ai) 또는 [관리 API](https://aka.ms/qnamaker-v4-apis)를 통해 수행할 수 있습니다. 관리 서비스는 아래의 런타임 구성 요소와 통신합니다.

2. **QnA Maker 런타임(데이터 평면)**: 데이터 및 런타임은 선택한 지역에 있는 사용자의 Azure 구독에 배포됩니다. 고객 질문/대답 콘텐츠는 [Azure Search](https://azure.microsoft.com/services/search/)에 저장되고 런타임은 [App 서비스](https://azure.microsoft.com/services/app-service/)로 배포됩니다. 필요에 따라 분석을 위해 [Application Insights](https://azure.microsoft.com/services/application-insights/) 리소스를 배포하도록 선택할 수도 있습니다.

![아키텍처](../media/qnamaker-overview-learnabout/architecture.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [QnA Maker 서비스 만들기](../how-to/set-up-qnamaker-service-azure.md)
