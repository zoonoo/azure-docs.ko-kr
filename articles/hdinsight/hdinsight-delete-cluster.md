---
title: HDInsight 클러스터를 삭제하는 방법 - Azure
description: Azure HDInsight 클러스터를 삭제할 수 있는 다양한 방법에 대한 정보
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive, devx-track-azurecli
ms.date: 11/29/2019
ms.openlocfilehash: 2160cfcd1f12f5effe7bb182eb665f85fec863af
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863397"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>브라우저, PowerShell 또는 Azure CLI를 사용하여 HDInsight 클러스터 삭제

클러스터가 만들어지면 HDInsight 클러스터 청구가 시작되고 클러스터가 삭제되면 중지됩니다. 청구는 분 단위로 비례 배분되므로 더 이상 사용하지 않을 때는 항상 클러스터를 삭제해야 합니다. 이 문서에서는 [Azure Portal](https://portal.azure.com), [Azure PowerShell Az 모듈](/powershell/azure/), [Azure CLI](/cli/azure/)를 사용하여 클러스터를 삭제하는 방법을 학습합니다.

> [!IMPORTANT]  
> HDInsight 클러스터를 삭제해도 클러스터와 연결된 Azure Storage 계정 또는 Data Lake Storage는 삭제되지 않습니다. 나중에 해당 서비스에 저장된 데이터를 다시 사용할 수 있습니다.

## <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 메뉴에서 **모든 서비스** > **Analytics** > **HDInsight 클러스터** 로 이동하고 클러스터를 선택합니다.

3. 기본 보기에서 **삭제** 아이콘을 선택합니다. 프롬프트에 따라 클러스터를 삭제합니다.

    :::image type="content" source="./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png" alt-text="HDInsight 클러스터 삭제 단추":::

## <a name="azure-powershell"></a>Azure PowerShell

아래 코드에서 `CLUSTERNAME`을 HDInsight 클러스터의 이름으로 바꿉니다. PowerShell 프롬프트에서 다음 명령을 입력하여 클러스터를 삭제합니다.

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure CLI

아래 코드에서 `CLUSTERNAME`을 HDInsight 클러스터의 이름으로 바꾸고 `RESOURCEGROUP`을 리소스 그룹의 이름으로 바꿉니다.  명령 프롬프트에서 다음을 입력하여 클러스터를 삭제합니다.

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
