---
title: 상태 컨테이너에 대 한 Docker 풀
titleSuffix: Azure Cognitive Services
description: 상태 컨테이너의 Text Analytics에 대 한 Docker pull 명령
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 4c33952ed56399b94a7db1088cb60919a4f88137
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108988"
---
컨테이너에 대 한 액세스를 요청 하는 [Cognitive Services 컨테이너 요청 양식을](https://aka.ms/cognitivegate) 작성 하 고 제출 합니다.

[!INCLUDE [Request access to the container registry](../../../../includes/cognitive-services-containers-request-access-only.md)]

온 보 딩 전자 메일에 제공 된 자격 증명과 함께 docker login 명령을 사용 하 여 Cognitive Services 컨테이너를 위한 개인 컨테이너 레지스트리에 연결 합니다.

```bash
docker login containerpreview.azurecr.io -u <username> -p <password>
```

명령을 사용 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 하 여 개인 컨테이너 레지스트리에서이 컨테이너 이미지를 다운로드 합니다.

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
