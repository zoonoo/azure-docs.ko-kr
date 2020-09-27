---
title: Azure 피어 링 서비스 연결
description: Microsoft Azure 피어 링 서비스 연결에 대 한 자세한 정보
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 0540fa9c7a29456aaf343adce83509b2d0e390e4
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2020
ms.locfileid: "91398923"
---
# <a name="peering-service-connection"></a>피어 링 서비스 연결

연결은 일반적으로 피어 링 서비스를 식별 하는 논리적 정보 집합을 나타냅니다. 다음 특성을 지정 하 여 정의 됩니다.

- 논리적 이름
- 연결 파트너
- 고객의 실제 위치
- IP 접두사

고객은 요구 사항에 따라 단일 연결 또는 다중 연결을 설정할 수 있습니다. 연결은 원격 분석 컬렉션의 단위로도 사용 됩니다. 예를 들어 원격 분석 경고를 옵트인 하기 위해 고객은 모니터링 되는 연결을 정의 해야 합니다.

> [!Note]
> 피어 링 서비스에 등록 하면 선택 된 접두사에 대 한 대기 시간 측정을 제공 하기 위해 Windows 및 Microsoft 365 원격 분석을 분석 합니다.
>연결 원격 분석에 대 한 자세한 내용은 [피어 링 서비스 연결 원격 분석](connection-telemetry.md)을 참조 하세요.
>

>## <a name="how-to-register-a-connection"></a>연결을 등록 하는 방법

**시나리오** -아래 그림에 표시 된 것 처럼 지점이 여러 지리적 위치에 분산 되어 있다고 가정해 보겠습니다. 여기서 고객은 단일 연결과 관련 된 논리적 이름, SP (서비스 공급자) 이름, 고객의 실제 위치 및 IP 접두사 (고객 소유 또는 서비스 공급자가 할당)를 제공 해야 합니다. 별도의 지역 중복 연결에 대 한 피어 링 서비스를 등록 하려면이 프로세스를 반복 해야 합니다.

![지역 중복 연결](./media/peering-service-connection/peering-service-connections.png)

> [!Note]
> 상태 수준-필터링은 미국에서 연결이 지리적 위치에 있는 경우 고객의 물리적 위치에 대해 고려 됩니다.
>

## <a name="next-steps"></a>다음 단계

피어 링 서비스 연결을 등록 하는 방법에 대 한 단계별 프로세스를 알아보려면 [Azure Portal를 사용 하 여 피어 링 서비스 등록](azure-portal.md)을 참조 하세요.

Peering Service 연결 원격 분석에 대해 알아보려면 [Peering Service 연결 원격 분석](connection-telemetry.md)을 참조하세요.

원격 분석을 측정하려면 [연결 원격 분석 측정](measure-connection-telemetry.md)을 참조하세요.
