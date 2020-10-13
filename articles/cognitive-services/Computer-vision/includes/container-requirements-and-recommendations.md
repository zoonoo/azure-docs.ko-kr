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
ms.openlocfilehash: 3ab5462cc2b368bd1518606b03c8ec1027bc31c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397194"
---
> [!NOTE]
> 요구 사항 및 권장 사항은 초당 단일 요청을 포함하는 벤치마크를 기준으로 하며, 8MB의 스캔된 비즈니스 문자 이미지를 사용하여 29개 줄과 총 803 문자를 포함합니다.

다음 표에서는 각 Read 컨테이너의 최소 및 권장 리소스 할당에 대해 설명합니다.

| 컨테이너 | 최소 | 권장 |
|-----------|---------|-------------|
| 2.0 읽기-미리 보기 | 1 코어, 8gb 메모리 |  8 코어, 16-GB 메모리 |
| Read 3.0 미리 보기 | 8 코어, 16-GB 메모리 | 8 코어, 24-GB 메모리 |
| Read 3.1 미리 보기 | 8 코어, 16-GB 메모리 | 8 코어, 24-GB 메모리 |

* 각 코어는 속도가 2.6GHz 이상이어야 합니다.

`docker run` 명령의 일부로 사용되는 `--cpus` 및 `--memory` 설정에 해당하는 코어 및 메모리.
