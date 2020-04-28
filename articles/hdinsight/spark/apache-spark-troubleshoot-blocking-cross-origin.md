---
title: Jupyter 404 오류-"상호 원본 API 차단"-Azure HDInsight
description: Azure HDInsight의 "크로스 원본 API 차단"으로 인해 jupyter 서버 404 "찾을 수 없음" 오류가 발생 함
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: e241657186582955d21981f7dfe18856724aa692
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75894421"
---
# <a name="scenario-jupyter-server-404-not-found-error-due-to-blocking-cross-origin-api-in-azure-hdinsight"></a>시나리오: Azure HDInsight의 "크로스 원본 API 차단"으로 인해 Jupyter 서버 404 "찾을 수 없음" 오류가 발생 했습니다.

이 문서에서는 Azure HDInsight 클러스터에서 Apache Spark 구성 요소를 사용 하는 경우 문제 해결 단계와 가능한 문제 해결 방법을 설명 합니다.

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

- HDInsight 게이트웨이에 표준 `xxx.azurehdinsight.net`이외의 사용자 지정 DNS 이름을 지정 하는 경우

## <a name="resolution"></a>해결 방법

1. 다음 두 위치에서 jupyter.py 파일을 수정 합니다.

    ```bash
    /var/lib/ambari-server/resources/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    /var/lib/ambari-agent/cache/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    ```

1. 표시 되는 `NotebookApp.allow_origin='\"https://{2}.{3}\"'` 줄을 찾은 다음로 변경 `NotebookApp.allow_origin='\"*\"'`합니다.

1. Ambari에서 Jupyter 서비스를 다시 시작 합니다.

1. 명령 `ps aux | grep jupyter` 프롬프트에서를 입력 하면 URL에 연결할 수 있는지를 표시 합니다.

이는 이미 보유 한 설정 보다 보안성이 낮습니다. 그러나 클러스터에 대 한 액세스가 제한 되는 것으로 간주 되며, 내부에서 NSG를 사용 하 여 클러스터에 연결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure [커뮤니티 지원을](https://azure.microsoft.com/support/community/)통해 azure 전문가 로부터 답변을 받으세요.

* 연결 방법 [@AzureSupport](https://twitter.com/azuresupport) -Azure 커뮤니티를 적절 한 리소스 (답변, 지원 및 전문가)에 연결 하 여 고객 환경을 개선 하기 위한 공식 Microsoft Azure 계정입니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원** 을 선택 하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)을 참조 하세요. 구독 관리 및 청구 지원에 대 한 액세스는 Microsoft Azure 구독에 포함 되며, [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 기술 지원이 제공 됩니다.
