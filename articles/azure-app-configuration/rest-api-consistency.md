---
title: Azure App Configuration REST API - 일관성
description: Azure App Configuration REST API를 사용하여 실시간 일관성을 보장하기 위한 참조 페이지
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: b324d23ce9abc1eb3893f316365aff828de2063d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932628"
---
# <a name="real-time-consistency"></a>실시간 일관성

일부 분산 시스템의 특성으로 인해 요청 간 실시간 일관성은 암시적으로 적용하기 어렵습니다. 해결 방법은 여러 동기화 토큰 형식으로 프로토콜 지원을 허용하는 것입니다. 동기화 토큰은 선택 사항입니다.

## <a name="initial-request"></a>초기 요청

서로 다른 클라이언트 인스턴스와 요청 간에 실시간 일관성을 보장하려면 선택적 `Sync-Token` 요청 및 응답 헤더를 사용합니다.

구문

```http
Sync-Token: <id>=<value>;sn=<sn>
```

|매개 변수|Description|
|--|--|
| `<id>` | 토큰 ID(불투명) |
| `<value>` | 토큰 값(불투명). Base64 인코딩된 문자열을 허용합니다. |
| `<sn>` | 토큰 시퀀스 번호(버전). 번호가 높을수록 동일한 토큰의 최신 버전을 의미합니다. 동시성과 클라이언트 캐싱 향상을 위해 허용합니다. 토큰 버전은 마지막 번호를 포함하므로 클라이언트는 토큰의 마지막 버전만 사용하도록 선택할 수 있습니다. 이 매개 변수는 요청에 필요하지 않습니다. |

## <a name="response"></a>응답

서비스는 `Sync-Token` 헤더에 각 응답을 제공합니다.

```http
Sync-Token: jtqGc1I4=MDoyOA==;sn=28
```

## <a name="subsequent-requests"></a>후속 요청

모든 후속 요청은 제공된 `Sync-Token`과 관련하여 실시간 일관된 응답이 보장됩니다.

```http
Sync-Token: <id>=<value>
```

요청에서 `Sync-Token` 헤더를 생략하면 서비스는 내부적으로 해결하기 전에 짧은 기간(최대 몇 초) 동안 캐시된 데이터를 사용하여 응답할 수 있습니다. 읽기 직전에 변경 내용이 발생한 경우 이 동작으로 인해 일관되지 않은 읽기가 발생할 수 있습니다.

## <a name="multiple-sync-tokens"></a>여러 동기화-토큰

서버는 단일 요청에 대해 여러 동기화 토큰을 사용하여 응답할 수 있습니다. 다음 요청에 대해 실시간 일관성을 유지하기 위해 클라이언트는 수신된 모든 동기화 토큰을 사용하여 응답해야 합니다. 여러 헤더 값은 쉼표로 구분해야 합니다.

```http
Sync-Token: <token1-id>=<value>,<token2-id>=<value>
```
