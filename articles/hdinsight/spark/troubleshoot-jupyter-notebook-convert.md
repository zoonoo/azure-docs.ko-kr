---
title: Azure HDInsight에서 Jupyter 노트북을 만들 수 없습니다.
description: Azure HDInsight 클러스터와 상호 작용 하는 경우 문제 해결 단계 및 가능한 해결 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/11/2020
ms.openlocfilehash: e4f4fcfa52733ec8f1f0ceedc669b8307efabf3c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324011"
---
# <a name="unable-to-create-jupyter-notebook-in-azure-hdinsight"></a>Azure HDInsight에서 Jupyter 노트북을 만들 수 없습니다.

이 문서에서는 Azure HDInsight 클러스터와 상호 작용할 때 문제에 대 한 문제 해결 단계 및 가능한 해결 방법을 설명 합니다.

## <a name="issue"></a>문제

Jupyter 노트북을 시작 하면 다음을 포함 하는 오류 메시지가 표시 됩니다.

```error
Cannot convert notebook to v5 because that version doesn't exist
```

## <a name="cause"></a>원인

버전이 일치 하지 않습니다.

## <a name="resolution"></a>해결 방법

1. [ssh command](../hdinsight-hadoop-linux-use-ssh-unix.md) 명령을 사용하여 클러스터에 연결합니다. CLUSTERNAME을 클러스터 이름으로 바꿔서 아래 명령을 편집하고, 다음 명령을 입력합니다.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. `_version.py`다음 명령을 실행 하 여를 엽니다.

    ```bash
    sudo nano /usr/bin/anaconda/lib/python2.7/site-packages/nbformat/_version.py
    ```

1. **5** 를 **4** 로 변경 하 여 수정 된 줄이 다음과 같이 표시 되도록 합니다.

    ```python
    version_info = (4, 0, 3)
    ```

    **Ctrl + X**, **Y**, **Enter**를 입력 하 여 변경 내용을 저장 합니다.

1. 웹 브라우저에서 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/JUPYTER`로 이동합니다. 여기서 `CLUSTERNAME`은 클러스터의 이름입니다.

1. Jupyter 서비스를 다시 시작 합니다.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

* [Azure 커뮤니티 지원](https://azure.microsoft.com/support/community/)을 통해 Azure 전문가로부터 답변을 얻습니다.

* [@AzureSupport](https://twitter.com/azuresupport)(고객 환경을 개선하기 위한 공식 Microsoft Azure 계정)에 연결합니다. Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결합니다.

* 도움이 더 필요한 경우 [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)에서 지원 요청을 제출할 수 있습니다. 메뉴 모음에서 **지원**을 선택하거나 **도움말 + 지원** 허브를 엽니다. 자세한 내용은 [Azure 지원 요청을 만드는 방법](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)을 참조하세요. 구독 관리 및 청구 지원에 대한 액세스는 Microsoft Azure 구독에 포함되며 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/) 중 하나를 통해 기술 지원이 제공됩니다.
