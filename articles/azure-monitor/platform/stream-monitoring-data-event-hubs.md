---
title: 이벤트 허브로 Azure 모니터링 데이터 스트림
description: Azure 모니터링 데이터를 이벤트 허브로 스트리밍하여 파트너를 SIEM 또는 분석 도구로 변환하는 방법을 알아봅니다.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 32bc90cc069ac82641c3aa7692c900c60db7ba87
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733092"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub"></a>이벤트 허브로 Azure 모니터링 데이터 스트리밍
Azure Monitor는 Azure, 다른 클라우드 및 온-프레미스의 응용 프로그램 및 서비스에 대한 완전한 전체 스택 모니터링 솔루션을 제공합니다. Azure Monitor를 사용하여 데이터를 분석하고 다양한 모니터링 시나리오에 활용할 수 있을 뿐만 아니라 환경의 다른 모니터링 도구로 보내야 할 수도 있습니다. 대부분의 경우 모니터링 데이터를 외부 도구로 스트리밍하는 가장 효과적인 방법은 [Azure Event Hubs를](/azure/event-hubs/)사용하는 것입니다. 이 문서에서는 모니터링 데이터를 다른 소스에서 이벤트 허브로 스트리밍하고 자세한 지침에 대한 링크를 제공하는 방법에 대한 간략한 설명을 제공합니다.


## <a name="create-an-event-hubs-namespace"></a>Event Hubs 네임스페이스 만들기

모든 데이터 원본에 대한 스트리밍을 구성하기 전에 [이벤트 허브 네임스페이스 및 이벤트 허브를 만들어야](../../event-hubs/event-hubs-create.md)합니다. 이 네임스페이스 및 이벤트 허브는 모든 모니터링 데이터에 대한 대상입니다. Event Hubs 네임스페이스는 같은 액세스 정책을 공유하는 Event Hubs의 논리적 그룹으로, 스토리지 계정은 해당 스토리지 계정 내에서 개별 Blob을 갖기 쉽습니다. 모니터링 데이터 스트리밍에 사용하는 이벤트 허브 네임스페이스 및 이벤트 허브에 대한 다음 세부 정보를 고려하십시오.

* 처리량 단위 수를 사용하여 이벤트 허브에 대한 처리량 비율을 높일 수 있습니다. 일반적으로 하나의 처리량 단위만 필요합니다. 로그 사용량이 증가함에 따라 확장해야 하는 경우 네임스페이스의 처리량 단위 수를 수동으로 늘리거나 자동 인플레이션을 활성화할 수 있습니다.
* 파티션 수를 사용하면 많은 소비자 간에 소비량의 균형을 이룰 수 있습니다. 단일 파티션은 초당 최대 20MBps 또는 약 20,000개의 메시지를 지원할 수 있습니다. 데이터를 소비하는 도구에 따라, 여러 파티션을 통한 소비를 지원할 수도있 고 지원하지 않을 수도 있습니다. 설정할 파티션 수에 대해 잘 모르는 경우 4개의 파티션을 시작하는 것이 합리적입니다.
* 이벤트 허브에서 메시지 보존을 최소 7일로 설정합니다. 소모 도구가 하루 이상 중단되면 최대 7일 동안 이벤트가 중단된 위치를 도구를 선택할 수 있습니다.
* 이벤트 허브에 기본 소비자 그룹을 사용해야 합니다. 2개의 다른 도구에서 동일한 이벤트 허브의 동일한 데이터를 소비하려는 경우가 아니면, 다른 소비자 그룹을 만들거나 별도의 소비자 그룹을 사용할 필요가 없습니다.
* Azure 활동 로그의 경우 이벤트 허브 네임스페이스를 선택하고 Azure Monitor는 _인사이트-로그-운영 로그라는_네임스페이스 내에 이벤트 허브를 만듭니다. 다른 로그 유형의 경우 기존 이벤트 허브를 선택하거나 Azure Monitor에서 로그 범주당 이벤트 허브를 만들 수 있습니다.
* 아웃바운드 포트 5671 및 5672는 일반적으로 이벤트 허브에서 데이터를 사용하는 컴퓨터 또는 VNET에서 열어야 합니다.

## <a name="monitoring-data-available"></a>사용 가능한 데이터 모니터링
[Azure Monitor에 대한 모니터링 데이터 소스는](data-sources.md) Azure 응용 프로그램에 대한 다양한 데이터 계층과 각각에 대해 사용할 수 있는 모니터링 데이터의 종류를 설명합니다. 다음 표에는 이러한 각 계층과 해당 데이터를 이벤트 허브로 스트리밍하는 방법에 대한 설명이 나열되어 있습니다. 자세한 내용은 제공된 링크를 따르십시오.

| 계층 | 데이터 | 방법 |
|:---|:---|:---|
| [Azure 테넌트](data-sources.md#azure-tenant) | Azure Active Directory 감사 로그 | AAD 테넌트에서 테넌트 진단 설정을 구성합니다. 자습서: 자세한 내용은 [Azure Active Directory 로그를 Azure 이벤트 허브로 스트리밍합니다.](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |
| [Azure 구독](data-sources.md#azure-subscription) | Azure 동작 로그 | 로그 프로필을 만들어 활동 로그 이벤트를 이벤트 허브로 내보냅니다.  자세한 내용은 [Azure 이벤트 허브에 대한 Azure 플랫폼 로그 스트림을](resource-logs-stream-event-hubs.md) 참조하십시오. |
| [Azure 리소스](data-sources.md#azure-resources) | 플랫폼 메트릭<br> 리소스 로그 |두 가지 유형의 데이터는 리소스 진단 설정을 사용하여 이벤트 허브로 전송됩니다. 자세한 내용은 [이벤트 허브에 Azure 리소스 로그 스트리밍을](resource-logs-stream-event-hubs.md) 참조하세요. |
| [운영 체제(게스트)](data-sources.md#operating-system-guest) | Azure 가상 머신 | Azure의 Windows 및 Linux 가상 컴퓨터에 [Azure 진단 확장을 설치합니다.](diagnostics-extension-overview.md) Windows VM에 대한 자세한 내용은 이벤트 허브를 사용하고 Linux 진단 [확장을 사용하여 메트릭 및 로그를 모니터링하여](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings) Linux VM에 대한 자세한 내용을 모니터링하여 [핫 경로의 Azure 진단 데이터 스트리밍을](diagnostics-extension-stream-event-hubs.md) 참조하십시오. |
| [애플리케이션 코드](data-sources.md#application-code) | Application Insights | 응용 프로그램 인사이트는 이벤트 허브로 데이터를 스트리밍하는 직접적인 방법을 제공하지 않습니다. Application Insights 데이터의 [지속적인 내보내기를](../../azure-monitor/app/export-telemetry.md) 저장소 계정으로 설정한 다음 논리 앱을 사용하여 논리 앱이 있는 [수동 스트리밍에](#manual-streaming-with-logic-app)설명된 대로 이벤트 허브로 데이터를 보낼 수 있습니다. |

## <a name="manual-streaming-with-logic-app"></a>논리 앱으로 수동 스트리밍
이벤트 허브로 직접 스트리밍할 수 없는 데이터의 경우 Azure 저장소에 작성한 다음 [Blob 저장소에서 데이터를 가져와](../../connectors/connectors-create-api-azureblobstorage.md#add-action) 이벤트 [허브에 메시지로 푸시하는](../../connectors/connectors-create-api-azure-event-hubs.md#add-action)시간 트리거된 논리 앱을 사용할 수 있습니다. 


## <a name="partner-tools-with-azure-monitor-integration"></a>Azure 모니터 통합을 위한 파트너 도구

Azure Monitor를 사용하여 모니터링 데이터를 이벤트 허브로 라우팅하면 외부 SIEM 및 모니터링 도구와 쉽게 통합할 수 있습니다. Azure 모니터 통합을 사용 하 고 도구의 예는 다음과 같습니다.

| 도구 | Azure에서 호스팅됨 | 설명 |
|:---|:---| :---|
|  IBM QRadar | 예 | Microsoft Azure DSM 및 Microsoft Azure 이벤트 허브 프로토콜은 [IBM 지원 웹 사이트](https://www.ibm.com/support)에서 다운로드할 수 있습니다. [QRadar DSM 구성에서](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0)Azure와의 통합에 대해 자세히 알아볼 수 있습니다. |
| Splunk | 예 | [스플렁크에 대한 Azure 모니터 추가 기능](https://splunkbase.splunk.com/app/3534/) 스플렁크베이스에서 사용할 수있는 오픈 소스 프로젝트입니다. 이 설명서는 [Azure 모니터 애드온에서 스플렁크를 사용할](https://github.com/Microsoft/AzureMonitorAddonForSplunk/wiki/Azure-Monitor-Addon-For-Splunk)수 있습니다.<br><br> 스플렁크 인스턴스에 추가 기능을 설치할 수 없는 경우 프록시를 사용하거나 Splunk Cloud에서 실행 중인 경우 이벤트 허브의 새 메시지에 의해 트리거되는 [Azure Function For Splunk를](https://github.com/Microsoft/AzureFunctionforSplunkVS)사용하여 이러한 이벤트를 스플렁크 HTTP 이벤트 수집기로 전달할 수 있습니다. |
| sumologic | 예 | 이벤트 허브에서 데이터를 사용 하도록 SumoLogic을 설정 하는 방법에 대 한 지침은 이벤트 허브에서 [Azure 감사 앱에 대 한 로그 수집에서](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub)사용할 수 있습니다. |
| ArcSight | 예 | ArcSight Azure 이벤트 허브 스마트 커넥터는 [ArcSight 스마트 커넥터 컬렉션의](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852)일부로 사용할 수 있습니다. |
| Syslog 서버 | 예 | Azure Monitor 데이터를 syslog 서버로 직접 스트리밍하려는 경우 [Azure 함수를 기반으로](https://github.com/miguelangelopereira/azuremonitor2syslog/)하는 솔루션을 사용할 수 있습니다.
| 로그 리듬 | 예| 이벤트 허브에서 로그를 수집하도록 LogRhythm을 설정하는 방법에 대한 지침은 [여기에서](https://logrhythm.com/six-tips-for-securing-your-azure-cloud-environment/)확인할 수 있습니다. 
|Logz.io | 예 | 자세한 내용은 [Azure에서 실행되는 Java 앱에 대한 Logz.io 사용하여 모니터링 및 로깅을 시작하기](https://docs.microsoft.com/azure/developer/java/fundamentals/java-get-started-with-logzio) 를 참조하십시오.


## <a name="next-steps"></a>다음 단계
* [활동 로그를 저장소 계정에 보관](../../azure-monitor/platform/archive-activity-log.md)
* [Azure 활동 로그의 개요 읽기](../../azure-monitor/platform/platform-logs-overview.md)
* [활동 로그 이벤트를 기반으로 경고 설정](../../azure-monitor/platform/alerts-log-webhook.md)


