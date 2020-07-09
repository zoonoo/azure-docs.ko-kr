---
title: Docker run 명령의 컨테이너 실행 예
titleSuffix: Azure Cognitive Services
description: 감정 분석 컨테이너에 대 한 Docker run 명령
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: 459ab7a254994929174e4f81ad25f0729f43fa0b
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108973"
---
*감정 분석 v3* 컨테이너를 실행 하려면 다음 명령을 실행 합니다 `docker run` . 아래 자리 표시자를 사용자 고유의 값으로 바꿉니다.

| 자리 표시자 | 값 | 형식 또는 예 |
|-------------|-------|---|
| **{API_KEY}** | Text Analytics 리소스의 키입니다. 리소스의 **키 및 끝점** 페이지의 Azure Portal에서 찾을 수 있습니다. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | 텍스트 분석 API에 액세스 하기 위한 끝점입니다. 리소스의 **키 및 끝점** 페이지의 Azure Portal에서 찾을 수 있습니다. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/sentiment \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

이 명령은 다음을 수행합니다.

* 컨테이너 이미지에서 *감정 분석* 컨테이너를 실행 합니다.
* 1개 CPU 코어 및 8GB 메모리 할당
* 5000 TCP 포트 표시 및 컨테이너에 의사-TTY 할당
* 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.