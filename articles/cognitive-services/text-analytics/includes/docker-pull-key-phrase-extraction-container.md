---
title: 핵심 구 추출 컨테이너에 대 한 Docker 풀
titleSuffix: Azure Cognitive Services
description: 핵심 구 추출 컨테이너에 대 한 Docker pull 명령
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 59313528e4beb343f9ac1103d3f9f2faae5dfc16
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966674"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>핵심 구 추출 컨테이너에 대 한 Docker 풀

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 Microsoft 컨테이너 레지스트리에서 컨테이너 이미지를 다운로드합니다.

Text Analytics 컨테이너의 사용 가능한 태그에 대 한 전체 설명은 Docker 허브의 [핵심 구 추출](https://go.microsoft.com/fwlink/?linkid=2018757) 컨테이너를 참조 하세요.

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
