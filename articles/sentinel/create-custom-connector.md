---
title: Azure 센티널 사용자 지정 커넥터를 만들기 위한 리소스 | Microsoft Docs
description: Azure 센티널 용 사용자 지정 커넥터를 만드는 데 사용할 수 있는 리소스에 대해 알아봅니다. 메서드에는 Log Analytics 에이전트와 API, Logstash, Logic Apps, PowerShell 및 Azure Functions가 포함 됩니다.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2021
ms.author: bagol
ms.openlocfilehash: 25f83088bdc55dbafe7ccf0ff06b0c6595c9ea71
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724356"
---
# <a name="resources-for-creating-azure-sentinel-custom-connectors"></a>Azure 센티널 사용자 지정 커넥터를 만들기 위한 리소스

Azure 센티널은 [azure 서비스 및 외부 솔루션에 대 한 다양 한 기본 제공 커넥터](connect-data-sources.md)를 제공 하며, 전용 커넥터가 없는 일부 원본에서의 데이터 수집 지원 합니다.

사용 가능한 기존 솔루션 중 하나를 사용 하 여 데이터 원본을 Azure 센티널에 연결할 수 없는 경우 고유한 데이터 원본 커넥터를 만드는 것이 좋습니다.

지원 되는 커넥터의 전체 목록은 [Azure 센티널: 커넥터 그랜드 (CEF, Syslog, Direct, 에이전트, 사용자 지정 등)](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-the-connectors-grand-cef-syslog-direct-agent/ba-p/803891) 블로그 게시물을 참조 하세요.

## <a name="compare-custom-connector-methods"></a>사용자 지정 커넥터 메서드 비교

다음 표에서는이 문서에서 설명 하는 사용자 지정 커넥터를 만들기 위한 각 방법에 대 한 필수 정보를 비교 합니다. 각 방법에 대 한 자세한 내용을 보려면 표에서 링크를 선택 합니다.

|메서드 설명  |기능 | 서버를 사용하지 않음    |복잡성  |
|---------|---------|---------|---------|
|**[Log Analytics 에이전트](#connect-with-the-log-analytics-agent)** <br>온-프레미스 및 IaaS 원본에서 파일을 수집 하는 데 가장 적합 합니다.   | 파일 컬렉션 전용  |   예      |낮음         |
|**[Logstash](#connect-with-logstash)** <br>온-프레미스 및 IaaS 원본, 플러그 인을 사용할 수 있는 원본 및 이미 Logstash 태 트에 익숙한 조직에 가장 적합 합니다.  | 사용 가능한 플러그 인 및 사용자 지정 플러그 인 기능은 상당한 유연성을 제공 합니다.   |   전혀 VM 또는 VM 클러스터를 실행 해야 합니다.           |   거의 플러그 인을 사용 하는 많은 시나리오 지원      |
|**[Logic Apps](#connect-with-logic-apps)** <br>높은 비용; 대용량 데이터의 경우 방지 <br>볼륨이 낮은 클라우드 원본에 가장 적합  | 코드 없는 프로그래밍을 사용 하면 알고리즘 구현에 대 한 지원 없이 유연성을 제한할 수 있습니다.<br><br> 사용 가능한 작업에서 요구 사항을 이미 지원 하지 않는 경우 사용자 지정 작업을 만들면 복잡성이 추가 될 수 있습니다.    |    예         |   거의 간단 하 고 코드 없는 개발      |
|**[PowerShell](#connect-with-powershell)** <br>프로토타입 및 정기 파일 업로드에 가장 적합 합니다. | 파일 컬렉션에 대 한 직접 지원. <br><br>PowerShell을 사용 하 여 더 많은 소스를 수집할 수 있지만 코딩 하 고 스크립트를 서비스로 구성 해야 합니다.      |예               |  낮음       |
|**[Log Analytics API](#connect-with-the-log-analytics-api)** <br>Isv의 통합 구현 및 고유한 컬렉션 요구 사항에 가장 적합   | 는 코드에서 사용할 수 있는 모든 기능을 지원 합니다.  | 구현에 따라 달라 집니다.           |     높음    |
|**[Azure Functions](#connect-with-azure-functions)** 대용량 클라우드 원본 및 고유한 컬렉션 요구 사항에 가장 적합  | 는 코드에서 사용할 수 있는 모든 기능을 지원 합니다.  |  예             |     최고 프로그래밍 지식 필요    |
|     |         |                |

> [!TIP]
> 동일한 커넥터에 대해 Logic Apps 및 Azure Functions를 사용 하는 비교는 다음을 참조 하세요.
> 
> - [Fastly 웹 응용 프로그램 방화벽 로그를 Azure 센티널에 수집](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-fastly-web-application-firewall-logs-into-azure-sentinel/ba-p/1238804)
> - Office 365 (azure 센티널 GitHub 커뮤니티): [논리 앱 커넥터](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Get-O365Data)  |  [Azure 함수 커넥터](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data)
> 

## <a name="connect-with-the-log-analytics-agent"></a>Log Analytics 에이전트와 연결

데이터 원본이 파일에 이벤트를 전달 하는 경우 Azure Monitor Log Analytics 에이전트를 사용 하 여 사용자 지정 커넥터를 만드는 것이 좋습니다.

- 자세한 내용은 [Azure Monitor에서 사용자 지정 로그 수집](../azure-monitor/agents/data-sources-custom-logs.md)을 참조 하세요.

- 이 메서드에 대 한 예제는 [Azure Monitor에서 Linux 용 Log Analytics 에이전트를 사용 하 여 사용자 지정 JSON 데이터 원본 수집](../azure-monitor/agents/data-sources-json.md)을 참조 하세요.

## <a name="connect-with-logstash"></a>Logstash 태로 연결

[Logstash 태](https://www.elastic.co/logstash)에 익숙한 경우 [Azure 센티널의 logstash 태 시 출력 플러그 인에서](connect-logstash.md) logstash를 사용 하 여 사용자 지정 커넥터를 만들 수 있습니다.

Azure 센티널 Logstash 출력 플러그 인을 사용 하 여 Logstash 태 트 입력 및 필터링 플러그 인을 사용 하 고 Logstash 태 파이프라인에 대 한 출력으로 Azure 센티널을 구성할 수 있습니다. Logstash 태에는 Event Hubs, Apache Kafka, 파일, 데이터베이스 및 클라우드 서비스와 같은 다양 한 원본에서 입력을 사용할 수 있도록 하는 많은 플러그 인 라이브러리가 있습니다. 필터링 플러그 인을 사용 하 여 이벤트를 구문 분석 하 고, 불필요 한 이벤트를 필터링 하 고, 값을 난독 처리 합니다.

Logstash를 사용자 지정 커넥터로 사용 하는 예는 다음을 참조 하세요.

- Azure 센티널 (블로그)을 [사용 하 여 AWS 로그의 자본 1 위반 TTPs에 대 한 구하기](https://techcommunity.microsoft.com/t5/azure-sentinel/hunting-for-capital-one-breach-ttps-in-aws-logs-using-azure/ba-p/1019767)
- [Radware Azure 센티널 구현 가이드](https://support.radware.com/ci/okcsFattach/get/1025459_3)

유용한 Logstash 플러그 인의 예는 다음을 참조 하세요.

- [Cloudwatch 입력 플러그 인](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-cloudwatch.html)
- [Azure Event Hubs 플러그 인](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-azure_event_hubs.html)
- [Google 클라우드 저장소 입력 플러그 인](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_cloud_storage.html)
- [Google_pubsub 입력 플러그 인](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-google_pubsub.html)

> [!TIP]
> Logstash 태 트는 클러스터를 사용 하 여 크기 조정 된 데이터 수집도 가능 합니다. 자세한 내용은 [대규모로 부하가 분산 된 Logstash 태 트 VM 사용](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854)을 참조 하세요.
>

## <a name="connect-with-logic-apps"></a>Logic Apps 연결

Azure [논리 앱](../logic-apps/index.yml) 을 사용 하 여 azure 센티널에 대해 서버 리스 사용자 지정 커넥터를 만듭니다.

> [!NOTE]
> Logic Apps를 사용 하 여 서버를 사용 하지 않는 커넥터를 만드는 것이 편리할 수 있지만, 커넥터에 대해 Logic Apps를 사용 하면 대용량 데이터에 대해 비용이 많이 들 수 있습니다
>
> 이 방법은 볼륨이 낮은 데이터 원본에만 사용 하거나 데이터 업로드를 보강는 것이 좋습니다.
>

1. **다음 트리거 중 하나를 사용 하 여 Logic Apps를 시작** 합니다.

    |트리거  |Description  |
    |---------|---------|
    |**되풀이 작업**     |   예를 들어 특정 파일, 데이터베이스 또는 외부 Api에서 정기적으로 데이터를 검색 하도록 논리 앱을 예약 합니다. <br>자세한 내용은 [Azure Logic Apps에서 되풀이 작업 및 워크플로 만들기, 예약 및 실행](../connectors/connectors-native-recurrence.md)을 참조 하세요.      |
    |**주문형 트리거**     | 수동 데이터 수집 및 테스트를 위해 요청 시 논리 앱을 실행 합니다. <br>자세한 내용은  [HTTPS 끝점을 사용 하 여 논리 앱 호출, 트리거 또는 중첩](../logic-apps/logic-apps-http-endpoint.md)을 참조 하세요.        |
    |**HTTP/S 끝점**     |  스트리밍 및 원본 시스템이 데이터 전송을 시작할 수 있는 경우에 권장 됩니다. <br>자세한 내용은 [HTTP 또는 HTTPs를 통한 서비스 끝점 호출](../connectors/connectors-native-http.md)을 참조 하세요.       |
    |     |         |

1. **정보를 읽는 논리 앱 커넥터를 사용 하 여 이벤트를 가져옵니다**. 다음은 그 예입니다. 

    - [REST API에 연결](/connectors/custom-connectors/)
    - [SQL Server에 연결](/connectors/sql/)
    - [파일 시스템에 연결](/connectors/filesystem/)

    > [!TIP]
    > REST Api, SQL Server 및 파일 시스템에 대 한 사용자 지정 커넥터는 온-프레미스 데이터 원본에서 데이터 검색도 지원 합니다. 자세한 내용은 [온-프레미스 데이터 게이트웨이 설치](/connectors/filesystem/) 설명서를 참조 하세요.
    >

1. **검색 하려는 정보를 준비** 합니다.

    예를 들어 json [구문 분석 작업](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) 을 사용 하 여 json 콘텐츠에서 속성에 액세스 하면 논리 앱에 대 한 입력을 지정할 때 동적 콘텐츠 목록에서 해당 속성을 선택할 수 있습니다.

    자세한 내용은 [Azure Logic Apps에서 데이터 작업 수행](../logic-apps/logic-apps-perform-data-operations.md)을 참조 하세요.

1. **Log Analytics에 데이터를 씁니다**.

    자세한 내용은 [Azure Log Analytics 데이터 수집기](/connectors/azureloganalyticsdatacollector/) 설명서를 참조 하세요.

Logic Apps를 사용 하 여 Azure 센티널에 대 한 사용자 지정 커넥터를 만드는 방법의 예는 다음을 참조 하세요.

- [데이터 수집기 API를 사용하여 데이터 파이프라인 만들기](/connectors/azureloganalyticsdatacollector/)
- [Webhook를 사용 하는 Palo Alto의 sma Sma 논리 앱 커넥터](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Ingest-Prisma) (Azure 센티널 GitHub 커뮤니티)
- [예약 된 활성화로 Microsoft 팀 호출 보호](https://techcommunity.microsoft.com/t5/azure-sentinel/secure-your-calls-monitoring-microsoft-teams-callrecords/ba-p/1574600) (블로그)
- [수집 AlienVault \X 위협 표시기를 Azure 센티널로](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-alien-vault-otx-threat-indicators-into-azure-sentinel/ba-p/1086566) 표시 (블로그)
- [PROOFPOINT ON DEMAND 탭핑 로그를 Azure 센티널로 보내기](https://techcommunity.microsoft.com/t5/azure-sentinel/sending-proofpoint-tap-logs-to-azure-sentinel/ba-p/767727) (블로그)


## <a name="connect-with-powershell"></a>PowerShell로 연결

[AzMonitorLog powershell 스크립트](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) 를 사용 하면 powershell을 사용 하 여 명령줄에서 Azure 센티널로 이벤트 또는 컨텍스트 정보를 스트리밍할 수 있습니다. 이 스트리밍은 데이터 원본과 Azure 센티널 사이에 사용자 지정 커넥터를 효과적으로 만듭니다.

예를 들어 다음 스크립트는 CSV 파일을 Azure 센티널에 업로드 합니다.

``` PowerShell
Import-Csv .\testcsv.csv
| .\Upload-AzMonitorLog.ps1
-WorkspaceId '69f7ec3e-cae3-458d-b4ea-6975385-6e426'
-WorkspaceKey $WSKey
-LogTypeName 'MyNewCSV'
-AddComputerName
-AdditionalDataTaggingName "MyAdditionalField"
-AdditionalDataTaggingValue "Foo"
```

[AzMonitorLog PowerShell 스크립트](https://www.powershellgallery.com/packages/Upload-AzMonitorLog/) 스크립트는 다음 매개 변수를 사용 합니다.

|매개 변수  |Description  |
|---------|---------|
|**WorkspaceId**     |   데이터를 저장할 Azure 센티널 작업 영역 ID입니다.  [작업 영역 ID 및 키를 찾습니다](#find-your-workspace-id-and-key).  |
|**WorkspaceKey**     |   데이터를 저장할 Azure 센티널 작업 영역에 대 한 기본 또는 보조 키입니다. [작업 영역 ID 및 키를 찾습니다](#find-your-workspace-id-and-key).  |
|**LogTypeName**     |    데이터를 저장 하려는 사용자 지정 로그 테이블의 이름입니다. **_CL** 의 접미사가 테이블 이름의 끝에 자동으로 추가 됩니다.  |
|**AddComputerName**     |   이 매개 변수가 있는 경우 스크립트는 **computer** 라는 필드의 모든 로그 레코드에 현재 컴퓨터 이름을 추가 합니다.      |
|**TaggedAzureResourceId**     | 이 매개 변수가 있는 경우 스크립트는 모든 업로드 된 로그 레코드를 지정 된 Azure 리소스와 연결 합니다. <br><br>이 연결은 리소스 컨텍스트 쿼리에 대해 업로드 된 로그 레코드를 사용 하도록 설정 하 고, 리소스 중심의 역할 기반 액세스 제어를 준수 합니다.       |
|**AdditionalDataTaggingName**     |      이 매개 변수가 있는 경우 스크립트는 구성 된 이름과 **AdditionalDataTaggingValue** 매개 변수에 대해 구성 된 값을 사용 하 여 모든 로그 레코드에 다른 필드를 추가 합니다. <br><br>이 경우 **AdditionalDataTaggingValue** 은 비워 둘 수 없습니다. |
|**AdditionalDataTaggingValue**     |   이 매개 변수가 있는 경우 스크립트는 구성 된 값을 사용 하 여 모든 로그 레코드에 다른 필드를 추가 하 고 **AdditionalDataTaggingName** 매개 변수에 대해 구성 된 필드 이름을 추가 합니다. <br><br>**AdditionalDataTaggingName** 매개 변수가 비어 있지만 값이 구성 된 경우 기본 필드 이름은 **DataTagging** 입니다.       |
|     |         |

### <a name="find-your-workspace-id-and-key"></a>작업 영역 ID 및 키 찾기

Azure 센티널에서 **WorkspaceID** 및 **WorkspaceKey** 매개 변수에 대 한 세부 정보를 확인 합니다.

1. Azure 센티널의 왼쪽에서 **설정** 을 선택한 다음 **작업 영역 설정** 탭을 선택 합니다.

1. **Log Analytics**  >  **1 데이터 원본 연결** 시작에서 **Windows 및 Linux 에이전트 관리** 를 선택 합니다.

1. **Windows 서버** 탭에서 작업 영역 ID, 기본 키 및 보조 키를 찾습니다.
## <a name="connect-with-the-log-analytics-api"></a>Log Analytics API를 사용 하 여 연결

Log Analytics 데이터 수집기 API를 사용 하 여 RESTful 끝점을 직접 호출 하 여 Azure 센티널로 이벤트를 스트리밍할 수 있습니다.

RESTful 끝점을 직접 호출 하는 경우 더 많은 프로그래밍이 필요 하지만 유연성이 향상 됩니다.

자세한 내용은 [Log Analytics 데이터 수집기 API](../azure-monitor/logs/data-collector-api.md)를 참조 하세요. 특히 다음 예를 참조 하세요.

- [C#](../azure-monitor/logs/data-collector-api.md#c-sample)
- [Python 2](../azure-monitor/logs/data-collector-api.md#python-2-sample)

## <a name="connect-with-azure-functions"></a>Azure Functions 연결

RESTful API와 함께 Azure Functions를 사용 하 고 [PowerShell](../azure-functions/functions-reference-powershell.md)과 같은 다양 한 코딩 언어를 사용 하 여 서버를 사용 하지 않는 사용자 지정 커넥터를 만듭니다.

이 메서드의 예를 보려면 다음을 참조 하십시오.

- [Azure Function을 사용 하 여 Azure 센티널에 VMware 참조 블랙 Cloud 끝점 표준 연결](connect-vmware-carbon-black.md)
- [Azure Function을 사용 하 여 Azure 센티널에 Okta Single Sign-On 연결](connect-okta-single-sign-on.md)
- [Azure Function을 사용 하 여 Azure 센티널에 Proofpoint on demand 탭 연결](connect-proofpoint-tap.md)
- [Azure 함수를 사용 하 여 Qualys VM을 Azure 센티널에 연결](connect-qualys-vm.md)
- [XML, CSV 또는 다른 형식의 데이터를 수집합니다.](../azure-monitor/logs/create-pipeline-datacollector-api.md#ingesting-xml-csv-or-other-formats-of-data)
- [Azure 센티널로 확대/축소 모니터링](https://techcommunity.microsoft.com/t5/azure-sentinel/monitoring-zoom-with-azure-sentinel/ba-p/1341516) (블로그)
- [Office 365 관리 API 데이터를 Azure 센티널로 가져오기 위한 함수 앱 배포](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/O365%20Data) (Azure 센티널 GitHub 커뮤니티)

## <a name="parse-your-custom-connector-data"></a>사용자 지정 커넥터 데이터 구문 분석

사용자 지정 커넥터의 기본 제공 구문 분석 기법을 사용 하 여 관련 정보를 추출 하 고 Azure 센티널의 관련 필드를 채울 수 있습니다.

다음은 그 예입니다. 

- **Logstash 태를 사용한 경우** [Grok](https://www.elastic.co/guide/en/logstash/current/plugins-filters-grok.html) 필터 플러그 인을 사용 하 여 데이터를 구문 분석 합니다.
- **Azure 함수를 사용한 경우에는** 코드를 사용 하 여 데이터를 구문 분석 합니다. 자세한 내용은 [파서](normalization.md#parsers)를 참조 하세요.

Azure 센티널은 쿼리 시 구문 분석을 지원 합니다. 쿼리 시간에 구문 분석을 사용 하면 데이터를 원래 형식으로 푸시한 다음 필요할 때 필요할 때 구문 분석할 수 있습니다.

쿼리 시간에 구문 분석 하는 것은 사용자 지정 커넥터를 만들 때 또는 추출 해야 하는 정보를 비롯 하 여 데이터의 정확한 구조를 미리 알 필요가 없음을 의미 하기도 합니다. 대신, 조사 중에도 언제 든 지 데이터를 구문 분석 합니다.

> [!NOTE]
> 파서 업데이트는 이미 Azure 센티널에 수집 된 데이터에도 적용 됩니다.
> 
## <a name="next-steps"></a>다음 단계

다음 프로세스 중 하나를 사용 하 여 Azure 센티널에 데이터 수집를 사용 하 여 환경을 보호 합니다.

- [경고 표시](quickstart-get-visibility.md)
- [데이터 시각화 및 모니터링](tutorial-monitor-your-data.md)
- [인시던트 조사](tutorial-investigate-cases.md)
- [위협 감지](tutorial-detect-threats-built-in.md)
- [위협 방지 자동화](tutorial-respond-threats-playbook.md)
- [위협에 대한 헌팅](hunting.md)