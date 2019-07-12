---
title: OS 패치 Linux 기반 HDInsight 클러스터-Azure에 대 한 일정을 구성 합니다.
description: Linux 기반 HDInsight 클러스터의 OS 패치 일정을 구성하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: efe74618b269000749f7ba6c24d35903e540dcfb
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657048"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>OS 패치 Linux 기반 HDInsight 클러스터에 대 한 일정을 구성 합니다. 

> [!IMPORTANT]
> Ubuntu 이미지 게시할 3 개월 내 새 Azure HDInsight 클러스터를 만들기 위해 사용할 수 있습니다. 2019 년 1 월을 기준으로 실행 중인 클러스터 자동 패치가 적용 되지 않습니다. 고객이 스크립트 동작이나 기타 메커니즘을 사용하여 실행 중인 클러스터를 패치해야 합니다. 항상 새로 만든 클러스터에는 최신 보안 패치를 포함한 사용 가능한 최신 업데이트가 있습니다.

경우에 따라 중요 한 보안 패치를 설치 하려면 HDInsight 클러스터에서 가상 머신 (Vm)를 다시 시작 해야 있습니다.

이 문서에 설명 된 스크립트 동작을 사용 하 여 OS 패치 일정을 다음과 같이 수정할 수 있습니다.

1. 모든 업데이트를 설치 하거나 커널 + 보안 업데이트 되거나 커널이 업데이트를 설치 합니다.
2. 즉시 다시 시작을 수행 하거나 VM에서 다시 시작을 예약 합니다.

> [!NOTE]  
> 이 문서에 설명 된 스크립트 동작을 2016 년 8 월 1 일 이후에 만든 Linux 기반 HDInsight 클러스터 에서만 작동 합니다. Vm을 다시 시작 후에 패치가 적용 됩니다.
> 스크립트 동작 모든 향후 업데이트 주기에 대 한 업데이트를 자동으로 적용 되지 않습니다. 새 업데이트에 업데이트를 설치 하 고 VM을 다시 적용 해야 할 때마다 스크립트를 실행 합니다.

## <a name="add-information-to-the-script"></a>스크립트에 정보를 추가 합니다.

스크립트를 사용 하는 다음 정보가 필요 합니다.

- 설치 업데이트 일정 재부팅 스크립트 위치: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh 합니다.
    
   HDInsight이이 URI를 사용 하 여 찾기 및 클러스터의 모든 Vm에서 스크립트를 실행 합니다. 이 스크립트는 업데이트를 설치 하 고 VM을 다시 시작 하는 옵션을 제공 합니다.
  
- 일정-다시 부팅 스크립트 위치: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh 합니다.
    
   HDInsight이이 URI를 사용 하 여 찾기 및 클러스터의 모든 Vm에서 스크립트를 실행 합니다. 이 스크립트는 VM을 다시 시작 합니다.
  
- 스크립트에 적용 되는 클러스터 노드 유형 헤드 노드, workernode, zookeeper 됩니다. 스크립트를 클러스터의 모든 노드 유형에 적용 됩니다. 스크립트는 노드 형식에 적용 되지 않습니다, 경우 해당 노드 형식의 Vm 업데이트 하거나 다시 시작 하지 않습니다.

- 업데이트 일정 재부팅 설치 스크립트는 두 개의 숫자 매개 변수를 허용합니다.

    | 매개 변수 | 정의 |
    | --- | --- |
    | 모든 업데이트/설치 커널 + 보안 설치 / 커널 업데이트만 설치만 업데이트|0, 1 또는 2를 선택 합니다. 값이 0만 커널 업데이트를 설치 합니다. 값 1의 모든 업데이트 및 2 개의 설치만 커널 + 보안 업데이트를 설치합니다. 없는 매개 변수를 제공 하는 경우 기본값은 0입니다. |
    | 다시 부팅 사용/사용 일정 다시 부팅 사용/즉시 다시 부팅 안 함 |0, 1 또는 2를 선택 합니다. 값 0 다시 시작을 해제합니다. 값이 1 일정 다시 시작 있으며 2를 사용 하면 즉시 다시 시작 합니다. 없는 매개 변수를 제공 하는 경우 기본값은 0입니다. 사용자는 입력된 매개 변수 1 입력 매개 변수 2로 변경 해야 합니다. |
   
 - 일정-다시 부팅 한 후 스크립트는 하나의 숫자 매개 변수를 허용합니다.

    | 매개 변수 | 정의 |
    | --- | --- |
    | 일정 다시 부팅 사용/즉시 다시 부팅을 사용 하도록 설정 |1 또는 2입니다. 값이 1 (12 ~ 24 시간에 예약 된) 일정 다시 시작을 수 있습니다. 값이 2 (5 분)에 즉시 다시 시작 수 있습니다. 주어진 매개 변수가 없는 경우 기본값은 1입니다. |  

> [!NOTE]
> 스크립트를 기존 클러스터에 적용 하면 지속형으로 표시 해야 합니다. 그렇지 않은 경우 크기 조정 작업을 통해 만들어진 모든 새 노드는 기본 패치 일정을 사용합니다. 클러스터 만들기 프로세스의 일부로 스크립트를 적용 하는 경우 자동으로 유지 했습니다.


## <a name="next-steps"></a>다음 단계

다음 섹션을 참조 하는 스크립트 작업을 사용 하 여 특정 단계에 대 한 [스크립트 동작을 사용 하 여 사용자 지정 Linux 기반 HDInsight 클러스터](hdinsight-hadoop-customize-cluster-linux.md):

* [클러스터를 만드는 동안 스크립트 작업 사용](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [실행 중인 클러스터에 스크립트 작업 적용](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
