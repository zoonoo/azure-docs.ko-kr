---
title: Azure 보안 제어 - 로깅 및 모니터링
description: 보안 제어 로깅 및 모니터링
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: ae9c678d9dfca895ec74ed92bcb1b541db6b134e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76545503"
---
# <a name="security-control-logging-and-monitoring"></a>보안 제어: 로깅 및 모니터링

보안 로깅 및 모니터링은 Azure 서비스에 대한 감사 로그 사용, 획득 및 저장과 관련된 활동에 중점을 둡니다.

## <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인된 시간 동기화 소스 사용

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

Microsoft는 Azure 리소스에 대한 시간 원본을 유지 관리하지만 계산 리소스에 대한 시간 동기화 설정을 관리할 수 있는 옵션이 있습니다.

Azure 계산 리소스에 대한 시간 동기화를 구성하는 방법:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

## <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 2.2 | 6.5, 6.6 | Customer |

Azure Monitor를 통해 로그를 수집하여 엔드포인트 장치, 네트워크 리소스 및 기타 보안 시스템에서 생성된 보안 데이터를 집계합니다. Azure Monitor 내에서 로그 분석 작업 영역을 사용하여 분석을 쿼리하고 수행하고 장기/보관 저장소에 Azure 저장소 계정을 사용합니다.

또는 Azure Sentinel 또는 타사 SIEM에 대한 데이터를 활성화및 온보던할 수 있습니다. Azure 센티넬 온보보드 방법:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Azure 모니터를 사용하여 플랫폼 로그 및 메트릭을 수집하는 방법:

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Azure 모니터를 사용하여 Azure 가상 컴퓨터 내부 호스트 로그를 수집하는 방법:

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

Azure 모니터 및 타사 SIEM 통합을 시작하는 방법:

https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대한 감사 로깅 사용

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 2.3 | 6.2, 6.3 | Customer |

감사, 보안 및 진단 로그에 액세스하려면 Azure 리소스에서 진단 설정을 사용하도록 설정합니다. 자동으로 사용할 수 있는 활동 로그에는 이벤트 소스, 날짜, 사용자, 타임스탬프, 소스 주소, 대상 주소 및 기타 유용한 요소가 포함됩니다.

Azure 모니터를 사용하여 플랫폼 로그 및 메트릭을 수집하는 방법:

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Azure의 로깅 및 다른 로그 유형 이해:

https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 2.4 | 6.2, 6.3 | Customer |

계산 리소스가 Microsoft가 소유하고 있는 경우 Microsoft는 이를 모니터링해야 합니다. 계산 리소스가 조직에서 소유하는 경우 이를 모니터링하는 것은 사용자의 책임입니다. Azure 보안 센터를 사용하여 OS를 모니터링할 수 있습니다. 운영 체제에서 보안 센터에서 수집한 데이터에는 OS 유형 및 버전, OS 로그(Windows 이벤트 로그), 실행 중인 프로세스, 컴퓨터 이름, IP 주소 및 로그인된 사용자가 포함됩니다. 로그 분석 에이전트는 크래시 덤프 파일도 수집합니다.

Azure 모니터를 사용하여 Azure 가상 컴퓨터 내부 호스트 로그를 수집하는 방법:

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

Azure 보안 센터 데이터 수집 이해:

https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection

## <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 저장소 보존 구성

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 2.5 | 6.4 | Customer |

Azure Monitor 내에서 조직의 규정 준수 규정에 따라 로그 분석 작업 영역 보존 기간을 설정합니다. 장기/보관 저장소에 Azure 저장소 계정을 사용합니다.

로그 분석 작업 영역에 대한 로그 보존 매개 변수를 설정하는 방법:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

## <a name="26-monitor-and-review-logs"></a>2.6: 로그 모니터링 및 검토

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 2.6 | 6.7 | Customer |

로그를 분석 및 모니터링하여 비정상적인 동작을 모니터링하고 결과를 정기적으로 검토합니다. Azure 모니터의 로그 분석 작업 영역을 사용하여 로그를 검토하고 로그 데이터에 대한 쿼리를 수행합니다.

또는 Azure Sentinel 또는 타사 SIEM에 대한 데이터를 활성화및 온보드할 수 있습니다. 

Azure 센티넬 온보보드 방법:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

로그 분석 작업 영역 이해:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Azure 모니터에서 사용자 지정 쿼리를 수행하는 방법:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

## <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: 비정상적인 활동에 대한 경고 사용

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 2.7 | 6.8 | Customer |

보안 로그 및 이벤트에서 발견된 비정상적인 활동을 모니터링하고 경고하기 위해 로그 분석 작업 영역이 있는 Azure 보안 센터를 사용합니다.

또는 Azure Sentinel에 대한 데이터를 활성화하고 온보드할 수 있습니다.

Azure 센티넬 온보보드 방법:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Azure 보안 센터에서 경고를 관리하는 방법:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

로그 분석 로그 데이터에 대해 경고하는 방법:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

## <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅 중앙 집중화

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 2.8 | 8.6 | Customer |

Azure 가상 시스템 및 클라우드 서비스에 대한 맬웨어 방지 이벤트 컬렉션을 사용하도록 설정합니다.

가상 컴퓨터에 대 한 마이크로소프트 맬웨어 방지를 구성 하는 방법:

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmmicrosoftantimalwareextension?view=azuresmps-4.0.0

클라우드 서비스에 대 한 마이크로소프트 맬웨어 방지를 구성 하는 방법:

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureserviceantimalwareextension?view=azuresmps-4.0.0

마이크로소프트 맬웨어 방지 이해:

https://docs.microsoft.com/azure/security/fundamentals/antimalware

## <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅 사용

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 2.9 | 8.7 | Customer |

DNS 로깅을 위한 타사 솔루션을 구현합니다.

## <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅 사용

| Azure ID | CIS 아이디 | 책임 |
|--|--|--|
| 2.1 | 8.8 | Customer |

노드별로 콘솔 로깅 및 PowerShell 전사를 수동으로 구성합니다.

## <a name="next-steps"></a>다음 단계

다음 보안 제어 보기: [ID 및 액세스 제어](security-control-identity-access-control.md)
