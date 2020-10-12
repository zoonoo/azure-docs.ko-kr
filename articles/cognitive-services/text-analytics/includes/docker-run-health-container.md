---
title: Docker run 명령의 컨테이너 실행 예
titleSuffix: Azure Cognitive Services
description: 상태 컨테이너에 대 한 Docker run 명령
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 72a877ab8cfda126016376017c0a7eb42b1186d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86108950"
---
컨테이너를 실행 하려면 먼저 해당 이미지 ID를 찾습니다.
 
```bash
docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
```

다음 명령을 실행 `docker run` 합니다. 아래 자리 표시자를 사용자 고유의 값으로 바꿉니다.

| 자리 표시자 | 값 | 형식 또는 예 |
|-------------|-------|---|
| **{API_KEY}** | Text Analytics 리소스의 키입니다. 리소스의 **키 및 끝점** 페이지의 Azure Portal에서 찾을 수 있습니다. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | 텍스트 분석 API에 액세스 하기 위한 끝점입니다. 리소스의 **키 및 끝점** 페이지의 Azure Portal에서 찾을 수 있습니다. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
| **{IMAGE_ID}** | 컨테이너의 이미지 ID입니다. | `1.1.011300001-amd64-preview` |
| **{INPUT_DIR}** | 컨테이너의 입력 디렉터리입니다. | Windows: `C:\healthcareMount` <br> Linux/MacOS: `/home/username/input` |

```bash
docker run --rm -it -p 5000:5000 --cpus 6 --memory 12g \
--mount type=bind,src={INPUT_DIR},target=/output {IMAGE_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Disk:Format=json
```

이 명령은 다음을 수행합니다.

- 호스트 컴퓨터에 입력 디렉터리가 존재 한다고 가정 합니다.
- 컨테이너 이미지에서 상태 컨테이너에 대 한 Text Analytics를 실행 합니다.
- 6 CPU 코어 및 12기가바이트 (GB)의 메모리를 할당 합니다.
- 5000 TCP 포트 표시 및 컨테이너에 의사-TTY 할당
- 종료 후 자동으로 컨테이너를 제거합니다. 컨테이너 이미지는 호스트 컴퓨터에서 계속 사용할 수 있습니다.
