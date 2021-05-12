---
title: Azure HDInsight 클러스터 노드의 호스트 이름 가져오는 방법
description: Azure HDInsight 클러스터 노드의 호스트 이름과 FQDN 이름을 가져오는 방법에 대해 알아봅니다.
ms.service: hdinsight
ms.topic: how-to
author: yanancai
ms.author: yanacai
ms.date: 03/23/2021
ms.openlocfilehash: 676b4ccd52e8a6f1f378756a255ad55b74f18ebe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105111466"
---
# <a name="find-the-host-names-of-cluster-nodes"></a>클러스터 노드의 호스트 이름 찾기

HDInsight 클러스터는 퍼블릭 DNS clustername.azurehdinsight.net을 사용하여 만들어집니다. 개별 노드에 SSH를 수행하거나 동일한 사용자 지정 가상 네트워크 내에서 클러스터 노드에 연결을 설정할 경우 클러스터 노드의 호스트 이름 또는 FQDN(정규화된 도메인 이름)을 사용해야 합니다.

이 문서에서는 클러스터 노드의 호스트 이름을 가져오는 방법에 대해 알아봅니다. Ambari 웹 UI를 통해 수동으로 가져오거나 Ambari REST API를 통해 자동으로 가져올 수 있습니다.

> [!WARNING]
> 클러스터 노드의 호스트 이름을 가져오려면 다음과 같은 권장된 방법을 사용하세요. 호스트 이름의 숫자는 순서대로 보장되지 않으며 HDInsight는 릴리스 새로 고침을 사용하여 VM에 맞게 호스트 이름 형식을 변경할 수 있습니다. 현재 존재하는 특정 명명 규칙에 대한 종속성을 사용하지 마세요. 
>

Ambari UI 또는 Ambari REST API를 통해 호스트 이름을 가져올 수 있습니다. 

## <a name="get-the-host-names-from-ambari-web-ui"></a>Ambari 웹 UI에서 호스트 이름 가져오기
노드에 SSH를 수행할 때 Ambari 웹 UI를 사용하여 호스트 이름을 가져올 수 있습니다. Ambari 웹 UI 호스트 보기는 `https://CLUSTERNAME.azurehdinsight.net/#/main/hosts`의 HDInsight 클러스터에서 사용할 수 있습니다. 여기서 `CLUSTERNAME`은 클러스터 이름입니다.

![Ambari UI에서 호스트 이름 가져오기](.\media\find-host-name\find-host-name-in-ambari-ui.png)

## <a name="get-the-host-names-from-ambari-rest-api"></a>Ambari REST API에서 호스트 이름 가져오기
자동화 스크립트를 빌드할 때 호스트에 연결하기 전에 Ambari REST API를 사용하여 호스트 이름을 가져올 수 있습니다. 호스트 이름의 숫자는 순서대로 보장되지 않으며 HDInsight는 릴리스 새로 고침을 사용하여 VM에 맞게 호스트 이름 형식을 변경할 수 있습니다. 현재 존재하는 특정 명명 규칙에 대한 종속성을 사용하지 마세요. 

다음은 클러스터의 노드에 대한 FQDN을 검색하는 몇 가지 예시입니다. Ambari REST API에 대한 자세한 내용은 [Apache Ambari REST API를 사용하여 HDInsight 클러스터 관리](.\hdinsight-hadoop-manage-ambari-rest-api.md)를 참조

다음 예제에서는 [jq](https://stedolan.github.io/jq/) 또는 [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json)을 사용하여 JSON 응답 문서를 구문 분석하고 호스트 이름만 표시합니다.

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