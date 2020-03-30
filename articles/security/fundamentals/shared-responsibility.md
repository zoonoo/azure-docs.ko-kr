---
title: 클라우드에서 공동 책임 - 마이크로소프트 Azure
description: 공유 책임 모델과 클라우드 공급자가 처리하는 보안 작업 및 귀하가 처리하는 작업을 이해합니다.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: na
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/16/2019
ms.author: terrylan
ms.openlocfilehash: 8f16105d6bda1798828bc423ec8a158d49e0cf2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72518370"
---
# <a name="shared-responsibility-in-the-cloud"></a>클라우드의 공동 책임

퍼블릭 클라우드 서비스를 고려하고 평가할 때 공유 책임 모델과 클라우드 공급자가 처리하는 보안 작업 및 귀하가 처리하는 작업을 이해하는 것이 중요합니다. 워크로드 책임은 워크로드가 서비스로서의 소프트웨어(SaaS), 서비스로서의 플랫폼(PaaS), 서비스로서의 인프라(IaaS) 또는 온-프레미스 데이터 센터에서 호스팅되는지 여부에 따라 달라집니다.

## <a name="division-of-responsibility"></a>책임 분담
온-프레미스 데이터 센터에서는 전체 스택을 소유합니다. 클라우드로 이동하면서 일부 책임은 Microsoft로 이전됩니다. 다음 다이어그램은 스택 배포 유형에 따라 Microsoft와 사용자 간의 책임 영역을 보여 줍니다.

![책임 영역](./media/shared-responsibility/shared-responsibility.png)

모든 클라우드 배포 유형에 대해 사용자는 고유의 데이터와 ID를 소유합니다. 사용자는 자신이 제어하는 데이터 및 ID 보안, 온-프레미스 리소스 및 클라우드 구성 요소(서비스 유형에 따라 다름)의 보안을 보호하는 데 책임이 있습니다.

배포 유형에 관계없이 다음과 같은 책임은 항상 유지됩니다.

- 데이터
- 엔드포인트
- 계정
- 액세스 관리

## <a name="cloud-security-advantages"></a>클라우드 보안 이점
클라우드는 오랜 정보 보안 문제를 해결하는 데 상당한 이점을 제공합니다. 온-프레미스 환경에서 조직은 충족되지 않은 책임과 제한된 리소스를 보안에 투자할 가능성이 높으며, 이로 인해 공격자가 모든 계층의 취약점을 악용할 수 있는 환경이 만들어집니다.

다음 다이어그램에서는 제한된 리소스로 인해 많은 보안 책임이 충족되지 않는 기존 접근 방식을 보여 주어 있습니다. 클라우드 지원 접근 방식에서는 일상적인 보안 책임을 클라우드 공급자로 전환하고 리소스를 재할당할 수 있습니다.

![클라우드 시대의 보안 이점](./media/shared-responsibility/cloud-enabled-security.png)

클라우드 지원 접근 방식에서는 클라우드 기반 보안 기능을 활용하여 효율성을 높이고 클라우드 인텔리전스를 사용하여 위협 탐지 및 대응 시간을 개선할 수 있습니다. 조직은 클라우드 공급자에 책임을 전가함으로써 보안 적용 범위를 더 넓힐 수 있으며, 보안 리소스와 예산을 다른 비즈니스 우선 순위에 다시 할당할 수 있습니다.

## <a name="next-steps"></a>다음 단계
SaaS, PaaS 및 IaaS 배포에서 사용자와 Microsoft 간의 책임 분할에 대한 자세한 내용은 [클라우드 컴퓨팅에 대한 공유 책임을](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)참조하십시오.
