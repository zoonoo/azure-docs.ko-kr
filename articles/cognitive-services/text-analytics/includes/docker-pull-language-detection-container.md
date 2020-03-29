---
title: 언어 감지 컨테이너에 대한 도커 풀
titleSuffix: Azure Cognitive Services
description: 언어 검색 컨테이너에 대한 도커 끌어오기 명령
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: 23669a5440799ff071b3a30e3e41ab5d49f212df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966760"
---
#### <a name="docker-pull-for-the-language-detection-container"></a>언어 감지 컨테이너에 대한 도커 풀

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 Microsoft 컨테이너 레지스트리에서 컨테이너 이미지를 다운로드합니다.

텍스트 분석 컨테이너에 사용할 수 있는 태그에 대한 자세한 설명은 Docker Hub의 [언어 검색](https://go.microsoft.com/fwlink/?linkid=2018759) 컨테이너를 참조하십시오.

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
