---
title: HDInsight 클러스터를 삭제하는 방법 - Azure
description: Azure HDInsight 클러스터를 삭제할 수 있는 다양한 방법에 대한 정보
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 2912777c001a36ec913e2cfd618091ada5aa107a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74807139"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>브라우저, PowerShell 또는 Azure CLI를 사용하여 HDInsight 클러스터 삭제

클러스터가 만들어지면 HDInsight 클러스터 청구가 시작되고 클러스터가 삭제되면 중지됩니다. 청구는 분당 비례 배분되므로 더 이상 사용하지 않을 때는 항상 클러스터를 삭제해야 합니다. 이 문서에서는 [Azure 포털,](https://portal.azure.com) [Azure PowerShell Az 모듈](https://docs.microsoft.com/powershell/azure/overview)및 Azure [CLI를](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)사용하여 클러스터를 삭제하는 방법을 배웁니다.

> [!IMPORTANT]  
> HDInsight 클러스터를 삭제해도 클러스터와 연결된 Azure Storage 계정 또는 Data Lake Storage는 삭제되지 않습니다. 나중에 해당 서비스에 저장된 데이터를 다시 사용할 수 있습니다.

## <a name="azure-portal"></a>Azure portal

1. [Azure 포털에](https://portal.azure.com)로그인합니다.

2. 왼쪽 메뉴에서 모든 **서비스** > **분석** > **HDInsight 클러스터로** 이동하여 클러스터를 선택합니다.

3. 기본 보기에서 **삭제** 아이콘을 선택합니다. 프롬프트에 따라 클러스터를 삭제합니다.

    ![HDInsight 클러스터 버튼 삭제](./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

아래 `CLUSTERNAME` 코드에서 HDInsight 클러스터의 이름으로 바꿉니다. PowerShell 프롬프트에서 다음 명령을 입력하여 클러스터를 삭제합니다.

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure CLI

HDInsight 클러스터의 `CLUSTERNAME` `RESOURCEGROUP` 이름으로 바꾸고 아래 코드에서 리소스 그룹의 이름으로 바꿉니다.  명령 프롬프트에서 다음을 입력하여 클러스터를 삭제합니다.

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
