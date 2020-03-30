---
title: 코스모스 DB에 대한 Azure 보안 기준선
description: 코스모스 DB에 대한 Azure 보안 기준선
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: fd2706b6a6880d7c9454619e1315b2b5b5404561
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244276"
---
# <a name="azure-security-baseline-for-cosmos-db"></a>코스모스 DB에 대한 Azure 보안 기준선

Cosmos DB에 대한 Azure 보안 기준에는 배포의 보안 상태를 개선하는 데 도움이 되는 권장 사항이 포함되어 있습니다.

이 서비스의 기준은 Azure [Security 벤치마크 버전 1.0에서](https://docs.microsoft.com/azure/security/benchmarks/overview)가져온 것으로, 모범 사례 지침을 통해 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다.

자세한 내용은 [Azure 보안 기준 개요를](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)참조하십시오.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [보안 제어: 네트워크 보안](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)을 참조하십시오.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: 가상 네트워크에서 네트워크 보안 그룹 또는 Azure 방화벽을 사용하여 리소스 보호

**지침**: Azure 개인 링크를 사용하여 개인 끝점을 통해 Azure Cosmos 계정에 연결할 수 있습니다. 가상 네트워크와 서비스 간의 트래픽은 Microsoft 백본 네트워크를 통해 이동하여 공용 인터넷에서 노출을 제거합니다. 또한 NSG(네트워크 보안 그룹)에서 엄격한 아웃바운드 규칙 집합을 구성하고 NSG를 서브넷과 연결하여 데이터 유출 위험을 줄일 수 있습니다.

서비스 끝점을 사용하여 Azure Cosmos 계정을 보호할 수도 있습니다. 서비스 끝점을 사용하도록 설정하면 Azure Cosmos 계정을 구성하여 Azure 가상 네트워크의 특정 서브넷에서만 액세스할 수 있도록 허용할 수 있습니다. Azure Cosmos DB 서비스 끝점을 사용하도록 설정하면 가상 네트워크의 서브넷에서 연결되는 Azure Cosmos 계정에 대한 액세스를 제한할 수 있습니다.

IP 방화벽을 사용하여 Azure Cosmos 계정에 저장된 데이터를 보호할 수도 있습니다. Azure Cosmos DB는 인바운드 방화벽 지원을 위해 IP 기반 액세스 제어를 지원합니다. Azure 포털, Azure 리소스 관리자 템플릿을 사용하거나 Azure CLI 또는 Azure PowerShell을 사용하여 Azure Cosmos 계정에 IP 방화벽을 설정할 수 있습니다.

Azure 개인 링크 개요:https://docs.microsoft.com/azure/private-link/private-link-overview

Azure Cosmos DB에 대한 개인 끝점을 구성하는 방법:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints 

보안 구성을 사용하여 네트워크 보안 그룹을 만드는 방법:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

코스모스 DB에서 IP 방화벽을 구성하는 방법:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-firewall

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Vnet, 서브넷 및 NIC의 구성 및 트래픽을 모니터링하고 기록합니다.

**지침**: Azure 보안 센터를 사용하고 네트워크 보호 권장 사항을 수행하여 Azure Cosmos 계정과 관련된 네트워크 리소스를 보호할 수 있습니다.

Azure Cosmos 계정과 동일한 가상 네트워크에 가상 컴퓨터를 배포하는 경우 NSG(네트워크 보안 그룹)를 사용하여 데이터 유출 위험을 줄일 수 있습니다. NSG 흐름 로그를 활성화하고 트래픽 감사를 위해 Azure 저장소 계정으로 로그를 보냅니다. 또한 NSG 흐름 로그를 Log Analytics 작업 영역으로 보내고 트래픽 분석을 사용하여 Azure 클라우드의 트래픽 흐름에 대한 통찰력을 제공할 수도 있습니다. 트래픽 분석의 몇 가지 장점은 네트워크 활동을 시각화하고 핫스팟을 식별하고, 보안 위협을 식별하고, 트래픽 흐름 패턴을 이해하고, 네트워크 잘못된 구성을 정확히 찾아낼 수 있다는 점입니다.

Azure 보안 센터에서 제공하는 네트워크 보안 이해:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

NSG 흐름 로그를 활성화하는 방법:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

트래픽 분석을 활성화하고 사용하는 방법:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침:** CORS(원본 간 리소스 공유) 기능을 사용하여 한 도메인에서 실행중인 웹 응용 프로그램이 다른 도메인의 리소스에 액세스할 수 있도록 합니다. 웹 브라우저는 웹 페이지가 다른 도메인의 API를 호출하지 못하게 차단하는 동일 원본 정책이라고 하는 보안 제한을 구현합니다. 그러나 CORS는 원본 도메인이 다른 도메인의 API를 호출할 수 있는 안전한 방법을 제공합니다. Azure Cosmos 계정에 CORS 지원을 사용하도록 설정하면 오직 인증된 요청만 평가하여 사용자가 지정한 규칙에 따라 허용 여부가 결정됩니다.

원본 간 리소스 공유 구성:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-cross-origin-resource-sharing

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: Azure Cosmos DB에 고급 위협 보호(ATP)를 사용합니다. Azure Cosmos DB용 ATP는 Azure Cosmos 계정에 액세스하거나 악용하려는 특이하고 잠재적으로 유해한 시도를 감지하는 추가 보안 인텔리전스 계층을 제공합니다. 이 보호 계층을 사용하면 위협을 해결하고 중앙 보안 모니터링 시스템과 통합할 수 있습니다.

Azure Cosmos DB 인스턴스와 연결된 가상 네트워크에서 DDoS 보호 표준을 사용하여 DDoS 공격을 방지합니다. Azure 보안 센터 통합 위협 인텔리전스를 사용하여 알려진 악성 또는 사용되지 않는 인터넷 IP 주소와의 통신을 거부합니다.

Azure 코스모스 DB 고급 위협 보호를 구성하는 방법:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

DDoS 보호를 구성하는 방법:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Azure 보안 센터 통합 위협 인텔리전스 이해:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: 네트워크 패킷 및 흐름 로그 기록

**지침**: NSG(네트워크 보안 그룹) 흐름 로그를 활성화하고 트래픽 감사를 위해 스토리지 계정으로 로그를 보냅니다. NSG 흐름 로그를 Log Analytics 작업 영역으로 보내고 트래픽 분석을 사용하여 Azure 클라우드의 트래픽 흐름에 대한 통찰력을 제공할 수 있습니다. 트래픽 분석의 몇 가지 장점은 네트워크 활동을 시각화하고 핫스팟을 식별하고, 보안 위협을 식별하고, 트래픽 흐름 패턴을 이해하고, 네트워크 잘못된 구성을 정확히 찾아낼 수 있다는 점입니다.

NSG 흐름 로그를 활성화하는 방법:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

트래픽 분석을 활성화하고 사용하는 방법:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 침입 감지/침입 방지 시스템(IDS/IPS) 배포

**지침**: Azure Cosmos DB에 고급 위협 보호(ATP)를 사용합니다. Azure Cosmos DB용 ATP는 Azure Cosmos 계정에 액세스하거나 악용하려는 특이하고 잠재적으로 유해한 시도를 감지하는 추가 보안 인텔리전스 계층을 제공합니다. 이 보호 계층을 사용하면 위협을 해결하고 중앙 보안 모니터링 시스템과 통합할 수 있습니다. 

코스모스 DB 고급 위협 보호를 구성하는 방법:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션으로의 트래픽 관리

**지침**: 해당되지 않음; 권장 사항은 Azure 앱 서비스 또는 계산 리소스에서 실행되는 웹 응용 프로그램을 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: Azure Cosmos 계정에 액세스해야 하는 리소스의 경우 가상 네트워크 서비스 태그를 사용하여 네트워크 securitygGroups 또는 Azure 방화벽에서 네트워크 액세스 제어를 정의합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 규칙의 적절한 소스 또는 대상 필드에 서비스 태그 이름(예: AzureCosmosDB)을 지정하면 해당 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다. Microsoft는 서비스 태그로 둘러싸인 주소 접두사를 관리하고 주소가 변경될 때 서비스 태그를 자동으로 업데이트합니다.

서비스 태그 사용에 대한 자세한 내용은 다음을 참조하십시오.https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 장치에 대한 표준 보안 구성 유지

**지침**: Azure 정책을 통해 네트워크 리소스에 대한 표준 보안 구성을 정의하고 구현합니다. "Microsoft.DocumentDB" 및 "Microsoft.Network" 네임스페이스에서 Azure 정책 별칭을 사용하여 Azure Cosmos DB 인스턴스의 네트워크 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다. 다음과 같은 Azure Cosmos DB에 대한 기본 제공 정책 정의를 사용할 수도 있습니다.

- Cosmos DB 계정에 대한 Advanced Threat Protection 배포

- Cosmos DB는 가상 네트워크 서비스 엔드포인트를 사용해야 함

또한 Azure Blueprint를 사용하여 Azure Resource Manager 템플릿, RBAC(역할 기반 액세스 제어) 및 단일 Blueprint 정의의 정책과 같은 주요 환경 아티팩트를 패키징하여 대규모 Azure 배포를 단순화할 수도 있습니다. 새로운 구독, 환경에 Blueprint를 쉽게 적용하고 버전 관리를 통해 제어 및 관리를 미세 조정할 수 있습니다.

Azure 정책을 구성하고 관리하는 방법:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Blueprint를 만드는 방법:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙

**지침**: Azure Cosmos DB 배포와 연결된 네트워크 리소스에 태그를 사용하여 분류로 논리적으로 구성합니다.

태그를 만들고 사용하는 방법:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 을 모니터링하고 변경 사항을 감지합니다.

**지침**: Azure 활동 로그를 사용하여 네트워크 리소스 구성을 모니터링하고 Azure Cosmos DB 인스턴스와 관련된 네트워크 리소스의 변경 내용을 검색합니다. Azure Monitor 내에서 중요한 네트워크 리소스에 대한 변경이 발생할 때 트리거되는 경고를 만듭니다. 

Azure 활동 로그 이벤트를 보고 검색하는 방법:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Azure 모니터에서 경고를 만드는 방법:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [보안 제어: 로깅 및 모니터링](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)을 참조하십시오.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 소스 사용

**지침**: Microsoft는 로그의 타임스탬프에 대해 Azure Cosmos DB와 같은 Azure 리소스에 사용되는 시간 원본을 유지 관리합니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 마이크로 소프트

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: Azure Cosmos DB에서 생성된 보안 데이터를 집계하기 위해 Azure Monitor를 통해 로그를 수집합니다. Azure 모니터 내에서 Log Analytics 작업 영역을 사용하여 분석을 쿼리및 수행하고 장기/보관 저장소에 저장소 계정을 사용합니다. 또는 Azure Sentinel 또는 타사 보안 인시던트 및 이벤트 관리(SIEM)에 대한 데이터를 활성화하고 온보드할 수 있습니다. 

Azure Cosmos DB에 대한 진단 로그를 활성화하는 방법:https://docs.microsoft.com/azure/cosmos-db/logging

Azure 센티넬 온보보드 방법:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: Azure Cosmos DB에 대한 진단 설정을 활성화하고 로그 분석 작업 영역 또는 저장소 계정으로 로그를 보냅니다. Azure Cosmos DB의 진단 설정은 리소스 로그를 수집하는 데 사용됩니다. 이러한 로그는 요청당 캡처되며 "데이터 평면 로그"라고도 합니다. 데이터 평면 작업의 몇 가지 예로는 삭제, 삽입 및 읽기가 있습니다. Azure 활동 로그 진단 설정을 활성화하고 동일한 로그 분석 작업 영역으로 보낼 수도 있습니다.

Azure Cosmos DB에 대한 진단 설정을 활성화하는 방법:https://docs.microsoft.com/azure/cosmos-db/logging

Azure 활동 로그에 대한 진단 설정을 활성화하는 방법:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 저장소 보존 구성

**지침**: Azure Monitor에서 조직의 규정 준수 규정에 따라 Azure Cosmos DB 인스턴스와 연결된 로그 분석 작업 영역의 로그 보존 기간을 설정합니다.

로그 보존 매개 변수를 설정하는 방법:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: Log Analytics에서 쿼리를 수행하여 용어를 검색하고, 추세를 식별하고, 패턴을 분석하고, 수집한 Azure Cosmos DB 로그를 기반으로 다른 많은 정보를 제공할 수 있습니다.

로그 분석 작업 공간에서 Azure Cosmos DB에 대한 쿼리를 수행하는 방법:https://docs.microsoft.com/azure/cosmos-db/monitor-cosmos-db

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: 비정상적인 활동에 대한 경고 사용

**지침**: Azure 보안 센터에서 Azure Cosmos DB에 대한 고급 위협 보호를 사용하여 보안 로그 및 이벤트의 비정상적인 활동을 모니터링합니다. Azure Cosmos DB에서 진단 설정을 활성화하고 로그 분석 작업 영역으로 로그를 보냅니다.

 

또한 보안 오케스트레이션 자동화된 응답(SOAR) 솔루션을 제공하므로 Log Analytics 작업 영역을 Azure Sentinel에 온보답게 할 수도 있습니다. 이를 통해 재생책(자동화된 솔루션)을 만들고 보안 문제를 해결하는 데 사용할 수 있습니다. 또한 Azure Monitor를 사용하여 로그 분석 작업 영역에서 사용자 지정 로그 경고를 만들 수 있습니다.

Azure Cosmos DB에 대한 위협 보호 경고 목록:https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos

Azure 센티넬 온보보드 방법:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Azure 모니터를 사용하여 로그 경고를 생성, 보기 및 관리합니다.https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

**지침**: 해당되지 않음; Azure Cosmos DB는 맬웨어 방지 관련 로그를 처리하거나 생성하지 않습니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

**지침**: 해당되지 않음; Azure Cosmos DB는 DNS 관련 로그를 처리하거나 생성하지 않습니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅 사용

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [보안 제어: ID 및 액세스 제어](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)를 참조하십시오.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지

**지침:** Azure 포털의 IAM(ID 및 액세스 제어) 창을 사용하여 RBAC(역할 기반 액세스 제어)를 구성하고 Azure Cosmos DB 리소스에 대한 인벤토리를 유지할 수 있습니다. 역할은 Active Directory의 사용자, 그룹, 서비스 주체 및 관리되는 ID에 적용됩니다. 개인 및 그룹에 기본 제공 역할 또는 사용자 지정 역할을 사용할 수 있습니다.

Azure 코스모스 DB는 Azure 코스모스 DB의 일반적인 관리 시나리오에 대한 기본 제공 RBAC를 제공합니다. Azure Active Directory(AD)에 프로필이 있는 개인은 이러한 RBAC 역할을 사용자, 그룹, 서비스 주체 또는 관리되는 ID에 할당하여 Azure Cosmos DB 리소스의 리소스 및 작업에 대한 액세스 권한을 부여하거나 거부할 수 있습니다.

Azure AD PowerShell 모듈을 사용하여 adhoc 쿼리를 수행하여 관리 그룹의 구성원인 계정을 검색할 수도 있습니다. 

또한 Azure Cosmos DB의 일부 작업은 Azure Active Directory 및 계정별 마스터 키로 제어할 수 있습니다.  키 액세스를 제어하려면 'disableKey기반 메타데이터쓰기액세스' 계정 설정을 사용합니다.

Azure Cosmos DB의 역할 기반 액세스 제어 이해:https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

Azure 코스모스 DB 작업(Microsoft.DocumentDB 네임스페이스)을 사용하여 사용자 지정 역할을 빌드합니다.https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdocumentdb

Azure 활성 디렉터리에서 새 역할 만들기:https://docs.microsoft.com/azure/role-based-access-control/custom-roles

PowerShell을 사용하여 Azure Active 디렉터리에서 디렉터리 역할을 얻는 방법:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

PowerShell을 사용하여 Azure Active Directory에서 디렉터리 역할의 구성원을 얻는 방법:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

데이터 작업에 만 사용자 액세스를 제한합니다.https://docs.microsoft.com/azure/cosmos-db/how-to-restrict-user-data

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 해당하는 경우 기본 암호 변경

**지침**: Azure AD 또는 Azure Cosmos DB와 관련하여 기본 또는 빈 암호 개념이 없습니다. 대신 Azure Cosmos DB는 두 가지 유형의 키를 사용하여 사용자를 인증하고 해당 데이터 및 리소스에 대한 액세스를 제공합니다. 마스터 키 및 리소스 토큰을 사용합니다. 키는 언제든지 다시 생성할 수 있습니다.

Azure Cosmos DB의 데이터에 대한 보안 액세스 이해:https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data

Azure 코스모스 DB 키를 재생성하는 방법:https://docs.microsoft.com/azure/cosmos-db/manage-with-powershell#regenerate-keys

Azure Active Directory를 사용하여 프로그래밍 방식으로 키에 액세스하는 방법:https://docs.microsoft.com/azure/cosmos-db/certificate-based-authentication

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 공유

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 해당되지 않음; Azure 코스모스 DB는 관리자 계정을 지원하지 않습니다.  모든 액세스는 Azure Active Directory 및 Azure 역할 기반 액세스 제어(RBAC)와 통합됩니다.



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 단일 사인온(SSO) 사용

**지침**: Azure Cosmos DB는 두 가지 유형의 키를 사용하여 사용자에게 권한을 부여하며 데이터 평면 수준에서 단일 사인온(SSO)을 지원하지 않습니다. 코스모스 DB용 제어평면에 대한 액세스는 REST API를 통해 사용할 수 있으며 SSO를 지원합니다. 인증하려면 요청에 대한 권한 부여 헤더를 Azure Active Directory에서 가져오는 JSON 웹 토큰으로 설정합니다.

코스모스 DB REST API에 대한 Azure 데이터베이스 이해:https://docs.microsoft.com/rest/api/cosmos-db/

Azure Active 디렉터리로 SSO 이해:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure Active Directory 다단계 인증을 활성화하고 Azure 보안 센터 ID 및 액세스 관리 권장 사항을 따릅니다.

Azure에서 MFA를 활성화하는 방법:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Azure 보안 센터 내에서 ID 및 액세스를 모니터링하는 방법:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(권한 있는 액세스 워크스테이션) 사용

**지침**: Azure 리소스에 로그인하고 구성하도록 구성된 다단계 인증을 사용하여 권한 있는 액세스 워크스테이션(PAW)을 사용합니다.

권한 있는 액세스 워크스테이션에 대해 자세히 알아보기:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Azure에서 MFA를 활성화하는 방법:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 관리 계정의 의심스러운 활동에 대한 로그 및 경고

**지침**: Azure Cosmos DB에 고급 위협 보호(ATP)를 사용합니다. Azure Cosmos DB용 ATP는 Azure Cosmos 계정에 액세스하거나 악용하려는 특이하고 잠재적으로 유해한 시도를 감지하는 추가 보안 인텔리전스 계층을 제공합니다. 이 보호 계층을 사용하면 위협을 해결하고 중앙 보안 모니터링 시스템과 통합할 수 있습니다. 

또한 환경에서 의심스럽거나 안전하지 않은 활동이 발생할 때 로그 및 경고를 생성하기 위해 AD(AD) 권한 있는 ID 관리(PIM)를 사용할 수 있습니다.

Azure AD 위험 검색을 사용하여 위험한 사용자 동작에 대한 경고 및 보고서를 볼 수 있습니다.

권한 있는 ID 관리(PIM)를 배포하는 방법:https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Azure AD 위험 감지 이해:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**안내**: 조건부 액세스 정책의 위치 조건을 구성하고 지정된 위치를 관리합니다. 명명된 위치를 사용하여 IP 주소 범위 또는 국가 및 지역의 논리적 그룹을 만들 수 있습니다. Azure Cosmos DB 인스턴스와 같은 중요한 리소스에 대한 액세스를 구성된 명명된 위치로 제한할 수 있습니다.

Azure에서 명명된 위치를 구성하는 방법:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="39-use-azure-active-directory"></a>3.9: Azure 활성 디렉터리 사용

**지침**: Azure Active Directory(AD)를 중앙 인증 및 권한 부여 시스템으로 사용합니다. Azure AD는 미사용 및 전송 중 데이터에 대해 강력한 암호화를 사용하여 데이터를 보호합니다. 또한 Azure AD는 사용자 자격 증명을 솔트, 해시 및 안전하게 저장합니다.

Azure Active Directory 인스턴스를 만들고 구성하는 방법:https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

Azure SQL을 사용하여 Azure Active Directory 인증을 구성하고 관리하는 방법:https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 사용자 액세스를 정기적으로 검토하고 조정

**지침**: Azure Active Directory는 오래된 계정을 검색하는 데 도움이 되는 로그를 제공합니다. 또한 Azure ID Access Reviews를 사용하여 그룹 구성원 자격, 엔터프라이즈 응용 프로그램에 대한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 사용자의 액세스는 정기적으로 검토하여 올바른 사용자만 계속 액세스할 수 있도록 할 수 있습니다.

Azure ID 액세스 검토를 사용하는 방법:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 비활성화된 계정에 액세스하려는 시도를 모니터링합니다.

**지침**: Azure Active Directory 사용자 계정에 대한 진단 설정을 만들어 감사 로그 및 로그인 로그를 원하는 경고를 구성할 수 있는 Log Analytics 작업 영역으로 보낼 수 있습니다.

Azure 활동 로그를 Azure 모니터에 통합하는 방법:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure Cosmos DB에 고급 위협 보호(ATP)를 사용합니다. Azure Cosmos DB용 ATP는 Azure Cosmos 계정에 액세스하거나 악용하려는 특이하고 잠재적으로 유해한 시도를 감지하는 추가 보안 인텔리전스 계층을 제공합니다. 이 보호 계층을 사용하면 위협을 해결하고 중앙 보안 모니터링 시스템과 통합할 수 있습니다. 

Azure AD ID 보호 및 위험 검색 기능을 사용하여 사용자 ID와 관련된 의심스러운 작업을 검색하도록 자동화된 응답을 구성할 수도 있습니다. 또한 추가 조사를 위해 Azure Sentinel에 로그를 인더스트할 수 있습니다.

Azure Active Directory 위험한 로그인을 보는 방법:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Azure 센티넬 온보보드 방법:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오 중에 Microsoft에 관련 고객 데이터에 대한 액세스 제공

**지침**: 현재 사용할 수 없습니다. Cosmos DB용 Azure 데이터베이스에 대해 아직 고객 잠금 상자가 지원되지 않습니다.

고객 잠금 상자 지원 서비스 목록:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 해당되지 않음

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [보안 제어: 데이터 보호](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)를 참조하십시오.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 민감한 정보의 인벤토리 유지

**지침**: 태그를 사용하여 중요한 정보를 저장하거나 처리하는 Azure Cosmos DB 인스턴스를 추적할 수 있습니다.

태그를 만들고 사용하는 방법:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 민감한 정보를 저장하거나 처리하는 시스템을 격리

**지침**: 개발, 테스트 및 프로덕션을 위해 별도의 구독 및/또는 관리 그룹을 구현합니다. Azure Cosmos DB 인스턴스는 가상 네트워크/서브넷으로 구분되고 적절하게 태그가 지정되고 NSG(네트워크 보안 그룹) 또는 Azure 방화벽 내에서 보호됩니다. 중요한 데이터를 저장하는 Azure Cosmos DB 인스턴스는 격리되어야 합니다. Azure 개인 링크를 사용 하 여 개인 끝점을 통해 Azure 코스모스 DB 인스턴스 계정에 연결할 수 있습니다. 개인 끝점은 가상 네트워크 내의 서브넷에 있는 개인 IP 주소 집합입니다. 그런 다음 선택한 개인 IP 주소에 대한 액세스를 제한할 수 있습니다. 

추가 Azure 구독을 만드는 방법:https://docs.microsoft.com/azure/billing/billing-create-subscription

관리 그룹을 만드는 방법:https://docs.microsoft.com/azure/governance/management-groups/create

태그를 만들고 사용하는 방법:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Azure Cosmos DB에 대한 개인 끝점을 구성하는 방법:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints

보안 구성을 사용하여 네트워크 보안 그룹을 만드는 방법:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 민감한 정보의 무단 전송 모니터링 및 차단

**지침:** Azure Cosmos DB에 대한 고급 위협 보호를 배포하여 데이터베이스에 액세스하거나 악용하려는 비정상적인 시도를 나타내는 비정상적인 활동을 감지할 수 있습니다. 현재 다음 경고를 트리거할 수 있습니다.

- 특이한 장소에서 의 액세스

- 비정상적인 데이터 추출

또한 가상 컴퓨터를 사용하여 Azure Cosmos DB 인스턴스에 액세스할 때 개인 링크, 방화벽, 네트워크 보안 그룹 및 서비스 태그를 사용하여 데이터 유출 가능성을 완화합니다. Microsoft는 Azure Cosmos DB의 기본 인프라를 관리하고 고객 데이터의 손실 또는 노출을 방지하기 위해 엄격한 제어를 구현했습니다.

코스모스 DB 고급 위협 보호를 구성하는 방법:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

Azure의 고객 데이터 보호 이해:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 보안 센터 모니터링**: 예

**책임**: 공유

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중의 모든 민감한 정보 암호화

**지침**: Azure Cosmos DB에 대한 모든 연결은 HTTPS를 지원합니다. Azure 코스모스 DB는 TLS1.2도 지원합니다. 최소 TLS 버전 서버 측을 적용할 수 있습니다. 이렇게 하려면 다음 [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com)연락처로 문의하십시오.

코스모스 DB 시큐리티 개요:https://docs.microsoft.com/azure/cosmos-db/database-security

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 마이크로 소프트

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: Azure Cosmos DB에서는 자동 데이터 식별, 분류 및 손실 방지 기능을 아직 사용할 수 없습니다. 그러나 분류 및 데이터 분석에 Azure 인지 검색 통합을 사용할 수 있습니다. 규정 준수를 위해 필요한 경우 타사 솔루션을 구현할 수도 있습니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 민감한 것으로 취급하고 고객 데이터 손실 및 노출을 방지하기 위해 많은 시간을 보습니다. Azure 내의 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 제품군을 구현하고 유지 관리합니다.

Azure 인지 검색을 사용하여 색인 https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb?toc=/azure/cosmos-db/toc.json&ampAzure 코스모스 DB 데이터: ;bc=/azure/cosmos-db/이동 경로/toc.json

Azure의 고객 데이터 보호 이해:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 공유

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스를 제어합니다.

**지침**: Azure Cosmos DB는 Azure Cosmos DB의 일반적인 관리 시나리오에 대해 기본 제공 역할 기반 액세스 제어(RBAC)를 제공합니다. Azure Active Directory에 프로필이 있는 개인은 이러한 RBAC 역할을 사용자, 그룹, 서비스 주체 또는 관리되는 ID에 할당하여 Azure Cosmos DB 리소스의 리소스 및 작업에 대한 액세스 권한을 부여하거나 거부할 수 있습니다. 역할 할당은 Azure Cosmos 계정, 데이터베이스, 컨테이너 및 제공(처리량)에 대한 액세스를 포함하는 제어 평면 액세스로만 범위가 지정됩니다.

Azure 코스모스 DB에서 RBAC를 구현하는 방법:https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

**지침**: 해당되지 않음; 이 지침은 계산 리소스를 위한 것입니다.

Microsoft는 Cosmos DB의 기본 인프라를 관리하고 고객 데이터의 손실 또는 노출을 방지하기 위해 엄격한 제어를 구현했습니다.

Azure의 고객 데이터 보호 이해:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 미사용 정보 암호화

**안내**: Cosmos DB에 저장된 모든 사용자 데이터는 기본적으로 미사용 으로 암호화됩니다. 해제할 수 있는 컨트롤이 없습니다. Azure Cosmos DB는 계정이 실행 중인 모든 리전에서 AES-256 암호화를 사용합니다.

기본적으로 Microsoft는 Azure Cosmos 계정의 데이터를 암호화하는 데 사용되는 키를 관리합니다. 선택적으로 자신의 키로 두 번째 암호화 계층을 추가하도록 선택할 수 있습니다.

Azure Cosmos DB를 사용 하 고 미사용 암호화 이해:https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Azure Cosmos DB를 통해 미사용 암호화에 대한 키 관리 이해:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-security-controls

Azure Cosmos DB 계정에 대해 고객 관리 키를 구성하는 방법:https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 공유

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 사항에 대한 로그 및 경고

**지침**: Azure 활동 로그가 있는 Azure 모니터를 사용하여 Azure Cosmos DB의 프로덕션 인스턴스에 대한 변경 이 수행되는 시기에 대한 경고를 만듭니다.

Azure 활동 로그 이벤트에 대한 경고를 만드는 방법:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Azure 활동 로그 이벤트에 대한 경고를 만드는 방법:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

## <a name="vulnerability-management"></a>취약성 관리

*자세한 내용은 [보안 제어: 취약점 관리](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)를 참조하십시오.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약점 검색 도구 실행

**지침**: Azure Cosmos DB 인스턴스에 대한 Azure 보안 센터의 권장 사항을 따릅니다. 

Microsoft는 Azure Cosmos DB 인스턴스를 지원하는 기본 호스트에서 시스템 패치 및 취약성 관리를 수행합니다. Azure 내의 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 제품군을 구현하고 유지 관리합니다.

Azure 보안 센터에서 사용할 수 있는 지원되는 기능:https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows

**Azure 보안 센터 모니터링**: 예

**책임**: 공유

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: 자동화된 운영 체제 패치 관리 솔루션 배포

**지침**: 해당되지 않음; 이 지침은 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: 자동화된 타사 소프트웨어 패치 관리 솔루션 배포

**지침**: 해당되지 않음; 이 지침은 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 백투백 취약점 검사 비교

**지침**: 해당되지 않음; 이 지침은 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 발견된 취약점의 문제 해결에 우선 순위를 지정합니다.

**지침**: 해당되지 않음; 이 지침은 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [보안 관리: 인벤토리 및 자산 관리를](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)참조하십시오.*

### <a name="61-use-azure-asset-discovery"></a>6.1: Azure 자산 검색 사용

**지침**: Azure 포털 또는 Azure 리소스 그래프를 사용하여 구독 내의 모든 리소스(Azure Cosmos DB에 국한되지 않고 계산, 다른 저장소, 네트워크, 포트 및 프로토콜 등과 같은 리소스 포함)를 검색합니다.  테넌트에 적절한 사용 권한이 있고 구독 내의 모든 Azure 구독과 리소스를 등록할 수 있는지 확인합니다.

리소스 그래프를 통해 클래식 Azure 리소스를 검색할 수 있지만 앞으로 Azure 리소스 관리자 리소스를 만들고 사용하는 것이 좋습니다.

Azure 그래프를 사용하여 쿼리를 만드는 방법:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Azure 구독을 보는 방법:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Azure 역할 기반 액세스 제어 이해:https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: Azure Cosmos DB 인스턴스 및 메타데이터가 있는 관련 리소스에 태그를 적용하여 논리적으로 분류로 구성합니다.

태그를 만들고 사용하는 방법:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

태그를 지원하는 Azure Cosmos DB 리소스:https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support#microsoftdocumentdb

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 승인되지 않은 Azure 리소스 삭제

**지침:** Azure Cosmos DB 리소스를 포함하되 이에 국한되지 않는 자산을 구성하고 추적하기 위해 적절한 경우 태그 지정, 관리 그룹 및 별도의 구독을 사용합니다. 정기적으로 인벤토리를 조정하고 승인되지 않은 리소스가 적시에 구독에서 삭제되도록 합니다.

추가 Azure 구독을 만드는 방법:https://docs.microsoft.com/azure/billing/billing-create-subscription

관리 그룹을 만드는 방법:https://docs.microsoft.com/azure/governance/management-groups/create

태그를 만들고 사용하는 방법:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: 승인된 Azure 리소스 및 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: 해당되지 않음; 이 지침은 전체 리소스 및 Azure를 계산하기 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure 정책을 사용하여 다음 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 유형에 대한 제한을 두는 경우:

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

또한 Azure 리소스 그래프를 사용하여 구독 내의 리소스를 쿼리/검색합니다.

Azure 정책을 구성하고 관리하는 방법:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure 그래프를 사용하여 쿼리를 만드는 방법:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

**지침**: 해당되지 않음; 이 기준은 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 응용 프로그램 제거

**지침**: 해당되지 않음; 이 지침은 전체 리소스 및 Azure를 계산하기 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="68-use-only-approved-applications"></a>6.8: 승인된 신청서만 사용

**지침**: 해당되지 않음; 이 지침은 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure 정책을 사용하여 다음 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 유형에 대한 제한을 두는 경우:

- 허용되지 않는 리소스 종류 

- 허용되는 리소스 유형

Azure 정책을 구성하고 관리하는 방법:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure 정책을 사용하여 특정 리소스 유형을 거부하는 방법:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="610-implement-approved-application-list"></a>6.10: 승인된 신청 목록 구현

**지침**: 해당되지 않음; 이 지침은 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: 스크립트를 통해 AzureResources 관리자와 상호 작용하는 사용자의 기능 제한

**지침**: Azure 조건부 액세스를 사용하여 "Microsoft Azure 관리" 앱에 대해 "액세스 차단"을 구성하여 사용자가 Azure 리소스 관리자와 상호 작용하는 기능을 제한합니다. 이렇게 하면 높은 보안 환경 내에서 리소스를 만들고 변경하지 못할 수 있습니다.

Azure 리소스 관리자에 대한 액세스를 차단하기 위해 조건부 액세스를 구성하는 방법:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 컴퓨팅 리소스 내에서 스크립트를 실행하는 사용자의 기능 제한

**지침**: 해당되지 않음; 이 지침은 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 물리적 또는 논리적으로 고위험 응용 프로그램을 분리

**지침**: 해당되지 않음; 이 지침은 Azure 앱 서비스에서 실행중인 웹 응용 프로그램 또는 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [보안 제어: 보안 구성](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)을 참조하십시오.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure 정책을 통해 Cosmos DB 인스턴스에 대한 표준 보안 구성을 정의하고 구현합니다. "Microsoft.DocumentDB" 네임스페이스에서 Azure 정책 별칭을 사용하여 Cosmos DB 인스턴스의 구성을 감사하거나 적용하는 사용자 지정 정책을 만듭니다. 다음과 같은 Azure Cosmos DB에 대한 기본 제공 정책 정의를 사용할 수도 있습니다.

- Cosmos DB 계정에 대한 Advanced Threat Protection 배포

- Cosmos DB는 가상 네트워크 서비스 엔드포인트를 사용해야 함

사용 가능한 Azure 정책 별칭을 보는 방법:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Azure 정책을 구성하고 관리하는 방법:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정

**지침**: 해당되지 않음; 이 지침은 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 안전한 Azure 리소스 구성 유지

**지침**: Azure 정책 [거부] 및 [존재하지 않는 경우 배포]를 사용하여 Azure 리소스 간에 보안 설정을 적용합니다.

Azure 정책을 구성하고 관리하는 방법:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure 정책 효과 이해:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 안전한 운영 체제 구성 유지

**지침**: 해당되지 않음; 이 지침은 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스의 구성을 안전하게 저장

**지침**: Cosmos DB 또는 관련 리소스에 대한 사용자 지정 Azure 정책 정의를 사용하는 경우 Azure Repos를 사용하여 코드를 안전하게 저장하고 관리합니다.

Azure 리포지토리 설명서: https://docs.microsoft.com/azure/devops/repos/index?view=azure-devopshttps://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전하게 저장

**지침**: 해당되지 않음; 이 지침은 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: 시스템 구성 관리 도구 배포

**지침**: "Microsoft.DocumentDB" 네임스페이스에서 Azure 정책 별칭을 사용하여 시스템 구성을 경고, 감사 및 적용하는 사용자 지정 정책을 만듭니다. 또한 정책 예외를 관리하기 위한 프로세스 및 파이프라인을 개발합니다.

Azure 정책을 구성하고 관리하는 방법:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 시스템 구성 관리 도구 배포

**지침**: 해당되지 않음; 이 지침은 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Azure 서비스에 대한 자동화된 구성 모니터링 구현

**지침**: "Microsoft.DocumentDB" 네임스페이스에서 Azure 정책 별칭을 사용하여 시스템 구성을 경고, 감사 및 적용하는 사용자 지정 정책을 만듭니다. Azure 정책 [감사], [거부]및 [존재하지 않는 경우 배포]를 사용하여 Azure Cosmos DB 인스턴스 및 관련 리소스에 대한 구성을 자동으로 적용합니다. 

Azure 정책을 구성하고 관리하는 방법:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제를 위한 자동화된 구성 모니터링 구현

**지침**: 해당되지 않음; 이 지침은 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure 비밀을 안전하게 관리

**지침**: Azure Cosmos DB 인스턴스에 액세스하는 데 사용되는 Azure 앱 서비스에서 실행되는 Azure 가상 시스템 또는 웹 응용 프로그램의 경우 Azure 키 자격 증명 모음과 함께 관리되는 서비스 ID를 사용하여 Azure Cosmos DB 보안 관리를 단순화하고 보호합니다. 키 볼트 소프트 삭제가 활성화되어 있는지 확인합니다.

Azure 관리 ID와 통합하는 방법:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

키 볼트를 만드는 방법:https://docs.microsoft.com/azure/key-vault/quick-create-portal

관리되는 ID로 키 볼트 인증을 제공하는 방법:https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: ID를 안전하고 자동으로 관리

**지침**: Azure Cosmos DB 인스턴스에 액세스하는 데 사용되는 Azure 앱 서비스에서 실행되는 Azure 가상 시스템 또는 웹 응용 프로그램의 경우 Azure 키 자격 증명 모음과 함께 관리되는 서비스 ID를 사용하여 Azure Cosmos DB 보안 관리를 단순화하고 보호합니다.

관리되는 ID를 사용하여 Azure Active Directory(AD)에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 관리되는 ID를 사용하면 코드의 자격 증명 없이 키 볼트를 포함하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있습니다.

관리되는 ID를 구성하는 방법:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Azure 관리 ID와 통합하는 방법:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내에서 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 Azure Key Vault와 같은 보다 안전한 위치로 이동하는 것을 권장합니다.

자격 증명 스캐너를 설정하는 방법:https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [보안 제어: 맬웨어 방어](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)를 참조하십시오.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: 중앙에서 관리되는 맬웨어 방지 소프트웨어 사용

**지침**: 해당되지 않음; 이 지침은 계산 리소스를 위한 것입니다. Microsoft 맬웨어 방지는 Azure 서비스(예: Azure 앱 서비스)를 지원하는 기본 호스트에서 활성화되지만 고객 콘텐츠에서는 실행되지 않습니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 비계산 Azure 리소스에 업로드할 파일을 미리 스캔합니다.

**지침**: Microsoft 맬웨어 방지는 Azure 서비스(예: Azure App Service)를 지원하는 기본 호스트에서 활성화되지만 고객 콘텐츠에서는 실행되지 않습니다.

Azure Cosmos DB를 포함하여 계산되지 않는 Azure 리소스에 업로드되는 모든 파일을 미리 검사하는 것은 사용자의 책임입니다. Microsoft는 고객 데이터에 액세스할 수 없으므로 고객을 대신하여 고객 콘텐츠에 대한 맬웨어 방지 검사를 수행할 수 없습니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명이 업데이트되었는지 확인

**지침**: 해당되지 않음; 벤치마크는 컴퓨팅 리소스를 위한 것입니다. Microsoft 맬웨어 방지는 Azure 서비스를 지원하는 기본 호스트에서 활성화되지만 고객 콘텐츠에서는 실행되지 않습니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [보안 제어: 데이터 복구](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)를 참조하십시오.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 정기적인 자동 백업 보장

**지침**: Azure Cosmos DB는 4시간마다 데이터의 스냅샷을 생성합니다. 모든 백업은 스토리지 서비스에서 개별적으로 저장되고 이러한 백업은 지역 재해에 대비한 복원을 위해 전역적으로 복제됩니다. 지정된 시간에는 마지막 두 스냅샷만 유지됩니다. 단, 컨테이너 또는 데이터베이스가 삭제된 경우 Azure Cosmos DB는 지정된 컨테이너 또는 데이터베이스의 기존 스냅샷을 30일 동안 유지합니다. Azure 지원에 문의하여 백업에서 복원합니다.

Azure 코스모스 DB 자동 백업 이해:https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 마이크로 소프트

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업 수행 및 고객 관리 키 백업

**지침**: Azure Cosmos DB는 정기적으로 데이터의 백업을 자동으로 수행합니다. 데이터베이스 또는 컨테이너가 삭제된 경우 지원 티켓을 제출하거나 Azure 지원에 전화하여 자동 온라인 백업에서 데이터를 복원할 수 있습니다. Azure 지원은 표준, 개발자 및 계획보다 높은 요금제와 같은 선택된 계획에만 사용할 수 있습니다. 백업의 특정 스냅샷을 복원하려면 Azure Cosmos DB에서 해당 스냅샷의 백업 주기 동안 데이터를 사용할 수 있어야 합니다. 

Key Vault를 사용하여 Cosmos DB 인스턴스에 대한 자격 증명을 저장하는 경우 키를 정기적으로 자동으로 백업해야 합니다.

Azure 코스모스 DB 자동 백업 이해:https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Azure 코스모스 DB에서 데이터를 복원하는 방법:https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

키 볼트 키를 백업하는 방법:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 공유

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 포함한 모든 백업의 유효성 검사

**지침**: 데이터베이스 또는 컨테이너가 삭제된 경우 지원 티켓을 제출하거나 Azure 지원에 전화하여 자동 온라인 백업에서 데이터를 복원할 수 있습니다. Azure 지원은 표준, 개발자 및 계획보다 높은 요금제와 같은 선택된 계획에만 사용할 수 있습니다. 백업의 특정 스냅샷을 복원하려면 Azure Cosmos DB에서 해당 스냅샷의 백업 주기 동안 데이터를 사용할 수 있어야 합니다.

PowerShell을 사용하여 Azure 키 자격 증명 모음에 저장된 비밀의 복원을 테스트합니다. 복원-AzureKeyVaultKey cmdlet 지정된 키 자격 증명 모음에 키를 만듭니다. 이 키는 입력 파일에서 백업된 키의 복제본이며 원래 키와 이름이 같습니다.

Azure 코스모스 DB 자동 백업 이해:

https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Azure 코스모스 DB에서 데이터를 복원하는 방법:

https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Azure 키 볼트 비밀을 복원 하는 방법:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 공유

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리 키 보호 보장

**지침**: Cosmos DB에 저장된 모든 사용자 데이터는 미사용 및 전송 시 암호화되므로 조치를 취할 필요가 없습니다. 또한 미사용 암호화가 기본적으로 "설정" 상태라는 것도 이러한 노력 중 하나입니다. 설정하거나 해제하는 데 사용되는 컨트롤이 없습니다. Azure Cosmos DB는 계정이 실행 중인 모든 리전에서 AES-256 암호화를 사용합니다.

키 자격 증명 모음에서 소프트 삭제를 사용하여 실수로 또는 악의적인 삭제로부터 키를 보호합니다.

Azure Cosmos DB의 데이터 암호화 이해:https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

키 자격 증명 모음에서 소프트 삭제를 사용하도록 설정하는 방법:https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Azure 보안 센터 모니터링**: 예

**책임**: 공유

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [보안 제어: 인시던트 대응](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)을 참조하십시오.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 사고 대응 가이드 만들기

**지침**: 조직에 대한 인시던트 대응 가이드를 작성합니다. 감지에서 사후 검토에 이르는 사고 처리/관리 단계뿐만 아니라 직원의 모든 역할을 정의하는 서면 인시던트 대응 계획이 있는지 확인합니다.

NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용하여 고유한 사고 대응 계획을 수립할 수도 있습니다.https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

Azure 보안 센터 내에서 워크플로 자동화를 구성하는 방법:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

자체 보안 인시던트 대응 프로세스 구축에 대한 지침:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Microsoft 보안 대응 센터의 사고 해부학:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 채점 및 우선 순위 지정 절차 만들기

**지침:** 보안 센터는 각 경고에 심각도를 할당하여 먼저 조사해야 하는 경고의 우선 순위를 지정합니다. 심각도는 보안 센터가 경고를 찾는 데 얼마나 확신하는지 또는 경고를 발행하는 데 사용되는 분석과 경고로 이어진 활동의 배후에 악의적인 의도가 있다는 신뢰 수준에 기반을 두고 있습니다.

또한 구독을 명확하게 표시합니다(ex. Azure 리소스를 명확하게 식별하고 분류하는 명명 시스템을 만듭니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 절차 테스트

**지침**: 정기적으로 시스템의 사고 대응 기능을 테스트하기 위한 연습을 수행합니다. 약점과 격차를 식별하고 필요에 따라 계획을 수정합니다.

NIST의 간행물: IT 계획 및 기능에 대한 테스트, 교육 및 운동 프로그램 가이드를 참조하십시오.https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침:** Microsoft 보안 대응 센터(MSRC)에서 고객의 데이터가 불법적이거나 승인되지 않은 당사자가 액세스한 사실을 발견한 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 연락합니다.  사실 이후에 인시던트를 검토하여 문제가 해결되었는지 확인합니다.

Azure 보안 센터 보안 연락처를 설정 하는 방법:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure 보안 센터 모니터링**: 예

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
