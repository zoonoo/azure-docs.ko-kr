---
title: 언어 감지 컨테이너 docker 예
titleSuffix: Azure Cognitive Services
description: 언어 감지 컨테이너 docker 예
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/19/2019
ms.author: dapine
ms.openlocfilehash: 6e09a19ceb7b892a8ec405b348700b65c9d55ec4
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71148452"
---
### <a name="language-detection-container-docker-examples"></a>언어 감지 컨테이너 docker 예

다음 docker 예는 언어 감지 컨테이너를 위한 것입니다.

#### <a name="basic-example"></a>기본 예제 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/language \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} 
  ```

#### <a name="logging-example"></a>로깅 예 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/language \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
  ```
