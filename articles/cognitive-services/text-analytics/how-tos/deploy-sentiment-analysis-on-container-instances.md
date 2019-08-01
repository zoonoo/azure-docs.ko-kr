---
title: Azure Container Instances 실행-Text Analytics
titleSuffix: Azure Cognitive Services
description: 감정 분석 이미지를 사용 하 여 텍스트 분석 컨테이너를 Azure Container Instance에 배포 하 고 웹 브라우저에서 테스트 합니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 9ef529c9d505e5b305602c80a8dbef906f52269c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552525"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-container-instances"></a>Azure Container Instances에 감정 분석 컨테이너 배포

감정 분석 이미지를 사용 하 여 Cognitive Services [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) 컨테이너를 Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/)에 배포 하는 방법을 알아봅니다. 이 절차에서는 Text Analytics 리소스 생성, 연결 된 감정 분석 이미지 만들기, 브라우저에서 두 작업의이 오케스트레이션을 실행 하는 기능을 예시 합니다. 컨테이너를 사용 하면 개발자가 인프라를 관리 하지 않고 응용 프로그램 개발에 집중 하는 것으로 전환할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독을 사용합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>다음 단계 

* 추가로 [Cognitive Services 컨테이너](../../cognitive-services-container-support.md)를 사용합니다.
* [Text Analytics 연결 된 서비스](../vs-text-connected-service.md) 사용
