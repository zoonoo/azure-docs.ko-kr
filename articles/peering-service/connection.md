---
title: Azure Peering Service 연결
description: Microsoft Azure Peering Service 연결에 대한 자세한 정보
services: peering-service
author: gthareja
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 04/07/2021
ms.author: gatharej
ms.openlocfilehash: 9463a0967b9cfc25f189dd3efac80e26428866fa
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108202662"
---
# <a name="peering-service-connection"></a>Peering Service 연결

연결은 일반적으로 Peering Service를 식별하는 논리적 정보 집합을 나타냅니다. 연결은 다음 특성을 지정하여 정의됩니다.

- 논리적 이름
- 연결 파트너
- 연결 파트너 기본 서비스 위치
- 연결 파트너 Backup 서비스 위치
- 고객의 실제 위치
- IP 접두사

고객은 요구 사항에 따라 단일 연결 또는 다중 연결을 설정할 수 있습니다. 연결은 원격 분석 컬렉션의 단위로도 사용됩니다. 예를 들어 원격 분석 경고를 선택하기 위해 고객은 모니터링할 연결을 정의해야 합니다.

> [!Note]
> Peering Service에 등록하면 선택한 접두사에 대한 대기 시간 측정을 제공하기 위해 Windows 및 Microsoft 365 원격 분석을 분석합니다. 현재 원격 분석 데이터는 /24 이상의 크기 접두사에 대해서만 지원됩니다.
>연결 원격 분석에 대한 자세한 내용은 [Peering Service 연결 원격 분석](connection-telemetry.md)을 참조하세요.
>

## <a name="how-to-create-a-peering-service-connection"></a>피어링 서비스 연결을 만드는 방법

**시나리오** - 아래 그림에 표시된 것처럼 지점이 여러 지리적 위치에 분산되어 있다고 가정하겠습니다. 여기서 고객은 단일 연결과 연결된(고객이 소유하거나 서비스 공급자가 할당한) 논리적 이름, SP(서비스 공급자) 이름, 고객의 실제 위치 및 IP 접두사를 제공해야 합니다.  기본 및 백업 서비스 위치(파트너 포함)는 고객이 선호하는 서비스 위치를 정의하는 데 도움이 됩니다. 다른 위치에 대한 Peering Service를 만들려면 이 프로세스를 반복해야 합니다.

![지역 중복 연결](./media/peering-service-connection/peering-service-connections.png)

> [!Note]
> 상태 수준 필터링은 연결이 지리적으로 미국에 위치해 있는 경우 고객의 실제 위치에 대해 고려됩니다.
>

## <a name="next-steps"></a>다음 단계

Peering Service 연결을 등록하는 방법에 대한 단계별 프로세스를 알아보려면 [Azure Portal을 사용하여 Peering Service 만들기](azure-portal.md)를 참조하세요.

Peering Service 연결 원격 분석에 대해 알아보려면 [Peering Service 연결 원격 분석](connection-telemetry.md)을 참조하세요.

원격 분석에 액세스하려면 [연결 원격 분석 액세스](measure-connection-telemetry.md)를 참조하세요.
