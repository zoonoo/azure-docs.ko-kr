---
title: Azure Security 벤치 마크 V2-로깅 및 위협 검색
description: Azure Security 벤치 마크 V2 로깅 및 위협 검색
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 9ada1956af2dabdb8e5deff33722af7a8691dd19
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91295467"
---
# <a name="security-control-v2-logging-and-threat-detection"></a>보안 제어 V2: 로깅 및 위협 검색

로깅 및 위협 검색은 Azure에서 위협을 감지 하 고 Azure 서비스에 대 한 감사 로그를 사용, 수집 및 저장 하는 컨트롤을 포함 합니다. 여기에는 Azure 서비스에서 네이티브 위협 검색을 통해 고품질의 경고를 생성 하는 컨트롤을 사용 하 여 검색, 조사 및 재구성 프로세스를 사용할 수 있습니다. 또한 Azure Monitor를 사용 하 여 로그를 수집 하 고, Azure 센티널, 시간 동기화 및 로그 보존으로 보안 분석을 중앙화 합니다. 

## <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Azure 리소스에 대 한 위협 감지 사용

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| LT-1 | 6.7 | AU-3, AU-6, AU-12, SI-4 |

잠재적 위협 및 비정상에 대해 다양 한 유형의 Azure 자산을 모니터링 하 고 있는지 확인 합니다. 분석에 대 한 가양성을 줄이기 위해 고품질 경고를 얻는 데 집중 하세요. 경고는 로그 데이터, 에이전트 또는 기타 데이터에서 원본으로 사용할 수 있습니다.

Azure 서비스 원격 분석 모니터링 및 서비스 로그 분석을 기반으로 하는 Azure Security Center 기본 제공 위협 검색 기능을 사용 합니다. 데이터는 시스템에서 다양 한 보안 관련 구성 및 이벤트 로그를 읽고 분석을 위해 작업 영역에 데이터를 복사 하는 Log Analytics 에이전트를 사용 하 여 수집 됩니다. 

또한 Azure 센티널을 사용 하 여 사용자 환경에서 특정 조건과 일치 하는 위협을 검색 하는 분석 규칙을 작성 합니다. 규칙은 조건이 일치할 때 인시던트를 생성 하므로 각 인시던트를 조사할 수 있습니다. 또한 Azure 센티널은 타사 위협 인텔리전스를 가져와서 위협 검색 기능을 향상 시킬 수 있습니다. 

- [위협 방지 및 Azure Security Center](../../security-center/threat-protection.md)

- [Azure Security Center 보안 경고 참조 가이드](../../security-center/alerts-reference.md)

- [위협 검색을 위한 사용자 지정 분석 규칙 만들기](../../sentinel/tutorial-detect-threats-custom.md)

- [Azure 센티널로 위협 인텔리전스 사이버](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [인프라 및 엔드포인트 보안](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [보안 운영](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [포스처 관리](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [응용 프로그램 보안 및 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [위협 인텔리전스](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Azure id 및 액세스 관리에 대 한 위협 감지 사용

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| LT-2 | 6.8 | AU-3, AU-6, AU-12, SI-4 |

Azure AD는 보다 정교한 모니터링 및 분석 사용 사례를 위해 Azure AD reporting에서 볼 수 있거나 Azure Monitor, Azure 센티널 또는 기타 SIEM/모니터링 도구와 통합 될 수 있는 다음과 같은 사용자 로그를 제공 합니다. 
-   로그인 – 로그인 보고서는 관리되는 애플리케이션 및 사용자 로그인 활동의 사용 정보를 제공합니다.

-   감사 로그 - Azure AD 내의 다양한 기능에 의해 수행된 모든 변경 내용에 대한 로그를 통한 추적 기능을 제공합니다. 감사 로그의 예제로는 사용자, 앱, 그룹, 역할 및 정책 추가 또는 제거와 같은 Azure AD 내의 모든 리소스에 대한 변경 내용이 있습니다.

-   위험한 로그인 - 위험한 로그인은 사용자 계정의 정당한 소유자가 아닌 사용자에 의해 수행된 로그인 시도에 대한 지표입니다.

-   위험 플래그가 지정된 사용자 - 위험한 사용자는 손상되었을 수 있는 사용자 계정에 대한 표시기입니다.

Azure Security Center은 과도 한 인증 시도 횟수와 같은 의심 스러운 특정 활동, 구독에서 사용 되지 않는 계정에 대해 경고할 수도 있습니다. 기본 보안 예방 조치 외에도, Azure Security Center의 위협 방지 모듈은 개별 Azure 계산 리소스 (예: 가상 머신, 컨테이너, app service), 데이터 리소스 (예: SQL DB 및 저장소), Azure 서비스 계층에서 더 심층적인 보안 경고를 수집할 수 있습니다. 이 기능을 사용 하면 개별 리소스 내에서 계정 비정상을 확인할 수 있습니다.

- [Azure AD의 감사 활동 보고서](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Azure Id 보호 사용](../../active-directory/identity-protection/overview-identity-protection.md)

- [위협 방지 및 Azure Security Center](../../security-center/threat-protection.md)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [인프라 및 엔드포인트 보안](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [보안 운영](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [포스처 관리](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [응용 프로그램 보안 및 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [위협 인텔리전스](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Azure 네트워크 작업에 대 한 로깅 사용

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| LT-3 | 9.3, 12.2, 12.5, 12.8 | AU-3, AU-6, AU-12, SI-4 |

보안 분석을 위한 NSG (네트워크 보안 그룹) 리소스 로그, NSG 흐름 로그, Azure 방화벽 로그 및 WAF (웹 응용 프로그램 방화벽) 로그를 사용 하 고 수집 하 여 인시던트 조사, 위협 구하기 및 보안 경고 생성을 지원 합니다. 흐름 로그를 Azure Monitor Log Analytics 작업 영역으로 보낸 다음 트래픽 분석를 사용 하 여 정보를 제공할 수 있습니다. 다른 네트워크 데이터의 상관 관계를 지원 하기 위해 DNS 쿼리 로그를 수집 하 고 있는지 확인 합니다.

- [네트워크 보안 그룹 흐름 로그를 사용 하도록 설정 하는 방법](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Azure Firewall 로그 및 메트릭](../../firewall/logs-and-metrics.md)

- [트래픽 분석 사용 및 사용 방법](../../network-watcher/traffic-analytics.md)

- [Network Watcher 모니터링](../../network-watcher/network-watcher-monitoring-overview.md)

- [Azure Monitor의 Azure 네트워킹 모니터링 솔루션](../../azure-monitor/insights/azure-networking-analytics.md)

- [DNS 분석 솔루션을 사용 하 여 DNS 인프라에 대 한 통찰력 수집](../../azure-monitor/insights/dns-analytics.md)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [인프라 및 엔드포인트 보안](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [보안 운영](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [포스처 관리](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [응용 프로그램 보안 및 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [위협 인텔리전스](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Azure 리소스에 대 한 로깅 사용

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| LT-4 | 6.2, 6.3, 8.8 | AU-3, AU-12 |

규정 준수, 위협 검색, 구하기 및 인시던트 조사를 위한 요구 사항을 충족 하기 위해 Azure 리소스에 대 한 로깅을 사용 하도록 설정 합니다. 

Azure Security Center 및 Azure Policy를 사용 하 여 Azure 리소스에 대 한 리소스 로그 및 로그 데이터 수집을 사용 하도록 설정 하 여 감사, 보안 및 리소스 로그에 액세스할 수 있습니다. 자동으로 사용할 수 있는 활동 로그에는 이벤트 원본, 날짜, 사용자, 타임스탬프, 원본 주소, 대상 주소 및 기타 유용한 요소가 포함됩니다. 

- [Azure의 로깅 및 다른 로그 유형 이해](../../azure-monitor/platform/platform-logs-overview.md)

- [Azure Security Center 데이터 수집 이해](../../security-center/security-center-enable-data-collection.md)

- [맬웨어 방지 모니터링 사용 및 구성](../fundamentals/antimalware.md#enable-and-configure-antimalware-monitoring-using-powershell-cmdlets)

**책임**: 공유됨

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [보안 운영](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

인프라 및 엔드포인트 보안 

- [응용 프로그램 보안 및 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [위협 인텔리전스](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT: 보안 로그 관리 및 분석을 중앙 집중화

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| LT-5 | 6.5, 6.6 | AU-3, SI-4 |

상관 관계를 설정할 수 있도록 로깅 저장소 및 분석을 중앙 집중화 합니다. 각 로그 원본에 대해 데이터 소유자, 액세스 지침, 저장소 위치, 데이터를 처리 하 고 액세스 하는 데 사용 되는 도구 및 데이터 보존 요구 사항을 할당 했는지 확인 합니다.

Azure 활동 로그를 중앙 로깅에 통합 하 고 있는지 확인 합니다. Azure Monitor를 통해 로그를 수집 하 여 끝점 장치, 네트워크 리소스 및 기타 보안 시스템에 의해 생성 된 보안 데이터를 집계 합니다. Azure Monitor에서 Log Analytics 작업 영역을 사용 하 여 분석을 쿼리하고 수행 하 고 장기 및 보관 저장소에 Azure Storage 계정을 사용할 수 있습니다.

또한 Azure 센티널 또는 타사 SIEM에 데이터를 사용 하도록 설정 하 고 등록 합니다.

많은 조직에서 자주 사용 되는 "핫" 데이터에 대해 Azure 센티널을 사용 하 고 덜 자주 사용 되는 "콜드" 데이터를 Azure Storage 하는 것을 선택 합니다. 

- [Azure Monitor를 사용 하 여 플랫폼 로그 및 메트릭을 수집 하는 방법](../../azure-monitor/platform/diagnostic-settings.md)

- [Azure Sentinel을 온보딩하는 방법](../../sentinel/quickstart-onboard.md)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [보안 아키텍처](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [응용 프로그램 보안 및 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [인프라 및 엔드포인트 보안](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="lt-6-configure-log-storage-retention"></a>LT-6: 로그 저장소 보존 구성

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| LT-6 | 6.4 | AU-3, AU-11 |

준수, 규정 및 비즈니스 요구 사항에 따라 로그 보존을 구성 합니다. 

Azure Monitor에서 조직의 규정 준수 규정에 따라 Log Analytics 작업 영역 보존 기간을 설정할 수 있습니다. 장기 및 보관 저장소에 대 한 Azure Storage, Data Lake 또는 Log Analytics 작업 영역 계정을 사용 합니다.

- [Log Analytics에서 데이터 보존 기간 변경](../../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [Azure Storage 계정 로그에 대 한 보존 정책을 구성 하는 방법](../../storage/common/storage-monitor-storage-account.md#configure-logging)

- [Azure Security Center 경고 및 권장 사항 내보내기](../../security-center/continuous-export.md)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [보안 아키텍처](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [응용 프로그램 보안 및 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [보안 운영](/azure/cloud-adoption-framework/organize/cloud-security-operations-center) 

- [파트너 규정 준수 관리](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="lt-7-use-approved-time-synchronization-sources"></a>LT-7: 승인 된 시간 동기화 원본 사용

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP800-53 4, 000 개 ID |
|--|--|--|--|
| LT-7 | 6.1 | AU-8 |

Microsoft는 대부분의 Azure PaaS 및 SaaS 서비스에 대 한 시간 소스를 유지 관리 합니다. 가상 컴퓨터의 경우 특정 요구 사항이 없는 경우 시간 동기화에 Microsoft 기본 NTP 서버를 사용 합니다.  사용자 고유의 NTP (network time protocol) 서버를 만들어야 하는 경우 UDP 서비스 포트 123를 보호 해야 합니다.

Azure 내에서 리소스에 의해 생성 된 모든 로그는 기본적으로 지정 된 표준 시간대를 사용 하 여 타임 스탬프를 제공 합니다.

- [Azure Windows 계산 리소스에 대 한 시간 동기화를 구성 하는 방법](../../virtual-machines/windows/time-sync.md)

- [Azure Linux 계산 리소스에 대 한 시간 동기화를 구성 하는 방법](../../virtual-machines/linux/time-sync.md)

- [Azure 서비스에 대 한 인바운드 UDP를 사용 하지 않도록 설정 하는 방법](https://support.microsoft.com/help/4558520/how-to-disable-inbound-udp-for-azure-services)

**책임**: 공유됨

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [정책 및 표준](/azure/cloud-adoption-framework/organize/cloud-security-policy-standards)

- [응용 프로그램 보안 및 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

- [인프라 및 엔드포인트 보안](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

