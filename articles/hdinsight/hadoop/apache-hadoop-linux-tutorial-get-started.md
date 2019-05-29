---
title: '빠른 시작: Resource Manager를 사용하여 Apache Hadoop 클러스터 만들기 - Azure HDInsight'
description: HDInsight 클러스터를 만드는 방법을 알아봅니다.
keywords: Hadoop 시작, Hadoop Linux, Hadoop 빠른 시작, Hive 시작, Hive 빠른 시작
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: quickstart
ms.date: 05/14/2019
ms.openlocfilehash: 9084e8e0fe7707b5f115df80792cea102772b8e9
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65861639"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-resource-manager-template"></a>빠른 시작: Azure HDInsight에서 Resource Manager 템플릿을 사용하여 Apache Hadoop 클러스터 만들기

이 빠른 시작에서는 Azure HDInsight에서 Resource Manager 템플릿을 사용하여 Apache Hadoop 클러스터를 만드는 방법을 알아봅니다.

비슷한 템플릿은 [Azure 퀵 스타트 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)에서 볼 수 있습니다. 템플릿 참조는 [여기](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/allversions)서 찾을 수 있습니다.  또한 [Azure Portal](apache-hadoop-linux-create-cluster-get-started-portal.md)을 사용하여 클러스터를 만들 수도 있습니다.  

현재 HDInsight는 [일곱 가지 클러스터 형식](./apache-hadoop-introduction.md#cluster-types-in-hdinsight)으로 제공됩니다. 각 클러스터 유형은 서로 다른 구성 요소 집합을 지원합니다. 모든 클러스터 형식은 Hive를 지원합니다. HDInsight에서 지원되는 구성 요소 목록은 [HDInsight에서 제공하는 Hadoop 클러스터 버전의 새로운 기능](../hdinsight-component-versioning.md)  

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

<a name="create-cluster"></a>
## <a name="create-a-hadoop-cluster"></a>Hadoop 클러스터 만들기

1. 아래 **Azure에 배포** 단추를 선택하여 Azure에 로그인하고 Azure Portal에서 Resource Manager 템플릿을 엽니다.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-ssh-password%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hadoop-linux-tutorial-get-started/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. 다음 값을 입력하거나 선택합니다.

    |자산  |설명  |
    |---------|---------|
    |**구독**     |  Azure 구독을 선택합니다. |
    |**리소스 그룹**     | 리소스 그룹을 만들거나 기존 리소스 그룹을 선택합니다.  리소스 그룹은 Azure 구성 요소의 컨테이너입니다.  이 경우 리소스 그룹에는 HDInsight 클러스터 및 종속 Azure Storage 계정이 포함되어 있습니다. |
    |**위치**:     | 클러스터를 만들려는 Azure 위치를 선택합니다.  성능 향상을 위해 가까운 곳을 선택합니다. |
    |**클러스터 이름**     | Hadoop 클러스터에 사용할 이름을 입력합니다. HDInsight의 모든 클러스터는 동일한 DNS 네임스페이스를 공유하므로 이 이름은 고유해야 합니다. 이름은 소문자, 숫자 및 하이픈만 포함할 수 있으며 문자로 시작해야 합니다.  각 하이픈의 앞과 뒤에는 하이픈이 아닌 문자가 있어야 합니다.  또한 이름은 3~59자여야 합니다. |
    |**클러스터 유형**     | **hadoop**을 선택합니다. |
    |**클러스터 로그인 이름 및 암호**     | 기본 로그인 이름은 **admin**입니다. 암호는 10자 이상이어야 하며, 숫자, 대문자, 소문자 및 영숫자가 아닌 문자(' " ` 문자 제외\)를 각각 하나 이상 포함해야 합니다. "Pass@word1"과 같은 일반적인 암호를 **제공하지 않았는지** 확인합니다.|
    |**SSH 사용자 이름 및 암호**     | 기본 사용자 이름은 **sshuser**입니다.  SSH 사용자 이름은 바꿀 수 있습니다.  SSH 사용자 암호에는 클러스터 로그인 암호와 동일한 요구 사항이 적용됩니다.|

    일부 속성이 템플릿에 하드 코딩되었습니다.  템플릿에서 이러한 값을 구성할 수 있습니다. 이러한 속성에 대한 자세한 설명은 [HDInsight에서 Apache Hadoop 클러스터 만들기](../hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.

    > [!NOTE]  
    > 사용자가 제공하는 값은 고유해야 하며 명명 지침을 따라야 합니다. 템플릿은 유효성 검사를 수행하지 않습니다. 사용자가 제공하는 값이 이미 사용 중이거나 지침을 준수하지 않는 경우 템플릿을 제출한 후 오류가 발생합니다.  

    ![HDInsight Linux 포털에서 Resource Manager 템플릿 시작](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png "Azure Portal과 리소스 그룹 관리자 템플릿을 사용하여 HDInsight에 Hadoop 클러스터 배포")

3. **위에 명시된 사용 약관에 동의함**을 선택한 다음, **구매**를 선택합니다. 배포가 진행 중이라는 알림이 표시됩니다.  클러스터를 만들려면 20분 정도가 걸립니다.

4. 클러스터가 만들어지면 **리소스 그룹으로 이동** 링크가 포함된 **배포 성공** 알림이 표시됩니다.  **리소스 그룹** 페이지에 새 HDInsight 클러스터와 해당 클러스터에 연결된 기본 스토리지가 나열됩니다. 각 클러스터에는 [Azure Storage 계정](../hdinsight-hadoop-use-blob-storage.md) 또는 [Azure Data Lake Storage 계정](../hdinsight-hadoop-use-data-lake-store.md) 종속성이 있습니다. 이 저장소 계정을 기본 저장소 계정이라고 합니다. HDInsight 클러스터와 해당 기본 스토리지 계정은 같은 Azure 지역에 있어야 합니다. 클러스터를 삭제해도 저장소 계정은 삭제되지 않습니다.

> [!NOTE]  
> 기타 클러스터 생성 방법 및 이 빠른 시작에 사용된 속성에 대한 이해는 [HDInsight 클러스터 만들기](../hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.

## <a name="troubleshoot"></a>문제 해결

HDInsight 클러스터를 만드는 동안 문제가 발생할 경우 [액세스 제어 요구 사항](../hdinsight-hadoop-customize-cluster-linux.md#access-control)을 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리
빠른 시작을 완료한 후 클러스터를 삭제하는 것이 좋습니다. HDInsight를 사용하면 데이터가 Azure Storage에 저장되기 때문에 클러스터를 사용하지 않을 때 안전하게 삭제할 수 있습니다. HDInsight 클러스터를 사용하지 않는 기간에도 요금이 청구됩니다. 클러스터에 대한 요금이 저장소에 대한 요금보다 몇 배 더 많기 때문에, 클러스터를 사용하지 않을 때는 삭제하는 것이 경제적인 면에서 더 합리적입니다.

> [!NOTE]  
> HDInsight에서 Hadoop을 사용하여 ETL 작업을 실행하는 방법을 알아보기 위해 다음 자습서를 *즉시* 진행하는 경우 클러스터가 실행되도록 유지할 수 있습니다. 자습서에서 Hadoop 클러스터를 다시 만들어야 하기 때문입니다. 그러나 다음 자습서로 바로 진행하지 않는 경우 이제 클러스터를 삭제해야 합니다.

**클러스터 및/또는 기본 저장소 계정을 삭제하려면**

1. Azure Portal이 있는 브라우저 탭으로 돌아갑니다. 그러면 클러스터 개요 페이지가 표시됩니다. 클러스터는 삭제하지만 기본 저장소 계정은 유지하려는 경우 **삭제**를 선택합니다.

    ![HDInsight 클러스터 삭제](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "HDInsight 클러스터 삭제")

2. 클러스터와 기본 저장소 계정을 삭제하려는 경우 리소스 그룹 이름(이전 스크린샷에서 강조 표시됨)을 선택하여 리소스 그룹 페이지를 엽니다.

3. **리소스 그룹 삭제**를 선택하여 클러스터와 기본 저장소 계정을 포함하는 리소스 그룹을 삭제합니다. 리소스 그룹을 삭제하면 저장소 계정이 삭제됩니다. 저장소 계정을 유지하려면 클러스터만 삭제하세요.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 HDInsight에서 Resource Manager 템플릿을 사용하여 Apache Hadoop 클러스터를 만드는 방법을 알아보았습니다. 다음 문서에서는 HDInsight의 Hadoop을 사용하여 ETL(추출, 변환 및 로드) 작업을 수행하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
>[HDInsight에서 Apache Hive를 사용하여 데이터 추출, 변환 및 로드](../hdinsight-analyze-flight-delay-data-linux.md)

자신의 데이터로 작업을 시작할 준비가 된 상태에서 HDInsight에서 데이터를 저장하는 방법과 HDInsight로 데이터를 가져오는 방법에 대해 더 알아보려면 다음 문서를 참조하세요.

* HDInsight에서 Azure Storage를 사용하는 방법에 대한 자세한 내용은 [HDInsight에서 Azure Storage 사용](../hdinsight-hadoop-use-blob-storage.md)을 참조하세요.
* Data Lake Storage를 사용하여 HDInsight 클러스터를 만드는 방법에 대한 자세한 내용은 [빠른 시작: HDInsight에서 클러스터 설정](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)을 참조하세요.
* 데이터를 HDInsight로 업로드하는 방법에 대한 정보는 [HDInsight에 데이터 업로드](../hdinsight-upload-data.md)를 참조하세요.
* [Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 사용](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

HDInsight를 사용하여 데이터를 분석하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* Visual Studio에서 Hive 쿼리를 수행하는 방법을 비롯하여 HDInsight로 Hive를 사용하는 데 대한 자세한 내용은 [HDInsight에서 Apache Hive 사용](hdinsight-use-hive.md)을 참조하세요.
* 데이터를 변환하는 데 사용되는 언어인 Pig에 대해 알아보려면 [HDInsight에서 Apache Pig 사용](hdinsight-use-pig.md)을 참조하세요.
* Hadoop에서 데이터를 처리하는 프로그램을 작성하는 방법인 MapReduce에 대해 알아보려면 [HDInsight로 MapReduce 사용](hdinsight-use-mapreduce.md)을 참조하세요.
* Visual Studio에 HDInsight Tools를 사용하여 HDInsight의 데이터를 분석하는 방법에 대해 알아보려면 [HDInsight용 Visual Studio Hadoop 도구를 사용하여 시작](apache-hadoop-visual-studio-tools-get-started.md)을 참조하세요.
* HDInsight Tools for VSCode를 사용하여 HDInsight의 데이터를 분석하는 방법에 대해 알아보려면 [Azure HDInsight Tools for Visual Studio Code 사용](../hdinsight-for-vscode.md)을 참조하세요.


HDInsight 클러스터를 만들거나 관리하는 방법에 대해 자세히 알아보려면 다음 문서를 참조하세요.

* Linux 기반 HDInsight 클러스터 관리에 대해 알아보려면 [Apache Ambari를 사용하여 HDInsight 클러스터 관리](../hdinsight-hadoop-manage-ambari.md)를 참조하세요.
* HDInsight 클러스터를 만들 때 선택하는 옵션에 대해 자세히 알아보려면 [사용자 지정 옵션을 사용하여 Linux에 HDInsight 만들기](../hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.

Azure Resource Manager 템플릿을 사용하여 HDInsight 클러스터를 만드는 방법에 대한 자세한 내용은 다음 항목을 참조하세요.

* [Azure 퀵 스타트 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)
* [Azure 템플릿 참조](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/allversions)

[1]: ../HDInsight/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md