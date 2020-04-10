---
title: Azure SQL 데이터베이스에 대한 Azure 보안 기준선
description: Azure SQL 데이터베이스에 대한 Azure 보안 기준선
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: d51cf22d7be167501927e54ce159e0b732209b0d
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011381"
---
# <a name="azure-security-baseline-for-azure-sql-database"></a>Azure SQL 데이터베이스에 대한 Azure 보안 기준선

Azure SQL Database에 대한 Azure 보안 기준에는 배포의 보안 상태를 개선하는 데 도움이 되는 권장 사항이 포함되어 있습니다.

이 서비스의 기준은 Azure [Security 벤치마크 버전 1.0에서](https://docs.microsoft.com/azure/security/benchmarks/overview)가져온 것으로, 모범 사례 지침을 통해 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다.

자세한 내용은 [Azure 보안 기준 개요를](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)참조하십시오.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [보안 제어: 네트워크 보안](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)을 참조하십시오.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: 가상 네트워크에서 네트워크 보안 그룹 또는 Azure 방화벽을 사용하여 리소스 보호

**지침**: Azure 개인 링크를 활성화하여 가상 네트워크의 개인 엔드포인트를 통해 Azure PaaS 서비스(예: SQL Database) 및 Azure 호스팅 고객/파트너 서비스에 대한 액세스를 허용할 수 있습니다. 가상 네트워크와 서비스 간의 트래픽은 Microsoft 백본 네트워크를 통해 이동하여 공용 인터넷에서 노출을 제거합니다. 

트래픽이 Azure SQL Database로 전송되도록 하려면 SQL 서비스 태그를 사용해 네트워크 보안 그룹을 통한 아웃바운드 트래픽을 허용합니다.

Azure SQL Database는 가상 네트워크 규칙을 통해 Virtual Network 내의 선택한 서브넷에서 전송된 통신만 수락할 수 있습니다.

Azure SQL 데이터베이스에 대한 개인 링크를 설정하는 방법:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

데이터베이스 서버에 가상 네트워크 서비스 끝점 및 규칙을 사용하는 방법:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Vnet, 서브넷 및 NIC의 구성 및 트래픽을 모니터링하고 기록합니다.

**지침**: Azure 보안 센터를 사용하고 Azure SQL Database Server가 배포되는 서브넷에 대한 네트워크 보호 권장 사항을 수정합니다. 

Azure SQL Database Server 인스턴스에 연결되는 Azure 가상 시스템(VM)의 경우 해당 VM을 보호하는 NSG에 대한 NSG(네트워크 보안 그룹) 흐름 로그를 사용하도록 설정하고 트래픽 감사를 위해 Azure 저장소 계정으로 로그를 보냅니다. 

또한 NSG 흐름 로그를 Log Analytics 작업 영역으로 보내고 트래픽 분석을 사용하여 Azure 클라우드의 트래픽 흐름에 대한 통찰력을 제공할 수도 있습니다. 트래픽 분석의 몇 가지 장점은 네트워크 활동을 시각화하고 핫스팟을 식별하고, 보안 위협을 식별하고, 트래픽 흐름 패턴을 이해하고, 네트워크 잘못된 구성을 정확히 찾아낼 수 있다는 점입니다.

NSG 흐름 로그를 활성화하는 방법:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Azure 보안 센터에서 제공하는 네트워크 보안 이해:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

트래픽 분석을 활성화하고 사용하는 방법:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Azure 보안 센터에서 제공하는 네트워크 보안 이해:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침**: 해당되지 않음; 이 권장 사항은 Azure Apps Service 또는 웹 응용 프로그램을 호스팅하는 리소스를 계산하기 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: SQL Server 인스턴스와 연결된 가상 네트워크에서 DDoS 보호 표준을 사용하여 분산 서비스 거부 공격으로부터 보호합니다. Azure 보안 센터 통합 위협 인텔리전스를 사용하여 알려진 악성 또는 사용되지 않는 인터넷 IP 주소와의 통신을 거부합니다.

DDoS 보호를 구성하는 방법:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Azure 보안 센터 통합 위협 인텔리전스 이해:

https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: 네트워크 패킷 및 흐름 로그 기록

**지침**: Azure SQL Database 인스턴스에 연결되는 Azure 가상 컴퓨터(VM)의 경우 해당 VM을 보호하는 NSG에 대한 NSG(네트워크 보안 그룹) 흐름 로그를 활성화하고 트래픽 감사를 위해 Azure 저장소 계정으로 로그를 보냅니다. 비정상적인 활동을 조사하는 데 필요한 경우 네트워크 감시자 패킷 캡처를 사용하도록 설정합니다.

NSG 흐름 로그를 활성화하는 방법:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

네트워크 감시자 사용 방법:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 침입 감지/침입 방지 시스템(IDS/IPS) 배포

지침 : Azure SQL 데이터베이스에 대한 ATP(고급 위협 보호)를 사용하도록 **설정합니다.**  사용자는 의심스러운 데이터베이스 활동, 잠재적 취약성 및 SQL 삽입 공격뿐만 아니라 비정상적인 데이터베이스 액세스 및 쿼리 패턴에 대한 경고도 받습니다. 고급 위협 보호는 경고를 Azure 보안 센터와통합합니다. 

Azure SQL 데이터베이스에 대한 고급 위협 보호 이해 및 사용:https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview


**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션으로의 트래픽 관리

**지침**: 해당되지 않음; 이 권장 사항은 Azure Apps Service 또는 웹 응용 프로그램을 호스팅하는 리소스를 계산하기 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: 가상 네트워크 서비스 태그를 사용하여 네트워크 보안 그룹 또는 Azure 방화벽에서 네트워크 액세스 제어를 정의합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 규칙의 적절한 소스 또는 대상 필드에 서비스 태그 이름(예: ApiManagement)을 지정하면 해당 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다. Microsoft는 서비스 태그로 둘러싸인 주소 접두사를 관리하고 주소가 변경될 때 서비스 태그를 자동으로 업데이트합니다.

Azure SQL Database에 서비스 끝점을 사용하는 경우 Azure SQL Database 공용 IP 주소로의 아웃바운드가 필요합니다. Azure SQL Database에 NSG 서비스 태그를 사용하여 이 작업을 수행할 수 있습니다.

Azure SQL 데이터베이스에 대한 서비스 끝점을 사용하여 서비스 태그를 이해합니다.

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations

서비스 태그 이해 및 사용:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 장치에 대한 표준 보안 구성 유지

**지침**: Azure 정책을 사용하여 Azure SQL Database 서버 인스턴스에 대한 네트워크 보안 구성을 정의하고 구현합니다. "Microsoft.Sql" 네임스페이스를 사용하여 사용자 지정 정책 정의를 정의하거나 Azure SQL Database 서버 네트워크 보호를 위해 설계된 기본 제공 정책 정의를 사용할 수 있습니다. Azure SQL Database 서버에 적용 가능한 기본 제공 네트워크 보안 정책의 예로는 "SQL Server가 가상 네트워크 서비스 끝점을 사용해야 합니다."

 

Azure Blueprint를 사용하여 단일 Blueprint 정의에서 Azure 리소스 관리 템플릿, 역할 기반 액세스 제어(RBAC) 및 정책과 같은 주요 환경 아티팩트를 패키징하여 대규모 Azure 배포를 간소화합니다. 새로운 구독 및 환경에 Blueprint를 쉽게 적용하고 버전 관리를 통해 제어 및 관리를 미세 조정할 수 있습니다.

Azure 정책을 구성하고 관리하는 방법:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Blueprint를 만드는 방법:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙

**지침**: NSG(네트워크 보안 그룹) 및 네트워크 보안 및 트래픽 흐름과 관련된 기타 리소스에 태그를 사용합니다. 개별 NSG 규칙의 경우 "설명" 필드를 사용하여 네트워크로 의 트래픽을 허용하는 모든 규칙에 대한 비즈니스 요구 및/또는 기간(등)을 지정합니다.

"태그 및 해당 값 필요"와 같이 태그 지정과 관련된 기본 제공 Azure 정책 정의를 사용하여 모든 리소스가 태그로 만들어지도록 하고 기존 태그가 지정되지 않은 리소스를 알려줍니다.

Azure PowerShell 또는 Azure CLI를 사용하여 태그를 기반으로 리소스에 대한 작업을 조회하거나 수행할 수 있습니다.

태그를 만들고 사용하는 방법:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 을 모니터링하고 변경 사항을 감지합니다.

**지침**: Azure 활동 로그를 사용하여 네트워크 리소스 구성을 모니터링하고 Azure SQL Database 서버 인스턴스와 관련된 네트워크 리소스의 변경 내용을 검색합니다. Azure Monitor 내에서 중요한 네트워크 리소스에 대한 변경이 발생할 때 트리거되는 경고를 만듭니다.

Azure 활동 로그 이벤트를 보고 검색하는 방법:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Azure 모니터에서 경고를 만드는 방법:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [보안 제어: 로깅 및 모니터링](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)을 참조하십시오.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 소스 사용

**지침**: Microsoft는 Azure 리소스에 대한 시간 원본을 유지 관리합니다. 계산 배포에 대한 시간 동기화를 업데이트할 수 있습니다.

Azure 계산 리소스에 대한 시간 동기화를 구성하는 방법:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 마이크로 소프트

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: Azure SQL Database에 대한 감사를 활성화하여 데이터베이스 이벤트를 추적하고 Azure 저장소 계정, 로그 분석 작업 영역 또는 이벤트 허브의 감사 로그에 씁니다.

또한 Azure SQL 진단 원격 분석을 Azure SQL Analytics로 스트리밍할 수 있으며, 확장 및 여러 구독에서 Azure SQL 데이터베이스, 탄력적 풀 및 관리되는 인스턴스의 성능을 모니터링하는 클라우드 솔루션입니다. Azure SQL Database 성능 메트릭을 수집 및 시각화하는 데 도움이 될 수 있고 성능 문제 해결을 위한 기본 제공 인텔리전스를 제공합니다.

Azure SQL 데이터베이스에 대한 감사를 설정하는 방법:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

Azure 모니터를 사용하여 플랫폼 로그 및 메트릭을 수집하는 방법:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging

진단을 Azure SQL 분석으로 스트리밍하는 방법:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging#stream-into-azure-sql-analytics

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: Azure SQL Database 서버 인스턴스에서 감사를 활성화하고 감사 로그(Azure 저장소, 로그 분석 또는 이벤트 허브)에 대한 저장소 위치를 선택합니다.

Azure SQL Server에 대한 감사를 사용하도록 설정하는 방법:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

**지침**: 해당되지 않음; 이 벤치마크는 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 저장소 보존 구성

**지침**: Azure SQL Database 로그를 로그 분석 작업 영역에 저장할 때 조직의 규정 준수 규정에 따라 로그 보존 기간을 설정합니다.

로그 보존 매개 변수를 설정하는 방법:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: 비정상적인 동작이 대두되는 로그를 분석 및 모니터링하고 결과를 정기적으로 검토합니다. Azure 보안 센터의 고급 위협 보호를 사용하여 Azure SQL Database 인스턴스와 관련된 비정상적인 활동에 대해 경고합니다. 또는 Azure SQL Database 인스턴스와 관련된 메트릭 값 또는 Azure 활동 로그 항목을 기반으로 경고를 구성합니다.

Azure SQL Server에 대한 고급 위협 보호 및 경고 이해:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

Azure SQL 데이터베이스에 대한 사용자 지정 경고를 구성하는 방법:

https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal?view=azps-1.4.0

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: 비정상적인 활동에 대한 경고 사용

**지침**: 비정상적인 작업을 모니터링하고 경고하기 위해 Azure SQL 데이터베이스에 Azure 보안 센터 고급 위협 보호를 사용합니다. SQL 데이터베이스에 대한 고급 데이터 보안을 사용하도록 설정합니다. 고급 데이터 보안에는 중요한 데이터를 검색 및 분류하고, 잠재적인 데이터베이스 취약점을 표시 및 완화하고, 데이터베이스에 대한 위협을 나타낼 수 있는 비정상적인 활동을 탐지하는 기능이 포함됩니다.

Azure SQL 데이터베이스에 대한 고급 위협 보호 및 경고 이해:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

Azure SQL 데이터베이스에 대한 고급 데이터 보안을 사용하도록 설정하는 방법:

https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security

Azure 보안 센터에서 경고를 관리하는 방법:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

**지침**: 해당되지 않음; Azure SQL Server의 경우 맬웨어 방지 솔루션은 기본 플랫폼에서 Microsoft에서 관리합니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

**지침**: 해당되지 않음; DNS 로깅은 Azure SQL Server에 적용되지 않습니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅 사용

**지침**: 해당되지 않음; 명령줄 감사는 Azure SQL Server에 적용되지 않습니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [보안 제어: ID 및 액세스 제어](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)를 참조하십시오.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정의 인벤토리 유지

**지침**: AAD(Azure Active Directory)에는 명시적으로 할당되어야 하고 쿼리할 수 있는 기본 제공 역할이 있습니다. AAD PowerShell 모듈을 사용하여 임시 쿼리를 수행하여 관리 그룹의 구성원인 계정을 검색합니다.

PowerShell을 사용하여 Azure AD에서 디렉터리 역할을 얻는 방법:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

PowerShell을 사용하여 Azure AD에서 디렉터리 역할의 구성원을 얻는 방법:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 해당하는 경우 기본 암호 변경

**지침**: Azure Active Directory에는 기본 암호의 개념이 없습니다. Azure SQL Database 인스턴스를 프로비전할 때 Azure Active Directory와 인증을 통합하도록 선택하는 것이 좋습니다.

Azure SQL을 사용하여 Azure Active Directory 인증을 구성하고 관리하는 방법:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 전용 관리 계정 사용에 대한 정책 및 절차를 만듭니다. Azure 보안 센터 ID 및 액세스 관리를 사용하여 관리 계정 수를 모니터링합니다.

Azure 보안 센터 ID 및 액세스 이해:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에서 단일 사인온(SSO) 사용

**지침**: 해당되지 않음; Azure Active Directory 인증을 Azure SQL Server와 통합하도록 구성할 수 있지만 단일 사인온은 지원되지 않습니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: AAD(Azure Active Directory) 다단계 인증(MFA)을 활성화하고 Azure 보안 센터 ID 및 액세스 관리 권장 사항을 따릅니다.

Azure에서 MFA를 활성화하는 방법:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Azure 보안 센터 내에서 ID 및 액세스를 모니터링하는 방법:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터(권한 있는 액세스 워크스테이션) 사용

**지침**: Azure 리소스에 로그인하고 구성하도록 구성된 다단계 인증 MFA를 사용하여 권한 있는 액세스 워크스테이션(PAW)을 사용합니다.

권한 있는 액세스 워크스테이션에 대해 자세히 알아보기:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Azure에서 MFA를 활성화하는 방법:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 관리 계정의 의심스러운 활동에 대한 로그 및 경고

**지침**: 환경에서 의심스럽거나 안전하지 않은 활동이 발생할 때 로그 및 경고를 생성하기 위해 Azure Active Directory 보안 보고서를 사용합니다.

Azure SQL Database에 대한 고급 위협 보호를 사용하여 데이터베이스에 액세스하거나 악용하려는 비정상적인 시도를 나타내는 비정상적인 활동을 검색합니다.

위험한 활동에 플래그가 지정된 Azure AD 사용자를 식별하는 방법:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Azure 보안 센터에서 사용자 ID 및 액세스 활동을 모니터링하는 방법:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

고급 위협 보호 및 잠재적인 경고 검토:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview#advanced-threat-protection-alerts


**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명된 위치를 사용하여 IP 주소 범위 또는 국가/지역의 특정 논리 그룹에서만 포털 및 Azure 리소스 관리에 액세스할 수 있습니다.

Azure에서 명명된 위치를 구성하는 방법:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="39-use-azure-active-directory"></a>3.9: Azure 활성 디렉터리 사용

**지침**: Azure SQL Database 서버 인스턴스에 대한 AAD(Azure Active Directory) 관리자를 만듭니다.

Azure SQL을 사용하여 Azure Active Directory 인증을 구성하고 관리하는 방법:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

AAD 인스턴스를 만들고 구성하는 방법:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 사용자 액세스를 정기적으로 검토하고 조정

**지침**: Azure Active Directory(AAD)는 오래된 계정을 검색하는 데 도움이 되는 로그를 제공합니다. 또한 Azure Id 액세스 검토를 사용하여 그룹 구성원 자격, 엔터프라이즈 응용 프로그램에 대한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 사용자의 액세스는 정기적으로 검토하여 올바른 사용자만 계속 액세스할 수 있도록 할 수 있습니다.

Azure ID 액세스 검토를 사용하는 방법:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 비활성화된 계정에 액세스하려는 시도를 모니터링합니다.

**지침**: Azure SQL을 사용하여 Azure Active Directory(AAD) 인증을 구성하고 Azure Active Directory 사용자 계정에 대한 진단 설정을 만들어 감사 로그 및 로그인 로그를 로그 분석 작업 영역으로 보냅니다. 로그 분석 작업 영역 내에서 원하는 경고를 구성합니다.

Azure SQL을 사용하여 Azure Active Directory 인증을 구성하고 관리하는 방법:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

Azure 활동 로그를 Azure 모니터에 통합하는 방법:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure Active Directory(AAD) ID 보호 및 위험 검색을 사용하여 사용자 ID와 관련된 의심스러운 작업을 검색하도록 자동화된 응답을 구성합니다. 또한 추가 조사를 위해 Azure Sentinel에 데이터를 수집할 수 있습니다.

Azure AD 위험 로그인을 보는 방법:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오 중에 Microsoft에 관련 고객 데이터에 대한 액세스 제공

**지침**: Microsoft가 고객 데이터에 액세스해야 하는 지원 시나리오에서 Azure Customer Lockbox는 고객이 고객 데이터 액세스 요청을 검토하고 승인하거나 거부할 수 있는 인터페이스를 제공합니다.

고객 잠금 상자 이해:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [보안 제어: 데이터 보호](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)를 참조하십시오.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 민감한 정보의 인벤토리 유지

**지침**: 태그를 사용하여 중요한 정보를 저장하거나 처리하는 Azure 리소스를 추적합니다.

태그를 만들고 사용하는 방법:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 민감한 정보를 저장하거나 처리하는 시스템을 격리

**지침**: 개발, 테스트 및 프로덕션을 위해 별도의 구독 및/또는 관리 그룹을 구현합니다. 리소스는 Vnet/Subnet으로 구분하고 적절하게 태그를 지정하고 NSG 또는 Azure 방화벽 내에서 보호해야 합니다. 중요한 데이터를 저장하거나 처리하는 리소스는 격리해야 합니다. 개인 링크를 사용; Azure SQL Server를 Vnet 내부에 배포하고 비공개 끝점을 사용하여 비공개로 연결합니다.

추가 Azure 구독을 만드는 방법:

https://docs.microsoft.com/azure/billing/billing-create-subscription

관리 그룹을 만드는 방법:

https://docs.microsoft.com/azure/governance/management-groups/create

태그를 만들고 사용하는 방법:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Azure SQL 데이터베이스에 대한 개인 링크를 설정하는 방법:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 민감한 정보의 무단 전송 모니터링 및 차단

**지침**: Azure SQL Database의 경우 중요한 정보를 저장하거나 처리하는 경우 태그를 사용하여 데이터베이스 및 관련 리소스를 중요한 것으로 표시합니다. Azure SQL Database 인스턴스의 네트워크 보안 그룹 서비스 태그와 함께 개인 링크를 구성하여 중요한 정보가 유출되지 않도록 합니다.

Microsoft에서 관리하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 민감한 것으로 취급하고 고객 데이터 손실 및 노출을 방지하기 위해 많은 시간을 보습니다. Azure 내의 고객 데이터를 안전하게 유지하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 제품군을 구현하고 유지 관리합니다.

Azure SQL Database 인스턴스에서 데이터 유출을 방지하기 위해 개인 링크 및 NSG를 구성하는 방법:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview

Azure의 고객 데이터 보호 이해:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중의 모든 민감한 정보 암호화

**지침**: Azure SQL Database는 전송 계층 보안을 사용하여 동작 중인 데이터를 암호화하여 데이터를 보호합니다. SQL Server는 모든 연결에 대해 항상 암호화(SSL/TLS)를 적용합니다. 이렇게 하면 연결 문자열에서 암호화 또는 TrustServerCertificate 설정에 관계 없이 클라이언트와 서버 간에 모든 데이터가 "전송 중"으로 암호화됩니다.

전송 중 Azure SQL 암호화 이해:

https://docs.microsoft.com/azure/sql-database/sql-database-security-overview#information-protection-and-encryption

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 마이크로 소프트

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: Azure SQL Database 데이터 검색 및 분류 기능을 사용합니다. 데이터 검색 및 분류는 데이터베이스의 중요한 데이터를 검색, 분류, 레이블 지정을 &amp; 위해 Azure SQL Database에 기본 제공된 고급 기능을 제공합니다.

Azure SQL Server에 대한 데이터 검색 및 분류를 사용하는 방법:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용하여 리소스에 대한 액세스를 제어합니다.

**지침**: Azure SQL Database 인스턴스에 대한 액세스를 인증하고 제어하기 위해 AAD(Azure Active Directory)를 사용합니다.

인증을 위해 Azure SQL Server를 Azure Active Directory와 통합하는 방법:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication

Azure SQL Server에서 액세스를 제어하는 방법:

https://docs.microsoft.com/azure/sql-database/sql-database-control-access

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용하여 액세스 제어 적용

**지침**: Microsoft는 Azure SQL Database의 기본 인프라를 관리하고 고객 데이터의 손실 또는 노출을 방지하기 위해 엄격한 제어를 구현했습니다.

Azure의 고객 데이터 보호 이해:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 미사용 정보 암호화

**지침**: 투명 데이터 암호화(TDE)는 미사용 데이터를 암호화하여 Azure SQL Database, Azure SQL 관리 인스턴스 및 Azure Data Warehouse를 악의적인 오프라인 활동의 위협으로부터 보호하는 데 도움이 됩니다. 이 기능은 애플리케이션을 변경할 필요 없이 미사용 데이터베이스, 연결된 백업 및 트랜잭션 로그 파일의 실시간 암호화 및 암호 해독을 수행합니다. 기본적으로 TDE는 새로 배포된 모든 Azure SQL 데이터베이스에 대해 활성화됩니다. TDE 암호화 키는 Microsoft 또는 고객이 관리할 수 있습니다.

투명한 데이터 암호화를 관리하고 고유한 암호화 키를 사용하는 방법:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal#manage-transparent-data-encryption

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 사항에 대한 로그 및 경고

**지침**: Azure 활동 로그와 함께 Azure 모니터를 사용하여 Azure SQL Database 및 기타 중요 하거나 관련 리소스의 프로덕션 인스턴스에 대한 변경 이 수행되는 시기에 대한 경고를 만듭니다.

Azure 활동 로그 이벤트에 대한 경고를 만드는 방법:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Azure 보안 센터 모니터링**: 예

**책임**: 고객

## <a name="vulnerability-management"></a>취약성 관리

*자세한 내용은 [보안 제어: 취약점 관리](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)를 참조하십시오.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약점 검색 도구 실행

**지침**: Azure SQL 데이터베이스에 대한 고급 데이터 보안을 사용하도록 설정하고 Azure SQL Server에서 취약성 평가를 수행하는 Azure 보안 센터의 권장 사항을 따릅니다.

Azure SQL 데이터베이스에서 취약성 평가를 실행하는 방법:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment

고급 데이터 보안을 활성화하는 방법:

https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security

Azure 보안 센터 취약성 평가 권장 사항을 구현하는 방법:

https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: 자동화된 운영 체제 패치 관리 솔루션 배포

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: 자동화된 타사 소프트웨어 패치 관리 솔루션 배포

**지침**: 해당되지 않음; 이 벤치마크는 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 백투백 취약점 검사 비교

**지침**: Azure SQL Database 인스턴스에 대해 주기적인 되풀이 검사를 사용하도록 설정합니다. 이렇게 하면 일주일에 한 번 데이터베이스에서 검사를 자동으로 실행하도록 취약점 평가가 구성됩니다. 사용자가 제공한 전자 메일 주소로 검색 결과 요약이 전송됩니다. 결과를 비교하여 취약점이 해결되었는지 확인합니다.

Azure 보안 센터에서 취약성 평가 보고서를 내보내는 방법:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment#implementing-vulnerability-assessment

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 발견된 취약점의 문제 해결에 우선 순위를 지정합니다.

**지침**: Azure 보안 센터에서 제공하는 기본 위험 등급(보안 점수)을 사용합니다.

Azure 보안 센터 보안 점수 이해:

https://docs.microsoft.com/azure/security-center/security-center-secure-score

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [보안 관리: 인벤토리 및 자산 관리를](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)참조하십시오.*

### <a name="61-use-azure-asset-discovery"></a>6.1: Azure 자산 검색 사용

**지침**: Azure 리소스 그래프를 사용하여 구독 내의 모든 리소스(Azure SQL Server 인스턴스 포함)를 쿼리하고 검색합니다.  테넌트에 적절한 (읽기) 권한이 있고 구독 내의 모든 Azure 구독과 리소스를 등록할 수 있는지 확인합니다.

리소스 그래프를 통해 클래식 Azure 리소스를 검색할 수 있지만 앞으로 Azure 리소스 관리자 리소스를 만들고 사용하는 것이 좋습니다.

Azure 그래프를 사용하여 쿼리를 만드는 방법:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Azure 구독을 보는 방법:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Azure RBAC 이해:https://docs.microsoft.com/azure/role-based-access-control/overview


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타데이터를 제공하는 Azure 리소스에 태그를 적용하여 논리적으로 분류로 구성합니다.

태그를 만들고 사용하는 방법:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 승인되지 않은 Azure 리소스 삭제

**지침:** 적절한 경우 태그 지정, 관리 그룹 및 별도의 구독을 사용하여 자산을 구성하고 추적합니다. 정기적으로 인벤토리를 조정하고 승인되지 않은 리소스가 적시에 구독에서 삭제되도록 합니다.

추가 Azure 구독을 만드는 방법:

https://docs.microsoft.com/azure/billing/billing-create-subscription

관리 그룹을 만드는 방법:

https://docs.microsoft.com/azure/governance/management-groups/create

태그를 만들고 사용하는 방법:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: 승인된 Azure 리소스 및 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: 계산 리소스에 대해 승인된 Azure 리소스 및 승인된 소프트웨어 목록 정의

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure 정책을 사용하여 다음 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 유형에 대한 제한을 두는 경우:

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

Azure 리소스 그래프를 사용하여 구독 내의 리소스를 쿼리/검색합니다. 환경에 있는 모든 Azure 리소스가 승인되었는지 확인합니다.

Azure 정책을 구성하고 관리하는 방법:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure 그래프를 사용하여 쿼리를 만드는 방법:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 컴퓨팅 리소스 내에서 승인되지 않은 소프트웨어 애플리케이션 모니터링

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 응용 프로그램 제거

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="68-use-only-approved-applications"></a>6.8: 승인된 신청서만 사용

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure 정책을 사용하여 다음 기본 제공 정책 정의를 사용하여 고객 구독에서 만들 수 있는 리소스 유형에 제한을 두는 경우:

- 허용되지 않는 리소스 종류

- 허용되는 리소스 유형

Azure 리소스 그래프를 사용하여 구독 내의 리소스를 쿼리/검색합니다. 환경에 있는 모든 Azure 리소스가 승인되었는지 확인합니다.

Azure 정책을 구성하고 관리하는 방법:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure 정책을 사용하여 특정 리소스 유형을 거부하는 방법:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="610-implement-approved-application-list"></a>6.10: 승인된 신청 목록 구현

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스에서 실행되는 응용 프로그램을 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: 스크립트를 통해 Azure 리소스 관리자와 상호 작용하는 사용자의 기능 제한

**지침**: Azure 조건부 액세스를 사용하여 "Microsoft Azure 관리" 앱에 대해 "액세스 차단"을 구성하여 사용자가 Azure 리소스 관리자와 상호 작용하는 기능을 제한합니다.

Azure 리소스 관리자에 대한 액세스를 차단하기 위해 조건부 액세스를 구성하는 방법:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 컴퓨팅 리소스 내에서 스크립트를 실행하는 사용자의 기능 제한

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 물리적 또는 논리적으로 고위험 응용 프로그램을 분리

**지침**: 해당되지 않음; 이 권장 사항은 데스크톱 또는 웹 응용 프로그램을 호스팅하는 App Service 또는 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [보안 제어: 보안 구성](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)을 참조하십시오.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: Azure SQL Server/데이터베이스에 Azure 정책 또는 Azure 보안 센터 권장 사항을 사용하여 모든 Azure 리소스에 대한 보안 구성을 유지 관리합니다.

Azure 정책을 구성하고 관리하는 방법:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성 설정

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 안전한 Azure 리소스 구성 유지

**지침**: Azure 정책 [거부] 및 [존재하지 않는 경우 배포]를 사용하여 Azure 리소스 간에 보안 설정을 적용합니다.

Azure 정책을 구성하고 관리하는 방법:

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

**지침**: 사용자 지정 Azure 정책 정의를 사용하는 경우 Azure DevOps 또는 Azure 리포지토리를 사용하여 코드를 안전하게 저장하고 관리합니다.

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

**지침**: "Microsoft.SQL" 네임스페이스에서 Azure Policy 별칭을 사용하여 시스템 구성을 경고, 감사 및 적용하는 사용자 지정 정책을 만듭니다. 또한 정책 예외를 관리하기 위한 프로세스 및 파이프라인을 개발합니다.

Azure 정책을 구성하고 관리하는 방법:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 시스템 구성 관리 도구 배포

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Azure 서비스에 대한 자동화된 구성 모니터링 구현

**지침**: Azure 보안 센터를 활용하여 Azure SQL Server 및 데이터베이스에 대한 기준 검사를 수행합니다.

Azure 보안 센터에서 권장 사항을 수정하는 방법:

https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제를 위한 자동화된 구성 모니터링 구현

**지침**: 해당되지 않음; 이 권장 사항은 계산 리소스를 위한 것입니다.


**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 해당되지 않음

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure 비밀을 안전하게 관리

**지침**: Azure 키 자격 증명 모음을 사용하여 Azure SQL 데이터베이스 투명 데이터 암호화(TDE)에 대한 암호화 키를 저장합니다.

Azure SQL Server에 저장되는 중요한 데이터를 보호하고 암호화 키를 Azure 키 볼트에 저장하는 방법:

https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault

**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: ID를 안전하고 자동으로 관리

**지침**: 관리되는 ID를 사용하여 Azure Active Directory(AAD)에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 관리되는 ID를 사용하면 코드의 자격 증명 없이 Azure Key Vault를 포함하여 AAD 인증을 지원하는 모든 서비스에 인증할 수 있습니다.

자습서: Windows VM 시스템에서 할당된 관리되는 ID를 사용하여 Azure SQL에 액세스합니다.

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql

관리되는 ID를 구성하는 방법:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도하지 않은 자격 증명 노출 제거

**지침**: 자격 증명 스캐너를 구현하여 코드 내의 자격 증명을 식별합니다. 또한 자격 증명 스캐너는 검색된 자격 증명을 Azure Key Vault와 같은 보다 안전한 위치로 이동하는 것을 권장합니다. 

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

**지침**: Azure 서비스(예: Azure App Service)를 지원하는 기본 호스트에서 Microsoft 맬웨어 방지 기능이 활성화되지만 고객 콘텐츠에서는 실행되지 않습니다.

앱 서비스, 데이터 레이크 저장소, Blob 저장소, Azure SQL Server 등과 같이 계산되지 않은 Azure 리소스에 업로드되는 모든 콘텐츠를 미리 검사합니다. 이러한 경우 Microsoft는 사용자의 데이터에 액세스할 수 없습니다.

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

**지침**: 데이터 손실로부터 비즈니스를 보호하기 위해 Azure SQL Database는 매주 전체 데이터베이스 백업을 자동으로 만들고, 12시간마다 차등 데이터베이스 백업을 만들고, 트랜잭션 로그 백업을 5~10분마다 자동으로 만듭니다. 백업은 모든 서비스 계층에 대해 최소 7일 동안 RA-GRS 저장소에 저장됩니다. Basic 을 제외한 모든 서비스 계층은 최대 35일동안 시간 내 복원을 위해 구성 가능한 백업 보존 기간을 구성합니다.

다양한 규정 준수 요구 사항을 충족하려면 주간, 월별 및/또는 연간 백업에 대해 서로 다른 보존 기간을 선택할 수 있습니다. 스토리지 사용량은 선택한 백업 빈도 및 보존 기간에 따라 다릅니다.

Azure SQL Server를 통해 백업 및 비즈니스 연속성을 이해합니다.

https://docs.microsoft.com/azure/sql-database/sql-database-business-continuity

**Azure 보안 센터 모니터링**: 예

**책임**: 공유

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업 수행 및 고객 관리 키 백업

**지침**: Azure SQL Database는 7일에서 35일 사이에 보관되는 데이터베이스 백업을 자동으로 만들고 Azure 읽기 액세스 지리적 중복 저장소(RA-GRS)를 사용하여 데이터 센터를 사용할 수 없는 경우에도 보존되도록 합니다. 이러한 백업은 자동으로 만들어집니다. 필요한 경우 Azure SQL Database에 대해 장기 지리적 중복 백업을 사용하도록 설정합니다.

투명 데이터 암호화에 고객 관리 키를 사용하는 경우 키가 백업되고 있는지 확인합니다.

Azure SQL Server의 백업 이해:

https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups?tabs=single-database

Azure에서 키 자격 증명 모음 키를 백업하는 방법:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 포함한 모든 백업의 유효성 검사

**지침**: Azure Backup 내에서 콘텐츠의 데이터 복원을 주기적으로 수행할 수 있는 기능을 보장합니다. 필요한 경우 격리된 VLAN에 콘텐츠를 복원합니다. 백업된 고객 관리 키의 복원을 테스트합니다.

Azure에서 키 자격 증명 모음 키를 복원 하는 방법:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

시간 별 복원을 사용하여 Azure SQL Database 백업을 복구하는 방법:

https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리 키 보호 보장

**지침**: Azure Key Vault에서 소프트 삭제를 사용하도록 설정하여 실수로 또는 악의적인 삭제로부터 키를 보호합니다.

키 볼트에서 소프트 삭제를 사용하도록 설정하는 방법:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [보안 제어: 인시던트 대응](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)을 참조하십시오.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 사고 대응 가이드 만들기

**지침**: 인력의 역할과 사고 처리/관리 단계를 정의하는 서면 인시던트 대응 계획이 있는지 확인합니다.

Azure 보안 센터 내에서 워크플로 자동화를 구성하는 방법:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 채점 및 우선 순위 지정 절차 만들기

**지침**: 보안 센터는 각 경고에 참석하는 순서의 우선 순위를 지정하는 데 도움이 되도록 경고에 심각도를 할당하므로 리소스가 손상되면 즉시 확인할 수 있습니다. 심각도는 보안 센터가 찾기 또는 경고를 발행하는 데 사용되는 분석 및 경고로 이어진 활동의 배후에 악의적인 의도가 있다는 신뢰 수준에 얼마나 확신을 가지고 있는지에 따라 다집니다.

Azure 보안 센터의 보안 경고:https://docs.microsoft.com/azure/security-center/security-center-alerts-overview



**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 절차 테스트

**지침**: 정기적으로 시스템의 사고 대응 기능을 테스트하기 위한 연습을 수행합니다. 약점과 격차를 식별하고 필요에 따라 계획을 수정합니다.

NIST의 간행물인 IT 계획 및 기능에 대한 테스트, 교육 및 연습 프로그램 가이드를 참조할 수 있습니다.

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 고객

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침:** MICROSOFT 보안 대응 센터(MSRC)에서 불법 또는 승인되지 않은 당사자가 데이터에 액세스한 사실을 발견한 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 연락합니다.

Azure 보안 센터 보안 연락처를 설정 하는 방법:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure 보안 센터 모니터링**: 예

**책임**: 고객

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 사고 대응 시스템에 통합

**지침**: 지속적인 내보내기 기능을 사용하여 Azure 보안 센터 경고 및 권장 사항을 내보냅니다. 연속 내보내기를 사용하면 수동으로 또는 지속적으로 지속방식으로 경고 및 권장 사항을 내보낼 수 있습니다. Azure 보안 센터 데이터 커넥터를 사용하여 경고를 Sentinel으로 스트리밍할 수 있습니다.

연속 내보내기를 구성하는 방법:

https://docs.microsoft.com/azure/security-center/continuous-export

Azure Sentinel로 경고를 스트리밍하는 방법:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 응답 자동화

**지침**: Azure 보안 센터의 워크플로 자동화 기능을 사용하여 보안 경고 및 권장 사항에 대한 "논리 앱"을 통해 응답을 자동으로 트리거합니다.

워크플로 자동화 및 논리 앱을 구성하는 방법:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure 보안 센터 모니터링**: 현재 사용할 수 없음

**책임**: 고객

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [보안 제어: 침투 테스트 및 빨간색 팀 연습을](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)참조하십시오.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Azure 리소스에 대한 정기적인 침투 테스트를 수행하고 60일 이내에 모든 중요한 보안 결과의 수정을 보장합니다.

**지침**: 보급 시험이 Microsoft 정책을 위반하지 않도록 Microsoft 참여 규칙을 따르십시오.

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Microsoft 관리 클라우드 인프라, 서비스 및 응용 프로그램에 대한 Microsoft의 전략 및 Red Teaming 및 라이브 사이트 침투 테스트실행에 대한 자세한 내용은 여기에서 확인할 수 있습니다.https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure 보안 센터 모니터링**: 해당되지 않음

**책임**: 공유

## <a name="next-steps"></a>다음 단계

- Azure [보안 벤치마크](https://docs.microsoft.com/azure/security/benchmarks/overview) 보기
- [Azure 보안 기준에](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview) 대해 자세히 알아보기
