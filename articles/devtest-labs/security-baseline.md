---
title: Azure DevTest Labs에 대 한 Azure 보안 기준
description: Azure DevTest Labs에 대 한 Azure 보안 기준
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 4cdfab5df2e97c2bd39c1c104e9552fb940f24d9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098516"
---
# <a name="azure-security-baseline-for-azure-devtest-labs"></a>Azure DevTest Labs에 대 한 Azure 보안 기준

Azure DevTest Labs에 대 한 Azure 보안 기준에는 배포의 보안 상태를 개선 하는 데 도움이 되는 권장 사항이 포함 되어 있습니다.

이 서비스의 기준은 azure [보안 벤치 마크 버전 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)에서 가져온 것으로, azure에서 클라우드 솔루션을 보호할 수 있는 방법에 대 한 권장 사항을 제공 합니다.

자세한 내용은 [Azure 보안 기준 개요](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)를 참조하세요.

## <a name="logging-and-monitoring"></a>로깅 및 모니터링

*자세한 내용은 [보안 그룹: 로깅 및 모니터링](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)을 참조하세요.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 원본 사용
**지침:** Microsoft는 Azure 리소스에 대 한 시간 소스를 유지 관리 합니다. 그러나 계산 리소스에 대 한 시간 동기화 설정을 관리할 수 있습니다.

Azure 계산 리소스에 대 한 시간 동기화를 구성 하는 방법에 대 한 자세한 내용은 [azure에서 Windows vm에 대 한 시간 동기화](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync)문서를 참조 하세요. 

**Azure Security Center 모니터링:** 현재 사용할 수 없음

**책임:** Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성
**지침:** Azure 활동 로그 진단 설정을 사용 하도록 설정 하 고 보관을 위해 Log Analytics 작업 영역, Azure 이벤트 허브 또는 Azure storage 계정으로 로그를 보냅니다. 활동 로그는 관리 평면 수준에서 Azure DevTest Labs 인스턴스에서 수행 된 작업에 대 한 통찰력을 제공 합니다. Azure 활동 로그 데이터를 사용 하 여 DevTest Labs 인스턴스에 대 한 관리 평면 수준에서 수행 되는 모든 쓰기 작업 (PUT, POST, DELETE)에 대해 "무엇을, 누가, 언제"를 결정할 수 있습니다.

자세한 내용은 [진단 설정 만들기를 참조 하 여 플랫폼 로그 및 메트릭을 다른 대상으로 보냅니다](../azure-monitor/platform/diagnostic-settings.md).

**Azure Security Center 모니터링:** 현재 사용할 수 없음

**책임:** Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용
**지침:** Azure 활동 로그 진단 설정을 사용 하도록 설정 하 고 보관을 위해 Log Analytics 작업 영역, Azure 이벤트 허브 또는 Azure storage 계정으로 로그를 보냅니다. 활동 로그는 관리 평면 수준에서 Azure DevTest Labs 인스턴스에서 수행 된 작업에 대 한 통찰력을 제공 합니다. Azure 활동 로그 데이터를 사용 하 여 DevTest Labs 인스턴스에 대 한 관리 평면 수준에서 수행 되는 모든 쓰기 작업 (PUT, POST, DELETE)에 대해 "무엇을, 누가, 언제"를 결정할 수 있습니다.

자세한 내용은 [진단 설정 만들기를 참조 하 여 플랫폼 로그 및 메트릭을 다른 대상으로 보냅니다](../azure-monitor/platform/diagnostic-settings.md).

**Azure Security Center 모니터링:** 현재 사용할 수 없음

**책임:** Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집
**지침:** Azure DevTest Labs 가상 컴퓨터를 만들고 고객이 소유 합니다. 따라서이를 모니터링 하는 것은 조직의 책임입니다. Azure Security Center를 사용 하 여 계산 OS를 모니터링할 수 있습니다. 운영 체제에서 Security Center에 의해 수집 되는 데이터에는 OS 유형 및 버전, OS (Windows 이벤트 로그), 실행 중인 프로세스, 컴퓨터 이름, IP 주소 및 로그인 한 사용자가 포함 됩니다. 또한 Log Analytics 에이전트는 크래시 덤프 파일을 수집 합니다.

자세한 내용은 다음 문서를 참조하세요. 

- [Azure Monitor를 사용 하 여 Azure 가상 머신 내부 호스트 로그를 수집 하는 방법](../azure-monitor/learn/quick-collect-azurevm.md)
- [Azure Security Center 데이터 수집 이해](../security-center/security-center-enable-data-collection.md)

**Azure Security Center 모니터링:** 예로

**책임:** Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 기간 구성
***지침:** Azure Monitor에서 조직의 규정 준수 규정에 따라 Azure DevTest Labs 인스턴스와 연결 된 Log Analytics 작업 영역에 대 한 로그 보존 기간을 설정 합니다.

자세한 내용은 [로그 보존 매개 변수를 설정 하는 방법](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period) 문서를 참조 하세요.

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

### <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토
**지침:** Azure 활동 로그 진단 설정을 사용 하도록 설정 하 고 로그를 Log Analytics 작업 영역으로 보냅니다. Log Analytics에서 쿼리를 실행 하 여 용어를 검색 하 고, 추세를 식별 하 고, 패턴을 분석 하 고, Azure DevTest Labs에 대해 수집 되었을 수 있는 활동 로그 데이터를 기반으로 다양 한 통찰력을 제공 합니다.

자세한 내용은 다음 문서를 참조하세요.

- [진단 설정을 Azure 활동 로그에 사용하도록 설정하는 방법](../azure-monitor/platform/diagnostic-settings.md)
- [Azure Monitor의 Log Analytics 작업 영역에서 Azure 활동 로그를 수집 하 고 분석 하는 방법](../azure-monitor/platform/activity-log.md)

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: 비정상 활동에 대한 경고 사용
**지침:** Azure Log Analytics 작업 영역을 사용 하 여 Azure DevTest Labs와 관련 된 보안 로그 및 이벤트의 비정상적인 활동을 모니터링 하 고 경고 합니다.

자세한 내용은 [log analytics 로그 데이터를 경고 하는 방법](../azure-monitor/learn/tutorial-response.md) 문서를 참조 하세요.

**Azure Security Center 모니터링:** 현재 사용할 수 없음

**책임:** Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화
**지침:** 해당 없음. Azure DevTest Labs는 맬웨어 방지 관련 로그를 처리 하거나 생성 하지 않습니다.

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

### <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용
**지침:** 해당 없음. Azure DevTest Labs는 DNS 관련 로그를 처리 하거나 생성 하지 않습니다.

**Azure Security Center 모니터링:** 해당 없음

**책임:** Customer

### <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅 사용
**지침:** Azure DevTest Labs는 고객이 소유 하 고 관리 하는 Azure Compute 컴퓨터를 만듭니다. 지원 되는 모든 Azure Windows 가상 머신에서 Microsoft Monitoring Agent를 사용 하 여 프로세스 생성 이벤트와 CommandLine 필드를 로깅합니다. 지원 되는 Azure Linux 가상 머신의 경우 노드 단위로 콘솔 로깅을 수동으로 구성 하 고 Syslog를 사용 하 여 데이터를 저장할 수 있습니다. 또한 Azure Monitor의 Log Analytics 작업 영역을 사용 하 여 Azure Virtual machines에서 로그를 검토 하 고 기록 되는 데이터에 대해 쿼리를 실행 합니다.

- [Azure Security Center에서 데이터 수집](../security-center/security-center-enable-data-collection.md#data-collection-tier)
- [Azure Monitor에서 사용자 지정 쿼리를 실행 하는 방법](../azure-monitor/log-query/get-started-queries.md)
- [Azure Monitor의 Syslog 데이터 원본](../azure-monitor/platform/data-sources-syslog.md)

**Azure Security Center 모니터링:** 예로

**책임:** Customer

## <a name="next-steps"></a>다음 단계
다음 문서를 참조 하세요.

- [환경에 대 한 보안 경고 Azure DevTest Labs](environment-security-alerts.md)