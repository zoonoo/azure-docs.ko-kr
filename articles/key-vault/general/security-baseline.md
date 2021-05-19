---
title: Key Vault에 대한 Azure 보안 기준선
description: Key Vault 보안 기준은 Azure Security Benchmark에 지정된 보안 권장 사항을 구현하기 위한 절차 참고 자료와 리소스를 제공합니다.
author: msmbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 1f56de94df4fd5d4dd154ae8485edb9eed88364c
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753347"
---
# <a name="azure-security-baseline-for-key-vault"></a>Key Vault에 대한 Azure 보안 기준선

이 보안 기준은 [Azure Security Benchmark 버전 1.0](../../security/benchmarks/overview-v1.md)에서 Key Vault에 대한 지침을 적용합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다. 콘텐츠는 Azure Security Benchmark 및 Key Vault에 적용되는 관련 지침에서 정의된 **보안 컨트롤** 에 따라 그룹화됩니다. Key Vault에 적용되지 않거나 Microsoft의 책임인 **컨트롤** 은 제외되었습니다.

Key Vault가 완전히 Azure Security Benchmark에 매핑되는 방법을 보려면 [전체 Key Vault 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조하세요.

## <a name="network-security"></a>네트워크 보안

자세한 내용은 [Azure Security Benchmark: 네트워크 보안](../../security/benchmarks/security-control-network-security.md)을 참조하세요.

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**참고 자료**: Azure Private Link Service와 Azure Key Vault를 통합하는 방법을 알아봅니다. Azure Private Link 서비스를 사용하면 가상 네트워크의 프라이빗 엔드포인트를 통해 Azure Key Vault 등의 Azure Services 및 Azure 호스팅 고객/파트너 서비스에 액세스할 수 있습니다.

Azure 프라이빗 엔드포인트는 Azure Private Link에서 제공하는 서비스에 비공개로 안전하게 연결하는 네트워크 인터페이스입니다. 프라이빗 엔드포인트는 VNet의 개인 IP 주소를 사용하여 서비스를 VNet으로 효과적으로 가져옵니다. 서비스에 대한 모든 트래픽은 프라이빗 엔드포인트를 통해 라우팅할 수 있으므로 게이트웨이, NAT 디바이스, ExpressRoute 또는 VPN 연결 또는 공용 IP 주소가 필요하지 않습니다. 가상 네트워크와 서비스 간의 트래픽은 Microsoft 백본 네트워크를 통해 이동하여 공용 인터넷에서 노출을 제거합니다. Azure 리소스의 인스턴스에 연결하여 액세스 제어에서 가장 높은 수준의 세분성을 제공할 수 있습니다.

- [Azure Private Link와 Key Vault를 통합하는 방법](/azure/key-vault/private-link-service)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며, [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 컨트롤과 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 제어와 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.KeyVault**:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: 가상 네트워크, 서브넷 및 네트워크 인터페이스의 구성 및 트래픽을 모니터링 및 기록

**참고 자료**: Azure Security Center를 사용하고 네트워크 보호 권장 사항에 따라 Azure에서 Key Vault 설정된 리소스를 안전하게 보호합니다. 

- [Azure Security Center에서 제공하는 네트워크 보안에 대한 자세한 내용](../../security-center/security-center-network-recommendations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**참고 자료**: 분산 서비스 거부 공격(DDoS)으로부터 보호하기 위해 Azure Key Vault 인스턴스와 연결된 Azure Virtual Network에서 Azure DDoS Protection 표준을 사용하도록 설정합니다. Azure Security Center 통합 위협 인텔리전스를 사용하여 알려진 악성 인터넷 IP 주소 또는 사용하지 않는 인터넷 IP 주소와의 통신을 거부합니다.

 
- [Azure Portal을 사용하여 Azure DDoS Protection 표준 관리](/azure/virtual-network/manage-ddos-protection)

- [Azure Security Center에서 Azure 서비스 계층에 대한 위협 감지](/azure/security-center/security-center-alerts-service-layer)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 IDS/IPS(침입 탐지/침입 방지 시스템) 배포

**참고 자료**: Azure Key Vault에 대해 고급 위협 보호 (ATP)를 구성하여 이 요구 사항을 충족할 수 있습니다. ATP는 보안 인텔리전스의 추가 계층을 제공합니다. 이는 Azure Key Vault 계정에 액세스하거나 이를 악용하려는 비정상적이고 잠재적으로 유해한 시도를 탐지합니다.

Azure Security Center는 비정상적인 활동을 검색할 때 경고를 표시합니다. 또한 구독 관리자에게 의심스러운 활동에 대한 세부 정보 및 식별된 위협을 조사하고 수정하는 방법에 대한 권장 사항을 이메일로 보냅니다.

- [Azure Key Vault에 대한 고급 위협 방지 설정](/azure/security-center/advanced-threat-protection-key-vault)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**참고 자료**: Azure Key Vault 인스턴스에 액세스해야 하는 리소스의 경우 Azure 서비스 태그를 사용하여 네트워크 보안 그룹 또는 Azure Firewall에서 네트워크 액세스 제어를 정의합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 서비스 태그 이름(예: ApiManagement)을 규칙의 적절한 원본 또는 대상 필드에 지정하면 해당 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다. Microsoft에서는 서비스 태그에서 압축한 주소 접두사를 관리하고 주소를 변경하는 대로 서비스 태그를 자동으로 업데이트합니다.

- [Azure 서비스 태그 개요](../../virtual-network/service-tags-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지

**참고 자료**: Azure Policy를 사용하여 Azure Key Vault 인스턴스와 연결된 네트워크 리소스에 대한 표준 보안 구성을 정의하고 구현합니다. "Microsoft.KeyVault" 및 "Microsoft.Network" 네임스페이스에서 Azure Policy 별칭을 사용하여 Azure Key Vault 인스턴스의 네트워크 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다. Azure Key Vault와 관련된 기본 제공 정책 정의도 다음과 같이 사용할 수 있습니다.
- Key Vault는 가상 네트워크 서비스 엔드포인트를 사용해야 함

자세한 내용은 다음 참조 문서를 참조하세요.

- [규정 준수를 적용하는 정책 만들기 및 관리](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy 샘플](/azure/governance/policy/samples)

- [포털에서 청사진 정의 및 할당](../../governance/blueprints/create-blueprint-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙

**참고 자료**: Azure Key Vault 인스턴스에 대한 네트워크 보안 및 트래픽 흐름과 관련된 리소스를 위해 태그를 사용하여 메타데이터 및 논리적 조직을 제공합니다.

태그 지정과 관련된 기본 제공 Azure Policy 정의(예: "태그 및 해당 값 필요")를 사용하여 모든 리소스에서 태그를 포함하도록 만들고 태그가 없는 리소스를 알립니다.

Azure PowerShell 또는 Azure CLI를 사용하여 태그를 기준으로 리소스에 대한 작업을 조회하거나 수행할 수 있습니다.

- [태그를 사용하여 Azure 리소스 구성](/azure/azure-resource-manager/resource-group-using-tags)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**참고 자료**: Azure 활동 로그를 사용하여 네트워크 리소스 구성을 모니터링하고 Azure Key Vault 인스턴스 관련 네트워크 리소스에 대한 변경 내용을 검색합니다. 중요한 네트워크 리소스에 변경 내용이 발생하는 경우 트리거하는 Azure Monitor 내에서 경고를 만듭니다.

- [Azure 활동 로그 이벤트를 보고 검색하기](/azure/azure-monitor/platform/activity-log-view)

- [Azure Monitor를 사용하여 활동 로그 경고 만들기, 보기 및 관리하기](/azure/azure-monitor/platform/alerts-activity-log)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure Security Benchmark: 로깅 및 모니터링](../../security/benchmarks/security-control-logging-monitoring.md)을 참조하세요.*

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**참고 자료**: Azure Monitor를 통해 로그를 수집하여 Azure Key Vault에서 생성된 보안 데이터를 수집합니다. Azure Monitor 내에서 Log Analytics 작업 영역을 사용하여 쿼리 및 분석을 수행하고, Azure Storage 계정을 장기/보관 스토리지에 사용할 수 있습니다. 또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다. 

- [Azure Key Vault 로깅](/azure/key-vault/key-vault-logging)

- [빠른 시작: Azure Sentinel을 온보딩하는 방법](../../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**참고 자료**: 감사, 보안 및 진단 로그에 액세스하기 위해 Azure Key Vault 인스턴스에서 진단 설정을 사용하도록 설정합니다. 자동으로 사용할 수 있는 활동 로그에는 이벤트 원본, 날짜, 사용자, 타임스탬프, 원본 주소, 대상 주소 및 기타 유용한 요소가 포함됩니다.

- [Azure Key Vault 로깅](/azure/key-vault/key-vault-logging)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며, [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 컨트롤과 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 제어와 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.KeyVault**:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 2.3](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

**참고 자료**: Azure Monitor 내에서 조직의 준수 규정에 따라 Azure Key Vault 로그를 보관하는 데 사용되는 Log Analytics 작업 영역에 대한 보존 기간을 설정합니다. 장기/보관 스토리지에 Azure Storage 계정을 사용합니다.

- [데이터 보존 기간 변경](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**참고 자료**: 비정상적인 동작에 대한 로그를 분석 및 모니터링하고 Azure Key Vault protected 리소스에 대한 결과를 정기적으로 검토합니다. Azure Monitor의 Log Analytics 작업 영역을 사용하여 로그를 검토하고 로그 데이터에 대한 쿼리를 수행합니다. 또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다. 

- [Azure Sentinel 온보딩](../../sentinel/quickstart-onboard.md)

- [Azure Monitor에서 Log Analytics 시작](/azure/azure-monitor/log-query/get-started-portal)

- [Azure Monitor에서 로그 쿼리 시작](/azure/azure-monitor/log-query/get-started-queries)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상 활동에 대한 경고 사용

**참고 자료**: Azure Security Center에서 Key Vault에 대해 ATP (advanced threat protection)를 사용하도록 설정합니다. Azure Key Vault에 대한 진단 설정을 사용하도록 설정하고 Log Analytics 작업 영역으로 로그를 보냅니다. Log Analytics 작업 영역을 Azure Sentinel에 등록하여 SOAR(보안 오케스트레이션 자동화 응답) 솔루션을 제공합니다. 이를 통해 플레이북(자동화된 솔루션)을 만들어 보안 문제를 수정하는 데 사용할 수 있습니다.

- [빠른 시작: Azure Sentinel 온보딩](../../sentinel/quickstart-onboard.md)

- [Azure Security Center에서 보안 경고 관리 및 응답](../../security-center/security-center-managing-and-responding-alerts.md)

- [Azure Monitor 경고로 이벤트에 응답](/azure/azure-monitor/learn/tutorial-response)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [Azure Security Benchmark: ID 및 액세스 제어](../../security/benchmarks/security-control-identity-access-control.md)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정 인벤토리 유지 관리

**참고 자료**: Azure Key Vault 키, 암호 및 인증서에 대한 액세스 권한이 있는 사용자 계정뿐만 아니라 Azure Active Directory(Azure AD)에 등록된 애플리케이션의 인벤토리를 유지 관리합니다. Azure Portal 또는 PowerShell을 사용하여 Key Vault 액세스를 쿼리하고 조정할 수 있습니다. PowerShell에서 액세스를 보려면 다음 명령을 사용합니다.

(Get-AzResource -ResourceId [KeyVaultResourceID]).Properties.AccessPolicies

- [Azure AD로 애플리케이션 등록](/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory)

- [Key vault에 대한 액세스 보안](security-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**참고 자료**: Azure Key Vault 인스턴스에 액세스할 수 있는 전용 관리 계정의 사용에 대한 표준 운영 프로시저를 만듭니다. Azure Security Center ID와 액세스 관리(현재 프리뷰 내에)를 사용하여 관리 계정 수를 모니터링합니다.

- [ID 및 액세스 모니터링(프리뷰)](../../security-center/security-center-identity-access.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory Single Sign-On(SSO) 사용

**참고 자료**: AppId, TenantID 및 ClientSecret과 함께 Azure 서비스 주체를 사용하여 애플리케이션을 원활하게 인증하고, Azure Key Vault 암호에 액세스하는 데 사용되는 토큰을 검색합니다.

- [.NET을 사용하여 Azure Key Vault로 서비스 간 인증](/azure/key-vault/service-to-service-authentication)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**참고 자료**: Azure Active Directory(Azure AD) 다단계 인증을 사용하도록 설정하고, Azure Security Center ID 및 액세스 관리(현재 프리뷰 내에) 권장 사항에 따라 Event Hub 사용 리소스를 보호합니다.

- [Azure AD 다단계 인증 배포 계획](../../active-directory/authentication/howto-mfa-getstarted.md)

- [ID 및 액세스 모니터링(프리뷰)](../../security-center/security-center-identity-access.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: 관리 작업에 안전한 Azure 관리 워크스테이션 사용

**참고 자료**: Key Vault 사용 리소스에 로그인하고 구성하도록 설정된 다단계 인증이 있는 Privileged Access Workstation(PAW)를 사용합니다.

- [권한 있는 액세스 워크스테이션](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [클라우드 기반 Azure AD 다단계 인증 배포 계획](../../active-directory/authentication/howto-mfa-getstarted.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정의 의심스러운 활동에 대한 로그 및 경고

**참고 자료**: 환경에서 의심스럽거나 안전하지 않은 활동이 발생하는 경우 로그와 경고를 생성하기 위해 Azure Active Directory(Azure AD) Privileged Identity Management (PIM)를 사용합니다. Azure AD 위험 검색을 사용하여 위험한 사용자 동작에 대한 경고 및 보고서를 봅니다. 추가 로깅을 위해 Azure Security Center 위험 검색 경고를 Azure Monitor에 보내고, 작업 그룹을 사용하여 사용자 지정 경고/알림을 구성합니다.

Azure Key Vault에 대한 Advanced Threat Protection (ATP)을 사용하여 의심스러운 활동에 대한 경고를 생성합니다.

- [Azure AD PIM(Privileged Identity Management) 배포](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure Key Vault(프리뷰)에 대한 Advanced Threat Protection 설정](/azure/security-center/advanced-threat-protection-key-vault)

- [Azure Key Vault에 대한 경고(프리뷰)](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv)

- [Azure AD 위험 검색](/azure/active-directory/reports-monitoring/concept-risk-events)

- [Azure Portal에서 작업 그룹 만들기 및 관리](/azure/azure-monitor/platform/action-groups)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**참고 자료**: 조건부 액세스 정책의 위치 조건을 구성하고 명명된 위치를 관리합니다. 명명된 위치를 사용하면 IP 주소 범위 또는 지역의 논리적 그룹을 만들 수 있습니다. Azure Key Vault 비밀과 같은 중요한 리소스에 대한 액세스를 구성된 명명된 위치로 제한합니다.

- [Azure Active Directory(Azure AD) 조건부 액세스에서 위치 조건이란?](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**참고 자료**: Azure Active Directory(Azure AD)를 Key Vault와 같은 Azure 리소스에 대한 중앙 인증 및 권한 부여 시스템으로 사용합니다. 이를 통해 관리가 중요한 리소스에 대한 Azure 역할 기반 액세스 제어 (Azure RBAC)를 사용할 수 있습니다.

- [Azure AD에서 새 테넌트 만들기](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**참고 자료**: Azure Active Directory(Azure AD) 로그를 검토하여 Azure Key Vault 관리 역할이 있는 계정을 비롯한 부실 계정을 검색할 수 있습니다. 또한 Azure AD 액세스 검토를 사용하여 그룹 멤버 자격, Azure Key Vault에 액세스하는 데 사용할 수 있는 엔터프라이즈 애플리케이션에 대한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 알맞은 사용자만 지속적으로 액세스할 수 있도록 사용자 액세스를 90일마다 정기적으로 검토해야 합니다.

- [Azure AD 보고서 및 모니터링 설명서](/azure/active-directory/reports-monitoring/)

- [Azure AD 액세스 검토란?](../../active-directory/governance/access-reviews-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화된 자격 증명에 대한 액세스 시도 모니터링

**참고 자료**: Azure Key Vault 및 Azure Active Directory(Azure AD)에 대한 진단 설정을 사용하도록 설정하여 모든 로그를 Log Analytics 작업 영역으로 보냅니다. Log Analytics 내에서 원하는 경고(예: 사용할 수 없는 비밀에 대한 액세스 시도)를 구성합니다.

- [Azure Monitor 로그를 Azure AD 로그와 통합](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [이전 Key Vault 솔루션에서 마이그레이션](/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**참고 자료**: Azure Active Directory(Azure AD)의 ID 보호 및 위험 탐지 기능을 사용하여 Azure Key Vault 보호 리소스와 관련하여 탐지된 의심스러운 작업에 대한 자동화된 응답을 구성합니다. 조직의 보안 대응을 구현하기 위해 Azure Sentinel을 통한 자동화된 대응을 사용하도록 설정해야 합니다.

- [Azure AD 포털의 위험한 로그인 보고서](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [방법: 위험 정책 구성 및 사용](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel을 온보딩하는 방법](../../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-protection"></a>데이터 보호

자세한 내용은 [Azure Security Benchmark: 데이터 보호](../../security/benchmarks/security-control-data-protection.md)를 참조하세요.

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**참고 자료**: 태그를 사용하여 Azure Key Vault 사용 리소스에 중요한 정보를 저장하거나 처리하는 Azure 리소스를 추적할 수 있도록 지원합니다. 

- [태그를 사용하여 Azure 리소스 구성](/azure/azure-resource-manager/resource-group-using-tags)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**참고 자료**: 특정 서브넷에 대한 액세스를 제한하도록 구성된 가상 네트워크 서비스 엔드포인트를 사용하여 Azure Key Vault에 대한 액세스를 보호할 수 있습니다.

방화벽 규칙이 적용되면, 사용자는 요청이 허용되는 가상 네트워크 또는 IPv4 주소 범위에서 시작되는 경우에만 Azure Key Vault 데이터 평면 작업을 수행할 수 있습니다. Azure Portal에서 Azure Key Vault에 액세스하는 경우도 마찬가지입니다. 사용자가 Azure Portal에서 키 자격 증명 모음으로 찾아볼 수 있다고 해도, 해당 클라이언트 머신이 허용 목록에 없는 경우 키, 비밀 또는 인증서를 나열하지 못할 수 있습니다. 다른 Azure 서비스의 Azure Key Vault 선택기도 마찬가지입니다. 방화벽 규칙이 해당 사용자의 클라이언트 컴퓨터를 금지하는 경우, 사용자는 Key Vault 목록을 볼 수 있지만 키의 목록은 그러지 못합니다.

- [Azure Key Vault 방화벽 및 가상 네트워크 구성](/azure/key-vault/key-vault-network-security)

- [Azure Key Vault의 가상 네트워크 서비스 엔드포인트](/azure/key-vault/key-vault-overview-vnet-service-endpoints)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**참고 자료**: Azure Key Vault 내에 저장된 모든 데이터는 중요한 것으로 간주됩니다. Azure Key Vault 데이터 평면 액세스 제어를 사용하여 Azure Key Vault 암호에 대한 액세스를 제어합니다. Key Vault의 기본 제공 방화벽을 사용하여 네트워크 계층에서 액세스를 제어할 수도 있습니다. Azure Key Vault에 대한 액세스를 모니터링하려면, Key Vault 진단 설정을 사용하도록 설정하고 Azure Storage 계정 또는 Log Analytics 작업 영역으로 로그를 보냅니다.

- [Key vault에 대한 액세스 보안](security-overview.md)

- [Azure Key Vault 방화벽 및 가상 네트워크 구성](/azure/key-vault/key-vault-network-security)

- [Azure Key Vault 로깅](/azure/key-vault/key-vault-logging)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스 관리

**참고 자료**: Azure Key Vault 인스턴스의 관리 및 데이터 평면에 안전하게 액세스할 수 있습니다.

- [Key vault에 대한 액세스 보안](security-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 기록 및 경고

**참고 자료**: Azure Monitor에서 Azure Key Vault 분석 솔루션을 사용하여 Azure Key Vault 감사 이벤트 로그를 검토할 수 있습니다.

- [Azure Monitor의 Azure Key Vault 분석 솔루션](/azure/azure-monitor/insights/azure-key-vault)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="vulnerability-management"></a>취약성 관리

*자세한 내용은 [Azure Security Benchmark: 취약성 관리](../../security/benchmarks/security-control-vulnerability-management.md)를 참조하세요.*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

**참고 자료**: Azure Security Center에서 제공하는 기본 위험 등급(보안 점수)을 사용합니다.

- [Azure Security Center의 보안 점수 개선](/azure/security-center/security-center-secure-score)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure Security Benchmark: 인벤토리 및 자산 관리](../../security/benchmarks/security-control-inventory-asset-management.md)를 참조하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화된 자산 검색 솔루션 사용

**참고 자료**: Azure Resource Graph를 사용하여 구독 내의 모든 리소스(Azure Key Vault 인스턴스 포함)를 쿼리하고 발견합니다. 테넌트에서 적절한 권한(읽기)이 있는지 확인하고, 모든 Azure 구독 및 구독 내의 리소스를 열거할 수 있습니다.

- [Azure Resource Graph Explorer를 사용하여 첫 번째 Resource Graph 쿼리 실행](../../governance/resource-graph/first-query-portal.md)

- [현재 계정에서 액세스할 수 있는 구독을 가져옵니다](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [RBAC(역할 기반 액세스 제어)](../../role-based-access-control/overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**참고 자료**: 메타데이터를 제공하는 Azure Key Vault 리소스에 태그를 적용하여 분류 체계로 구성합니다.

- [태그를 만들고 사용하는 방법](/azure/azure-resource-manager/resource-group-using-tags)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**참고 자료**: 태그 지정, 관리 그룹 그리고 해당하는 경우에는 별도의 구독을 사용하여 Azure Key Vault 인스턴스 및 관련 리소스를 구성하고 추적합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

- [추가 Azure 구독 만들기](/azure/billing/billing-create-subscription)

- [리소스 조직 및 관리를 위한 관리 그룹 만들기](/azure/governance/management-groups/create)

- [태그를 사용하여 Azure 리소스 구성](/azure/azure-resource-manager/resource-group-using-tags)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**참고 자료**: Azure Policy를 사용하여 다음 기본 제공 정책 정의를 사용한 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

또한 Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리/검색합니다.

- [자습서: 규정 준수를 적용하는 정책 만들기 및 관리](../../governance/policy/tutorials/create-and-manage.md)

- [빠른 시작: Azure Resource Graph Explorer를 사용하여 첫 번째 Resource Graph 쿼리 실행](../../governance/resource-graph/first-query-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**참고 자료**: Azure Policy를 사용하여 다음 기본 제공 정책 정의를 사용한 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

자세한 내용은 다음 참조 문서를 참조하세요.

- [자습서: 규정 준수를 적용하는 정책 만들기 및 관리](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy 샘플](/azure/governance/policy/samples/built-in-policies#general)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Azure Resource Manager와 상호 작용하는 사용자 기능 제한

**지침**: "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성하여 사용자가 Azure Resource Manager와 상호 작용하는 기능을 제한하려면 Azure 조건부 액세스를 사용합니다. 이렇게 하면 Key Vault 구성된 것과 같은 높은 보안 환경 내의 리소스 생성 및 변경을 방지할 수 있습니다.

- [조건부 액세스로 Azure 관리에 대한 액세스 관리](../../role-based-access-control/conditional-access-azure-management.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure Security Benchmark: 보안 구성](../../security/benchmarks/security-control-secure-configuration.md)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**참고 자료**: "Microsoft.KeyVault" 네임스페이스에서 Azure Policy 별칭을 사용하여 Azure Key Vault 인스턴스에 대한 구성을 감사하거나 적용하기 위한 사용자 지정 정책을 만듭니다. Azure Key Vault에 다음과 같이 기본 제공 Azure Policy 정의를 사용할 수도 있습니다.

- Key Vault 개체를 복구할 수 있어야 합니다.

- Key Vault의 진단 설정을 Log Analytics 작업 영역에 배포

- Key Vault의 진단 로그를 사용하도록 설정해야 합니다.

- Key Vault는 가상 네트워크 서비스 엔드포인트를 사용해야 함

- Key Vault의 진단 설정을 Event Hub에 배포

- Azure Security Center의 권장 사항을 Azure Key Vault 인스턴스에 대한 보안 구성 기준으로 사용할 수도 있습니다

자세한 내용은 다음 참조 문서를 참조하세요.

- [사용 가능한 Azure Policy 별칭 확인 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [자습서: 규정 준수를 적용하는 정책 만들기 및 관리](../../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**참고 자료**: Azure 정책 [거부] 및 [존재하지 않으면 배포]를 사용하여 보안 설정을 Azure Key Vault 사용 리소스 전체에 적용합니다. 

- [규정 준수를 적용하는 정책 만들기 및 관리](../../governance/policy/tutorials/create-and-manage.md)

  
- [Azure Policy 효과 이해](../../governance/policy/concepts/effects.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**참고 자료**: Azure Key Vault 또는 사용 리소스에 사용자 지정 Azure Policy 정의를 사용하는 경우, Azure Repos를 사용하여 코드를 안전하게 저장하고 관리합니다.

- [Azure DevOps에 코드를 저장하는 방법](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Azure Repos 설명서](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스를 위한 구성 관리 도구 배포

**참고 자료**: "Microsoft.KeyVault" 네임스페이스에서 Azure Policy 별칭을 사용하여 시스템 구성을 경고, 감사 및 적용하는 사용자 지정 정책을 만듭니다. 또한 정책 예외를 관리하는 프로세스와 파이프라인을 개발합니다.

- [Azure Policy를 구성하고 관리하는 방법](../../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대한 자동화된 구성 모니터링 구현

**참고 자료**: Azure Security Center를 사용하여 Key Vault 보호 리소스의 기준 검사를 실행합니다.

- [Azure Security Center에서 권장 사항을 수정하는 방법](../../security-center/security-center-remediate-recommendations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**참고 자료**: Azure Key Vault와 함께 관리 서비스 ID를 사용하여 클라우드 애플리케이션에 대한 비밀 관리를 간소화하고 보호합니다. Azure Key Vault 일시 삭제를 사용하도록 설정되어 있는지 확인합니다.

- [Azure 관리 ID와 통합](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Key Vault 만들기](quick-create-portal.md)

- [Key Vault에 인증](authentication.md)

- [Key Vault 액세스 정책 할당](assign-access-policy-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며, [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 컨트롤과 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 제어와 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.KeyVault**:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 7.11](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-7-11.md)]

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**참고 자료**: Azure Key Vault와 함께 관리 서비스 ID를 사용하여 클라우드 애플리케이션에 대한 비밀 관리를 간소화하고 보호합니다.

  

- [Azure 관리 ID와 통합하는 방법](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [Key Vault를 만드는 방법](quick-create-portal.md)    

- [Key Vault에 인증하는 방법](authentication.md)

- [Key Vault 액세스 정책 할당](assign-access-policy-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다.  
  
- [ 자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [Azure Security Benchmark: 맬웨어 방어](../../security/benchmarks/security-control-malware-defense.md)를 참조하세요.*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**참고 자료**: Microsoft 맬웨어 방지는 Azure 서비스(예: Azure Key Vault)를 지원하는 기본 호스트에서 사용하도록 설정되어 있지만, 고객 콘텐츠에서 실행되지 않습니다.

Azure Key Vault와 같은 비 컴퓨팅 Azure 리소스로 업로드되거나 전송되는 콘텐츠를 미리 검색합니다. Microsoft는 이러한 인스턴스의 데이터에 액세스할 수 없습니다.

- [Azure Cloud Services 및 Virtual Machines용 Microsoft Antimalware 이해](../../security/fundamentals/antimalware.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [Azure Security Benchmark: 데이터 복구](../../security/benchmarks/security-control-data-recovery.md)를 참조하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 자동화된 정기 백업 보장

**참고 자료**: 다음 PowerShell 명령을 사용하여 정기적으로 Key Vault 인증서, 키, 관리 스토리지 계정 및 비밀의 데이터 백업을 수행합니다.

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Restore-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

필요에 따라 Azure Backup 내에 Key Vault 백업을 저장할 수 있습니다.

- [Key Vault 인증서를 백업하는 방법](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Key Vault 키를 백업하는 방법](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Key Vault 관리 스토리지 계정을 백업하는 방법](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Key Vault 비밀을 백업하는 방법](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

- [Azure Backup을 사용하는 방법](/azure/backup)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업 수행 및 고객 관리형 키 백업

**참고 자료**: 다음 PowerShell 명령을 사용하여 정기적으로 Key Vault 인증서, 키, 관리 스토리지 계정 및 비밀의 데이터 백업을 수행합니다.

- Backup-AzKeyVaultCertificate

- Backup-AzKeyVaultKey

- Restore-AzKeyVaultManagedStorageAccount

- Backup-AzKeyVaultSecret

필요에 따라 Azure Backup 내에 Key Vault 백업을 저장할 수 있습니다.

- [Key Vault 인증서를 백업하는 방법](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Key Vault 키를 백업하는 방법](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Key Vault 관리 스토리지 계정을 백업하는 방법](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount)

- [Key Vault 비밀을 백업하는 방법](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

- [Azure Backup을 사용하는 방법](/azure/backup)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리형 키를 포함한 모든 백업의 유효성 검사

**지침**: 다음 PowerShell 명령을 사용하여 정기적으로 Key Vault 인증서, 키, 관리 스토리지 계정 및 비밀의 데이터 복원을 수행합니다.

- Restore-AzKeyVaultCertificate

- Restore-AzKeyVaultKey

- Restore-AzKeyVaultManagedStorageAccount

- Restore-AzKeyVaultSecret

자세한 내용은 다음 참조 문서를 참조하세요.

- [Key Vault 인증서를 복원하는 방법](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultcertificate?view=azps-5.3.0&amp;preserve-view=true)

- [Key Vault 키를 복원하는 방법](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-5.3.0&amp;preserve-view=true)

- [Key Vault 관리 스토리지 계정을 복원하는 방법](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Key Vault 비밀을 복원하는 방법](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultsecret?view=azps-5.3.0&amp;preserve-view=true)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리형 키 보호 보장

**참고 자료**: 일시 삭제가 Azure Key Vault에 사용되도록 설정되어 있는지 확인합니다. 일시 삭제를 사용하면 삭제된 Key Vault 및 자격 증명 모음 개체(예: 키, 비밀 및 인증서)를 복구할 수 있습니다. 

- [Azure Key Vault의 일시 삭제를 사용하는 방법](/azure/key-vault/key-vault-soft-delete-powershell)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며, [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 컨트롤과 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 제어와 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.KeyVault**:

[!INCLUDE [Resource Policy for Microsoft.KeyVault 9.4](../../../includes/policy/standards/asb/rp-controls/microsoft.keyvault-9-4.md)]

## <a name="incident-response"></a>사고 대응

자세한 내용은 [Azure Security Benchmark: 인시던트 응답](../../security/benchmarks/security-control-incident-response.md)을 참조하세요.

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 작성합니다. 검색에서 사후 검토에 이르는 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할을 정의하는 인시던트 대응 계획이 있는지 확인합니다. 이러한 프로세스는 Key Vault 암호를 사용하는 것과 같은 중요한 시스템을 보호하는 데 중점을 두어야 합니다.

- [Azure Security Center 내에서 워크플로 자동화를 구성하는 방법](../../security-center/security-center-planning-and-operations-guide.md)   

- [자체 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)   

- [고객이 NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용하여 사용자 고유의 인시던트 대응 계획수립을 지원할 수도 있습니다](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center에서 심각도를 각 경고에 할당하여 먼저 조사해야 하는 경고에 대한 우선 순위를 지정합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용되는 결과 또는 메트릭의 신뢰도 및 경고가 발생한 활동 배후에 악의적인 의도가 있었다는 신뢰 수준을 기준으로 합니다. 또한 구독(예: 프로덕션, 비 프로덕션)을 명확하게 표시하고, Azure 리소스, 특히 Azure Key Vault 암호와 같은 중요한 데이터를 처리하는 리소스를 명확하게 식별하고 분류하는 명명 시스템을 만듭니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**참고 자료**: Azure Key Vault 인스턴스 및 관련 리소스를 보호하는 데 도움이 되도록 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 격차를 식별하고 필요에 따라 계획을 수정합니다.

- [NIST의 게시물을 참조하세요. IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 안내](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다.  문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.

- [Azure Security Center 보안 연락처를 설정하는 방법](../../security-center/security-center-provide-security-contact-details.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**참고 자료**: Azure Key Vault 사용 리소스에 대한 위험을 식별하는 데 도움이 되도록 연속 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다.  Azure Security Center 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다. 

 

- [연속 내보내기를 구성하는 방법](../../security-center/continuous-export.md) 

  

- [경고를 Azure Sentinel로 스트림하는 방법](../../sentinel/connect-azure-security-center.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**참고 자료**: Azure Security Center의 워크플로 자동화 기능을 사용하여 보안 경고 및 권장 사항에 관한 "Logic Apps"를 통해 응답을 자동으로 트리거함으로써 Azure Key Vault-보호 리소스를 보호합니다. 

 

- [워크플로 자동화와 Logic Apps를 구성하는 방법](../../security-center/workflow-automation.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [Azure Security Benchmark: 침투 테스트 및 레드 팀 연습](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대한 침투 테스트를 정기적으로 수행 및 모든 중요한 보안 결과를 수정

**참고 자료**: Microsoft 클라우드 침투 테스트 참여 규칙을 따라서 침투 테스트가 Microsoft 정책을 위반하지 않도록 하십시오. Microsoft의 전략과 Microsoft에서 관리하는 클라우드 인프라, 서비스, 애플리케이션에 대한 레드 팀 실행 및 실시간 사이트 침투 테스트를 사용합니다.

- [침투 테스트 시행 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud 레드 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark V2 개요](/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
