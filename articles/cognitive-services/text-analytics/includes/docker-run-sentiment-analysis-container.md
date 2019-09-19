---
title: Docker run 명령의 컨테이너 실행 예
titleSuffix: Azure Cognitive Services
description: 감정 분석 컨테이너에 대 한 Docker run 명령
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: f0e587fc39fa2cc6f5275ae16834372a206b37d2
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966686"
---
*감정 분석* 컨테이너를 실행 하려면 다음 `docker run` 명령을 실행 합니다.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/sentiment \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 *감정 분석* 컨테이너를 실행 합니다.
* 1개 CPU 코어 및 4GB 메모리 할당
* 5000 TCP 포트 표시 및 컨테이너에 의사-TTY 할당
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.