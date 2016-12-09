---
title: "HDInsight 클러스터를 삭제하는 방법 | Microsoft 문서"
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
ms.date: 10/28/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 67442bf4b04f6f3799d30f7ce26547c8145d9168


---
# <a name="how-to-delete-an-hdinsight-cluster"></a>HDInsight 클러스터를 삭제하는 방법
HDInsight 클러스터 청구는 클러스터를 만들면 시작하고 클러스터가 삭제되면 중지하고 분당 비례 배분되므로 더 이상 사용하지 않는 경우에 항상 클러스터를 삭제해야 합니다. 이 문서에서는 Azure 포털, Azure PowerShell 및 Azure CLI를 사용하여 클러스터를 삭제하는 방법을 배웁니다.

> [!IMPORTANT]
> HDInsight 클러스터를 삭제해도 클러스터와 연결된 Azure 저장소 계정은 삭제되지 않습니다. 따라서 클러스터에서 저장한 모든 데이터를 유지하고 다시 사용할 수 있습니다.
> 
> 

## <a name="azure-portal"></a>Azure 포털
1. [Azure 포털](https://portal.azure.com) 에 로그인하고 HDInsight 클러스터를 선택합니다. HDInsight 클러스터가 대시보드에 고정되어 있지 않은 경우 탐색 모음의 오른쪽에서 검색 필드(돋보기 아이콘)를 사용하여 이름으로 검색할 수 있습니다.
   
    ![포털 검색](./media/hdinsight-delete-cluster/navbar.png)
2. 클러스터에 대한 블레이드가 열리면 **삭제** 아이콘을 선택합니다. 메시지가 표시되면 **예** 를 선택하여 클러스터를 삭제합니다.
   
    ![삭제 아이콘](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell
PowerShell 프롬프트에서 다음 명령을 사용하여 클러스터를 삭제합니다.

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

**CLUSTERNAME** 을 HDInsight 클러스터의 이름으로 바꿉니다.

## <a name="azure-cli"></a>Azure CLI
프롬프트에서 다음을 사용하여 클러스터를 삭제합니다.

    azure hdinsight cluster delete CLUSTERNAME

**CLUSTERNAME** 을 HDInsight 클러스터의 이름으로 바꿉니다.




<!--HONumber=Nov16_HO3-->


