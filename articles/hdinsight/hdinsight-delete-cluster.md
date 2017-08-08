---
title: "HDInsight 클러스터를 삭제하는 방법 - Azure | Microsoft Docs"
description: "HDInsight 클러스터를 삭제할 수 있는 다양한 방법에 대한 정보입니다."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 55f7838b-9786-47ff-96db-1b64437bd0bb
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/03/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.translationtype: Human Translation
ms.sourcegitcommit: 5bbeb9d4516c2b1be4f5e076a7f63c35e4176b36
ms.openlocfilehash: 7d3aed71d9b5867a87954a60b1e470def0563f71
ms.contentlocale: ko-kr
ms.lasthandoff: 06/13/2017

---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>브라우저, PowerShell 또는 Azure CLI를 사용하여 HDInsight 클러스터 삭제

클러스터가 만들어지면 HDInsight 클러스터 청구가 시작되고 클러스터가 삭제되면 중지됩니다. 분 단위로 청구되므로 더 이상 사용하지 않으면 항상 클러스터를 삭제해야 합니다. 이 문서에서는 Azure Portal, Azure PowerShell 및 Azure CLI 1.0을 사용하여 클러스터를 삭제하는 방법을 배웁니다.

> [!IMPORTANT]
> HDInsight 클러스터를 삭제해도 클러스터와 연결된 Azure Storage 계정은 삭제되지 않습니다. 그러면 데이터를 나중에 다시 사용할 수 있습니다.

## <a name="azure-portal"></a>Azure 포털

1. [Azure Portal](https://portal.azure.com)에 로그인하고 HDInsight 클러스터를 선택합니다. HDInsight 클러스터가 대시보드에 고정되어 있지 않은 경우 검색 필드를 사용하여 이름으로 검색할 수 있습니다.
   
    ![포털 검색](./media/hdinsight-delete-cluster/navbar.png)

2. 클러스터에 대한 블레이드가 열리면 **삭제** 아이콘을 선택합니다. 메시지가 표시되면 **예**를 선택하여 클러스터를 삭제합니다.
   
    ![삭제 아이콘](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

PowerShell 프롬프트에서 다음 명령을 사용하여 클러스터를 삭제합니다.

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

**CLUSTERNAME**을 HDInsight 클러스터의 이름으로 바꿉니다.

## <a name="azure-cli"></a>Azure CLI

프롬프트에서 다음을 사용하여 클러스터를 삭제합니다.

    azure hdinsight cluster delete CLUSTERNAME

**CLUSTERNAME**을 HDInsight 클러스터의 이름으로 바꿉니다.


