---
title: Azure Data Box에 대 한 Azure 보안 기준
description: Azure Data Box 보안 기준은 Azure 보안 벤치 마크에 지정 된 보안 권장 사항을 구현 하기 위한 절차 지침과 리소스를 제공 합니다.
author: msmbaldwin
ms.service: databox
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 0a05a4692bb4a09213532ed29b2501f6b8056857
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721993"
---
# <a name="azure-security-baseline-for-azure-data-box"></a>Azure Data Box에 대 한 Azure 보안 기준

이 보안 기준은 [Azure Security 벤치 마크 버전 1.0](../security/benchmarks/overview-v1.md) 의 지침을 Azure Data Box 적용 합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다.
콘텐츠는 Azure 보안 벤치 마크에 정의 된 **보안 컨트롤** 및 Azure Data Box에 적용 되는 관련 지침에 따라 그룹화 됩니다. Azure Data Box에 적용할 수 없는 **컨트롤** 은 제외 되었습니다.

 
Azure Data Box 완전히 Azure 보안 벤치 마크에 매핑되는 방법을 보려면 [전체 Azure Data Box 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조 하세요.

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure 보안 벤치 마크: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조 하세요.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 원본 사용

**지침**: Azure 리소스 (예: 로그의 타임 스탬프)에 사용 되는 시간 원본을 Microsoft에서 유지 관리 합니다. 고객 사이트의 NTP 서버에 액세스할 수 있는 네트워크에 연결 되어 있지 않으면 Azure Data Box 시간이 드리프트 될 수 있습니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: Data Box 주문의 각 단계에 해당 하는 여러 작업을 수행 하 여 주문에 대 한 액세스를 제어 하 고, 이벤트를 감사 하 고, 주문을 추적 하 고, 생성 되는 다양 한 로그를 해석할 수 있습니다.

- [Azure Data Box에 대 한 추적 및 이벤트 로깅 이해](data-box-logs.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: Data Box 주문의 각 단계에 해당 하는 여러 작업을 수행 하 여 주문에 대 한 액세스를 제어 하 고, 이벤트를 감사 하 고, 주문을 추적 하 고, 생성 되는 다양 한 로그를 해석할 수 있습니다.

- [Azure Data Box에 대 한 추적 및 이벤트 로깅 이해](data-box-logs.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="26-monitor-and-review-logs"></a>2.6: 로그를 모니터링 하 고 검토 합니다.

**지침**: Data Box 주문의 각 단계에 해당 하는 여러 작업을 수행 하 여 주문에 대 한 액세스를 제어 하 고, 이벤트를 감사 하 고, 주문을 추적 하 고, 생성 되는 다양 한 로그를 해석할 수 있습니다.

- [Azure Data Box에 대 한 추적 및 이벤트 로깅 이해](data-box-logs.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상적인 활동에 대해 경고를 사용 하도록 설정

**지침**: Data Box 주문의 각 단계에 해당 하는 여러 작업을 수행 하 여 주문에 대 한 액세스를 제어 하 고, 이벤트를 감사 하 고, 주문을 추적 하 고, 생성 되는 다양 한 로그를 해석할 수 있습니다.

- [Azure Data Box에 대 한 추적 및 이벤트 로깅 이해](data-box-logs.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [Azure 보안 벤치 마크: id 및 Access Control](../security/benchmarks/security-control-identity-access-control.md)을 참조 하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지 관리

**지침**: Azure Data Box에 대 한 관리 권한이 있는 사용자 계정의 인벤토리를 유지 관리 합니다. 구독에 대 한 Azure Portal의 IAM (Id 및 액세스 제어) 창을 사용 하 여 azure RBAC (역할 기반 액세스 제어)를 구성할 수 있습니다. 역할은 Active Directory의 사용자, 그룹, 서비스 사용자 및 관리 되는 id에 적용 됩니다.

주문이 처음 생성 될 때 주문에 액세스할 수 있는 사용자를 제어할 수 있습니다. 다양 한 범위에서 Azure RBAC 역할을 설정 하 여 Data Box 주문에 대 한 액세스를 제어 합니다. Azure RBAC 역할은 작업의 하위 집합에 대 한 읽기-쓰기, 읽기 전용, 읽기/쓰기 권한 유형을 결정 합니다.

- [사용자 지정 역할 이해](../role-based-access-control/custom-roles.md)

- [통합 문서에 대 한 Azure RBAC를 구성 하는 방법](../sentinel/quickstart-get-visibility.md)

- [주문에 대 한 액세스 제어를 설정 하는 방법 이해](https://docs.microsoft.com/azure/databox/data-box-logs#set-up-access-control-on-the-order)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 기본 암호 변경(해당하는 경우)

**참고**: Azure Active Directory (Azure AD)에는 기본 암호 개념이 없습니다. 암호를 요구 하는 다른 Azure 리소스는 복잡성 요구 사항과 최소 암호 길이를 사용 하 여 암호를 강제로 만들도록 합니다 .이는 서비스에 따라 다릅니다. 기본 암호를 사용할 수 있는 타사 응용 프로그램 및 marketplace 서비스를 담당 합니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 전용 관리 계정 사용에 대한 표준 운영 절차를 만듭니다. Azure Security Center ID와 액세스 관리를 사용하여 관리 계정 수를 모니터링합니다.

또한 전용 관리 계정을 추적하는 데 도움이 되도록 Azure 다음과 같은 Azure Security Center 또는 기본 제공 Azure 정책의 추천 사항을 사용할 수 있습니다.

- 구독에 둘 이상의 소유자를 할당해야 합니다.
- 소유자 권한이 있는 사용되지 않는 계정은 구독에서 제거해야 합니다.
- 소유자 권한이 있는 외부 계정은 구독에서 제거해야 합니다.

- [Azure Security Center를 사용하여 ID 및 액세스를 모니터링하는 방법(미리 보기)](../security-center/security-center-identity-access.md)

- [Azure Policy를 사용하는 방법](../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(Privileged Access Workstation) 사용

**지침**: Azure AD 다단계 인증을 사용 하는 PAW (권한 있는 액세스 워크스테이션)를 사용 하 여 Azure Data Box 주문에 로그인 하 고 구성 합니다.

- [권한 있는 액세스 워크스테이션](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [클라우드 기반 Azure AD 다단계 인증 배포 계획](../active-directory/authentication/howto-mfa-getstarted.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정에서 의심 스러운 활동에 대 한 로그 및 경고

**지침**: 해당 없음 Azure Data Box에는 자체 관리 계정이 없습니다. Azure Portal를 통해 액세스 합니다. 그러나 환경에서 의심 스러운 작업이 나 안전 하지 않은 활동이 발생 하는 경우 로그 및 경고를 생성 하는 데 Azure Active Directory (Azure AD) Privileged Identity Management (PIM)를 사용 하도록 Azure 구독을 구성할 수 있습니다.

또한 Azure AD 위험 탐지를 사용하여 위험한 사용자 동작에 대한 경고 및 보고서를 봅니다.

- [PIM(Privileged Identity Management)을 배포하는 방법](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure AD 위험 탐지 이해](../active-directory/identity-protection/overview-identity-protection.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명된 위치를 사용하여 IP 주소 범위 또는 국가/지역의 특정 논리적 그룹화에서만 Azure Portal에 액세스할 수 있도록 허용합니다.

- [Azure에서 명명된 위치를 구성하는 방법](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: 해당 하는 경우 중앙 인증 및 권한 부여 시스템으로 Azure Active Directory (Azure AD)를 사용 합니다. Azure AD는 강력한 암호화를 저장 데이터 및 전송 중 데이터에 사용하여 데이터를 보호합니다. 또한 Azure AD는 사용자 자격 증명을 솔트하고, 해시하고, 안전하게 저장합니다.

- [Azure AD 인스턴스를 만들고 구성하는 방법](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure Active Directory (Azure AD)는 오래 된 계정을 검색 하는 데 도움이 되는 로그를 제공 합니다. 또한 Azure Id 액세스 검토를 사용 하 여 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 사용자의 액세스를 정기적으로 검토하여 적합한 사용자만 계속 액세스할 수 있도록 합니다.

Data Box 어플라이언스의 경우 실시간으로 지원 되지 않습니다. 작업이 끝날 때 로그를 검토할 수 있습니다.

- [Azure AD 보고 이해](/azure/active-directory/reports-monitoring/)

- [Azure ID 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화 되는 자격 증명에 대 한 액세스 시도를 모니터링 합니다.

**지침**: 해당 하는 경우 중앙 인증 및 권한 부여 시스템으로 Azure Active Directory (Azure AD)를 사용 합니다. Azure AD는 강력한 암호화를 저장 데이터 및 전송 중 데이터에 사용하여 데이터를 보호합니다. 또한 Azure AD는 사용자 자격 증명을 솔트하고, 해시하고, 안전하게 저장합니다.

Azure AD 로그인 활동, 감사 및 위험 이벤트 로그 원본에 액세스할 수 있으며,이를 통해 Azure 센티널 또는 타사 SIEM과 통합할 수 있습니다.

Azure AD 사용자 계정에 대 한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics 작업 영역으로 전송 하 여이 프로세스를 간소화할 수 있습니다. Log Analytics 내에서 원하는 로그 경고를 구성할 수 있습니다.

Azure Data Box 서비스 로그는 Log Analytics 작업 영역에 기록 되지 않습니다.

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대 한 경고

**지침**: 제어 평면 (예: Azure Portal)의 계정 로그인 동작 편차에 대해 Azure Active Directory (Azure AD) id 보호 및 위험 검색 기능을 사용 하 여 사용자 id와 관련 된 검색 된 의심 스러운 작업에 대 한 자동화 된 응답을 구성 합니다. 추가 조사를 위해 데이터를 Azure Sentinel로 수집할 수도 있습니다.

- [Azure AD 위험한 로그인을 확인 하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오에서 관련 고객 데이터에 대한 액세스 권한을 Microsoft에 제공

**지침**: 곧 제공 될 예정입니다. 고객 Lockbox는 Azure Data Box에 대해 아직 지원 되지 않습니다. 

- [Customer Lockbox 지원 서비스 목록](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-protection"></a>데이터 보호

자세한 내용은 [Azure Security Benchmark: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조하세요.

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: Microsoft는 Azure Data Box에 대 한 기본 인프라를 관리 하 고, 고객 데이터 손실 또는 노출을 방지 하기 위해 엄격한 컨트롤을 구현 했습니다. 고객 사이트에 Data Box 있는 경우 모범 사례에 따라 전송 되는 중요 한 데이터가 보호 되는지 확인 합니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: Microsoft는 Azure Data Box에 대 한 기본 인프라를 관리 하 고, 고객 데이터 손실 또는 노출을 방지 하기 위해 엄격한 컨트롤을 구현 했습니다. 고객 사이트에 Data Box 있는 경우 모범 사례에 따라 전송 되는 중요 한 데이터가 보호 되는지 확인 합니다.

- [Azure Data Box의 데이터 마이그레이션 이해](data-box-faq.md)

- [Data Box 보안 개요](data-box-security.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 제어 

**지침**: 구독에 대 한 소유자 또는 참가자 액세스 권한이 있는지 확인 하 여 Data Box 순서를 만듭니다. 리소스 수준에서 Data Box 판독기 및 Data Box 참가자 역할을 정의할 수도 있습니다.

- [Azure Data Box를 시작 하는 방법 이해](data-box-quickstart-portal.md)

- [액세스 제어를 설정 하는 방법 이해](https://docs.microsoft.com/azure/databox/data-box-logs#set-up-access-control-on-the-order)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: Azure Data Box는 미사용 데이터에 대 한 AES 256 비트 암호화를 구현 합니다.

Azure Data Box는 미사용 데이터에 대 한 AES 256 비트 암호화를 구현 합니다.

또한 Azure Data Box는 암호화 키를 통해 장치를 잠그는 데 사용 되는 장치 잠금 해제 키 (장치 암호 라고도 함)를 보호 합니다. 기본적으로 Data Box 주문의 장치 잠금 해제 키는 Microsoft 관리 키를 사용 하 여 암호화 됩니다. 장치 잠금 해제 키에 대 한 추가 제어를 위해 고객이 관리 하는 키를 제공할 수도 있습니다. 그러려면 고객 관리형 키를 만들어 Azure Key Vault에 저장해야 합니다.
 

- [데이터 보호 Data Box 이해](data-box-security.md)

- [Azure Data Box Azure Key Vault에서 고객이 관리 하는 키 사용](data-box-customer-managed-encryption-key-portal.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 로그 및 경고

**지침**: Azure 활동 로그와 함께 Azure Monitor를 사용 하 여 변경 내용이 Azure Data Box 및 기타 중요 한 리소스 또는 관련 된 리소스에 대 한 경고를 만듭니다.

- [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](/azure/azure-monitor/platform/alerts-activity-log)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure 보안 벤치 마크: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조 하세요.*

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: 사용자가 Azure Resource Manager 상호 작용할 수 있도록 제한

**지침**: "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성하여 사용자가 Azure Resource Manager와 상호 작용하는 기능을 제한하도록 Azure 조건부 액세스를 구성합니다.

- [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure 보안 벤치 마크: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조 하세요.*

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: 고객 관리 키를 만들어 Azure Key Vault에 저장 해야 합니다.

- [Azure Data Box Azure Key Vault에서 고객이 관리 하는 키를 사용 하는 방법](data-box-customer-managed-encryption-key-portal.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: Microsoft는 Data Box 코드에서 자격 증명 스캐너를 실행 합니다. 또한 Microsoft는 자격 증명을 안전 하 게 보호 합니다. 자격 증명 스캐너를 구현 하 여 코드 내에서 자격 증명을 식별 합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다.

- [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [Azure 보안 벤치 마크: 맬웨어 방어](../security/benchmarks/security-control-malware-defense.md)를 참조 하세요.*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: Microsoft 맬웨어 방지 프로그램은 azure 서비스 (예: azure 고객 Lockbox)를 지 원하는 기본 호스트에서 사용 하도록 설정 되어 있지만 고객 콘텐츠에서 실행 되지 않습니다. 

비 계산 Azure 리소스에 업로드 되는 모든 콘텐츠를 미리 검색 하는 것은 사용자의 책임입니다. Microsoft는 고객 데이터에 액세스할 수 없으므로 사용자를 대신해 서 고객 콘텐츠의 맬웨어 방지 검색을 수행할 수 없습니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [Azure 보안 벤치 마크: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조 하세요.*

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업을 수행 하 고 고객 관리 키를 백업 합니다.

**지침**: 데이터 및 고객이 관리 하는 키를 백업 해야 합니다. Data Box는 백업을 수행 하지 않습니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 비롯 한 모든 백업 유효성 검사

**지침**: 온-프레미스에서 삭제 하기 전에 모든 데이터가 Azure Storage 계정에 있는지 확인 해야 합니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객이 관리 하는 키를 보호 해야 합니다.

**지침**: 사용 중인 모든 백업 또는 고객 관리 키가 모범 사례에 따라 보호 되는지 확인 합니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="incident-response"></a>사고 대응

자세한 내용은 [Azure Security Benchmark: 인시던트 응답](../security/benchmarks/security-control-incident-response.md)을 참조하세요.

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 작성합니다. 탐지에서 인시던트 사후 검토까지의 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할도 정의하는 인시던트 대응 계획이 작성되어 있는지 확인합니다. 

- [사용자 고유의 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용하여 고유한 인시던트 대응 계획 수립 지원](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center에서 심각도를 각 경고에 할당하여 먼저 조사해야 하는 경고에 대한 우선 순위를 지정합니다. 심각도는 경고를 실행 하는 데 사용 되는 찾기 또는 분석에 사용 되는 것과 경고를 발생 시킨 활동의 악의적인 의도를 받은 신뢰 수준에 Security Center 따라 달라 집니다. 

또한 태그를 사용하여 구독(예: 프로덕션, 비 프로덕션)을 명확하게 표시하고 Azure 리소스, 특히 중요한 데이터를 처리하는 리소스를 명확하게 식별하고 분류하는 명명 시스템을 만듭니다.  인시던트가 발생한 Azure 리소스 및 환경의 중요도에 따라 경고의 수정에 대한 우선 순위를 지정해야 합니다.

- [Azure Security Center의 보안 경고](../security-center/security-center-alerts-overview.md)

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: Azure 리소스를 보호하는 데 도움이 되도록 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 결함을 식별하고 필요에 따라 계획을 수정합니다.

- [NIST 게시물 - IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 가이드](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.

- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: Azure 리소스에 대한 위험을 식별하는 데 도움이 되도록 연속 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Azure Security Center 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다.

- [연속 내보내기를 구성하는 방법](../security-center/continuous-export.md)

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Azure Security Center의 Workflow Automation 기능을 사용 하 여 Azure 리소스를 보호 하기 위해 보안 경고 및 권장 사항에 대 한 "Logic Apps"를 통해 응답을 자동으로 트리거합니다.

- [워크플로 자동화 및 Logic Apps를 구성하는 방법](../security-center/workflow-automation.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [Azure 보안 벤치 마크: 침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조 하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대 한 정기적인 침투 테스트를 수행 하 고 모든 중요 한 보안 결과를 수정 하세요.

**지침**: Microsoft는 Data Box 장치에서 침투 테스트 및 취약성 검색을 수행 합니다. 사용자 고유의 침투 테스트 및 취약성 검사를 수행할 수 있습니다. 이 작업을 수행 하도록 선택 하는 경우 microsoft Engagement 규칙에 따라 침투 테스트가 Microsoft 정책을 위반 하지 않는지 확인 합니다. Microsoft에서 관리 하는 클라우드 인프라, 서비스 및 응용 프로그램에 대 한 레드 팀 및 라이브 사이트 침투 테스트의 전략과 실행을 사용 합니다.

- [침투 테스트 시행 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud 레드 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark V2 개요](/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
