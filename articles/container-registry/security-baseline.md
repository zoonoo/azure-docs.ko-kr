---
title: Azure 컨테이너 레지스트리에 대한 Azure 보안 기준
description: Azure 컨테이너 레지스트리에 대한 Azure 보안 기준
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 9225cfd9793a84f371387d6450a3dfa80ba74de3
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547534"
---
# <a name="azure-security-baseline-for-azure-container-registry"></a>Azure 컨테이너 레지스트리에 대한 Azure 보안 기준

Azure 컨테이너 레지스트리에 대한 Azure 보안 기준에는 배포의 보안 상태를 개선하는 데 도움이 되는 권장 사항이 포함되어 있습니다.

이 서비스의 기준은 Azure [Security 벤치마크 버전 1.0에서](https://docs.microsoft.com/azure/security/benchmarks/overview)가져온 것으로, 모범 사례 지침을 통해 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다.

자세한 내용은 [Azure 보안 기준 개요를](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)참조하십시오.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [보안 제어: 네트워크 보안](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)을 참조하십시오.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: 가상 네트워크에서 네트워크 보안 그룹 또는 Azure 방화벽을 사용하여 리소스 보호

**안내**: Azure 가상 네트워크는 Azure 및 온-프레미스 리소스에 대한 안전한 개인 네트워킹을 제공합니다. Azure 가상 네트워크에서 개인 Azure 컨테이너 레지스트리에 대한 액세스를 제한하면 가상 네트워크의 리소스만 레지스트리에 액세스하도록 할 수 있습니다. 크로스-프레미스 시나리오의 경우 특정 IP 주소에서만 레지스트리 액세스를 허용하도록 방화벽 규칙을 구성할 수도 있습니다. 방화벽 뒤에서 방화벽 액세스 규칙 및 서비스 태그를 구성하여 컨테이너 레지스트리에 액세스합니다.

Azure 가상 네트워크 또는 방화벽 규칙을 사용하여 Azure 컨테이너 레지스트리에 대한 액세스를 제한합니다.https://docs.microsoft.com/azure/container-registry/container-registry-vnet 

방화벽 뒤의 Azure 컨테이너 레지스트리에 액세스하도록 규칙을 구성합니다.https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules


**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Vnet, 서브넷 및 NIC의 구성 및 트래픽을 모니터링하고 기록합니다.

**지침:** Azure 보안 센터를 사용하고 네트워크 보호 권장 사항을 수정하여 Azure에서 네트워크 리소스를 보호합니다. NSG 흐름 로그를 활성화하고 트래픽 감사를 위해 저장소 계정으로 로그를 보냅니다.

NSG 흐름 로그를 활성화하는 방법:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

네트워크 리소스 보호:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations



**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침**: 해당되지 않습니다. 벤치마크는 Azure 앱 서비스 또는 웹 응용 프로그램을 호스팅하는 리소스를 계산하기 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: DDoS 공격으로부터 보호하기 위해 가상 네트워크에서 DDoS 표준 보호를 활성화합니다. Azure 보안 센터 통합 위협 인텔리전스를 사용하여 알려진 악성 또는 사용되지 않는 인터넷 IP 주소와의 통신을 거부합니다.  위협 인텔리전스를 사용하도록 설정하고 악의적인 네트워크 트래픽에 대해 "경고 및 거부"로 구성된 조직의 각 네트워크 경계에 Azure 방화벽을 배포합니다.

Azure 보안 센터 Just In Time 네트워크 액세스를 사용하여 제한된 기간 동안 승인된 IP 주소로 끝점 의 노출을 제한하도록 NSG를 구성할 수 있습니다. 또한 Azure 보안 센터 적응형 네트워크 강화를 사용하여 실제 트래픽 및 위협 인텔리전스를 기반으로 포트 및 소스 IP를 제한하는 NSG 구성을 권장합니다.

DDoS 보호를 구성하는 방법:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Azure 방화벽을 배포하는 방법:https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Azure 보안 센터 통합 위협 인텔리전스 이해:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Azure 보안 센터 적응형 네트워크 강화 이해:https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Azure 보안 센터 그냥 시간 네트워크 액세스 제어:https://docs.microsoft.com/azure/security-center/security-center-just-in-time


**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: 네트워크 패킷 및 흐름 로그 기록

**지침**: Azure 컨테이너 레지스트리를 보호하는 데 사용되는 서브넷에 연결된 NSG에 대한 NSG(네트워크 보안 그룹) 흐름 로그를 사용하도록 설정합니다. NSG 흐름 로그를 Azure 저장소 계정에 기록하여 흐름 레코드를 생성할 수 있습니다. 비정상적인 활동을 조사하는 데 필요한 경우 Azure Network Watcher 패킷 캡처를 사용하도록 설정합니다.

NSG 흐름 로그를 활성화하는 방법:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

네트워크 감시자 사용 방법:https://docs.microsoft.com/azure/network-watcher/network-watcher-create


**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 침입 감지/침입 방지 시스템(IDS/IPS) 배포

**지침**: 페이로드 검사 기능을 갖춘 IDS/IPS 기능을 지원하는 Azure Marketplace에서 오퍼를 선택합니다. 페이로드 검사를 기반으로 하는 침입 감지 및/또는 방지가 요구 사항이 아닌 경우 위협 인텔리전스가 있는 Azure 방화벽을 사용할 수 있습니다. Azure 방화벽 위협 인텔리전스 기반 필터링은 알려진 악의적인 IP 주소 및 도메인에 대한 트래픽을 경고하고 거부할 수 있습니다. IP 주소 및 도메인은 Microsoft 위협 인텔리전스 피드에서 제공됩니다.

조직의 각 네트워크 경계에 선택한 방화벽 솔루션을 배포하여 악의적인 트래픽을 탐지 및/또는 거부합니다.

Azure 마켓플레이스:https://azuremarketplace.microsoft.com/marketplace/?term=Firewall 

Azure 방화벽을 배포하는 방법:https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Azure 방화벽으로 경고를 구성하는 방법:https://docs.microsoft.com/azure/firewall/threat-intel


**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션으로의 트래픽 관리

**지침**: 해당되지 않습니다. 벤치마크는 Azure 앱 서비스 또는 계산 리소스에서 실행되는 웹 응용 프로그램을 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: 컨테이너 레지스트리에 액세스해야 하는 리소스의 경우 Azure 컨테이너 레지스트리 서비스에 대한 가상 네트워크 서비스 태그를 사용하여 네트워크 보안 그룹 또는 Azure 방화벽에서 네트워크 액세스 제어를 정의합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 규칙의 적절한 원본 또는 대상 필드에 서비스 태그 이름 "AzureContainerRegistry"를 지정하면 해당 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다. Microsoft는 서비스 태그로 둘러싸인 주소 접두사를 관리하고 주소가 변경될 때 서비스 태그를 자동으로 업데이트합니다.

서비스 태그로 액세스 허용:https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules#allow-access-by-service-tag


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 장치에 대한 표준 보안 구성 유지

**지침**: Azure 정책을 통해 Azure 컨테이너 레지스트리와 연결된 네트워크 리소스에 대한 표준 보안 구성을 정의하고 구현합니다. "Microsoft.ContainerRegistry" 및 "Microsoft.Network" 네임스페이스에서 Azure 정책 별칭을 사용하여 컨테이너 레지스트리의 네트워크 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다. 

Azure Blueprint를 사용하여 단일 Blueprint 정의에서 Azure 리소스 관리자 템플릿, RBAC 컨트롤 및 정책과 같은 주요 환경 아티팩트를 패키징하여 대규모 Azure 배포를 단순화할 수 있습니다. 새로운 구독에 Blueprint를 쉽게 적용하고 버전 관리를 통해 제어 및 관리를 미세 조정할 수 있습니다.

Azure 정책을 사용하여 Azure 컨테이너 레지스트리의 준수 를 감사합니다.https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Azure Blueprint를 만드는 방법:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙

**지침**: 고객은 Azure Blueprint를 사용하여 단일 Blueprint 정의에서 Azure Resource Manager 템플릿, RBAC 컨트롤 및 정책과 같은 주요 환경 아티팩트를 패키징하여 대규모 Azure 배포를 단순화할 수 있습니다. 새로운 구독에 Blueprint를 쉽게 적용하고 버전 관리를 통해 제어 및 관리를 미세 조정할 수 있습니다.

Azure Blueprint를 만드는 방법:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 을 모니터링하고 변경 사항을 감지합니다.

**지침**: Azure 활동 로그를 사용하여 네트워크 리소스 구성을 모니터링하고 컨테이너 레지스트리와 관련된 네트워크 리소스의 변경 내용을 검색합니다. Azure Monitor 내에서 중요한 네트워크 리소스에 대한 변경이 발생할 때 트리거되는 경고를 만듭니다.

Azure 활동 로그 이벤트를 보고 검색하는 방법:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Azure 모니터에서 경고를 만드는 방법:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Azure 보안 센터 모니터링**: 예

**책임**: 고객

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [보안 제어: 로깅 및 모니터링](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)을 참조하십시오.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 소스 사용

**지침**: Microsoft는 Azure 리소스에 대한 시간 원본을 유지 관리하지만 계산 리소스에 대한 시간 동기화 설정을 관리할 수 있는 옵션이 있습니다.

Azure 계산 리소스에 대한 시간 동기화를 구성하는 방법:https://docs.microsoft.com/azure/virtual-machines/windows/time-sync


**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 마이크로 소프트

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: Azure 모니터를 통해 로그를 수집하여 Azure 컨테이너 레지스트리에서 생성된 보안 데이터를 집계합니다. Azure Monitor 내에서 로그 분석 작업 영역을 사용하여 분석을 쿼리하고 수행하고 장기/보관 저장소에 Azure 저장소 계정을 사용합니다.

진단 평가 및 감사를 위한 Azure 컨테이너 레지스트리 로그:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs



**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: Azure Monitor는 레지스트리의 사용자 기반 이벤트에 대한 리소스 로그(이전의 진단 로그)를 수집합니다. 레지스트리 인증 이벤트를 감사하고 끌어오기 및 푸시 이벤트와 같은 레지스트리 아티팩트에 대한 전체 활동 추적을 제공하여 레지스트리의 보안 문제를 진단할 수 있도록 이 데이터를 수집하고 사용합니다.

진단 평가 및 감사를 위한 Azure 컨테이너 레지스트리 로그:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

**지침**: 해당되지 않습니다. 벤치마크는 컴퓨팅 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 저장소 보존 구성

**지침**: Azure Monitor 내에서 조직의 규정 준수 규정에 따라 로그 분석 작업 영역 보존 기간을 설정합니다. 장기/보관 저장소에 Azure 저장소 계정을 사용합니다.

로그 분석 작업 영역에 대한 로그 보존 매개 변수를 설정하는 방법:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: Azure 컨테이너 레지스트리 로그를 분석 및 모니터링하여 비정상적인 동작을 검사하고 결과를 정기적으로 검토합니다. Azure 모니터의 로그 분석 작업 영역을 사용하여 로그를 검토하고 로그 데이터에 대한 쿼리를 수행합니다.

진단 평가 및 감사를 위한 Azure 컨테이너 레지스트리 로그:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

로그 분석 작업 영역 이해:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Azure 모니터에서 사용자 지정 쿼리를 수행하는 방법:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: 비정상적인 활동에 대한 경고 사용

**지침**: Azure Log Analytics 작업 영역을 사용하여 Azure 컨테이너 레지스트리와 관련된 보안 로그 및 이벤트에서 비정상적인 활동을 모니터링하고 경고합니다.

진단 평가 및 감사를 위한 Azure 컨테이너 레지스트리 로그:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

로그 분석 로그 데이터에 대해 경고하는 방법:https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

**지침**: 해당되지 않습니다. Azure 컨테이너 레지스트리는 맬웨어 방지 관련 로그를 처리하거나 생성하지 않습니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

**지침**: 해당되지 않습니다. Azure 컨테이너 레지스트리는 끝점이며 통신을 시작하지 않으며 서비스는 DNS를 쿼리하지 않습니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅 사용

**지침**: 해당되지 않습니다. 벤치마크는 컴퓨팅 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [보안 제어: ID 및 액세스 제어](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)를 참조하십시오.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지

**지침**: Azure Active Directory(Azure AD)에는 명시적으로 할당되어야 하고 쿼리할 수 있는 기본 제공 역할이 있습니다. Azure AD PowerShell 모듈을 사용하여 임시 쿼리를 수행하여 관리 그룹의 구성원인 계정을 검색합니다.

각 Azure 컨테이너 레지스트리에 대해 기본 제공 관리자 계정이 활성화되어 있는지 또는 사용하지 않도록 설정되었는지 추적합니다. 사용하지 않을 때는 계정을 비활성화합니다.

PowerShell을 사용하여 Azure AD에서 디렉터리 역할을 얻는 방법:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

PowerShell을 사용하여 Azure AD에서 디렉터리 역할의 구성원을 얻는 방법:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Azure 컨테이너 레지스트리 관리자 계정:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account


**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 해당하는 경우 기본 암호 변경

**지침**: Azure Active Directory(Azure AD)에는 기본 암호 개념이 없습니다. 암호가 필요한 다른 Azure 리소스는 서비스에 따라 달라보이는 복잡성 요구 사항및 최소 암호 길이로 암호를 만들도록 강제합니다. 기본 암호를 사용할 수 있는 타사 응용 프로그램 및 마켓플레이스 서비스에 대한 책임은 귀하에게 있습니다.

Azure 컨테이너 레지스트리의 기본 관리자 계정이 활성화된 경우 복잡한 암호가 자동으로 만들어지므로 회전해야 합니다. 사용하지 않을 때는 계정을 비활성화합니다.

Azure 컨테이너 레지스트리 관리자 계정:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 전용 관리 계정 사용에 대한 표준 운영 절차를 만듭니다. Azure 보안 센터 ID 및 액세스 관리를 사용하여 관리 계정 수를 모니터링합니다.

또한 컨테이너 레지스트리의 기본 제공 관리자 계정을 사용하도록 설정하는 프로시저를 만듭니다. 사용하지 않을 때는 계정을 비활성화합니다.

Azure 보안 센터 ID 및 액세스 이해:https://docs.microsoft.com/azure/security-center/security-center-identity-access

Azure 컨테이너 레지스트리 관리자 계정:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 단일 사인온(SSO) 사용

**지침**: 가능한 경우 서비스당 개별 독립 실행형 자격 증명을 구성하는 대신 Azure Active Directory SSO를 사용합니다. Azure 보안 센터 ID 및 액세스 관리 권장 사항을 사용합니다.

컨테이너 레지스트리에 대한 개별 액세스의 경우 Azure Active Directory와 통합된 개별 로그인을 사용합니다.

Azure AD를 통해 SSO 이해:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

컨테이너 레지스트리에 대한 개별 로그인:https://docs.microsoft.com/azure/container-registry/container-registry-authentication#individual-login-with-azure-ad


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure Active Directory(Azure AD) 다단계 인증(MFA)을 활성화하고 Azure 보안 센터 ID 및 액세스 관리 권장 사항을 따릅니다.

Azure에서 MFA를 활성화하는 방법:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Azure 보안 센터 내에서 ID 및 액세스를 모니터링하는 방법:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(권한 있는 액세스 워크스테이션) 사용

**지침**: Azure 리소스에 로그인하고 구성하도록 구성된 MFA가 있는 PA(권한 있는 액세스 워크스테이션)를 사용합니다.

권한 있는 액세스 워크스테이션에 대해 자세히 알아보기:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Azure에서 MFA를 활성화하는 방법:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Azure 보안 센터 모니터링**: 해당/A

**책임**: 고객

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 관리 계정의 의심스러운 활동에 대한 로그 및 경고

**지침**: 환경에서 의심스럽거나 안전하지 않은 활동이 발생할 때 로그 및 경고 생성을 위해 Azure Active Directory(Azure AD) 보안 보고서를 사용합니다. Azure 보안 센터를 사용하여 ID 및 액세스 활동을 모니터링합니다.

위험한 활동에 플래그가 지정된 Azure AD 사용자를 식별하는 방법:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Azure 보안 센터에서 사용자의 ID 및 액세스 활동을 모니터링하는 방법:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명된 위치를 사용하여 IP 주소 범위 또는 국가/지역의 특정 논리 그룹에서만 액세스할 수 있습니다.

Azure에서 명명된 위치를 구성하는 방법:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="39-use-azure-active-directory"></a>3.9: Azure 활성 디렉터리 사용

**지침**: Azure Active Directory(Azure AD)를 중앙 인증 및 권한 부여 시스템으로 사용합니다. Azure AD는 미사용 및 전송 중 데이터에 대해 강력한 암호화를 사용하여 데이터를 보호합니다. 또한 Azure AD는 사용자 자격 증명을 솔트, 해시 및 안전하게 저장합니다.

Azure AD 인스턴스를 만들고 구성하는 방법:https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 사용자 액세스를 정기적으로 검토하고 조정

**지침**: Azure Active Directory(Azure AD)는 오래된 계정을 검색하는 데 도움이 되는 로그를 제공합니다. 또한 Azure ID Access Reviews를 사용하여 그룹 구성원 자격, 엔터프라이즈 응용 프로그램에 대한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 사용자 액세스는 정기적으로 검토하여 올바른 사용자만 계속 액세스할 수 있도록 할 수 있습니다.

Azure AD 보고 이해:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Azure ID 액세스 검토를 사용하는 방법:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 비활성화된 계정에 액세스하려는 시도를 모니터링합니다.

**지침**: AZURE Active Directory(Azure AD) 로그인 활동, 감사 및 위험 이벤트 로그 원본에 액세스할 수 있으므로 모든 보안 정보 및 이벤트 관리(SIEM) /모니터링 도구와 통합할 수 있습니다.

Azure Active Directory 사용자 계정에 대한 진단 설정을 만들고 감사 로그 및 로그인 로그를 로그 분석 작업 영역으로 전송하여 이 프로세스를 간소화할 수 있습니다. 로그 분석 작업 영역 내에서 원하는 경고를 구성할 수 있습니다.

Azure 활동 로그를 Azure 모니터에 통합하는 방법:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure Active Directory(Azure AD) 위험 및 ID 보호 기능을 사용하여 사용자 ID와 관련된 의심스러운 작업을 검색하도록 자동화된 응답을 구성합니다. 

Azure AD 위험한 로그인을 보는 방법:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오 중에 Microsoft에 관련 고객 데이터에 대한 액세스 제공

**지침**: 사용할 수 없습니다. Azure 컨테이너 레지스트리에 대해 현재 지원되지 않는 고객 잠금 상자입니다.

고객 잠금 상자 지원 서비스 목록:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [보안 제어: 데이터 보호](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)를 참조하십시오.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 민감한 정보의 인벤토리 유지

**지침:** 리소스 태그를 사용하여 중요한 정보를 저장하거나 처리하는 Azure 컨테이너 레지스트리를 추적합니다.

레지스트리의 컨테이너 이미지 또는 기타 아티팩트에 태그를 붙이거나 버전처리하고 이미지 또는 리포지토리를 잠그면 중요한 정보를 저장하거나 처리하는 이미지를 추적할 수 있습니다.

태그를 만들고 사용하는 방법:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

컨테이너 이미지에 태그 지정 및 버전 지정에 대한 권장 사항:https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version

Azure 컨테이너 레지스트리에서 컨테이너 이미지 잠금:https://docs.microsoft.com/azure/container-registry/container-registry-image-lock



**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 민감한 정보를 저장하거나 처리하는 시스템을 격리

**지침**: 개발, 테스트 및 프로덕션을 위해 별도의 컨테이너 레지스트리, 구독 및/또는 관리 그룹을 구현합니다. 중요한 데이터를 저장하거나 처리하는 리소스는 충분히 격리되어야 합니다.

리소스는 가상 네트워크 또는 서브넷으로 구분하고 적절하게 태그가 지정되고 NSG(네트워크 보안 그룹) 또는 Azure 방화벽에 의해 보호되어야 합니다.

추가 Azure 구독을 만드는 방법:https://docs.microsoft.com/azure/billing/billing-create-subscription

관리 그룹을 만드는 방법:https://docs.microsoft.com/azure/governance/management-groups/create

태그를 만들고 사용하는 방법:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Azure 가상 네트워크 또는 방화벽 규칙을 사용하여 Azure 컨테이너 레지스트리에 대한 액세스를 제한합니다.https://docs.microsoft.com/azure/container-registry/container-registry-vnet

보안 구성을 사용하여 NSG를 만드는 방법:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Azure 방화벽을 배포하는 방법:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Azure 방화벽을 사용하여 경고 또는 경고를 구성하고 거부하는 방법:

https://docs.microsoft.com/azure/firewall/threat-intel



**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 민감한 정보의 무단 전송 모니터링 및 차단

**지침**: 네트워크 경계에 자동화된 도구를 배포하여 중요한 정보의 무단 전송을 모니터링하고 정보 보안 전문가에게 경고하면서 이러한 전송을 차단합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 민감한 것으로 취급하고 고객 데이터 손실 및 노출을 방지하기 위해 많은 시간을 보습니다. Azure 내의 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 제품군을 구현하고 유지 관리합니다.

Azure의 고객 데이터 보호 이해:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 공유

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중의 모든 민감한 정보 암호화

**지침**: Azure 컨테이너 레지스트리에 연결하는 모든 클라이언트가 TLS 1.2 이상을 협상할 수 있는지 확인합니다. Microsoft Azure 리소스는 기본적으로 TLS 1.2를 협상합니다.

해당되는 경우 미사용 암호화 및 전송 중 암호화에 대한 Azure Security Center 권장 사항을 따릅니다.

Azure를 사용한 전송 중 암호화 이해:https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit



**Azure 보안 센터 모니터링**: 예

**책임**: 공유

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: Azure 컨테이너 레지스트리에서 데이터 식별, 분류 및 손실 방지 기능을 아직 사용할 수 없습니다. 규정 준수를 위해 필요한 경우 타사 솔루션을 구현합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 민감한 것으로 취급하고 고객 데이터 손실 및 노출을 방지하기 위해 많은 시간을 보습니다. Azure 내의 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 제품군을 구현하고 유지 관리합니다.

Azure의 고객 데이터 보호 이해:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 공유

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스를 제어합니다.

**지침**: Azure Active Directory(Azure AD) RBAC를 사용하여 Azure 컨테이너 레지스트리의 데이터 및 리소스에 대한 액세스를 제어합니다. 

Azure에서 RBAC를 구성하는 방법:https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Azure 컨테이너 레지스트리 역할 및 권한:https://docs.microsoft.com/azure/container-registry/container-registry-roles



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

**지침**: 컴퓨팅 리소스를 준수하는 데 필요한 경우 자동화된 호스트 기반 데이터 손실 방지 솔루션과 같은 타사 도구를 구현하여 시스템에서 데이터를 복사하는 경우에도 데이터에 대한 액세스 제어를 적용합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 민감한 것으로 취급하고 고객 데이터 손실 및 노출을 방지하기 위해 많은 시간을 보습니다. Azure 내의 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 제품군을 구현하고 유지 관리합니다.

Azure의 고객 데이터 보호 이해:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 공유

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 미사용 정보 암호화

**지침**: 모든 Azure 리소스에서 미사용 암호화를 사용합니다. 기본적으로 Azure 컨테이너 레지스트리의 모든 데이터는 Microsoft 관리 키를 사용하여 미사용 으로 암호화됩니다.

Azure의 미사용 암호화 이해:https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Azure 컨테이너 레지스트리에서 고객 관리 키:https://aka.ms/acr/cmk



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 사항에 대한 로그 및 경고

**지침**: Azure Monitor는 레지스트리의 사용자 기반 이벤트에 대한 리소스 로그(이전의 진단 로그)를 수집합니다. 레지스트리 인증 이벤트를 감사하고 끌어오기 및 끌어오기 이벤트와 같은 레지스트리 아티팩트에 대한 전체 활동 추적을 제공하여 레지스트리의 운영 문제를 진단할 수 있도록 이 데이터를 수집하고 사용합니다.

진단 평가 및 감사를 위한 Azure 컨테이너 레지스트리 로그:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

## <a name="vulnerability-management"></a>취약성 관리

*자세한 내용은 [보안 제어: 취약점 관리](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)를 참조하십시오.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약점 검색 도구 실행

**지침**: 컨테이너 이미지에 대한 취약성 평가를 수행하는 Azure 보안 센터의 권장 사항을 따릅니다. Azure Marketplace에서 타사 솔루션을 선택적으로 배포하여 이미지 취약성 평가를 수행합니다.

Azure 보안 센터 취약성 평가 권장 사항을 구현하는 방법:https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

보안 센터(미리 보기)와의 Azure 컨테이너 레지스트리 통합:https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: 자동화된 운영 체제 패치 관리 솔루션 배포

**지침**: Microsoft는 Azure 컨테이너 레지스트리를 지원하는 기본 시스템에서 패치 관리를 수행합니다.

운영 체제 및 기타 패치의 기본 이미지에 대한 업데이트가 감지되면 컨테이너 이미지 업데이트를 자동화합니다.

Azure 컨테이너 레지스트리 작업에 대한 기본 이미지 업데이트 소개:https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: 자동화된 타사 소프트웨어 패치 관리 솔루션 배포

**지침**: 타사 솔루션을 사용하여 응용 프로그램 이미지를 패치할 수 있습니다.  또한 Azure 컨테이너 레지스트리 작업을 실행하여 기본 이미지의 보안 패치 또는 기타 업데이트를 기반으로 컨테이너 레지스트리에서 응용 프로그램 이미지에 대한 업데이트를 자동화할 수 있습니다.

ACR 작업에 대한 기본 이미지 업데이트 소개:https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 백투백 취약점 검사 비교

**지침**: Azure 컨테이너 레지스트리(ACR)를 Azure 보안 센터와 통합하여 취약점에 대한 컨테이너 이미지를 주기적인 검색할 수 있습니다. Azure Marketplace에서 타사 솔루션을 선택적으로 배포하여 정기적인 이미지 취약성 검사를 수행합니다.

보안 센터(미리 보기)와의 Azure 컨테이너 레지스트리 통합:https://docs.microsoft.com/azure/security-center/azure-container-registry-integration


**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 발견된 취약점의 문제 해결에 우선 순위를 지정합니다.

**지침**: Azure 컨테이너 레지스트리(ACR)를 Azure 보안 센터와 통합하여 취약성에 대한 컨테이너 이미지를 주기적인 검색으로 설정하고 위험을 분류합니다. Azure Marketplace에서 타사 솔루션을 선택적으로 배포하여 정기적인 이미지 취약성 검사 및 위험 분류를 수행합니다.

보안 센터(미리 보기)와의 Azure 컨테이너 레지스트리 통합:https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Azure 보안 센터 모니터링**: 해당/A

**책임**: 고객

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [보안 관리: 인벤토리 및 자산 관리를](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)참조하십시오.*

### <a name="61-use-azure-asset-discovery"></a>6.1: Azure 자산 검색 사용

**지침**: Azure 리소스 그래프를 사용하여 구독 내의 모든 리소스(예: 계산, 저장소, 네트워크, 포트 및 프로토콜 등)를 쿼리/검색합니다.  테넌트의 적절한(읽기) 권한을 확인하고 구독 내의 모든 Azure 구독및 리소스를 등록합니다.

리소스 그래프를 통해 클래식 Azure 리소스를 검색할 수 있지만 앞으로 Azure 리소스 관리자 리소스를 만들고 사용하는 것이 좋습니다.

Azure 리소스 그래프를 사용하여 쿼리를 만드는 방법:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Azure 구독을 보는 방법:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0 

Azure RBAC 이해:https://docs.microsoft.com/azure/role-based-access-control/overview



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: Azure 컨테이너 레지스트리는 레지스트리의 이미지에 대한 태그 및 매니페스트를 포함한 메타데이터를 유지 관리합니다. 아티팩트 에 태그를 지정하는 데 권장하는 방법을 따릅니다.

레지스트리, 리포지토리 및 이미지 에 대해:https://docs.microsoft.com/azure/container-registry/container-registry-concepts

컨테이너 이미지에 태그 지정 및 버전 지정에 대한 권장 사항:https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 승인되지 않은 Azure 리소스 삭제

**지침**: Azure 컨테이너 레지스트리는 레지스트리의 이미지에 대한 태그 및 매니페스트를 포함한 메타데이터를 유지 관리합니다. 아티팩트 에 태그를 지정하는 데 권장하는 방법을 따릅니다.

레지스트리, 리포지토리 및 이미지 에 대해:https://docs.microsoft.com/azure/container-registry/container-registry-concepts

컨테이너 이미지에 태그 지정 및 버전 지정에 대한 권장 사항:https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: 승인된 Azure 리소스 및 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: 조직의 필요에 따라 승인된 Azure 리소스의 인벤토리를 만들어야 합니다.  

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure 정책을 사용하여 구독에서 만들 수 있는 리소스 유형에 제한을 두십시오.

Azure 리소스 그래프를 사용하여 구독 내에서 리소스를 쿼리/검색합니다.  환경에 있는 모든 Azure 리소스가 승인되었는지 확인합니다.

Azure 정책을 사용하여 Azure 컨테이너 레지스트리의 준수 를 감사합니다.https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Azure 정책을 구성하고 관리하는 방법:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure 그래프를 사용하여 쿼리를 만드는 방법:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

**지침**: Azure 컨테이너 레지스트리 로그를 분석 및 모니터링하여 비정상적인 동작을 검사하고 결과를 정기적으로 검토합니다. Azure 모니터의 로그 분석 작업 영역을 사용하여 로그를 검토하고 로그 데이터에 대한 쿼리를 수행합니다.

진단 평가 및 감사를 위한 Azure 컨테이너 레지스트리 로그:https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

로그 분석 작업 영역 이해:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Azure 모니터에서 사용자 지정 쿼리를 수행하는 방법:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 응용 프로그램 제거

**지침**: Azure Automation은 워크로드 및 리소스의 배포, 작업 및 서비스 해제 중에 완전한 제어를 제공합니다.  권한이 승인되지 않은 Azure 리소스를 제거하기 위한 고유한 솔루션을 구현할 수 있습니다. Azure 자동화에 대한 소개:https://docs.microsoft.com/azure/automation/automation-intro


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="68-use-only-approved-applications"></a>6.8: 승인된 신청서만 사용

**지침**: 해당되지 않습니다. 벤치마크는 컴퓨팅 리소스를 위해 설계되었습니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침:** Azure 정책을 활용하여 사용자 환경에서 프로비전할 수 있는 서비스를 제한합니다.

Azure 정책을 사용하여 Azure 컨테이너 레지스트리의 준수 를 감사합니다.https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Azure 정책을 구성하고 관리하는 방법:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure 정책을 사용하여 특정 리소스 유형을 거부하는 방법:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="610-implement-approved-application-list"></a>6.10: 승인된 신청 목록 구현

**지침**: 해당되지 않습니다. 벤치마크는 컴퓨팅 리소스를 위해 설계되었습니다.



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: 스크립트를 통해 AzureResources 관리자와 상호 작용하는 사용자의 기능 제한

**지침:** 운영 체제별 구성 또는 타사 리소스를 사용하여 Azure 계산 리소스 내에서 스크립트를 실행하는 사용자의 기능을 제한합니다.

Azure 리소스 관리자에 대한 액세스를 차단하기 위해 조건부 액세스를 구성하는 방법:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 컴퓨팅 리소스 내에서 스크립트를 실행하는 사용자의 기능 제한

**지침:** 운영 체제별 구성 또는 타사 리소스를 사용하여 Azure 계산 리소스 내에서 스크립트를 실행하는 사용자의 기능을 제한합니다.

예를 들어 Windows 환경에서 PowerShell 스크립트 실행을 제어하는 방법은 다음과 같습니다.https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 물리적 또는 논리적으로 고위험 응용 프로그램을 분리

**지침:** 비즈니스 작업에 필요하지만 조직에 더 높은 위험이 발생할 수 있는 소프트웨어는 자체 가상 시스템 및/또는 가상 네트워크 내에서 격리되어야 하며 Azure 방화벽 또는 네트워크 보안 그룹으로 충분히 보호되어야 합니다.

가상 네트워크를 만드는 방법:https://docs.microsoft.com/azure/virtual-network/quick-create-portal

보안 구성을 사용하여 NSG를 만드는 방법:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [보안 제어: 보안 구성](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)을 참조하십시오.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure 정책 또는 Azure 보안 센터를 사용하여 모든 Azure 리소스에 대한 보안 구성을 유지 관리합니다.

Azure 정책을 구성하고 관리하는 방법:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure 정책을 사용하여 Azure 컨테이너 레지스트리의 준수 를 감사합니다.https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정

**지침**: Azure 보안 센터 권장 사항 "가상 시스템의 보안 구성에서 취약성 해결"을 활용하여 모든 계산 리소스에서 보안 구성을 유지 관리합니다.

Azure 보안 센터 권장 사항을 모니터링하는 방법:https://docs.microsoft.com/azure/security-center/security-center-recommendations

Azure 보안 센터 권장 사항을 수정하는 방법:https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 안전한 Azure 리소스 구성 유지

**지침**: Azure 정책 [거부] 및 [존재하지 않는 경우 배포]를 사용하여 Azure 리소스 간에 보안 설정을 적용합니다.

Azure 정책을 사용하여 Azure 컨테이너 레지스트리의 준수 를 감사합니다.https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Azure 정책을 구성하고 관리하는 방법:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure 정책 효과 이해:https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 안전한 운영 체제 구성 유지

**지침**: 해당되지 않습니다. 벤치마크는 컴퓨팅 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 공유

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스의 구성을 안전하게 저장

**지침**: 사용자 지정 Azure 정책 정의를 사용하는 경우 Azure Repos를 사용하여 코드를 안전하게 저장하고 관리합니다.

Azure DevOps에 코드를 저장하는 방법:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure 리포지토리 설명서:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장

**지침**: 해당되지 않습니다. 벤치마크는 계산 리소스에 적용됩니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: 시스템 구성 관리 도구 배포

**지침**: Azure 정책을 사용하여 시스템 구성에 대해 경고, 감사 및 적용합니다. 또한 정책 예외를 관리하기 위한 프로세스 및 파이프라인을 개발합니다.

Azure 정책을 사용하여 Azure 컨테이너 레지스트리의 준수 를 감사합니다.https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

Azure 정책을 구성하고 관리하는 방법:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 시스템 구성 관리 도구 배포

**지침**: 해당되지 않습니다. 벤치마크는 계산 리소스에 적용됩니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Azure 서비스에 대한 자동화된 구성 모니터링 구현

**지침**: Azure 보안 센터를 사용하여 Azure 리소스에 대한 기준 검사를 수행합니다.

Azure 정책을 사용하여 구독에서 만들 수 있는 리소스 유형에 제한을 두십시오.

Azure 보안 센터에서 권장 사항을 수정하는 방법:https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

Azure 정책을 사용하여 Azure 컨테이너 레지스트리의 준수 를 감사합니다.https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제를 위한 자동화된 구성 모니터링 구현

**지침**: 해당되지 않습니다. 벤치마크는 계산 리소스에 적용됩니다.


**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure 비밀을 안전하게 관리

**지침**: Azure Key Vault와 함께 관리되는 서비스 ID를 사용하여 클라우드 응용 프로그램에 대한 비밀 관리를 단순화하고 보호합니다.

Azure 관리 ID와 통합하는 방법:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

키 볼트를 만드는 방법:https://docs.microsoft.com/azure/key-vault/quick-create-portal

관리되는 ID로 키 볼트 인증을 제공하는 방법:https://docs.microsoft.com/azure/key-vault/managed-identity

Azure 컨테이너 레지스트리 작업에서 Azure 관리 ID를 사용합니다.https://docs.microsoft.com/azure/container-registry/container-registry-tasks-authentication-managed-identity


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: ID를 안전하고 자동으로 관리

**지침**: 관리되는 ID를 사용하여 Azure AD에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 관리되는 ID를 사용하면 코드의 자격 증명 없이 키 볼트를 포함하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있습니다.

관리되는 ID를 구성하는 방법:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

관리되는 ID를 사용하여 Azure 컨테이너 레지스트리를 인증합니다.https://docs.microsoft.com/azure/container-registry/container-registry-authentication-managed-identity


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

**지침**: Azure 클라우드 서비스 및 가상 시스템에 대한 Microsoft 맬웨어 방지를 사용하여 리소스를 지속적으로 모니터링하고 방어합니다. Linux의 경우 타사 맬웨어 방지 솔루션을 사용합니다.

클라우드 서비스 및 가상 컴퓨터에 대 한 Microsoft 맬웨어 방지를 구성 하는 방법:https://docs.microsoft.com/azure/security/fundamentals/antimalware


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비계산 Azure 리소스에 업로드할 파일을 미리 스캔합니다.

**지침**: Microsoft 맬웨어 방지는 Azure 서비스(예: Azure 컨테이너 레지스트리)를 지원하는 기본 호스트에서 활성화되지만 고객 콘텐츠에서는 실행되지 않습니다.

앱 서비스, 데이터 레이크 저장소, Blob 저장소 등과 같이 계산되지 않은 Azure 리소스에 업로드되는 파일을 미리 검사합니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명이 업데이트되었는지 확인

**지침**: 해당되지 않습니다. 벤치마크는 컴퓨팅 리소스를 위한 것입니다. Microsoft는 기본 플랫폼에 대한 맬웨어 방지를 처리합니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [보안 제어: 데이터 복구](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)를 참조하십시오.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 정기적인 자동 백업 보장

**지침:** Microsoft Azure 컨테이너 레지스트리의 데이터는 항상 자동으로 복제되어 내구성과 고가용성을 보장합니다. Azure 컨테이너 레지스트리는 계획되고 계획되지 않은 이벤트로부터 보호되도록 데이터를 복사합니다.

선택적으로 여러 Azure 지역에서 레지스트리 복제본을 유지 관리하려면 컨테이너 레지스트리를 지리적으로 복제합니다. 

Azure 컨테이너 레지스트리의 지역 복제:https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업 수행 및 고객 관리 키 백업

**지침**: 한 레지스트리에서 다른 레지스트리로 가져와서 컨테이너 이미지를 선택적으로 백업합니다.

Azure 명령줄 도구 또는 SDK를 사용하여 Azure 키 자격 증명 모음에서 고객 관리 키를 백업합니다.

컨테이너 이미지를 컨테이너 레지스트리로 가져오기:https://docs.microsoft.com/azure/container-registry/container-registry-import-images

Azure에서 키 자격 증명 모음 키를 백업하는 방법:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 포함한 모든 백업의 유효성 검사

**지침**: Azure 명령줄 도구 또는 SDK를 사용하여 Azure 키 자격 증명 모음에서 백업된 고객 관리 키의 복원을 테스트합니다.

Azure에서 Azure 키 볼트 키를 복원 하는 방법:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리 키 보호 보장

**지침**: Azure 키 자격 증명 모음에서 소프트 삭제를 사용하도록 설정하여 실수로 또는 악의적인 삭제로부터 키를 보호할 수 있습니다.

키 자격 증명 모음에서 소프트 삭제를 사용하도록 설정하는 방법:https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [보안 제어: 인시던트 대응](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)을 참조하십시오.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 사고 대응 가이드 만들기

**지침**: 조직에 대한 인시던트 대응 가이드를 작성합니다. 감지에서 사후 검토에 이르는 사고 처리/관리 단계뿐만 아니라 직원의 모든 역할을 정의하는 서면 인시던트 대응 계획이 있는지 확인합니다.

Azure 보안 센터 내에서 워크플로 자동화를 구성하는 방법:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

자체 보안 인시던트 대응 프로세스 구축에 대한 지침:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Microsoft 보안 대응 센터의 사고 해부학:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

고객은 NIST의 컴퓨터 보안 사고 처리 가이드를 활용하여 자체 사고 대응 계획을 수립할 수 있습니다.https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 채점 및 우선 순위 지정 절차 만들기

**지침**: Azure Security Center는 각 경고에 심각도를 할당하여 먼저 조사할 경고의 우선 순위를 지정합니다. 심각도는 보안 센터가 찾기 또는 경고를 발행하는 데 사용되는 분석 및 경고로 이어진 활동의 배후에 악의적인 의도가 있다는 신뢰 수준에 얼마나 확신을 가지고 있는지에 따라 다집니다.

또한 구독을 명확하게 표시합니다(ex. Azure 리소스를 명확하게 식별하고 분류하는 명명 시스템을 만듭니다.


**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 절차 테스트

**지침**: 정기적으로 시스템의 사고 대응 기능을 테스트하기 위한 연습을 수행합니다. 약점과 격차를 식별하고 필요에 따라 계획을 수정합니다.

NIST의 간행물: IT 계획 및 기능에 대한 테스트, 교육 및 운동 프로그램 가이드를 참조하십시오.https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침:** Microsoft 보안 대응 센터(MSRC)에서 고객의 데이터가 불법적이거나 승인되지 않은 당사자가 액세스한 사실을 발견한 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 연락합니다.  사실 이후에 인시던트를 검토하여 문제가 해결되었는지 확인합니다.

Azure 보안 센터 보안 연락처를 설정 하는 방법:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 사고 대응 시스템에 통합

**지침**: 지속적인 내보내기 기능을 사용하여 Azure 보안 센터 경고 및 권장 사항을 내보냅니다. 연속 내보내기를 사용하면 수동으로 또는 지속적으로 지속방식으로 경고 및 권장 사항을 내보낼 수 있습니다. Azure 보안 센터 데이터 커넥터를 사용하여 Sentinel 경고를 스트리밍할 수 있습니다.

연속 내보내기를 구성하는 방법:https://docs.microsoft.com/azure/security-center/continuous-export

Azure Sentinel로 경고를 스트리밍하는 방법:https://docs.microsoft.com/azure/sentinel/connect-azure-security-center


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 응답 자동화

**지침**: Azure 보안 센터의 워크플로 자동화 기능을 사용하여 보안 경고 및 권장 사항에 대한 "논리 앱"을 통해 응답을 자동으로 트리거합니다.

워크플로 자동화 및 논리 앱을 구성하는 방법:https://docs.microsoft.com/azure/security-center/workflow-automation


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [보안 제어: 침투 테스트 및 빨간색 팀 연습을](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)참조하십시오.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Azure 리소스에 대한 정기적인 침투 테스트를 수행하고 60일 이내에 모든 중요한 보안 결과의 수정을 보장합니다.

**지침**: 보급 시험이 Microsoft 정책을 위반하지 않도록 Microsoft 참여 규칙을 따르십시오.https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Microsoft에서 관리하는 클라우드 인프라, 서비스 및 응용 프로그램에 대한 Red Teaming 및 실시간 사이트 침투 테스트의 전략 및 실행에 대한 자세한 내용은 여기에서 확인할 수 있습니다.https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 공유

## <a name="next-steps"></a>다음 단계

- Azure [보안 벤치마크](https://docs.microsoft.com/azure/security/benchmarks/overview) 보기
- [Azure 보안 기준에](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview) 대해 자세히 알아보기
