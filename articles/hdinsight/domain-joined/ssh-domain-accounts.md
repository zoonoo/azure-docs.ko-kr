---
title: Azure HDInsight에서 도메인 계정에 대 한 SSH 액세스 관리
description: HDInsight에서 Azure AD 계정에 대 한 SSH 액세스를 관리 하는 단계입니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 5529989384df75b592afa8f5e4960eb9817fb2d7
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472519"
---
# <a name="manage-ssh-access-for-domain-accounts-in-azure-hdinsight"></a>Azure HDInsight에서 도메인 계정에 대 한 SSH 액세스 관리

보안 클러스터에서는 기본적으로 [Azure AD DS](../../active-directory-domain-services/overview.md) 의 모든 도메인 사용자가 헤드 및에 지 노드로 [SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) 를 수행할 수 있습니다. 이러한 사용자는 sudoers 그룹에 속하지 않으며 루트 액세스 권한을 얻지 못합니다. 클러스터를 만드는 동안 생성 된 SSH 사용자에 게 루트 액세스 권한이 있습니다.

## <a name="manage-access"></a>액세스 관리

특정 사용자 또는 그룹에 대 한 SSH 액세스를 수정 하려면 각 노드에서 `/etc/ssh/sshd_config`를 업데이트 합니다.

1. [Ssh 명령을](../hdinsight-hadoop-linux-use-ssh-unix.md) 사용 하 여 클러스터에 연결 합니다. CLUSTERNAME을 클러스터의 이름으로 바꿔서 아래 명령을 편집 하 고 명령을 입력 합니다.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. `ssh_confi`g 파일을 엽니다.

    ```bash
    sudo nano /etc/ssh/sshd_config
    ```

1. 필요에 따라 `sshd_config` 파일을 수정 합니다. 사용자를 특정 그룹으로 제한 하면 로컬 계정이 해당 노드에 SSH를 실행할 수 없습니다. 다음은 구문의 예입니다.

    ```bash
    AllowUsers useralias1 useralias2

    AllowGroups groupname1 groupname2
    ```

    그런 다음 변경 내용을 저장 합니다. **Ctrl + X**, **Y**, **Enter**.

1. Sshd를 다시 시작 합니다.

    ```bash
    sudo systemctl restart sshd
    ```

1. 각 노드에 대해 위의 단계를 반복 합니다.

## <a name="ssh-authentication-log"></a>SSH 인증 로그

SSH 인증 로그는 `/var/log/auth.log`에 기록 됩니다. 로컬 또는 도메인 계정에 대 한 SSH를 통해 로그인 실패가 표시 되는 경우 로그를 통해 오류를 디버깅 해야 합니다. 일반적으로이 문제는 특정 사용자 계정과 관련이 있을 수 있으며, 기본 SSH 사용자 (로컬 계정)를 사용 하 여 다른 사용자 계정 또는 SSH를 시도한 후 kinit를 시도 하는 것이 좋습니다.

## <a name="ssh-debug-log"></a>SSH 디버그 로그

자세한 정보 로깅을 사용 하도록 설정 하려면 `-d` 옵션을 사용 하 여 `sshd`를 다시 시작 해야 합니다. `/usr/sbin/sshd -d`와 같이 사용자 지정 포트 (예: 2222)에서 `sshd`를 실행 하 여 주 SSH 디먼을 중지할 필요가 없습니다. SSH 클라이언트와 함께 `-v` 옵션을 사용 하 여 더 많은 로그 (오류에 대 한 클라이언트 쪽 보기)를 가져올 수도 있습니다.

## <a name="next-steps"></a>다음 단계

* [Enterprise Security Package를 사용 하 여 HDInsight 클러스터 관리](./apache-domain-joined-manage.md)
* [SSH를 사용하여 HDInsight(Apache Hadoop)에 연결합니다](../hdinsight-hadoop-linux-use-ssh-unix.md).
