---
title: Batch에 대 한 Azure 보안 기준
description: Batch 보안 기준은 Azure 보안 벤치 마크에 지정 된 보안 권장 사항을 구현 하기 위한 절차 지침과 리소스를 제공 합니다.
author: msmbaldwin
ms.service: batch
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: d8ef3efcda55868abec188c10ef904ae40a56722
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728147"
---
# <a name="azure-security-baseline-for-batch"></a>Batch에 대 한 Azure 보안 기준

이 보안 기준은 [Azure 보안 벤치 마크 버전 1.0](../security/benchmarks/overview-v1.md) 의 지침을 Batch에 적용 합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다.
콘텐츠는 Azure 보안 벤치 마크에 정의 된 **보안 컨트롤** 및 Batch에 적용 되는 관련 지침에 따라 그룹화 됩니다. 일괄 처리에 적용 되지 않는 **컨트롤** 은 제외 되었습니다.

 
Batch가 Azure 보안 벤치 마크에 완전히 매핑되는 방식을 보려면 [전체 batch 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조 하세요.

## <a name="network-security"></a>네트워크 보안

자세한 내용은 [Azure Security Benchmark: 네트워크 보안](../security/benchmarks/security-control-network-security.md)을 참조하세요.

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: 가상 네트워크 내에 Azure Batch 풀을 배포 합니다. 풀 계산 노드가 다른 가상 머신 또는 온-프레미스 네트워크와 안전 하 게 통신 하도록 허용 하려면 Azure 가상 네트워크의 서브넷에서 풀을 프로 비전 할 수 있습니다. 또한 가상 네트워크 내에서 풀을 배포 하면 개별 노드의 NIC (네트워크 인터페이스) 및 서브넷을 보호 하는 데 사용 되는 NSG (네트워크 보안 그룹)를 제어할 수 있습니다. 인터넷의 신뢰할 수 있는 IP/위치 에서만 트래픽을 허용 하도록 NSG를 구성 합니다.

해당 하는 경우 개인 끝점을 통해 Azure Batch 계정에 연결 하려면 Azure 개인 링크를 사용 하 여 공용 네트워크 액세스를 사용 하지 않도록 설정 합니다. Azure 개인 링크 서비스는 보안 되며 인증 되 고 권한이 부여 된 개인 끝점의 연결만 허용 합니다. Batch 풀의 계산 노드에 대해 공개적으로 노출 된 RDP/SSH 끝점을 사용 하지 않도록 설정 하 여 연결 및 검색 기능을 추가로 제한할 수 있습니다.

- [Virtual Network 내에서 Azure Batch 풀을 만드는 방법](batch-virtual-network.md)

- [네트워크 액세스가 사용 하지 않도록 설정 된 Azure Batch 계정을 만드는 방법](private-connectivity.md)

- [개인 끝점을 만드는 방법](../private-link/create-private-endpoint-portal.md)

- [RDP/SSH 트래픽에 대 한 액세스를 거부 하는 방법](pool-endpoint-configuration.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: 가상 네트워크, 서브넷 및 네트워크 인터페이스의 구성 및 트래픽을 모니터링 하 고 기록 합니다.

**지침**: Azure Security Center을 사용 하 고 Batch 풀과 연결 된 nsg (가상 네트워크/네트워크 보안 그룹)와 관련 된 네트워크 보호 권장 사항을 수정 합니다. Batch 풀을 보호 하는 데 사용 되는 NSG에 대 한 흐름 로그를 사용 하도록 설정 하 고, 트래픽 감사를 위해 Azure Storage 계정으로 로그를 보냅니다. Azure Log Analytics 작업 영역에 NSG 흐름 로그를 보내고 azure 트래픽 분석를 사용 하 여 Azure 클라우드의 트래픽 흐름에 대 한 통찰력을 제공할 수도 있습니다. Azure 트래픽 분석의 장점 중 일부는 네트워크 활동을 시각화 하 고 핫 스폿을 식별 하 고, 보안 위협을 식별 하 고, 트래픽 흐름 패턴을 이해 하 고, 네트워크 잘못 된 구성을 파악 하는 기능입니다.

- [NSG 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [트래픽 분석을 사용하도록 설정하고 사용하는 방법](../network-watcher/traffic-analytics.md)

- [Azure Security Center에서 제공 하는 네트워크 보안 이해](../security-center/security-center-network-recommendations.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: DDoS 공격 으로부터 보호 하기 위해 Azure Batch 풀을 보호 하는 가상 네트워크에서 Azure DDoS (배포 된 서비스 거부) 표준 보호를 사용 하도록 설정 합니다. Azure Security Center 통합 위협 인텔리전스를 사용하여 알려진 악성 인터넷 IP 주소 또는 사용하지 않는 인터넷 IP 주소와의 통신을 거부합니다.

- [DDoS 보호를 구성 하는 방법](../ddos-protection/manage-ddos-protection.md)

- [Azure Security Center 통합 위협 인텔리전스 이해](../security-center/azure-defender.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷을 기록 합니다.

**지침**: Azure Batch 풀을 보호 하는 데 사용 되는 nsg (네트워크 보안 그룹)에서 흐름 로그를 사용 하도록 설정 하 고, 트래픽 감사를 위해 Azure Storage 계정에 로그를 보냅니다.

- [NSG 흐름 로그를 사용하도록 설정하는 방법](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 침입 감지/침입 방지 시스템 (IDS/IPS) 배포

**지침**: 규정 준수를 위해 필요한 경우에는 페이로드 검사 기능을 사용 하는 id (침입 감지 시스템) 및 ip (침입 방지 시스템) 기능을 지 원하는 Azure Marketplace에서 네트워크 가상 어플라이언스를 선택 합니다.

페이로드 검사를 기반으로 하는 침입 탐지 및/또는 방지 기능이 반드시 필요한 것은 아닙니다. 위협 인텔리전스를 포함 하는 Azure 방화벽을 사용할 수 있습니다. Azure 방화벽 위협 인텔리전스 기반 필터링은 알려진 악성 IP 주소 및 도메인과의 트래픽을 경고 하 고 거부할 수 있습니다. IP 주소 및 도메인은 Microsoft 위협 인텔리전스 피드에서 제공됩니다.

Azure Batch 풀 노드와 동일한 가상 네트워크에 공용 IP 주소를 사용 하 여 Azure 방화벽을 배포 합니다. 인터넷의 신뢰할 수 있는 위치와 개별 풀 노드의 개인 IP 주소 간에 NAT (network address translation) 규칙을 구성 합니다. Azure 방화벽의 위협 인텔리전스에서 "경고 및 거부"를 구성 하 여 알려진 악성 IP 주소 및 도메인 간에 들어오고 나가는 트래픽을 차단 합니다. IP 주소 및 도메인은 Microsoft 위협 인텔리전스 피드에서 소싱 되며 가장 높은 신뢰도 레코드만 포함 됩니다. 

- [Virtual Network 내에서 Azure Batch 풀을 만드는 방법](batch-virtual-network.md)

- [Azure 방화벽을 배포 하는 방법](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: 가상 네트워크 서비스 태그를 사용 하 여 Azure Batch 풀과 연결 된 네트워크 보안 그룹 또는 Azure 방화벽에서 네트워크 액세스 제어를 정의 합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 서비스 태그 이름(예: ApiManagement)을 규칙의 적절한 원본 또는 대상 필드에 지정하면 해당 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다. Microsoft는 서비스 태그에 포함되는 주소 접두사를 관리하고 주소가 변경되면 서비스 태그를 자동으로 업데이트합니다.

- [서비스 태그 이해 및 사용](../virtual-network/service-tags-overview.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지 관리

**지침**: Azure Policy를 사용 하 여 Azure Batch 풀과 연결 된 네트워크 리소스에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "Microsoft.Batch" 및 "Microsoft. Network" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 사용자 지정 정책을 만들어 Azure Batch 풀의 네트워크 구성을 감사 하거나 적용 합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10: 트래픽 구성 규칙 문서화

**지침**: Azure batch 풀과 연결 된 네트워크 보안 및 트래픽 흐름과 관련 된 nsgs (네트워크 서비스 그룹) 및 기타 리소스에 대 한 태그를 사용 합니다. 개별 NSG 규칙의 경우 "설명" 필드를 사용하여 네트워크에서 주고 받는 트래픽을 허용하는 모든 규칙에 대한 비즈니스 요구 및/또는 기간(등)을 지정합니다.

태그를 사용 하 여 모든 리소스를 만들고 태그가 지정 되지 않은 기존 리소스를 알리도록 하려면 태그 지정과 관련 된 기본 제공 Azure Policy 정의 (예: "태그 및 해당 값 필요")를 사용 합니다.

Azure PowerShell 또는 Azure CLI를 사용하여 태그를 기준으로 리소스에 대한 작업을 조회하거나 수행할 수 있습니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

- [가상 네트워크를 만드는 방법](../virtual-network/quick-create-portal.md)

- [NSG를 만드는 방법](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure 활동 로그를 사용 하 여 네트워크 리소스 구성을 모니터링 하 고 Azure Batch 풀과 관련 된 네트워크 리소스에 대 한 변경 내용을 검색 합니다. Azure Monitor 내에서 중요한 네트워크 리소스가 변경되면 트리거되는 경고를 만듭니다.

- [Azure 활동 로그 이벤트를 확인하고 검색하는 방법](../azure-monitor/essentials/activity-log.md#view-the-activity-log) 

- [Azure Monitor에서 경고를 만드는 방법](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure 보안 벤치 마크: 로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 참조 하세요.*

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: 클러스터 장치에 의해 생성 된 보안 데이터를 집계 하는 Azure Monitor에 Azure Batch 계정을 등록 합니다. 사용자 지정 쿼리를 활용 하 여 환경에서 위협을 감지 하 고 대응 합니다.  리소스 수준 모니터링 Azure Batch 경우 Batch Api를 사용 하 여 작업, 태스크, 노드 및 풀을 포함 하 여 리소스의 상태를 모니터링 하거나 쿼리 합니다.

- [Azure Batch 계정을 Azure Monitor에 등록 하는 방법](batch-diagnostics.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: 계정 수준 모니터링을 Azure Batch Azure Monitor의 기능을 사용 하 여 각 Batch 계정을 모니터링 합니다. Azure Monitor는 Batch 계정 수준에서 범위가 지정된 풀, 작업, 태스크 등의 리소스에 대한 메트릭, 그리고 필요에 따라 진단 로그를 수집합니다. 이 데이터를 수동으로 또는 프로그래밍 방식으로 수집하고 사용하여 Batch 계정의 활동을 모니터링하고 문제를 진단할 수 있습니다.

리소스 수준 모니터링을 Azure Batch Azure Batch Api를 사용 하 여 작업, 작업, 노드 및 풀을 포함 하 여 리소스의 상태를 모니터링 하거나 쿼리 합니다.

- [계정 수준 모니터링 및 로깅을 Azure Batch 구성 하는 방법](monitoring-overview.md)

- [일괄 처리 리소스 수준 모니터링 이해](monitoring-overview.md#batch-resource-monitoring)

**Azure Security Center 모니터링**: 예

**책임**: Customer

#### <a name="azure-policy-built-in-definitions"></a>기본 제공 정의 Azure Policy

[!INCLUDE [microsoft.batch-2-3](../../includes/policy/standards/asb/rp-controls/microsoft.batch-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

**지침**: Azure Monitor Azure Batch 계정의 리소스에 대 한 메트릭 및 진단 로그를 수집 합니다. 다양 한 방법으로이 데이터를 수집 하 고 사용 하 여 Azure Batch 계정을 모니터링 하 고 문제를 진단할 수 있습니다. 또한 메트릭이 지정된 값에 도달할 때 알림을 받을 수 있도록 메트릭 경고를 구성할 수 있습니다.

필요한 경우 SSH (보안 셸) 또는 RDP (원격 데스크톱 프로토콜)를 통해 개별 풀 노드에 연결 하 여 로컬 운영 체제 로그에 액세스할 수 있습니다.

- [Azure Batch 계정에서 진단 로그를 수집 하는 방법](batch-diagnostics.md#batch-diagnostics)

- [Azure Batch 풀 노드에 원격으로 연결 하는 방법](./batch-service-workflow-features.md#basic-workflow)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

**지침**: Azure Monitor에 Azure Batch 계정을 등록 합니다. 사용 되는 Azure Log Analytics 작업 영역에 조직의 규정 준수 규정에 따라 설정 된 로그 보존 기간이 있는지 확인 합니다.

- [Azure Batch 모니터링 및 로깅을 구성 하는 방법](monitoring-overview.md)

- [Azure Log Analytics 작업 영역 보존 기간을 구성 하는 방법](../azure-monitor/logs/manage-cost-storage.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그를 모니터링 하 고 검토 합니다.

**지침**: 지정 된 메트릭의 값이 지정 된 임계값을 초과할 때 트리거되는 Azure Batch 메트릭 경고를 만듭니다.

- [Azure Batch 메트릭 경고를 구성 하는 방법](batch-diagnostics.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상적인 활동에 대해 경고를 사용 하도록 설정

**지침**: 지정 된 메트릭의 값이 지정 된 임계값을 초과할 때 트리거되는 Azure Batch 메트릭 경고를 만듭니다.

- [Azure Batch 메트릭 경고를 구성 하는 방법](batch-diagnostics.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

**지침**: windows 운영 체제의 경우 개별 Batch 노드에서 windows Defender를 사용 하거나 Linux를 사용 하는 경우 고유한 맬웨어 방지 솔루션을 제공 합니다.

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

**지침**: DNS 로깅을 위한 타사 솔루션을 구현 합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅 사용

**지침**: 노드 단위로 콘솔 로깅 및 PowerShell 기록을 수동으로 구성 합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [Azure 보안 벤치 마크: id 및 Access Control](../security/benchmarks/security-control-identity-access-control.md)을 참조 하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지 관리

**지침**: 사용자가 만드는 다른 계정 뿐만 아니라 Azure Batch 풀을 프로 비전 하는 동안 생성 되는 로컬 관리자 계정에 대 한 기록을 유지 관리 합니다. 또한 Azure Active Directory 통합이 사용 되는 경우 Azure AD에는 명시적으로 할당 되어야 하 고 따라서 쿼리할 수 있는 기본 제공 역할이 있습니다. Azure AD PowerShell 모듈을 사용 하 여 임시 쿼리를 수행 하 여 관리 그룹의 구성원 인 계정을 검색 합니다.

또한 Azure Security Center Id 및 액세스 관리 권장 사항을 사용할 수 있습니다.

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할을 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

- [Azure Security Center를 사용 하 여 id 및 액세스를 모니터링 하는 방법](../security-center/security-center-identity-access.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 기본 암호 변경(해당하는 경우)

**지침**: Azure Batch 풀을 프로 비전 할 때 로컬 컴퓨터 계정을 만드는 옵션이 제공 됩니다. 변경할 기본 암호는 없지만, SSH (보안 셸) 및 RDP (원격 데스크톱 프로토콜) 액세스에 대해 다른 암호를 지정할 수 있습니다. Azure Batch 풀이 구성 된 후에는 Azure Portal 내에서 또는 Azure Resource Manager API를 통해 개별 노드에 대해 임의의 사용자를 생성할 수 있습니다.

- [특정 계산 노드에 사용자를 추가 하는 방법](/rest/api/batchservice/computenode/adduser)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: Azure Active Directory를 사용 하 여 Azure Batch 응용 프로그램에 대 한 인증을 통합 합니다. 전용 관리 계정의 사용에 대 한 정책과 절차를 만듭니다.

또한 Azure Security Center Id 및 액세스 관리 권장 사항을 사용할 수 있습니다.

- [Azure Active Directory를 사용 하 여 Batch 응용 프로그램을 인증 하는 방법](batch-aad-auth.md)

- [Azure Security Center를 사용 하 여 id 및 액세스를 모니터링 하는 방법](../security-center/security-center-identity-access.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 대해 multi-factor authentication을 사용 하십시오.

**지침**: Azure Active Directory를 사용 하 여 Azure Batch 응용 프로그램에 대 한 인증을 통합 합니다. Azure AD 다단계 인증을 사용 하도록 설정 하 고 Azure Security Center Id 및 액세스 관리 권장 사항을 따릅니다.

 

- [Azure에서 다단계 인증을 사용 하도록 설정 하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../security-center/security-center-identity-access.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: 관리 작업에는 안전한 Azure 관리 워크스테이션 사용

**지침**: Azure Batch 리소스에 로그인 하 고 구성 하도록 구성 된 다단계 인증을 사용 하는 paw (권한 있는 액세스 워크스테이션)를 사용 합니다.

- [Privileged Access Workstation에 대한 자세한 정보](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure에서 다단계 인증을 사용 하도록 설정 하는 방법](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정에서 의심 스러운 활동에 대 한 로그 및 경고

**지침**: Azure Active Directory를 사용 하 여 Azure Batch 응용 프로그램에 대 한 통합 인증을 사용 하는 경우 환경에서 의심 스러운 작업이 나 안전 하지 않은 활동이 발생 하는 경우 로그 및 경고 생성을 위한 Azure Active Directory 보안 보고서 Azure Security Center를 사용하여 ID 및 액세스 활동을 모니터링합니다.

- [위험한 활동에 대해 플래그가 지정된 Azure AD 사용자를 식별하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Security Center에서 사용자 id 및 액세스 활동을 모니터링 하는 방법](../security-center/security-center-identity-access.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: Azure Active Directory를 사용 하 여 Azure Batch 응용 프로그램에 대 한 통합 인증을 사용 하는 경우 조건부 액세스 명명 된 위치를 사용 하 여 IP 주소 범위 또는 국가/지역의 특정 논리적 그룹 에서만 액세스를 허용할 수 있습니다.

- [Azure에서 명명된 위치를 구성하는 방법](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure Active Directory를 중앙 인증 및 권한 부여 시스템으로 사용 하 고 Azure Batch 응용 프로그램에 대 한 인증을 Azure AD와 통합 합니다. Azure AD는 강력한 암호화를 저장 데이터 및 전송 중 데이터에 사용하여 데이터를 보호합니다. 또한 Azure AD는 사용자 자격 증명을 솔트하고, 해시하고, 안전하게 저장합니다.

- [Azure AD 인스턴스를 만들고 구성하는 방법](../active-directory-domain-services/tutorial-create-instance.md)

- [Azure AD를 사용 하 여 Batch 응용 프로그램을 인증 하는 방법](batch-aad-auth.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure Active Directory는 부실 계정을 검색하는 데 도움이 되는 로그를 제공합니다. 또한 Azure Id 액세스 검토를 사용 하 여 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 사용자의 액세스를 정기적으로 검토 하 여 적절 한 사용자만 계속 액세스할 수 있도록 할 수 있습니다.

- [Azure ID 액세스 검토를 사용하는 방법](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화 되는 자격 증명에 대 한 액세스 시도를 모니터링 합니다.

**지침**: Azure Log Analytics 작업 영역에 감사 로그 및 로그인 로그를 전송 하는 Azure Active Directory 사용자 계정에 대 한 진단 설정을 만듭니다. Azure Log Analytics 작업 영역 내에서 원하는 경고를 구성 합니다.

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대 한 경고

**지침**: Azure Active Directory 위험 검색 및 id 보호 기능을 사용 하 여 사용자 id와 관련 된 검색 된 의심 스러운 작업에 대 한 자동화 된 응답을 구성할 수 있습니다. 또한 추가 조사를 위해 데이터를 Azure 센티널로 수집할 수 있습니다.

- [Azure AD 위험한 로그인을 확인하는 방법](../active-directory/identity-protection/overview-identity-protection.md)

- [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel을 온보딩하는 방법](../sentinel/quickstart-onboard.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오에서 관련 고객 데이터에 대한 액세스 권한을 Microsoft에 제공  

**지침**: 사용할 수 없음 고객 Lockbox Azure Batch에 대해 아직 지원 되지 않습니다.
 
- [지원 되 고객 Lockbox 서비스 목록](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="data-protection"></a>데이터 보호

자세한 내용은 [Azure Security Benchmark: 데이터 보호](../security/benchmarks/security-control-data-protection.md)를 참조하세요.

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 태그를 사용하여 중요한 정보를 저장하거나 처리하는 Azure 리소스를 추적할 수 있도록 지원합니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 개발, 테스트 및 프로덕션을 위한 별도의 구독 및/또는 관리 그룹을 구현합니다. Azure Batch 풀은 가상 네트워크/서브넷으로 구분 되 고, 적절 하 게 태그가 지정 되 고, NSG (네트워크 보안 그룹)로 보호 되어야 합니다. Azure Batch 데이터는 보안 Azure Storage 계정 내에 포함 되어야 합니다.

- [Virtual Network 내에서 Azure Batch 풀을 만드는 방법](batch-virtual-network.md)

- [Azure Storage 계정을 보호 하는 방법](../storage/blobs/security-recommendations.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: 중요 한 정보를 포함 하는 Azure Batch 풀과 연결 된 Azure Storage 계정은 태그를 사용 하 여 중요 한 것으로 표시 하 고 Azure 모범 사례를 사용 하 여 보안을 유지 합니다.

데이터 식별, 분류 및 손실 방지 기능은 아직 Azure Storage 또는 계산 리소스에 사용할 수 없습니다. 규정 준수를 위해 필요한 경우 타사 솔루션을 구현합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고, 고객 데이터 손실 및 노출을 방지하기 위해 모든 노력을 다하고 있습니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

- [Azure Storage 계정을 보호 하는 방법](../storage/blobs/security-recommendations.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 공유됨

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: 전송 중인 모든 중요 한 정보를 암호화 합니다. Microsoft Azure 리소스는 기본적으로 TLS 1.2을 협상 합니다. Azure Batch 풀 또는 데이터 저장소 (Azure Storage 계정)에 연결 하는 클라이언트에서 TLS 1.2 이상을 협상할 수 있는지 확인 합니다.

Azure Batch 데이터를 포함 하는 저장소 계정에 액세스 하려면 HTTPS가 필요 합니다.

- [전송 중인 Azure Storage 계정 암호화 이해](../storage/blobs/security-recommendations.md)

**Azure Security Center 모니터링**: 예

**책임**: 공유됨

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: 중요 한 정보를 포함 하는 Azure Batch 풀과 연결 된 Azure Storage 계정은 태그를 사용 하 여 중요 한 것으로 표시 하 고 Azure 모범 사례를 사용 하 여 보안을 유지 합니다.

데이터 식별, 분류 및 손실 방지 기능은 아직 Azure Storage 또는 계산 리소스에 사용할 수 없습니다. 규정 준수를 위해 필요한 경우 타사 솔루션을 구현합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고, 고객 데이터 손실 및 노출을 방지하기 위해 모든 노력을 다하고 있습니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

- [Azure Storage 계정을 보호 하는 방법](../storage/blobs/security-recommendations.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 공유됨

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: 역할 기반 액세스 제어를 사용 하 여 리소스에 대 한 액세스를 제어 합니다.

**지침**: azure RBAC (역할 기반 액세스 제어)를 사용 하 여 batch 계정, batch 풀 및 저장소 계정을 비롯 한 azure 리소스의 관리 평면에 대 한 액세스를 제어 합니다.

- [Azure RBAC 이해](../role-based-access-control/overview.md)

- [Azure RBAC를 구성 하는 방법](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

**지침**: 데이터 식별, 분류 및 손실 방지 기능은 아직 Azure Storage 또는 계산 리소스에 사용할 수 없습니다. 규정 준수를 위해 필요한 경우 타사 솔루션을 구현합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요한 것으로 간주하고, 고객 데이터 손실 및 노출을 방지하기 위해 모든 노력을 다하고 있습니다. Azure 내에서 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현하고 유지 관리합니다.

- [Azure의 고객 데이터 보호 이해](../security/fundamentals/protection-customer-data.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 공유됨

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: Azure Batch 계정과 연결 된 저장소 계정의 경우 Microsoft에서 암호화 키를 관리 하도록 허용 하는 것이 좋습니다. 그러나 필요한 경우 고유한 키를 관리 하는 옵션이 있습니다.

Azure disk encryption을 사용 하 여 조직의 보안 및 규정 준수 약정에 맞게 데이터를 보호 하 고 보호할 수 있습니다. 기존 디스크에 작성 된 모든 관리 디스크, 스냅숏, 이미지 및 데이터는 플랫폼 관리 키를 사용 하 여 미사용 상태로 자동으로 암호화 됩니다.

- [Azure Storage 계정의 암호화 키를 관리 하는 방법](../storage/common/customer-managed-keys-configure-key-vault.md)

- [고객이 관리 하는 암호화 키를 구성 하는 방법](../storage/common/customer-managed-keys-configure-key-vault.md)

- [디스크 암호화를 사용 하는 풀을 만드는 방법](disk-encryption.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 로그 및 경고

**지침**: Azure 활동 로그와 함께 Azure Monitor를 사용 하 여 Azure Batch 계정/풀과 관련 된 중요 한 Azure 리소스에 대 한 변경 내용이 발생 하는 경우에 대 한 경고를 만듭니다.

풀 데이터에 대 한 모든 CRUD 작업을 모니터링 하 고 기록 하기 위해 Azure Batch 풀과 연결 된 저장소 계정에 대 한 진단 설정을 구성 합니다.

- [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](../azure-monitor/alerts/alerts-activity-log.md)

- [Azure Storage 계정에 대 한 추가 로깅/감사를 사용 하도록 설정 하는 방법](../storage/common/manage-storage-analytics-logs.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="vulnerability-management"></a>취약성 관리

*자세한 내용은 [Azure 보안 벤치 마크: 취약성 관리](../security/benchmarks/security-control-vulnerability-management.md)를 참조 하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약성 검사 도구 실행

**지침**: Azure Batch 풀 노드의 경우 취약점 관리 솔루션을 관리 해야 합니다.

필요에 따라 Rapid7, Qualys 또는 다른 취약성 관리 플랫폼 구독이 있는 경우에는 해당 포털을 통해 Batch 풀 노드에 수동으로 취약성 평가 에이전트를 설치 하 고 노드를 관리할 수 있습니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: 자동화된 운영 체제 패치 관리 솔루션 배포

**지침**: 기본 Azure Batch 풀 노드 이미지를 유지 관리 하 고 업데이트 합니다. 자동 업데이트를 사용 하도록 설정 하거나, 노드를 모니터링 하거나, 정기적으로 재부팅을 수행 해야 하는 클러스터 수명 기간 동안 Azure Batch 풀 노드의 운영 체제가 패치 된 상태로 유지 되는지 확인 합니다.

**Azure Security Center 모니터링**: 예

**책임**: 공유됨

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: 타사 소프트웨어 타이틀에 대 한 자동화 된 패치 관리 솔루션 배포

**지침**: 풀 노드의 타사 응용 프로그램은 자동 업데이트를 사용 하도록 설정 하거나, 노드를 모니터링 하거나, 정기적으로 재부팅을 수행 해야 하는 클러스터 수명 기간 동안 패치 된 상태로 유지 되는지 Azure Batch 확인 합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 연속 취약성 검사 비교

**참고**: Rapid7, Qualys 또는 다른 취약성 관리 플랫폼 구독이 있는 경우 해당 공급 업체의 포털을 사용 하 여 백 엔드 취약성 검색을 보고 비교할 수 있습니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

**지침**: 일반적인 위험 채점 프로그램(예: Common Vulnerability Scoring System) 또는 타사 검사 도구에 제공된 기본 위험 등급을 사용합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure 보안 벤치 마크: 인벤토리 및 자산 관리](../security/benchmarks/security-control-inventory-asset-management.md)를 참조 하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화 된 asset discovery 솔루션 사용

**지침**: Azure 리소스 그래프를 사용 하 여 구독 내에서 계산, 저장소, 네트워크 등의 모든 리소스 (예: 계산, 저장소, 네트워크 등)를 쿼리/검색 합니다. 테 넌 트에서 적절 한 (읽기) 권한이 있는지 확인 하 고 구독 내의 리소스 뿐만 아니라 모든 Azure 구독을 열거할 수 있습니다.

클래식 Azure 리소스는 Azure 리소스 그래프 탐색기를 통해 검색 될 수 있지만 앞으로 Azure Resource Manager 리소스를 만들고 사용 하는 것이 좋습니다.

- [Azure Resource Graph Explorer를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

- [Azure 구독을 확인하는 방법](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Azure RBAC 이해](../role-based-access-control/overview.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타데이터를 제공하는 Azure 리소스에 태그를 적용하여 논리적인 분류로 구성합니다.

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 태그 지정, 관리 그룹 및 별도의 구독 (해당 하는 경우)을 사용 하 여 자산을 구성 하 고 추적 합니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다.

- [추가 Azure 구독을 만드는 방법](../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../governance/management-groups/create-management-group-portal.md)

- [태그를 만들고 사용하는 방법](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: 승인 된 Azure 리소스의 인벤토리 정의 및 유지 관리

**지침**: 계산 리소스에 대해 승인 된 Azure 리소스 및 승인 된 소프트웨어 목록을 정의 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy에서 다음 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.

- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

Azure 리소스 그래프를 사용 하 여 구독 내에서 리소스를 쿼리/검색 합니다. 환경에 있는 모든 Azure 리소스가 승인되었는지 확인합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Graph Explorer를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

**지침**: Azure Batch 풀 노드의 경우 타사 솔루션을 구현 하 여 승인 되지 않은 소프트웨어 응용 프로그램에 대 한 클러스터 노드를 모니터링 합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침**: Azure Batch 풀 노드의 경우 타사 솔루션을 구현 하 여 승인 되지 않은 소프트웨어 응용 프로그램에 대 한 클러스터 노드를 모니터링 합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="68-use-only-approved-applications"></a>6.8: 승인된 애플리케이션만 사용

**지침**: Azure Batch 풀 노드의 경우 타사 솔루션을 구현 하 여 권한 없는 소프트웨어가 실행 되지 않도록 합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy에서 다음 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다. 
- 허용되지 않는 리소스 종류 
- 허용되는 리소스 유형 

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](../governance/policy/samples/built-in-policies.md#general)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: 승인 된 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: Azure Batch 풀 노드의 경우 타사 솔루션을 구현 하 여 권한 없는 파일 형식이 실행 되지 않도록 합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: 사용자가 Azure Resource Manager 상호 작용할 수 있도록 제한

**지침**: "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성함으로써 Azure 조건부 액세스를 사용하여 Azure Resource Manager와 상호 작용하는 사용자 기능을 제한합니다.

- [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure 보안 벤치 마크: 보안 구성](../security/benchmarks/security-control-secure-configuration.md)을 참조 하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: "Microsoft.Batch" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 사용자 지정 정책을 만들어 Azure Batch 계정 및 풀의 구성을 감사 하거나 적용 합니다.

- [사용 가능한 Azure Policy 별칭을 보는 방법](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정

**지침**: Batch 풀 노드의 운영 체제에 대 한 보안 구성을 설정 합니다.

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: Azure Policy [거부] 및 [배포 되지 않은 경우 배포]를 사용 하 여 Batch 계정 및 풀과 관련 된 Azure 리소스 (예: 가상 네트워크, 서브넷, Azure 방화벽, Azure Storage 계정 등)에 대 한 보안 설정을 적용 합니다. 다음 네임 스페이스의 Azure Policy 별칭을 사용 하 여 사용자 지정 정책을 만들 수 있습니다.

- Microsoft.Batch

- Microsoft.Storage

- Microsoft.Network

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy 효과 이해](../governance/policy/concepts/effects.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 보안 운영 체제 구성 유지 관리

**지침**: Microsoft에서 관리 하 고 유지 관리 하는 Azure Batch Pool 운영 체제 이미지입니다. OS 수준 상태 구성을 구현하는 것은 사용자의 책임입니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 공유됨

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: Azure Batch 계정, 풀 또는 관련 리소스에 대 한 사용자 지정 Azure Policy 정의를 사용 하는 경우 Azure Repos를 사용 하 여 코드를 안전 하 게 저장 하 고 관리 합니다.

- [Azure DevOps에 코드를 저장하는 방법](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Azure Repos 설명서](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장

**지침**: Azure Batch 풀에 대해 사용자 지정 이미지를 사용 하는 경우 권한 있는 사용자만 이미지에 액세스할 수 있도록 RBAC (역할 기반 액세스 제어)를 사용 합니다.

- [Azure의 RBAC 이해](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Azure에서 RBAC를 구성하는 방법](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스에 대 한 구성 관리 도구 배포

**지침**: 기본 제공 Azure Policy 정의를 사용 하 여 Azure Batch 관련 리소스 구성을 경고, 감사 및 적용 합니다.  "Microsoft.Batch" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 Azure Batch 계정 및 풀에 대 한 사용자 지정 정책을 만듭니다. 또한 정책 예외를 관리하는 프로세스와 파이프라인을 개발합니다.

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 구성 관리 도구를 배포 합니다.

**지침**: Azure Batch 풀 노드의 운영 체제에 대해 필요한 상태를 유지 하기 위해 타사 솔루션을 구현 합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대 한 자동화 된 구성 모니터링 구현

**지침**: "Microsoft.Batch" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 사용자 지정 정책을 만들어 Azure Batch 인스턴스의 구성을 감사 하거나 적용 합니다. Azure Batch 또는 Azure Batch에서 사용 하는 리소스 (예:)에 대해 특별히 생성 된 기본 제공 정책을 사용할 수도 있습니다.
- 서브넷은 네트워크 보안 그룹과 연결 되어야 합니다. 저장소 계정은 가상 네트워크 서비스 끝점을 사용 해야 합니다.
- 배치 계정의 진단 로그를 사용하도록 설정해야 합니다.

- [사용 가능한 Azure Policy 별칭을 보는 방법](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Azure Policy를 구성하고 관리하는 방법](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대한 자동화된 구성 모니터링 구현

**지침**: Azure Batch 풀 노드의 운영 체제 상태를 모니터링 하는 타사 솔루션을 구현 합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: Azure Key Vault Azure Batch 배포와 함께 사용 하 여 Azure Storage 계정 내에서 풀 저장소에 대 한 키를 관리할 수 있습니다.

- [Azure 관리 Id와 통합 하는 방법](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Azure Key Vault를 만드는 방법](../key-vault/general/quick-create-portal.md)

- [Key Vault에 인증 하는 방법](../key-vault/general/authentication.md)
- [Key Vault 액세스 정책을 할당 하는 방법](../key-vault/general/assign-access-policy-portal.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 더 안전한 위치(예: Azure Key Vault)로 이동하도록 추천합니다. 

- [자격 증명 스캐너를 설정하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [Azure 보안 벤치 마크: 맬웨어 방어](../security/benchmarks/security-control-malware-defense.md)를 참조 하세요.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: 중앙에서 관리 하는 맬웨어 방지 소프트웨어 사용

**지침**: windows 운영 체제의 경우 개별 Azure Batch 풀 노드에서 windows Defender를 사용 하거나 Linux를 사용 하는 경우 고유한 맬웨어 방지 솔루션을 제공 합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비 컴퓨팅 Azure 리소스에 업로드할 파일 미리 검사

**지침**: Microsoft 맬웨어 방지 프로그램은 Azure 서비스 (예: Azure Batch)를 지 원하는 기본 호스트에서 사용 하도록 설정 되어 있지만 고객 콘텐츠에서 실행 되지 않습니다.

App Service, Data Lake Storage, Blob Storage 등의 비 계산 Azure 리소스에 업로드 되는 파일을 미리 검색 합니다. Microsoft는 이러한 인스턴스의 고객 데이터에 액세스할 수 없습니다.

- [Azure Cloud Services 및 Virtual Machines에 대 한 Microsoft 맬웨어 방지 이해](../security/fundamentals/antimalware.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명이 업데이트되었는지 확인

**지침**: windows 운영 체제의 경우 개별 Azure Batch 풀 노드에서 windows Defender를 사용 하 고 자동 업데이트를 사용 하도록 설정 해야 합니다. Linux를 사용 하는 경우 고유한 맬웨어 방지 솔루션을 제공 합니다.

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [Azure 보안 벤치 마크: 데이터 복구](../security/benchmarks/security-control-data-recovery.md)를 참조 하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 정기 자동 백업 확인

**지침**: Azure Batch 풀 데이터 저장소에 대 한 Azure Storage 계정을 사용 하는 경우 적절 한 중복성 옵션 (LRS, ZRS, GRS, RA-GRS)을 선택 합니다. 

- [Azure Storage 계정에 대 한 저장소 중복성을 구성 하는 방법](../storage/common/storage-redundancy.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업을 수행 하 고 고객 관리 키를 백업 합니다.

**지침**: Azure Batch 풀 데이터 저장소에 대 한 Azure Storage 계정을 사용 하는 경우 적절 한 중복성 옵션 (LRS, ZRS, GRS, RA-GRS)을 선택 합니다. Azure Batch 배포의 모든 부분에 대해 Azure Key Vault를 사용 하는 경우 키를 백업 해야 합니다.

- [Azure Storage 계정에 대 한 저장소 중복성을 구성 하는 방법](../storage/common/storage-redundancy.md)

- [Azure에서 키 자격 증명 모음 키를 백업하는 방법](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 비롯 한 모든 백업 유효성 검사

**지침**: Azure Storage 계정 또는 Azure Batch 구현과 관련 된 다른 리소스에 대 한 고유한 키를 관리 하는 경우 백업 된 키의 복원을 정기적으로 테스트 합니다.

- [Azure에서 키 자격 증명 모음 키를 백업하는 방법](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

- [PowerShell을 사용 하 여 고객 관리 키를 복원 하는 방법](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객이 관리 하는 키를 보호 해야 합니다.

**지침**: Azure Batch 풀 저장소 계정과 관련 된 키를 저장 하는 데 Azure Key Vault를 사용 하는 경우 Azure Key Vault에서 Soft-Delete를 사용 하 여 실수로 인 한 삭제 또는 악의적인 삭제 로부터 키를 보호 합니다.

- [Azure Key Vault에서 일시 삭제를 사용 하도록 설정 하는 방법](../key-vault/general/key-vault-recovery.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

## <a name="incident-response"></a>사고 대응

자세한 내용은 [Azure Security Benchmark: 인시던트 응답](../security/benchmarks/security-control-incident-response.md)을 참조하세요.

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 직원의 역할 및 인시던트 처리/관리 단계를 정의 하는 사고 대응 계획을 작성 해야 합니다.

- [Azure Security Center 내에서 워크플로 자동화를 구성하는 방법](../security-center/security-center-planning-and-operations-guide.md)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center는 경고에 대 한 심각도를 할당 하 여 각 경고에 참석 하는 순서의 우선 순위를 지정할 수 있도록 합니다. 따라서 리소스가 손상 되 면 즉시 가져올 수 있습니다. 심각도는 Security Center에서 경고를 실행하는 데 사용된 결과 또는 분석의 신뢰도 및 경고가 발생된 활동의 배후에 악의적인 의도가 있었음에 대한 신뢰 수준을 기준으로 합니다.

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 격차를 식별하고 필요에 따라 계획을 수정합니다.

- [NIST의 게시물을 참조하세요. IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 안내](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center 모니터링**: 해당 없음

**책임**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: Microsoft 보안 대응 센터 (MSRC)가 불법적인 또는 권한이 없는 당사자가 데이터에 액세스 한 것을 발견 한 경우 microsoft에서 사용자에 게 연락 하는 데 사용 됩니다.

- [Azure Security Center 보안 연락처를 설정하는 방법](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center 모니터링**: 예

**책임**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: 연속 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Azure Security Center 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다.

- [연속 내보내기를 구성하는 방법](../security-center/continuous-export.md)

- [경고를 Azure Sentinel로 스트림하는 방법](../sentinel/connect-azure-security-center.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Azure Security Center의 워크플로 자동화 기능을 사용하여 보안 경고 및 권장 사항에 대한 "Logic Apps"를 통해 응답을 자동으로 트리거합니다.

- [워크플로 자동화와 Logic Apps를 구성하는 방법](../security-center/workflow-automation.md)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [Azure 보안 벤치 마크: 침투 테스트 및 레드 팀 연습](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조 하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대 한 정기적인 침투 테스트를 수행 하 고 모든 중요 한 보안 결과를 수정 하세요.

**지침**: Microsoft [Engagement 규칙에 따라 침투 테스트가 microsoft 정책을 위반 하지 않는지 확인 하세요](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.) .

Microsoft에서 관리 되는 클라우드 인프라, 서비스 및 응용 프로그램에 대 한 레드 팀 및 라이브 사이트 침투 테스트에 대 한 Microsoft의 전략과 실행에 대 한 자세한 내용은 아래에서 확인할 수 있습니다. 

- [Microsoft Cloud 레드 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center 모니터링**: 해당 없음

**책임**: 공유됨

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark V2 개요](../security/benchmarks/overview.md)를 참조하세요.
- [Azure 보안 기준](../security/benchmarks/security-baselines-overview.md)에 대해 자세히 알아보세요.