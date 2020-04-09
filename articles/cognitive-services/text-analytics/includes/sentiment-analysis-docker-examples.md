---
title: 감정 분석 컨테이너 도커 예제
titleSuffix: Azure Cognitive Services
description: 감정 분석 컨테이너 도커 예제
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e1d80f2ee869b2d71a536e499f900c7e8d6e425d
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878680"
---
### <a name="sentiment-analysis-container-docker-examples"></a>감정 분석 컨테이너 도커 예제

다음 도커 예제는 감정 분석 컨테이너에 대한 것입니다.

#### <a name="basic-example"></a>기본 예제 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/sentiment \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} 
  ```

#### <a name="logging-example"></a>로깅 예제 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/sentiment \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
  ```
