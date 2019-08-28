---
title: Azure 인프라 보안 | Microsoft Docs
description: 이 문서에서는 Microsoft가 Azure 데이터 센터의 보안을 유지하는 방법을 설명합니다.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: 3da9fef8c7de248f30100f94724cd5dcdb7db8e3
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727069"
---
# <a name="azure-infrastructure-security"></a>Azure 인프라 보안
Microsoft Azure는 Microsoft에서 관리하고 운영하는 데이터 센터에서 실행됩니다. 지리적으로 분산된 데이터 센터는 보안 및 안정성을 위해 ISO/IEC 27001:2013 및 NIST SP 800-53과 같은 주요 산업 표준을 준수합니다. 데이터 센터는 Microsoft 운영 담당자에 의해 관리되고 모니터링됩니다. 운영 담당자는 연중 무휴로 사용하여 세계에서 가장 규모가 큰 온라인 서비스를 제공하는 환경을 경험했습니다.

이 문서 시리즈에서는 Microsoft에서 Azure 인프라를 보호하는 내용에 대한 정보를 제공합니다. 아티클을 다음을 확인합니다.

- [물리적 보안](physical-security.md)
- [가용성](infrastructure-availability.md)
- [구성 요소 및 경계](infrastructure-components.md)
- [네트워크 아키텍처](infrastructure-network.md)
- [프로덕션 네트워크](production-network.md)
- [SQL Database](infrastructure-sql.md)
- [작업](infrastructure-operations.md)
- [모니터링](infrastructure-monitoring.md)
- [무결성](infrastructure-integrity.md)
- [데이터 보호](protection-customer-data.md)

## <a name="shared-responsibility-model"></a>공동 책임 모델
사용자와 Microsoft 간의 책임 분담을 이해해야 합니다. 온-프레미스에서는 전체 스택을 가지고 있지만 클라우드로 이동할 때 일부 책임이 Microsoft로 이전됩니다. 다음 그림은 스택 배포 형식(software as a service [SaaS], platform as a service [PaaS], infrastructure as a service [IaaS], 온-프레미스)에 따른 책임 영역을 보여줍니다.

![책임을 보여주는 그래픽](./media/infrastructure/responsibility-zones.png)

고객은 배포 유형에 관계없이 항상 다음에 대한 책임이 있습니다.

- 보기
- 엔드포인트
- 계좌
- 액세스 관리

SaaS, PaaS 및 IaaS 배포에서 사용자와 Microsoft 간의 책임 분배를 이해해야 합니다. 자세한 내용은 [클라우드 컴퓨팅에 대한 공유 책임](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf)을 참조하세요.

## <a name="next-steps"></a>다음 단계
Azure 인프라를 보호하기 위해 Microsoft에서 수행하는 작업에 대해 자세히 알아보려면 다음을 참조하세요.

- [Azure 시설, 구역 및 물리적 보안](physical-security.md)
- [Azure 인프라 가용성](infrastructure-availability.md)
- [Azure 정보 시스템 구성 요소 및 경계](infrastructure-components.md)
- [Azure 네트워크 아키텍처](infrastructure-network.md)
- [Azure 프로덕션 네트워크](production-network.md)
- [Azure SQL Database 보안 기능](infrastructure-sql.md)
- [Azure 프로덕션 운영 및 관리](infrastructure-operations.md)
- [심층 인프라 모니터링](infrastructure-monitoring.md)
- [Azure 인프라 무결성](infrastructure-integrity.md)
- [Azure 고객 데이터 보호](protection-customer-data.md)


