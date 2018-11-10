---
title: HDInsight 클러스터를 삭제하는 방법 - Azure
description: HDInsight 클러스터를 삭제할 수 있는 다양한 방법에 대한 정보입니다.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: dff9420eb0f91652cc134a37d1b248e2e5b2b681
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51004538"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-classic-cli"></a>브라우저, PowerShell 또는 Azure 클래식 CLI를 사용하여 HDInsight 클러스터 삭제

클러스터가 만들어지면 HDInsight 클러스터 청구가 시작되고 클러스터가 삭제되면 중지됩니다. 분 단위로 청구되므로 더 이상 사용하지 않으면 항상 클러스터를 삭제해야 합니다. 이 문서에서는 Azure Portal, Azure PowerShell 및 Azure 클래식 CLI를 사용하여 클러스터를 삭제하는 방법을 알아봅니다.

> [!IMPORTANT]
> HDInsight 클러스터를 삭제해도 클러스터와 연결된 Azure Storage 계정 또는 Data Lake Store는 삭제되지 않습니다. 나중에 해당 서비스에 저장된 데이터를 다시 사용할 수 있습니다.

## <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com)에 로그인하고 HDInsight 클러스터를 선택합니다. HDInsight 클러스터가 대시보드에 고정되어 있지 않은 경우 검색 필드를 사용하여 이름으로 검색할 수 있습니다.
   
    ![포털 검색](./media/hdinsight-delete-cluster/navbar.png)

2. 클러스터 설정에서 **삭제** 아이콘을 선택합니다. 메시지가 표시되면 **예**를 선택하여 클러스터를 삭제합니다.
   
    ![삭제 아이콘](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

PowerShell 프롬프트에서 다음 명령을 사용하여 클러스터를 삭제합니다.

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

**CLUSTERNAME**을 HDInsight 클러스터의 이름으로 바꿉니다.

## <a name="azure-classic-cli"></a>Azure 클래식 CLI

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

프롬프트에서 다음을 사용하여 클러스터를 삭제합니다.

    azure hdinsight cluster delete CLUSTERNAME

**CLUSTERNAME**을 HDInsight 클러스터의 이름으로 바꿉니다.
