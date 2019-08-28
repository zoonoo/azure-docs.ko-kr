---
title: Azure 오스트레일리아의 보안을 위한 시스템 모니터링
description: 오스트레일리아 정부 정책, 규정 및 법규의 특정 요구 사항을 충족 하기 위해 오스트레일리아 지역 내에서 시스템 모니터링을 구성 하는 방법에 대 한 지침입니다.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: f7f78dbde9810c8786e2344555444efabcc989b0
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779318"
---
# <a name="system-monitoring-for-security-in-azure-australia"></a>Azure 오스트레일리아의 보안을 위한 시스템 모니터링

실시간 모니터링 및 일상적인 보안 평가를 포함 하는 강력한 보안 전략을 보유 하는 것은 클라우드를 포함 하 여 IT 환경의 일상 업무 보안을 개선 하는 데 매우 중요 합니다.

클라우드 보안은 고객과 클라우드 공급자 간의 공동 작업입니다. [오스트레일리아 사이버 Security center의 ACSC (Information Security Manual control) ISM (Information Security Manual Controls](https://acsc.gov.au/infosec/ism/index.htm) )에 포함 된 권장 사항을 고려 하 여 이러한 요구 사항을 용이 하 게 하 Microsoft Azure는 데 사용할 수 있는 네 가지 서비스가 있습니다. 특히 centralised 이벤트 로깅, 이벤트 로그 감사 및 보안 취약성 평가 및 관리의 구현입니다. Microsoft Azure 서비스는 다음과 같습니다.

* Azure Security Center
* Azure Monitor
* Azure Advisor
* Azure Policy

ACSC는 **보호** 된 데이터에 대해 이러한 서비스를 사용 하는 것을 권장 합니다. 이러한 서비스를 사용 하 여 사전에 IT 환경을 모니터링 하 고 분석 하 고, 보안을 향상 시키기 위해 리소스를 가장 잘 할당할 수 있는 위치에 대 한 의사 결정을 내릴 수 있습니다. 이러한 각 서비스는 결합 된 솔루션의 일부로, 가능한 최상의 통찰력, 권장 사항 및 보호 기능을 제공 합니다.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) 는 Azure 리소스 및 호스트 된 데이터의 보안을 모니터링 하 고 향상 시키는 데 사용할 수 있는 통합 보안 관리 콘솔을 제공 합니다. Azure Security Center에는 Azure Advisor에서 모범 사례 구성의 상태 분석 및 전반적인 Azure Policy 준수를 기반으로 하는 보안 점수가 포함 됩니다.

Azure Security Center는 Azure 고객에 게 다음과 같은 기능을 제공 합니다.

* 보안 정책, 평가 및 권장 사항
* 보안 이벤트 컬렉션 및 검색
* 액세스 및 응용 프로그램 제어
* 고급 위협 검색
* Just-in-time Virtual Machines access control
* 하이브리드 보안

하이브리드 클라우드 환경에서 지원 되는 온-프레미스 리소스를 포함 하도록 Azure Security Center 모니터링 하는 리소스 범위를 확장할 수 있습니다. 여기에는 지원 되는 버전의 System Center Operations Manager에서 현재 모니터링 하 고 있는 온-프레미스 리소스가 포함 됩니다.

Security Center "Standard" 계층에서는 [ASD 필수 8](https://acsc.gov.au/publications/protect/essential-eight-explained.htm)에서 요구 하는 클라우드 기반 보안 제어도 제공 합니다. 여기에는 just-in-time 액세스를 통한 관리 권한 제한과 응용 프로그램 허용 목록 포함 됩니다.

### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) 은 모든 Azure 리소스에 대 한 centralised 로깅 솔루션 이며 Log Analytics 및 Application Insights를 포함 합니다. Azure 리소스에서 수집 되는 두 가지 주요 데이터 형식은 로그 및 메트릭입니다. Azure Monitor 수집 되 면 다양 한 도구와 다양 한 용도로 로깅 정보를 사용할 수 있습니다.

![Azure Monitor 개요](media/overview.png)

또한 Azure Monitor에는 "Azure 활동 로그"가 포함 됩니다. SActivity 로그는 Azure 내에서 발생 한 모든 구독 수준 이벤트를 저장 합니다. 이를 통해 Azure 고객은 Azure 리소스에서 수행 되는 작업의 "누가, 언제, 언제"를 볼 수 있습니다. Azure Monitor 및 Azure 활동 로그 이벤트로 전송 되는 리소스 기반 로깅은 빌드된 Kusto 쿼리 언어를 사용 하 여 일부 이벤트가 분석 수 있습니다. 그러면 사용자 지정 대시보드 및 보기를 만드는 데 사용 되 고 경고 및 알림을 트리거하기 위해 구성 된 로그를 내보낼 수 있습니다.

### <a name="azure-advisor"></a>Azure Advisor

Azure 구독 내에서 지원 되는 Azure 리소스, 시스템 생성 로그 파일 및 현재 리소스 구성을 분석 [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) 분석 합니다. Azure Advisor에서 제공 하는 분석은 Microsoft의 권장 모범 사례에 따라 실시간으로 생성 됩니다. 사용자 환경에 추가 된 모든 지원 되는 Azure 리소스가 일부 이벤트가 분석 되며 적절 한 권장 사항이 제공 됩니다. Azure Advisor 권장 사항은 다음 네 가지 모범 사례 범주로 categorised 됩니다.

* 보안
* 고가용성
* 성능
* 비용

Azure Advisor에서 생성 된 보안 권장 사항은 Azure Security Center에서 제공 하는 전반적인 보안 분석의 일부입니다.

Azure Advisor에서 수집한 정보는 관리자에 게 다음을 제공 합니다.

* 권장 모범 사례를 충족 하지 않는 리소스 구성에 대 한 통찰력
* 특정 수정 작업에 대 한 지침
* 높은 우선 순위로 수행 되어야 하는 수정 작업을 나타내는 순위

### <a name="azure-policy"></a>Azure Policy

[Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview) 은 Azure 리소스의 형식 및 허용 되는 구성을 제어 하는 규칙을 적용 하는 기능을 제공 합니다. 정책을 사용 하 여 리소스 만들기 및 구성을 제어 하거나, 환경 전체에서 구성 설정을 감사 하는 데 사용할 수 있습니다. 이러한 감사 결과는 수정 작업의 기반을 형성 하는 데 사용할 수 있습니다. Azure Policy는 RBAC (역할 기반 액세스 제어)와 다릅니다. 리소스와 해당 구성을 제한 하는 데 사용 되는 Azure Policy RBAC는 Azure 사용자에 대 한 권한 있는 액세스를 제한 하는 데 사용 됩니다.

특정 정책이 적용 되 고 있는지 또는 정책의 영향을 감사 하 고 있는지 여부에 대 한 정책 준수를 지속적으로 모니터링 하 고 전체 및 리소스 관련 규정 준수 정보를 관리자에 게 제공 합니다. Azure Policy 준수 데이터가 Azure Security Center에 제공 되어 보안 점수의 일부를 형성 합니다.

## <a name="key-design-considerations"></a>주요 디자인 고려 사항

이벤트 로그 전략을 구현할 때 ACSC ISM은 다음과 같은 고려 사항을 강조 표시 합니다.

* Centralised 로깅 기능
* 로깅할 특정 이벤트
* 이벤트 로그 보호
* 이벤트 로그 보존
* 이벤트 로그 감사

또한 로그를 수집 하 고 관리 하기 위해 ISM은 조직의 IT 환경에 대 한 일상적인 취약성 평가를 권장 합니다.

### <a name="centralised-logging"></a>Centralised 로깅

모든 로깅 솔루션은 가능한 경우 캡처된 로그를 단일 데이터 리포지토리로 통합 해야 합니다. 이로 인해 운영 복잡성을 줄이고 여러 데이터 사일로를 만들 수 있을 뿐만 아니라 여러 원본에서 수집 된 데이터를 일부 이벤트가 분석 하 여 상관 관계 이벤트를 식별할 수 있습니다. 이는 사이버 보안 인시던트의 범위를 검색 하 고 관리 하는 데 중요 합니다.

Azure Monitor를 사용 하는 모든 Azure 고객에 게이 요구 사항이 충족 됩니다. 이 제품은 azure에서 모든 Azure 리소스에 대 한 centralised 로깅 리포지토리를 제공할 뿐 아니라 Azure 이벤트 허브로 데이터를 스트리밍할 수도 있습니다. Azure Event Hubs는 완전히 관리 되는 실시간 데이터 수집 서비스를 제공 합니다. Azure Monitor 데이터가 Azure 이벤트 허브로 스트리밍되 면 데이터는 기존의 지원 되는 SIEM (보안 정보 및 이벤트 관리) 리포지토리와 추가 타사 모니터링 도구에 쉽게 연결 될 수 있습니다.

Microsoft는 azure 기본 SIEM 솔루션인 Azure 센티널도 제공 합니다. Azure 센티널은 다양 한 데이터 커넥터를 지원 하며 전체 기업에서 보안 이벤트를 모니터링 하는 데 사용할 수 있습니다. 지원 되는 [데이터 커넥터](https://docs.microsoft.com/azure/sentinel/connect-data-sources), Azure 센티널의 기본 제공 기계 학습 및 Kusto 쿼리 언어의 데이터를 결합 하 여 보안 관리자에 게는 경고 검색, 위협 가시성, 자동 관리 사냥을 위한 단일 솔루션이 제공 됩니다. 및 위협 응답. 또한 센티널에서는 보안 관리자가 조직 내에서 공유할 수 있는 reuseable 플레이 북의 보안 조사 과정에서 수행 되는 모든 단계를 기록할 수 있도록 하는 구하기 및 노트북 기능을 제공 합니다. 보안 관리자는 기본 제공 [사용자 분석](https://docs.microsoft.com/azure/sentinel/user-analytics) 을 사용 하 여 지정한 단일 사용자의 작업을 조사할 수도 있습니다.

### <a name="logged-events-and-log-detail"></a>기록 이벤트 및 로그 정보

ISM은 로깅 전략에 포함 되어야 하는 이벤트 로그 유형의 세부 목록을 제공 합니다. 모든 캡처된 로그는 분석 및 조사를 수행 하는 데 실용적으로 사용할 수 있는 충분 한 세부 정보를 포함 해야 합니다.

Azure에서 수집 된 로그는 다음 세 가지 범주 중 하나에 속합니다.

* **제어 및 관리 로그**: 이러한 로그는 Azure Resource Manager 만들기, 업데이트 및 삭제 작업에 대 한 정보를 제공 합니다.

* **데이터 평면 로그**: 여기에는 Azure 리소스 사용의 일부로 발생 하는 이벤트가 포함 됩니다. 시스템, 보안 및 응용 프로그램 로그를 포함 하는 Windows 이벤트 로그와 같은 소스를 포함 합니다.

* **처리 된 이벤트**: 이러한 이벤트에는 Azure를 통해 고객을 대신 하 여 자동으로 처리 된 이벤트 및 경고에 대 한 정보가 포함 됩니다. 처리 된 이벤트의 예는 Azure Security Center 경고입니다.

Azure 가상 머신 모니터링은 Windows 및 Linux 모두에 대 한 가상 머신 에이전트 배포를 통해 향상 되었습니다. 이 현저히 수집 된 로깅 정보의 범위를 늘립니다. Azure Security Center를 통해 자동으로 수행 되도록이 에이전트의 배포를 구성할 수 있습니다.

Microsoft에서는 Azure 리소스 관련 로그 및 해당 [스키마](https://docs.microsoft.com/azure/security/fundamentals/log-audit)에 대 한 자세한 정보를 제공 합니다.

### <a name="log-retention-and-protection"></a>로그 보존 및 보호

 필요한 보존 기간 동안 이벤트 로그를 안전 하 게 저장 해야 합니다. ISM은 로그가 최소 7 년 동안 보존 됨을 알려줍니다. Azure는 수집 된 로그의 긴 수명을 보장 하는 다양 한 방법을 제공 합니다. 기본적으로 Azure 로그 이벤트는 90 일 동안 저장 됩니다. Azure Monitor에서 캡처한 로그 데이터는 장기 보존을 위해 필요에 따라 Azure Storage 계정으로 이동 하 고 저장할 수 있습니다. Azure Storage 계정에 저장 된 활동 로그는 설정 된 기간 (일) 동안 보존 될 수도 있고 필요한 경우 무기한으로 유지할 수도 있습니다.

Azure 로그 이벤트를 저장 하는 데 사용 되는 Azure Storage 계정은 지리적으로 중복 될 수 있으며 Azure Backup를 사용 하 여 백업할 수 있습니다. Azure Backup로 캡처한 후에는 로그를 포함 하는 백업을 삭제 하면 관리 승인이 필요 하 고 삭제 된 것으로 표시 된 백업도 복구를 허용 하는 14 일 동안 유지 됩니다. Azure Backup는 보호 된 인스턴스의 9999 복사본을 허용 하 여 27 년간 매일 백업을 제공 합니다.

역할 기반 Access Control는 Azure 로깅에 사용 되는 리소스에 대 한 액세스를 제어 하는 데 사용 해야 합니다. 로그 내에 포함 된 데이터의 보안을 보장 하기 위해 역할 기반 액세스 제어를 사용 하 여 Azure Monitor, Azure Storage 계정 및 Azure 백업을 구성 해야 합니다.

### <a name="log-auditing"></a>로그 감사

로그의 true 값은 일부 이벤트가 분석 경우 realised 됩니다. 자동화 된 분석과 수동 분석을 모두 사용 하 고 사용 가능한 도구에 대해 잘 알고 있으면 조직 보안 정책의 위반을 검색 하 고 관리 하 고 보안 인시던트를 사이버 하는 데 도움이 됩니다. Azure Monitor은 수집 된 로그를 분석 하는 다양 한 도구 집합을 제공 합니다. 그런 다음이 분석의 결과를 여러 형식으로 시스템, visualised 또는 disseminated 간에 공유할 수 있습니다.

Azure Monitor에 저장 된 로그 데이터는 Log Analytics 작업 영역에 유지 됩니다. 모든 분석은 쿼리로 시작 합니다. Azure Monitor 쿼리는 Kusto 쿼리 언어로 작성 됩니다. 쿼리는 Azure 대시보드에서 경고 규칙에 이르기까지 Azure Monitor의 모든 출력에 대 한 기초를 형성 합니다.

![Azure 로그 쿼리 개요](media/queries-overview.png)

모니터링 솔루션을 사용 하 여 로그 감사를 향상 시킬 수 있습니다. 이러한 솔루션은 컬렉션 논리, 쿼리 및 데이터 모델링과 뷰를 포함 하는 미리 패키지 된 솔루션입니다. Microsoft는 다양 한 모니터링 솔루션을 [제공](https://docs.microsoft.com/azure/azure-monitor/insights/solutions-inventory) 하 고 제품 공급 업체의 추가 솔루션은 Azure Marketplace에서 찾을 수 있습니다.

### <a name="vulnerability-assessment-and-management"></a>취약성 평가 및 관리

ISM은 일상적인 취약성 평가 및 관리가 필수적입니다. IT 환경은 지속적으로 진화 하 고 있으며 외부 보안 위협은 끊임 없이 변화 하 고 있습니다. Azure Security Center를 통해 자동화 된 취약점 평가를 수행 하 고 수정 작업을 계획 하 고 수행 하는 방법에 대 한 지침을 얻을 수 있습니다.

Azure Security Center 보안 점수는 적용 될 때 환경의 보안을 개선 하는 권장 사항 목록을 제공 합니다. 이 목록은 전반적인 보안 점수에 미치는 영향을 기준으로 내림차순으로 정렬 됩니다. 영향을 기준으로 목록을 정렬 하면 보안을 개선 하는 데 가장 많은 가치를 제공 하는 우선 순위가 가장 높은 권장 사항에 집중할 수 있습니다.

또한 Azure Policy는 지속적인 취약성 평가에서 주요 부분을 재생 합니다. Azure Policy에서 사용할 수 있는 정책 유형은 리소스를 만들 수 있는 Azure 지역을 제한 하기 위해 리소스 태그 및 값을 적용 하는 범위에 있습니다. Azure 정책 집합을 이니셔티브로 그룹화 할 수 있습니다. 이니셔티브는 그룹으로 함께 적용 될 때 특정 보안 또는 규정 준수 목표의 기반을 형성 하는 관련 Azure 정책을 적용 하는 데 사용 됩니다.

Azure Policy에는 지속적으로 증가 하는 기본 제공 정책 정의 라이브러리가 있습니다. Azure Portal 사용자 지정 Azure Policy 정의를 작성 하는 옵션도 제공 됩니다. 기존 라이브러리에서 정책을 찾거나 새로 만든 후에는 정책을 Azure 리소스에 할당할 수 있습니다. 이러한 할당은 리소스 관리 계층의 다양 한 수준에서 [범위](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage) 를 지정할 수 있습니다. 정책 할당은 상속 됩니다. 즉, 범위 내의 모든 자식 리소스는 동일한 정책 할당을 받습니다. 필요에 따라 범위 지정 정책 할당에서 리소스를 제외할 수도 있습니다.

배포 된 모든 Azure 정책은 조직의 보안 점수에 영향을 주지 않습니다. 매우 맞춤식 환경에서는 특정 작업에 맞게 조정 된 감사 정보를 제공 하기 위해 사용자 지정 Azure Policy 정의를 만들고 배포할 수 있습니다.

## <a name="getting-started"></a>시작

Azure Security Center 시작 하 고 Azure Monitor, Advisor 및 정책을 완전히 활용 하려면 다음과 같은 초기 단계를 권장 합니다.

* Azure Security Center 활성화
* 표준 계층으로 업그레이드
* 지원 되는 Azure Virtual Machines에 Microsoft Monitoring Agent 자동 프로 비전 사용
* 보안 센터 대시보드에서 보안 권장 사항 및 경고를 검토 하 고, prioritise 하 고, 완화 합니다.

## <a name="next-steps"></a>다음 단계

정책 및 규정 준수를 보장 하기 위해 관리를 구현 하 고 Azure 오스트레일리아 리소스를 제어 하는 방법에 대 한 자세한 내용은 [Azure Policy 및 Azure 청사진](azure-policy.md) 을 참조 하세요.
