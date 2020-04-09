---
title: Azure Kubernetes 서비스 실행 - 텍스트 분석
titleSuffix: Azure Cognitive Services
description: 텍스트 분석 컨테이너 이미지를 Azure Kubernetes 서비스에 배포하고 웹 브라우저에서 테스트합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 155f32ee76f69fe0f16e7698123381fdc12efd0e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877843"
---
# <a name="deploy-a-text-analytics-container-to-azure-kubernetes-service"></a>Azure Kubernetes 서비스에 텍스트 분석 컨테이너 배포

Azure 코그너티브 서비스 [텍스트 분석](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) 컨테이너 이미지를 AZURE Kubernetes 서비스(AKS)에 배포하는 방법을 알아봅니다. 이 절차에서는 텍스트 분석 리소스를 만드는 방법, 연결된 감정 분석 이미지를 만드는 방법 및 브라우저에서 이 오케스트레이션을 연습하는 방법을 보여 주었습니다. 컨테이너를 사용하면 인프라 관리에서 응용 프로그램 개발에 집중하는 대신 주의를 끌 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 절차를 수행하려면 로컬로 설치 및 실행해야 하는 몇 가지 도구가 필요합니다. Azure 클라우드 셸을 사용하지 마십시오. 다음이 필요합니다.

* Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* 텍스트 편집기(예: [Visual Studio Code)](https://code.visualstudio.com/download)
* [Azure CLI가](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 설치되었습니다.
* [Kubernetes CLI가](https://kubernetes.io/docs/tasks/tools/install-kubectl/) 설치되었습니다.
* 올바른 가격 책정 계층이 지정된 Azure 리소스. 모든 가격 책정 계층이 이 컨테이너에 작동하는 것은 아닙니다.
    * F0 또는 표준 가격 책정 계층만 있는 **Azure 텍스트 분석** 리소스입니다.
    * S0 가격 책정 계층이 있는 **Azure 코그너티브 서비스** 리소스입니다.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

#### <a name="key-phrase-extraction"></a>[핵심 문구 추출](#tab/keyphrase)

[!INCLUDE [Key Phrase Extraction Kubernetes config and deploy steps](../includes/key-phrase-extraction-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[언어 검색](#tab/language)

[!INCLUDE [Language Detection Kubernetes config and deploy steps](../includes/language-detection-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[감정 분석](#tab/sentiment)

[!INCLUDE [Sentiment Analysis Kubernetes config and deploy steps](../includes/sentiment-analysis-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>다음 단계

* 더 많은 [코그너티브 서비스 컨테이너](../../cognitive-services-container-support.md) 사용
* 텍스트 [분석 커넥티드 서비스](../vs-text-connected-service.md) 사용
