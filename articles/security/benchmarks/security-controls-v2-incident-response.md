---
title: Azure 보안 벤치 마크 V2-인시던트 응답
description: Azure Security 벤치 마크 V2 인시던트 응답
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: b9295482c2464eb80bc49fa707744f49a2fbebfd
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102035380"
---
# <a name="security-control-v2-incident-response"></a>보안 제어 V2: 인시던트 응답

인시던트 응답은 인시던트 응답 수명 주기 (준비, 검색 및 분석, 포함 및 인시던트 후 작업)의 제어를 포함 합니다. 여기에는 Azure Security Center 및 센티널과 같은 Azure 서비스를 사용 하 여 인시던트 응답 프로세스를 자동화 하는 작업이 포함 됩니다.

해당 하는 기본 제공 Azure Policy를 보려면 [Azure 보안 벤치 마크 규정 준수 기본 제공 이니셔티브: 인시던트 응답의 세부 정보](../../governance/policy/samples/azure-security-benchmark.md#incident-response) 를 참조 하세요.

## <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: 준비 - Azure에 대한 인시던트 응답 프로세스 업데이트

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP 800-53 4, 000 개 ID |
|--|--|--|--|
| IR-1 | 19 | IR-4, IR-8 |

조직이 보안 인시던트에 대응 하 고, Azure에 대 한 이러한 프로세스를 업데이트 했으며, 준비 상태를 확인 하기 위해 정기적으로 수행 하는 프로세스 인지 확인 합니다.

- [엔터프라이즈 환경에서 보안 구현](/azure/cloud-adoption-framework/security/security-top-10#3-process-assign-accountability-for-cloud-security-decisions)

- [인시던트 응답 참조 가이드](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [보안 운영](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [인시던트 준비](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [위협 인텔리전스](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: 준비 - 인시던트 알림 설정

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP 800-53 4, 000 개 ID |
|--|--|--|--|
| IR-2 | 19.5 | IR-4, IR-5, IR-6, IR-8 |

Azure Security Center에서 보안 인시던트 연락처 정보를 설정 합니다. 이 연락처 정보는 MSRC(Microsoft 보안 대응 센터)가 불법적인 당사자나 권한 없는 당사자가 데이터에 액세스한 것을 발견한 경우 Microsoft가 연락하는 데 사용됩니다. 또한 인시던트 응답 요구 사항에 따라 다양한 Azure 서비스에서 인시던트 경고 및 알림을 사용자 지정하는 옵션도 있습니다. 

- [Azure Security Center 보안 연락처를 설정하는 방법](../../security-center/security-center-provide-security-contact-details.md)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [보안 운영](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [인시던트 준비](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

## <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: 탐지 및 분석 – 고품질 경고를 기반으로 인시던트 만들기

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP 800-53 4, 000 개 ID |
|--|--|--|--|
| IR-3 | 19.6 | IR-4, IR-5 |

높은 품질의 경고를 만들고 경고 품질을 측정 하는 프로세스가 있는지 확인 합니다. 이를 통해 이전 인시던트에 대 한 교훈을 파악 하 고 분석가에 대 한 경고의 우선 순위를 지정할 수 있으므로 가양성을 낭비 하지 않습니다. 

고품질 경고는 과거 인시던트로부터의 경험, 유효성이 검사된 커뮤니티 원본 및 다양한 신호 원본을 융합하고 상관 관계를 설정하여 경고를 생성하고 정리하도록 설계된 도구를 기반으로 하여 작성될 수 있습니다. 

Azure Security Center는 많은 Azure 자산에 대해 고품질 경고를 제공합니다. ASC 데이터 커넥터를 사용하여 경고를 Azure Sentinel로 스트림할 수 있습니다. Azure Sentinel을 사용하면 조사를 위해 인시던트를 자동으로 생성하는 고급 경고 규칙을 만들 수 있습니다. 

Azure 리소스에 대한 위험을 식별하는 데 도움이 되도록 내보내기 기능을 사용하여 Azure Security Center 경고 및 추천 사항을 내보냅니다. 경고 및 추천 사항을 수동 또는 지속적인 방식으로 내보냅니다.

- [내보내기를 구성하는 방법](../../security-center/continuous-export.md)

- [경고를 Azure Sentinel로 스트림하는 방법](../../sentinel/connect-azure-security-center.md)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [보안 운영](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [인시던트 준비](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [위협 인텔리전스](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: 탐지 및 분석 – 인시던트 조사

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP 800-53 4, 000 개 ID |
|--|--|--|--|
| IR-4 | 19 | IR-4 |

분석가가 잠재적 인시던트를 조사 하 여 다양 한 데이터 원본을 쿼리하고 사용할 수 있도록 하 여 발생 한 상황에 대 한 전체 뷰를 작성 합니다. 사각지대를 방지하기 위해 다양한 로그를 수집하여 킬 체인 전체에서 잠재적 공격자의 활동을 추적해야 합니다. 또한 다른 분석가 및 향후 기록 참조를 위해 인사이트 및 습득 지식을 캡처해야 합니다.

조사할 데이터 원본에는 범위 내 서비스 및 실행 중인 시스템에서 이미 수집되고 있는 중앙 집중식 로깅 원본이 포함되지만 다음과 같은 원본도 포함될 수 있습니다.

- 네트워크 데이터 – 네트워크 보안 그룹의 흐름 로그, Azure Network Watcher 및 Azure Monitor를 사용하여 네트워크 흐름 로그 및 기타 분석 정보를 캡처합니다. 

- 실행 중인 시스템의 스냅샷 

    - Azure 가상 머신의 스냅샷 기능을 사용하여 실행 중인 시스템의 디스크에 대한 스냅샷을 만듭니다. 

    - 운영 체제의 기본 메모리 덤프 기능을 사용하여 실행 중인 시스템의 메모리에 대한 스냅샷을 만듭니다.

    - Azure 서비스의 스냅샷 기능 또는 소프트웨어의 자체 기능을 사용하여 실행 중인 시스템의 스냅샷을 만듭니다.

Azure Sentinel은 거의 모든 로그 원본 및 사례 관리 포털에서 광범위한 데이터 분석을 제공하여 인시던트의 전체 수명 주기를 관리합니다. 조사 중에 인텔리전스 정보는 추적 및 보고를 위해 인시던트에 연결할 수 있습니다. 

- [Windows 컴퓨터의 디스크 스냅샷 만들기](../../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Linux 컴퓨터의 디스크 스냅샷 만들기](../../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure 지원 진단 정보 및 메모리 덤프 수집](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Azure Sentinel을 사용하여 인시던트 조사](../../sentinel/tutorial-investigate-cases.md)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [보안 운영](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [인시던트 준비](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [위협 인텔리전스](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: 탐지 및 분석 – 인시던트 우선 순위 지정

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP 800-53 4, 000 개 ID |
|--|--|--|--|
| IR-5 | 19.8 | CA-2, IR-4 |

경고 심각도 및 자산 민감도에 따라 첫 번째 인시던트에 집중할 분석가에 게 컨텍스트를 제공 합니다. 

Azure Security Center는 먼저 조사해야 하는 경고의 우선 순위를 지정하는 데 도움이 되도록 심각도를 각 경고에 할당합니다. 심각도는 Security Center에서 경고를 실행하는 데 사용되는 결과 또는 분석의 신뢰도 및 경고가 발생한 활동에 악의적인 의도가 있었다는 신뢰 수준을 기준으로 합니다.

또한 태그를 사용하여 리소스를 표시하고, Azure 리소스, 특히 중요한 데이터를 처리하는 리소스를 식별하고 분류할 수 있는 명명 시스템을 만듭니다. 인시던트가 발생한 Azure 리소스 및 환경의 중요도에 따라 경고의 수정에 대한 우선 순위를 지정해야 합니다.

- [Azure Security Center의 보안 경고](../../security-center/security-center-alerts-overview.md)

- [태그를 사용하여 Azure 리소스 구성](../../azure-resource-manager/management/tag-resources.md).

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [보안 운영](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [인시던트 준비](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [위협 인텔리전스](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: 차단, 제거, 복구 - 인시던트 처리 자동화

| Azure ID | CIS 컨트롤 v 7.1 ID | NIST SP 800-53 4, 000 개 ID |
|--|--|--|--|
| IR-6 | 19 | IR-4, IR-5, IR-6 |

수동 반복적인 작업을 자동화 하 여 응답 시간을 단축 하 고 분석가의 부담을 줄입니다. 수동 작업은 실행하는 데 더 오래 걸려 각 인시던트의 속도를 늦추고 분석가가 처리할 수 있는 인시던트 수를 줄입니다. 또한 수동 작업은 분석가를 지치게 하여 지연을 유발하는 인간 오류의 위험을 높이고, 복잡한 작업에 효과적으로 집중할 수 있는 분석가의 능력을 저하시킵니다. Azure Security Center 및 Azure Sentinel의 워크플로 자동화 기능을 사용하여 작업을 자동으로 트리거하거나 플레이북을 실행하여 들어오는 보안 경고에 대응합니다. 플레이북은 알림 보내기, 계정 사용 안 함 및 문제가 있는 네트워크 격리와 같은 작업을 수행합니다. 

- [Security Center에서 워크플로 자동화 구성](../../security-center/workflow-automation.md)

- [Azure Security Center에서 자동화된 위협 대응 설정](../../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Azure Sentinel에서 자동화된 위협 응답 설정](../../sentinel/tutorial-respond-threats-playbook.md)

**책임**: Customer

**고객 보안 관련자** ([자세한 정보](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [보안 운영](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [인시던트 준비](/azure/cloud-adoption-framework/organize/cloud-security-incident-preparation)

- [위협 인텔리전스](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)