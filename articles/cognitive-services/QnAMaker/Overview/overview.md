---
title: QnA Maker란?
titleSuffix: Azure Cognitive Services
description: QnA Maker는 사용자의 자연어 질문에 사용자 지정 기계 학습 인텔리전스를 적용하여 가장 적합한 답변을 제공하는 클라우드 기반 API 서비스입니다.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: overview
ms.date: 10/09/2018
ms.author: tulasim
ms.openlocfilehash: 742c18815445b038e85c33a96743790491976945
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901110"
---
# <a name="what-is-qna-maker"></a>QnA Maker란?

QnA Maker는 사용자의 자연어 질문에 사용자 지정 기계 학습 인텔리전스를 적용하여 가장 적합한 답변을 결정하는 질문 및 답변 KB(기술 자료) 서비스입니다.

QnA Maker를 사용하면 FAQ(자주 묻는 질문) 문서, URL, 제품 설명서 및 사용자 지정 질문 및 답변과 같은 반구조화된 콘텐츠에서 클라우드 기반 서비스를 강화할 수 있습니다. 사용하기 쉬운 [웹 포털](https://qnamaker.ai)을 사용하면 개발자 경험 없이 서비스에 대한 만들기, 관리, 학습 및 게시 작업을 수행할 수 있습니다. 서비스가 엔드포인트에 게시되면 챗봇과 같은 클라이언트 응용 프로그램에서 사용자와의 대화를 관리하여 질문을 받고 이에 답변할 수 있습니다. 

![개요](../media/qnamaker-overview-learnabout/overview.png)

## <a name="key-qna-maker-processes"></a>키 QnA Maker 프로세스

QnA Maker에서 데이터에 대해 제공하는 두 가지 핵심 서비스는 다음과 같습니다.

* **추출**: 구조적 질문-답변 데이터는 FAQ 및 제품 설명서와 같은 반구조적 [데이터 원본](../Concepts/data-sources-supported.md)에서 추출됩니다. 이 추출은 편집 프로세스의 일환으로 KB [만들기](https://aka.ms/qnamaker-docs-createkb) 등의 일부로 수행할 수 있습니다.

* **일치**: 기술 자료가 [학습되고 테스트](https://aka.ms/qnamaker-docs-trainkb)되면 [게시](https://aka.ms/qnamaker-docs-publishkb)합니다. 이렇게 하면 QnA Maker 기술 자료에 엔드포인트를 사용할 수 있으며, 이에 따라 봇 또는 클라이언트앱에서 이를 사용할 수 있습니다. 이 엔드포인트는 사용자 질문을 허용하고, 일치에 대한 신뢰도 점수와 함께 가장 적합한 기술 자료의 답변으로 응답합니다.

```JSON
{
    "answers": [
        {
            "questions": [
                "How do I share a knowledge base with other?"
            ],
            "answer": "Sharing works at the level of a QnA Maker service, i.e. all knowledge bases in the services will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base)how to collaborate on a knowledge base.",
            "score": 70.95,
            "id": 4,
            "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
            "metadata": []
        }
    ]
}

```

## <a name="qna-maker-architecture"></a>QnA Maker 아키텍처

QnA Maker를 구성하는 API 서비스는 다음과 같습니다.

1. **QnA Maker 관리 서비스**: 초기 만들기, 업데이트, 학습 및 게시가 포함된 QnA Maker 기술 자료에 대한 관리 환경입니다. 이러한 작업은 [포털](https://qnamaker.ai) 또는 [관리 API](https://aka.ms/qnamaker-v4-apis)를 통해 수행할 수 있습니다. 

2. **QnA Maker 예측 서비스**: 지정된 지역의 Azure 구독에 배포됩니다. 고객 KB 콘텐츠는 [Azure Search](https://azure.microsoft.com/services/search/)에 저장되고, 엔드포인트는 [앱 서비스](https://azure.microsoft.com/services/app-service/)로 배포됩니다. 또한 분석을 위해 [Application Insights](https://azure.microsoft.com/services/application-insights/) 리소스를 배포하도록 선택할 수도 있습니다.

![아키텍처](../media/qnamaker-overview-learnabout/architecture.png)


## <a name="service-highlights"></a>서비스의 주요 사항

- [FAQ 봇을 만드는](https://aka.ms/qnamaker-docs-create-faqbot) 완벽한 **무코드** 환경입니다.
- **예측에 대한 네트워크 제한이 없습니다**. 트랜잭션 수가 아니라 서비스 호스팅에 대한 비용을 지불합니다. 자세한 내용은 [가격 페이지](https://aka.ms/qnamaker-docs-pricing)를 참조하세요.
- **필요에 따라 크기를 조정합니다**. 시나리오에 맞게 개별 구성 요소에 대해 적절한 SKU를 선택합니다. QnA Maker 서비스에 대한 [용량을 선택하는 방법](https://aka.ms/qnamaker-docs-capacity)을 참조하세요.
- **전체 데이터 준수**가 적용됩니다. 예측 서비스 구성 요소는 Azure 구독 및 준수 경계 내에 배포됩니다.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [QnA Maker 서비스 만들기](../how-to/set-up-qnamaker-service-azure.md)
