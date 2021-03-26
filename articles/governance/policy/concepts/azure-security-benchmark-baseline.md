---
title: Azure Policy에 대 한 Azure 보안 기준
description: Azure Policy 보안 기준은 Azure 보안 벤치 마크에 지정 된 보안 권장 사항을 구현 하기 위한 절차 지침과 리소스를 제공 합니다.
author: msmbaldwin
ms.service: azure-policy
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6e8bb4cf715c6cb8d0729399c1985376de18687b
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561290"
---
# <a name="azure-security-baseline-for-azure-policy"></a>Azure Policy에 대 한 Azure 보안 기준

이 보안 기준은 [Azure 보안 벤치 마크](../../../security/benchmarks/overview.md) 에서 Azure Policy에 대 한 지침을 적용 합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 콘텐츠는 Azure 보안 벤치 마크에서 정의한 **규정 준수 도메인** 및 **보안 제어** 와 Azure Policy에 적용 되는 관련 지침을 기준으로 그룹화 됩니다. Azure Policy에 적용할 수 없는 **컨트롤** 은 제외 되었습니다. Azure Policy 완전히 Azure 보안 벤치 마크에 매핑되는 방법을 보려면 [전체 Azure Policy 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조 하세요.

기본 제공 이니셔티브를 통해 Azure 보안 벤치 마크 컨트롤을 기본 제공 정책 정의로 매핑하면 [규정 준수: Azure 보안 벤치 마크](../samples/azure-security-benchmark.md)를 참조 하세요.

Azure Policy는 _책임_ 대신 _소유권_ 이라는 용어를 사용 합니다. _소유권_ 에 대 한 자세한 내용은 클라우드에서 [정책 정의](./definition-structure.md#type) 및 [공유 책임](../../../security/fundamentals/shared-responsibility.md)Azure Policy을 참조 하세요.

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure 보안 벤치 마크: 로깅 및 모니터링](../../../security/benchmarks/security-control-logging-monitoring.md)을 참조 하세요.*

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: Azure Policy에서는 자동으로 사용 하도록 설정 된 활동 로그를 사용 하 여 이벤트 원본, 날짜, 사용자, 타임 스탬프, 원본 주소, 대상 주소 및 기타 유용한 요소를 포함 합니다.

- [Azure Monitor를 사용 하 여 플랫폼 로그 및 메트릭을 수집 하는 방법](../../../azure-monitor/essentials/diagnostic-settings.md)

- [Azure의 로깅 및 다른 로그 유형 이해](../../../azure-monitor/essentials/platform-logs-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [Azure 보안 벤치 마크: id 및 Access Control](../../../security/benchmarks/security-control-identity-access-control.md)을 참조 하세요.*

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 전용 관리 계정 사용에 대한 표준 운영 절차를 만듭니다. Azure Security Center ID와 액세스 관리를 사용하여 관리 계정 수를 모니터링합니다. [Azure Active Directory (AZURE AD) Privileged Identity Management](../../../active-directory/privileged-identity-management/pim-configure.md) 권한 있는 역할 또는 [Azure Resource Manager](../../../azure-resource-manager/management/overview.md)를 사용 하 여 just-in-time/간단 하 게 액세스 솔루션을 사용 하도록 설정할 수도 있습니다.

**책임**: Customer

**Azure Security Center 모니터링**: [Azure 보안 벤치 마크](/azure/governance/policy/samples/azure-security-benchmark) 는 Security Center에 대 한 기본 정책 이니셔티브 이며 [Security Center 권장 사항의](/azure/security-center/security-center-recommendations)기초가 됩니다. 이 컨트롤과 관련 된 Azure Policy 정의는 Security Center에 의해 자동으로 설정 됩니다. 이 컨트롤과 관련 된 경고에는 관련 서비스에 대 한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의-GuestConfiguration**:

[!INCLUDE [Resource Policy for Microsoft.GuestConfiguration 3.3](../../../../includes/policy/standards/asb/rp-controls/microsoft.guestconfiguration-3-3.md)]

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: 관리 작업에는 안전한 Azure 관리 워크스테이션 사용

**지침**: 다단계 인증을 사용 하는 paw (권한 있는 액세스 워크스테이션)를 사용 하 여 Azure 리소스에 로그인 하 고 구성 합니다.

- [Privileged Access Workstation에 대한 자세한 정보](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure에서 다단계 인증을 사용 하도록 설정 하는 방법](../../../active-directory/authentication/howto-mfa-getstarted.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-protection"></a>데이터 보호

자세한 내용은 [Azure Security Benchmark: 데이터 보호](../../../security/benchmarks/security-control-data-protection.md)를 참조하세요.

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 제어 

**지침**: azure RBAC (역할 기반 액세스 제어)를 사용 하 여 Azure Policy에 대 한 액세스를 제어 합니다.

- [Azure Policy의 Azure RBAC 사용 권한](../overview.md#azure-rbac-permissions-in-azure-policy)

- [Azure RBAC를 구성하는 방법](../../../role-based-access-control/role-assignments-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 로그 및 경고

**지침**: Azure Policy에서 변경이 발생 하는 경우에 대 한 경고를 만들려면 활동 로그와 Azure Monitor를 사용 합니다.

- [Azure 활동 로그 이벤트에 대 한 경고를 만드는 방법](../../../azure-monitor/alerts/alerts-activity-log.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure 보안 벤치 마크: 인벤토리 및 자산 관리](../../../security/benchmarks/security-control-inventory-asset-management.md)를 참조 하세요.*

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타데이터를 제공하는 Azure 리소스에 태그를 적용하여 논리적인 분류로 구성합니다. Azure Policy _수정_ 효과를 사용 하 여 규정 준수 및 일관적인 태그 관리를 보고 하 고 적용 합니다.

- [자습서: 정책 만들기 및 관리](../tutorials/create-and-manage.md)

- [자습서: 태그 거버넌스 관리](../tutorials/govern-tags.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: 승인 된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 조직 요구 사항에 따라 승인 된 정책 정의 및 정책 할당의 인벤토리를 만듭니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy을 사용 하 여 구독에 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다.

- [Azure Policy를 구성하고 관리하는 방법](../tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark V2 개요](../../../security/benchmarks/overview.md)를 참조하세요.
- [Azure 보안 기준](../../../security/benchmarks/security-baselines-overview.md)에 대해 자세히 알아보세요.