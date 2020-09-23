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
ms.openlocfilehash: bffdb7b33fc7da38b6985edc3948b848f417a497
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90982078"
---
> [!NOTE]
> 요구 사항 및 권장 사항은 초당 단일 요청을 포함 하는 벤치 마크를 기준으로 하며, 8 MB의 스캔 된 비즈니스 문자 이미지를 사용 하 여 29 개 줄과 총 803 문자를 포함 합니다.

다음 표에서는 각 읽기 컨테이너의 최소 및 권장 리소스 할당에 대해 설명 합니다.

| 컨테이너 | 최소 | 권장 |
|-----------|---------|-------------|
| 3.0 읽기-미리 보기 | 8 코어, 16gb 메모리 | 8 코어, 24gb 메모리 |
| 3.1 읽기-미리 보기 | 8 코어, 16gb 메모리 | 8 코어, 24gb 메모리 |

* 각 코어는 속도가 2.6GHz 이상이어야 합니다.

`docker run` 명령의 일부로 사용되는 `--cpus` 및 `--memory` 설정에 해당하는 코어 및 메모리.
