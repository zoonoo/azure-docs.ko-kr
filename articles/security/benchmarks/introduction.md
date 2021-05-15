---
title: Azure 보안 벤치마크 소개
description: 보안 벤치마크 소개
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 32aa64a9343a8e3c62af4322b3320b28ce805064
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97369194"
---
# <a name="azure-security-benchmark-introduction"></a>Azure 보안 벤치마크 소개

새로운 서비스 및 기능이 Azure에서 매일 릴리스됩니다. 개발자는 이러한 서비스를 기반으로 하는 새로운 클라우드 애플리케이션을 빠르게 게시하고 있고 공격자는 항상 잘못 구성된 리소스를 악용할 새로운 방법을 찾고 있습니다. 클라우드는 빠르게 이동하고 개발자는 빠르게 움직이며 공격자는 항상 바쁘게 활동합니다. 클라우드 배포가 항상 안전하게 보호되도록 하려면 어떻게 해야 할까요? 클라우드 시스템의 보안 방법은 온-프레미스 시스템과 어떻게 다른가요? 여러 독립 개발 팀에서 일관성을 모니터링하려면 어떻게 하나요?

Microsoft는 *보안 벤치마크* 를 사용하면 클라우드 배포를 신속하게 보호하는 데 도움이 될 수 있다는 점을 발견했습니다. 클라우드 서비스 공급자의 벤치마크 권장 사항은 사용자 환경에서 특정 보안 구성 설정을 선택할 수 있는 시작 지점을 제공하고 조직에 대한 위험을 빠르게 줄일 수 있게 해줍니다.

Azure Security Benchmark에는 Azure에서 사용하는 서비스를 보호하는 데 사용할 수 있는 효과적인 보안 권장 사항 컬렉션이 포함되어 있습니다.

- **보안 제어**: 이러한 권장 사항은 일반적으로 Azure 테넌트 및 Azure 서비스 전체에 적용됩니다. 각 권장 사항은 일반적으로 벤치마크의 계획, 승인 또는 구현에 관여하는 이해관계자의 목록을 식별합니다. 
- **서비스 기준**: 개별 Azure 서비스에 컨트롤을 적용하여 해당 서비스의 보안 구성에 대한 권장 사항을 제공합니다.

## <a name="implement-the-azure-security-benchmark"></a>Azure 보안 벤치마크 구현
- 엔터프라이즈 컨트롤 및 서비스별 기준에 대한 [설명서](overview.md)를 검토하여 컨트롤 프레임워크를 계획하고 컨트롤 프레임워크가 CIS(Controls v 7.1) 및 NIST(SP 800-53) 프레임워크와 같은 지침에 매핑되는 방법을 계획하는 방식으로 Azure 보안 벤치마크 구현을 **계획** 합니다.
- Azure Security Center [규정 준수 대시보드](../../security-center/security-center-compliance-dashboard.md)를 사용하여 Azure 보안 벤치마크 상태(및 기타 컨트롤 집합)의 규정 준수를 **모니터링** 합니다.
- Azure Blueprints 및 Azure Policy를 사용하여 보안 구성을 자동화하고 Azure 보안 벤치마크(및 조직의 기타 요구 사항)의 규정 준수를 적용하도록 **가드레일을 설정** 합니다.
 
Azure 보안 벤치마크 v2는 Azure 보안 벤치마크에서 Microsoft의 Azure 보안 권장 사항의 단일 통합 보기를 제공할 수 있도록 [Microsoft 보안 모범 사례](/security/compass/microsoft-security-compass-introduction)(이전의 Azure Security Compass)에 맞춰져 있습니다.

## <a name="common-use-cases"></a>일반적인 사용 사례

Azure 보안 벤치마크는 다음과 같은 고객 또는 서비스 파트너의 일반적인 문제를 해결하는 데 자주 사용됩니다.
- Azure를 처음 사용하고 보안 배포를 보장하는 보안 모범 사례를 찾고 있는 고객.
- 상위 위험과 완화의 우선 순위를 지정하기 위해 기존 Azure 배포의 보안 상태를 개선하고 있는 고객.
- 특정 보안 지침을 충족하기 위해 기술 및 비즈니스에 사용할 수 있도록 Azure 서비스를 승인 중인 고객.
- 정부 또는 금융 및 의료(또는 이러한 고객을 위해 시스템을 구축해야 하는 서비스 공급업체)와 같은 강력하게 규제되는 산업에 속한 고객에 대한 규정 요구 사항을 충족하려는 고객. 이러한 고객은 Azure 구성이 CI, NIST 또는 PCI와 같은 업계 프레임워크에서 지정된 보안 기능을 충족하는지 확인해야 합니다. Azure 보안 벤치마크는 이미 이러한 산업 벤치마크에 미리 매핑되어 있는 컨트롤을 사용하는 효율적인 방법을 제공합니다.

## <a name="terminology"></a>용어

"컨트롤", "벤치마크" 및 "기준"이라는 용어는 Azure 보안 벤치마크 설명서에서 자주 사용되며, Azure에서 이러한 용어가 어떻게 사용되는지 이해하는 것이 중요합니다.


| 용어 | 설명 | 예제 |
|--|--|--|
| 컨트롤 | 컨트롤은 처리해야 하고 기술 또는 구현과 관련이 없는 기능이나 작업에 대한 개략적인 설명입니다. | 데이터 보호는 보안 컨트롤 중 하나입니다. 이 컨트롤은 데이터를 보호하기 위해 처리해야 하는 특정 작업을 포함합니다. |
| 벤치마크 | 벤치마크에는 Azure 같은 특정 기술에 대한 보안 권장 사항이 포함되어 있습니다. 권장 사항은 속해 있는 제어로 분류됩니다. | Azure 보안 벤치마크는 Azure 플랫폼과 관련된 보안 권장 사항으로 구성됩니다. |
| 기초 | 기준은 개별 Azure 서비스에 대한 벤치마크의 구현입니다. 각 조직은 벤치마크 권장 사항을 결정하고 해당 구성은 Azure 구현 범위에서 필요합니다. | Contoso Company는 Azure SQL 보안 기준에서 권장되는 구성을 수행하여 Azure SQL 보안 기능을 사용하도록 설정하고자 합니다.

Azure 보안 벤치마크에 대한 피드백을 환영합니다! 아래의 피드백 영역에 의견을 제공해 주시기 바랍니다. Azure 보안 벤치마크 팀과 보다 개인적으로 정보를 공유하기를 원하는 경우 https://aka.ms/AzSecBenchmark 에서 다음 양식을 작성해 주세요.
