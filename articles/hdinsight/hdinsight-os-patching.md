---
title: Azure HDInsight 클러스터에 대한 OS 패치 일정 구성
description: Linux 기반 HDInsight 클러스터의 OS 패치 일정을 구성하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/21/2020
ms.openlocfilehash: f8e694f658d6e9de04c92001214ecd5c32ff7753
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206863"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Linux 기반 HDInsight 클러스터에 대한 OS 패치 일정 구성

> [!IMPORTANT]
> 우분투 이미지는 게시 된 후 3 개월 이내에 새로운 Azure HDInsight 클러스터 생성에 사용할 수 있게 됩니다. 2019년 1월부터 실행 중인 클러스터는 자동 패치되지 않습니다. 고객이 스크립트 동작이나 기타 메커니즘을 사용하여 실행 중인 클러스터를 패치해야 합니다. 항상 새로 만든 클러스터에는 최신 보안 패치를 포함한 사용 가능한 최신 업데이트가 있습니다.

HDInsight는 OS 패치 설치, 보안 업데이트 및 노드 재부팅과 같은 일반적인 작업을 클러스터에서 수행할 수 있도록 지원합니다. 이러한 작업은 [스크립트 작업으로](hdinsight-hadoop-customize-cluster-linux.md)실행하고 매개 변수로 구성할 수 있는 다음 두 스크립트를 사용하여 수행됩니다.

- `schedule-reboots.sh`- 즉시 다시 시작하거나 클러스터 노드에서 다시 시작을 예약합니다.
- `install-updates-schedule-reboots.sh`- 모든 업데이트, 커널 + 보안 업데이트 또는 커널 업데이트만 설치합니다.

> [!NOTE]  
> 스크립트 작업은 이후의 모든 업데이트 주기에 대한 업데이트를 자동으로 적용하지 않습니다. 업데이트를 설치하기 위해 새 업데이트를 적용해야 할 때마다 스크립트를 실행한 다음 VM을 다시 시작합니다.

## <a name="preparation"></a>준비

프로덕션 환경에 배포하기 전에 대표적인 비프로덕션 환경에 패치합니다. 실제 패치를 하기 전에 시스템을 적절하게 테스트할 계획을 수립합니다.

클러스터가 있는 ssh 세션에서 수시로 업그레이드를 사용할 수 있다는 메시지가 나타날 수 있습니다. 메시지는 다음과 같이 보일 수 있습니다.

```
New release '18.04.3 LTS' available.
Run 'do-release-upgrade' to upgrade it
```

패치는 선택 사항이며 사용자의 재량에 따라 선택사항입니다.

## <a name="restart-nodes"></a>노드 다시 시작
  
스크립트 [일정-재부팅은](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh)클러스터의 컴퓨터에서 수행될 재부팅 유형을 설정합니다. 스크립트 작업을 제출할 때 헤드 노드, 작업자 노드 및 사육사의 세 가지 노드 유형 모두에 적용하도록 설정합니다. 스크립트가 노드 유형에 적용되지 않으면 해당 노드 유형에 대한 VM이 업데이트되거나 다시 시작되지 않습니다.

는 `schedule-reboots script` 하나의 숫자 매개 변수를 허용합니다.

| 매개 변수 | 허용되는 값 | 정의 |
| --- | --- | --- |
| 수행을 위한 다시 시작 유형 | 1 또는 2 | 값이 1이면 일정을 다시 시작할 수 있습니다(12-24시간 으로 예약). 값이 2이면 즉시 다시 시작할 수 있습니다(5분). 매개 변수가 지정되지 않으면 기본값은 1입니다. |  

## <a name="install-updates-and-restart-nodes"></a>업데이트 설치 및 노드 다시 시작

스크립트 [install-updates-schedule-reboots.sh](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh) 다양한 유형의 업데이트를 설치하고 VM을 다시 시작하는 옵션을 제공합니다.

스크립트는 `install-updates-schedule-reboots` 다음 표에 설명된 대로 두 개의 숫자 매개 변수를 허용합니다.

| 매개 변수 | 허용되는 값 | 정의 |
| --- | --- | --- |
| 설치할 업데이트 유형 | 0, 1 또는 2 | 값이 0이면 커널 업데이트만 설치됩니다. 값이 1이면 모든 업데이트가 설치되고 2는 커널 + 보안 업데이트만 설치합니다. 매개 변수가 제공되지 않으면 기본값은 0입니다. |
| 수행을 위한 다시 시작 유형 | 0, 1 또는 2 | 값이 0이면 다시 시작되지 않습니다. 값이 1이면 일정을 다시 시작할 수 있고 2는 즉시 다시 시작할 수 있습니다. 매개 변수가 제공되지 않으면 기본값은 0입니다. 사용자는 입력 매개 변수 1을 입력 매개 변수 2로 변경해야 합니다. |

> [!NOTE]
> 기존 클러스터에 적용한 후 스크립트를 유지됨으로 표시해야 합니다. 그렇지 않은 경우 크기 조정 작업을 통해 만들어진 모든 새 노드는 기본 패치 일정을 사용합니다. 클러스터 만들기 프로세스의 일부로 스크립트를 적용하면 스크립트가 자동으로 유지됩니다.

## <a name="next-steps"></a>다음 단계

스크립트 작업 사용에 대한 구체적인 단계는 [스크립트 작업을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정의](hdinsight-hadoop-customize-cluster-linux.md)다음 섹션을 참조하십시오.

- [클러스터를 만드는 동안 스크립트 작업 사용](hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation)
- [실행 중인 클러스터에 스크립트 작업 적용](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
