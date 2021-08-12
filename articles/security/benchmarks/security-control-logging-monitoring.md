---
title: Azure 보안 제어 - 로깅 및 모니터링
description: Azure 보안 제어 로깅 및 모니터링
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: ccfbb585ccf50366721925b0b31b17088fd36371
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102612425"
---
# <a name="security-control-logging-and-monitoring"></a>보안 제어: 로깅 및 모니터링

보안 로깅 및 모니터링은 Azure 서비스에 대한 감사 로그 사용, 확보 및 저장과 관련된 작업을 중점적으로 다룹니다.

## <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 원본 사용

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

Azure 리소스에 대한 시간 원본은 Microsoft에서 유지 관리되지만 컴퓨팅 리소스에 대한 시간 동기화 설정을 관리할 수 있는 옵션이 있습니다.

- [Azure Windows 컴퓨팅 리소스에 대한 시간 동기화를 구성하는 방법](../../virtual-machines/windows/time-sync.md)

- [Azure Linux 컴퓨팅 리소스에 대한 시간 동기화를 구성하는 방법](../../virtual-machines/linux/time-sync.md)

## <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 2.2 | 6.5, 6.6 | Customer |

Azure Monitor를 통해 로그를 수집하여 엔드포인트 장치, 네트워크 리소스 및 기타 보안 시스템에 의해 생성된 보안 데이터를 집계합니다. Azure Monitor 내에서 Log Analytics 작업 영역을 사용하여 분석을 쿼리 및 수행하고, Azure Storage 계정을 장기/보관 스토리지에 사용할 수 있습니다.

또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다. 

- [Azure Sentinel을 온보딩하는 방법](../../sentinel/quickstart-onboard.md)

- [Azure Monitor를 사용하여 플랫폼 로그 및 메트릭을 수집하는 방법](../../azure-monitor/essentials/diagnostic-settings.md)

- [Azure Monitor를 사용하여 Azure 가상 머신 내부 호스트 로그를 수집하는 방법](../../azure-monitor/vm/quick-collect-azurevm.md)

- [Azure Monitor 및 타사 SIEM 통합을 시작하는 방법](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 2.3 | 6.2, 6.3 | Customer |

감사, 보안 및 진단 로그에 액세스하기 위해 Azure 리소스에 대한 진단 설정을 사용하도록 설정합니다. 자동으로 사용할 수 있는 활동 로그에는 이벤트 원본, 날짜, 사용자, 타임스탬프, 원본 주소, 대상 주소 및 기타 유용한 요소가 포함됩니다.

- [Azure Monitor를 사용하여 플랫폼 로그 및 메트릭을 수집하는 방법](../../azure-monitor/essentials/diagnostic-settings.md)

- [Azure의 로깅 및 기타 로그 형식 이해](../../azure-monitor/essentials/platform-logs-overview.md)

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 2.4 | 6.2, 6.3 | Customer |

컴퓨팅 리소스가 Microsoft 소유인 경우 그 모니터링은 Microsoft의 책임입니다. 컴퓨팅 리소스가 사용자 조직의 소유인 경우 그 모니터링은 사용자의 책임입니다. Azure Security Center를 사용하여 OS를 모니터링할 수 있습니다. Security Center가 운영 체제에서 수집하는 데이터에는 OS 형식 및 버전, OS(Windows 이벤트 로그), 실행 중인 프로세스, 컴퓨터 이름, IP 주소 및 로그인한 사용자가 포함됩니다. 또한 Log Analytics 에이전트는 크래시 덤프 파일을 수집합니다.

- [Azure Monitor를 사용하여 Azure 가상 머신 내부 호스트 로그를 수집하는 방법](../../azure-monitor/vm/quick-collect-azurevm.md)

- [Azure Security Center 데이터 수집 이해](../../security-center/security-center-enable-data-collection.md)

## <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 스토리지 보존 구성

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 2.5 | 6.4 | Customer |

Azure Monitor 내에서 조직의 규정 준수 규칙에 따라 Log Analytics 작업 영역 보존 기간을 설정합니다. 장기/보관 스토리지에 Azure Storage 계정을 사용합니다.

- [Log Analytics에서 데이터 보존 기간 변경](../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

- [Azure Storage 계정 로그에 관한 보존 정책을 구성하는 방법](../../storage/common/manage-storage-analytics-logs.md#configure-logging)

## <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 2.6 | 6.7 | Customer |

로그를 분석하고 모니터링하여 비정상 동작이 있는지 확인하고 결과를 정기적으로 검토합니다. Azure Monitor의 Log Analytics 작업 영역을 사용하여 로그를 검토하고 로그 데이터에 대한 쿼리를 수행합니다.

또는 데이터를 사용하도록 설정하여 Azure Sentinel 또는 타사 SIEM에 온보딩할 수 있습니다. 

- [Azure Sentinel을 온보딩하는 방법](../../sentinel/quickstart-onboard.md)

- [Log Analytics 작업 영역 이해](../../azure-monitor/logs/log-analytics-tutorial.md)

- [Azure Monitor에서 사용자 지정 쿼리를 수행하는 방법](../../azure-monitor/logs/get-started-queries.md)

## <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: 비정상 활동에 대한 경고 사용

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 2.7 | 6.8 | Customer |

보안 로그 및 이벤트에서 발견된 비정상적인 활동을 모니터링하고 경고하기 위해 Log Analytics 작업 영역에서 Azure Security Center를 사용합니다.

또는 데이터를 사용하도록 설정하고 Azure Sentinel에 온보딩할 수 있습니다.

- [Azure Sentinel을 온보딩하는 방법](../../sentinel/quickstart-onboard.md)

- [Azure Security Center에서 경고를 관리하는 방법](../../security-center/security-center-managing-and-responding-alerts.md)

- [로그 분석 로그 데이터에 대해 경고하는 방법](../../azure-monitor/alerts/tutorial-response.md)

## <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 2.8 | 8.6 | Customer |

Azure Virtual Machines 및 Cloud Services에 대해 맬웨어 방지 이벤트 컬렉션을 사용하도록 설정합니다.

- [Virtual Machines용 Microsoft Antimalware를 구성하는 방법](/powershell/module/servicemanagement/azure.service/set-azurevmmicrosoftantimalwareextension)

- [Cloud Services용 Microsoft Antimalware를 구성하는 방법](/powershell/module/servicemanagement/azure.service/set-azureserviceantimalwareextension)

- [Microsoft Antimalware 이해](../fundamentals/antimalware.md)

## <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 2.9 | 8.7 | Customer |

조직의 요구 사항에 맞는 DNS 로깅 솔루션을 위해 Azure Marketplace에서 타사 솔루션을 구현합니다.  

## <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅 사용

| Azure ID | CIS ID | 책임 |
|--|--|--|
| 2.10 | 8.8 | Customer |

지원되는 모든 Azure Windows Virtual Machines에서 Microsoft Monitoring Agent를 사용하여 프로세스 생성 이벤트와 CommandLine 필드를 기록합니다.   지원되는 Azure Linux Virtual Machines의 경우 노드 단위로 콘솔 로깅을 수동으로 구성하고 Syslog를 사용하여 데이터를 저장할 수 있습니다.  또한 Azure Monitor의 Log Analytics 작업 영역을 사용하여 Azure 가상 머신에서 로그를 검토하고, 기록되는 데이터에 대한 쿼리를 수행합니다. 

- [Azure Security Center에서 데이터 수집](../../security-center/security-center-enable-data-collection.md#data-collection-tier)

- [Azure Monitor에서 사용자 지정 쿼리를 수행하는 방법](../../azure-monitor/logs/get-started-queries.md)

- [Azure Monitor의 Syslog 데이터 원본](../../azure-monitor/agents/data-sources-syslog.md)


## <a name="next-steps"></a>다음 단계

- 다음 보안 제어: [ID 및 Access Control](security-control-identity-access-control.md)을 참조하세요