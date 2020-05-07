---
title: Azure Functions에 대 한 Azure 보안 기준
description: Azure Functions에 대 한 Azure 보안 기준
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 040eeda3edc8aa1165915a157cb7e1bdd1594740
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796500"
---
# <a name="azure-security-baseline-for-azure-functions"></a>Azure Functions에 대 한 Azure 보안 기준

Azure Functions에 대 한 Azure 보안 기준에는 배포의 보안 상태를 개선 하는 데 도움이 되는 권장 사항이 포함 되어 있습니다.

모범 사례 지침을 사용 하 여 Azure에서 클라우드 솔루션을 보호할 수 있는 방법에 대 한 권장 사항을 제공 하는 [Azure Security 벤치 마크 버전 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)에서이 서비스에 대 한 기준을 가져옵니다.

자세한 내용은 [Azure 보안 기준 개요](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)를 참조 하세요.

## <a name="network-security"></a>네트워크 보안

*자세한 내용은 [보안 제어: 네트워크 보안](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)을 참조 하세요.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Virtual Network에서 네트워크 보안 그룹 또는 Azure 방화벽을 사용 하 여 리소스를 보호 합니다.

**지침**: Azure virtual network와 Azure Functions apps 통합 프리미엄 계획에서 실행 되는 함수 앱은 "VNet 통합" 기능을 포함 하는 Azure App Service의 웹 앱과 동일한 호스팅 기능을 제공 합니다.  Azure virtual network를 사용 하면 Azure Functions와 같은 많은 Azure 리소스를 인터넷에서 라우팅할 수 없는 네트워크에 저장할 수 있습니다.

- [Azure Virtual Network와 기능을 통합 하는 방법](https://docs.microsoft.com/azure/azure-functions/functions-create-vnet)

- [Azure Functions 및 Azure App Service에 대 한 Vnet 통합 이해](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Vnet, 서브넷 및 Nic의 구성 및 트래픽을 모니터링 하 고 기록 합니다.

**지침**: Azure Security Center을 사용 하 고 네트워크 보호 권장 사항을 따라 Azure Functions 앱과 관련 된 네트워크 리소스 및 네트워크 구성을 보호 합니다.

Azure Functions 구현에서 NSGs (네트워크 보안 그룹)를 사용 하는 경우 NSGS 흐름 로그를 사용 하도록 설정 하 고 로그를 트래픽 감사에 대 한 Azure Storage 계정으로 보냅니다. 또한 Log Analytics 작업 영역에 NSG 흐름 로그를 보내고 트래픽 분석를 사용 하 여 Azure 클라우드의 트래픽 흐름에 대 한 통찰력을 제공할 수 있습니다. 트래픽 분석의 장점 중 일부는 네트워크 활동을 시각화 하 고 핫 스폿을 식별 하 고, 보안 위협을 식별 하 고, 트래픽 흐름 패턴을 이해 하 고, 네트워크 구성을 잘못 파악할 수 있다는 것입니다.

- [Azure Security Center에서 제공 하는 네트워크 보안 이해](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

- [NSG 흐름 로그를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [트래픽 분석 사용 및 사용 방법](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="13-protect-critical-web-applications"></a>1.3: 중요 한 웹 응용 프로그램 보호

**지침**: 프로덕션 환경에서 Azure 함수 끝점을 완벽 하 게 보호 하려면 다음 함수 앱 수준 보안 옵션 중 하나를 구현 하는 것을 고려해 야 합니다.
- 함수 앱에 대 한 App Service 인증/권한 부여를 설정 합니다.
- Azure API Management (APIM)를 사용 하 여 요청을 인증 하거나
- Azure App Service Environment에 함수 앱을 배포 합니다.

또한 프로덕션 Azure Functions에 대해 원격 디버깅을 사용 하지 않도록 설정 했는지 확인 합니다. 또한 CORS (크로스-원본 자원 공유)는 모든 도메인에서 Azure 함수 앱에 액세스할 수 있도록 허용 해서는 안 됩니다. 필요한 도메인만 Azure 함수 앱과 상호 작용 하도록 허용 합니다.

들어오는 트래픽의 추가 검사를 위해 네트워킹 구성의 일부로 WAF (웹 응용 프로그램 방화벽)를 배포 하는 것이 좋습니다. WAF에 대해 진단 설정을 사용 하도록 설정 하 고 로그를 저장소 계정, 이벤트 허브 또는 Log Analytics 작업 영역에 수집 합니다. 

- [프로덕션 환경에서 Azure 함수 끝점을 보호 하는 방법](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#secure-an-http-endpoint-in-production)

- [Azure WAF를 배포 하는 방법](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: 알려진 악성 IP 주소와의 통신 거부

**지침**: DDoS 공격 으로부터 보호 하기 위해 함수 앱과 연결 된 가상 네트워크에서 DDoS Protection Standard를 사용 하도록 설정 합니다. Azure Security Center 통합 위협 인텔리전스를 사용 하 여 알려진 악성 또는 사용 되지 않는 공용 IP 주소와의 통신을 거부 합니다.
또한 들어오는 모든 요청을 인증 하 고 악성 트래픽을 필터링 하기 위해 Azure 웹 응용 프로그램 방화벽과 같은 프런트 엔드 게이트웨이를 구성 합니다. Azure 웹 응용 프로그램 방화벽은 인바운드 웹 트래픽을 검사 하 여 SQL 삽입, 사이트 간 스크립팅, 맬웨어 업로드 및 DDoS 공격을 차단 하 여 Azure 함수 앱의 보안을 유지할 수 있습니다. WAF를 도입 하려면 개인 끝점 (미리 보기)을 App Service Environment 또는 사용 해야 합니다. 프로덕션 워크 로드와 함께 사용 하기 전에 개인 끝점이 더 이상 (미리 보기)에 있지 않은지 확인 합니다.

- [Azure Functions 네트워킹 옵션](https://docs.microsoft.com/azure/azure-functions/functions-networking-options)

- [Azure Functions 프리미엄 계획](https://docs.microsoft.com/azure/azure-functions/functions-scale#premium-plan)

- [App Service Environment 소개](https://docs.microsoft.com/azure/app-service/environment/intro)

- [App Service Environment에 대한 네트워킹 고려 사항](https://docs.microsoft.com/azure/app-service/environment/network-info)

- [DDoS 보호를 구성 하는 방법](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

- [Azure 방화벽을 배포 하는 방법](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Azure Security Center 통합 위협 인텔리전스 이해](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

- [적응 네트워크 강화 Azure Security Center 이해](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

- [Azure Security Center Just-in-time 네트워크 Access Control 이해](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

- [Azure Functions에 대 한 전용 끝점 사용](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: 네트워크 패킷 및 흐름 로그 기록

**지침**: Azure Functions 구현에 Nsgs (네트워크 보안 그룹)를 사용 하는 경우 네트워크 보안 그룹 흐름 로그를 사용 하도록 설정 하 고 트래픽 감사를 위해 저장소 계정에 로그를 보냅니다. 또한 Log Analytics 작업 영역에 흐름 로그를 보내고 트래픽 분석를 사용 하 여 Azure 클라우드의 트래픽 흐름에 대 한 정보를 제공할 수 있습니다. 트래픽 분석의 장점 중 일부는 네트워크 활동을 시각화 하 고 핫 스폿을 식별 하 고, 보안 위협을 식별 하 고, 트래픽 흐름 패턴을 이해 하 고, 네트워크 구성을 잘못 파악할 수 있다는 것입니다.

- [NSG 흐름 로그를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [트래픽 분석 사용 및 사용 방법](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

- [Network Watcher를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: 네트워크 기반 침입 감지/침입 방지 시스템 (IDS/IPS)을 배포 합니다.

**지침**: 들어오는 모든 요청을 인증 하 고 악성 트래픽을 필터링 하도록 Azure 웹 응용 프로그램 방화벽과 같은 프런트 엔드 게이트웨이를 구성 합니다. Azure 웹 응용 프로그램 방화벽은 인바운드 웹 트래픽을 검사 하 여 SQL 주입, 사이트 간 스크립팅, 맬웨어 업로드 및 DDoS 공격을 차단 하 여 함수 앱을 보호할 수 있습니다. WAF를 도입 하려면 개인 끝점 (미리 보기)을 App Service Environment 또는 사용 해야 합니다. 프로덕션 워크 로드와 함께 사용 하기 전에 개인 끝점이 더 이상 (미리 보기)에 있지 않은지 확인 합니다.

또는 IDS/IPS 기능을 포함 하는 Azure Marketplace에서 사용할 수 있는 Azure 용 Barracuda WAF와 같은 여러 marketplace 옵션이 있습니다.

- [Azure Functions 네트워킹 옵션](https://docs.microsoft.com/azure/azure-functions/functions-networking-options)

- [Azure Functions 프리미엄 계획](https://docs.microsoft.com/azure/azure-functions/functions-scale#premium-plan)

- [App Service Environment 소개](https://docs.microsoft.com/azure/app-service/environment/intro)

- [App Service Environment에 대한 네트워킹 고려 사항](https://docs.microsoft.com/azure/app-service/environment/network-info)

- [Azure 웹 응용 프로그램 방화벽 이해](https://docs.microsoft.com/azure/application-gateway/overview#web-application-firewall)

- [Azure Functions에 대 한 전용 끝점 사용](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)

- [Barracuda WAF 클라우드 서비스 이해](https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-web-application-firewall#configuring-your-barracuda-waf-cloud-service)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="17-manage-traffic-to-web-applications"></a>1.7: 웹 응용 프로그램에 대 한 트래픽 관리

**지침**: 종단 간 TLS 암호화를 사용 하 여 Azure 웹 응용 프로그램 방화벽과 같은 네트워크에 대 한 프런트 엔드 게이트웨이를 구성 합니다. WAF를 도입 하려면 개인 끝점 (미리 보기)을 App Service Environment 또는 사용 해야 합니다. 프로덕션 워크 로드와 함께 사용 하기 전에 개인 끝점이 더 이상 (미리 보기)에 있지 않은지 확인 합니다.

- [Azure Functions 네트워킹 옵션](https://docs.microsoft.com/azure/azure-functions/functions-networking-options)

- [Azure Functions 프리미엄 계획](https://docs.microsoft.com/azure/azure-functions/functions-scale#premium-plan)

- [App Service Environment 소개](https://docs.microsoft.com/azure/app-service/environment/intro)

- [App Service Environment에 대한 네트워킹 고려 사항](https://docs.microsoft.com/azure/app-service/environment/network-info)

- [Azure 웹 응용 프로그램 방화벽 이해](https://docs.microsoft.com/azure/application-gateway/overview#web-application-firewall)

- [포털에서 Application Gateway를 사용 하 여 종단 간 TLS를 구성 하는 방법](https://docs.microsoft.com/azure/application-gateway/end-to-end-ssl-portal)

- [Azure Functions에 대 한 전용 끝점 사용](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: 네트워크 보안 규칙의 복잡성 및 관리 오버 헤드를 최소화 합니다.

**지침**: Virtual Network 서비스 태그를 사용 하 여 네트워크 보안 그룹 또는 Azure 방화벽에서 네트워크 액세스 제어를 정의 합니다. 보안 규칙을 만들 때 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다. 규칙의 적절 한 원본 또는 대상 필드에서 서비스 태그 이름 (예: AzureAppService)을 지정 하 여 해당 서비스에 대 한 트래픽을 허용 하거나 거부할 수 있습니다. Microsoft는 서비스 태그가 들어 있는 주소 접두사를 관리 하 고 주소가 변경 되 면 서비스 태그를 자동으로 업데이트 합니다.

- [서비스 태그를 사용 하는 방법에 대 한 자세한 내용](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: 네트워크 장치에 대 한 표준 보안 구성 유지

**지침**: Azure Functions와 관련 된 네트워크 설정에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "Microsoft 웹" 및 "Microsoft. 네트워크" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 사용자 지정 정책을 만들어 Azure Functions의 네트워크 구성을 감사 하거나 적용 합니다. 다음과 같이 Azure Functions에 대 한 기본 제공 정책 정의를 사용할 수도 있습니다.
- CORS에서 모든 리소스가 함수 앱에 액세스하도록 허용하지 않아야 합니다.
- 함수 앱에 HTTPS를 통해서만 액세스 가능
- 함수 앱에서 최신 TLS 버전을 사용해야 합니다.

또한 Azure 청사진을 사용 하 여 Azure Resource Manager 템플릿, RBAC (역할 기반 액세스 제어) 및 단일 청사진 정의의 정책과 같은 주요 환경 아티팩트를 패키지화 하 여 대규모 Azure 배포를 간소화할 수 있습니다. 버전 관리를 통해 새 구독, 환경 및 미세 조정 제어 및 관리에 청사진을 쉽게 적용할 수 있습니다.

- [Azure Policy를 구성 하 고 관리 하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure Blueprint를 만드는 방법](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="110-document-traffic-configuration-rules"></a>1.10: 문서 트래픽 구성 규칙

**지침**: Azure Functions 구현에 Nsgs (네트워크 보안 그룹)를 사용 하는 경우 nsgs 및 네트워크 보안 및 트래픽 흐름과 관련 된 기타 리소스에 대 한 태그를 사용 합니다. 개별 NSG 규칙의 경우 "설명" 필드를 사용 하 여 네트워크에서 들어오고 나가는 트래픽을 허용 하는 모든 규칙에 대 한 비즈니스 필요 및/또는 기간 (등)을 지정 합니다.

태그를 사용 하 여 모든 리소스를 만들고 태그가 지정 되지 않은 기존 리소스를 알리도록 하려면 태그 지정과 관련 된 기본 제공 Azure 정책 정의 (예: "태그 및 해당 값 필요")를 사용 합니다.

Azure PowerShell 또는 Azure CLI를 사용 하 여 태그를 기준으로 리소스에 대 한 작업을 조회 하거나 수행할 수 있습니다.

- [태그를 만들고 사용 하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: 자동 도구를 사용 하 여 네트워크 리소스 구성을 모니터링 하 고 변경 내용을 검색 합니다.

**지침**: Azure 활동 로그를 사용 하 여 네트워크 리소스 구성을 모니터링 하 고 Azure Functions 배포와 관련 된 네트워크 설정 및 리소스에 대 한 변경 내용을 검색 합니다. 중요 한 네트워크 설정 또는 리소스에 대 한 변경이 발생 하는 경우 트리거할 Azure Monitor 내에서 경고를 만듭니다. 

- [Azure 활동 로그 이벤트를 확인 하 고 검색 하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [Azure Monitor에서 경고를 만드는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [보안 제어: 로깅 및 모니터링](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)을 참조 하세요.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인 된 시간 동기화 원본을 사용 하십시오.

**지침**: Microsoft는 로그의 타임 스탬프에 대 한 Azure Functions와 같은 Azure 리소스에 사용 되는 시간 원본을 유지 관리 합니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

**지침**: 제어 평면 감사 로깅의 경우 Azure 활동 로그 진단 설정을 사용 하도록 설정 하 고 Log Analytics 작업 영역, azure 이벤트 허브 또는 보관을 위해 azure storage 계정으로 로그를 보냅니다. Azure 활동 로그 데이터를 사용 하 여 Azure 리소스에 대 한 제어 평면 수준에서 수행 되는 모든 쓰기 작업 (PUT, POST, DELETE)에 대 한 "무엇을, 누가, 언제"를 결정할 수 있습니다.

또한 Azure Functions는 기능을 모니터링 하기 위한 Azure 애플리케이션 Insights와의 기본 제공 통합 기능을 제공 합니다. Application Insights는 로그, 성능 및 오류 데이터를 수집 합니다. 성능 변칙을 자동으로 감지 하 고, 문제를 진단 하 고 함수 사용 방법을 이해 하는 데 도움이 되는 강력한 분석 도구를 포함 합니다.

Azure 함수 앱 내에서 기본 제공 사용자 지정 보안/감사 로깅을 사용 하는 경우 진단 설정 "FunctionAppLogs"을 사용 하도록 설정 하 고 로그를 보관을 위해 Log Analytics 작업 영역, Azure 이벤트 허브 또는 Azure storage 계정으로 보냅니다. 

필요에 따라 Azure 센티널 또는 타사 SIEM에 대 한 온-보드 데이터를 사용 하도록 설정할 수 있습니다. 

- [Azure 활동 로그에 대 한 진단 설정을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Azure 애플리케이션 Insights를 사용 하 여 Azure Functions를 설정 하는 방법](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)

- [Azure Functions에 대 한 진단 설정 (사용자 생성 로그)을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/azure-functions/functions-monitor-log-analytics)

- [Azure 센티널을 등록 하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대 한 감사 로깅 사용

**지침**: 제어 평면 감사 로깅의 경우 Azure 활동 로그 진단 설정을 사용 하도록 설정 하 고 Log Analytics 작업 영역, azure 이벤트 허브 또는 보관을 위해 azure storage 계정으로 로그를 보냅니다. Azure 활동 로그 데이터를 사용 하 여 Azure 리소스에 대 한 제어 평면 수준에서 수행 되는 모든 쓰기 작업 (PUT, POST, DELETE)에 대 한 "무엇을, 누가, 언제"를 결정할 수 있습니다.

Azure 함수 앱 내에서 기본 제공 사용자 지정 보안/감사 로깅을 사용 하는 경우 진단 설정 "FunctionAppLogs"을 사용 하도록 설정 하 고 로그를 보관을 위해 Log Analytics 작업 영역, Azure 이벤트 허브 또는 Azure storage 계정으로 보냅니다. 

- [Azure 활동 로그에 대 한 진단 설정을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Azure Functions에 대 한 진단 설정 (사용자 생성 로그)을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/azure-functions/functions-monitor-log-analytics)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

**지침**: 해당 사항 없음 이 지침은 IaaS 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 저장소 보존 구성

**지침**: Azure Monitor에서 조직의 규정 준수 규정에 따라 Azure Functions 앱과 연결 된 Log Analytics 작업 영역에 대 한 로그 보존 기간을 설정 합니다.

- [로그 보존 매개 변수를 설정 하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="26-monitor-and-review-logs"></a>2.6: 로그를 모니터링 하 고 검토 합니다.

**지침**: Azure 활동 로그 진단 설정 및 Azure Functions 앱에 대 한 진단 설정을 사용 하도록 설정 하 고 Log Analytics 작업 영역으로 로그를 보냅니다. Log Analytics 쿼리를 수행 하 여 용어를 검색 하 고, 추세를 식별 하 고, 패턴을 분석 하 고, 수집 된 데이터를 기반으로 다양 한 통찰력을 제공 합니다

Azure Functions 앱에 대 한 Application Insights를 사용 하도록 설정 하 여 로그, 성능 및 오류 데이터를 수집 합니다. Azure Portal 내 Application Insights에서 수집한 원격 분석 데이터를 볼 수 있습니다.

Azure 함수 앱 내에서 기본 제공 사용자 지정 보안/감사 로깅을 사용 하는 경우 진단 설정 "FunctionAppLogs"을 사용 하도록 설정 하 고 로그를 보관을 위해 Log Analytics 작업 영역, Azure 이벤트 허브 또는 Azure storage 계정으로 보냅니다. 

필요에 따라 Azure 센티널 또는 타사 SIEM에 대 한 온-보드 데이터를 사용 하도록 설정할 수 있습니다. 

- [Azure 활동 로그에 대 한 진단 설정을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Azure Functions에 대 한 진단 설정을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/azure-functions/functions-monitor-log-analytics)

- [Azure 애플리케이션 Insights를 사용 하 여 Azure Functions를 설정 하 고 원격 분석 데이터를 보는 방법](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)

- [Azure 센티널을 등록 하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: 비정상적인 활동에 대해 경고를 사용 하도록 설정

**지침**: Azure 활동 로그 진단 설정 및 Azure Functions 앱에 대 한 진단 설정을 사용 하도록 설정 하 고 Log Analytics 작업 영역으로 로그를 보냅니다. Log Analytics 쿼리를 수행 하 여 용어를 검색 하 고, 추세를 식별 하 고, 패턴을 분석 하 고, 수집 된 데이터를 기반으로 다양 한 통찰력을 제공 합니다 Log Analytics 작업 영역 쿼리에 기반 하 여 경고를 만들 수 있습니다.

Azure Functions 앱에 대 한 Application Insights를 사용 하도록 설정 하 여 로그, 성능 및 오류 데이터를 수집 합니다. Application Insights에 의해 수집 된 원격 분석 데이터를 보고 Azure Portal 내에서 경고를 만들 수 있습니다.

필요에 따라 Azure 센티널 또는 타사 SIEM에 대 한 온-보드 데이터를 사용 하도록 설정할 수 있습니다. 

- [Azure 활동 로그에 대 한 진단 설정을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [Azure Functions에 대 한 진단 설정을 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/azure-functions/functions-monitor-log-analytics)

- [Azure Functions에 대해 Application Insights를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#enable-application-insights-integration)

- [Azure 내에서 경고를 만드는 방법](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

- [Azure 센티널을 등록 하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅을 중앙 집중화

**지침**: 해당 사항 없음 Azure Functions 앱은 맬웨어 방지 관련 로그를 처리 하거나 생성 하지 않습니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅을 사용 하도록 설정

**지침**: 해당 사항 없음 Azure Functions 앱은 사용자가 액세스할 수 있는 DNS 관련 로그를 처리 하거나 생성 하지 않습니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅을 사용 합니다.

**지침**: 해당 사항 없음 이 지침은 IaaS 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

## <a name="identity-and-access-control"></a>ID 및 액세스 제어

*자세한 내용은 [보안 제어: id 및 액세스 제어](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)를 참조 하세요.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 관리 계정 인벤토리 유지 관리

**지침**: AD (Azure Active Directory)에는 명시적으로 할당 되어야 하며 쿼리할 수 있는 기본 제공 역할이 있습니다. Azure AD PowerShell 모듈을 사용 하 여 임시 쿼리를 수행 하 여 관리 그룹의 구성원 인 계정을 검색 합니다. 

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할을 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [PowerShell을 사용 하 여 Azure AD에서 디렉터리 역할의 멤버를 가져오는 방법](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 해당 하는 경우 기본 암호를 변경 합니다.

**지침**: Azure Functions에 대 한 제어 평면 액세스는 AZURE ACTIVE DIRECTORY (AD)를 통해 제어 됩니다. Azure AD에는 기본 암호 개념이 없습니다.

데이터 평면 액세스는 권한 부여 키, 네트워크 제한 및 AAD id의 유효성 검사를 비롯 한 여러 가지 방법을 통해 제어할 수 있습니다. 권한 부여 키는 Azure Functions HTTP 끝점에 연결 하는 클라이언트에서 사용 되며 언제 든 지 다시 생성할 수 있습니다. 이러한 키는 기본적으로 새 HTTP 끝점에 대해 생성 됩니다.

함수 앱에 사용할 수 있는 여러 배포 방법이 있으며, 그 중 일부는 생성 된 자격 증명 집합을 활용할 수 있습니다. 응용 프로그램에 사용 되는 배포 방법을 검토 합니다.

- [HTTP 끝점 보안](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#secure-an-http-endpoint-in-production)

- [권한 부여 키를 획득 하 고 다시 생성 하는 방법](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook-trigger?tabs=csharp#obtaining-keys)

- [Azure Functions의 배포 기술](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 전용 관리 계정 사용

**지침**: 전용 관리 계정 사용과 관련 하 여 표준 운영 절차를 만듭니다. Azure Security Center Id 및 액세스 관리를 사용 하 여 관리 계정 수를 모니터링 합니다.

또한 전용 관리 계정을 추적 하는 데 도움이 되도록 다음과 같은 Azure Security Center 또는 기본 제공 Azure 정책의 권장 사항을 사용할 수 있습니다. 구독에 할당 된 소유자가 둘 이상 있어야 합니다. 소유자 권한이 있는 계정을 구독에서 제거 해야 합니다. 소유자 권한이 있는 외부 계정은 구독에서 제거 해야 합니다.

- [Azure Security Center를 사용 하 여 id 및 액세스를 모니터링 하는 방법 (미리 보기)](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

- [Azure Policy 사용 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory에 Single Sign-On (SSO)를 사용 하십시오.

**지침**: 가능한 경우 함수 앱에 대 한 데이터 액세스를 위해 개별 독립 실행형 자격 증명을 구성 하는 대신 Azure Active Directory SSO를 사용 합니다. Azure Security Center Id 및 액세스 관리 권장 사항을 사용 합니다. App Service 인증/권한 부여 기능을 사용 하 여 Azure Functions 앱에 대 한 Single Sign-On를 구현 합니다.

- [Azure Functions의 인증 및 권한 부여 이해](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization#identity-providers)

- [Azure AD를 사용 하 여 SSO 이해](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: 모든 Azure Active Directory 기반 액세스에 대해 multi-factor authentication을 사용 하십시오.

**지침**: AZURE ACTIVE DIRECTORY (AD) MULTI-FACTOR AUTHENTICATION (MFA)를 사용 하도록 설정 하 고 Azure Security Center Id 및 액세스 관리 권장 사항을 따릅니다.

- [Azure에서 MFA를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

- [Azure Security Center 내에서 id 및 액세스를 모니터링 하는 방법](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: 모든 관리 작업에 전용 컴퓨터 (권한 있는 액세스 워크스테이션)를 사용 합니다.

**지침**: MFA (권한 있는 액세스 워크스테이션)를 사용 하 여 Azure 리소스에 로그인 하 고 구성 하도록 구성 된 PAW (권한 Multi-Factor Authentication 있는 액세스 워크스테이션)를 사용 합니다.

- [권한 있는 액세스 워크스테이션에 대 한 자세한 정보](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Azure에서 MFA를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 관리 계정에서 의심 스러운 활동에 대 한 로그 및 경고

**지침**: 환경에서 의심 스러운 작업이 나 안전 하지 않은 활동이 발생 하는 경우 로그 및 경고를 생성 하는 데 AD (Azure Active Directory) PRIVILEGED IDENTITY MANAGEMENT (PIM)를 사용 합니다.

또한 Azure AD 위험 감지를 사용 하 여 위험한 사용자 동작에 대 한 경고 및 보고서를 볼 수 있습니다.

- [Privileged Identity Management 배포 하는 방법 (PIM)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

- [Azure AD 위험 검색 이해](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: 승인 된 위치 에서만 Azure 리소스 관리

**지침**: 조건부 액세스 명명 된 위치를 사용 하 여 IP 주소 범위 또는 국가/지역의 특정 논리적 그룹 에서만 Azure Portal에 대 한 액세스를 허용 합니다.

- [Azure에서 명명 된 위치를 구성 하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory 사용

**지침**: Azure Functions 앱에 대 한 중앙 인증 및 권한 부여 시스템으로 AD (Azure Active Directory)를 사용 합니다. Azure AD는 미사용 데이터 및 전송 중인 데이터에 대해 강력한 암호화를 사용 하 여 데이터를 보호 합니다. 또한 Azure AD는 사용자 자격 증명을 salts, 해시 및 안전 하 게 저장 합니다.

- [Azure AD 로그인을 사용 하도록 Azure Functions 앱을 구성 하는 방법](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)

- [AAD 인스턴스를 만들고 구성 하는 방법](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: 정기적으로 사용자 액세스를 검토 하 고 조정 합니다.

**지침**: AD (Azure Active Directory)는 오래 된 계정을 검색 하는 데 도움이 되는 로그를 제공 합니다. 또한 Azure Id 액세스 검토를 사용 하 여 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 사용자 액세스를 정기적으로 검토 하 여 적절 한 사용자만 계속 액세스할 수 있도록 할 수 있습니다. 

- [Azure AD 보고 이해](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

- [Azure Id 액세스 검토를 사용 하는 방법](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 비활성화 되는 계정에 대 한 액세스 시도를 모니터링 합니다.

**지침**: Azure 함수 앱에 대 한 중앙 인증 및 권한 부여 시스템으로 AD (Azure Active Directory)를 사용 합니다. Azure AD는 미사용 데이터 및 전송 중인 데이터에 대해 강력한 암호화를 사용 하 여 데이터를 보호 합니다. 또한 Azure AD는 사용자 자격 증명을 salts, 해시 및 안전 하 게 저장 합니다.

Azure AD 로그인 활동, 감사 및 위험 이벤트 로그 원본에 액세스할 수 있으며,이를 통해 Azure 센티널 또는 타사 SIEM과 통합할 수 있습니다.

Azure AD 사용자 계정에 대 한 진단 설정을 만들고 감사 로그 및 로그인 로그를 Log Analytics 작업 영역으로 전송 하 여이 프로세스를 간소화할 수 있습니다. Log Analytics 내에서 원하는 로그 경고를 구성할 수 있습니다.

- [Azure AD 로그인을 사용 하도록 Azure Functions 앱을 구성 하는 방법](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)

- [Azure Monitor에 Azure 활동 로그를 통합 하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Azure 센티널을 온보드 하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: 계정 로그인 동작 편차에 대 한 경고

**지침**: Azure Functions 앱에 대 한 중앙 인증 및 권한 부여 시스템으로 AD (Azure Active Directory)를 사용 합니다. 제어 평면 (Azure Portal)의 계정 로그인 동작 편차에 대해 AD (Azure Active Directory) Id 보호 및 위험 검색 기능을 사용 하 여 사용자 id와 관련 된 검색 된 의심 스러운 작업에 대 한 자동화 된 응답을 구성 합니다. 추가 조사를 위해 Azure 센티널로 데이터를 수집할 수도 있습니다.

- [Azure AD 위험한 로그인을 확인 하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Id 보호 위험 정책을 구성 하 고 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

- [Azure 센티널을 등록 하는 방법](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: 지원 시나리오에서 Microsoft에 관련 고객 데이터에 대 한 액세스 권한을 제공 합니다.

**지침**: 현재 사용할 수 없음 고객 Lockbox은 현재 Azure Functions에 대해 지원 되지 않습니다.

- [고객 Lockbox 지원 서비스 목록](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

## <a name="data-protection"></a>데이터 보호

*자세한 내용은 [보안 제어: 데이터 보호](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)를 참조 하세요.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 중요 한 정보 인벤토리 유지 관리

**지침**: 태그를 사용 하 여 중요 한 정보를 저장 하거나 처리 하는 Azure 리소스를 추적 하는 데 도움을 줍니다.

- [태그를 만들고 사용 하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: 중요 한 정보를 저장 하거나 처리 하는 시스템 격리

**지침**: 개발, 테스트 및 프로덕션을 위한 별도의 구독 및/또는 관리 그룹을 구현 합니다. Azure 함수 앱은 VNet (가상 네트워크)/서브넷으로 구분 하 고 적절 하 게 태그를 지정 해야 합니다.

또한 개인 끝점을 사용 하 여 네트워크 격리를 수행할 수 있습니다. Azure 개인 끝점은 Azure 개인 링크로 구동 되는 서비스 (예: Azure Functions app HTTPs 끝점)에 안전 하 게 연결 되는 네트워크 인터페이스입니다. 개인 끝점은 VNet의 개인 IP 주소를 사용 하 여 서비스를 VNet에 효과적으로 제공 합니다. 전용 끝점은 프리미엄 계획에서 실행 되는 함수 앱에 대 한 (미리 보기)입니다. 프로덕션 워크 로드와 함께 사용 하기 전에 개인 끝점이 더 이상 (미리 보기)에 있지 않은지 확인 합니다.

- [추가 Azure 구독을 만드는 방법](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [관리 그룹를 만드는 방법](https://docs.microsoft.com/azure/governance/management-groups/create)

- [태그를 만들고 사용 하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Azure Functions 네트워킹 옵션](https://docs.microsoft.com/azure/azure-functions/functions-networking-options)

- [Azure Functions 프리미엄 계획](https://docs.microsoft.com/azure/azure-functions/functions-scale#premium-plan)

- [개인 끝점 이해](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)

- [Azure Functions에 대 한 전용 끝점 사용](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: 중요 한 정보에 대 한 무단 전송을 모니터링 하 고 차단 합니다.

**지침**: 네트워크 경계에 자동화 된 도구를 배포 하 여 중요 한 정보를 무단으로 전송 하는 것을 모니터링 하 고 정보 보안 전문가에 게 경고 하는 동안 이러한 전송 차단 

Microsoft는 Azure Functions에 대 한 기본 인프라를 관리 하 고, 고객 데이터의 손실 또는 노출을 방지 하기 위해 엄격한 컨트롤을 구현 했습니다.

- [Azure의 고객 데이터 보호 이해](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 해당 없음

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: 전송 중인 모든 중요 한 정보를 암호화 합니다.

**지침**: Azure 함수 앱에 대 한 Azure Portal의 "플랫폼 기능: 네트워킹: SSL"에서 "HTTPs만" 설정을 사용 하도록 설정 하 고 최소 TLS 버전을 1.2으로 설정 합니다.

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: 활성 검색 도구를 사용 하 여 중요 한 데이터 식별

**지침**: 현재 사용할 수 없음 현재 Azure Functions에는 데이터 식별, 분류 및 손실 방지 기능을 사용할 수 없습니다. 중요 한 정보를 처리할 수 있는 함수 앱에 태그를 지정할 수 있으며 규정 준수를 위해 필요한 경우 타사 솔루션을 구현할 수 있습니다.

Microsoft에서 관리 하는 기본 플랫폼의 경우 Microsoft는 모든 고객 콘텐츠를 중요 한 것으로 간주 하 고 고객 데이터 손실 및 노출을 방지 하기 위해 좋은 길이로 이동 합니다. Azure 내에서 고객 데이터를 안전 하 게 유지 하기 위해 Microsoft는 강력한 데이터 보호 제어 및 기능 모음을 구현 하 고 유지 관리 합니다.

- [Azure의 고객 데이터 보호 이해](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC를 사용 하 여 리소스에 대 한 액세스 제어

**지침**: AD (AZURE ACTIVE DIRECTORY) RBAC (역할 기반 액세스 제어)를 사용 하 여 Azure 함수 제어 평면 (Azure Portal)에 대 한 액세스를 제어 합니다. 

- [Azure에서 RBAC를 구성 하는 방법](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: 호스트 기반 데이터 손실 방지를 사용 하 여 액세스 제어를 적용 하십시오.

**지침**: 해당 사항 없음 이 권장 사항은 IaaS 계산 리소스를 위한 것입니다.

Microsoft는 Azure Functions에 대 한 기본 인프라를 관리 하 고, 고객 데이터의 손실 또는 노출을 방지 하기 위해 엄격한 컨트롤을 구현 했습니다.

- [Azure의 고객 데이터 보호 이해](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center 모니터링**: 현재 사용할 수 없음

**책임**: 고객

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: 휴지 상태의 중요 정보 암호화

**지침**: 함수 앱을 만들 때 Blob, 큐 및 테이블 저장소를 지 원하는 범용 Azure Storage 계정을 만들거나 연결 해야 합니다. 이는 함수는 트리거 관리 및 함수 실행 로깅 등의 작업 Azure Storage에 의존 하기 때문입니다. Azure Storage는 미사용 저장소 계정의 모든 데이터를 암호화 합니다. 기본적으로 데이터는 Microsoft 관리 키를 사용 하 여 암호화 됩니다. 암호화 키에 대 한 추가 제어를 위해 blob 및 파일 데이터의 암호화를 위해 고객이 관리 하는 키를 제공할 수 있습니다. 이러한 키는 함수 앱이 저장소 계정에 액세스할 수 있도록 Azure Key Vault에 있어야 합니다.

- [Azure Functions에 대 한 저장소 고려 사항 이해](https://docs.microsoft.com/azure/azure-functions/storage-considerations)

- [휴지 상태의 데이터에 대 한 Azure storage 암호화 이해](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 공유

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: 중요 한 Azure 리소스에 대 한 변경 내용을 기록 하 고 경고 합니다.

**지침**: azure 활동 로그와 함께 Azure Monitor를 사용 하 여 프로덕션 Azure 함수 앱 뿐만 아니라 기타 중요 한 리소스 또는 관련 된 리소스에 대 한 변경 내용이 발생 하는 경우에 대 한 경고를 만듭니다.

- [Azure 활동 로그 이벤트에 대 한 경고를 만드는 방법](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

## <a name="vulnerability-management"></a>취약점 관리

*자세한 내용은 [보안 제어: 취약성 관리](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)를 참조 하세요.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: 자동화 된 취약점 검사 도구 실행

**지침**: DevSecOps를 채택 하 여 Azure Functions 응용 프로그램의 보안을 유지 하 고 수명 주기 동안 최대한 안전 하 게 유지 합니다. DevSecOps는 조직의 보안 팀과 해당 기능을 DevOps 사례에 통합 하 여 팀의 모든 사용자가 보안을 책임 지 게 합니다.

또한 Azure 함수 앱을 보호 하는 데 도움이 되는 Azure Security Center의 권장 사항을 따릅니다.

- [CI/CD 파이프라인에 지속적인 보안 유효성 검사를 추가 하는 방법](https://docs.microsoft.com/azure/devops/migrate/security-validation-cicd-pipeline?view=azure-devops)

- [Azure Security Center 취약성 평가 권장 사항을 구현 하는 방법](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="52-deploy-an-automated-operating-system-patch-management-solution"></a>5.2: 자동화 된 운영 체제 패치 관리 솔루션 배포

**지침**: 해당 사항 없음 이 권장 사항은 IaaS 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: 자동화 된 타사 소프트웨어 패치 관리 솔루션 배포

**지침**: 해당 사항 없음 이 권장 사항은 IaaS 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: 백엔드 취약성 검색 비교

**지침**: 해당 사항 없음 이 권장 사항은 IaaS 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: 위험 등급 프로세스를 사용 하 여 검색 된 취약성에 대 한 수정의 우선 순위를 지정 합니다.

**지침**: Microsoft는 Azure Functions을 지 원하는 기본 시스템에서 취약성 관리를 수행 하지만 Azure Security Center 내에서 권장 사항의 심각도와 보안 점수를 사용 하 여 사용자 환경 내에서 위험을 측정할 수 있습니다. 보안 점수는 완화 된 Security Center 권장 사항의 수를 기준으로 합니다. 권장 사항에 우선 순위를 지정 하려면 각각의 심각도를 고려 합니다.

- [보안 권장 사항 참조 가이드](https://docs.microsoft.com/azure/security-center/recommendations-reference)

**Azure Security Center 모니터링**: 예

**책임**: 공유

## <a name="inventory-and-asset-management"></a>인벤토리 및 자산 관리

*자세한 내용은 [보안 제어: 인벤토리 및 자산 관리](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)를 참조 하세요.*

### <a name="61-use-azure-asset-discovery"></a>6.1: Azure Asset Discovery 사용

**지침**: Azure 리소스 그래프를 사용 하 여 구독 내에서 모든 리소스 (예: 계산, 저장소, 네트워크, 포트 및 프로토콜 등)를 쿼리하고 검색 합니다.  테 넌 트에서 적절 한 (읽기) 권한을 확인 하 고 구독 내의 리소스 뿐만 아니라 모든 Azure 구독을 열거 합니다.

클래식 Azure 리소스는 리소스 그래프를 통해 검색 될 수 있지만 앞으로 Azure Resource Manager 리소스를 만들고 사용 하는 것이 좋습니다.

- [Azure 리소스 그래프를 사용 하 여 쿼리를 만드는 방법](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [Azure 구독을 보는 방법](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Azure RBAC 이해](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="62-maintain-asset-metadata"></a>6.2: 자산 메타 데이터 유지 관리

**지침**: Azure 리소스에 태그를 적용 하 여 논리적으로 분류로 구성 하는 메타 데이터를 제공 합니다.

- [태그를 만들고 사용 하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: 권한이 없는 Azure 리소스를 삭제 합니다.

**지침**: 적절 한 태그 지정, 관리 그룹 및 별도의 구독을 사용 하 여 Azure 리소스를 구성 하 고 추적 합니다. 정기적으로 인벤토리를 조정 하 고 권한이 없는 리소스가 적시에 구독에서 삭제 되도록 합니다.

또한 Azure policy를 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다. 허용 되지 않는 리소스 유형 리소스 유형

- [추가 Azure 구독을 만드는 방법](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [관리 그룹를 만드는 방법](https://docs.microsoft.com/azure/governance/management-groups/create)

- [태그를 만들고 사용 하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: 승인 된 Azure 리소스 및 소프트웨어 타이틀의 인벤토리 유지 관리

**지침**: 계산 리소스에 대해 승인 된 Azure 리소스 및 승인 된 소프트웨어를 정의 합니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: 승인 되지 않은 Azure 리소스에 대 한 모니터링

**지침**: Azure Policy을 사용 하 여 구독에 만들 수 있는 리소스의 유형에 대 한 제한을 설정할 수 있습니다. 

Azure 리소스 그래프를 사용 하 여 구독 내에서 리소스를 쿼리/검색 합니다.  환경에 있는 모든 Azure 리소스가 승인 되었는지 확인 합니다. 

- [Azure Policy를 구성 하 고 관리 하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure Graph를 사용 하 여 쿼리를 만드는 방법](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: 계산 리소스 내에서 승인 되지 않은 소프트웨어 응용 프로그램 모니터링

**지침**: 해당 사항 없음 이 권장 사항은 IaaS 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: 승인 되지 않은 Azure 리소스 및 소프트웨어 응용 프로그램 제거

**지침**: 해당 사항 없음 이 권장 사항은 IaaS 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="68-use-only-approved-applications"></a>6.8: 승인 된 응용 프로그램만 사용 하십시오.

**지침**: 해당 사항 없음 이 권장 사항은 IaaS 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="69-use-only-approved-azure-services"></a>6.9: 승인 된 Azure 서비스만 사용 합니다.

**지침**: Azure Policy을 사용 하 여 다음 기본 제공 정책 정의를 사용 하 여 고객 구독에서 만들 수 있는 리소스 유형에 대 한 제한을 설정할 수 있습니다. 허용 되지 않는 리소스 유형 리소스 유형

- [Azure Policy를 구성 하 고 관리 하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure Policy를 사용 하 여 특정 리소스 종류를 거부 하는 방법](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="610-implement-approved-application-list"></a>6.10: 승인 된 응용 프로그램 목록 구현

**지침**: 해당 사항 없음 이 권장 사항은 IaaS 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: 사용자가 스크립트를 통해 Azure 리소스 관리자와 상호 작용할 수 있도록 제한

**지침**: "Microsoft Azure 관리" 앱에 대 한 "액세스 차단"을 구성 하 여 사용자가 Azure Resource Manager 상호 작용 하는 기능을 제한 하도록 Azure 조건부 액세스를 구성 합니다.

- [Azure Resource Manager에 대 한 액세스를 차단 하도록 조건부 액세스를 구성 하는 방법](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: 사용자가 계산 리소스 내에서 스크립트를 실행 하는 기능을 제한 합니다.

**지침**: 해당 사항 없음 이 권장 사항은 IaaS 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: 위험 수준이 높은 응용 프로그램을 물리적 또는 논리적으로 분리

**지침**: 중요 하거나 높은 위험 수준의 Azure 함수 앱의 경우 격리를 제공 하는 별도의 구독 및/또는 관리 그룹을 구현 합니다.

높은 위험 수준의 Azure 함수 앱을 자체 Virtual Network (VNet)에 배포 합니다. Azure Functions의 경계 보안은 Vnet를 통해 달성 됩니다. App Service Environment (ASE)에서 실행 되는 함수는 Vnet와 통합 될 수 있습니다. 사용 사례에 가장 적합 한 아키텍처를 선택 합니다.

- [Azure Functions 네트워킹 옵션](https://docs.microsoft.com/azure/azure-functions/functions-networking-options)

- [Azure Functions 프리미엄 계획](https://docs.microsoft.com/azure/azure-functions/functions-scale#premium-plan)

- [App Service Environment에 대한 네트워킹 고려 사항](https://docs.microsoft.com/azure/app-service/environment/network-info)

- [외부 ASE를 만드는 방법](https://docs.microsoft.com/azure/app-service/environment/create-external-ase)

내부 ASE를 만드는 방법:

- [https://docs.microsoft.com/azure/app-service/environment/create-ilb-as](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [보안 구성을 사용 하 여 NSG를 만드는 방법](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

## <a name="secure-configuration"></a>보안 구성

*자세한 내용은 [보안 제어: 보안 구성](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)을 참조 하세요.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: 모든 Azure 리소스에 대 한 보안 구성을 설정 합니다.

**지침**: Azure Policy을 사용 하 여 Azure 함수 앱에 대 한 표준 보안 구성을 정의 하 고 구현 합니다. "Microsoft 웹" 네임 스페이스의 Azure Policy 별칭을 사용 하 여 Azure Functions 앱의 구성을 감사 하거나 적용 하는 사용자 지정 정책을 만듭니다. 다음과 같은 기본 제공 정책 정의를 사용할 수도 있습니다.
- 함수 앱에서 관리 ID를 사용해야 합니다.
- 함수 앱에 대해 원격 디버깅을 해제해야 합니다.
- 함수 앱에 HTTPS를 통해서만 액세스 가능

- [사용 가능한 Azure Policy 별칭을 보는 방법](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Azure Policy를 구성 하 고 관리 하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: 보안 운영 체제 구성을 설정 합니다.

**지침**: 해당 사항 없음 이 지침은 IaaS 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: 보안 Azure 리소스 구성 유지

**지침**: azure 정책 [거부] 및 [배포 되지 않은 경우 배포]를 사용 하 여 azure 리소스에서 보안 설정을 적용 합니다.

- [Azure Policy를 구성 하 고 관리 하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure Policy 효과 이해](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: 보안 운영 체제 구성 유지

**지침**: 해당 사항 없음 온-프레미스 함수를 배포할 수 있지만이 지침은 IaaS 계산 리소스를 위한 것입니다. 온-프레미스 함수를 배포할 때 사용자 환경의 보안을 구성할 책임이 있습니다.

- [온-프레미스 함수 이해](https://docs.microsoft.com/azure/azure-functions/functions-runtime-install)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Azure 리소스의 구성을 안전 하 게 저장

**지침**: 소스 제어에서 ARM 템플릿 및 사용자 지정 Azure 정책 정의를 안전 하 게 저장 하 고 관리 합니다.

- [코드로 서의 인프라 란?](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code)

- [코드 워크플로로 정책 디자인](https://docs.microsoft.com/azure/governance/policy/concepts/policy-as-code)

- [Azure DevOps에서 코드를 저장 하는 방법](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure Repos 설명서](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: 사용자 지정 운영 체제 이미지를 안전 하 게 저장

**지침**: 해당 사항 없음 이 지침은 IaaS 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: 시스템 구성 관리 도구 배포

**지침**: 기본 제공 Azure Policy Azure Policy 정의 및 "Microsoft 웹" 네임 스페이스의 별칭을 사용 하 여 시스템 구성을 경고, 감사 및 적용 하기 위한 사용자 지정 정책을 만들 수 있습니다. 또한 정책 예외를 관리 하는 프로세스와 파이프라인을 개발 합니다.

- [Azure Policy를 구성 하 고 관리 하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: 운영 체제용 시스템 구성 관리 도구 배포

**지침**: 해당 사항 없음 이 지침은 IaaS 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Azure 서비스에 대 한 자동화 된 구성 모니터링 구현

**지침**: 기본 제공 Azure Policy Azure Policy 정의 및 "Microsoft 웹" 네임 스페이스의 별칭을 사용 하 여 시스템 구성을 경고, 감사 및 적용 하기 위한 사용자 지정 정책을 만들 수 있습니다. Azure 정책을 사용 하 여 azure 리소스에 대 한 구성을 자동으로 적용 하려면 [감사], [거부] 및 [없는 경우 배포]를 사용 합니다.

- [Azure Policy를 구성 하 고 관리 하는 방법](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: 운영 체제에 대 한 자동화 된 구성 모니터링 구현

**지침**: 해당 사항 없음 이 지침은 IaaS 계산 리소스를 위한 것입니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure 암호를 안전 하 게 관리

**지침**: 관리 되는 id를 Azure Key Vault와 함께 사용 하 여 클라우드 응용 프로그램에 대 한 비밀 관리를 간소화 하 고 보호 합니다. 관리 Id를 사용 하면 함수 앱이 코드에서 자격 증명 없이 Key Vault를 포함 하 여 Azure AD 인증을 지 원하는 모든 서비스에 인증할 수 있습니다.

- [Key Vault를 만드는 방법](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

- [App Service 및 Azure Functions에 대한 관리 ID를 사용하는 방법](https://docs.microsoft.com/azure/app-service/overview-managed-identity)

- [관리 id를 사용 하 여 Key Vault 인증을 제공 하는 방법](https://docs.microsoft.com/azure/key-vault/managed-identity)

- [App Service 및 Azure Functions에 대 한 Key Vault 참조를 사용 합니다.](https://docs.microsoft.com/azure/app-service/app-service-key-vault-references)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: id를 안전 하 게 자동으로 관리

**지침**: 관리 되는 id를 사용 하 여 azure AD에서 자동으로 관리 되는 Id를 azure 함수 앱에 제공 합니다. 관리 Id를 사용 하면 코드에 자격 증명 없이 Key Vault를 포함 하 여 Azure AD 인증을 지 원하는 모든 서비스에 인증할 수 있습니다.

- [App Service 및 Azure Functions에 대한 관리 ID를 사용하는 방법](https://docs.microsoft.com/azure/app-service/overview-managed-identity)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: 의도 하지 않은 자격 증명 노출을 제거 합니다.

**지침**: 자격 증명 스캐너를 구현 하 여 코드 내에서 자격 증명을 식별 합니다. 또한 자격 증명 스캐너는 검색 된 자격 증명을 Azure Key Vault와 같은 보다 안전한 위치로 이동 하는 것을 권장 합니다. 

- [자격 증명 스캐너를 설정 하는 방법](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

## <a name="malware-defense"></a>맬웨어 방어

*자세한 내용은 [보안 제어: 맬웨어 방어](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)를 참조 하세요.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: 중앙에서 관리 하는 맬웨어 방지 소프트웨어 사용

**지침**: 해당 사항 없음 이 지침은 IaaS 계산 리소스를 위한 것입니다.

Microsoft 맬웨어 방지는 Azure 서비스 (예: Azure Functions)를 지 원하는 기본 호스트에서 사용 하도록 설정 되어 있지만 고객 콘텐츠에서 실행 되지 않습니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: Microsoft

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: 계산 되지 않는 Azure 리소스에 업로드할 파일 사전 검사

**지침**: 해당 사항 없음 이 권장 사항은 데이터를 저장 하도록 디자인 된 비 계산 리소스를 위한 것입니다.


**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: 맬웨어 방지 소프트웨어 및 서명을 업데이트 해야 합니다.

**지침**: 해당 사항 없음 이 권장 사항은 데이터를 저장 하도록 디자인 된 비 계산 리소스를 위한 것입니다.

Microsoft 맬웨어 방지는 Azure 서비스 (예: Azure Functions)를 지 원하는 기본 호스트에서 사용 하도록 설정 되어 있지만 고객 콘텐츠에서 실행 되지 않습니다.

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 해당 없음

## <a name="data-recovery"></a>데이터 복구

*자세한 내용은 [보안 제어: 데이터 복구](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)를 참조 하세요.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: 정기 자동 백업 확인

**지침**: 백업 및 복원 기능을 사용 하 여 앱의 정기 백업을 예약 합니다. 프리미엄 계획에서 실행 되는 함수 앱은 "백업 및 복원" 기능을 포함 하는 Azure App Service의 웹 앱과 동일한 호스팅 기능을 제공 합니다.

또한 Azure Repos 및 Azure DevOps와 같은 소스 제어 솔루션을 사용 하 여 코드를 안전 하 게 저장 하 고 관리 합니다. Azure DevOps Services는 여러 Azure storage 기능을 활용 하 여 하드웨어 오류, 서비스 중단 또는 지역 재해 시 데이터 가용성을 보장 합니다. 또한 Azure DevOps 팀은 데이터를 실수로 또는 악의적으로 삭제 하지 않도록 보호 하는 절차를 따릅니다.

- [Azure에서 앱 백업](https://docs.microsoft.com/azure/app-service/manage-backup)

- [Azure DevOps의 데이터 가용성 이해](https://docs.microsoft.com/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

- [Azure DevOps에서 코드를 저장 하는 방법](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure Repos 설명서](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: 전체 시스템 백업을 수행 하 고 고객 관리 키를 백업 합니다.

**지침**: 백업 및 복원 기능을 사용 하 여 앱의 정기 백업을 예약 합니다. 프리미엄 계획에서 실행 되는 함수 앱은 "백업 및 복원" 기능을 포함 하는 Azure App Service의 웹 앱과 동일한 호스팅 기능을 제공 합니다. Azure Key Vault 내에서 고객 관리 키를 백업 합니다.

또한 Azure Repos 및 Azure DevOps와 같은 소스 제어 솔루션을 사용 하 여 코드를 안전 하 게 저장 하 고 관리 합니다. Azure DevOps Services는 여러 Azure storage 기능을 활용 하 여 하드웨어 오류, 서비스 중단 또는 지역 재해 시 데이터 가용성을 보장 합니다. 또한 Azure DevOps 팀은 데이터를 실수로 또는 악의적으로 삭제 하지 않도록 보호 하는 절차를 따릅니다.

- [Azure에서 앱 백업](https://docs.microsoft.com/azure/app-service/manage-backup)

- [Azure에서 주요 자격 증명 모음 키를 백업 하는 방법](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Azure DevOps의 데이터 가용성 이해](https://docs.microsoft.com/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

- [Azure DevOps에서 코드를 저장 하는 방법](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure Repos 설명서](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: 고객 관리 키를 포함 한 모든 백업의 유효성을 검사 합니다.

**지침**: 백업 및 복원 기능에서 정기적으로 복원을 수행할 수 있는지 확인 합니다. 다른 오프 라인 위치를 사용 하 여 코드를 백업 하는 경우 정기적으로 전체 복원을 수행할 수 있는지 확인 합니다. 백업 된 고객 관리 키의 복원을 테스트 합니다.

- [백업에서 Azure의 앱 복원](https://docs.microsoft.com/azure/app-service/web-sites-restore)

- [스냅샷에서 Azure의 앱 복원](https://docs.microsoft.com/azure/app-service/app-service-web-restore-snapshots)

- [Azure에서 주요 자격 증명 모음 키를 복원 하는 방법](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: 백업 및 고객 관리 키의 보호 보장

**지침**: 백업 및 복원 기능의 백업은 구독의 Azure Storage 계정을 사용 합니다. Azure Storage는 미사용 저장소 계정의 모든 데이터를 암호화 합니다. 기본적으로 데이터는 Microsoft 관리 키를 사용 하 여 암호화 됩니다. 암호화 키에 대 한 추가 제어를 위해 고객이 관리 하는 키를 제공 하 여 저장소 데이터를 암호화할 수 있습니다.

고객 관리 키를 사용 하는 경우 실수로 또는 악의적으로 삭제 되지 않도록 키를 보호 하기 위해 Key Vault에서 일시 삭제를 사용 하도록 설정 해야 합니다.

- [Azure Storage 미사용 데이터 암호화](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

- [Key Vault에서 일시 삭제를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure Security Center 모니터링**: 예

**책임**: 공유

## <a name="incident-response"></a>사고 대응

*자세한 내용은 [보안 제어: 인시던트 응답](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)을 참조 하세요.*

### <a name="101-create-an-incident-response-guide"></a>10.1: 인시던트 응답 가이드 만들기

**지침**: 조직에 대 한 인시던트 대응 가이드를 작성 합니다. 담당자의 모든 역할을 정의 하는 사고 대응 계획 및 인시던트를 검색 하 고 인시던트를 해결 하는 방법에 대 한 인시던트 처리/관리 단계를 정의 합니다.

- [Azure Security Center 내에서 워크플로 자동화를 구성 하는 방법](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

- [사용자 고유의 보안 인시던트 대응 프로세스를 구축 하는 방법에 대 한 지침](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 보안 대응 센터의 인시던트 분석](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [또한 고객은 NIST의 컴퓨터 보안 인시던트 처리 가이드를 활용 하 여 고유한 인시던트 대응 계획을 만드는 데 도움이 될 수 있습니다.](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: 인시던트 점수 매기기 및 우선 순위 지정 절차 만들기

**지침**: Security Center는 각 경고에 심각도를 할당 하 여 먼저 조사 해야 하는 경고의 우선 순위를 지정할 수 있도록 합니다. 심각도는 경고를 실행 하는 데 사용 되는 검색 또는 분석에 사용 되는 Security Center의 신뢰도와 경고를 발생 시킨 활동의 악의적인 의도를 가진 신뢰도 수준을 기준으로 합니다.

또한 구독을 명확 하 게 표시 합니다 (예: 프로덕션, 비 prod) 및 Azure 리소스를 명확 하 게 식별 하 고 범주화 하는 명명 시스템을 만듭니다.

**Azure Security Center 모니터링**: 예

**책임**: 공유

### <a name="103-test-security-response-procedures"></a>10.3: 보안 응답 프로시저 테스트

**지침**: 정기적으로 시스템의 인시던트 응답 기능을 테스트 하는 연습을 수행 합니다. 필요에 따라 weak 점수와 격차 및 수정 계획을 식별 합니다.

- [IT 계획 및 기능에 대 한 NIST의 게시: 테스트, 학습 및 연습 프로그램 가이드를 참조 하세요.](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: 보안 인시던트에 대 한 세부 정보를 제공 하 고 보안 인시던트에 대 한 경고 알림을 구성 하십시오.

**지침**: Microsoft 보안 대응 센터 (MSRC)가 불법적인 또는 권한이 없는 당사자가 고객의 데이터에 액세스 한 것을 발견 한 경우 microsoft에서 사용자에 게 연락 하는 데이 정보를 사용 합니다.  문제가 해결 되었는지 확인 하기 위해 팩트 이후에 인시던트를 검토 합니다.

- [Azure Security Center 보안 연락처를 설정 하는 방법](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure Security Center 모니터링**: 예

**책임**: 고객

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: 보안 경고를 인시던트 응답 시스템에 통합 하세요.

**지침**: 연속 내보내기 기능을 사용 하 여 Azure Security Center 경고 및 권장 사항을 내보냅니다. 연속 내보내기를 사용 하면 경고 및 권장 사항을 수동으로 또는 지속적인 지속적인 방식으로 내보낼 수 있습니다. Azure Security Center data connector를 사용 하 여 경고를 Azure 센티널로 스트리밍할 수 있습니다.

- [연속 내보내기를 구성 하는 방법](https://docs.microsoft.com/azure/security-center/continuous-export)

- [Azure 센티널로 경고를 스트리밍하는 방법](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: 보안 경고에 대 한 응답을 자동화 합니다.

**지침**: Azure Security Center의 Workflow Automation 기능을 사용 하 여 Logic Apps으로 보안 경고 및 권장 사항에 대 한 응답을 자동으로 트리거합니다.

- [워크플로 자동화를 구성 하 고 Logic Apps 하는 방법](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 고객

## <a name="penetration-tests-and-red-team-exercises"></a>침투 테스트 및 레드 팀 연습

*자세한 내용은 [보안 제어: 침투 테스트 및 레드 팀 연습](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)을 참조 하세요.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Azure 리소스에 대 한 정기적인 침투 테스트를 수행 하 고 모든 중요 한 보안 결과를 수정 하세요.

**지침**: Microsoft Engagement 규칙에 따라 침투 테스트가 microsoft 정책을 위반 하지 않는지 확인 합니다. Microsoft에서 관리 하는 클라우드 인프라, 서비스 및 응용 프로그램에 대 한 레드 팀 및 라이브 사이트 침투 테스트의 전략과 실행을 사용 합니다.

- [Engagement의 침투 테스트 규칙](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft 클라우드 Red 팀](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center 모니터링**: 해당 사항 없음

**책임**: 공유

## <a name="next-steps"></a>다음 단계

- [Azure 보안 벤치 마크](https://docs.microsoft.com/azure/security/benchmarks/overview) 를 참조 하세요.
- [Azure 보안 기준](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview) 에 대 한 자세한 정보
