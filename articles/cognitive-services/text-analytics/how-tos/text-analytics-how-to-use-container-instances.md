---
title: Azure 컨테이너 인스턴스 실행 - 텍스트 분석
titleSuffix: Azure Cognitive Services
description: 텍스트 분석 컨테이너를 Azure 컨테이너 인스턴스에 배포하고 웹 브라우저에서 테스트합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e4b61c6fe2f62745d0f5268221cbb5c84803eb10
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876443"
---
# <a name="deploy-a-text-analytics-container-to-azure-container-instances"></a>Azure 컨테이너 인스턴스에 텍스트 분석 컨테이너 배포

Azure [컨테이너 인스턴스에][container-instances]코그너티브 서비스 [텍스트 분석][install-and-run-containers] 컨테이너를 배포하는 방법에 대해 알아봅니다. 이 절차는 텍스트 분석 리소스 생성, 관련 감정 분석 이미지 생성 및 브라우저에서 이 두 오케스트레이션을 연습하는 기능을 예로 들어 있습니다. 컨테이너를 사용하면 개발자의 관심을 인프라 관리에서 응용 프로그램 개발에 집중하는 것으로 전환할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독을 사용합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

#### <a name="key-phrase-extraction"></a>[핵심 문구 추출](#tab/keyphrase)

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[언어 검색](#tab/language)

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[감정 분석](#tab/sentiment)

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>다음 단계 

* 추가적인 [Cognitive Services 컨테이너](../../cognitive-services-container-support.md) 사용
* 텍스트 [분석 커넥티드 서비스](../vs-text-connected-service.md) 사용

[install-and-run-containers]: ./text-analytics-how-to-install-containers.md
[container-instances]: https://docs.microsoft.com/azure/container-instances