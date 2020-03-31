---
title: Azure HDInsight에서 도메인 계정에 대한 SSH 액세스 관리
description: HDInsight에서 Azure AD 계정에 대한 SSH 액세스를 관리하는 단계입니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 5529989384df75b592afa8f5e4960eb9817fb2d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472519"
---
# <a name="manage-ssh-access-for-domain-accounts-in-azure-hdinsight"></a>Azure HDInsight에서 도메인 계정에 대한 SSH 액세스 관리

보안 클러스터에서는 기본적으로 [Azure AD DS의](../../active-directory-domain-services/overview.md) 모든 도메인 사용자가 헤드 노드 및 에지 노드로 [SSH를](../hdinsight-hadoop-linux-use-ssh-unix.md) 허용할 수 있습니다. 이러한 사용자는 sudoers 그룹의 일부가 아니며 루트 액세스 권한을 얻지 못합니다. 클러스터 를 만드는 동안 만든 SSH 사용자는 루트 액세스 권한을 갖습니다.

## <a name="manage-access"></a>액세스 관리

특정 사용자 또는 그룹에 대한 SSH `/etc/ssh/sshd_config` 액세스를 수정하려면 각 노드를 업데이트합니다.

1. [ssh 명령을](../hdinsight-hadoop-linux-use-ssh-unix.md) 사용하여 클러스터에 연결합니다. CLUSTERNAME을 클러스터 이름으로 바꿉니다 아래 명령을 편집한 다음 명령을 입력합니다.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. `ssh_confi`g 파일을 엽니다.

    ```bash
    sudo nano /etc/ssh/sshd_config
    ```

1. 원하는 `sshd_config` 대로 파일을 수정합니다. 사용자를 특정 그룹으로 제한하면 로컬 계정이 해당 노드로 SSH를 사용할 수 없습니다. 다음은 구문의 예일 뿐입니다.

    ```bash
    AllowUsers useralias1 useralias2

    AllowGroups groupname1 groupname2
    ```

    그런 다음 변경 내용 저장: **Ctrl + X**, **Y**, **Enter**.

1. sshd를 다시 시작합니다.

    ```bash
    sudo systemctl restart sshd
    ```

1. 각 노드에 대해 위의 단계를 반복합니다.

## <a name="ssh-authentication-log"></a>SSH 인증 로그

SSH 인증 로그가 `/var/log/auth.log`에 기록됩니다. 로컬 또는 도메인 계정에 대한 SSH를 통해 로그인 오류가 표시되면 로그를 통해 오류를 디버깅해야 합니다. 종종 문제가 특정 사용자 계정과 관련될 수 있으며 일반적으로 기본 SSH 사용자(로컬 계정)를 사용하여 다른 사용자 계정이나 SSH를 시도한 다음 kinit을 시도하는 것이 좋습니다.

## <a name="ssh-debug-log"></a>SSH 디버그 로그

자세한 로깅을 사용하려면 `sshd` `-d` 옵션으로 다시 시작해야 합니다. 마찬가지로 `/usr/sbin/sshd -d` 사용자 지정 `sshd` 포트(예: 2222)에서 실행할 수 있으므로 주 SSH 데몬을 중지할 필요가 없습니다. SSH 클라이언트에서 옵션을 사용하여 `-v` 더 많은 로그(오류의 클라이언트 측 보기)를 얻을 수도 있습니다.

## <a name="next-steps"></a>다음 단계

* [Enterprise Security Package를 사용하여 HDInsight 클러스터 관리](./apache-domain-joined-manage.md)
* [SSH를 사용하여 HDInsight (아파치 하두롭)에 연결합니다.](../hdinsight-hadoop-linux-use-ssh-unix.md)
