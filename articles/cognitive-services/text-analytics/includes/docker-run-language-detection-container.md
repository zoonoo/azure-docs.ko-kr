---
title: docker run 명령의 컨테이너 실행 예
titleSuffix: Azure Cognitive Services
description: 언어 감지 컨테이너를 위한 docker run 명령
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: 1ac035e669ae6d5fcec8dfa8faee12d34039004b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91779266"
---
언어 감지 컨테이너를 실행하려면 다음 `docker run` 명령을 실행합니다. 아래 자리 표시자를 자체 값으로 바꿉니다.

| 자리 표시자 | 값 | 형식 또는 예 |
|-------------|-------|---|
| **{API_KEY}** | Text Analytics 리소스의 키입니다. Azure Portal의 리소스 **키 및 엔드포인트** 페이지에서 이 값을 찾을 수 있습니다. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Text Analytics API에 액세스하기 위한 엔드포인트입니다. Azure Portal의 리소스 **키 및 엔드포인트** 페이지에서 이 값을 찾을 수 있습니다. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |


```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/textanalytics/language \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 언어 감지 컨테이너 실행
* 1개 CPU 코어 및 4GB 메모리 할당
* 5000 TCP 포트 표시 및 컨테이너에 의사-TTY 할당
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.