---
title: Linux 기반 HDInsight 클러스터의 OS 패치 일정 구성 - Azure
description: Linux 기반 HDInsight 클러스터의 OS 패치 일정을 구성하는 방법에 대해 알아봅니다.
author: omidm1
ms.author: omidm
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: cfbd68e66730fc338130bc16849fe0b2f4abd6be
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244419"
---
# <a name="os-patching-for-hdinsight"></a>HDInsight의 OS 패치 

> [!IMPORTANT]
> Ubuntu 이미지는 게시된 날부터 3개월 이내에 새 HDInsight 클러스터를 만드는 데 사용할 수 있게 됩니다. 2019년 1월부터 실행 중인 클러스터는 자동 패치되지 **않습니다**. 고객이 스크립트 동작이나 기타 메커니즘을 사용하여 실행 중인 클러스터를 패치해야 합니다. 항상 새로 만든 클러스터에는 최신 보안 패치를 포함한 사용 가능한 최신 업데이트가 있습니다.

## <a name="how-to-configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Linux 기반 HDInsight 클러스터의 OS 패치 일정을 구성하는 방법
중요한 보안 패치를 설치할 수 있도록 간혹 HDInsight 클러스터에서 가상 머신을 다시 부팅해야 합니다. 

이 문서에서 설명한 스크립트 작업을 사용하여 다음과 같이 OS 패치 일정을 수정할 수 있습니다.
1. 전체 OS 업데이트를 설치 하거나 보안 업데이트만 설치
2. VM을 다시 부팅

> [!NOTE]  
> 2016년 8월 1일 이후에 만들어진 Linux 기반 HDInsight 클러스터에서만 이 스크립트 작업이 작동합니다. VM이 다시 부팅되는 경우에만 패치가 적용됩니다. 이 스크립트는 모든 향후 업데이트 주기에 대 한 업데이트를 자동으로 적용 되지 않습니다. 각 시간 새 업데이트에서 업데이트를 설치 하 고 VM을 다시 부팅 하기 위해 적용 해야 하는 스크립트를 실행 합니다.

## <a name="how-to-use-the-script"></a>스크립트를 사용하는 방법 

이 스크립트를 사용하는 경우 다음 정보가 필요합니다.
1. 스크립트 위치: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/os-patching-reboot-config.sh  HDInsight는 이 URI를 사용하여 클러스터의 모든 가상 머신에서 스크립트를 찾아 실행합니다.
  
2. 스크립트를 적용하는 클러스터 노드 유형: 헤드 노드, workernode, zookeeper 이 스크립트는 클러스터의 모든 노드 유형에 적용되어야 합니다. 노드 형식에는 적용 되지 않으면, 한 다음 해당 노드 유형인 가상 머신은 업데이트 되지 않습니다.


3.  매개 변수: 이 스크립트는 하나의 숫자 매개 변수를 허용합니다.

    | 매개 변수 | 정의 |
    | --- | --- |
    | 전체 OS 업데이트/설치 보안 업데이트만 설치 |0 또는 1입니다. 값 0 1에서 전체 OS 업데이트를 설치 하는 동안에 보안 업데이트를 설치 합니다. 매개 변수가 없는 기본 제공 된 경우은 0입니다. |

> [!NOTE]  
> 기존 클러스터에 적용하는 경우 이 스크립트를 지속형으로 표시해야 합니다. 그렇지 않은 경우 크기 조정 작업을 통해 만들어진 모든 새 노드는 기본 패치 일정을 사용합니다.  클러스터를 만드는 프로세스의 일부로 스크립트를 적용하는 경우 자동으로 유지됩니다.


## <a name="next-steps"></a>다음 단계

스크립트 작업을 사용하는 방법에 대한 특정 단계는 [스크립트 작업을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)에서 다음 섹션을 참조하세요.

* [클러스터를 만드는 동안 스크립트 작업 사용](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [실행 중인 클러스터에 스크립트 작업 적용](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
