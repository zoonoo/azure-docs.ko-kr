---
title: Azure HDInsight 클러스터에 대 한 OS 패치 일정 구성
description: Linux 기반 HDInsight 클러스터의 OS 패치 일정을 구성하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/21/2020
ms.openlocfilehash: bf22e20a6c6187677f000e0c50ac64582233c3cd
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90019668"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Linux 기반 HDInsight 클러스터에 대 한 OS 패치 일정 구성

> [!IMPORTANT]
> Ubuntu 이미지는 게시 되는 3 개월 이내에 새 Azure HDInsight 클러스터를 만드는 데 사용할 수 있게 됩니다. 1 월 2019을 기반으로 실행 중인 클러스터는 자동으로 패치가 적용 되지 않습니다. 고객이 스크립트 동작이나 기타 메커니즘을 사용하여 실행 중인 클러스터를 패치해야 합니다. 항상 새로 만든 클러스터에는 최신 보안 패치를 포함한 사용 가능한 최신 업데이트가 있습니다.

HDInsight는 OS 패치, 보안 업데이트 및 다시 부팅 노드 설치와 같은 클러스터에서 일반적인 작업을 수행할 수 있도록 지원 합니다. 이러한 작업은 [스크립트 동작](hdinsight-hadoop-customize-cluster-linux.md)으로 실행 하 고 매개 변수로 구성할 수 있는 다음 두 스크립트를 사용 하 여 수행 됩니다.

- `schedule-reboots.sh` -즉시 다시 시작 하거나 클러스터 노드에서 다시 시작을 예약 합니다.
- `install-updates-schedule-reboots.sh` -모든 업데이트, 커널 + 보안 업데이트만 또는 커널 업데이트만 설치 합니다.

> [!NOTE]  
> 스크립트 작업은 이후의 모든 업데이트 주기에 대해 업데이트를 자동으로 적용 하지 않습니다. 업데이트를 설치 하기 위해 새 업데이트를 적용 해야 할 때마다 스크립트를 실행 하 고 VM을 다시 시작 합니다.

## <a name="preparation"></a>준비

프로덕션 환경에 배포 하기 전에 대표적인 비프로덕션 환경에 패치를 적용할 수 있습니다. 실제 패치 적용 전에 시스템을 적절 하 게 테스트 하기 위한 계획을 개발 합니다.

클러스터와의 ssh 세션에서 시간에 이르기까지 보안 업데이트를 사용할 수 있다는 메시지가 표시 될 수 있습니다. 메시지는 다음과 유사 하 게 표시 될 수 있습니다.

```
89 packages can be updated.
82 updates are security updates.

*** System restart required ***

Welcome to Spark on HDInsight.

```

패치는 선택 사항이 며 사용자의 판단에 따라 결정 됩니다.

## <a name="restart-nodes"></a>노드 다시 시작
  
스크립트 [일정-다시 부팅](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh)은 클러스터의 컴퓨터에서 수행 되는 다시 부팅 유형을 설정 합니다. 스크립트 동작을 제출할 때 헤드 노드, 작업자 노드 및 사육 사의 세 가지 노드 형식 모두에 적용 되도록 설정 합니다. 스크립트를 노드 형식에 적용 하지 않으면 해당 노드 형식에 대 한 Vm이 업데이트 되거나 다시 시작 되지 않습니다.

는 `schedule-reboots script` 하나의 숫자 매개 변수를 허용 합니다.

| 매개 변수 | 허용되는 값 | 정의 |
| --- | --- | --- |
| 수행할 다시 시작 유형 | 1 또는 2 | 값 1을 사용 하면 일정을 다시 시작할 수 있습니다 (12-24 시간으로 예약 됨). 값을 2로 설정 하면 즉시 다시 시작할 수 있습니다 (5 분 이내). 매개 변수를 지정 하지 않으면 기본값은 1입니다. |  

## <a name="install-updates-and-restart-nodes"></a>업데이트 설치 및 노드 다시 시작

스크립트 [install-updates-schedule-reboots.sh](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh) 는 여러 유형의 업데이트를 설치 하 고 VM을 다시 시작 하는 옵션을 제공 합니다.

`install-updates-schedule-reboots`스크립트는 다음 표에 설명 된 대로 두 개의 숫자 매개 변수를 허용 합니다.

| 매개 변수 | 허용되는 값 | 정의 |
| --- | --- | --- |
| 설치할 업데이트 유형 | 0, 1 또는 2 | 값 0은 커널 업데이트만 설치 합니다. 값 1은 모든 업데이트를 설치 하 고, 2는 커널 + 보안 업데이트만 설치 합니다. 매개 변수를 제공 하지 않으면 기본값은 0입니다. |
| 수행할 다시 시작 유형 | 0, 1 또는 2 | 0 값은 다시 시작을 사용 하지 않습니다. 값 1은 일정 다시 시작을 사용 하도록 설정 하 고 2는 즉시 다시 시작을 사용 하도록 설정 합니다. 매개 변수를 제공 하지 않으면 기본값은 0입니다. 사용자는 입력 매개 변수 1을 입력 매개 변수 2로 변경 해야 합니다. |

> [!NOTE]
> 기존 클러스터에 적용 한 후에는 스크립트를 지속형으로 표시 해야 합니다. 그렇지 않은 경우 크기 조정 작업을 통해 만들어진 모든 새 노드는 기본 패치 일정을 사용합니다. 클러스터 생성 프로세스의 일부로 스크립트를 적용 하면 자동으로 유지 됩니다.

> [!NOTE]
> 예약 된 다시 시작 옵션은 12 시간에서 24 시간 동안 패치 된 클러스터 노드의 자동 롤링 다시 시작을 수행 하 고 고가용성, 업데이트 도메인 및 장애 도메인 고려 사항을 고려 합니다. 예약 된 다시 시작은 실행 중인 작업을 종료 하지 않지만 노드를 사용할 수 없게 되 면 일시적으로 클러스터 용량이 부족 하 여 처리 시간이 길어질 수 있습니다. 

## <a name="next-steps"></a>다음 단계

스크립트 동작 사용에 대 한 특정 단계는 [스크립트 작업을 사용 하 여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)의 다음 섹션을 참조 하세요.

- [클러스터를 만드는 동안 스크립트 작업 사용](hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation)
- [실행 중인 클러스터에 스크립트 작업 적용](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
