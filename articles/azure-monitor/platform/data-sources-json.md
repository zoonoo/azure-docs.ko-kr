---
title: Azure Monitor에서 사용자 지정 JSON 데이터 수집 | Microsoft Docs
description: Linux용 Log Analytics 에이전트를 사용하여 Azure Monitor로 사용자 지정 JSON 데이터 원본을 수집할 수 있습니다.  이러한 사용자 지정 데이터 원본은 curl 또는 FluentD의 300+ 플러그 인의 하나와 같은 JSON을 반환하는 간단한 스크립트일 수 있습니다. 이 문서에서는 이 데이터 수집에 필요한 구성을 설명합니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: 407257dbe9fbfa560153d5044263fc4c947cb05c
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86111935"
---
# <a name="collecting-custom-json-data-sources-with-the-log-analytics-agent-for-linux-in-azure-monitor"></a>Azure Monitor에서 Linux용 Log Analytics 에이전트를 사용하여 사용자 지정 JSON 데이터 원본 수집
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

Linux용 Log Analytics 에이전트를 사용하여 [Azure Monitor](data-platform.md)로 사용자 지정 JSON 데이터 원본을 수집할 수 있습니다.  이러한 사용자 지정 데이터 원본은 [FluentD의 300](https://www.fluentd.org/plugins/all)개 이상의 플러그인과 같은 [JSON을 반환](https://curl.haxx.se/) 하는 간단한 스크립트 일 수 있습니다. 이 문서에서는 이 데이터 수집에 필요한 구성을 설명합니다.


> [!NOTE]
> 사용자 지정 JSON 데이터에 Linux용 Log Analytics 에이전트 v1.1.0-217+가 필요합니다.

## <a name="configuration"></a>Configuration

### <a name="configure-input-plugin"></a>입력 플러그 인 구성

Azure Monitor에서 JSON 데이터를 수집하려면 `oms.api.`를 입력 플러그 인에서 FluentD 태그의 시작 부분에 추가합니다.

예를 들어 다음은 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`에서 별도 구성 파일 `exec-json.conf`입니다.  FluentD 플러그 인 `exec`를 사용하여 30초마다 curl 명령을 실행합니다.  이 명령의 출력은 JSON 출력 플러그 인을 통해 수집됩니다.

```xml
<source>
  type exec
  command 'curl localhost/json.output'
  format json
  tag oms.api.httpresponse
  run_interval 30s
</source>

<match oms.api.httpresponse>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api_httpresponse*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` 아래에 추가된 구성 파일은 다음 명령을 사용하여 변경된 소유권을 필요로 합니다.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>출력 플러그 인 구성 
다음 출력 플러그 인 구성을 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`의 기본 구성에 추가하거나 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`에 배치된 별도 구성 파일로 추가합니다.

```xml
<match oms.api.**>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

### <a name="restart-log-analytics-agent-for-linux"></a>Linux용 Log Analytics 에이전트 다시 시작
다음 명령을 사용하여 Linux용 Log Analytics 에이전트 서비스를 다시 시작합니다.

```console
sudo /opt/microsoft/omsagent/bin/service_control restart 
```

## <a name="output"></a>출력
`<FLUENTD_TAG>_CL`의 레코드 형식으로 Azure Monitor에 데이터가 수집됩니다.

예를 들어 `tomcat_CL`의 레코드 형식으로 Azure Monitor에 사용자 지정 태그 `tag oms.api.tomcat`이 수집됩니다.  다음 로그 쿼리로 이 형식의 모든 레코드를 검색할 수 있습니다.

```console
Type=tomcat_CL
```

중첩된 JSON 데이터 원본이 지원되지만 부모 필드를 기반으로 인덱싱됩니다. 예를 들어 다음 JSON 데이터가 `tag_s : "[{ "a":"1", "b":"2" }]`로 로그 쿼리에서 반환됩니다.

```json
{
    "tag": [{
        "a":"1",
        "b":"2"
    }]
}
```


## <a name="next-steps"></a>다음 단계
* 데이터 원본 및 솔루션에서 수집한 데이터를 분석하는 [로그 쿼리](../log-query/log-query-overview.md)에 대해 알아봅니다. 
