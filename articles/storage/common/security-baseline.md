---
title: Azure Storage에 대한 Azure 보안 기준
description: Azure Storage 보안 기준은 Azure Security Benchmark에 지정된 보안 권장 사항을 구현하기 위한 절차적 지침과 리소스를 제공합니다.
author: msmbaldwin
ms.service: storage
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 28d5bdc788e3b292545fd4c016fae36149f3a600
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589451"
---
# <a name="azure-security-baseline-for-azure-storage"></a>Azure Storage에 대한 Azure 보안 기준

이 보안 기준은 [Azure Security Benchmark 버전 1.0](../../security/benchmarks/overview-v1.md)의 지침을 Azure Storage에 적용합니다. Azure Security Benchmark는 Azure에서 클라우드 솔루션을 보호하는 방법에 대한 권장 사항을 제공합니다.
콘텐츠는 Azure Security Benchmark에서 정의한 **보안 컨트롤** 및 Azure Storage에 적용되는 관련 지침에 따라 그룹화됩니다. Azure Storage에 적용할 수 없는 **컨트롤** 은 제외되었습니다.

 
Azure Storage가 Azure Security Benchmark에 완전히 매핑되는 방식을 확인하려면 [전체 Azure Storage 보안 기준 매핑 파일](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)을 참조하세요.

## <a name="network-security"></a>네트워크 보안

자세한 내용은 [Azure Security Benchmark: 네트워크 보안](../../security/benchmarks/security-control-network-security.md)을 참조하세요.

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: 가상 네트워크 내에서 Azure 리소스 보호

**지침**: 특정 공용 IP 주소 범위의 클라이언트에 대한 액세스를 제한하고 가상 네트워크 또는 특정 Azure 리소스를 선택하여 스토리지 계정의 방화벽을 구성합니다.  또한 기업의 스토리지 서비스에 대한 트래픽이 전용 네트워크를 통해 전송되도록 프라이빗 엔드포인트를 구성할 수도 있습니다.

참고: 클래식 스토리지 계정은 방화벽 및 가상 네트워크를 지원하지 않습니다.

- [Azure Storage Firewall을 구성하는 방법](https://docs.microsoft.com/azure/storage/common/storage-network-security#change-the-default-network-access-rule)

- [Azure Storage에 대한 프라이빗 엔드포인트를 구성하는 방법](storage-private-endpoints.md)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며, [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 컨트롤과 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Storage**:

[!INCLUDE [Resource Policy for Microsoft.Storage 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2: 가상 네트워크, 서브넷 및 네트워크 인터페이스의 구성 및 트래픽을 모니터링 및 기록

**지침**: Azure Storage는 계층화된 보안 모델을 제공합니다. 스토리지 계정에 대한 액세스를 지정된 IP 주소, IP 범위 또는 Azure Virtual Network의 서브넷 목록에서 시작되는 요청으로 제한할 수 있습니다. Azure Security Center를 사용하고 네트워크 보호 권장 사항에 따라 Azure에서 네트워크 리소스를 보호할 수 있습니다. 또한 스토리지 계정 방화벽을 통해 스토리지 계정에 대해 구성된 가상 네트워크 또는 서브넷에 대해 네트워크 보안 그룹 흐름 로그를 사용하도록 설정하고 트래픽 감사를 위해 로그를 스토리지 계정에 보냅니다. 

 
스토리지 계정에 프라이빗 엔드포인트가 연결되어 있는 경우 서브넷에 대한 네트워크 보안 그룹 규칙을 구성할 수 없습니다. 
 

 
- [Azure Storage 방화벽 및 가상 네트워크 구성](storage-network-security.md) 
 

 
- [NSG 흐름 로그를 사용하도록 설정하는 방법](../../network-watcher/network-watcher-nsg-flow-logging-portal.md) 
 

 
- [Azure Security Center에서 제공하는 네트워크 보안 이해](../../security-center/security-center-network-recommendations.md) 
 

 
- [Azure Storage에 대한 프라이빗 엔드포인트 이해](https://docs.microsoft.com/azure/storage/common/storage-private-endpoints#known-issues)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="13-protect-critical-web-applications"></a>1.3: 중요한 웹 애플리케이션 보호

**지침**: 해당 없음. 이 추천 사항은 Azure App Service 또는 컴퓨팅 리소스에서 실행되는 웹 애플리케이션을 위한 것입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: Azure Storage 계정의 스토리지에 대해 Azure Defender를 사용하도록 설정합니다. 스토리지용 Azure Defender는 스토리지 계정에 액세스하거나 악용하려는 비정상적이고 잠재적으로 유해한 시도를 감지하는 추가 보안 인텔리전스 계층을 제공합니다.  Azure Security Center 통합 경보는 IP 주소가 알려진 위험 IP 주소(예: 알려진 암호 화폐 채굴기(cryptominer))인지, 이전에 위험한 것으로 인식되지 않은 IP 주소인지 여부에 관계없이 성공적으로 해결된 IP 주소와 네트워크 통신이 연결된 작업을 기반으로 합니다. 보안 경고는 활동의 비정상 현상이 발생할 때 트리거됩니다. 

- [Azure Defender for Storage 구성](azure-defender-storage-configure.md) 

- [Azure Security Center 통합 위협 인텔리전스 이해](../../security-center/azure-defender.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="15-record-network-packets"></a>1.5: 네트워크 패킷 기록

**지침**: Network Watcher 패킷 캡처를 사용하면 캡처 세션을 생성하여 스토리지 계정과 가상 시스템 간의 트래픽을 추적할 수 있습니다. 원하는 트래픽만 캡처할 수 있도록 캡처 세션에 대 한 필터가 제공됩니다. 패킷 캡처를 통해 사후 및 사전 대응적으로 네트워크 예외를 진단할 수 있습니다. 또한 네트워크 침입에 대한 정보를 가져오는 네트워크 통계를 수집하는 것을 포함하여 클라이언트 서버 간 통신을 디버그할 수 있습니다. 원격으로 패킷 캡처를 트리거할 수 있으면 원하는 가상 머신에서 수동으로 패킷 캡처를 실행하는 부담이 없어지고 시간이 단축됩니다. 

- [ 포털에서 Azure Network Watcher를 사용하여 패킷 캡처 관리](../../network-watcher/network-watcher-packet-capture-manage-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 IDS/IPS(침입 탐지/침입 방지 시스템) 배포

**지침**: 스토리지용 Azure Defender는 스토리지 계정에 액세스하거나 악용하려는 비정상적이고 잠재적으로 유해한 시도를 감지하는 추가 보안 인텔리전스 계층을 제공합니다. 보안 경고는 활동의 비정상 현상이 발생할 때 트리거됩니다. 이러한 보안 경고는 Azure Security Center와 통합되며, 의심스러운 활동에 대한 세부 정보 및 위협을 조사하고 수정하는 방법에 대한 권장 사항을 포함하여 구독 관리자에게 메일로도 전송됩니다. 

- [Azure Defender for Storage 구성](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-security-center)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 애플리케이션에 대한 트래픽 관리

**지침**: 해당 없음. 이 추천 사항은 Azure App Service 또는 컴퓨팅 리소스에서 실행되는 웹 애플리케이션을 위한 것입니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버헤드 최소화

**지침**: 스토리지 계정에 액세스해야 하는 Virtual Network 리소스의 경우 구성된 Virtual Network의 Virtual Network 서비스 태그를 사용하여 네트워크 보안 그룹 또는 Azure Firewall에서 네트워크 액세스 제어를 정의합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 규칙의 적절한 원본 또는 대상 필드에 서비스 태그 이름(예: 스토리지)을 지정하면 해당 서비스에 대한 트래픽을 허용하거나 거부할 수 있습니다. Microsoft에서는 서비스 태그에서 압축한 주소 접두사를 관리하고 주소를 변경하는 대로 서비스 태그를 자동으로 업데이트합니다. 

네트워크 액세스 범위를 특정 스토리지 계정으로 확장해야 하는 경우 Virtual Network 서비스 엔드포인트 정책을 사용합니다.

- [서비스 태그를 사용하는 방법에 대한 자세한 정보](../../virtual-network/service-tags-overview.md)

- [Azure Storage에 대한 가상 네트워크 서비스 엔드포인트 정책에 대한 자세한 내용](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 디바이스에 대한 표준 보안 구성 유지

**지침**: Azure Policy를 사용하는 Azure Storage 계정과 연결된 네트워크 리소스에 대한 표준 보안 구성을 정의하고 구현합니다. "Microsoft.Storage" 및 "Microsoft.Network" 네임스페이스에서 Azure Policy 별칭을 사용하여 스토리지 계정 리소스의 네트워크 구성을 감사하거나 적용하기 위한 사용자 지정 정책을 생성합니다. 

스토리지 계정과 관련된 기본 제공 정책 정의도 사용할 수 있습니다. 예를 들어 스토리지 계정은 스토리지 계정과 관련된 기본 제공 정책 정의를 사용할 수도 있습니다. 

- [Azure Policy를 구성하고 관리하는 방법](../../governance/policy/tutorials/create-and-manage.md) 

- [네트워킹을 위한 Azure Policy 샘플](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#storage) 

- [네트워킹을 위한 Azure Policy 샘플](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network) 

- [Azure Blueprint를 만드는 방법](../../governance/blueprints/create-blueprint-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙

**지침**: NSG(네트워크 보안 그룹) 및 네트워크 보안/트래픽 흐름과 관련하여 기타 리소스에 태그를 사용합니다. 태그 지정을 사용하면 기본 제공 및 사용자 지정 이름 값 쌍을 지정된 네트워크 리소스에 연결하여 네트워크 리소스를 구성하고 Azure 리소스를 네트워크 디자인과 다시 연결할 수 있습니다.

태그 지정과 관련된 기본 제공 Azure Policy 정의(예: "태그 및 해당 값 필요")를 사용하여 모든 리소스에서 태그를 포함하도록 만들고 태그가 없는 기존 리소스를 알립니다. 

네트워크 보안 그룹은 태그를 지원하지만 개별 보안 규칙은 지원하지 않습니다. 보안 규칙에는 일반적으로 태그에 포함되는 일부 정보를 저장하는 데 사용할 수 있는 **설명** 필드가 있습니다.

- [리소스 태그를 만들고 사용하는 방법](../../azure-resource-manager/management/tag-resources.md)

- [네트워크 보안 그룹을 사용하여 네트워크 트래픽을 필터링하는 방법](../../virtual-network/tutorial-filter-network-traffic.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동화된 도구를 사용하여 네트워크 리소스 구성 모니터링 및 변경 내용 검색

**지침**: Azure Policy를 사용하여 네트워크 리소스에 대한 구성 변경 내용을 기록합니다.  중요한 네트워크 리소스에 변경 내용이 발생하는 경우 트리거하는 Azure Monitor 내에서 경고를 만듭니다.  

 
- [Azure Policy를 구성하고 관리하는 방법](../../governance/policy/tutorials/create-and-manage.md)
 
 
- [Azure Monitor에서 경고를 만드는 방법](../../azure-monitor/alerts/alerts-activity-log.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [Azure Security Benchmark: 로깅 및 모니터링](../../security/benchmarks/security-control-logging-monitoring.md)을 참조하세요.*

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: 엔드포인트 디바이스, 네트워크 리소스 및 기타 보안 시스템에 의해 생성된 보안 데이터를 집계하기 위해 Azure Monitor를 통해 로그를 수집합니다. Azure Monitor 내에서 Log Analytics 작업 영역을 사용하여 분석을 쿼리 및 수행하고, 장기/보관 스토리지에 Azure Storage 계정을 사용하고, 필요에 따라 변경이 불가능한 스토리지 및 적용된 보존 보류와 같은 보안 기능을 사용합니다.

 
- [Azure Monitor를 사용하여 플랫폼 로그 및 메트릭을 수집하는 방법](../../azure-monitor/essentials/diagnostic-settings.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

**지침**: Azure 스토리지 분석은 BLOB, 큐 및 테이블에 대한 로그를 제공합니다. Azure Portal을 사용하여 계정에 대한 로그를 기록하도록 구성할 수 있습니다.   

 
- [Azure Storage 계정에 대한 모니터링을 구성하는 방법](manage-storage-analytics-logs.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

**지침**: Azure Storage 계정 또는 Log Analytics 작업 영역에 보안 이벤트 로그를 저장하는 경우 조직의 요구 사항에 따라 보존 정책을 설정할 수 있습니다.  

 
- [Azure Storage 계정 로그에 관한 보존 정책을 구성하는 방법](https://docs.microsoft.com/azure/storage/common/manage-storage-analytics-logs#configure-logging) 

 
- [Log Analytics에서 데이터 보존 기간 변경](https://docs.microsoft.com/azure/azure-monitor/logs/manage-cost-storage#change-the-data-retention-period)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

**지침**: Azure Storage 로그를 검토하려면 Log Analytics 제품을 통한 쿼리와 같은 일반적인 옵션과 로그 파일을 직접 볼 수 있는 고유한 옵션을 사용할 수 있습니다. Azure Storage에서 로그는 `http://accountname.blob.core.windows.net/$logs`에 직접 액세스해야 하는 BLOB에 저장됩니다(로깅 폴더는 기본적으로 숨겨져 있으므로 직접 탐색해야 합니다. (목록 명령에 표시되지 않습니다.) 

 
또한 스토리지 계정에 대한 스토리지에 Azure Defender를 사용하도록 설정합니다. 스토리지용 Azure Defender는 스토리지 계정에 액세스하거나 악용하려는 비정상적이고 잠재적으로 유해한 시도를 감지하는 추가 보안 인텔리전스 계층을 제공합니다. 보안 경고는 활동의 비정상 현상이 발생할 때 트리거됩니다. 이러한 보안 경고는 Azure Security Center와 통합되며, 의심스러운 활동에 대한 세부 정보 및 위협을 조사하고 수정하는 방법에 대한 권장 사항을 포함하여 구독 관리자에게 메일로도 전송됩니다. 
 

 
- [데이터 기록 및 검토](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#how-logs-are-stored) 
 

 
- [Azure Defender for Storage 구성](https://docs.microsoft.com/azure/storage/common/azure-defender-storage-configure?tabs=azure-portal)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상 활동에 대한 경고 사용

**지침**: Azure Security Center에서 Storage 계정의 Azure Defender를 사용하도록 설정합니다. 스토리지 계정에 대한 진단 설정을 사용하도록 설정하고 Log Analytics 작업 영역으로 로그를 보냅니다. SOAR(보안 오케스트레이션 자동화 응답) 솔루션을 제공하므로 Log Analytics 작업 영역을 Azure Sentinel에 온보딩합니다. 이를 통해 플레이북(자동화된 솔루션)을 만들어 보안 문제를 수정하는 데 사용할 수 있습니다.  

 
- [Azure Sentinel을 온보딩하는 방법](../../sentinel/quickstart-onboard.md)  

 
- [Azure Security Center에서 경고를 관리하는 방법](../../security-center/security-center-managing-and-responding-alerts.md)  

 
- [Log Analytics 로그 데이터에 대해 경고하는 방법](../../azure-monitor/alerts/tutorial-response.md)  

 
- [Azure Storage 분석 로깅](storage-analytics-logging.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

**지침**: Azure Security Center를 사용하고 Azure Defender for Storage를 사용하도록 설정하여 해시 평판 분석 및 활성 TOR 출구 노드(익명 프록시)에서 의심스러운 액세스 권한을 사용하여 Azure Storage에 대한 맬웨어 업로드를 탐지합니다. 

- [Azure Defender for Storage 구성](azure-defender-storage-configure.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

**지침**: Azure Monitor의 Azure DNS 분석(미리 보기) 솔루션은 보안, 성능 및 운영에 관련하여 DNS 인프라에 대한 인사이트를 수집합니다.  현재는 Azure Storage 계정을 지원하지 않지만 타사 DNS 로깅 솔루션을 사용할 수 있습니다.  

 
- [DNS 분석 미리 보기 솔루션으로 DNS 인프라에 대한 인사이트 수집](../../azure-monitor/insights/dns-analytics.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="identity-and-access-control"></a>ID 및 Access Control

*자세한 내용은 [Azure Security Benchmark: ID 및 액세스 제어](../../security/benchmarks/security-control-identity-access-control.md)를 참조하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정 인벤토리 유지 관리

**지침**: Azure AD(Azure Active Directory)에 명시적으로 할당되고 쿼리할 수 있어야 하는 기본 제공 역할이 있습니다. Azure AD PowerShell 모듈을 사용하여 임시 쿼리를 수행해서 관리 그룹의 구성원인 계정을 검색합니다.

- [PowerShell을 사용하여 Azure AD에서 디렉터리 역할을 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrole)

- [PowerShell을 사용하여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법](/powershell/module/azuread/get-azureaddirectoryrolemember)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 기본 암호 변경(해당하는 경우)

**지침**: Azure Storage 계정 또는 Azure AD(Azure Active Directory)에는 기본 또는 빈 암호 개념이 있습니다. Azure Storage는 Azure RBAC(Azure 역할 기반 액세스 제어) 뿐만 아니라 공유 키 및 SAS(공유 액세스 서명)를 지원하는 액세스 제어 모델을 구현합니다. 공유 키 및 SAS 인증의 특징은 호출자와 연결된 ID가 없으므로 보안 주체 권한 기반 권한 부여를 수행할 수 없습니다.

- [Azure Storage의 데이터에 대한 액세스 권한 부여](storage-auth.md)

- [보안 주체 및 Azure Storage 계정에 대한 액세스 제어 이해](storage-introduction.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 스토리지 계정에 액세스할 수 있는 전용 관리 계정의 사용에 대한 표준 운영 프로시저를 만듭니다. Azure Security Center ID와 액세스 관리를 사용하여 관리 계정 수를 모니터링합니다.

또한 Azure AD(Azure Active Directory) Privileged Identity Management Privileged Roles for Microsoft Services와 Azure Resource Manager를 사용하여 Just-In-Time/Just-Enough-Access를 사용하도록 설정할 수도 있습니다.

- [Azure Security Center ID 및 액세스 이해](../../security-center/security-center-identity-access.md)

- [Azure AD Privileged Identity Management 개요](/azure/active-directory/privileged-identity-management/)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory SSO(Single Sign-On) 사용

**지침**: 가능하면 서비스별로 개별 독립 실행형 자격 증명을 구성하는 대신 Azure AD(Azure Active Directory) SSO를 사용합니다. Azure Security Center ID 및 액세스 관리 권장 사항을 사용합니다.
 

 
- [Azure AD를 사용하는 SSO 이해](../../active-directory/manage-apps/what-is-single-sign-on.md)
 

 
- [Azure Storage의 데이터에 대한 액세스 권한 부여](storage-auth.md)
 

 
- [Azure AD를 사용하여 BLOB 및 큐에 대한 액세스 권한 부여](storage-auth-aad.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 다단계 인증 사용

**지침**: Azure AD(Azure Active Directory) 다단계 인증을 사용하도록 설정하고 Azure Security Center ID 및 액세스 관리 권장 사항에 따라 스토리지 계정 리소스를 보호합니다.

- [Azure에서 다단계 인증을 사용하는 방법](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center 내에서 ID 및 액세스를 모니터링하는 방법](../../security-center/security-center-identity-access.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: 관리 작업에 안전한 Azure 관리 워크스테이션 사용

**지침**: 스토리지 계정 리소스에 로그인하고 구성하도록 구성된 다단계 인증과 함께 PAW(권한 액세스 워크스테이션)를 사용합니다.  

 
- [Privileged Access Workstation에 대한 자세한 정보](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

 
- [Azure에서 다단계 인증을 사용하는 방법](../../active-directory/authentication/howto-mfa-getstarted.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 관리 계정의 의심스러운 활동에 대한 로그 및 경고

**지침**: Azure Security Center 위험 검색 경고를 Azure Monitor로 보내고 작업 그룹을 사용하여 사용자 지정 경고/알림을 구성합니다. Azure Defender for Storage 계정을 사용하도록 설정하여 의심스러운 활동에 대한 경고를 생성합니다. 또한 Azure AD(Azure Active Directory) 위험 검색을 사용하여 위험한 사용자 동작에 대한 경고 및 보고서를 볼 수 있습니다.

- [Azure Defender for Storage 구성](azure-defender-storage-configure.md)
 

- [Azure AD 위험 탐지 이해](../../active-directory/identity-protection/overview-identity-protection.md)
 

- [사용자 지정 경고 및 알림에 대한 작업 그룹을 구성하는 방법](../../azure-monitor/alerts/action-groups.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인된 위치에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명된 위치를 사용하여 IP 주소 범위 또는 국가/지역의 특정 논리 그룹에서만 액세스하도록 허용합니다. 

- [Azure에서 명명된 위치를 구성하는 방법](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure AD(Azure Active Directory)를 중앙 인증 및 권한 부여 시스템으로 사용합니다. Azure는 스토리지 계정의 리소스에 대한 클라이언트 액세스를 세부적으로 제어하는 Azure RBAC(Azure 역할 기반 액세스 제어)를 제공합니다.   가능한 경우 더 쉽게 손상될 수 있는 계정 키를 사용하는 대신 Azure AD 자격 증명을 보안 모범 사례로 사용합니다. 애플리케이션 설계에 Blob Storage 액세스를 위한 공유 액세스 서명이 필요한 경우, 보안을 강화하기 위해 가능한 경우 Azure AD 자격 증명을 사용하여 사용자 위임 SAS(공유 액세스 서명)를 만듭니다.

- [Azure AD 인스턴스를 만들고 구성하는 방법](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [Azure Storage 리소스 공급자를 사용하여 관리 리소스에 액세스](authorization-resource-provider.md)

- [Azure Portal에서 Azure RBAC를 사용하여 Azure BLOB 및 대기열 데이터에 대한 액세스를 구성하는 방법](storage-auth-aad-rbac-portal.md)

- [Azure Storage의 데이터에 대한 액세스 권한 부여](storage-auth.md)

- [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](storage-sas-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스 검토 및 조정

**지침**: Azure AD(Azure Active Directory) 로그를 검토하여 스토리지 계정 관리 역할이 있는 계정을 비롯한 부실 계정을 검색할 수 있습니다. 또한 Azure ID 액세스 검토를 사용하여 그룹 멤버 자격, 스토리지 계정 리소스에 액세스하는 데 사용할 수 있는 엔터프라이즈 애플리케이션에 대한 액세스 및 역할 할당을 효율적으로 관리합니다. 사용자 액세스를 정기적으로 검토하여 적합한 사용자만 계속 액세스할 수 있도록 합니다. 
 

 
또한 SAS(공유 액세스 서명)를 사용하여 데이터 보안을 손상시키지 않고 스토리지 계정의 리소스에 대한 보안 위임 액세스를 제공할 수 있습니다. 클라이언트가 액세스할 수 있는 리소스, 해당 리소스에 대한 사용 권한 및 SAS 유효 기간을 다른 매개 변수 중에서 제어할 수 있습니다.
 

 
컨테이너 및 BLOB 대한 익명 읽기 권한도 검토합니다. 기본적으로, 컨테이너와 컨테이너 내의 모든 Blob은 적절한 권한이 부여된 사용자만 액세스할 수 있습니다. Azure Monitor를 사용하여 익명 인증 조건을 통해 스토리지 계정에 대한 익명 액세스에 대해 경고할 수 있습니다.
 

 
예기치 않은 사용자 계정 액세스의 위험을 줄이는 한 가지 효과적인 방법은 사용자에게 부여하는 액세스 기간을 제한하는 것입니다. 시간이 제한된 SAS URI는 스토리지 계정에 대한 사용자 액세스를 자동으로 만료하는 한 가지 효과적인 방법입니다. 또한 스토리지 계정 키를 자주 순환하면 스토리지 계정 키를 통해 예상치 못한 액세스에 제한된 기간이 있도록 합니다.
 

 
- [Azure AD 보고 이해](/azure/active-directory/reports-monitoring/) 
 

 
- [Azure Storage 계정 수준에서 액세스를 확인하고 변경하는 방법](storage-auth-aad-rbac-portal.md)
 

 
- [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](storage-sas-overview.md)
 

 
- [컨테이너 및 Blob에 대한 익명 읽기 권한 관리](../blobs/anonymous-read-access-configure.md)
 

 
- [Azure Portal에서 스토리지 계정 모니터링](manage-storage-analytics-logs.md)
 

 
- [스토리지 계정 액세스 키 관리](storage-account-keys-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 비활성화된 자격 증명에 대한 액세스 시도 모니터링

**지침**: 스토리지 분석은 스토리지 서비스에 대해 성공한 요청과 실패한 요청 관련 상세 정보를 기록합니다. 모든 로그는 $logs라는 컨테이너의 블록 Blob에 저장되며, Storage 계정에 대해 스토리지 분석을 사용하도록 설정하면 자동으로 만들어집니다.
 

Azure AD(Azure Active Directory) 사용자 계정에 대한 진단 설정을 만들어서 감사 로그 및 로그인 로그를 Log Analytics 작업 영역으로 보냅니다. Log Analytics 작업 영역 내에서 원하는 경고를 구성할 수 있습니다.

Azure Storage 계정에 대한 인증 오류를 모니터링하려면 스토리지 리소스 메트릭에 대해 특정 임계값에 도달했을 때 알리도록 경고를 만들 수 있습니다. 또한 Azure Monitor를 사용하여 익명 인증 조건을 통한 스토리지 계정에 대한 익명 액세스를 경고합니다.

- [Azure Storage 분석 로깅](storage-analytics-logging.md)
 

- [Azure 활동 로그를 Azure Monitor에 통합하는 방법](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)
 

- [Azure Storage 계정에 대한 메트릭 경고를 구성하는 방법](manage-storage-analytics-logs.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대한 경고

**지침**: Azure AD(Azure Active Directory)의 위험 및 ID 보호 기능을 사용하여 스토리지 계정 리소스와 관련하여 검색된 의심스러운 동작에 대한 자동 응답을 구성합니다. 조직의 보안 대응을 구현하기 위해 Azure Sentinel을 통해 자동화된 대응을 사용하도록 설정해야 합니다.

- [Azure AD 위험한 로그인을 확인하는 방법](../../active-directory/identity-protection/overview-identity-protection.md)

- [ID 보호 위험 정책을 구성하고 사용하도록 설정하는 방법](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel을 온보딩하는 방법](../../sentinel/quickstart-onboard.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오 중 관련 고객 데이터에 대한 액세스 권한을 Microsoft에 제공

**참고 자료**: Microsoft에서 고객 데이터에 액세스해야 하는 지원 시나리오에서는 고객 Lockbox(스토리지 계정에 대한 미리 보기)가 고객 데이터 액세스 요청을 소비자가 검토, 승인 또는 거부할 수 있는 인터페이스를 제공합니다. Microsoft는 스토리지 계정 내에 저장된 조직의 비밀에 대한 액세스를 요구하거나 요청하지 않습니다.

- [고객 Lockbox 이해](../../security/fundamentals/customer-lockbox-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-protection"></a>데이터 보호

자세한 내용은 [Azure Security Benchmark: 데이터 보호](../../security/benchmarks/security-control-data-protection.md)를 참조하세요.

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요한 정보의 인벤토리 유지 관리

**지침**: 태그를 사용하여 중요한 정보를 저장하거나 처리하는 스토리지 계정 리소스를 추적할 수 있도록 지원합니다. 

- [태그를 만들고 사용하는 방법](../../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요한 정보를 저장하거나 처리하는 시스템 격리

**지침**: 환경 및 데이터 민감도와 같은 개별 보안 도메인에 대해 별도의 구독, 관리 그룹 및 스토리지 계정을 사용하여 격리를 구현합니다.  스토리지 계정을 제한하여 사용하는 네트워크의 유형 및 하위 집합에 따라 애플리케이션 및 엔터프라이즈 환경에 필요한 스토리지 계정의 액세스 수준을 제어할 수 있습니다. 네트워크 규칙이 구성되면 지정된 네트워크 세트를 통해 데이터를 요청하는 애플리케이션만 스토리지 계정에 액세스할 수 있습니다. Azure RBAC(Azure RBAC)를 통해 Azure Storage에 대한 액세스를 제어할 수 있습니다.

또한 가상 네트워크와 서비스 간의 트래픽이 Microsoft 백본 네트워크를 통과할 때 보안이 개선되어 공용 인터넷에서 노출되지 않도록 프라이빗 엔드포인트를 구성할 수도 있습니다.

- [추가 Azure 구독을 만드는 방법](../../cost-management-billing/manage/create-subscription.md)

- [관리 그룹을 만드는 방법](../../governance/management-groups/create-management-group-portal.md)

- [태그를 만들고 사용하는 방법](../../azure-resource-manager/management/tag-resources.md)

- [Azure Storage 방화벽 및 가상 네트워크 구성](storage-network-security.md)

- [Virtual Network 서비스 엔드포인트](../../virtual-network/virtual-network-service-endpoints-overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요한 정보에 대한 무단 전송 모니터링 및 차단

**지침**: 중요한 정보를 저장하거나 처리하는 스토리지 계정 리소스의 경우 태그를 사용하여 리소스를 중요한 리소스로 표시합니다. 반출을 통한 데이터 손실의 위험을 줄이려면 Azure Firewall을 사용하여 Azure Storage 계정에 대한 아웃바운드 네트워크 트래픽을 제한합니다.  

또한 가상 네트워크 서비스 엔드포인트 정책을 사용하여 서비스 엔드포인트를 통해 Azure Storage 계정으로 송신되는 가상 네트워크 트래픽을 필터링하고 특정 Azure Storage 계정에만 데이터 유출을 허용합니다.

- [Azure Storage 방화벽 및 가상 네트워크 구성](../../virtual-network/virtual-network-service-endpoint-policies-overview.md)

- [Azure Storage에 대한 가상 네트워크 서비스 엔드포인트 정책](../../private-link/tutorial-private-endpoint-storage-portal.md)

- [Azure의 고객 데이터 보호 이해](../../security/fundamentals/protection-customer-data.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요한 정보 암호화

**지침**: 스토리지 계정에 필요한 보안 전송을 활성화하여 HTTPS를 강제 사용할 수 있습니다. 이 옵션을 사용하도록 설정하면 HTTP를 사용한 연결이 거부됩니다.  또한 Azure Security Center 및 Azure Policy를 사용하여 스토리지 계정에 대한 보안 전송을 적용합니다.

- [Azure Storage에서 보안 전송을 요구하는 방법](storage-require-secure-transfer.md)

- [Azure Security Center에서 모니터링되는 Azure 보안 정책](../../security-center/policy-reference.md)

**책임**: 공유됨

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며 [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 컨트롤과 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.Storage**:

[!INCLUDE [Resource Policy for Microsoft.Storage 4.4](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-4-4.md)]

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용하여 중요한 데이터 식별

**지침**: Azure Storage 계정 및 관련 리소스에 대한 데이터 식별 기능을 아직 사용할 수 없습니다. 규정 준수를 위해 필요한 경우 타사 솔루션을 구현합니다. 

- [Azure의 고객 데이터 보호 이해](../../security/fundamentals/protection-customer-data.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6: 역할 기반 액세스 제어를 사용하여 리소스에 대한 액세스 제어

**지침**: Azure AD(Azure Active Directory)는 Azure RBAC(Azure 역할 기반 액세스 제어)를 통해 보안 리소스에 대한 액세스 권한을 부여합니다. Azure Storage는 BLOB 또는 큐 데이터에 액세스하는 데 사용되는 일반 권한 집합을 포함하는 Azure 기본 제공 RBAC 역할 집합을 정의합니다.

- [Azure Storage 계정에 대한 Azure RBAC 역할을 할당하는 방법](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal#assign-azure-roles-using-the-azure-portal)

- [Azure Storage 리소스 공급자를 사용하여 관리 리소스에 액세스](authorization-resource-provider.md)

- [Azure Portal에서 Azure RBAC를 사용하여 Azure BLOB 및 대기열 데이터에 대한 액세스를 구성하는 방법](storage-auth-aad-rbac-portal.md)

- [Azure AD 인스턴스를 만들고 구성하는 방법](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Azure Storage의 데이터에 대한 액세스 권한 부여](storage-auth.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 중요한 저장 정보 암호화

**지침**: Azure Storage 암호화는 모든 스토리지 계정에 사용하도록 설정되며, 사용하지 않도록 설정할 수 없습니다. Azure Storage는 데이터를 클라우드에 보존할 때 자동으로 암호화합니다. Azure Storage에서 데이터를 읽을 때 데이터가 반환되기 전에 Azure Storage에서 암호가 해독됩니다. Azure Storage 암호화를 사용하면 코드를 수정하거나 애플리케이션에 코드를 추가하지 않고도 미사용 데이터를 보호할 수 있습니다. 

- [유휴 상태의 Azure Storage 암호화 이해](storage-service-encryption.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요한 Azure 리소스에 대한 변경 내용 기록 및 경고

**지침**: Azure Activity Log와 함께 Azure Monitor를 사용하여 스토리지 계정 리소스가 변경되는 시점에 대한 경고를 생성합니다.  Azure Storage 로깅을 사용하여 Azure Storage에 수행된 각 요청에 대한 권한이 있는지 추적할 수도 있습니다. 로그는 OAuth 2.0 토큰을 사용하거나 공유 키를 사용하거나 SAS(공유 액세스 서명)를 사용하여 요청이 익명으로 수행되었는지 여부를 나타냅니다.  또한 Azure Monitor를 사용하여 익명 인증 조건을 통한 스토리지 계정에 대한 익명 액세스를 경고합니다. 

 
- [Azure 활동 로그 이벤트에 대한 경고를 만드는 방법](../../azure-monitor/alerts/alerts-activity-log.md) 

 
- [Azure Storage 분석 로깅](storage-analytics-logging.md) 

 
- [Azure Storage 계정에 대한 메트릭 경고를 구성하는 방법](manage-storage-analytics-logs.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="vulnerability-management"></a>취약성 관리

*자세한 내용은 [Azure Security Benchmark: 취약성 관리](../../security/benchmarks/security-control-vulnerability-management.md)를 참조하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화된 취약성 검사 도구 실행

**지침**: Azure Security Center의 권장 사항에 따라 스토리지 계정의 구성을 지속적으로 감사하고 모니터링합니다.  

- [보안 권장 사항 - 참조 가이드](../../security-center/recommendations-reference.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 연속 취약성 검색 비교

**지침**: 해당 사항 없음. Microsoft는 스토리지 계정을 지원하는 기본 시스템에서 취약성 관리를 수행합니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용하여 검색된 취약성의 수정 우선 순위 지정

**참고 자료**: Azure Security Center에서 제공하는 기본 위험 등급(보안 점수)을 사용합니다. 

- [Azure Security Center 보안 점수의 이해](/azure/security-center/security-center-secure-score)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [Azure Security Benchmark: 인벤토리 및 자산 관리](../../security/benchmarks/security-control-inventory-asset-management.md)를 참조하세요.*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: 자동화된 자산 검색 솔루션 사용

**지침**: Azure Resource Graph를 사용하여 제품 내의 모든 리소스(스토리지 계정 포함)를 쿼리하고 검색합니다. 테넌트에서 적절한 권한(읽기)이 있는지 확인하고, 모든 Azure 구독 및 구독 내의 리소스를 열거할 수 있습니다.

- [Azure Graph를 사용하여 쿼리를 만드는 방법](../../governance/resource-graph/first-query-portal.md)

- [Azure 구독을 확인하는 방법](/powershell/module/az.accounts/get-azsubscription)

- [Azure RBAC 이해](../../role-based-access-control/overview.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타데이터 유지 관리

**지침**: 메타데이터를 제공하는 스토리지 계정 리소스에 태그를 적용하여 논리적으로 분류합니다. 

- [태그를 만들고 사용하는 방법](../../azure-resource-manager/management/tag-resources.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한 없는 Azure 리소스 삭제

**지침**: 태그 지정, 관리 그룹 및 별도의 구독(해당되는 경우)을 사용하여 스토리지 계정 및 관련 리소스를 구성하고 추적할 수 있습니다. 정기적으로 인벤토리를 조정하고, 구독에서 권한 없는 리소스가 적시에 삭제되도록 합니다. 

또한 Azure Defender for Storage를 사용하여 권한이 없는 Azure 리소스를 검색합니다. 

- [추가 Azure 구독을 만드는 방법](../../cost-management-billing/manage/create-subscription.md) 

- [관리 그룹을 만드는 방법](../../governance/management-groups/create-management-group-portal.md)

- [태그를 만들고 사용하는 방법](../../azure-resource-manager/management/tag-resources.md)

- [Azure Defender for Storage 구성](azure-defender-storage-configure.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4: 승인된 Azure 리소스의 인벤토리 정의 및 유지 관리

**참고 자료**: 조직 요구 사항에 따라 승인된 Azure 리소스의 인벤토리를 만들어야 합니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인되지 않은 Azure 리소스 모니터링

**지침**: Azure Policy에서 다음의 기본 제공 정책 정의를 사용해 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다. 

 
 - 허용되지 않는 리소스 종류 
 
 - 허용되는 리소스 유형 
 

 
또한 Azure Resource Graph를 사용하여 구독 내에서 리소스를 쿼리하거나 검색합니다. 이는 스토리지 계정을 사용하는 환경 등의 높은 보안 기반 환경에서 유용할 수 있습니다. 
 

 
- [Azure Policy를 구성하고 관리하는 방법](../../governance/policy/tutorials/create-and-manage.md) 
 

 
- [Azure Graph를 사용하여 쿼리를 만드는 방법](../../governance/resource-graph/first-query-portal.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인되지 않은 Azure 리소스 및 소프트웨어 애플리케이션 제거

**지침**: 고객은 고객의 회사 정책에 따라 Azure Policy로 리소스 만들기 또는 사용을 방지할 수 있습니다. 

- [Azure Policy를 구성하고 관리하는 방법](../../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인된 Azure 서비스만 사용

**지침**: Azure Policy에서 다음의 기본 제공 정책 정의를 사용해 고객 구독에서 만들 수 있는 리소스 종류를 제한합니다.

- 허용되지 않는 리소스 종류
- 허용되는 리소스 유형

참조된 링크에서 추가 정보를 확인할 수 있습니다.

- [Azure Policy를 구성하고 관리하는 방법](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy를 사용하여 특정 리소스 종류를 거부하는 방법](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**책임**: Customer

**Azure Security Center 모니터링**: [Azure Security Benchmark](/azure/governance/policy/samples/azure-security-benchmark)는 Security Center에 대한 기본 정책 이니셔티브이며, [Security Center 권장 사항](/azure/security-center/security-center-recommendations)의 기초입니다. 이 컨트롤과 관련된 Azure Policy 정의는 Security Center에서 자동으로 사용하도록 설정됩니다. 이 컨트롤과 관련된 경고에는 관련 서비스에 대한 [Azure Defender](/azure/security-center/azure-defender) 계획이 필요할 수 있습니다.

**Azure Policy 기본 제공 정의 - Microsoft.ClassicStorage**:

[!INCLUDE [Resource Policy for Microsoft.ClassicStorage 6.9](../../../includes/policy/standards/asb/rp-controls/microsoft.classicstorage-6-9.md)]

**Azure Policy 기본 제공 정의 - Microsoft.Storage**:

[!INCLUDE [Resource Policy for Microsoft.Storage 6.9](../../../includes/policy/standards/asb/rp-controls/microsoft.storage-6-9.md)]

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11: Azure Resource Manager와 상호 작용하는 사용자 기능 제한

**지침**: "Microsoft Azure 관리" 앱에 대한 "액세스 차단"을 구성하여 사용자가 Azure Resource Manager와 상호 작용하는 기능을 제한하려면 Azure 조건부 액세스를 사용합니다. 이렇게 하면 스토리지 계정과 같은 높은 보안 환경 내의 리소스 생성 및 변경을 방지할 수 있습니다. 

- [Azure Resource Manager에 대한 액세스를 차단하도록 조건부 액세스를 구성하는 방법](../../role-based-access-control/conditional-access-azure-management.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [Azure Security Benchmark: 보안 구성](../../security/benchmarks/security-control-secure-configuration.md)을 참조하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대한 보안 구성 설정

**지침**: **Microsoft.Storage** 네임스페이스에서 Azure Policy를 사용하여 스토리지 계정 인스턴스의 구성을 감사하거나 적용하기 위한 사용자 지정 정책을 만듭니다. 다음과 같은 Azure Storage 계정에 기본 제공 Azure Policy 정의를 사용할 수도 있습니다.

- 스토리지 계정에 대한 무제한 네트워크 액세스 감사
- Azure Defender for Storage 배포
- 스토리지 계정을 새 Azure Resource Manager 리소스로 마이그레이션해야 합니다.
- Storage 계정에 보안 전송을 사용하도록 설정해야 합니다.

스토리지 계정에 대한 보안 구성 기준으로 Azure Security Center의 권장 사항을 사용합니다.

- [사용 가능한 Azure Policy 별칭 확인 방법](/powershell/module/az.resources/get-azpolicyalias)

- [Azure Policy를 구성하고 관리하는 방법](../../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지 관리

**지침**: [거부] 및 [존재하지 않으면 배포] Azure Policy를 사용하여 보안 설정을 스토리지 계정 리소스 전체에 적용합니다. 

- [Azure Policy를 구성하고 관리하는 방법](../../governance/policy/tutorials/create-and-manage.md) 

- [Azure Policy 효과 이해](../../governance/policy/concepts/effects.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스 구성을 안전하게 저장

**지침**: Azure Repos를 사용하여 사용자 지정 Azure Policy, Azure Resource Manager 템플릿, Desired State Configuration 스크립트 등과 같은 코드를 안전하게 저장하고 관리합니다. Azure DevOps에서 관리하는 리소스에 액세스하려면 Azure DevOps와 통합된 경우 Azure AD(Azure Active Directory)에 정의된 특정 사용자, 기본 제공 보안 그룹 또는 그룹에 대한 권한을 부여하거나 거부할 수 있습니다. 또는 TFS와 통합된 경우 Azure AD에 대한 권한을 부여하거나 거부할 수 있습니다.

- [Azure DevOps에 코드를 저장하는 방법](/azure/devops/repos/git/gitworkflow)

- [Azure DevOps의 그룹 및 사용 권한 정보](/azure/devops/organizations/security/about-permissions)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Azure 리소스를 위한 구성 관리 도구 배포

**지침**: Azure Policy를 활용하여 스토리지 계정에 대한 시스템 구성을 경고, 감사 및 적용합니다. 또한 정책 예외를 관리하는 프로세스와 파이프라인을 개발합니다. 

- [ Azure Policy를 구성하고 관리하는 방법](../../governance/policy/tutorials/create-and-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Azure 리소스에 대한 자동화된 구성 모니터링 구현

**지침**: Azure Security Center를 활용하여 Azure Storage 계정 리소스에 대한 기준 검사를 수행합니다. 

- [Azure Security Center에서 권장 사항을 수정하는 방법](../../security-center/security-center-remediate-recommendations.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="711-manage-azure-secrets-securely"></a>7.11: 안전하게 Azure 비밀 관리

**지침**: Azure Storage는 데이터를 클라우드에 보존할 때 자동으로 암호화합니다. 스토리지 계정의 암호화에 Microsoft 관리 키를 사용하거나 자체 키로 암호화를 관리할 수 있습니다. 고객이 제공한 키를 사용하는 경우 Azure Key Vault를 사용하여 키를 안전하게 저장할 수 있습니다. 

또한 스토리지 계정 키의 손실 또는 공개 영향을 제한하기 위해 스토리지 계정 키를 자주 순환합니다.

- [미사용 데이터에 대한 Azure Storage 암호화](storage-service-encryption.md)

- [스토리지 계정 액세스 키 관리](storage-account-keys-manage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: 안전하게 자동으로 ID 관리

**지침**: Azure AD(Azure Active Directory) 및 관리 ID를 사용하여 Azure Storage 계정 내의 BLOB 및 큐에 대한 액세스 권한을 부여합니다. Azure BLOB 및 큐 스토리지는 Azure 리소스에 대한 관리 ID가 있는 Azure AD 인증을 지원합니다. 

Azure 리소스의 관리 ID를 사용하여 Azure 가상 머신, 함수 앱, 가상 머신 확장 집합 및 기타 서비스에서 실행 중인 애플리케이션의 Azure AD 자격 증명을 사용하여 BLOB 및 큐 데이터에 대한 액세스 권한을 부여할 수 있습니다. Azure AD 인증과 함께 Azure 리소스의 관리 ID를 사용하면 클라우드에서 실행되는 애플리케이션에 자격 증명을 저장할 필요가 없습니다.

- [관리 ID를 사용하여 Azure BLOB 및 큐 데이터에 대한 액세스 권한을 부여하는 방법](storage-auth-aad-rbac-portal.md)

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

**지침**: Azure Defender for Storage를 사용하도록 설정하여 해시 평판 분석 및 활성 TOR 출구 노드(익명 프록시)에서 의심스러운 액세스 권한을 사용하여 Azure Storage에 대한 맬웨어 업로드를 탐지합니다. 
 

 
또한 App Service, Data Lake Storage, Blob Storage 등과 같이 컴퓨팅이 아닌 Azure 리소스에 업로드하기 전에 조직의 요구 사항을 충족하기 위해 콘텐츠에 대한 맬웨어를 사전 검색할 수도 있습니다.
 

 
- [Azure Defender for Storage 구성](azure-defender-storage-configure.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [Azure Security Benchmark: 데이터 복구](../../security/benchmarks/security-control-data-recovery.md)를 참조하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 자동화된 정기 백업 보장

**지침**: Microsoft Azure 스토리지 계정의 데이터는 내구성 및 고가용성을 보장하기 위해 항상 자동으로 복제됩니다. Azure Storage는 계획된 이벤트 그리고 일시적인 하드웨어 오류, 네트워크 또는 정전, 대규모 자연 재해 등의 계획되었거나 계획되지 않은 이벤트로부터 데이터를 보호하기 위해 데이터를 복사합니다. 동일한 데이터 센터, 동일한 지역 내의 영역 데이터 센터 또는 지리적으로 분리된 지역 간에 데이터를 복제하도록 선택할 수 있습니다. 

Azure Automation을 사용하도록 설정하여 BLOB의 정기적인 스냅샷을 만들 수도 있습니다.

- [Azure Storage 중복성 및 서비스 수준 계약 이해](storage-redundancy.md)

- [BLOB의 스냅샷 만들기](/rest/api/storageservices/creating-a-snapshot-of-a-blob)

- [Azure Automation 개요](../../automation/automation-intro.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업 수행 및 고객 관리형 키 백업

**지침**: 스토리지 계정 지원 서비스에서 데이터를 백업하려면 azcopy 또는 타사 도구 등 여러 가지 방법을 사용할 수 있습니다. Azure Blob Storage에 대한 변경 불가능한 스토리지를 사용하면 사용자가 중요 비즈니스용 데이터 개체를 WORM(Write Once, Read Many) 상태로 저장할 수 있습니다. 이 상태는 사용자가 지정한 간격 동안 데이터를 지울 수 없고 수정할 수 없게 만듭니다.
 

Azure CLI 또는 PowerShell을 사용하여 Azure Key Vault 내에서 고객 관리/제공 키를 백업할 수 있습니다. 

 
- [AzCopy 시작](storage-use-azcopy-v10.md)  

- [Blob Storage에 대한 불변성 정책 설정 및 관리](../blobs/storage-blob-immutability-policies-manage.md)
 

 
- [Azure에서 키 자격 증명 모음 키를 백업하는 방법](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리형 키를 포함한 모든 백업의 유효성 검사

**지침**: 다음 PowerShell 명령을 사용하여 정기적으로 Key Vault 인증서, 키, 관리 스토리지 계정 및 비밀의 데이터 복원을 수행합니다.

Restore-AzKeyVaultCertificate

Restore-AzKeyVaultKey

Restore-AzKeyVaultManagedStorageAccount

Restore-AzKeyVaultSecret

- [Key Vault 인증서를 복원하는 방법](/powershell/module/az.keyvault/restore-azkeyvaultcertificate)

- [Key Vault 키를 복원하는 방법](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [Key Vault 관리 스토리지 계정을 복원하는 방법](/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Key Vault 비밀을 복원하는 방법](/powershell/module/az.keyvault/restore-azkeyvaultsecret)

- [AzCopy는 블록, 파일 및 테이블 데이터를 스토리지 계정 간에 복사하는 데 사용할 수 있는 명령줄 유틸리티입니다.](storage-use-azcopy-v10.md)

참고: Azure Table Storage 서비스 간에 데이터를 복사하려는 경우 AzCopy 버전 7.3을 설치합니다.

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리형 키 보호 보장

**지침**: 스토리지 계정에서 고객 관리 키를 사용하려면 Azure Key Vault를 사용하여 키를 저장해야 합니다. 키 자격 증명 모음에서 일시 삭제 및 제거 안 함 속성을 둘 다 사용하도록 설정해야 합니다.  Key Vault의 일시 삭제 기능을 사용하면 삭제된 자격 증명 모음 및 자격 증명 모음 개체(예: 키, 비밀 및 인증서)를 복구할 수 있습니다.  스토리지 계정 데이터를 Azure Storage BLOB에 백업하는 경우, BLOB 또는 BLOB 스냅샷이 삭제될 때 데이터를 저장하고 복구하도록 일시 삭제를 사용하도록 설정합니다.  백업을 중요한 데이터로 처리하고 이 기준의 일부로 관련 액세스 및 데이터 보호 제어를 적용해야 합니다.  또한 향상된 보호를 위해 중요 비즈니스용 데이터 개체를 WORM(Write Once, Read Many) 상태로 저장할 수 있습니다.

- [Azure Key Vault의 일시 삭제를 사용하는 방법](../../key-vault/general/key-vault-recovery.md)

- [Azure Storage Blob에 대한 일시 삭제](../blobs/soft-delete-blob-overview.md)

- [비즈니스에 중요한 BLOB 데이터를 변경이 불가능한 스토리지에 저장](../blobs/storage-blob-immutable-storage.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="incident-response"></a>사고 대응

자세한 내용은 [Azure Security Benchmark: 인시던트 응답](../../security/benchmarks/security-control-incident-response.md)을 참조하세요.

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 대응 지침 만들기

**지침**: 조직에 대한 인시던트 대응 지침을 작성합니다. 탐지에서 인시던트 사후 검토까지의 인시던트 처리/관리 단계뿐만 아니라 담당자의 모든 역할도 정의하는 인시던트 대응 계획이 작성되어 있는지 확인합니다.

- [사용자 고유의 보안 인시던트 대응 프로세스를 구축하는 방법에 대한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [NIST의 컴퓨터 보안 인시던트 처리 가이드](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center에서 심각도를 각 경고에 할당하여 먼저 조사해야 하는 경고에 대한 우선 순위를 지정합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용되는 결과 또는 분석의 신뢰도 및 경고가 발생한 활동에 악의적인 의도가 있었다는 신뢰 수준을 기준으로 합니다. 

 
또한 구독(예: 프로덕션, 비 프로덕션)을 명확하게 표시하고 Azure 리소스, 특히 중요한 데이터를 처리하는 리소스를 명확하게 식별하고 분류하는 명명 시스템을 만듭니다.  인시던트가 발생한 Azure 리소스 및 환경의 중요도에 따라 경고의 수정에 대한 우선 순위를 지정해야 합니다.
 

 
- [Azure Security Center의 보안 경고](../../security-center/security-center-alerts-overview.md)
 

 
- [태그를 사용하여 Azure 리소스 구성](../../azure-resource-manager/management/tag-resources.md).

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="103-test-security-response-procedures"></a>10.3: 보안 대응 프로시저 테스트

**지침**: Azure 리소스를 보호하는 데 도움이 되도록 시스템의 인시던트 대응 기능을 정기적으로 테스트합니다. 약점과 결함을 식별하고 필요에 따라 계획을 수정합니다.

- [NIST 게시물 - IT 계획 및 기능에 대한 테스트, 학습 및 연습 프로그램에 대한 가이드](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트 연락처 세부 정보 제공 및 보안 인시던트에 대한 경고 알림 구성

**지침**: MSRC(Microsoft 보안 대응 센터)에서 불법적이거나 권한이 없는 당사자가 데이터에 액세스했다고 검색하는 경우 Microsoft에서 보안 인시던트 연락처 정보를 사용하여 사용자에게 연락합니다. 문제가 해결되었는지 확인하기 위해 사후에 인시던트를 검토합니다.

- [Azure Security Center 보안 연락처를 설정하는 방법](../../security-center/security-center-provide-security-contact-details.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 대응 시스템에 통합

**지침**: Azure 리소스에 대한 위험을 식별하는 데 도움이 되도록 연속 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 연속 내보내기를 사용하면 경고 및 추천 사항을 수동으로 또는 지속적으로 내보낼 수 있습니다. Azure Security Center 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다.

- [연속 내보내기를 구성하는 방법](../../security-center/continuous-export.md)

- [경고를 Azure Sentinel로 스트림하는 방법](../../sentinel/connect-azure-security-center.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대한 대응 자동화

**지침**: Azure Security Center의 워크플로 자동화 기능을 사용하여 보안 경고 및 권장 사항에 관한 'Logic Apps'를 통해 응답을 자동으로 트리거함으로써 Azure 리소스를 보호합니다.
    

- [ 워크플로 자동화 및 Logic App을 구성하는 방법](../../security-center/workflow-automation.md)

**책임**: Customer

**Azure Security Center 모니터링**: 없음

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [Azure Security Benchmark: 침투 테스트 및 레드 팀 연습](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)을 참조하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대한 침투 테스트를 정기적으로 수행 및 모든 중요한 보안 결과를 수정

**지침**: Microsoft Engagement 규칙에 따라 침투 테스트가 Microsoft 정책을 위반하지 않는지 확인합니다. Microsoft 관리형 클라우드 인프라, 서비스, 애플리케이션에 대한 Microsoft의 Red Teaming 및 라이브 사이트 침투 테스트 전략과 실행을 사용합니다.

- [침투 테스트 시행 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud 레드 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**책임**: 공유됨

**Azure Security Center 모니터링**: 없음

## <a name="next-steps"></a>다음 단계

- [Azure Security Benchmark V2 개요](/azure/security/benchmarks/overview)를 참조하세요.
- [Azure 보안 기준](/azure/security/benchmarks/security-baselines-overview)에 대해 자세히 알아보세요.
