---
title: Logstash 태를 통해 Azure 센티널에 데이터 원본 연결 | Microsoft Docs
description: Logstash 태를 사용 하 여 외부 데이터 원본에서 Azure 센티널로 로그를 전달 하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/10/2020
ms.author: yelevin
ms.openlocfilehash: 7fe47289dcc6b6d6af4d13b36b5c3b1dae3baaf5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89664224"
---
# <a name="use-logstash-to-connect-data-sources-to-azure-sentinel"></a>Logstash를 사용 하 여 데이터 원본을 Azure 센티널에 연결

> [!IMPORTANT]
> Logstash 출력 플러그 인을 사용 하 여 데이터를 수집 하는 것은 현재 공개 미리 보기 상태입니다. 이 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

**Logstash 데이터 수집 엔진**에 대 한 azure 센티널의 새 출력 플러그 인을 사용 하 여 이제 Logstash 태에서 원하는 모든 유형의 로그를 azure 센티널의 Log Analytics 작업 영역으로 직접 보낼 수 있습니다. 로그는 출력 플러그 인을 사용 하 여 정의 하는 사용자 지정 테이블로 전송 됩니다.

Logstash 데이터 수집 엔진을 사용 하는 방법에 대해 자세히 알아보려면 [Logstash 태 시 시작](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html)하기를 참조 하세요.

## <a name="overview"></a>개요

### <a name="architecture-and-background"></a>아키텍처 및 배경

![로그 스 태 아키텍처의 다이어그램입니다.](./media/connect-logstash/logstash-architecture.png)

Logstash 태 시 엔진은 다음과 같은 세 가지 구성 요소로 구성 됩니다.

- 입력 플러그 인: 다양 한 소스에서 사용자 지정 된 데이터 컬렉션입니다.
- 플러그 인 필터: 지정 된 조건에 따라 데이터를 조작 하 고 정규화 합니다.
- 출력 플러그 인: 수집 되 고 처리 된 데이터를 다양 한 대상으로 사용자 지정 합니다.

> [!NOTE]
> Azure 센티널은 자체 제공 된 출력 플러그 인만 지원 합니다. Azure 센티널의 타사 출력 플러그 인 또는 모든 형식의 다른 Logstash 플러그 인은 지원 하지 않습니다.

Logstash 태 시에 대 한 Azure 센티널 출력 플러그 인은 Log Analytics HTTP 데이터 수집기 REST API를 사용 하 여 JSON 형식 데이터를 Log Analytics 작업 영역에 보냅니다. 데이터는 사용자 지정 로그로 수집 됩니다.

- [Log Analytics REST API](https://docs.microsoft.com/rest/api/loganalytics/create-request)에 대해 자세히 알아보세요.
- [사용자 지정 로그](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-custom-logs)에 대해 자세히 알아보세요.

## <a name="deploy-the-azure-sentinel-output-plugin-in-logstash"></a>Logstash 태 시에 Azure 센티널 출력 플러그 인 배포

### <a name="step-1-installation"></a>1 단계: 설치

Azure 센티널 출력 플러그 인은 Logstash 태 시 컬렉션에서 사용할 수 있습니다.

- Logstash [플러그 인 작업](https://www.elastic.co/guide/en/logstash/current/working-with-plugins.html) 문서의 지침에 따라 ***microsoft logstash 태 시-출력-azure-logstash*** 플러그 인을 설치 합니다.
   
- Logstash 태 시 시스템에서 인터넷에 액세스할 수 없는 경우 Logstash [오프 라인 플러그 인 관리](https://www.elastic.co/guide/en/logstash/current/offline-plugins.html) 문서의 지침에 따라 오프 라인 플러그 인 팩을 준비 하 고 사용 합니다. 이렇게 하려면 인터넷에 액세스할 수 있는 다른 Logstash 태 시 시스템을 구축 해야 합니다.

### <a name="step-2-configuration"></a>2 단계: 구성

구성 파일 문서의 Logstash 태 시 [구조](https://www.elastic.co/guide/en/logstash/current/configuration-file-structure.html) 에 있는 정보를 사용 하 고 다음 키와 값을 사용 하 여 Azure 센티널 출력 플러그 인을 구성에 추가 합니다. 테이블 뒤에 적절 한 구성 파일 구문이 표시 됩니다.

| 필드 이름 | 데이터 형식 | Description |
|----------------|---------------|-----------------|
| `workspace_id` | 문자열 | 작업 영역 ID GUID를 입력 합니다. * |
| `workspace_key` | 문자열 | 작업 영역 기본 키 GUID를 입력 합니다. * |
| `custom_log_table_name` | 문자열 | 로그를 수집 테이블의 이름을 설정 합니다. 출력 플러그 인 당 하나의 테이블 이름만 구성할 수 있습니다. 로그 테이블은 Azure 센티널의 **로그**, **사용자 지정 로그** 범주의 **테이블** 에서 접미사로 표시 됩니다 `_CL` . |
| `endpoint` | 문자열 | 선택적 필드입니다. 기본적으로 Log Analytics 끝점입니다. 이 필드를 사용 하 여 대체 끝점을 설정 합니다. |
| `time_generated_field` | 문자열 | 선택적 필드입니다. 이 속성은 Log Analytics의 기본 **Timegenerated** 필드를 재정의 합니다. 데이터 원본에 타임 스탬프 필드의 이름을 입력 합니다. 필드의 데이터는 ISO 8601 형식 ()을 준수 해야 합니다. `YYYY-MM-DDThh:mm:ssZ` |
| `key_names` | array | Log Analytics 출력 스키마 필드 목록을 입력 합니다. 각 목록 항목은 작은따옴표로 묶고 쉼표로 구분 된 전체 목록으로 묶어야 합니다. 아래 예제를 참조하세요. |
| `plugin_flush_interval` | number | 선택적 필드입니다. Log Analytics 메시지 전송 사이의 최대 간격 (초)을 정의 하려면 설정 합니다. 기본값은 5입니다. |
    | `amount_resizing` | boolean | True 또는 False입니다. 수신 되는 로그 데이터의 양에 따라 메시지 버퍼 크기를 조정 하는 자동 크기 조정 메커니즘을 사용 하거나 사용 하지 않도록 설정 합니다. |
| `max_items` | number | 선택적 필드입니다. `amount_resizing`"False"로 설정 되어 있는 경우에만 적용 됩니다. 를 사용 하 여 메시지 버퍼 크기 (레코드)에 cap를 설정 합니다. 기본값은 2000입니다.  |

\* 작업 영역 리소스의 **에이전트 관리**아래에서 작업 영역 ID 및 기본 키를 찾을 수 있습니다.

#### <a name="sample-configurations"></a>샘플 구성

다음은 몇 가지 다른 옵션을 사용 하는 몇 가지 샘플 구성입니다.

- Filebeat 트 파이프를 사용 하는 기본 구성:

   ```ruby
    input {
        beats {
            port => "5044"
        }
    }
    filter {
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
          workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
          workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
          custom_log_table_name => "tableName"
        }
    }
   ```
    
- Tcp 입력 파이프를 사용 하는 기본 구성:

   ```ruby
    input {
        tcp {
            port => "514"
            type => syslog #optional, will effect log type in table
        }
    }
    filter {
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
          workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
          workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
          custom_log_table_name => "tableName"
        }
    }
   ```
  
- 고급 구성:

   ```ruby    
    input {
        tcp {
            port => 514
            type => syslog
        }
    }
    filter {
        grok {
            match => { "message" => "<%{NUMBER:PRI}>1 (?<TIME_TAG>[0-9]{4}-[0-9]{1,2}-[0-9]{1,2}T[0-9]{1,2}:[0-9]{1,2}:[0-9]{1,2})[^ ]* (?<HOSTNAME>[^ ]*) %{GREEDYDATA:MSG}" }
        }
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
            workspace_id => "<WS_ID>"
            workspace_key => "${WS_KEY}"
            custom_log_table_name => "logstashCustomTable"
            key_names => ['PRI','TIME_TAG','HOSTNAME','MSG']
            plugin_flush_interval => 5
        }
    } 
   ```

   > [!NOTE]
   > 출력 플러그 인 [GitHub 리포지토리](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/microsoft-logstash-output-azure-loganalytics) 를 방문 하 여 내부 작동, 구성 및 성능 설정에 대해 자세히 알아보세요.

### <a name="step-3-restart-logstash"></a>3 단계: Logstash 태 시 다시 시작

### <a name="step-4-view-incoming-logs-in-azure-sentinel"></a>4 단계: Azure 센티널에서 들어오는 로그 보기

1. 메시지가 출력 플러그 인으로 전송 되 고 있는지 확인 합니다.

1. Azure 센티널 탐색 메뉴에서 **로그**를 클릭 합니다. **테이블** 머리글 아래에서 **사용자 지정 로그** 범주를 확장 합니다. 구성에서 접미사를 사용 하 여 지정한 테이블의 이름을 찾아 클릭 합니다 `_CL` .

   :::image type="content" source="./media/connect-logstash/logstash-custom-logs-menu.png" alt-text="로그 스 태 시 사용자 지정 로그의 스크린샷":::

1. 테이블의 레코드를 보려면 테이블 이름을 스키마로 사용 하 여 테이블을 쿼리 합니다.

   :::image type="content" source="./media/connect-logstash/logstash-custom-logs-query.png" alt-text="로그 스 태 시 사용자 지정 로그의 스크린샷":::

## <a name="monitor-output-plugin-audit-logs"></a>출력 플러그 인 감사 로그 모니터링

Azure 센티널 출력 플러그 인의 연결 및 작업을 모니터링 하려면 적절 한 Logstash 태 시 로그 파일을 사용 하도록 설정 합니다. 로그 파일 위치는 [Logstash 디렉터리 레이아웃](https://www.elastic.co/guide/en/logstash/current/dir-layout.html#dir-layout) 문서를 참조 하세요.

이 로그 파일에 데이터가 표시 되지 않는 경우에는 입력 및 필터 플러그 인을 통해 일부 이벤트를 생성 하 여 로컬로 전송 하 여 출력 플러그 인에서 데이터를 수신 하 고 있는지 확인 합니다. Azure 센티널은 출력 플러그 인과 관련 된 문제만 지원 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Logstash 태를 사용 하 여 외부 데이터 원본을 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [기본 제공](tutorial-detect-threats-built-in.md) 또는 [사용자 지정](tutorial-detect-threats-custom.md) 규칙을 사용 하 여 Azure 센티널에서 위협을 검색 하기 시작 합니다.
