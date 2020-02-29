---
title: Azure CLI를 사용 하 여 Azure HDInsight 클러스터 관리
description: Azure CLI를 사용 하 여 Azure HDInsight 클러스터를 관리 하는 방법을 알아봅니다. 클러스터 유형으로는 Apache Hadoop, Spark, HBase, 폭풍, Kafka, 대화형 쿼리 및 ML 서비스가 있습니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/26/2020
ms.openlocfilehash: 2c6495454e5ba2449d4b3c74a096681f74610813
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199464"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Azure CLI를 사용 하 여 Azure HDInsight 클러스터 관리

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 를 사용 하 여 Azure HDInsight 클러스터를 관리 하는 방법을 알아봅니다. Azure CLI(명령줄 인터페이스)는 Azure 리소스를 관리하기 위한 Microsoft의 플랫폼 간 명령줄 환경입니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

* Azure CLI. Azure CLI를 설치하지 않은 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)에 나온 단계를 참조하세요.

* HDInsight의 Apache Hadoop 클러스터. [Linux에서 HDInsight 시작](hadoop/apache-hadoop-linux-tutorial-get-started.md)을 참조하세요.

## <a name="connect-to-azure"></a>Azure에 연결

Azure 구독에 로그인합니다. Azure Cloud Shell를 사용 하려는 경우 코드 블록의 오른쪽 위 모퉁이에서 **시도** 를 선택 합니다. 그렇지 않은 경우 아래 명령을 입력합니다.

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>클러스터 나열

[Az hdinsight list](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-list) 를 사용 하 여 클러스터를 나열 합니다. `RESOURCE_GROUP_NAME`을 리소스 그룹의 이름으로 바꾸고 다음 명령을 입력 하 여 아래 명령을 편집 합니다.

```azurecli-interactive
# List all clusters in the current subscription
az hdinsight list

# List only cluster name and its resource group
az hdinsight list --query "[].{Cluster:name, ResourceGroup:resourceGroup}" --output table

# List all cluster for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME

# List all cluster names for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME --query "[].{clusterName:name}" --output table
```

## <a name="show-cluster"></a>클러스터 표시

[Az hdinsight show](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-show) 를 사용 하 여 지정 된 클러스터에 대 한 정보를 표시 합니다. `RESOURCE_GROUP_NAME`를 대체 하 여 아래 명령을 편집 하 고 `CLUSTER_NAME` 관련 정보를 입력 한 다음 명령을 입력 합니다.

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>클러스터 삭제

지정 된 클러스터를 삭제 하려면 [az hdinsight delete](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-delete) 를 사용 합니다. `RESOURCE_GROUP_NAME`를 대체 하 여 아래 명령을 편집 하 고 `CLUSTER_NAME` 관련 정보를 입력 한 다음 명령을 입력 합니다.

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

또한 클러스터를 포함하는 리소스 그룹을 삭제하여 클러스터를 삭제할 수도 있습니다. 이렇게 하면 기본 저장소 계정을 포함 하 여 그룹의 모든 리소스가 삭제 됩니다.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>클러스터 크기 조정

지정 된 HDInsight 클러스터의 크기를 지정 된 크기로 조정 하려면 [az hdinsight resize](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) 를 사용 합니다. `RESOURCE_GROUP_NAME`를 대체 하 여 아래 명령을 편집 하 고 관련 정보를 `CLUSTER_NAME` 합니다. `WORKERNODE_COUNT`를 클러스터에 필요한 작업자 노드 수로 바꿉니다. 클러스터 크기 조정에 대 한 자세한 내용은 [HDInsight 클러스터 크기 조정](./hdinsight-scaling-best-practices.md)을 참조 하세요. 다음 명령을 입력합니다.

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --workernode-count WORKERNODE_COUNT
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 다양 한 HDInsight 클러스터 관리 작업을 수행 하는 방법을 알아보았습니다. 자세히 알아보려면 다음 아티클을 참조하세요.

* [Azure Portal을 사용하여 HDInsight의 Apache Hadoop 클러스터 관리](hdinsight-administer-use-portal-linux.md)
* [Azure PowerShell를 사용 하 여 HDInsight 관리](hdinsight-administer-use-powershell.md)
* [Azure HDInsight 시작](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Azure CLI 시작](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)
