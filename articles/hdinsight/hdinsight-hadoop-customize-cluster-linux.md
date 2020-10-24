---
title: 스크립트 동작을 사용하여 Azure HDInsight 클러스터 사용자 지정
description: '스크립트 작업을 사용 하 여 HDInsight 클러스터에 사용자 지정 구성 요소를 추가 합니다. 스크립트 동작은 클러스터 구성을 사용자 지정 하는 데 사용할 수 있는 Bash 스크립트입니다. 또는 추가 서비스 및 유틸리티 (예: 색조, Sr 또는 R)를 추가 합니다.'
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 09/02/2020
ms.openlocfilehash: 23361470fd7b1cdb5b6153580e0240ac2f6c9133
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490342"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>스크립트 동작을 사용하여 Azure HDInsight 클러스터 사용자 지정

Azure HDInsight는 사용자 지정 스크립트를 호출 하 여 클러스터를 사용자 지정 하는 **스크립트 동작** 이라는 구성 메서드를 제공 합니다. 이러한 스크립트는 추가 구성 요소를 설치하고 구성 설정을 변경하는 데 사용합니다. 스크립트 작업은 클러스터를 만드는 중이거나 만든 후에 사용할 수 있습니다.

스크립트 작업을 Azure Marketplace에 HDInsight 애플리케이션으로 게시할 수도 있습니다. HDInsight 애플리케이션에 대한 자세한 내용은 [Azure Marketplace에 HDInsight 애플리케이션 게시](hdinsight-apps-publish-applications.md)를 참조하세요.

## <a name="permissions"></a>사용 권한

도메인 조인 HDInsight 클러스터를 사용하는 경우 클러스터에서 스크립트 동작을 사용할 때 다음 두 가지 Apache Ambari 권한이 필요합니다.

* **AMBARI.RUN\_CUSTOM\_COMMAND**. Ambari 관리자 역할은 기본적으로 이 권한을 가집니다.
* **CLUSTER.RUN\_CUSTOM\_COMMAND**. HDInsight 클러스터 관리자와 Ambari 관리자는 기본적으로 이 권한을 가집니다.

도메인 조인 HDInsight에서 권한을 사용하는 방법에 대한 자세한 내용은 [Enterprise Security Package를 사용하여 HDInsight 클러스터 관리](./domain-joined/apache-domain-joined-manage.md)를 참조하세요.

## <a name="access-control"></a>Access Control

Azure 구독의 관리자/소유자가 아닌 경우에는 적어도 HDInsight 클러스터가 포함된 리소스 그룹에 대한 [기여자] 액세스 권한이 있어야 합니다.

Azure 구독에 대 한 참가자 이상의 액세스 권한이 있는 사용자는 이전에 공급자를 등록 해야 합니다. 구독에 대 한 참가자 액세스 권한이 있는 사용자가 리소스를 만들 때 공급자 등록이 발생 합니다. 리소스를 만들지 않고의 경우 [REST를 사용 하 여 공급자 등록](https://msdn.microsoft.com/library/azure/dn790548.aspx)을 참조 하세요.

액세스 관리 작업에 대한 자세한 내용은 다음을 참조하세요.

* [Azure 포털에서 액세스 관리 시작](../role-based-access-control/overview.md)
* [역할 할당을 사용하여 Azure 구독 리소스에 대한 액세스 관리](../role-based-access-control/role-assignments-portal.md)

## <a name="understand-script-actions"></a>스크립트 동작 이해

스크립트 작업은 HDInsight 클러스터의 노드에서 실행되는 Bash 스크립트입니다. 스크립트 동작의 특징과 기능은 다음과 같습니다.

* HDInsight 클러스터에서 액세스할 수 있는 URI에 저장해야 합니다. 가능한 스토리지 위치는 다음과 같습니다.

    * 일반 클러스터의 경우:

      * ADLS Gen1: HDInsight가 Data Lake Storage에 액세스 하는 데 사용 하는 서비스 주체에는 스크립트에 대 한 읽기 권한이 있어야 합니다. Data Lake Storage Gen1에 저장되는 스크립트에 대한 URI 형식은 `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file`입니다.

      * HDInsight 클러스터에 대한 기본 또는 추가 스토리지 계정인 Azure Storage 계정의 Blob. HDInsight는 클러스터를 만드는 동안 이러한 두 유형의 스토리지 계정 모두에 대해 액세스 권한을 부여받습니다.

        > [!IMPORTANT]  
        > 이 Azure Storage 계정에서 저장소 키를 회전 하지 마십시오 .이 경우 스크립트를 사용 하 여 후속 스크립트 작업을 수행 하면 오류가 발생 합니다.

      * Http://경로를 통해 액세스할 수 있는 공용 파일 공유 서비스입니다. Azure Blob, GitHub, OneDrive 등을 예로 들 수 있습니다. URI 예제는 [예제 스크립트 동작 스크립트](#example-script-action-scripts)를 참조하세요.

     * ESP를 사용 하는 클러스터의 경우 wasb://또는 wasbs://또는 http [s]://Uri가 지원 됩니다.

* 특정 노드 유형에서만 실행되도록 제한할 수 있습니다. 예를 들어 헤드 노드 또는 작업자 노드가 있습니다.

* 지속형 또는 일 수 있습니다 `ad hoc` .

    지속형 스크립트 작업은 고유한 이름이 있어야 합니다. 지속형 스크립트는 크기 조정 작업을 통해 클러스터에 추가되는 새 작업자 노드를 사용자 지정하는 데 사용됩니다. 또한 크기 조정 작업이 수행되면 지속형 스크립트에서 다른 노드 유형에도 변경 내용을 적용할 수 있습니다. 예를 들어 헤드 노드가 있습니다.

    `Ad hoc` 스크립트는 지속 되지 않습니다. 클러스터를 만들 때 사용되는 스크립트 작업은 자동으로 보존됩니다. 이 스크립트가 실행되더라도 클러스터에 추가된 작업자 노드에는 적용되지 않습니다. 그런 다음 `ad hoc` 스크립트를 지속형 스크립트로 승격 하거나 지속형 스크립트의 수준을 스크립트로 내릴 수 있습니다 `ad hoc` . 사용자가 특별히 유지해야 한다고 지정하더라도 실패한 스크립트는 유지되지 않습니다.

* 실행 중에 스크립트에서 사용하는 매개 변수를 수락할 수 있습니다.

* 클러스터 노드에서 루트 수준 권한으로 실행합니다.

* Azure Portal, Azure PowerShell, Azure CLI 또는 HDInsight .NET SDK를 통해 사용할 수 있습니다.

* VM에서 서비스 파일을 제거 하거나 수정 하는 스크립트 작업은 서비스 상태 및 가용성에 영향을 줄 수 있습니다.

클러스터에서 실행된 모든 스크립트에 대한 기록을 보관합니다. 이 기록은 승격 또는 강등 작업에 사용할 스크립트 ID를 찾아야 할 때 도움이 됩니다.

> [!IMPORTANT]  
> 스크립트 동작으로 인한 변경을 자동으로 실행 취소하는 방법은 없습니다. 변경 사항을 수동으로 되돌리거나 되돌린 스크립트를 제공하세요.

### <a name="script-action-in-the-cluster-creation-process"></a>클러스터 만들기 프로세스의 스크립트 동작

클러스터를 만드는 중에 사용되는 스크립트 동작은 기존 클러스터에서 실행되는 스크립트 동작과 약간 다릅니다.

* 스크립트는 자동으로 유지됩니다.

* 스크립트가 실패하면 클러스터 만들기 프로세스도 실패할 수 있습니다.

다음 다이어그램에서는 만들기 프로세스 중에 스크립트 동작이 실행되는 경우를 보여 줍니다.

![HDInsight 클러스터 사용자 지정 및 클러스터 만드는 동안의 단계][img-hdi-cluster-states]

HDInsight를 구성하는 동안 스크립트가 실행됩니다. 스크립트는 클러스터에 지정된 모든 노드에서 병렬로 실행됩니다. 노드에서 루트 권한으로 실행됩니다.

Apache Hadoop 관련 서비스를 포함 하 여 서비스 중지 및 시작과 같은 작업을 수행할 수 있습니다. 서비스를 중지 하는 경우 스크립트를 완료 하기 전에 Ambari 및 기타 Hadoop 관련 서비스가 실행 중인지 확인 합니다. 이러한 필수 서비스는 클러스터를 만드는 동안 클러스터의 상태를 결정 합니다.

클러스터를 만드는 동안 여러 스크립트 동작을 한 번에 사용할 수 있습니다. 이러한 스크립트는 지정된 순서로 호출됩니다.

> [!IMPORTANT]  
> 스크립트 작업은 60 분 이내에 완료 되어야 합니다. 그렇지 않으면 시간 제한이 초과 됩니다. 클러스터를 프로 비전 하는 동안 스크립트는 다른 설정 및 구성 프로세스와 동시에 실행 됩니다. CPU 시간 또는 네트워크 대역폭과 같은 리소스에 대한 경쟁으로 인해 스크립트를 완료하는 데 걸리는 시간이 개발 환경에서보다 더 오래 걸릴 수 있습니다.
>
> 스크립트를 실행하는 데 걸리는 시간을 최소화하려면 원본에서 애플리케이션을 다운로드하고 컴파일하는 작업을 수행하지 않습니다. 애플리케이션을 미리 컴파일하고 이진 파일을 Azure Storage에 저장합니다.

### <a name="script-action-on-a-running-cluster"></a>실행 중인 클러스터의 스크립트 작업

이미 실행 중인 클러스터의 스크립트 실패로 인해 클러스터가 실패 상태로 자동으로 변경 되지 않습니다. 스크립트가 완료되면 클러스터는 '실행 중' 상태로 돌아갑니다. 클러스터가 '실행 중' 상태인 경우에도 실패한 스크립트가 손상되었을 수 있습니다. 예를 들어 스크립트는 클러스터에 필요한 파일을 삭제할 수 있습니다.

스크립트 동작은 루트 권한으로 실행됩니다. 스크립트를 클러스터에 적용 하기 전에 스크립트가 수행 하는 작업을 이해 해야 합니다.

스크립트가 클러스터에 적용되면 클러스터 상태가 **실행 중**에서 **수락됨**으로 변경됩니다. 그런 다음, **HDInsight 구성**으로 변경되고, 마지막으로 스크립트가 성공하면 **실행 중**으로 돌아갑니다. 스크립트 상태는 스크립트 동작 기록에 기록됩니다. 이 정보는 스크립트의 성공 여부를 알려 줍니다. 예를 들어 `Get-AzHDInsightScriptActionHistory` PowerShell cmdlet은 스크립트 상태를 표시합니다. 이 명령은 다음 텍스트와 비슷한 정보를 반환합니다.

```output
ScriptExecutionId : 635918532516474303
StartTime         : 8/14/2017 7:40:55 PM
EndTime           : 8/14/2017 7:41:05 PM
Status            : Succeeded
```

> [!IMPORTANT]  
> 클러스터를 만든 후에 클러스터 사용자, 관리자, 암호를 변경하면 이 클러스터에서 실행되는 스크립트 동작이 실패할 수 있습니다. 작업자 노드를 대상으로 하는 지속형 스크립트 동작이 있는 경우 클러스터 크기를 조정할 때 이러한 스크립트가 실패할 수 있습니다.

## <a name="example-script-action-scripts"></a>예제 스크립트 동작 스크립트

스크립트 동작 스크립트는 다음 유틸리티를 통해 사용할 수 있습니다.

* Azure portal
* Azure PowerShell
* Azure CLI
* HDInsight .NET SDK

HDInsight는 HDInsight 클러스터에서 다음 구성 요소를 설치하는 스크립트를 제공합니다.

| 이름 | 스크립트 |
| --- | --- |
| Azure Storage 계정 추가 |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. [HDInsight에 추가 스토리지 계정 추가](hdinsight-hadoop-add-storage.md) 참조 |
| Hue 설치 |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. [HDInsight Hadoop 클러스터에 Hue 설치 및 사용](hdinsight-hadoop-hue-linux.md) 참조 |
| Hive 라이브러리 미리 로드 |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. [HDInsight 클러스터를 만들 때 사용자 지정 Apache Hive 라이브러리 추가](hdinsight-hadoop-add-hive-libraries.md) 참조 |

## <a name="script-action-during-cluster-creation"></a>클러스터를 만드는 동안 스크립트 작업

이 섹션에서는 HDInsight 클러스터를 만들 때 스크립트 동작을 사용할 수 있는 다양한 방법에 대해 설명합니다.

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>클러스터를 만드는 동안 Azure Portal에서 스크립트 동작 사용

1. [Azure Portal를 사용 하 여 HDInsight에서 Linux 기반 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-portal.md)에서 설명한 대로 클러스터 만들기를 시작 합니다. **구성 + 가격 책정** 탭에서 **+ 스크립트 동작 추가**를 선택 합니다.

    ![Azure Portal 클러스터 스크립트 동작](./media/hdinsight-hadoop-customize-cluster-linux/azure-portal-cluster-configuration-scriptaction.png)

1. __스크립트 선택__ 항목을 사용하여 미리 만들어져 있는 스크립트를 선택합니다. 사용자 지정 스크립트를 사용하려면 __사용자 지정__을 선택합니다. 그런 다음, 스크립트에 대한 __이름__ 및 __Bash 스크립트 URI__를 제공합니다.

    ![엄선된 스크립트 양식에서 스크립트 추가](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    다음 표에서는 양식의 요소에 대해 설명합니다.

    | 속성 | 값 |
    | --- | --- |
    | 스크립트 선택 | 사용자 소유 스크립트를 사용하려면 __사용자 지정__을 선택합니다. 그렇지 않은 경우 제공된 스크립트 중 하나를 선택합니다. |
    | 이름 |스크립트 작업의 이름을 지정합니다. |
    | Bash 스크립트 URI |스크립트의 URI를 지정합니다. |
    | 헤드/작업자/사육 사 |스크립트가 실행 되는 노드 ( **헤드**, **작업자**또는 **사육 사**)를 지정 합니다. |
    | 매개 변수 |스크립트에 필요한 경우 매개 변수를 지정합니다. |

    __이 스크립트 동작을 유지__ 항목을 사용하여 크기 조정 작업 시 스크립트가 적용되도록 합니다.

1. __만들기__를 선택하여 스크립트를 저장합니다. 그런 다음, __+ 새로운 항목 제출__을 사용하여 다른 스크립트를 추가할 수 있습니다.

    ![HDInsight 다중 스크립트 작업](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png)

    스크립트를 추가 하는 작업이 완료 되 면 **구성 + 가격 책정** 탭으로 돌아갑니다.

1. 일반적인 방법으로 나머지 클러스터 만들기 단계를 완료 합니다.

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿에서 스크립트 동작 사용

스크립트 동작을 Azure Resource Manager 템플릿과 함께 사용할 수 있습니다. 예를 들어 [HDInsight Linux 클러스터 만들기 및 스크립트 동작](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/)을 참조하세요.

이 예제에서는 다음 코드를 사용하여 스크립트 동작을 추가합니다.

```json
"scriptActions": [
    {
        "name": "setenvironmentvariable",
        "uri": "[parameters('scriptActionUri')]",
        "parameters": "headnode"
    }
]
```

템플릿을 배포하는 방법에 대한 자세한 내용은 다음을 참조하세요.

* [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

* [Resource Manager 템플릿과 Azure CLI로 리소스 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>클러스터를 만드는 동안 Azure PowerShell에서 스크립트 동작 사용

이 섹션에서는 [AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) cmdlet을 사용 하 여 클러스터를 사용자 지정 하는 스크립트를 호출 합니다. 시작하기 전에 Azure PowerShell을 설치하고 구성해야 합니다. 이러한 PowerShell 명령을 사용 하려면 [AZ 모듈이](https://docs.microsoft.com/powershell/azure/)필요 합니다.

다음 스크립트에서는 PowerShell을 사용하여 클러스터를 만들 때 스크립트 동작을 적용하는 방법을 보여 줍니다.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

클러스터가 생성되는 데 몇 분 정도 걸릴 수 있습니다.

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>클러스터를 만드는 동안 HDInsight .NET SDK에서 스크립트 동작 사용

HDInsight .NET SDK는 .NET 애플리케이션에서 HDInsight를 더 쉽게 사용할 있게 하는 클라이언트 라이브러리를 제공합니다. 코드 샘플은 [스크립트 작업](/dotnet/api/overview/azure/hdinsight?view=azure-dotnet&preserve-view=true#script-actions)을 참조 하세요.

## <a name="script-action-to-a-running-cluster"></a>실행 중인 클러스터에 대 한 작업 스크립팅

이 섹션에서는 실행 중인 클러스터에 스크립트 동작을 적용하는 방법에 대해 설명합니다.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>Azure Portal에서 실행 중인 클러스터에 스크립트 동작 적용

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 고 클러스터를 찾습니다.

1. 기본 보기의 **설정** 아래에서 **스크립트 동작**을 선택합니다.

1. **스크립트 동작** 페이지의 위쪽에서 **+ 새로운 항목 제출**을 선택합니다.

    ![실행 중인 클러스터에 스크립트 적용](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

1. __스크립트 선택__ 항목을 사용하여 미리 만들어져 있는 스크립트를 선택합니다. 사용자 지정 스크립트를 사용하려면 __사용자 지정__을 선택합니다. 그런 다음, 스크립트에 대한 __이름__ 및 __Bash 스크립트 URI__를 제공합니다.

    ![엄선된 스크립트 양식에서 스크립트 추가](./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png)

    다음 표에서는 양식의 요소에 대해 설명합니다.

    | 속성 | 값 |
    | --- | --- |
    | 스크립트 선택 | 사용자 고유의 스크립트를 사용 하려면 __사용자 지정__을 선택 합니다. 그렇지 않은 경우 제공된 스크립트를 선택합니다. |
    | 이름 |스크립트 작업의 이름을 지정합니다. |
    | Bash 스크립트 URI |스크립트의 URI를 지정합니다. |
    | Head/Worker/Zookeeper |스크립트가 실행 되는 노드 ( **헤드**, **작업자**또는 **사육 사**)를 지정 합니다. |
    | 매개 변수 |스크립트에 필요한 경우 매개 변수를 지정합니다. |

    __이 스크립트 작업을 유지__ 항목을 사용하여 크기 조정 작업 시 스크립트가 적용되도록 합니다.

1. 마지막으로 **만들기** 단추를 선택하여 스크립트를 클러스터에 적용합니다.

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>Azure PowerShell에서 실행 중인 클러스터에 스크립트 동작 적용

이러한 PowerShell 명령을 사용 하려면 [AZ 모듈이](https://docs.microsoft.com/powershell/azure/)필요 합니다. 다음 예제에서는 스크립트 동작을 실행 중인 클러스터에 적용하는 방법을 보여 줍니다.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

작업이 완료되면 다음 텍스트와 비슷한 정보가 표시됩니다.

```output
OperationState  : Succeeded
ErrorMessage    :
Name            : Giraph
Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
Parameters      :
NodeTypes       : {HeadNode, WorkerNode}
```

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>Azure CLI에서 실행 중인 클러스터에 스크립트 동작 적용

시작하기 전에 Azure CLI를 설치하고 구성해야 합니다. 최신 버전이 있는지 확인합니다. 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

1. Azure 구독을 인증합니다.

    ```azurecli
    az login
    ```

1. 실행 중인 클러스터에 스크립트 동작을 적용합니다.

    ```azurecli
    az hdinsight script-action execute --cluster-name CLUSTERNAME --name SCRIPTNAME --resource-group RESOURCEGROUP --roles ROLES
    ```

    유효한 역할은,, `headnode` `workernode` `zookeepernode` , `edgenode` 입니다. 스크립트를 여러 노드 형식에 적용 해야 하는 경우에는 역할을 공백으로 구분 합니다. 정의합니다(예: `--roles headnode workernode`).

    스크립트를 유지하려면 `--persist-on-success`를 추가합니다. 나중에 `az hdinsight script-action promote`을(를) 사용하여 스크립트를 지속할 수도 있습니다.

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>REST API를 사용하여 실행 중인 클러스터에 스크립트 동작 적용

[Azure HDInsight의 클러스터 REST API](https://msdn.microsoft.com/library/azure/mt668441.aspx)를 참조하세요.

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>HDInsight .NET SDK에서 실행 중인 클러스터에 스크립트 동작 적용

.NET SDK를 사용하여 스크립트를 클러스터에 적용하는 예제는 [실행 중인 Linux 기반 HDInsight 클러스터에 스크립트 동작 적용](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)을 참조하세요.

## <a name="view-history-and-promote-and-demote-script-actions"></a>기록 보기, 스크립트 동작 승격 및 강등

### <a name="the-azure-portal"></a>Azure Portal

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 고 클러스터를 찾습니다.

1. 기본 보기의 **설정** 아래에서 **스크립트 동작**을 선택합니다.

1. 이 클러스터에 대한 스크립트 기록이 스크립트 동작 섹션에 표시됩니다. 이 정보에는 지속된 스크립트 목록이 포함되어 있습니다. 다음 스크린샷에서는 이 클러스터에서 Solr 스크립트가 실행되었음을 보여 줍니다. 이 스크린샷에는 지속형 스크립트가 보이지 않습니다.

    ![포털 스크립트 작업 제출 기록](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

1. 기록에서 스크립트를 선택하여 이 스크립트의 **속성** 섹션을 표시합니다. 화면 맨 위에서 스크립트를 다시 실행하거나 승격할 수 있습니다.

    ![스크립트 동작 속성 수준 올리기](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

1. 스크립트 동작 섹션의 항목 오른쪽에 있는 **줄임표 (...**)를 선택 하 여 작업을 수행할 수도 있습니다.

    ![지속형 스크립트 동작 삭제](./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png)

### <a name="azure-powershell"></a>Azure PowerShell

| Cmdlet | 함수 |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |지속형 스크립트 동작에 대한 정보를 검색합니다. 이 cmdlet은 스크립트에 의해 수행 된 작업을 실행 취소 하지 않으며 지속형 플래그만 제거 합니다.|
| `Get-AzHDInsightScriptActionHistory` |클러스터에 적용된 스크립트 동작의 기록 또는 특정 스크립트에 대한 세부 정보를 검색합니다. |
| `Set-AzHDInsightPersistedScriptAction` |`ad hoc`스크립트 동작을 지속형 스크립트 동작으로 승격 합니다. |
| `Remove-AzHDInsightPersistedScriptAction` |지속형 스크립트 동작의 수준을 작업으로 내립니다 `ad hoc` . |

다음 예제 스크립트에서는 cmdlet을 사용하여 스크립트를 승격한 다음, 강등하는 방법을 보여 줍니다.

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="azure-cli"></a>Azure CLI

| 명령 | 설명 |
| --- | --- |
| [`az hdinsight script-action delete`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-delete) |클러스터의 지정 된 지속형 스크립트 작업을 삭제 합니다. 이 명령은 스크립트에 의해 수행 된 작업을 실행 취소 하지 않으며 지속형 플래그만 제거 합니다.|
|[`az hdinsight script-action execute`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-execute)|지정된 HDInsight 클러스터에서 스크립트 동작을 실행합니다.|
| [`az hdinsight script-action list`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-list) |지정 된 클러스터에 대해 지속형 스크립트 작업을 모두 나열 합니다. |
|[`az hdinsight script-action list-execution-history`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-list-execution-history)|지정 된 클러스터에 대 한 모든 스크립트 실행 기록을 나열 합니다.|
|[`az hdinsight script-action promote`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-promote)|지속형 스크립트에 지정 된 임시 스크립트 실행을 승격 합니다.|
|[`az hdinsight script-action show-execution-details`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-show-execution-details)|지정 된 스크립트 실행 ID에 대 한 스크립트 실행 세부 정보를 가져옵니다.|

### <a name="hdinsight-net-sdk"></a>HDInsight .NET SDK

.NET SDK를 사용하여 클러스터에서 스크립트 기록을 검색하거나 스크립트를 승격 또는 강등하는 예제는 [실행 중인 Linux 기반 HDInsight 클러스터에 스크립트 동작 적용](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)을 참조하세요.

> [!NOTE]  
> 이 예제에서는 .NET SDK를 사용하여 HDInsight 애플리케이션을 설치하는 방법도 보여 줍니다.

## <a name="next-steps"></a>다음 단계

* [HDInsight용 스크립트 동작 스크립트 개발](hdinsight-hadoop-script-actions-linux.md)
* [HDInsight 클러스터에 추가 스토리지 추가](hdinsight-hadoop-add-storage.md)
* [스크립트 동작 문제 해결](troubleshoot-script-action.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png "클러스터를 만드는 동안의 단계"
