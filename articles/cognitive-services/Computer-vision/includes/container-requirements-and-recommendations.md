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
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73481769"
---
> [!NOTE]
> 요구 사항 및 권장 사항은 초당 단일 요청을 포함 하는 벤치 마크를 기준으로 하며, 8 MB의 스캔 된 비즈니스 문자 이미지를 사용 하 여 29 개 줄과 총 803 문자를 포함 합니다.

다음 표에서는 각 읽기 컨테이너의 최소 및 권장 리소스 할당에 대해 설명 합니다.

| 컨테이너 | 최소 | 권장 |TP<br>(최소, 최대)|
|-----------|---------|-------------|--|
| 읽기 | 1 코어, 8gb 메모리, 0.24 TPS | 8 코어, 16gb 메모리, 1.17 TPS | 0.24, 1.17 |

* 각 코어는 속도가 2.6GHz 이상이어야 합니다.
* TPS-초당 트랜잭션 수입니다.

`--cpus` 명령의 일부로 사용되는 `--memory` 및 `docker run` 설정에 해당하는 코어 및 메모리.
