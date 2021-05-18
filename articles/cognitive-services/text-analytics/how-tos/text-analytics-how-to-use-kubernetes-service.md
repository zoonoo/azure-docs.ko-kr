---
title: Azure Kubernetes Service 실행 - Text Analytics
titleSuffix: Azure Cognitive Services
description: Text Analytics 컨테이너 이미지를 Azure Kubernetes Service에 배포하고 웹 브라우저에서 테스트합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: fb33bd4fe70c51e1e2afffece10ba3b29dcb0450
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98932434"
---
# <a name="deploy-a-text-analytics-container-to-azure-kubernetes-service"></a>Azure Kubernetes Service에 Text Analytics 컨테이너 배포

Azure Cognitive Services [Text Analytics](./text-analytics-how-to-install-containers.md) 컨테이너 이미지를 AKS(Azure Kubernetes Service)에 배포하는 방법을 알아봅니다. 이 절차에서는 Text Analytics 리소스를 만드는 방법 및 연관된 감정 분석 이미지를 만드는 방법, 그리고 브라우저에서 이 두 방법의 오케스트레이션을 실행하는 방법을 보여 줍니다. 컨테이너를 사용하면 인프라 관리에 대한 주의를 돌려 애플리케이션 개발에 집중할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 절차를 수행하려면 로컬로 설치 및 실행해야 하는 몇 가지 도구가 필요합니다. Azure Cloud Shell은 사용하지 마세요. 다음이 필요합니다.

* Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services)을 만듭니다.
* 텍스트 편집기(예: [Visual Studio Code](https://code.visualstudio.com/download)).
* [Azure CLI](/cli/azure/install-azure-cli)가 설치되어 있음.
* [Kubernetes CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/)가 설치되어 있음.
* 올바른 가격 책정 계층이 지정된 Azure 리소스. 모든 가격 책정 계층이 이 컨테이너에 작동하는 것은 아닙니다.
    * F0 또는 표준 가격 책정 계층만 있는 **Azure Text Analytics** 리소스
    * S0 가격 책정 계층이 있는 **Azure Cognitive Services** 리소스.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

#### <a name="key-phrase-extraction"></a>[핵심 구 추출](#tab/keyphrase)

[!INCLUDE [Key Phrase Extraction Kubernetes config and deploy steps](../includes/key-phrase-extraction-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[언어 감지](#tab/language)

[!INCLUDE [Language Detection Kubernetes config and deploy steps](../includes/language-detection-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[감정 분석](#tab/sentiment)

[!INCLUDE [Sentiment Analysis Kubernetes config and deploy steps](../includes/sentiment-analysis-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>다음 단계

* 추가적인 [Cognitive Services 컨테이너](../../cognitive-services-container-support.md) 사용
* [Text Analytics 연결된 서비스](../index.yml) 사용
