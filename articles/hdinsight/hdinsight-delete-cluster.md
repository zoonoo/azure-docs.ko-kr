---
title: HDInsight 클러스터를 삭제하는 방법 - Azure
description: Azure HDInsight 클러스터를 삭제할 수 있는 다양 한 방법에 대 한 정보
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 08bfdcab0b7fbb8e533cb8d2d6a74d315ad4074c
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70885242"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>브라우저, PowerShell 또는 Azure CLI를 사용하여 HDInsight 클러스터 삭제

클러스터가 만들어지면 HDInsight 클러스터 청구가 시작되고 클러스터가 삭제되면 중지됩니다. 분 단위로 청구되므로 더 이상 사용하지 않으면 항상 클러스터를 삭제해야 합니다. 이 문서에서는 [Azure Portal](https://portal.azure.com), [Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/overview)및 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)를 사용 하 여 클러스터를 삭제 하는 방법에 대해 알아봅니다.

> [!IMPORTANT]  
> HDInsight 클러스터를 삭제해도 클러스터와 연결된 Azure Storage 계정 또는 Data Lake Storage는 삭제되지 않습니다. 나중에 해당 서비스에 저장된 데이터를 다시 사용할 수 있습니다.

## <a name="azure-portal"></a>Azure Portal

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 메뉴에서 **모든 서비스** > **분석** > **HDInsight 클러스터** 로 이동 하 여 클러스터를 선택 합니다.

3. 기본 보기에서 **삭제** 아이콘을 선택 합니다. 프롬프트에 따라 클러스터를 삭제 합니다.
   
    ![삭제 아이콘](./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png)

## <a name="azure-powershell-az-module"></a>Azure PowerShell Az module

을 `CLUSTERNAME` 아래 코드의 HDInsight 클러스터 이름으로 바꿉니다. PowerShell 프롬프트에서 다음 명령을 입력 하 여 클러스터를 삭제 합니다.

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure CLI

을 `CLUSTERNAME` HDInsight 클러스터의 이름으로 바꾸고 `RESOURCEGROUP` ,을 아래 코드의 리소스 그룹 이름으로 바꿉니다.  명령 프롬프트에서 다음을 입력 하 여 클러스터를 삭제 합니다.

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
