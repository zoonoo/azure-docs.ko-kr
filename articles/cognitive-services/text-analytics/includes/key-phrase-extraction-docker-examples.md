---
title: 핵심 구 추출 컨테이너 docker 예
titleSuffix: Azure Cognitive Services
description: 핵심 구 추출 컨테이너 docker 예
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/21/2019
ms.author: dapine
ms.openlocfilehash: bc0375369db351038c7ac550cbe51415a0b3e069
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71148451"
---
### <a name="key-phrase-extraction-container-docker-examples"></a>핵심 구 추출 컨테이너 docker 예

다음 docker 예는 핵심 구 추출 컨테이너를 위한 것입니다.

#### <a name="basic-example"></a>기본 예제 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} 
  ```

#### <a name="logging-example"></a>로깅 예 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
  ```
