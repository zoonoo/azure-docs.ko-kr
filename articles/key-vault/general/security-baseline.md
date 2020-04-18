---
title: 키 볼트에 대한 Azure 보안 기준
description: 키 볼트에 대한 Azure 보안 기준
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 46fa0160dd8b37e89cdd77ba8acdae294fddbefe
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616903"
---
# <a name="azure-security-baseline-for-key-vault"></a>키 볼트에 대한 Azure 보안 기준

키 볼트에 대한 Azure 보안 기준에는 배포의 보안 상태를 개선하는 데 도움이 되는 권장 사항이 포함되어 있습니다.

이 서비스의 기준은 Azure [Security 벤치마크 버전 1.0에서](https://docs.microsoft.com/azure/security/benchmarks/overview)가져온 것으로, 모범 사례 지침을 통해 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다.

자세한 내용은 [Azure 보안 기준 개요를](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)참조하십시오.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [보안 제어: 네트워크 보안](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)을 참조하십시오.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: 가상 네트워크에서 네트워크 보안 그룹 또는 Azure 방화벽을 사용하여 리소스 보호

지침 : Azure 키 자격 증명 모음을 Azure 개인 링크와 **통합합니다.** 

Azure 개인 링크 서비스를 사용하면 가상 네트워크의 전용 엔드포인트를 통해 Azure 서비스(예: Azure Key Vault) 및 Azure 호스팅 고객/파트너 서비스에 액세스할 수 있습니다.

Azure 프라이빗 엔드포인트는 Azure Private Link에서 제공하는 서비스에 비공개로 안전하게 연결하는 네트워크 인터페이스입니다. 프라이빗 엔드포인트는 VNet의 개인 IP 주소를 사용하여 서비스를 VNet으로 효과적으로 가져옵니다. 서비스에 대한 모든 트래픽은 프라이빗 엔드포인트를 통해 라우팅할 수 있으므로 게이트웨이, NAT 디바이스, ExpressRoute 또는 VPN 연결 또는 공용 IP 주소가 필요하지 않습니다. 가상 네트워크와 서비스 간의 트래픽은 Microsoft 백본 네트워크를 통해 이동하여 공용 인터넷에서 노출을 제거합니다. Azure 리소스의 인스턴스에 연결하여 액세스 제어에서 가장 높은 수준의 세분성을 제공할 수 있습니다.

키 볼트를 Azure 개인 링크와 통합하는 방법:

https://docs.microsoft.com/azure/key-vault/private-link-service


**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Vnet, 서브넷 및 NIC의 구성 및 트래픽을 모니터링하고 기록합니다.

**지침**: Azure 보안 센터를 사용하고 네트워크 보호 권장 사항을 따라 Azure에서 키 볼트로 구성된 리소스를 보호합니다. 

Azure 보안 센터에서 제공하는 네트워크 보안에 대한 자세한 내용은 다음을 참조하십시오. 

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침**: 해당되지 않음; 이 권장 사항은 Azure 앱 서비스에서 실행중인 웹 응용 프로그램 또는 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: 분산 서비스 거부 공격로부터 보호하기 위해 키 볼트 인스턴스와 연결된 Azure 가상 네트워크에서 Azure DDoS 보호 표준을 활성화합니다. Azure 보안 센터 통합 위협 인텔리전스를 사용하여 알려진 악성 또는 사용되지 않는 인터넷 IP 주소와의 통신을 거부합니다.

 
Azure 포털을 사용하여 Azure DDoS 보호 표준을 관리합니다.https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Azure 보안 센터의 Azure 서비스 계층에 대한 위협 검색:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: 네트워크 패킷 및 흐름 로그 기록

**지침**: Azure 키 볼트는 NSG(네트워크 보안 그룹)를 사용하지 않으며 Azure 키 자격 증명 모음에 대한 흐름 로그는 캡처되지 않습니다. 대신 Azure 개인 링크를 사용하여 Azure Key Vault 인스턴스를 보호하고 진단 설정을 사용하여 메트릭 및 감사 이벤트를 기록합니다.

키 볼트를 Azure 개인 링크와 통합합니다.

https://docs.microsoft.com/azure/key-vault/private-link-service

Azure 키 볼트 로깅:https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 침입 감지/침입 방지 시스템(IDS/IPS) 배포

**지침**: Azure 키 자격 증명 모음에 대한 고급 위협 보호(ATP)를 구성하여 이 요구 사항을 충족할 수 있습니다. ATP는 추가 보안 인텔리전스 계층을 제공합니다. 이 도구는 Azure Key Vault 계정에 액세스하거나 악용하려는 잠재적으로 유해한 시도를 감지합니다.

Azure Security Center에서 비정상적인 활동을 감지하면 경고가 표시됩니다. 또한 의심스러운 활동에 대한 세부 정보와 확인된 위협을 조사하고 해결하는 방법에 대한 권장 사항을 구독 관리자에게 이메일로 보냅니다.

Azure 키 자격 증명 모음에 대한 고급 위협 보호 설정:

https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault



**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션으로의 트래픽 관리

**지침**: 해당되지 않음; 이 권장 사항은 Azure 앱 서비스에서 실행중인 웹 응용 프로그램 또는 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: Azure Key Vault 인스턴스에 액세스해야 하는 리소스의 경우 Azure Key Vault에 대한 Azure 서비스 태그를 사용하여 네트워크 보안 그룹 또는 Azure 방화벽에서 네트워크 액세스 제어를 정의합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 규칙의 적절한 소스 또는 대상 필드에 서비스 태그 이름(예: ApiManagement)을 지정하면 해당 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다. Microsoft는 서비스 태그로 둘러싸인 주소 접두사를 관리하고 주소가 변경될 때 서비스 태그를 자동으로 업데이트합니다.

Azure 서비스 태그 개요:https://docs.microsoft.com/azure/virtual-network/service-tags-overview


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 장치에 대한 표준 보안 구성 유지

**지침**: Azure 키 볼트 인스턴스와 연결된 네트워크 리소스에 대한 표준 보안 구성을 정의하고 구현합니다. "Microsoft.KeyVault" 및 "Microsoft.Network" 네임스페이스에서 Azure 정책 별칭을 사용하여 Azure Key Vault 인스턴스의 네트워크 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다. 다음과 같은 Azure Key Vault와 관련된 기본 제공 정책 정의를 사용할 수도 있습니다.

Key Vault는 가상 네트워크 서비스 엔드포인트를 사용해야 함

자습서: 규정 준수를 적용하기 위한 정책을 만들고 관리합니다.

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure 정책 샘플:

https://docs.microsoft.com/azure/governance/policy/samples/#networ

빠른 시작: 포털에서 청사진을 정의하고 할당합니다.

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙

**지침**: Azure Key Vault 인스턴스에 대한 네트워크 보안 및 트래픽 흐름과 관련된 리소스에 태그를 사용하여 메타데이터 및 논리 조직을 제공합니다.

"태그 및 해당 값 필요"와 같이 태그 지정과 관련된 기본 제공 Azure 정책 정의를 사용하여 모든 리소스가 태그로 만들어지도록 하고 기존 태그가 지정되지 않은 리소스를 알려줍니다.

Azure PowerShell 또는 Azure CLI를 사용하여 태그를 기반으로 리소스에 대한 작업을 조회하거나 수행할 수 있습니다.

태그를 사용하여 Azure 리소스를 구성합니다.

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 을 모니터링하고 변경 사항을 감지합니다.

**지침**: Azure 활동 로그를 사용하여 네트워크 리소스 구성을 모니터링하고 Azure Key Vault 인스턴스와 관련된 네트워크 리소스의 변경 내용을 검색합니다. Azure Monitor 내에서 중요한 네트워크 리소스에 대한 변경이 발생할 때 트리거되는 경고를 만듭니다.

Azure 활동 로그 이벤트를 보고 검색합니다.

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Azure Monitor를 사용하여 활동 로그 경고를 생성, 보기 및 관리합니다.

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [보안 제어: 로깅 및 모니터링](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)을 참조하십시오.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 소스 사용

**지침**: 해당되지 않음; Microsoft는 로그의 타임스탬프에 대해 Azure 키 볼트와 같은 Azure 리소스에 사용되는 시간 원본을 유지 관리합니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 마이크로 소프트

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: Azure 키 자격 증명 모음에서 생성된 보안 데이터를 집계하기 위해 Azure Monitor를 통해 로그를 수집합니다. Azure 모니터 내에서 Azure Log Analytics 작업 영역을 사용하여 분석을 쿼리및 수행하고 장기/보관 저장소에 Azure 저장소 계정을 사용합니다. 또는 Azure Sentinel 또는 타사 SIEM에 대한 데이터를 활성화및 온보던할 수 있습니다. 

Azure 키 볼트 로깅:

https://docs.microsoft.com/azure/key-vault/key-vault-logging

빠른 시작: Azure Sentinel 온보드 하는 방법:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: Azure Key Vault 인스턴스에서 감사, 보안 및 진단 로그에 액세스할 수 있도록 진단 설정을 사용하도록 설정합니다. 자동으로 사용할 수 있는 활동 로그에는 이벤트 소스, 날짜, 사용자, 타임스탬프, 소스 주소, 대상 주소 및 기타 유용한 요소가 포함됩니다.

Azure 키 볼트 로깅:

https://docs.microsoft.com/azure/key-vault/key-vault-logging


**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 저장소 보존 구성

**지침**: Azure 모니터 내에서 Azure Key Vault 로그를 보유하는 데 사용되는 로그 분석 작업 영역에 대해 조직의 규정 준수 규정에 따라 보존 기간을 설정합니다. 장기/보관 저장소에 Azure 저장소 계정을 사용합니다.

데이터 보존 기간 변경:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침:** 로그를 분석 및 모니터링하여 비정상적인 동작을 검사하고 Azure Key Vault로 보호된 리소스에 대한 결과를 정기적으로 검토합니다. Azure 모니터의 로그 분석 작업 영역을 사용하여 로그를 검토하고 로그 데이터에 대한 쿼리를 수행합니다. 또는 Azure Sentinel 또는 타사 SIEM에 대한 데이터를 활성화및 온보드할 수 있습니다. 

빠른 시작: 온보드 Azure 센티넬:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Azure 모니터에서 로그 분석 시작:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Azure 모니터에서 로그 쿼리를 시작하십시오.

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: 비정상적인 활동에 대한 경고 사용

**지침**: Azure 보안 센터에서 키 볼트에 대한 고급 위협 보호(ATP)를 사용하도록 설정합니다. Azure 키 볼트에서 진단 설정을 활성화하고 로그 분석 작업 영역으로 로그를 보냅니다. 보안 오케스트레이션 자동화된 응답(SOAR) 솔루션을 제공하므로 로그 분석 작업 영역을 Azure Sentinel에 온보답니다. 이를 통해 재생책(자동화된 솔루션)을 만들고 보안 문제를 해결하는 데 사용할 수 있습니다.

빠른 시작: 온보드 Azure 센티넬:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Azure 보안 센터에서 보안 경고를 관리하고 응답합니다.

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Azure 모니터 경고를 사용하여 이벤트에 응답합니다.

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

**지침**: 해당되지 않음; Azure Key Vault는 맬웨어 방지 관련 로그를 처리하거나 생성하지 않습니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

**지침**: 해당되지 않음; Azure 키 볼트는 DNS 관련 로그를 처리하거나 생성하지 않습니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅 사용

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [보안 제어: ID 및 액세스 제어](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)를 참조하십시오.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지

**지침**: Azure Active Directory에 등록된 응용 프로그램및 Azure Key Vault 키, 비밀 및 인증서에 액세스할 수 있는 모든 사용자 계정의 인벤토리를 유지 관리합니다. Azure 포털 또는 PowerShell을 사용하여 키 볼트 액세스를 쿼리하고 조정할 수 있습니다. PowerShell에서 액세스를 보려면 다음 명령을 사용합니다.

(Get-AzResource -ResourceId [KeyVaultResourceID]). 속성.액세스 정책

Azure Active 디렉터리로 응용 프로그램 등록:

https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#registering-an-application-with-azure-active-directory

키 자격 증명 모음에 대한 보안 액세스:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 해당하는 경우 기본 암호 변경

**지침**: 해당되지 않음; Azure Key Vault는 Active Directory에서 인증을 제공하고 역할 기반 액세스 제어로 보호하기 때문에 기본 암호 개념을 가지고 있지 않습니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: Azure Key Vault 인스턴스에 액세스할 수 있는 전용 관리 계정 사용에 대한 표준 운영 절차를 만듭니다. Azure 보안 센터 ID 및 액세스 관리(현재 미리 보기)를 사용하여 활성 관리 계정 수를 모니터링합니다.

ID 및 액세스 모니터링(미리 보기):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 단일 사인온(SSO) 사용

**지침**: AppId, TenantID 및 ClientSecret와 함께 Azure 서비스 주체를 사용하여 응용 프로그램을 원활하게 인증하고 Azure Key Vault 암호에 액세스하는 데 사용할 토큰을 검색합니다.

.NET을 사용하여 Azure 키 자격 증명 모음에 대한 서비스 간 인증:

https://docs.microsoft.com/azure/key-vault/service-to-service-authentication



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure Active Directory 다단계 인증을 활성화하고 Azure 보안 센터 ID 및 액세스 관리(현재 미리 보기) 권장 사항을 따라 이벤트 허브 지원 리소스를 보호합니다.

클라우드 기반 Azure 다단계 인증 배포 계획:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

ID 및 액세스 모니터링(미리 보기):

https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(권한 있는 액세스 워크스테이션) 사용

**지침**: 로그인하고 키 볼트 사용 리소스를 구성하도록 구성된 MFA(Azure 다단계 인증)를 사용하여 권한 있는 액세스 워크스테이션(PAW)을 사용합니다. 

권한 있는 액세스 워크스테이션:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations 

클라우드 기반 Azure 다단계 인증 배포 계획:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 관리 계정의 의심스러운 활동에 대한 로그 및 경고

**지침**: 환경에서 의심스럽거나 안전하지 않은 활동이 발생할 때 로그 및 경고를 생성하기 위해 AAD(Azure Active Directory) 권한 ID 관리(PIM)를 사용합니다. AAD 위험 검색을 사용하여 위험한 사용자 동작에 대한 경고 및 보고서를 볼 수 있습니다. 추가 로깅을 위해 Azure Security Center 위험 감지 경고를 Azure Monitor로 보내고 작업 그룹을 사용하여 사용자 지정 경고/알림을 구성합니다.

Azure Key Vault에 대한 고급 위협 보호(ATP)를 사용하여 의심스러운 활동에 대한 경고를 생성합니다.

Azure AD 권한 ID 관리(PIM) 배포:https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Azure 키 자격 증명 모음에 대한 고급 위협 보호 설정(미리 보기):https://docs.microsoft.com/azure/security-center/advanced-threat-protection-key-vault

Azure 키 자격 증명 모음에 대한 경고(미리 보기):https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurekv

Azure Active Directory 위험 검색:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Azure 포털에서 작업 그룹을 만들고 관리합니다.https://docs.microsoft.com/azure/azure-monitor/platform/action-groups



**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**안내**: 조건부 액세스 정책의 위치 조건을 구성하고 지정된 위치를 관리합니다. 명명된 위치를 사용하여 IP 주소 범위 또는 국가 및 지역의 논리적 그룹을 만들 수 있습니다. 키 볼트 비밀과 같은 중요한 리소스에 대한 액세스를 구성된 명명된 위치로 제한할 수 있습니다.

Azure Active 디렉터리 조건부 액세스의 위치 조건은 무엇입니까?:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations



**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="39-use-azure-active-directory"></a>3.9: Azure 활성 디렉터리 사용

**지침**: 키 볼트와 같은 Azure 리소스에 대한 중앙 인증 및 권한 부여 시스템으로 Azure Active Directory(AAD)를 사용합니다. 이렇게 하면 역할 기반 액세스 제어(RBAC)가 중요한 리소스를 관리할 수 있습니다.

 

빠른 시작: Azure Active 디렉터리에서 새 테넌트를 만듭니다.

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 사용자 액세스를 정기적으로 검토하고 조정

**지침**: Azure 키 볼트 관리 역할을 사용하여 오래된 계정을 검색하는 데 도움이 되는 AAD(Azure Active Directory) 로그를 검토합니다. 또한 AAD 액세스 검토를 사용하여 그룹 구성원 자격, Azure Key Vault에 액세스하는 데 사용할 수 있는 엔터프라이즈 응용 프로그램에 대한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 사용자 액세스는 90일마다 정기적으로 검토하여 올바른 사용자만 계속 액세스할 수 있도록 해야 합니다.

Azure Active Directory 보고서 및 모니터링 설명서는 다음과 같습니다.

https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Azure AD 액세스 리뷰는 무엇입니까?:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 비활성화된 계정에 액세스하려는 시도를 모니터링합니다.

**지침**: Azure 키 자격 증명 모음 및 Azure Active Directory에 대한 진단 설정을 활성화하여 모든 로그를 로그 분석 작업 영역으로 보냅니다. Log Analytics 내에서 원하는 경고(예: 비활성화된 암호에 액세스하려는 시도)를 구성합니다.

Azure AD 로그를 Azure 모니터 로그와 통합합니다.https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

이전 키 볼트 솔루션에서 마이그레이션:https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault#migrating-from-the-old-key-vault-solution



**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure Active Directory의 ID 보호 및 위험 검색 기능을 사용하여 Azure Key Vault 보호 리소스와 관련된 의심스러운 작업을 검색하도록 자동화된 응답을 구성합니다. Azure Sentinel을 통해 자동화된 응답을 활성화하여 조직의 보안 응답을 구현해야 합니다. 

Azure Active Directory 포털에서 위험한 로그인 보고서:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins 

사용 방법: 위험 정책을 구성하고 사용하도록 설정합니다.https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Azure 센티넬 온보보드 방법:https://docs.microsoft.com/azure/sentinel/quickstart-onboard


**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오 중에 Microsoft에 관련 고객 데이터에 대한 액세스 제공

**지침**: 해당되지 않음; Azure 키 자격 증명 모음에 대 한 지원 되지 않는 고객 잠금 상자입니다.

일반 공급에서 지원되는 서비스 및 시나리오:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [보안 제어: 데이터 보호](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)를 참조하십시오.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 민감한 정보의 인벤토리 유지

**지침**: 태그를 사용하여 Azure Key Vault 사용 지원 리소스에 대한 중요한 정보를 저장하거나 처리하는 Azure 리소스를 추적할 수 있습니다. 

태그를 사용하여 Azure 리소스를 구성합니다.https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 민감한 정보를 저장하거나 처리하는 시스템을 격리

**지침**: 특정 서브넷에 대한 액세스를 제한하도록 구성된 가상 네트워크 서비스 끝점을 사용하여 Azure Key Vault에 대한 액세스를 보호할 수 있습니다.

방화벽 규칙이 적용된 후에는 요청이 허용된 서브넷 또는 IP 주소 범위에서 시작된 경우에만 Azure Key Vault 데이터 평면 작업을 수행할 수 있습니다. 이는 Azure 포털의 Azure 키 자격 증명 모음 액세스에도 적용됩니다. Azure 포털에서 키 자격 증명 모음으로 찾아볼 수 있지만 클라이언트 컴퓨터가 허용된 목록에 없는 경우 키, 암호 또는 인증서를 나열하지 못할 수 있습니다. Azure 키 볼트 선택기 및 기타 Azure 서비스에도 영향을 줍니다. 방화벽 규칙으로 인해 클라이언트 컴퓨터가 키 볼트를 사용하지 못하도록 하는 경우 키 볼트 목록을 볼 수 있지만 키 목록은 표시되지 않을 수 있습니다.

Azure 키 볼트 방화벽 및 가상 네트워크 구성:https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Azure 키 볼트에 대한 가상 네트워크 서비스 끝점:https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints



**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 민감한 정보의 무단 전송 모니터링 및 차단

**지침**: Azure 키 자격 증명 모음에 저장된 모든 데이터는 중요한 것으로 간주됩니다. Azure 키 볼트 데이터 평면 액세스 컨트롤을 사용하여 Azure 키 볼트 보안 암호에 대한 액세스를 제어합니다. Key Vault의 기본 제공 방화벽을 사용하여 네트워크 계층의 액세스를 제어할 수도 있습니다. Azure 키 볼트에 대한 액세스를 모니터링하려면 키 볼트 진단 설정을 활성화하고 Azure 저장소 계정 또는 로그 분석 작업 영역으로 로그를 보냅니다.

키 자격 증명 모음에 대한 보안 액세스:https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault

Azure 키 볼트 방화벽 및 가상 네트워크 구성:https://docs.microsoft.com/azure/key-vault/key-vault-network-security

Azure 키 볼트 로깅:https://docs.microsoft.com/azure/key-vault/key-vault-logging



**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중의 모든 민감한 정보 암호화

**지침**: 인증, 관리 및 데이터 평면 액세스를 위해 Azure Key Vault로 의모든 트래픽이 암호화되어 HTTPS: port 443을 통과합니다. (그러나 CRL에 대한 HTTP [포트 80] 트래픽이 있을 수 있습니다.) 

방화벽 뒤에 있는 Azure 키 자격 증명 모음에 액세스합니다.https://docs.microsoft.com/azure/key-vault/key-vault-access-behind-firewall



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 마이크로 소프트

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: 해당되지 않음; Azure 키 볼트 내의 모든 데이터(비밀, 키 및 인증서)는 중요한 것으로 간주됩니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스를 제어합니다.

**안내:** Azure Key Vault 인스턴스의 관리 및 데이터 평면에 대한 보안 액세스.

키 자격 증명 모음에 대한 보안 액세스:

https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

**지침**: Microsoft는 Azure Key Vault의 기본 인프라를 관리하고 고객 데이터의 손실 또는 노출을 방지하기 위해 엄격한 제어를 구현했습니다.

Azure Key Vault란?

https://docs.microsoft.com/azure/key-vault/key-vault-overview

Azure 고객 데이터 보호:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 마이크로 소프트

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 미사용 정보 암호화

**지침**: 모든 관리되는 개체(키, 인증서 및 암호)는 Azure Key Vault에서 미사용 으로 암호화됩니다.

지원 문서:

- [암호화 모델 및 키 관리 테이블](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest#encryption-model-and-key-management-table)


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 마이크로 소프트

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 사항에 대한 로그 및 경고

**지침**: Azure 모니터의 Azure 키 볼트 분석 솔루션을 사용하여 Azure 키 볼트 감사 이벤트 로그를 검토합니다.

Azure 모니터의 Azure 키 볼트 분석 솔루션:

https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

## <a name="vulnerability-management"></a>취약성 관리

*자세한 내용은 [보안 제어: 취약점 관리](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)를 참조하십시오.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약점 검색 도구 실행

**지침**: Microsoft는 Azure 키 볼트를 지원하는 기본 시스템에서 취약점 관리를 수행합니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 마이크로 소프트

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: 자동화된 운영 체제 패치 관리 솔루션 배포

**지침**: 해당/A; Microsoft는 Key Vault를 지원하는 기본 시스템에서 패치 관리를 수행합니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: 자동화된 타사 소프트웨어 패치 관리 솔루션 배포

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 백투백 취약점 검사 비교

**지침**: Microsoft는 Key Vault를 지원하는 기본 시스템에서 취약점 관리를 수행합니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 마이크로 소프트

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 발견된 취약점의 문제 해결에 우선 순위를 지정합니다.

**지침**: Azure 보안 센터에서 제공하는 기본 위험 등급(보안 점수)을 사용합니다.

Azure 보안 센터에서 보안 점수 향상:

https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [보안 관리: 인벤토리 및 자산 관리를](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)참조하십시오.*

### <a name="61-use-azure-asset-discovery"></a>6.1: Azure 자산 검색 사용

**지침**: Azure 리소스 그래프를 사용하여 구독 내의 모든 리소스(Azure Key Vault 인스턴스 포함)를 쿼리하고 검색합니다. 테넌트에 적절한 (읽기) 권한이 있고 구독 내의 모든 Azure 구독과 리소스를 등록할 수 있는지 확인합니다.

빠른 시작: Azure 리소스 그래프 탐색기를 사용하여 첫 번째 리소스 그래프 쿼리를 실행합니다.

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

현재 계정이 액세스할 수 있는 구독을 가져옵니다.

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Azure 리소스에 대한 RBAC(역할 기반 액세스 제어)란?

https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타데이터를 제공하는 Azure Key Vault 리소스에 태그를 적용하여 논리적으로 분류로 구성합니다.

태그를 만들고 사용하는 방법:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 승인되지 않은 Azure 리소스 삭제

**지침:** 적절한 경우 태그 지정, 관리 그룹 및 별도의 구독을 사용하여 Azure Key Vault 인스턴스 및 관련 리소스를 구성하고 추적합니다. 정기적으로 인벤토리를 조정하고 승인되지 않은 리소스가 적시에 구독에서 삭제되도록 합니다.

추가 Azure 구독 만들기:

https://docs.microsoft.com/azure/billing/billing-create-subscription

리소스 구성 및 관리를 위한 관리 그룹 만들기:

https://docs.microsoft.com/azure/governance/management-groups/create

태그를 사용하여 Azure 리소스를 구성합니다.https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: 승인된 Azure 리소스 및 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: 계산 리소스에 대해 승인된 Azure 리소스 및 승인된 소프트웨어 목록 정의

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure 정책을 사용하여 다음 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 유형에 대한 제한을 두십시오.

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

또한 Azure 리소스 그래프를 사용하여 구독 내의 리소스를 쿼리/검색합니다.

자습서: 규정 준수를 적용하기 위한 정책을 만들고 관리합니다.https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

빠른 시작: Azure 리소스 그래프 탐색기를 사용하여 첫 번째 리소스 그래프 쿼리를 실행합니다.https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 응용 프로그램 제거

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스뿐만 아니라 Azure 전체를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="68-use-only-approved-applications"></a>6.8: 승인된 신청서만 사용

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure 정책을 사용하여 다음 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 유형에 대한 제한을 두십시오.

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

자습서: 규정 준수를 적용하기 위한 정책을 만들고 관리합니다.https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure 정책 샘플:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="610-implement-approved-application-list"></a>6.10: 승인된 신청 목록 구현

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: 스크립트를 통해 AzureResources 관리자와 상호 작용하는 사용자의 기능 제한

**지침**: Azure 조건부 액세스를 사용하여 "Microsoft Azure 관리" 앱에 대해 "액세스 차단"을 구성하여 사용자가 ARM(Azure Resource Manager)과 상호 작용하는 기능을 제한합니다. 이렇게 하면 Key Vault 구성과 같이 높은 보안 환경 내에서 리소스를 만들고 변경하지 못할 수 있습니다.

조건부 액세스를 사용하여 Azure 관리에 대한 액세스를 관리합니다.

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 컴퓨팅 리소스 내에서 스크립트를 실행하는 사용자의 기능 제한

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 물리적 또는 논리적으로 고위험 응용 프로그램을 분리

**지침**: 해당되지 않음; 이 권장 사항은 Azure 앱 서비스에서 실행중인 웹 응용 프로그램 또는 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [보안 제어: 보안 구성](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)을 참조하십시오.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: "Microsoft.KeyVault" 네임스페이스에서 Azure 정책 별칭을 사용하여 Azure Key Vault 인스턴스의 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다. 다음과 같은 Azure 키 자격 증명 모음에 대해 기본 제공 Azure 정책 정의를 사용할 수도 있습니다.

Key Vault 개체를 복구할 수 있어야 합니다.

Key Vault의 진단 설정을 Log Analytics 작업 영역에 배포

Key Vault의 진단 로그를 사용하도록 설정해야 합니다.

Key Vault는 가상 네트워크 서비스 엔드포인트를 사용해야 함

Key Vault의 진단 설정을 Event Hub에 배포

Azure 키 볼트 인스턴스에 대한 보안 구성 기준으로 Azure 보안 센터의 권장 사항을 사용합니다.

사용 가능한 Azure 정책 별칭을 보는 방법:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

자습서: 규정 준수를 적용하기 위한 정책을 만들고 관리합니다.

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 안전한 Azure 리소스 구성 유지

**지침**: Azure 정책 [거부] 및 [존재하지 않는 경우 배포]를 사용하여 Azure 키 볼트 지원 리소스에서 보안 설정을 적용합니다. 

자습서: 규정 준수를 적용하기 위한 정책을 만들고 관리합니다.

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage 

  
Azure 정책 효과 이해: 

https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 안전한 운영 체제 구성 유지

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스의 구성을 안전하게 저장

**지침**: Azure Key Vault 사용 리소스에 대한 사용자 지정 Azure 정책 정의를 사용하는 경우 Azure Repos를 사용하여 코드를 안전하게 저장하고 관리합니다.

Azure DevOps에 코드를 저장하는 방법: 

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops 

Azure 리포지토리 설명서: 

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: 시스템 구성 관리 도구 배포

**지침**: "Microsoft.KeyVault" 네임스페이스에서 Azure 정책 별칭을 사용하여 시스템 구성을 경고, 감사 및 적용하는 사용자 지정 정책을 만듭니다. 또한 정책 예외를 관리하기 위한 프로세스 및 파이프라인을 개발합니다.

Azure 정책을 구성하고 관리하는 방법:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 시스템 구성 관리 도구 배포

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Azure 서비스에 대한 자동화된 구성 모니터링 구현

**지침**: Azure 보안 센터를 사용하여 Azure 키 볼트로 보호된 리소스에 대한 기준 검사를 수행합니다. 

  

Azure 보안 센터에서 권장 사항을 수정하는 방법: 

https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제를 위한 자동화된 구성 모니터링 구현

**지침**: 해당되지 않음; 이 벤치마크는 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure 비밀을 안전하게 관리

**지침**: Azure Key Vault와 함께 관리되는 서비스 ID를 사용하여 클라우드 응용 프로그램에 대한 비밀 관리를 단순화하고 보호합니다. Azure 키 볼트 소프트 삭제가 활성화되어 있는지 확인합니다.

Azure 관리 ID와 통합하는 방법:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

키 볼트를 만드는 방법:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

관리되는 ID로 키 볼트 인증을 제공하는 방법: 

https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: ID를 안전하고 자동으로 관리

**지침**: Azure Key Vault와 함께 관리되는 서비스 ID를 사용하여 클라우드 응용 프로그램에 대한 비밀 관리를 단순화하고 보호합니다. 

  

Azure 관리 ID와 통합하는 방법: 

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity  

키 볼트를 만드는 방법: 

https://docs.microsoft.com/azure/key-vault/quick-create-portal    

관리되는 ID로 키 볼트 인증을 제공하는 방법:  
https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 Azure Key Vault와 같은 보다 안전한 위치로 이동하는 것을 권장합니다.  
  
 자격 증명 스캐너를 설정하는 방법:https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [보안 제어: 맬웨어 방어](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)를 참조하십시오.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: 중앙에서 관리되는 맬웨어 방지 소프트웨어 사용

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다. Microsoft는 기본 플랫폼에 대한 맬웨어 방지를 처리합니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비계산 Azure 리소스에 업로드할 파일을 미리 스캔합니다.

**지침**: Azure 서비스(예: Azure Key Vault)를 지원하는 기본 호스트에서 Microsoft 맬웨어 방지 기능이 활성화되어 있지만 고객 콘텐츠에서는 실행되지 않습니다.

Azure Key Vault와 같은 계산되지 않은 Azure 리소스로 업로드되거나 전송되는 콘텐츠를 미리 검사합니다. 이러한 경우 Microsoft는 사용자의 데이터에 액세스할 수 없습니다.

Azure 클라우드 서비스 및 가상 시스템에 대한 Microsoft 맬웨어 방지 이해:https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명이 업데이트되었는지 확인

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다. Microsoft는 기본 플랫폼에 대한 맬웨어 방지를 처리합니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [보안 제어: 데이터 복구](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)를 참조하십시오.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 정기적인 자동 백업 보장

**지침**: 다음 PowerShell 명령을 통해 키 볼트 인증서, 키, 관리되는 저장소 계정 및 비밀에 대한 정기적인 자동 백업을 보장합니다.

- 백업-아즈키볼트인증서

- 백업-아즈키볼트키

- 백업-아즈키볼트관리창고계정

- 백업-아즈키볼트시크릿

선택적으로 Azure 백업 내에 키 볼트 백업을 저장할 수 있습니다.

키 볼트 인증서를 백업하는 방법:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

키 볼트 키를 백업하는 방법:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

키 볼트 관리 저장소 계정을 백업하는 방법:https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

키 볼트 비밀을 백업하는 방법:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Azure 백업을 활성화하는 방법:https://docs.microsoft.com/azure/backup



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업 수행 및 고객 관리 키 백업

**지침**: 다음 PowerShell 명령을 통해 키 볼트 인증서, 키, 관리되는 저장소 계정 및 비밀의 백업을 수행합니다.

- 백업-아즈키볼트인증서

- 백업-아즈키볼트키

- 백업-아즈키볼트관리창고계정

- 백업-아즈키볼트시크릿

선택적으로 Azure 백업 내에 키 볼트 백업을 저장할 수 있습니다.

키 볼트 인증서를 백업하는 방법:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate

키 볼트 키를 백업하는 방법:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

키 볼트 관리 저장소 계정을 백업하는 방법:https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount

키 볼트 비밀을 백업하는 방법:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

Azure 백업을 활성화하는 방법:https://docs.microsoft.com/azure/backup



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 포함한 모든 백업의 유효성 검사

**지침**: 다음과 같은 PowerShell 명령을 사용하여 키 볼트 인증서, 키, 관리되는 저장소 계정 및 비밀의 데이터 복원을 주기적으로 수행합니다.

- 복원-아즈키볼트인증서

- 복원-아즈키볼트키

- 복원-아즈키볼트관리저장계정

- 복원-아즈키볼트시크릿

키 볼트 인증서를 복원하는 방법:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0

키 볼트 키를 복원하는 방법:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0 

키 볼트 관리 저장소 계정을 복원 하는 방법:https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount

키 볼트 비밀을 복원하는 방법:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리 키 보호 보장

**지침**: Azure 키 자격 증명 모음에 대해 소프트 삭제가 활성화되어 있는지 확인합니다. 소프트 삭제를 사용하면 삭제된 키 자격 증명 모음 및 키, 암호 및 인증서와 같은 자격 증명 모음 개체를 복구할 수 있습니다. 

Azure 키 볼트의 소프트 삭제를 사용하는 방법: 

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [보안 제어: 인시던트 대응](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)을 참조하십시오.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 사고 대응 가이드 만들기

**지침**: 조직에 대한 인시던트 대응 가이드를 작성합니다. 감지에서 사후 검토에 이르는 사고 처리/관리 단계뿐만 아니라 직원의 모든 역할을 정의하는 서면 인시던트 대응 계획이 있는지 확인합니다. 이러한 프로세스는 Key Vault 비밀을 사용하는 것과 같은 중요한 시스템을 보호하는 데 중점을 두어야 합니다.

Azure 보안 센터 내에서 워크플로 자동화를 구성하는 방법: 

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide   

자체 보안 인시던트 대응 프로세스 구축에 대한 지침:  

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Microsoft 보안 대응 센터의 사고 해부학:   

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process   

고객은 NIST의 컴퓨터 보안 사고 처리 가이드를 활용하여 자체 사고 대응 계획을 수립할 수 있습니다. 

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 채점 및 우선 순위 지정 절차 만들기

**지침:** 보안 센터는 각 경고에 심각도를 할당하여 먼저 조사해야 하는 경고의 우선 순위를 지정합니다. 심각도는 보안 센터가 찾기 또는 경고를 발행하는 데 사용되는 분석 및 경고로 이어진 활동의 배후에 악의적인 의도가 있다는 신뢰 수준에 얼마나 확신을 가지고 있는지에 따라 다집니다. 또한 구독을 명확하게 표시합니다(ex. 프로덕션, 비prod) 및 명명 시스템을 만들어 Azure 리소스, 특히 Azure Key Vault 암호와 같은 중요한 데이터를 처리하는 리소스를 명확하게 식별하고 분류합니다.


**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 절차 테스트

**지침**: Azure Key Vault 인스턴스 및 관련 리소스를 보호하는 데 도움이 되는 정기적인 흐름에서 시스템의 인시던트 대응 기능을 테스트하기 위한 연습을 수행합니다. 약점과 격차를 식별하고 필요에 따라 계획을 수정합니다.

NIST의 간행물: IT 계획 및 기능에 대한 테스트, 교육 및 운동 프로그램 가이드를 참조하십시오. 

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침:** MICROSOFT 보안 대응 센터(MSRC)에서 불법 또는 승인되지 않은 당사자가 데이터에 액세스한 사실을 발견한 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 연락합니다.  사실 이후에 인시던트를 검토하여 문제가 해결되었는지 확인합니다.

Azure 보안 센터 보안 연락처를 설정 하는 방법:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 사고 대응 시스템에 통합

**지침**: 지속적인 내보내기 기능을 사용하여 Azure 보안 센터 경고 및 권장 사항을 내보내 Azure 키 볼트 지원 리소스에 대한 위험을 식별할 수 있습니다. 연속 내보내기를 사용하면 수동으로 또는 지속적으로 지속방식으로 경고 및 권장 사항을 내보낼 수 있습니다.  Azure 보안 센터 데이터 커넥터를 사용하여 경고를 Azure Sentinel으로 스트리밍할 수 있습니다. 

 

연속 내보내기를 구성하는 방법: 

https://docs.microsoft.com/azure/security-center/continuous-export 

  

Azure Sentinel로 경고를 스트리밍하는 방법: 

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 응답 자동화

**지침**: Azure 보안 센터의 워크플로 자동화 기능을 사용하여 보안 경고 및 권장 사항에 대한 "논리 앱"을 통해 응답을 자동으로 트리거하여 Azure Key Vault로 보호된 리소스를 보호합니다. 

 

워크플로 자동화 및 논리 앱을 구성하는 방법: 

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [보안 제어: 침투 테스트 및 빨간색 팀 연습을](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)참조하십시오.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Azure 리소스에 대한 정기적인 침투 테스트를 수행하고 60일 이내에 모든 중요한 보안 결과의 수정을 보장합니다.

**지침**: Azure Key Vault 서비스에서 펜 테스트를 직접 수행할 수는 없지만 키 볼트를 사용하여 보안 보안을 보장하는 Azure 리소스를 테스트하는 것이 좋습니다.

보급 시험이 Microsoft 정책을 위반하지 않도록 Microsoft 참여 규칙을 따라야 합니다.

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Microsoft에서 관리하는 클라우드 인프라, 서비스 및 응용 프로그램에 대한 Red Teaming 및 실시간 사이트 침투 테스트의 전략 및 실행에 대한 자세한 내용은 여기에서 확인할 수 있습니다. 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 공유

## <a name="next-steps"></a>다음 단계

- Azure [보안 벤치마크](https://docs.microsoft.com/azure/security/benchmarks/overview) 보기
- [Azure 보안 기준에](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview) 대해 자세히 알아보기
