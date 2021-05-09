---
title: 이벤트 허브와 외부 파트너로 Azure 모니터링 데이터 스트리밍
description: Azure 모니터링 데이터를 이벤트 허브로 스트리밍하여 파트너 SIEM 또는 분석 도구로 데이터를 가져오는 방법을 알아봅니다.
services: azure-monitor
author: bwren
ms.author: bwren
ms.topic: conceptual
ms.date: 07/15/2020
ms.openlocfilehash: 7592935afadc88c4b9e0e5f3c5f9c83d42c63209
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768744"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-or-external-partner"></a>이벤트 허브 또는 외부 파트너로 Azure 모니터링 데이터 스트리밍

Azure Monitor는 Azure, 다른 클라우드, 온-프레미스의 애플리케이션 및 서비스를 위한 완전한 전체 스택 모니터링 솔루션을 제공합니다. Azure Monitor 사용하여 해당 데이터를 분석하고 여러 모니터링 시나리오에 활용하는 것 외에도 사용자 환경의 다른 모니터링 도구에 해당 데이터를 전송해야 할 수도 있습니다. 대부분의 경우 모니터링 데이터를 외부 도구로 스트리밍하는 가장 효과적인 방법은 [Azure Event Hubs](../../event-hubs/index.yml)를 사용하는 것입니다. 이 문서에서는 이 작업을 수행하는 방법을 간략히 설명하고 데이터를 보낼 수 있는 파트너 중 일부를 나열합니다. 파트너는 Azure Monitor와 특수하게 통합되어 있기도 하도 Azure에서 호스트될 수도 있습니다.  

## <a name="create-an-event-hubs-namespace"></a>Event Hubs 네임스페이스 만들기

데이터 원본의 스트리밍을 구성하기 전에 [Event Hubs 네임스페이스와 이벤트 허브를 만들어야](../../event-hubs/event-hubs-create.md) 합니다. 이 네임스페이스 및 이벤트 허브는 모든 모니터링 데이터에 대한 대상입니다. Event Hubs 네임스페이스는 같은 액세스 정책을 공유하는 Event Hubs의 논리적 그룹으로, 스토리지 계정은 해당 스토리지 계정 내에서 개별 Blob을 갖기 쉽습니다. 모니터링 데이터를 스트리밍하기 위해 사용하는 이벤트 허브 네임스페이스 및 이벤트 허브에 대한 다음 정보를 고려하세요.

* 처리량 단위 수를 사용하여 이벤트 허브에 대한 처리량 비율을 높일 수 있습니다. 일반적으로 하나의 처리량 단위만 필요합니다. 로그 사용량이 증가함에 따라 스케일 업할 경우 네임스페이스의 처리량 단위 수를 수동으로 늘리거나 자동 인플레이션을 사용하도록 설정할 수 있습니다.
* 파티션 수를 사용하면 많은 소비자 간에 소비량의 균형을 이룰 수 있습니다. 단일 파티션으로 최대 20MBps, 초당 약 20,000개의 메시지까지 지원할 수 있습니다. 데이터를 소비하는 도구에 따라, 여러 파티션을 통한 소비를 지원할 수도있 고 지원하지 않을 수도 있습니다. 설정할 파티션 수를 잘 모를 경우 4개의 파티션으로 시작하는 것이 좋습니다.
* 이벤트 허브의 메시지 보존 기간은 7일로 설정합니다. 이렇게 하면 소비 도구가 하루 넘게 다운될 경우 도구에서 최대 7일이 경과된 이벤트에 대해 중단된 지점을 선택할 수 있습니다.
* 이벤트 허브의 기본 소비자 그룹을 사용하는 것이 좋습니다. 2개의 다른 도구에서 동일한 이벤트 허브의 동일한 데이터를 소비하려는 경우가 아니면, 다른 소비자 그룹을 만들거나 별도의 소비자 그룹을 사용할 필요가 없습니다.
* Azure 활동 로그의 경우 Event Hubs 네임스페이스를 선택하면, Azure Monitor는 해당 네임스페이스 내에 _insights-logs-operationallogs_ 라는 이벤트 허브를 만듭니다. 다른 로그 형식의 경우, 기존 이벤트 허브를 선택하거나 Azure Monitor에서 로그 범주별로 이벤트 허브를 만들도록 할 수 있습니다.
* 일반적으로 이벤트 허브의 데이터를 사용하는 컴퓨터나 VNET에서 아웃바운드 포트 5671 및 5672를 열어야 합니다.

## <a name="monitoring-data-available"></a>사용 가능한 모니터링 데이터
[Sources of monitoring data for Azure Monitor](../agents/data-sources.md)(Azure Monitor의 모니터링 데이터 원본)에서는 Azure 애플리케이션의 여러 데이터 계층과 각 계층에서 사용할 수 있는 모니터링 데이터 종류를 설명합니다. 다음 표에는 이러한 각 계층과 해당 데이터를 이벤트 허브로 스트리밍할 수 있는 방법에 대한 설명이 나와 있습니다. 자세한 내용을 보려면 제공된 링크로 이동하세요.

| 계층 | 데이터 | 메서드 |
|:---|:---|:---|
| [Azure 테넌트](../agents/data-sources.md#azure-tenant) | Azure Active Directory 감사 로그 | AAD 테넌트에서 테넌트 진단 설정을 구성합니다. 자세한 내용은 [자습서: Azure 이벤트 허브로 Azure Active Directory 로그 스트리밍](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)을 참조하세요. |
| [Azure 구독](../agents/data-sources.md#azure-subscription) | Azure 동작 로그 | 활동 로그 이벤트를 Event Hubs로 내보내기 위한 로그 프로필을 만듭니다.  자세한 내용은 [Stream Azure platform logs to Azure Event Hubs](../essentials/resource-logs.md#send-to-azure-event-hubs)(Azure Event Hubs로 Azure 플랫폼 로그 스트리밍)를 참조하세요. |
| [Azure 리소스](../agents/data-sources.md#azure-resources) | 플랫폼 메트릭<br> 리소스 로그 |두 가지 유형의 데이터는 리소스 진단 설정을 사용하여 이벤트 허브로 전송됩니다. 자세한 내용은 [Stream Azure resource  logs to an event hub](../essentials/resource-logs.md#send-to-azure-event-hubs)(이벤트 허브로 Azure 리소스 로그 스트리밍)를 참조하세요. |
| [운영 체제(게스트)](../agents/data-sources.md#operating-system-guest) | Azure 가상 머신 | Azure에서 Windows 및 Linux 가상 머신에 [Azure Diagnostics 확장](../agents/diagnostics-extension-overview.md)을 설치합니다. Windows VM에 대한 자세한 내용은 [Streaming Azure Diagnostics data in the hot path by using Event Hubs](../agents/diagnostics-extension-stream-event-hubs.md)(Event Hubs를 사용하여 실행 부하 과다 경로에서 Azure Diagnostics 데이터 스트리밍)를 참조하고 Linux VM에 대한 자세한 내용은 [Use Linux Diagnostic Extension to monitor metrics and logs](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings)(Linux 진단 확장을 사용하여 메트릭 및 로그 모니터링)를 참조하세요. |
| [애플리케이션 코드](../agents/data-sources.md#application-code) | Application Insights | Application Insights는 이벤트 허브로 데이터를 스트리밍하는 직접적인 방법을 제공하지 않습니다. 스토리지 계정으로 Application Insights 데이터의 [연속 내보내기를 설정](../app/export-telemetry.md)한 다음, [논리 앱을 사용한 수동 스트리밍](#manual-streaming-with-logic-app)에 설명된 대로 논리 앱을 사용하여 이벤트 허브로 데이터를 보낼 수 있습니다. |

## <a name="manual-streaming-with-logic-app"></a>논리 앱을 사용한 수동 스트리밍
이벤트 허브로 직접 스트리밍할 수 없는 데이터의 경우 Azure Storage에 쓴 다음 시간으로 트리거되는 논리 앱을 사용하여 [Blob 스토리지에서 데이터를 가져오고](../../connectors/connectors-create-api-azureblobstorage.md#add-action) [이벤트 허브에 메시지로 푸시](../../connectors/connectors-create-api-azure-event-hubs.md#add-action)할 수 있습니다. 


## <a name="partner-tools-with-azure-monitor-integration"></a>Azure Monitor 통합을 사용하는 파트너 도구

Azure Monitor를 사용하여 모니터링 데이터를 이벤트 허브로 라우팅하면 외부 SIEM 및 모니터링 도구와 쉽게 통합할 수 있습니다. Azure Monitor 통합을 사용하는 도구의 예는 다음과 같습니다.

| 도구 | Azure에서 호스트 | Description |
|:---|:---| :---|
|  IBM QRadar | No | Microsoft Azure DSM 및 Microsoft Azure 이벤트 허브 프로토콜은 [IBM 지원 웹 사이트](https://www.ibm.com/support)에서 다운로드할 수 있습니다. Azure와의 통합에 대한 자세한 내용은 [QRadar DSM configuration](https://www.ibm.com/docs/en/dsm?topic=options-configuring-microsoft-azure-event-hubs-communicate-qradar)(QRadar DSM 구성)을 참조하세요. |
| Splunk | No | [Splunk용 Microsoft Azure 추가 기능](https://splunkbase.splunk.com/app/3757/)은 Splunkbase에서 사용할 수 있는 오픈 소스 프로젝트입니다. <br><br> Splunk 인스턴스에 추가 기능을 설치할 수 없는 경우, 예를 들어 프록시를 사용 중이거나 Splunk 클라우드에서 실행 중인 경우 이벤트 허브의 새 메시지로 트리거되는 [Splunk용 Azure 함수](https://github.com/Microsoft/AzureFunctionforSplunkVS)를 사용하여 이러한 이벤트를 Splunk HTTP 이벤트 수집기에 전달할 수 있습니다. |
| sumologic | No | 이벤트 허브에서 데이터를 사용하도록 SumoLogic을 설정하기 위한 지침은 [Collect Logs for the Azure Audit App from Event Hub](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub)(이벤트 허브에서 Azure Audit App의 로그 수집)에서 사용할 수 있습니다. |
| ArcSight | No | ArcSight Azure Event Hub 스마트 커넥터는 [ArcSight 스마트 커넥터 컬렉션](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852)의 일부로 제공됩니다. |
| Syslog 서버 | No | Azure Monitor 데이터를 syslog 서버로 직접 스트리밍하려면 [Azure 함수 기반의 솔루션](https://github.com/miguelangelopereira/azuremonitor2syslog/)을 사용할 수 있습니다.
| LogRhythm | No| 이벤트 허브에서 로그를 수집하도록 LogRhythm을 설정하기 위한 지침은 [여기](https://logrhythm.com/six-tips-for-securing-your-azure-cloud-environment/)에서 확인할 수 있습니다. 
|Logz.io | 예 | 자세한 내용은 [Azure에서 실행되는 Java 앱용 Logz.io를 사용하여 모니터링 및 로깅 시작](/azure/developer/java/fundamentals/java-get-started-with-logzio)을 참조하세요.

다른 파트너도 사용할 수 있습니다. 모든 Azure Monitor 파트너와 해당 기능의 전체 목록은 [Azure Monitor partner integrations](../partners.md)(Azure Monitor 파트너 통합)를 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Archive the Activity log to a storage account](./activity-log.md#legacy-collection-methods)(스토리지 계정에 활동 로그 보관)
* [Read the overview of the Azure Activity log](../essentials/platform-logs-overview.md)(Azure 활동 로그 개요 읽기)
* [Set up an alert based on an Activity log event](../alerts/alerts-log-webhook.md)(활동 로그 이벤트를 기반으로 경고 설정)
