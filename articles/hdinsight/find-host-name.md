---
title: Azure HDInsight 클러스터 노드의 호스트 이름을 가져오는 방법
description: Azure HDInsight 클러스터 노드의 호스트 이름 및 FQDN 이름을 가져오는 방법에 대해 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
author: yanancai
ms.author: yanacai
ms.date: 03/23/2021
ms.openlocfilehash: 676b4ccd52e8a6f1f378756a255ad55b74f18ebe
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105111466"
---
# <a name="find-the-host-names-of-cluster-nodes"></a>클러스터 노드의 호스트 이름 찾기

HDInsight 클러스터는 공용 DNS clustername.azurehdinsight.net를 사용 하 여 만들어집니다. 개별 노드에 SSH를 수행 하거나 동일한 사용자 지정 가상 네트워크에서를 사용 하 여 클러스터 노드에 대 한 연결을 설정 하는 경우 클러스터 노드의 호스트 이름 또는 FQDN (정규화 된 도메인 이름)을 사용 해야 합니다.

이 문서에서는 클러스터 노드의 호스트 이름을 가져오는 방법에 대해 알아봅니다. Ambari 웹 UI를 통해 수동으로 또는 Ambari REST API를 통해 자동으로 가져올 수 있습니다.

> [!WARNING]
> 클러스터 노드의 호스트 이름을 인출 하려면 다음 권장 방법을 사용 하세요. 호스트 이름의 숫자는 순서 대로 보장 되지 않으며, HDInsight는 릴리스 새로 고침이 있는 Vm에 맞게 호스트 이름 형식을 변경할 수 있습니다. 현재 존재 하는 특정 명명 규칙에 대 한 종속성을 사용 하지 마세요. 
>

Ambari UI 또는 Ambari REST API를 통해 호스트 이름을 가져올 수 있습니다. 

## <a name="get-the-host-names-from-ambari-web-ui"></a>Ambari 웹 UI에서 호스트 이름 가져오기
Ambari 웹 UI를 사용 하 여 노드에 SSH 할 때 호스트 이름을 가져올 수 있습니다. Ambari 웹 UI 호스트 보기는의 HDInsight 클러스터에서 사용할 수 있습니다 `https://CLUSTERNAME.azurehdinsight.net/#/main/hosts` . 여기서 `CLUSTERNAME` 은 클러스터의 이름입니다.

![Ambari-UI-이름-UI](.\media\find-host-name\find-host-name-in-ambari-ui.png)

## <a name="get-the-host-names-from-ambari-rest-api"></a>Ambari에서 호스트 이름 가져오기 REST API
자동화 스크립트를 작성 하는 경우 호스트에 연결 하기 전에 Ambari REST API를 사용 하 여 호스트 이름을 가져올 수 있습니다. 호스트 이름의 숫자는 순서 대로 보장 되지 않으며, HDInsight는 릴리스 새로 고침이 있는 Vm에 맞게 호스트 이름 형식을 변경할 수 있습니다. 현재 존재 하는 특정 명명 규칙에 대 한 종속성을 사용 하지 마세요. 

클러스터의 노드에 대 한 FQDN을 검색 하는 방법에 대 한 몇 가지 예는 다음과 같습니다. Ambari REST API에 대 한 자세한 내용은 [Apache Ambari를 사용 하 여 HDInsight 클러스터 관리](.\hdinsight-hadoop-manage-ambari-rest-api.md) 를 참조 하세요 REST API

다음 예제에서는 [jq](https://stedolan.github.io/jq/) 또는 [convertfrom-csv-json](/powershell/module/microsoft.powershell.utility/convertfrom-json) 을 사용 하 여 json 응답 문서를 구문 분석 하 고 호스트 이름만 표시 합니다.

```bash
export password=''
export clusterName=''
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq -r '.items[].Hosts.host_name'
```  

```powershell
$clusterName=''
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```