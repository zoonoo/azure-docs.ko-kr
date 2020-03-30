---
title: Jupyter 404 오류 - "교차 원산지 API 차단" - Azure HDInsight
description: Azure HDInsight의 "교차 오리진 API 차단"으로 인해 Jupyter 서버 404 "찾을 수 없습니다" 오류
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: e241657186582955d21981f7dfe18856724aa692
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894421"
---
# <a name="scenario-jupyter-server-404-not-found-error-due-to-blocking-cross-origin-api-in-azure-hdinsight"></a>시나리오: Azure HDInsight의 "교차 오리진 API 차단"으로 인해 Jupyter 서버 404 "찾을 수 없습니다" 오류

이 문서에서는 Azure HDInsight 클러스터에서 아파치 스파크 구성 요소를 사용할 때 문제에 대한 문제 해결 단계 및 가능한 해결 에 대해 설명합니다.

## <a name="issue"></a>문제

HDInsight에서 Jupyter 서비스에 액세스하면 "찾을 수 없습니다"라는 오류 상자가 표시됩니다. Jupyter 로그를 확인하면 다음과 같은 것을 볼 수 있습니다.

```log
[W 2018-08-21 17:43:33.352 NotebookApp] 404 PUT /api/contents/PySpark/notebook.ipynb (10.16.0.144) 4504.03ms referer=https://pnhr01hdi-corpdir.msappproxy.net/jupyter/notebooks/PySpark/notebook.ipynb
Blocking Cross Origin API request.  
Origin: https://xxx.xxx.xxx, Host: pnhr01.j101qxjrl4zebmhb0vmhg044xe.ax.internal.cloudapp.net:8001
```

Jupyter 로그의 "원본" 필드에 IP 주소가 표시될 수도 있습니다.

## <a name="cause"></a>원인

이 오류는 몇 가지 로 인해 발생할 수 있습니다.

- NSG(네트워크 보안 그룹) 규칙을 구성하여 클러스터에 대한 액세스를 제한하는 경우 NSG 규칙으로 액세스를 제한하면 클러스터 이름이 아닌 IP 주소를 사용하여 아파치 암바리 및 기타 서비스에 직접 액세스할 수 있습니다. 그러나 Jupyter에 액세스할 때 404 "찾을 수 없습니다" 오류가 표시 될 수 있습니다.

- HDInsight 게이트웨이에 표준 `xxx.azurehdinsight.net`이외의 사용자 지정 DNS 이름을 지정한 경우.

## <a name="resolution"></a>해결 방법

1. 다음 두 위치에서 jupyter.py 파일을 수정합니다.

    ```bash
    /var/lib/ambari-server/resources/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    /var/lib/ambari-agent/cache/common-services/JUPYTER/1.0.0/package/scripts/jupyter.py
    ```

1. 다음과 같은 줄을 `NotebookApp.allow_origin='\"https://{2}.{3}\"'` `NotebookApp.allow_origin='\"*\"'`찾으십시오.

1. 암바리에서 주피터 서비스를 다시 시작합니다.

1. 명령 `ps aux | grep jupyter` 프롬프트를 입력하면 URL에 연결할 수 있음을 보여 줘야 합니다.

이것은 우리가 이미 장소에 있던 설정 보다 덜 안전. 그러나 클러스터에 대한 액세스가 제한되어 있고 NSG가 있으므로 외부에서 클러스터에 연결할 수 있다고 가정합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* Azure 커뮤니티 지원을 통해 Azure 전문가의 답변을 얻을 [수 있습니다.](https://azure.microsoft.com/support/community/)

* 연결 [@AzureSupport](https://twitter.com/azuresupport) - Azure 커뮤니티를 올바른 리소스(답변, 지원 및 전문가)에 연결하여 고객 경험을 개선하기 위한 공식 Microsoft Azure 계정과 연결합니다.

* 추가 도움이 필요한 경우 [Azure 포털](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원을** 선택하거나 도움말 + 지원 허브를 **엽니다.** 자세한 내용은 [Azure 지원 요청을 만드는 방법을](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)검토하십시오. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 기술 지원은 [Azure 지원 계획](https://azure.microsoft.com/support/plans/)중 하나를 통해 제공됩니다.
