---
title: Azure Front 도어-백 엔드 상태 모니터링 | Microsoft Docs
description: 이 문서는 Azure Front 도어가 백 엔드의 상태를 모니터링 하는 방법을 이해 하는 데 도움이 됩니다.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 4cbeea8ad20d41daff3d4ad086a36df5e988991f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449248"
---
# <a name="health-probes"></a>상태 프로브

지정 된 Front 도어 환경에 대 한 각 백 엔드의 상태를 확인 하기 위해 각 프런트 도어 환경은 구성 된 각 백 엔드에 대 한 가상의 HTTP/HTTPS 요청을 주기적으로 보냅니다. 그러면 Front 도어가 프로브의 이러한 응답을 사용 하 여 클라이언트 요청을 라우팅하는 "최상의" 백 엔드 리소스를 결정 합니다. 

> [!WARNING]
> 프런트 도어는 전역적으로 많은 edge 환경을 포함 하므로, 백 엔드에 대 한 상태 프로브 볼륨은 구성 된 상태 프로브 주기에 따라 분당 25 개 요청에서 분당 1200 요청까지 매우 높을 수 있습니다. 기본 프로브 빈도로 30 초를 사용 하 여 백 엔드의 프로브 볼륨은 분당 약 200 요청이 되어야 합니다.

## <a name="supported-protocols"></a>지원되는 프로토콜

Front Door는 HTTP 또는 HTTPS 프로토콜을 통한 프로브 전송을 지원합니다. 이러한 프로브는 라우팅 클라이언트 요청에 대해 구성된 동일한 TCP 포트를 통해 전송되며, 재정의할 수 없습니다.

## <a name="supported-http-methods-for-health-probes"></a>상태 프로브에 대해 지원 되는 HTTP 메서드

전면 도어는 상태 프로브를 전송 하는 다음과 같은 HTTP 메서드를 지원 합니다.

1. **가져오기:** GET 메서드는 요청 URI로 식별 되는 모든 정보 (엔터티 형식)를 검색 함을 의미 합니다.
2. **헤드:** 서버는 응답에서 메시지 본문을 반환 해서는 안 된다는 점을 제외 하 고 HEAD 메서드는 GET과 동일 합니다. 새 Front 도어 프로필의 경우 기본적으로 프로브 방법이 HEAD로 설정 됩니다.

> [!NOTE]
> 백 엔드에 대 한 부하와 비용을 낮추기 위해 전방 도어는 상태 프로브에 HEAD 요청을 사용 하도록 권장 합니다.

## <a name="health-probe-responses"></a>상태 프로브 응답

| 응답  | 설명 | 
| ------------- | ------------- |
| 상태 확인  |  200 정상 상태 코드는 백 엔드가 정상임을 나타냅니다. 그 외 코드는 모두 실패로 간주됩니다. 네트워크 오류를 비롯 한 모든 이유로 인해 프로브에 대해 유효한 HTTP 응답이 수신 되지 않으면 프로브는 실패로 간주 됩니다.|
| 대기 시간 측정  | 대기 시간은 응답의 마지막 바이트를 수신하는 순간 프로브 요청을 보내기 직전에 측정된 벽시계 시간입니다. 각 요청에 대해 새 TCP 연결을 사용 하므로이 측정은 기존 웜 연결을 사용 하 여 백 엔드에 대해 편향 되지 않습니다.  |

## <a name="how-front-door-determines-backend-health"></a>Front Door가 백 엔드 상태를 결정하는 방법

Azure 전면 도어는 모든 알고리즘에서 아래와 동일한 3 단계 프로세스를 사용 하 여 상태를 확인 합니다.

1. 비활성화된 백 엔드를 제외합니다.

2. 상태 프로브 오류가 있는 백 엔드를 제외합니다.
    * 이 선택은 마지막 _n_ 상태 프로브 응답을 확인하여 완료합니다. 최소한 _x_가 정상적인 경우 백 엔드는 정상 상태로 간주됩니다.

    * _n_ 은 부하 분산 설정에서 samplesize 속성을 변경 하 여 구성 됩니다.

    * _x_ 는 부하 분산 설정에서 SuccessfulSamplesRequired 속성을 변경 하 여 구성 됩니다.

3. 백 엔드 풀의 정상 백 엔드 집합의 경우, 프런트 도어는 각 백 엔드에 대 한 대기 시간 (왕복 시간)을 추가로 측정 하 고 유지 관리 합니다.


## <a name="complete-health-probe-failure"></a>상태 프로브 실패 완료

상태 프로브가 백 엔드 풀의 모든 백 엔드에 대해 실패한 경우, Front Door는 모든 백 엔드를 정상으로 간주하고 모든 백 엔드에 걸친 라운드 로빈 배포에 트래픽을 라우팅합니다.

백 엔드가 정상 상태로 돌아가면 전방 도어는 정상적인 부하 분산 알고리즘을 다시 시작 합니다.

## <a name="disabling-health-probes"></a>상태 프로브 사용 안 함

백 엔드 풀에 하나의 백 엔드가 있는 경우 상태 프로브를 사용 하지 않도록 설정 하 여 응용 프로그램 백 엔드에 대 한 부하를 줄일 수 있습니다. 백 엔드 풀에 여러 백 엔드가 있지만 그 중 하나만 활성화 됨 상태 이면 상태 프로브를 사용 하지 않도록 설정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Front Door를 만드는](quickstart-create-front-door.md) 방법을 알아봅니다.
- [Front Door의 작동 원리](front-door-routing-architecture.md)를 알아봅니다.
