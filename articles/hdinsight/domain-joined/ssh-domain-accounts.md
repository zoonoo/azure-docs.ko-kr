---
title: Azure HDInsight에서 도메인 계정에 대한 SSH 액세스 관리
description: HDInsight에서 Azure AD 계정에 대한 SSH 액세스를 관리하는 단계입니다.
ms.service: hdinsight
ms.topic: how-to
ms.date: 02/14/2020
ms.openlocfilehash: 3fab06f5e269f311f798c096a6465c8c6ce75fc2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946754"
---
# <a name="manage-ssh-access-for-domain-accounts-in-azure-hdinsight"></a>Azure HDInsight에서 도메인 계정에 대한 SSH 액세스 관리

보안 클러스터에서는 기본적으로 [Azure AD DS](../../active-directory-domain-services/overview.md)의 모든 도메인 사용자가 [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)를 통해 헤드 및 에지 노드에 연결할 수 있습니다. 해당 사용자는 sudoers 그룹에 속하지 않으며 루트 액세스 권한이 없습니다. 클러스터를 만드는 동안 생성된 SSH 사용자는 루트 액세스 권한이 있습니다.

## <a name="manage-access"></a>액세스 관리

특정 사용자 또는 그룹에 대한 SSH 액세스를 수정하려면 각 노드에서 `/etc/ssh/sshd_config`를 업데이트합니다.

1. [ssh command](../hdinsight-hadoop-linux-use-ssh-unix.md) 명령을 사용하여 클러스터에 연결합니다. CLUSTERNAME을 클러스터 이름으로 바꿔서 아래 명령을 편집하고, 다음 명령을 입력합니다.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. `ssh_confi`g 파일을 엽니다.

    ```bash
    sudo nano /etc/ssh/sshd_config
    ```

1. `sshd_config` 파일을 원하는 대로 수정합니다. 사용자를 특정 그룹으로 제한하는 경우 로컬 계정은 SSH를 통해 해당 노드에 연결할 수 없습니다. 다음은 구문의 예제입니다.

    ```bash
    AllowUsers useralias1 useralias2

    AllowGroups groupname1 groupname2
    ```

    그런 다음, **Ctrl + X**, **Y**, **Enter** 를 클릭하여 변경 내용을 저장합니다.

1. sshd를 다시 시작합니다.

    ```bash
    sudo systemctl restart sshd
    ```

1. 각 노드에 대해 위의 단계를 반복합니다.

## <a name="ssh-authentication-log"></a>SSH 인증 로그

SSH 인증 로그는 `/var/log/auth.log`에 기록됩니다. 로컬 또는 도메인 계정에 대해 SSH를 통한 로그인 실패가 표시되는 경우 로그를 통해 오류를 디버깅해야 합니다. 이 문제는 특정 사용자 계정과 관련이 있을 수 있으며, 일반적으로 기본 SSH 사용자(로컬 계정)를 사용하여 다른 사용자 계정 또는 SSH를 시도한 다음, kinit를 시도하는 것이 좋습니다.

## <a name="ssh-debug-log"></a>SSH 디버그 로그

자세한 로깅을 사용하도록 설정하려면 `-d` 옵션을 사용하여 `sshd`를 다시 시작해야 합니다. `/usr/sbin/sshd -d`와 마찬가지로, 기본 SSH 디먼을 중지할 필요가 없도록 사용자 지정 포트(예: 2222)에서 `sshd`를 실행할 수도 있습니다. SSH 클라이언트에서 `-v` 옵션을 사용하여 더 많은 로그를 얻을 수도 있습니다(오류에 대한 클라이언트 쪽 보기).

## <a name="next-steps"></a>다음 단계

* [Enterprise Security Package를 사용하여 HDInsight 클러스터 관리](./apache-domain-joined-manage.md)
* [SSH를 사용하여 HDInsight(Apache Hadoop)에 연결합니다](../hdinsight-hadoop-linux-use-ssh-unix.md).
