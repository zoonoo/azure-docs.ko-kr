---
title: Linux 기반 HDInsight 클러스터에 대 한 OS 패치 일정 구성-Azure
description: Linux 기반 HDInsight 클러스터의 OS 패치 일정을 구성하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: 06111ec35a127cf17fdcc77ff717de7a4bc7299f
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076851"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Linux 기반 HDInsight 클러스터에 대 한 OS 패치 일정 구성 

> [!IMPORTANT]
> Ubuntu 이미지는 게시 되는 3 개월 이내에 새 Azure HDInsight 클러스터를 만드는 데 사용할 수 있게 됩니다. 1 월 2019을 기반으로 실행 중인 클러스터는 자동으로 패치가 적용 되지 않습니다. 고객이 스크립트 동작이나 기타 메커니즘을 사용하여 실행 중인 클러스터를 패치해야 합니다. 항상 새로 만든 클러스터에는 최신 보안 패치를 포함한 사용 가능한 최신 업데이트가 있습니다.

경우에 따라 HDInsight 클러스터에서 Vm (가상 컴퓨터)을 다시 시작 하 여 중요 한 보안 패치를 설치 해야 합니다.

이 문서에서 설명 하는 스크립트 작업을 사용 하 여 다음과 같이 OS 패치 일정을 수정할 수 있습니다.

1. 모든 업데이트를 설치 하거나 커널 + 보안 업데이트 또는 커널 업데이트만 설치 합니다.
2. 즉시 다시 시작 하거나 VM에서 다시 시작을 예약 합니다.

> [!NOTE]  
> 이 문서에서 설명 하는 스크립트 작업은 2016 년 8 월 1 일 이후에 생성 된 Linux 기반 HDInsight 클러스터 에서만 작동 합니다. 패치는 Vm을 다시 시작한 후에만 적용 됩니다.
> 스크립트 작업은 이후의 모든 업데이트 주기에 대해 업데이트를 자동으로 적용 하지 않습니다. 업데이트를 설치 하기 위해 새 업데이트를 적용 해야 할 때마다 스크립트를 실행 하 고 VM을 다시 시작 합니다.

## <a name="add-information-to-the-script"></a>스크립트에 정보 추가

스크립트를 사용 하려면 다음 정보가 필요 합니다.

- 설치-업데이트-예약-다시 부팅 스크립트 위치: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh.
    
   HDInsight는이 URI를 사용 하 여 클러스터의 모든 Vm에서 스크립트를 찾고 실행 합니다. 이 스크립트는 업데이트를 설치 하 고 VM을 다시 시작 하는 옵션을 제공 합니다.
  
- 예약-다시 부팅 스크립트 위치: https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh.
    
   HDInsight는이 URI를 사용 하 여 클러스터의 모든 Vm에서 스크립트를 찾고 실행 합니다. 이 스크립트는 VM을 다시 시작 합니다.
  
- 스크립트가 적용 되는 클러스터 노드 형식은 헤드 노드, 지 노드 및 사육 아웃입니다. 클러스터의 모든 노드 형식에 스크립트를 적용 합니다. 스크립트를 노드 형식에 적용 하지 않으면 해당 노드 형식에 대 한 Vm이 업데이트 되거나 다시 시작 되지 않습니다.

- 설치-업데이트-예약-다시 부팅 스크립트는 두 개의 숫자 매개 변수를 허용 합니다.

    | 매개 변수 | 정의 |
    | --- | --- |
    | 커널 업데이트만 설치/모든 업데이트 설치/커널 + 보안 업데이트만 설치|0, 1 또는 2입니다. 값 0은 커널 업데이트만 설치 합니다. 값 1은 모든 업데이트를 설치 하 고, 2는 커널 + 보안 업데이트만 설치 합니다. 매개 변수를 제공 하지 않으면 기본값은 0입니다. |
    | 다시 부팅/일정 설정 다시 부팅/즉시 다시 부팅 사용 |0, 1 또는 2입니다. 0 값은 다시 시작을 사용 하지 않습니다. 값 1은 일정 다시 시작을 사용 하도록 설정 하 고 2는 즉시 다시 시작을 사용 하도록 설정 합니다. 매개 변수를 제공 하지 않으면 기본값은 0입니다. 사용자는 입력 매개 변수 1을 입력 매개 변수 2로 변경 해야 합니다. |
   
 - 일정 다시 부팅 스크립트는 하나의 숫자 매개 변수를 허용 합니다.

    | 매개 변수 | 정의 |
    | --- | --- |
    | 일정 다시 부팅 설정/즉시 다시 부팅 사용 |1 또는 2. 값 1을 사용 하면 일정을 다시 시작할 수 있습니다 (12-24 시간으로 예약 됨). 값을 2로 설정 하면 즉시 다시 시작할 수 있습니다 (5 분 이내). 매개 변수를 지정 하지 않으면 기본값은 1입니다. |  

> [!NOTE]
> 기존 클러스터에 적용 한 후에는 스크립트를 지속형으로 표시 해야 합니다. 그렇지 않은 경우 크기 조정 작업을 통해 만들어진 모든 새 노드는 기본 패치 일정을 사용합니다. 클러스터 생성 프로세스의 일부로 스크립트를 적용 하면 자동으로 유지 됩니다.


## <a name="next-steps"></a>다음 단계

스크립트 동작 사용에 대 한 특정 단계는 [스크립트 작업을 사용 하 여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)의 다음 섹션을 참조 하세요.

* [클러스터를 만드는 동안 스크립트 작업 사용](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [실행 중인 클러스터에 스크립트 작업 적용](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
