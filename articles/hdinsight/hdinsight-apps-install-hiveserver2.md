---
title: Azure HDInsight에서 HiveServer2 스케일링
description: 에지 노드를 사용하여 Azure HDInsight 클러스터에서 HiveServer2를 수평으로 스케일링하면 내결함성 및 가용성을 높일 수 있습니다.
services: hdinsight
ms.service: hdinsight
ms.topic: conceptual
ms.reviewer: hrasheed-msft
ms.author: kecheung
author: kcheeeung
ms.date: 08/12/2020
ms.openlocfilehash: d0a0df4791492c1c9f0d600630d723024c46c1b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88227398"
---
# <a name="scale-hiveserver2-on-azure-hdinsight-clusters-for-high-availability"></a>고가용성을 위해 Azure HDInsight 클러스터에서 HiveServer2 스케일링

클러스터에 추가 HiveServer2를 배포하여 가용성을 높이고 부하를 분산하는 방법에 대해 알아봅니다. 헤드 노드 크기를 늘리지 않으려는 경우 에지 노드를 사용하여 HiveServer2를 배포할 수도 있습니다. 

> [!NOTE]
> 사용량에 따라 HiveServer2 수를 늘리면 Hive 메타스토어 연결 수가 증가할 수 있습니다. Azure SQL 데이터베이스 크기도 적절하게 조정되었는지 확인하세요.

## <a name="prerequisites"></a>필수 구성 요소

이 가이드를 사용하려면 다음 문서를 이해하고 있어야 합니다.
- [HDInsight의 Apache Hadoop 클러스터에서 빈 에지 노드 사용](hdinsight-apps-use-edge-node.md)

## <a name="install-hiveserver2"></a>HiveServer2 설치

이 섹션에서는 대상 호스트에 추가 HiveServer2를 설치합니다.

1. 브라우저에서 Ambari를 열고 대상 호스트를 클릭합니다.

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-a.png" alt-text="Ambari 호스트 메뉴입니다.":::

2. 추가 단추를 클릭하고 HiveServer2를 클릭합니다.

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-b.png" alt-text="호스트의 HiveServer2 추가 패널입니다.":::

3. 확인하면 프로세스가 실행됩니다. 원하는 모든 호스트에 대해 1-3단계를 반복합니다.

4. 설치가 완료되면 모든 서비스를 이전 구성으로 다시 시작하고 HiveServer2를 시작합니다.

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-c.png" alt-text="HiveServer2 시작 패널입니다.":::

## <a name="next-steps"></a>다음 단계

이 문서에서는 HiveServer2를 클러스터에 설치하는 방법을 알아보았습니다. 에지 노드 및 애플리케이션에 대한 자세한 정보는 다음 문서를 참조하세요.

* [에지 노드 설치](hdinsight-apps-use-edge-node.md): HDInsight 클러스터에 에지 노드를 설치하는 방법을 알아봅니다.
* [HDInsight 애플리케이션 설치](hdinsight-apps-install-applications.md): 클러스터에 HDInsight 애플리케이션을 설치하는 방법을 알아봅니다.
* [Azure SQL DTU 연결 제한](../azure-sql/database/resource-limits-dtu-single-databases.md): DTU를 통한 Azure SQL 데이터베이스 제한에 대해 알아봅니다.
* [Azure SQL vCore 연결 제한](../azure-sql/database/resource-limits-vcore-elastic-pools.md): vCore를 통한 Azure SQL 데이터베이스 제한에 대해 알아봅니다.
