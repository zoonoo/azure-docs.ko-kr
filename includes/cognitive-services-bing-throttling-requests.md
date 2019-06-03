---
author: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/09/2018
ms.author: nitinme
ms.openlocfilehash: 1d8340054ace25a0cdc36eef9c3d5b4238a6f99b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66124667"
---
서비스 및 구독 유형에 따라 만들 수 있는 QPS(초당 쿼리 수)가 결정됩니다. 할당량 내에서 유지하는 논리가 애플리케이션에 포함되어 있는지 확인합니다. QPS 제한에 도달하거나 초과하면 요청이 실패하고 HTTP 429 상태 코드가 반환됩니다. 응답에는 `Retry-After` 헤더가 포함됩니다. 이 헤더는 다른 요청을 보내기 전에 기다려야 하는 시간을 나타냅니다.

## <a name="denial-of-service-versus-throttling"></a>DoS(서비스 거부) 및 제한

서비스는 DoS(서비스 거부) 공격과 QPS 위반을 구분합니다. 서비스에서 DoS 공격을 의심하면 요청이 성공합니다(HTTP 상태 코드는 200 정상). 하지만 응답 본문은 비어 있습니다.