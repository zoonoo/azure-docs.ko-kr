---
title: Jupyter 404 오류 - "원본 간 API 차단" - Azure HDInsight
description: Azure HDInsight에서 "원본 간 API 차단"으로 인해 Jupyter 서버 404 "찾을 수 없음" 오류가 발생함
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: dc64a375f9010e48403493c1b13efa1e4f551bce
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112286816"
---
# <a name="scenario-jupyter-server-404-not-found-error-due-to-blocking-cross-origin-api-in-azure-hdinsight"></a>시나리오: Azure HDInsight의 "원본 간 API 차단"으로 인해 Jupyter 서버 404 "찾을 수 없음" 오류가 발생함

이 문서에서는 Azure HDInsight 클러스터에서 Apache Spark 구성 요소를 사용할 때 발생하는 문제 해결 단계와 가능한 문제 해결 방법을 설명합니다.

## <a name="issue"></a>문제

HDInsight에서 Jupyter 서비스에 액세스하면 "찾을 수 없음"이라는 오류 상자가 표시됩니다. Jupyter 로그를 확인하면 다음과 같은 내용이 표시됩니다.

```log
[W 2018-08-21 17:43:33.352 NotebookApp] 404 PUT /api/contents/PySpark/notebook.ipynb (10.16.0.144) 4504.03ms referer=https://pnhr01hdi-corpdir.msappproxy.net/jupyter/notebooks/PySpark/notebook.ipynb
Blocking Cross Origin API request.  
Origin: https://xxx.xxx.xxx, Host: pnhr01.j101qxjrl4zebmhb0vmhg044xe.ax.internal.cloudapp.net:8001
```

Jupyter 로그의 "원본" 필드에 IP 주소도 표시될 수 있습니다.

## <a name="cause"></a>원인

이 오류는 다음과 같은 몇 가지 원인으로 인해 발생할 수 있습니다.

- 클러스터에 대한 액세스를 제한하는 NSG(네트워크 보안 그룹) 규칙을 구성한 경우 NSG 규칙을 사용하여 액세스를 제한하면 클러스터 이름 대신 IP 주소를 사용하여 Apache Ambari 및 기타 서비스에 직접 액세스할 수 있습니다. 그러나 Jupyter에 액세스할 때 404 "찾을 수 없음" 오류가 표시될 수 있습니다.

- HDInsight 게이트웨이에 표준 `xxx.azurehdinsight.net` 이외의 사용자 지정된 DNS 이름을 지정한 경우

## <a name="resolution"></a>해결 방법

1. 다음 두 위치에서 jupyter.py 파일을 수정합니다.

    ```bash
    /var/lib/ambari-server/resources/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    /var/lib/ambari-agent/cache/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    ```

1. `NotebookApp.allow_origin='\"https://{2}.{3}\"'` 줄을 찾은 다음, `NotebookApp.allow_origin='\"*\"'`으로 변경합니다.

1. Ambari에서 Jupyter 서비스를 다시 시작합니다.

1. 명령 프롬프트에서 `ps aux | grep jupyter`를 입력하면 모든 URL에서 연결할 수 있는 것으로 표시됩니다.

이 방식은 이미 지정한 설정보다 덜 안전합니다. 그러나 클러스터에 대한 액세스가 제한되는 것으로 간주되며, NSG가 이미 있으므로 외부에서 클러스터에 연결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]