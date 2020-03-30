---
title: Azure HDInsight에 사용자 지정 아파치 암바리 데이터베이스
description: 사용자 지정 아파치 암바리 데이터베이스를 사용하여 HDInsight 클러스터를 만드는 방법을 알아봅니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: hrasheed
ms.openlocfilehash: e7351e2f39c7e4eed84f4a47e3eeb2214a062a94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240163"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>사용자 지정 Ambari DB로 HDInsight 클러스터 설정

아파치 암바리는 아파치 하두롭 클러스터의 관리 및 모니터링을 단순화합니다. Ambari는 사용하기 쉬운 웹 UI 및 REST API를 제공합니다. Ambari는 HDInsight 클러스터에 포함되어 있으며 클러스터를 모니터링하고 구성을 변경하는 데 사용됩니다.

[HDInsight에서 클러스터 설정과](hdinsight-hadoop-provision-linux-clusters.md)같은 다른 문서에서 설명한 대로 일반 클러스터 만들기에서 Ambari는 HDInsight에서 관리하며 사용자가 액세스할 수 없는 [S0 Azure SQL 데이터베이스에](../sql-database/sql-database-dtu-resource-limits-single-databases.md#standard-service-tier) 배포됩니다.

사용자 지정 Ambari DB 기능을 사용하면 관리하는 외부 데이터베이스에 새 클러스터및 설정 Ambari를 배포할 수 있습니다. 배포는 Azure 리소스 관리자 템플릿으로 수행됩니다. 이 기능에는 다음과 같은 이점이 있습니다.

- 사용자 지정 - 데이터베이스의 크기와 처리 용량을 선택합니다. 집중적인 워크로드를 처리하는 대규모 클러스터가 있는 경우 사양이 낮은 Ambari 데이터베이스는 관리 작업의 병목 현상이 될 수 있습니다.
- 유연성 - 필요에 따라 필요에 따라 데이터베이스를 확장할 수 있습니다.
- 제어 - 조직의 요구 사항에 맞는 방식으로 데이터베이스에 대한 백업 및 보안을 관리할 수 있습니다.

이 문서의 나머지 부분에서는 다음 사항에 대해 설명합니다.

- 사용자 지정 Ambari DB 기능을 사용하기 위한 요구 사항
- 아파치 암바리에 대한 자신의 외부 데이터베이스를 사용하여 HDInsight 클러스터를 프로비전하는 데 필요한 단계

## <a name="custom-ambari-db-requirements"></a>사용자 지정 암바리 DB 요구 사항

모든 클러스터 유형 및 버전으로 사용자 지정 Ambari DB를 배포할 수 있습니다. 여러 클러스터는 동일한 Ambari DB를 사용할 수 없습니다.

사용자 지정 Ambari DB에는 다음과 같은 다른 요구 사항이 있습니다.

- 기존 Azure SQL DB 서버 및 데이터베이스가 있어야 합니다.
- Ambari 설치에 대해 제공하는 데이터베이스는 비어 있어야 합니다. 기본 dbo 스키마에는 테이블이 없어야 합니다.
- 데이터베이스에 연결하는 데 사용되는 사용자는 데이터베이스에 SELECT, CREATE 테이블 및 INSERT 권한이 있어야 합니다.
- Ambari를 호스팅할 Azure SQL 서버에서 [Azure 서비스에 대한 액세스 허용](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#azure-portal-steps) 옵션을 켭니다.
- HDInsight 서비스의 관리 IP 주소는 SQL Server에서 허용해야 합니다. SQL 서버 방화벽에 추가해야 하는 IP 주소 목록은 [HDInsight 관리 IP 주소를](hdinsight-management-ip-addresses.md) 참조하십시오.

외부 데이터베이스에서 아파치 암바리 DB를 호스팅할 때 다음 사항을 기억하십시오.

- Ambari를 보유 하는 Azure SQL DB의 추가 비용에 대 한 책임은 담당 합니다.
- 사용자 지정 Ambari DB를 주기적으로 백업합니다. Azure SQL Database는 백업을 자동으로 생성하지만 백업 보존 기간은 다양합니다. 자세한 내용은 [자동 SQL 데이터베이스 백업에 대해 알아보기](../sql-database/sql-database-automated-backups.md)를 참조하세요.

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>사용자 지정 Ambari DB로 클러스터 배포

사용자 고유의 외부 Ambari 데이터베이스를 사용하는 HDInsight 클러스터를 만들려면 [사용자 지정 Ambari DB 빠른 시작 템플릿을](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db)사용합니다.

에서 매개 변수를 `azuredeploy.parameters.json` 편집하여 새 클러스터와 Ambari를 보유할 데이터베이스에 대한 정보를 지정합니다.

Azure CLI를 사용하여 배포를 시작할 수 있습니다. 클러스터를 배포할 리소스 그룹으로 바꿉습니다. `<RESOURCEGROUPNAME>`

```azurecli
az group deployment create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## <a name="next-steps"></a>다음 단계

- [Azure HDInsight에서 외부 메타데이터 저장소 사용](hdinsight-use-external-metadata-stores.md)