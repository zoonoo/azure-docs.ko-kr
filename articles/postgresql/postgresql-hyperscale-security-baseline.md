---
title: Azure Database for PostgreSQL - 하이퍼스케일(Citus)에 대한 Azure 보안 기준
description: Azure Database for PostgreSQL - 하이퍼스케일(Citus) 보안 기준은 Azure Security Benchmark에 지정된 보안 추천 사항을 구현하기 위한 절차 지침과 리소스를 제공합니다.
author: msmbaldwin
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: c373bb172be01594bb5642a626cad24838b66ea2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102607988"
---
# <a name="azure-security-baseline-for-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL - 하이퍼스케일(Citus)에 대한 Azure 보안 기준

Azure Database for PostgreSQL - 하이퍼스케일(Citus)에 대한 Azure 보안 기준에는 배포의 보안 상태를 향상하는 데 도움이 되는 추천 사항이 있습니다.

이 서비스의 기준은 [Azure Security Benchmark 버전 1.0](../security/benchmarks/overview.md)에서 가져왔으며, 모범 사례 지침을 통해 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 추천 사항을 제공합니다.

자세한 내용은 [Azure 보안 기준 개요](../security/benchmarks/security-baselines-overview.md)를 참조하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [보안 제어: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조하세요.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: Azure Database for PostgreSQL 서버 방화벽은 권한이 있는 컴퓨터를 지정할 때까지 하이퍼스케일(Citus) 코디네이터 노드에 대한 모든 액세스를 차단합니다. 방화벽은 각 요청이 시작된 IP 주소의 서버에 대한 액세스를 허용합니다. 방화벽을 구성하려면 허용 가능한 IP 주소 범위를 지정하는 방화벽 규칙을 생성해야 합니다. 서버 수준의 방화벽 규칙을 만들 수 있습니다.

- [Azure Database for PostgreSQL - 하이퍼스케일(Citus)에서 방화벽 규칙을 구성하는 방법](./concepts-hyperscale-firewall-rules.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지 관리

**지침**: Azure Policy를 사용하여 Azure Database for PostgreSQL 인스턴스에 연결된 네트워크 설정 및 네트워크 리소스에 대한 표준 보안 구성을 정의하고 구현합니다. “Microsoft.Network” 네임스페이스에서 Azure Policy 별칭을 사용하여 Azure Database for PostgreSQL 인스턴스에 대한 네트워크 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [네트워킹을 위한 Azure Policy 샘플](../governance/policy/samples/built-in-policies.md#network)

- [Azure Blueprint를 만드는 방법](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [보안 제어: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조하세요.*

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: 컨트롤 플레인 감사 로깅의 경우 Azure 활동 로그 진단 설정을 사용하도록 설정하고, 보관을 위해 로그를 Log Analytics 작업 영역, Azure 이벤트 허브 또는 Azure 스토리지 계정에 보냅니다. Azure 활동 로그 데이터를 사용하면 Azure 리소스의 컨트롤 플레인 수준에서 수행되는 모든 쓰기 작업(PUT, POST, DELETE)에 대한 "무엇을, 누가, 언제"를 판단할 수 있습니다.

또한 Azure Monitor를 통해 로그를 수집하여 하이퍼스케일(Citus)에서 생성된 보안 데이터를 집계합니다. Azure Monitor 내에서 Log Analytics 작업 영역을 사용하여 분석을 쿼리 및 수행하고, 스토리지 계정을 장기/보관 스토리지에 사용할 수 있습니다. 또는 Azure Sentinel 또는 타사 SIEM(Security Incident and Event Management)을 사용하도록 설정하고 데이터를 온보딩할 수 있습니다. 

- [Azure 활동 로그에 대한 진단 설정을 사용하도록 설정하는 방법](../azure-monitor/essentials/activity-log.md)

- [하이퍼스케일(Citus)의 메트릭](./concepts-hyperscale-monitoring.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: 하이퍼스케일(Citus)은 서버 그룹의 각 노드에 대한 메트릭을 제공합니다. 메트릭은 지원 리소스의 동작에 대한 인사이트를 제공합니다. 각 메트릭은 1분 빈도로 내보내지며, 최대 30일 동안 기록됩니다.

Azure 활동 로그 진단 설정을 사용하도록 설정하고 보관을 위해 로그를 Log Analytics 작업 영역, Azure 이벤트 허브 또는 Azure 스토리지 계정으로 보냅니다. Azure 활동 로그 데이터를 사용하면 Azure 리소스의 컨트롤 플레인 수준에서 수행되는 모든 쓰기 작업(PUT, POST, DELETE)에 대한 "무엇을, 누가, 언제"를 판단할 수 있습니다.

또한 Azure Monitor를 통해 로그를 수집하여 하이퍼스케일(Citus)에서 생성된 보안 데이터를 집계합니다. Azure Monitor 내에서 Log Analytics 작업 영역을 사용하여 분석을 쿼리 및 수행하고, 스토리지 계정을 장기/보관 스토리지에 사용할 수 있습니다. 또는 Azure Sentinel 또는 타사 SIEM(Security Incident and Event Management)을 사용하도록 설정하고 데이터를 온보딩할 수 있습니다. 

- [하이퍼스케일(Citus)의 메트릭](./concepts-hyperscale-monitoring.md)

- [Azure 활동 로그에 대한 진단 설정을 사용하도록 설정하는 방법](../azure-monitor/essentials/activity-log.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

**지침**: Azure Monitor 내에서 조직의 준수 규정에 따라 하이퍼스케일(Citus) 로그를 보관하는 데 사용되는 Log Analytics 작업 영역에 대한 보존 기간을 설정합니다. 장기/보관 스토리지에 Azure Storage 계정을 사용합니다.

- [Log Analytics 작업 영역에 대한 로그 보존 매개 변수를 설정하는 방법](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Azure Storage 계정에 리소스 로그 저장](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: 비정상 동작에 대한 하이퍼스케일(Citus) 인스턴스의 로그를 분석하고 모니터링합니다. Azure Monitor의 Log Analytics를 사용하여 로그를 검토하고 로그 데이터에 대한 쿼리를 수행합니다. 또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다.

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

- [Log Analytics에 대한 자세한 내용](../azure-monitor/logs/log-analytics-tutorial.md)

- [Azure Monitor에서 사용자 지정 쿼리를 수행하는 방법](../azure-monitor/logs/get-started-queries.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상 활동에 대한 경고 사용

**지침**: 하이퍼스케일(Citus)에 대한 진단 설정을 사용하도록 설정하고 Log Analytics 작업 영역으로 로그를 보낼 수 있습니다. Azure 서비스의 모니터링 메트릭을 기반으로 경고를 구성하고 받을 수 있습니다. Azure Monitor의 Log Analytics를 사용하여 로그를 검토하고 로그 데이터에 대한 쿼리를 수행합니다. 또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다.

Log Analytics 작업 영역을 Azure Sentinel에 등록하여 SOAR(보안 오케스트레이션 자동화 응답) 솔루션을 제공합니다. 이를 통해 플레이북(자동화된 솔루션)을 만들어 보안 문제를 수정하는 데 사용할 수 있습니다.

- [하이퍼스케일(Citus)의 메트릭](./howto-hyperscale-alert-on-metric.md)

- [Azure 활동 로그에 대한 진단 설정을 구성하는 방법](../azure-monitor/essentials/activity-log.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="identity-and-access-control"></a>ID 및 액세스 제어

*자세한 내용은 [보안 제어: ID 및 액세스 제어](../security/benchmarks/security-control-identity-access-control.md)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정 인벤토리 유지 관리

**지침**: 하이퍼스케일(Citus) 인스턴스의 컨트롤 플레인(예: Azure Portal)에 대한 관리 액세스 권한이 있는 사용자 계정의 인벤토리를 유지 관리합니다. 또한 하이퍼스케일(Citus) 인스턴스의 데이터 평면에 대한 액세스 권한이 있는 관리 계정의 인벤토리를 유지 관리합니다.

하이퍼스케일(Citus)은 기본 제공 역할 기반 액세스 제어를 지원하지 않지만, 특정 리소스 공급자 작업을 기반으로 하는 사용자 지정 역할을 만들 수 있습니다.

또한 PostgreSQL 엔진은 역할을 사용하여 데이터베이스 개체에 대한 액세스를 제어하고, 새로 만든 하이퍼스케일(Citus) 서버 그룹에는 사전 정의된 여러 역할이 제공됩니다. 사용자 권한을 수정하려면 PgAdmin 또는 psql과 같은 도구를 사용하여 표준 PostgreSQL 명령을 사용합니다.

- [Azure 구독에 대한 사용자 지정 역할 이해](../role-based-access-control/custom-roles.md) 

- [Azure Database for PostgreSQL 리소스 공급자 작업 이해](../role-based-access-control/resource-provider-operations.md#microsoftdbforpostgresql) 

- [Azure Database for PostgreSQL에 대한 액세스 관리 이해](./concepts-security.md#access-management)

- [Azure Database for PostgreSQL - 하이퍼스케일(Citus)에서 사용자를 만드는 방법](./howto-hyperscale-create-users.md)

- [psql을 사용하여 PostgreSQL - 하이퍼스케일(Citus)에 연결하는 방법](./quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql)


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 해당하는 경우 기본 암호 변경

**지침**: Azure AD에는 기본 암호 개념이 없습니다. 암호를 요구하는 다른 Azure 리소스는 복잡성 요구 사항과 최소 암호 길이(서비스에 따라 다름)를 준수하여 암호를 만들도록 강제합니다. 타사 애플리케이션 및 마켓플레이스 서비스의 경우 재량에 따라 기본 암호를 사용할 수 있습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 하이퍼스케일(Citus)에 액세스하는 데 사용되는 전용 관리 계정을 사용하는 방법에 대한 표준 운영 절차를 만듭니다. Azure 리소스를 관리하기 위한 관리자 계정은 Azure Active Directory에 연결되며, 데이터베이스 액세스 권한을 관리하기 위해 하이퍼스케일(Citus) 서버 그룹 내에 존재하는 로컬 서버 관리자 계정도 있습니다. Azure Security Center ID와 액세스 관리를 사용하여 Azure Active Directory 내의 관리 계정 수를 모니터링합니다.

- [Azure Security Center ID 및 액세스 이해](../security-center/security-center-identity-access.md) 

- [Azure Database for PostgreSQL - 하이퍼스케일(Citus)에서 사용자를 만드는 방법](./howto-hyperscale-create-users.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure Portal에 액세스하려면 Azure Active Directory MFA(Multi-Factor Authentication)를 사용하도록 설정하고, Azure Security Center ID 및 액세스 관리 추천 사항을 따릅니다.

- [Azure에서 MFA를 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)


**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(Privileged Access Workstation) 사용

**지침**: Azure 리소스에 로그인하여 구성하도록 구성된 MFA(Multi-Factor Authentication)를 통해 PAW(Privileged Access Workstation)를 사용합니다.

- [Privileged Access Workstation에 대한 자세한 정보](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure에서 MFA를 사용하도록 설정하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="37-alert-on-account-login-behavior-deviation"></a>3.7: 계정 로그인 동작 편차에 대한 경고

**지침**: 환경에서 의심스럽거나 안전하지 않은 활동이 발생하는 경우 로그와 경고를 생성하려면 Azure AD(Active Directory) PIM(Privileged Identity Management)을 사용합니다.

Azure AD 위험 검색을 사용하여 위험한 사용자 동작에 대한 경고 및 보고서를 봅니다.

- [PIM(Privileged Identity Management)을 배포하는 방법](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure AD 위험 탐지 이해](../active-directory/identity-protection/overview-identity-protection.md)


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명된 위치를 사용하여 IP 주소 범위 또는 국가/지역의 특정 논리적 그룹화에서만 포털 및 Azure Resource Manager에 액세스할 수 있도록 허용합니다.

- [Azure에서 명명된 위치를 구성하는 방법](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: PostgreSQL을 관리하려면 Azure AD(Active Directory)를 중앙 인증 및 권한 부여 시스템으로 사용합니다. Azure AD는 강력한 암호화를 저장 데이터 및 전송 중 데이터에 사용하여 데이터를 보호합니다. 또한 Azure AD는 사용자 자격 증명을 솔트하고, 해시하고, 안전하게 저장합니다.

하이퍼스케일(Citus) 서버 그룹 내의 사용자는 Azure Active Directory 계정에 직접 연결할 수 없습니다. 데이터베이스 개체 액세스에 대한 사용자 권한을 수정하려면 PgAdmin 또는 psql과 같은 도구를 사용하여 표준 PostgreSQL 명령을 사용합니다.

- [사용자 역할의 권한 수정](./howto-hyperscale-create-users.md#how-to-modify-privileges-for-user-role)

- [AAD 인스턴스를 만들고 구성하는 방법](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: PostgreSQL 리소스를 관리할 수 있도록 Azure Active Directory를 통해 로컬 데이터베이스에 대한 액세스 권한이 있는 사용자의 액세스를 검토하고 조정합니다.

데이터베이스 Azure 리소스 관리에 대한 액세스 권한이 있는 사용자의 경우 Azure AD(Active Directory) 로그를 검토하여 부실 계정을 검색할 수 있습니다. 또한 Azure ID 액세스 검토를 사용하여 그룹 구성원 자격, 하이퍼스케일(Citus)에 액세스하는 데 사용할 수 있는 엔터프라이즈 애플리케이션에 대한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 알맞은 사용자만 지속적으로 액세스할 수 있도록 사용자 액세스를 90일마다 정기적으로 검토해야 합니다.

- [PostgreSQL 사용자 및 할당된 역할 검토](https://www.postgresql.org/docs/current/database-roles.html)

- [Azure AD 보고 이해](../active-directory/reports-monitoring/index.yml)

- [Azure ID 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화된 자격 증명에 대한 액세스 시도 모니터링

**지침**: Azure AD(Azure Active Directory) 내에서 Azure AD 로그인 활동, 감사 및 위험 이벤트 로그 소스에 액세스가 가능하므로 SIEM 및 모니터링 툴과 통합할 수 있습니다. 

Azure Active Directory 사용자 계정에 대한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics Workspace로 보내면 이 프로세스를 간소화할 수 있습니다. Log Analytics 작업 영역 내에서 원하는 경고를 구성할 수 있습니다. 

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure Active Directory의 ID 보호 및 위험 검색 기능을 사용하여 Azure AD(Active Directory) 수준에서 탐지된 의심스러운 작업에 대해 자동화된 대응을 구성할 수 있습니다. 조직의 보안 대응을 구현하기 위해 Azure Sentinel을 통해 자동화된 대응을 사용하도록 설정할 수 있습니다.

추가 조사를 위해 로그를 Azure Sentinel로 수집할 수도 있습니다.

- [Azure AD ID 보호 개요](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure AD 위험한 로그인을 확인하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오 중 관련 고객 데이터에 대한 액세스 권한을 Microsoft에 제공

**지침**: 현재 사용할 수 없습니다. 고객 Lockbox는 아직 하이퍼스케일(Citus)에서 지원되지 않습니다.

- [고객 Lockbox 지원 서비스 목록](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 현재 사용할 수 없음

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [보안 제어: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조하세요.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 태그를 사용하여 하이퍼스케일(Citus) 인스턴스 또는 중요한 정보를 저장하거나 처리하는 관련 리소스를 추적할 수 있습니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 개발, 테스트 및 프로덕션을 위한 별도의 구독 및/또는 관리 그룹을 구현합니다. 관리 역할 및 방화벽 규칙의 조합을 사용하여 Azure Database for PostgreSQL 인스턴스에 대한 네트워크 액세스를 격리하고 제한합니다.

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

- [Azure Database for PostgreSQL - 하이퍼스케일(Citus)의 방화벽 규칙 이해](./concepts-hyperscale-firewall-rules.md)

- [하이퍼스케일(Citus)의 역할 이해](./howto-hyperscale-create-users.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: 하이퍼스케일(Citus) 코디네이터 노드에 클라이언트 애플리케이션을 연결하려면 TLS(전송 계층 보안) 1.2가 필요합니다. 데이터베이스 서버와 클라이언트 애플리케이션 간에 TLS 연결을 적용하면 서버와 애플리케이션 간의 데이터 스트림을 암호화하여 “메시지 가로채기(man-in-the-middle)” 공격으로부터 보호할 수 있습니다.

Azure Portal을 통해 프로비저닝된 모든 PostgreSQL용 Azure 데이터베이스 서버의 경우 TLS 연결 적용이 기본적으로 사용하도록 설정됩니다.

경우에 따라 안전한 연결을 위해 타사 애플리케이션에 신뢰할 수 있는 CA(인증 기관) 인증서 파일(.cer)에서 생성되는 로컬 인증서 파일이 필요합니다.

- [Azure Database for PostgreSQL - 하이퍼스케일(Citus)에서 TLS를 구성하는 방법](./concepts-hyperscale-ssl-connection-security.md)

- [TLS 연결에 대한 인증서 확인이 필요한 애플리케이션](./concepts-hyperscale-ssl-connection-security.md)



**Azure Security Center 모니터링**: 예

**책임**: 공유됨

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 제어

**지침**: Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 하이퍼스케일(Citus)(예: Azure Portal)에 대한 액세스를 제어합니다. Azure RBAC는 데이터베이스 내의 사용자 권한에 영향을 주지 않습니다.

데이터베이스 수준에서 사용자 권한을 수정하려면 PgAdmin 또는 psql과 같은 도구를 사용하여 표준 PostgreSQL 명령을 사용합니다.

- [Azure RBAC를 구성하는 방법](../role-based-access-control/role-assignments-portal.md)

- [SQL을 사용하여 Azure Database for PostgreSQL에 대한 사용자 액세스를 구성하는 방법](./howto-hyperscale-create-users.md)


**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**:  
Azure Database for PostgreSQL 하이퍼스케일(Citus)은 하루에 한 번 이상 데이터 파일과 데이터베이스 트랜잭션 로그의 스냅샷 백업을 수행합니다. 백업을 사용하면 보존 기간 내의 특정 시점으로 서버를 복원할 수 있습니다. 보존 기간은 모든 클러스터에 대해 최근 35일이며, 모든 백업은 AES 256비트 암호화를 사용하여 암호화됩니다. PostgreSQL 하이퍼스케일(Citus) 제품은 암호화에 Microsoft 관리형 키를 사용합니다.

- [Azure PostgreSQL - 하이퍼스케일(Citus) 백업을 위한 암호화 이해](./concepts-hyperscale-backup.md)



**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 기록 및 경고

**지침**: Azure Monitor에서 Azure 활동 로그를 사용하여 하이퍼스케일(Citus) 및 기타 중요하거나 관련된 리소스의 프로덕션 인스턴스가 변경되는 경우에 대한 경고를 만듭니다.

- [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="vulnerability-management"></a>취약점 관리

*자세한 내용은 [보안 제어: 취약성 관리](../security/benchmarks/security-control-vulnerability-management.md)를 참조하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약성 검사 도구 실행

**지침**: 현재 사용할 수 없음. Azure Security Center는 Azure Database for PostgreSQL - 하이퍼스케일(Citus)에 대한 취약성 평가를 아직 지원하지 않습니다.

- [Azure Security Center의 Azure PaaS 서비스에 대한 기능 적용 범위](../security-center/features-paas.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 현재 사용할 수 없음

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [보안 제어: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화된 자산 검색 솔루션 사용

**지침**: Azure Resource Graph를 사용하여 구독 내의 모든 리소스(하이퍼스케일(Citus) 인스턴스 포함)를 쿼리하고 검색합니다. 테넌트에서 적절한 권한(읽기)이 있는지 확인하고, 모든 Azure 구독 및 구독 내의 리소스를 열거할 수 있습니다.

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

- [Azure 구독을 확인하는 방법](/powershell/module/az.accounts/get-azsubscription)

- [Azure RBAC 이해](../role-based-access-control/overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타데이터를 제공하는 하이퍼스케일(Citus) 인스턴스 및 기타 관련 리소스에 태그를 적용하여 논리적으로 분류 체계를 구성합니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 태그 지정, 관리 그룹 그리고 해당하는 경우에는 별도의 구독을 사용하여 하이퍼스케일(Citus) 인스턴스 및 관련 리소스를 구성하고 추적합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: 승인된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: Azure Policy에서 다음 기본 제공 정책 정의를 사용한 고객 구독에 만들 수 있는 리소스 종류에 대한 제한을 설정할 수 있습니다.

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

또한 Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리/검색합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)


**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy에서 다음 기본 제공 정책 정의를 사용한 고객 구독에 만들 수 있는 리소스 종류에 대한 제한을 설정할 수 있습니다.

- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

또한 Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리/검색합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy에서 다음 기본 제공 정책 정의를 사용한 고객 구독에 만들 수 있는 리소스 종류에 대한 제한을 설정할 수 있습니다.

- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](../governance/policy/samples/index.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Azure Resource Manager와 상호 작용하는 사용자 기능 제한

**지침**: "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성하여 사용자가 Azure Resource Manager와 상호 작용하는 기능을 제한하려면 Azure 조건부 액세스를 사용합니다. 이렇게 하면 중요한 정보를 포함하는 하이퍼스케일(Citus) 인스턴스와 같이 높은 수준의 보안 환경에서 리소스를 만들고 변경하는 것을 방지할 수 있습니다.

- [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [보안 제어: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure Policy를 사용하여 하이퍼스케일(Citus) 인스턴스에 대한 표준 보안 구성을 정의하고 구현합니다. Azure Policy를 사용하여 Azure Database for PostgreSQL 인스턴스에 대한 네트워크 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다.

또한 Azure Resource Manager는 구성이 조직의 보안 요구 사항을 충족하거나 초과하는지 확인하기 위해 검토해야 하는 JSON(JavaScript Object Notation)에서 템플릿을 내보낼 수 있습니다. 

- [사용 가능한 Azure Policy 별칭을 보는 방법](/powershell/module/az.resources/get-azpolicyalias)

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Portal에서 템플릿에 대한 단일 리소스 및 다중 리소스 내보내기](../azure-resource-manager/templates/export-template-portal.md) 



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: [거부] 및 [존재하지 않으면 배포] Azure 정책을 사용하여 보안 설정을 Azure 리소스 전체에 적용합니다.  또한 Azure Resource Manager 템플릿을 사용하여 조직에서 요구하는 Azure 리소스의 보안 구성을 유지 관리할 수 있습니다. 

- [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)

- [규정 준수를 적용하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager 템플릿 개요](../azure-resource-manager/templates/overview.md)



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: 하이퍼스케일(Citus) 및 관련 리소스에 사용자 지정 Azure 정책의 정의를 사용하는 경우 Azure Repos를 사용하여 코드를 안전하게 저장하고 관리합니다.

- [Azure DevOps에 코드를 저장하는 방법](/azure/devops/repos/git/gitworkflow)

- [Azure Repos 설명서](/azure/devops/repos/index)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스를 위한 구성 관리 도구 배포

**지침**: [거부] 및 [존재하지 않으면 배포] Azure 정책을 사용하여 보안 설정을 Azure 리소스 전체에 적용합니다.  또한 Azure Resource Manager 템플릿을 사용하여 조직에서 요구하는 Azure 리소스의 보안 구성을 유지 관리할 수 있습니다. 

- [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)

- [규정 준수를 적용하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager 템플릿 개요](../azure-resource-manager/templates/overview.md)



**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대한 자동화된 구성 모니터링 구현

**지침**: "Microsoft. DBforPostgreSQL" 네임스페이스에서 Azure Policy 별칭을 사용하여 시스템 구성을 경고, 감사 및 적용하는 사용자 지정 정책을 만듭니다. [감사], [거부] 및 [없는 경우 배포] Azure 정책을 사용하여 Azure Database for PostgreSQL 인스턴스 및 관련 리소스에 대한 구성을 자동으로 적용합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: Azure Database for PostgreSQL - 하이퍼스케일(Citus)은 현재 관리 ID를 직접 지원하지 않습니다. Azure Database for PostgreSQL 서버를 만드는 동안 관리자에 대한 자격 증명을 제공해야 합니다. Azure Portal 인터페이스에서 추가 사용자 역할을 만들 수 있습니다.

- [Azure Database for PostgreSQL 만들기 – 하이퍼스케일(Citus)](./quickstart-create-hyperscale-portal.md#create-a-hyperscale-citus-server-group)

- [추가 사용자 역할 만들기](./howto-hyperscale-create-users.md#how-to-create-additional-user-roles)


**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 현재 사용할 수 없음

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다.

- [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [보안 제어: 맬웨어 방어](../security/benchmarks/security-control-malware-defense.md)를 참조하세요.*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: Microsoft 맬웨어 방지는 Azure 서비스(예: 하이퍼스케일(Citus))를 지원하는 기본 호스트에서 사용하도록 설정되어 있지만 고객 콘텐츠에서 실행되지 않습니다.

비 컴퓨팅 Azure 리소스(예: App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL 등)로 업로드되는 콘텐츠를 미리 검사합니다. Microsoft는 이러한 인스턴스의 데이터에 액세스할 수 없습니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [보안 제어: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 자동화된 정기 백업 보장

**지침**: Azure Database for PostgreSQL – 하이퍼스케일(Citus)은 각 노드의 백업을 자동으로 만들고 로컬 중복 스토리지에 저장합니다. 백업을 사용하여 하이퍼스케일(Citus) 클러스터를 지정된 시간으로 복원할 수 있습니다.

- [Azure Database for PostgreSQL - 하이퍼스케일(Citus)에서 백업 및 복원하는 방법](./concepts-hyperscale-backup.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업 수행 및 고객 관리형 키 백업

**지침**: Azure Database for PostgreSQL은 하루에 한 번 이상 데이터 파일과 데이터베이스 트랜잭션 로그의 스냅샷 백업을 수행합니다. 백업을 사용하면 보존 기간 내의 특정 시점으로 서버를 복원할 수 있습니다. 보존 기간은 모든 클러스터에 대해 최근 35일입니다. 모든 백업은 AES 256비트 암호화를 사용하여 암호화됩니다.

가용성 영역을 지원하는 Azure 지역에서 백업 스냅샷은 세 가지 가용성 영역에 저장됩니다. 하나 이상의 가용성 영역이 연결되어 있으면 하이퍼스케일(Citus) 클러스터를 복원할 수 있습니다.

- [Azure Database for PostgreSQL - 하이퍼스케일(Citus)에서 백업 및 복원하는 방법](./concepts-hyperscale-backup.md)


**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리형 키를 포함한 모든 백업의 유효성 검사

**지침**: Azure Database for PostgreSQL에서 하이퍼스케일(Citus) 클러스터를 복원하면 원래 노드의 백업으로부터 새 클러스터가 만들어집니다. 최근 35일 이내의 특정 시점으로 클러스터를 복원할 수 있습니다. 복원 프로세스는 원래와 동일한 Azure 지역, 구독, 리소스 그룹에 새 클러스터를 만듭니다. 새 클러스터 구성은 원래 클러스터 구성과 동일합니다. 즉, vCore 수, 스토리지 크기, 사용자 역할이 동일합니다.

방화벽 설정 및 PostgreSQL 서버 매개 변수는 원래 서버 그룹에서 보존되지 않으며 기본값으로 다시 설정됩니다. 방화벽에서 모든 연결을 차단하므로 복원 후에는 해당 설정을 수동으로 조정해야 합니다.

- [Azure Database for PostgreSQL - 하이퍼스케일(Citus)에서 백업 및 복원하는 방법](./concepts-hyperscale-backup.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리형 키 보호 보장

**지침**: 삭제된 하이퍼스케일(Citus) 클러스터는 복원할 수 없습니다. 클러스터를 삭제하면 클러스터에 속한 모든 노드가 삭제되며 이를 복구할 수 없습니다. 배포 후에 실수로 인한 삭제 또는 예기치 않은 변경에서 클러스터 리소스를 보호하려면 관리자는 관리 잠금을 활용할 수 있습니다.

- [Azure Database for PostgreSQL - 하이퍼스케일(Citus)에서 백업 및 복원하는 방법](./concepts-hyperscale-backup.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [보안 제어: 인시던트 대응](../security/benchmarks/security-control-incident-response.md)을 참조하세요.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 작성합니다. 검색에서 사후 검토에 이르는 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할을 정의하는 인시던트 대응 계획이 있는지 확인합니다. 

- [Azure Security Center 내에서 워크플로 자동화를 구성하는 방법](../security-center/security-center-planning-and-operations-guide.md) 

- [자체 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [고객이 NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용하여 고유한 인시던트 대응 계획 수립을 지원할 수도 있음](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center에서 심각도를 각 경고에 할당하여 먼저 조사해야 하는 경고에 대한 우선 순위를 지정합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용된 결과 또는 분석의 신뢰도 및 경고가 발생된 활동의 배후에 악의적인 의도가 있었음에 대한 신뢰 수준을 기준으로 합니다. 

또한 구독(예: 프로덕션, 비 프로덕션)을 명확하게 표시하고 Azure 리소스를 명확하게 식별하고 분류하는 명명 시스템을 만듭니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 격차를 식별하고 필요에 따라 계획을 수정합니다. 

- [NIST의 게시물을 참조하세요. IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 안내](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 고객 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다.  문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다. 

- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: 연속 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Azure Security Center 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다. 

- [연속 내보내기를 구성하는 방법](../security-center/continuous-export.md) 

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Azure Security Center의 워크플로 자동화 기능을 사용하여 보안 경고 및 권장 사항에 대한 "Logic Apps"를 통해 응답을 자동으로 트리거합니다. 

- [워크플로 자동화와 Logic Apps를 구성하는 방법](../security-center/workflow-automation.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [보안 제어: 침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대한 침투 테스트를 정기적으로 수행 및 모든 중요한 보안 결과를 수정

**지침**: 다음 Microsoft 시행 규칙에 따라 침투 테스트에서 Microsoft 정책을 위반하지 않는지 확인합니다. https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 

- [Microsoft에서 관리하는 클라우드 인프라, 서비스 및 애플리케이션에 대한 레드 팀 및 라이브 사이트 침투 테스트 실행에 대한 자세한 내용은 ](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)를 참조하세요.

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치마크](../security/benchmarks/overview.md)를 참조하세요.
- [Azure 보안 기준](../security/benchmarks/security-baselines-overview.md)에 대해 자세히 알아보세요.