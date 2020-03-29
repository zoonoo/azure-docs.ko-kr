---
title: 컨테이너 요구 사항 및 추천
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/20/2019
ms.author: dapine
ms.openlocfilehash: 2fa97a3a18741f506e10e5e4d61da286db3f744c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73481769"
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
