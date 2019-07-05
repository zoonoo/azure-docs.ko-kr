---
title: Azure Container Instances를 실행 합니다.
titleSuffix: Text Analytics - Azure Cognitive Services
description: Azure Container Instance에 감정 분석 이미지를 사용 하 여 텍스트 분석 컨테이너를 배포 하 고 웹 브라우저에서 테스트 합니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: a82b5a1cbed662289d3a406a61fdd19a3ca8861b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446450"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-container-instances-aci"></a>감정 분석 컨테이너를 Azure ACI (Container Instances) 배포

Cognitive Services를 배포 하는 방법을 알아봅니다 [텍스트 분석](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) 감정 분석 이미지를 Azure 사용 하 여 컨테이너 [Container Instances](https://docs.microsoft.com/azure/container-instances/) (ACI). 이 절차 Text Analytics 리소스 만들기, 두 브라우저에서이 오케스트레이션이 실행 하는 기능과 연결 된 감정 분석 이미지 만들기를 보여 줍니다. 컨테이너를 사용 하 여 응용 프로그램 개발에 집중 하는 대신에 인프라를 관리에서 개발자의 주의 이동할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독을 사용합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances (ACI)](../../containers/includes/create-aci-resource.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>다음 단계 

* 더 많은 [Cognitive Services 컨테이너](../../cognitive-services-container-support.md)를 사용합니다.
* 사용 된 [Text Analytics에 연결 된 서비스](../vs-text-connected-service.md)
