---
title: Jupyter 404 오류-"상호 원본 API 차단"-Azure HDInsight
description: Azure HDInsight의 "크로스 원본 API 차단"으로 인해 jupyter 서버 404 "찾을 수 없음" 오류가 발생 함
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: f2000c3938e7366dd85f36b8de9a8425e91fab8a
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287939"
---
# <a name="scenario-jupyter-server-404-not-found-error-due-to-blocking-cross-origin-api-in-azure-hdinsight"></a>시나리오: Azure HDInsight의 "크로스 원본 API 차단"으로 인해 Jupyter 서버 404 "찾을 수 없음" 오류가 발생 했습니다.

이 문서에서는 Azure HDInsight 클러스터에서 Apache Spark 구성 요소를 사용할 때 발생하는 문제 해결 단계와 가능한 문제 해결 방법을 설명합니다.

## <a name="issue"></a>문제

HDInsight에서 Jupyter 서비스에 액세스 하면 "찾을 수 없음" 이라는 오류 상자가 표시 됩니다. Jupyter 로그를 확인 하면 다음과 같은 내용이 표시 됩니다.

```log
[W 2018-08-21 17:43:33.352 NotebookApp] 404 PUT /api/contents/PySpark/notebook.ipynb (10.16.0.144) 4504.03ms referer=https://pnhr01hdi-corpdir.msappproxy.net/jupyter/notebooks/PySpark/notebook.ipynb
Blocking Cross Origin API request.  
Origin: https://xxx.xxx.xxx, Host: pnhr01.j101qxjrl4zebmhb0vmhg044xe.ax.internal.cloudapp.net:8001
```

Jupyter 로그의 "원본" 필드에 IP 주소도 표시 될 수도 있습니다.

## <a name="cause"></a>원인

이 오류는 다음과 같은 몇 가지 이유로 인해 발생할 수 있습니다.

- 클러스터에 대 한 액세스를 제한 하는 NSG (네트워크 보안 그룹) 규칙을 구성한 경우 NSG 규칙을 사용 하 여 액세스를 제한 하면 클러스터 이름 대신 IP 주소를 사용 하 여 Apache Ambari 및 기타 서비스에 직접 액세스할 수 있습니다. 그러나 Jupyter에 액세스할 때 404 "찾을 수 없음" 오류가 표시 될 수 있습니다.

- HDInsight 게이트웨이에 표준 이외의 사용자 지정 DNS 이름을 지정 하는 경우 `xxx.azurehdinsight.net`

## <a name="resolution"></a>해결 방법

1. 다음 두 위치에서 jupyter.py 파일을 수정 합니다.

    ```bash
    /var/lib/ambari-server/resources/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    /var/lib/ambari-agent/cache/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    ```

1. 표시 되는 줄을 찾은 다음 `NotebookApp.allow_origin='\"https://{2}.{3}\"'` 로 변경 합니다. `NotebookApp.allow_origin='\"*\"'`

1. Ambari에서 Jupyter 서비스를 다시 시작 합니다.

1. `ps aux | grep jupyter`명령 프롬프트에서를 입력 하면 URL에 연결할 수 있는지를 표시 합니다.

이는 이미 보유 한 설정 보다 보안성이 낮습니다. 그러나 클러스터에 대 한 액세스가 제한 되는 것으로 간주 되며, 내부에서 NSG를 사용 하 여 클러스터에 연결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]