---
title: 아파치 하두프 & 보안 전송 스토리지 - Azure HDInsight
description: 보안 전송이 활성화된 Azure Storage 계정을 사용하여 HDInsight 클러스터를 만드는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: c1e5ca8b0bb828e5e8ce896bba6a5278266b118e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560085"
---
# <a name="apache-hadoop-clusters-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Azure HDInsight에서 보안 전송 저장소 계정이 있는 아파치 하두롭 클러스터

[보안 전송 필요](../storage/common/storage-require-secure-transfer.md) 기능은 보안 연결을 통해 모든 요청을 계정에 적용하여 Azure Storage 계정의 보안을 강화합니다. 이 기능과 wasbs 체계는 HDInsight 클러스터 버전 3.6 이상에서만 지원됩니다.

> [!IMPORTANT]
> 클러스터를 만든 후 안전한 저장소 전송을 사용하도록 설정하면 저장소 계정을 사용하는 데 오류가 발생할 수 있으므로 권장되지 않습니다. 보안 전송이 이미 활성화된 저장소 계정을 사용하여 새 클러스터를 만드는 것이 좋습니다.

## <a name="storage-accounts"></a>Storage 계정

### <a name="azure-portal"></a>Azure portal

기본적으로 Azure Portal에서 저장소 계정을 만들 때 보안 전송 필요한 속성이 활성화됩니다.

Azure 포털을 사용하여 기존 저장소 계정을 업데이트하려면 [Azure 포털을 사용하여 보안 전송 을](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-for-an-existing-storage-account)참조하십시오.

### <a name="powershell"></a>PowerShell

PowerShell cmdlet [New-AzStorageAccount의](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount)경우 `-EnableHttpsTrafficOnly` 매개 `1`변수가 로 설정되어 있는지 확인합니다.

PowerShell을 사용하여 기존 저장소 계정을 업데이트하려면 [PowerShell을 사용하여 보안 전송 필요를](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-powershell)참조하십시오.

### <a name="azure-cli"></a>Azure CLI

Azure CLI 명령 [az 저장소 계정 create의](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)경우 매개 변수가 `--https-only` `true`로 설정되어 있는지 확인합니다.

Azure CLI를 사용하여 기존 저장소 계정을 업데이트하려면 [Azure CLI를 사용하여 보안 전송 필요를](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-azure-cli)참조하십시오.

## <a name="add-additional-storage-accounts"></a>추가 스토리지 계정 추가

보안 전송이 활성화된 스토리지 계정을 추가할 수 있는 여러 옵션이 있습니다.

* 마지막 섹션에서 Azure Resource Manager 템플릿을 수정합니다.
* [Azure Portal](https://portal.azure.com)을 사용하여 클러스터를 만들고 연결된 스토리지 계정을 지정합니다.
* 스크립트 동작을 사용하여 보안 전송이 활성화된 스토리지 계정을 기존 HDInsight 클러스터에 추가합니다. 자세한 내용은 [HDInsight에 추가 스토리지 계정 추가](hdinsight-hadoop-add-storage.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 기본 데이터 저장소로 [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) 대신 Azure Storage(WASB) 사용
* HDInsight에서 Azure Storage를 사용하는 방법에 대한 자세한 내용은 [HDInsight에서 Azure Storage 사용](hdinsight-hadoop-use-blob-storage.md)을 참조하세요.
* 데이터를 HDInsight로 업로드하는 방법에 대한 정보는 [HDInsight에 데이터 업로드](hdinsight-upload-data.md)를 참조하세요.
