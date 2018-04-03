---
title: Azure Government - 모니터링 | Microsoft Docs
description: Azure는 Azure에서 실행 중인 응용 프로그램뿐만 아니라 다른 클라우드 및 온-프레미스에 전체 관리를 제공하기 위해 작동하는 여러 서비스 및 도구입니다.  이 아티클에서는 관리의 다른 영역에 대한 높은 수준의 설명을 제공하고, 클라우드 응용 프로그램 및 리소스를 관리하는 Azure 도구의 콘텐츠에 연결합니다.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2018
ms.author: bwren
ms.openlocfilehash: b20283e1189e4f1a3555e2dd8d25972c9a677cd6
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="azure-management---monitoring"></a>Azure Government - 모니터링

Azure의 모니터링은 Azure 관리의 측면 중 하나입니다.  이 아티클은 설명서와 연결된 Azure에서 응용 프로그램과 리소스를 배포하고 유지 관리하는 데 필요한 관리의 다른 영역을 간략히 설명합니다.

## <a name="management-in-azure"></a>Azure의 관리

관리는 비즈니스 응용 프로그램을 유지 관리하는 데 필요한 작업과 프로세스 및 지원하는 리소스를 가리킵니다.  Azure는 Azure에서 실행 중인 응용 프로그램뿐만 아니라 다른 클라우드 및 온-프레미스에 전체 관리를 제공하기 위해 작동하는 여러 서비스 및 도구입니다.  전체 관리 환경을 디자인하는 첫 번째 단계는 제공되는 다양한 도구와 다양한 관리 시나리오에 함께 사용하는 방법을 이해하는 것입니다.

다음 다이어그램에서는 응용 프로그램이나 리소스를 유지 관리하는 데 필요한 관리의 다른 영역을 보여줍니다.  이러한 다른 영역은 각 영역이 리소스의 수명을 계속 승계하는 데 필요한 생명 주기 측면에서 생각해볼 수 있습니다.  이는 연속된 작업을 통해 초기 배포로 시작하고 마지막으로 사용 중지될 때 끝납니다.

![관리 기능](media/management-overview/management-capabilities.png)


단일 Azure 서비스는 특정 관리 영역의 요구 사항을 완전히 충족하지 못하지만 대신 각 요구 사항은 공동 작업하는 여러 서비스에 의해 실현됩니다.  일부 서비스는 웹 응용 프로그램에 대한 모니터링을 제공하는 Application Insights와 같은 대상 기능을 제공합니다.  다른 서비스는 다양한 서비스에서 수집한 다양한 유형의 데이터를 분석할 수 있게 하는 다른 서비스에 대한 관리 데이터를 저장하는 Log Analytics 같은 일반 기능을 제공합니다.  

다음 섹션에서는 다른 관리 영역을 간략히 설명하고 해결 의도가 있는 기본 Azure 서비스에서 자세한 콘텐츠에 대한 링크를 제공합니다.

## <a name="monitor"></a>모니터
모니터링은 비즈니스 응용 프로그램 및 종속 리소스의 성능, 상태 및 가용성을 결정하기 위해 데이터를 수집 및 분석하는 작업입니다. 효과적인 모니터링 전략은 응용 프로그램의 다양한 구성 요소에 대한 자세한 작동을 이해하고, 문제가 심각해지기 전에 해결할 수 있도록 중대한 문제를 사전에 알림으로써 작동 시간을 늘리는 데 도움을 줍니다.  [Azure 응용 프로그램 및 리소스 모니터링](monitoring-overview.md)에서 모니터링 전략에 사용되는 다른 서비스를 식별하는 Azure의 Monitoring에 대한 개요를 읽을 수 있습니다.


## <a name="configure"></a>구성
구성이란 응용 프로그램 및 리소스의 초기 배포 및 구성과 패치 및 업데이트를 사용하여 진행 중인 유지 관리를 가리킵니다.  스크립트 및 정책을 통해 이러한 작업의 자동화를 사용하면 중복성을 줄여서 시간과 노력을 최소화하고 정확도 및 효율성을 증가시킬 수 있습니다.  [Azure Automation](..\automation\automation-intro.md)에서는 구성 작업을 자동화하는 일련의 서비스를 제공합니다.  프로세스를 자동화하는 Runbook 외에도 구성 및 업데이트 관리를 제공합니다. 여기에서 정책을 통해 구성을 관리하고 업데이트를 식별하고 배포할 수 있습니다.

## <a name="govern"></a>거버넌스
거버넌스는 Azure의 응용 프로그램 및 리소스에 대한 제어를 유지 관리하는 메커니즘 및 프로세스를 제공합니다.  여기에는 이니셔티브를 계획하고 전략적 우선 순위를 설정하는 작업이 포함됩니다.  Azure의 거버넌스는 주로 두 가지 서비스를 통해 구현됩니다.  [Azure Policy](../azure-policy/azure-policy-introduction.md)를 사용하면 리소스에 대해 다양한 규칙과 작업을 적용하여 리소스를 회사 표준 및 서비스 수준 계약을 준수하는 상태로 유지하는 정책 정의를 만들고, 할당하고, 관리할 수 있습니다. [Cloudyn에서 개발한 Azure Cost Management](../cost-management/overview.md)를 사용하면 Azure 리소스와 AWS 및 Google을 포함한 다른 클라우드 공급자에 대한 클라우드 사용량 및 비용을 추적할 수 있습니다.

## <a name="secure"></a>보안
응용 프로그램, 리소스 및 데이터의 보안 관리에는 위협 평가하고 보안 데이터 수집 및 분석하고 응용 프로그램과 리소스가 안전하게 구성 및 디자인되도록 보장하는 조합이 필요합니다.  보안 모니터링 및 위협 분석은 [Azure Security Center](../security-center/security-center-intro.md)에서 제공하며 하이브리드 클라우드 워크로드에 대한 통합 보안 관리 및 고급 위협 방지가 포함됩니다.  또한 Azure의 보안에 포괄적인 정보 및 Azure 리소스를 안전하게 구성하는 지침은 [Azure 보안 소개](../security/azure-security.md)를 참조해야 합니다.


## <a name="protect"></a>보호
보호란 사용자 제어할 수 없는 중단이 발생한 경우에도 응용 프로그램 및 데이터를 항상 사용할 수 있도록 보장하는 것을 가리킵니다.  Azure의 보호는 두 가지 서비스에서 제공됩니다.  [Azure Backup](../backup/backup-introduction-to-azure-backup.md)은 클라우드 또는 온-프레미스에서 데이터의 백업 및 복구를 제공합니다.    [Azure Site Recovery](../site-recovery/site-recovery-overview.md)를 통해 재해 발생 시 비즈니스 연속성 및 즉시 복구를 제공하여 응용 프로그램의 고가용성을 보장합니다.

## <a name="migrate"></a>마이그레이션 
마이그레이션이란 현재 온-프레미스에서 실행하는 워크로드를 Azure 클라우드로 전환하는 것을 가리킵니다.  [Azure Migrate](../migrate/migrate-overview.md)는 Azure에 대한 온-프레미스 가상 머신의 성능 기반 크기 및 비용 예상을 비롯한 마이그레이션 적합성을 평가할 수 있는 서비스입니다.  Azure Site Recovery를 통해 [온-프레미스에서](../site-recovery/migrate-tutorial-on-premises-azure.md) 또는 [Amazon Web Services에서](../site-recovery/migrate-tutorial-aws-azure.md) 가상 머신의 실제 마이그레이션을 수행할 수 있습니다.  [Azure 데이터베이스 마이그레이션](../dms/dms-overview.md)은 여러 데이터베이스 원본을 Azure 데이터 플랫폼으로 마이그레이션하는 데 도움이 됩니다.