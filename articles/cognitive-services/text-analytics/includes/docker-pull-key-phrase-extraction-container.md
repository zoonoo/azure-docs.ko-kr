---
title: 핵심 구 추출 컨테이너에 대 한 Docker 풀
titleSuffix: Azure Cognitive Services
description: 핵심 구 추출 컨테이너에 대 한 Docker pull 명령
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: e71db5455a3d6726cd6567d568a13fcdcf9cb520
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051380"
---
## <a name="pull-the-key-phrase-extraction-container"></a>핵심 구 추출 컨테이너를 끌어옵니다.

Text Analytics에 대 한 컨테이너 이미지는 Microsoft Container Registry에서 사용할 수 있습니다.

| 컨테이너 | Container Registry/리포지토리/이미지 이름 |
|-----------|------------|
| 핵심 구 추출 | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 Microsoft 컨테이너 레지스트리에서 컨테이너 이미지를 다운로드합니다.

Text Analytics 컨테이너의 사용 가능한 태그에 대 한 전체 설명은 Docker 허브의 [핵심 구 추출](https://go.microsoft.com/fwlink/?linkid=2018757) 컨테이너를 참조 하세요.

### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>핵심 구 추출 컨테이너에 대 한 Docker 풀

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
