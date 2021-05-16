---
title: 컨테이너 요구 사항 및 추천
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/23/2020
ms.author: aahi
ms.openlocfilehash: 2a399b683dc9596d3514ce7d3be1fa2444449e2a
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284946"
---
> [!NOTE]
> 요구 사항 및 권장 사항은 초당 단일 요청을 포함하는 벤치마크를 기준으로 하며, 8MB의 스캔된 비즈니스 문자 이미지를 사용하여 29개 줄과 총 803 문자를 포함합니다.

다음 표에서는 각 Read OCR 컨테이너에 대한 최소 및 권장 리소스 할당을 설명합니다.

| 컨테이너 | 최소 | 권장 |
|-----------|---------|-------------|
| Read 2.0-preview | 1개 코어, 8GB 메모리 |    8 코어, 16-GB 메모리 |
| Read 3.2 미리 보기 | 8 코어, 16-GB 메모리 | 8 코어, 24-GB 메모리 |

* 각 코어는 속도가 2.6GHz 이상이어야 합니다.

`docker run` 명령의 일부로 사용되는 `--cpus` 및 `--memory` 설정에 해당하는 코어 및 메모리.
