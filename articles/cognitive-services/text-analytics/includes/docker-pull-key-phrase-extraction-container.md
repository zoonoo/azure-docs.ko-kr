---
title: 핵심 구 추출 컨테이너에 대 한 Docker 풀
titleSuffix: Azure Cognitive Services
description: 핵심 구 추출 컨테이너에 대 한 Docker pull 명령
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 02dde8a27b9687e58bf1a09c1a951f306937f0d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906097"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>핵심 구 추출 컨테이너에 대 한 Docker 풀

명령을 사용 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 하 여 Microsoft Container Registry에서 컨테이너 이미지를 다운로드 합니다.

Text Analytics 컨테이너의 사용 가능한 태그에 대 한 전체 설명은 Docker 허브의 [핵심 구 추출](https://go.microsoft.com/fwlink/?linkid=2018757) 컨테이너를 참조 하세요.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase:latest
```
