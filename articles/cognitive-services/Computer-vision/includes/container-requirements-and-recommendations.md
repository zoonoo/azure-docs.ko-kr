---
title: 컨테이너 요구 사항 및 추천
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: c62044582cb488a5ef2d20b3f407c0865b3994ba
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877980"
---
> [!NOTE]
> 요구 사항 및 권장 사항은 29줄과 총 803자로 구성된 스캔된 비즈니스 편지의 8MB 이미지를 사용하여 초당 단일 요청이 있는 벤치마크를 기반으로 합니다.

다음 표는 각 Read 컨테이너에 대한 최소 및 권장 리소스 할당에 대해 설명합니다.

| 컨테이너 | 최소 | 권장 |TPS<br>(최소, 최대)|
|-----------|---------|-------------|--|
| 읽기 | 코어 1개, 8GB 메모리, 0.24 TPS | 8코어, 16GB 메모리, 1.17 TPS | 0.24, 1.17 |

* 각 코어는 속도가 2.6GHz 이상이어야 합니다.
* TPS - 초당 트랜잭션.

`docker run` 명령의 일부로 사용되는 `--cpus` 및 `--memory` 설정에 해당하는 코어 및 메모리.
