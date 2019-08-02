---
title: Azure Policy 및 Azure 청사진을 사용 하 여 보안 준수
description: ASD ISM 및 필수 8과 관련 된 오스트레일리아 정부 기관에 대 한 Azure Policy 및 Azure 청사진으로 규정 준수 및 보안 적용
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 0eb3e669f622a9c09c903530cbbab19206ce227f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571745"
---
# <a name="security-compliance-with-azure-policy-and-azure-blueprints"></a>Azure Policy 및 Azure 청사진을 사용 하 여 보안 준수

온-프레미스, 클라우드 네이티브 또는 하이브리드 환경에 있든 상관 없이 IT 환경 내에서 거 버 넌 스를 적용 하는 과제는 모든 조직에 존재 합니다. Microsoft Azure 환경이 디자인, 규정 및 보안 요구 사항을 준수 하는지 확인 하기 위해 강력한 기술 관리 프레임 워크를 준비 해야 합니다.

오스트레일리아 정부 기관에서는 위험을 평가할 때 고려해 야 하는 주요 컨트롤이 [ACSC (오스트레일리아 사이버 Security center) ISM (Information Security Manual](https://acsc.gov.au/infosec/ism/index.htm) )에 있습니다. ISM 내에서 자세히 설명 하는 대부분의 컨트롤은 효과적으로 관리 하 고 적용 하기 위해 기술 관리를 적용 해야 합니다. 사용자 환경에서 구성을 평가 하 고 적용 하는 데 적합 한 도구가 있어야 합니다.

Microsoft Azure은 이러한 과제, Azure Policy 및 Azure 청사진을 지원 하기 위한 두 가지 무료 서비스를 제공 합니다.

## <a name="azure-policy"></a>Azure Policy

Azure Policy를 사용 하면 조직의 IT 거 버 넌 스에 대 한 기술 요소를 응용 프로그램에서 사용할 수 있습니다. Azure Policy는 지속적으로 증가 하는 기본 제공 정책 라이브러리를 포함 합니다. 각 정책은 대상 Azure 리소스에 규칙 및 효과를 적용 합니다.

정책이 리소스에 할당 되 면 해당 정책에 대 한 전반적인 규정 준수를 평가할 수 있으며 필요한 경우 재구성할 수 있습니다.

이 기본 제공 Azure 정책 라이브러리를 사용 하면 조직에서 ACSC ISM에 있는 컨트롤의 유형을 빠르게 적용할 수 있습니다. 컨트롤의 예는 다음과 같습니다.

* 누락 된 시스템 업데이트에 대 한 가상 컴퓨터 모니터링
* Multi-factor authentication에 대 한 승격 된 권한으로 계정 감사
* 암호화 되지 않은 SQL 데이터베이스 식별
* 사용자 지정 Azure RBAC (역할 기반 액세스 제어) 사용 모니터링
* 리소스를 만들 수 있는 Azure 지역 제한

기본 제공 Azure Policy 정의에서 거 버 넌 스 또는 규제 제어를 충족 하지 않는 경우 사용자 지정 정의를 만들고 할당할 수 있습니다. 모든 Azure Policy 정의는 JSON에 정의 되 고 표준 [정의 구조](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)를 따릅니다. 기존 Azure Policy 정의를 중복 하 여 사용자 지정 정책 정의의 기반으로 사용할 수도 있습니다.

특히 복잡 한 환경 또는 엄격한 규정 요구 사항이 있는 환경에서 리소스에 대 한 개별 Azure 정책을 할당 하면 관리자에 게 많은 오버 헤드가 발생할 수 있습니다. 이러한 문제를 지원 하기 위해 Azure 정책 집합을 함께 그룹화 하 여 Azure Policy 이니셔티브를 형성할 수 있습니다. 정책 이니셔티브는 그룹으로 함께 적용 될 때 특정 보안 또는 규정 준수 목표의 기반을 형성 하는 관련 Azure 정책을 결합 하는 데 사용 됩니다. Microsoft는 특정 규정 요구 사항을 충족 하도록 설계 된 정의를 포함 하 여 기본 제공 Azure Policy 이니셔티브 정의를 추가 합니다.

![규정 준수 정책 이니셔티브](media/regulatory-initiatives.png)

모든 Azure 정책 및 이니셔티브는 할당 범위에 할당 됩니다. 이 범위는 Azure 구독, Azure 관리 그룹 또는 Azure 리소스 그룹 수준에서 정의 됩니다. 필요한 Azure 정책 또는 정책 이니셔티브를 할당 한 후에는 조직에서 새로 만든 모든 Azure 리소스에 대 한 구성 요구 사항을 적용할 수 있습니다.

새 Azure Policy 또는 이니셔티브를 할당 해도 기존 Azure 리소스에는 영향을 주지 않습니다. Azure Policy 수 있습니다. 그러나 조직에서 기존 Azure 리소스의 준수를 확인할 수 있습니다. 비준수로 식별 된 모든 리소스는 조직의 판단에 따라 재구성 될 수 있습니다.

### <a name="azure-policy-and-initiatives-in-action"></a>작동 중인 Azure Policy 및 이니셔티브

사용 가능한 기본 제공 Azure Policy 및 이니셔티브 정의는 Azure Portal의 정책 섹션에서 정의 노드 아래에서 찾을 수 있습니다.

![기본 제공 Azure Policy 정의](media/policy-definitions.png)

기본 제공 정의 라이브러리를 사용 하 여 조직 요구 사항을 충족 하는 정책을 신속 하 게 검색 하 고, 정책 정의를 검토 하 고, 해당 리소스에 정책을 할당할 수 있습니다. 예를 들어 ISM에는 모든 권한 있는 사용자에 대 한 MFA (multi-factor authentication)와 중요 한 데이터 리포지토리에 액세스할 수 있는 모든 사용자에 대 한 MFA가 필요 합니다. Azure Policy에서 Azure Policy 정의 간에 "MFA"를 검색할 수 있습니다.

![Azure MFA 정책](media/mfa-policies.png)

적절 한 정책이 식별 되 면 원하는 범위에 정책을 할당 합니다. 요구 사항을 충족 하는 기본 제공 정책이 없으면 기존 정책을 복제 하 고 원하는 대로 변경할 수 있습니다.

![기존 Azure Policy 복제](media/duplicate-policy.png)

또한 Microsoft는 사용자 지정 Azure 정책을 빌드하기 위한 ' 퀵 스타트 '로 [GitHub](https://github.com/Azure/azure-policy) 의 Azure Policy 샘플 컬렉션을 제공 합니다. 이러한 정책 샘플은 Azure Portal 내의 Azure Policy 편집기에 직접 복사할 수 있습니다.

Azure Policy 이니셔티브를 만들 때 사용 가능한 정책 정의 목록 (기본 제공 및 사용자 지정)을 정렬 하 여 필요한 정의를 추가할 수 있습니다.

예를 들어 Windows 가상 머신과 관련 된 모든 정책에 대해 사용 가능한 Azure Policy 정의 목록을 검색할 수 있습니다. 그런 다음 권장 되는 가상 머신 강화 방법을 적용 하도록 설계 된 이니셔티브에 대 한 정의입니다.

![Azure 정책 목록](media/initiative-definitions.png)

할당 범위에 Azure Policy 또는 정책 이니셔티브를 할당 하는 동안 Azure 관리 그룹 또는 Azure 리소스 그룹을 제외 하 여 정책 효과에서 Azure 리소스를 제외할 수 있습니다.

### <a name="real-time-enforcement-and-compliance-assessment"></a>실시간 적용 및 규정 준수 평가

다음 조건을 충족 하는 경우 범위 내 Azure 리소스의 규정 준수 검사를 수행 하는 Azure Policy 합니다.

* Azure Policy 또는 Azure Policy 이니셔티브를 할당 하는 경우
* 기존 Azure Policy 또는 이니셔티브의 범위가 변경 된 경우
* API를 통해 시간당 최대 10 개까지 검색
* 24 시간 마다 한 번-기본 동작

단일 Azure 리소스에 대 한 정책 준수 검사는 리소스를 변경한 후 15 분이 소요 됩니다.

Azure Policy 리소스의 준수에 대 한 개요는 정책 준수 대시보드를 통해 Azure Portal 내에서 검토할 수 있습니다.

![Azure Policy 준수 점수](media/simple-compliance.png)

전체 리소스 준수 백분율 그림은 모든 할당 된 Azure 정책에 대해 범위 내에 배포 된 모든 리소스의 준수를 집계 한 것입니다. 이렇게 하면 비규격 환경 내에서 리소스를 식별 하 고 이러한 리소스를 최적으로 수정할 계획을 세울 수 있습니다.

정책 준수 대시보드에는 각 리소스에 대 한 변경 내용도 포함 됩니다. 리소스가 더 이상 할당 된 정책을 준수 하지 않는 것으로 식별 되 고 자동 수정이 사용 하도록 설정 되지 않은 경우 변경한 사람, 변경 된 내용, 해당 리소스를 변경한 시간을 볼 수 있습니다.

## <a name="azure-blueprints"></a>Azure Blueprints

Azure 청사진은와 결합 하 여 Azure Policy 기능을 확장 합니다.

* Azure RBAC
* Azure 리소스 그룹
* [Azure 리소스 관리자 템플릿](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

청사진을 사용 하면 리소스 관리자 템플릿에서 Azure 리소스를 배포 하 고, RBAC를 구성 하 고, Azure Policy를 할당 하 여 구성을 적용 하 고 감사 하는 환경 디자인을 만들 수 있습니다. 청사진은 편집 및 다시 배포 가능한 환경 템플릿을 구성 합니다. 청사진을 만든 후에는 Azure 구독에 할당 될 수 있습니다. 할당 되 면 청사진 내에 정의 된 모든 Azure 리소스가 만들어지고 Azure 정책이 적용 됩니다. Azure Blueprint에 정의 된 리소스의 배포 및 구성은 Azure Portal의 Azure Blueprint 콘솔에서 모니터링할 수 있습니다.

편집 된 Azure 청사진은 Azure Portal에서 다시 게시 해야 합니다. 청사진을 다시 게시할 때마다 청사진의 버전 번호가 증가 합니다. 버전 번호를 사용 하 여 조직의 Azure 구독에 배포 된 청사진의 특정 버전을 확인할 수 있습니다. 원하는 경우 현재 할당 된 버전의 청사진을 최신 버전으로 업데이트할 수 있습니다.

Azure Blueprint를 사용 하 여 배포 된 리소스는 배포할 때 [Azure 리소스 잠금으로](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) 구성할 수 있습니다. 리소스 잠금으로 인해 리소스가 실수로 수정 되거나 삭제 되지 않습니다.

Microsoft는 다양 한 산업 및 규정 요구 사항에 대 한 Azure Blueprint 템플릿을 개발 하 고 있습니다. 사용 가능한 Azure Blueprint 정의의 현재 라이브러리는 서비스 신뢰 포털의 Azure Portal 또는 [Azure 보안 및 규정 준수 청사진](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview/) 페이지에서 볼 수 있습니다.

### <a name="azure-blueprint-artifacts"></a>Azure Blueprint 아티팩트

Azure Blueprint 만들려면 빈 청사진 템플릿으로 시작 하거나 기존 샘플 청사진 중 하나를 시작 점으로 사용할 수 있습니다. 배포의 일부로 구성 될 청사진에 아티팩트를 추가할 수 있습니다.

![Azure Blueprint 아티팩트](media/blueprint-artifacts.png)

이러한 아티팩트에는 Azure 리소스 그룹 및 리소스 및 연결 된 Azure Policy 및 정책 이니셔티브를 포함 하 여 사용자가 규정 요구 사항을 준수 하는 데 필요한 구성 (예: ISM 컨트롤)을 적용할 수 있습니다. 시스템 강화

이러한 각 아티팩트는 매개 변수를 사용 하 여 구성할 수도 있습니다. 이러한 값은 청사진을 Azure 구독에 할당 하 고 배포할 때 제공 됩니다. 매개 변수를 사용 하면 단일 청사진을 만들고 사용 하 여 기본 청사진을 편집할 필요 없이 다른 환경에 리소스를 배포할 수 있습니다.

Microsoft는 청사진을 CI/CD 파이프라인을 통해 조직에서 유지 관리 하 고 배포할 수 있도록 Azure 청사진을 만들고 관리 하기 위해 Azure PowerShell 및 CLI cmdlet을 개발 하 고 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Policy 및 Azure 청사진을 사용 하 여 거 버 넌 스 및 보안을 적용 하는 방법을 설명 했습니다. 이제 높은 수준에서 제공 되었으므로 각 서비스를 사용 하는 방법에 대해 자세히 알아보세요.

* [Azure Policy 개요](https://docs.microsoft.com/azure/governance/policy/overview)
* [Azure 청사진 개요](https://azure.microsoft.com/services/blueprints/)
* [Azure Policy 샘플](https://docs.microsoft.com/azure/governance/policy/samples/index)
* [Azure Policy 샘플 리포지토리](https://github.com/Azure/azure-policy)
* [Azure Policy 정의 구조](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)
* [Azure Policy 효과](https://docs.microsoft.com/azure/governance/policy/concepts/effects)
