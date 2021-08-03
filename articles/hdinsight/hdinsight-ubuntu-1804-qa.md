---
title: Azure HDInsight Ubuntu 18.04 업데이트
description: Azure HDInsight Ubuntu 18.04 OS 변경 사항에 대해 알아봅니다.
ms.service: hdinsight
ms.topic: conceptual
ms.author: yanacai
author: yanancai
ms.date: 05/25/2021
ms.openlocfilehash: 61d801d7091d2a619ff6a8b6436f386c125ca4be
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111415377"
---
# <a name="hdinsight-ubuntu-1804-os-update"></a>HDInsight Ubuntu 18.04 OS 업데이트

이 문서에서는 HDInsight Ubuntu 18.04 OS 업데이트 및 필요한 잠재적 변경 사항에 대해 자세히 설명합니다.

## <a name="update-overview"></a>업데이트 개요

HDInsight는 2021년 5월에 Ubuntu 18.04에서 실행되는 새 HDInsight 4.0 클러스터 이미지 출시를 시작했습니다. 새로 만든 HDInsight 4.0 클러스터는 제공된 후 기본적으로 Ubuntu 18.04에서 실행됩니다. Ubuntu 16.04의 기존 클러스터는 완벽한 지원과 함께 있는 그대로 실행됩니다.

HDInsight 3.6은 Ubuntu 16.04에서 계속 실행됩니다. 2021년 6월 30일까지 표준 지원이 종료되고 2021년 7월 1일부터 기본 지원으로 변경됩니다. 날짜 및 지원 옵션에 대한 자세한 내용은 [Azure HDInsight 버전](./hdinsight-component-versioning.md)을 참조하세요. HDInsight 3.6에서는 Ubuntu 18.04가 지원되지 않습니다. Ubuntu 18.04를 사용하려면 클러스터를 HDInsight 4.0으로 마이그레이션해야 합니다.

기존 클러스터를 Ubuntu 18.04로 이동하려면 클러스터를 삭제하고 다시 만드세요. 클러스터를 만들거나 다시 만들 계획을 세우세요. 

## <a name="script-actions-changes"></a>스크립트 작업 변경 내용

HDInsight 스크립트 작업은 추가 구성 요소를 설치하고 구성 설정을 변경하는 데 사용됩니다. 스크립트 작업은 HDInsight 클러스터의 노드에서 실행되는 Bash 스크립트입니다.

스크립트 동작에 대해 변경해야 하는 몇 가지 잠재적 변경 사항이 있을 수 있습니다.

**필요할 때마다 패키지를 가져올 때 `xenial`의 각 인스턴스를 `bionic`으로 변경합니다.**

예를 들면 다음과 같습니다.
- `http://packages.treasuredata.com/3/ubuntu/xenial/ xenial contrib`를 `http://packages.treasuredata.com/3/ubuntu/bionic/ bionic contrib`으로 업데이트합니다.
- `http://azure.archive.ubuntu.com/ubuntu/ xenial main restricted`를 `http://azure.archive.ubuntu.com/ubuntu/ bionic main restricted`으로 업데이트합니다.

**일부 패키지 버전은 bionic용으로 존재하지 않습니다.** 

예를 들어 [Node.js 버전 4.x](https://deb.nodesource.com/node_4.x/dists/)는 bionic 리포지토리에 없습니다. [Node.js 버전 12.x](https://deb.nodesource.com/node_12.x/dists/bionic/)는 있습니다.

Bionic용으로 존재하지 않는 이전 버전을 설치하는 스크립트는 이후 버전으로 업데이트해야 합니다.

**/etc/rc.local은 기본적으로 18.04에 없습니다.**

일부 스크립트는 서비스 시작에 `/etc/rc.local`을 사용하지만 Ubuntu 18.04에는 기본적으로 존재하지 않습니다. 적절한 systemd 단위로 변환해야 합니다. 

**기본 OS 패키지가 업데이트되었습니다.**

스크립트가 Ubuntu 16.04의 이전 버전 패키지에 의존하는 경우 작동하지 않을 수 있습니다. 설치된 모든 패키지의 세부 정보를 표시하려면 클러스터 노드에 SSH로 연결하고 클러스터 노드에서 `dpkg --list`를 실행합니다.
 
**일반적으로 Ubuntu 18.04의 규칙이 16.04보다 더 엄격합니다.**

## <a name="custom-applications"></a>사용자 지정 애플리케이션
일부 [타사 애플리케이션](./hdinsight-apps-install-applications.md)은 HDInsight 클러스터에 설치할 수 있습니다. 이러한 애플리케이션은 Ubuntu 18.04에서 잘 작동하지 않을 수 있습니다. 호환성이 손상되는 변경의 위험을 줄이기 위해 HDInsight는 2021년 2월 25일 이후 사용자 지정 애플리케이션을 설치한 구독에 대해 새 이미지를 롤아웃하지 않습니다. 테스트 구독에서 새 이미지를 사용하려면 지원 티켓을 열어 구독을 사용하도록 설정합니다.

## <a name="edge-nodes"></a>에지 노드
새 이미지를 사용하면 클러스터 에지 노드용 OS도 Ubuntu 18.04로 업데이트됩니다. 기존 클라이언트는 Ubuntu 18.04를 사용하여 테스트해야 합니다. 호환성이 손상되는 변경의 위험을 줄이기 위해 HDInsight는 2021년 2월 25일 이후 에지 노드를 사용한 구독에 대해 새 이미지를 롤아웃하지 않습니다. 테스트 구독에서 새 이미지를 사용하려면 지원 티켓을 열어 구독을 사용하도록 설정합니다.

## <a name="references"></a>참조
 - [Ubuntu 18.04 LTS 릴리스 정보](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes/)





