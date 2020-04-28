---
title: HDInsight에서 Hadoop을 통해 Windows PC 사용 - Azure
description: HDInsight에서 Hadoop의 Windows PC에서 작업. PowerShell, Visual Studio 및 Linux 도구를 사용하는 클러스터를 관리 및 쿼리합니다. .NET을 사용하는 빅 데이터 솔루션을 개발합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/20/2019
ms.openlocfilehash: 3ec50acc693452fe73d929effcea98b12fc5ff8b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "75933944"
---
# <a name="work-in-the-apache-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>Windows PC에서 HDInsight의 Apache Hadoop 에코시스템 작업

HDInsight의 Apache Hadoop 에코시스템 작업을 위한 Windows PC의 개발 및 관리 옵션에 대해 알아봅니다.

HDInsight는 Apache Hadoop 및 Hadoop 구성 요소, Linux에서 개발된 오픈 소스 기술을 기반으로 합니다. HDInsight 버전 3.4 이상에서는 클러스터에 대한 기본 OS로 Ubuntu Linux 배포를 사용합니다. 그러나 Windows 클라이언트 또는 Windows 개발 환경에서 HDInsight로 작업할 수 있습니다.

## <a name="use-powershell-for-deployment-and-management-tasks"></a>PowerShell을 사용하여 배포 및 관리 작업

Azure PowerShell은 Windows에서 HDInsight의 배포 및 관리 작업을 제어하고 자동화하는 데 사용할 수 있는 스크립팅 환경입니다.

PowerShell로 수행할 수 있는 작업의 예:

* [PowerShell을 사용 하 여 클러스터를 만듭니다](hdinsight-hadoop-create-linux-clusters-azure-powershell.md).
* [PowerShell을 사용 하 여 Apache Hive 쿼리를 실행](hadoop/apache-hadoop-use-hive-powershell.md)합니다.
* [PowerShell을 사용 하 여 클러스터를 관리](hdinsight-administer-use-powershell.md)합니다.

[Azure Powershell 설치 및 구성](https://docs.microsoft.com/powershell/azure/install-az-ps) 단계에 따라 최신 버전을 가져옵니다.

## <a name="utilities-you-can-run-in-a-browser"></a>브라우저에서 실행할 수 있는 유틸리티

다음 유틸리티는 브라우저에서 실행되는 웹 UI가 있습니다.
* **[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)** 은 브라우저에서 실행 되 고 Azure Portal 내에서 실행 되는 대화형 명령줄 셸입니다.

* **[Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md)** 는 다음과 같은 다양한 종류의 작업을 관리하는 데 사용할 수 있으며 Azure Portal에서 사용 가능한 관리 및 모니터링 유틸리티입니다.
    * [REST API로 Apache Ambari 사용](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Apache Ambari에서 Apache Hive 보기](hadoop/apache-hadoop-use-hive-ambari-view.md)
    * [Apache Ambari에서 Apache Tez 보기](hdinsight-debug-ambari-tez-view.md)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>Visual Studio용 Data Lake(Hadoop) 도구

Visual Studio용 Data Lake 도구를 사용하여 Storm 토폴로지를 배포 및 관리합니다. Data Lake 도구는 또한 Visual Studio와 함께 C# Storm 토폴로지를 개발할 수 있는 SCP.NET SDK를 설치합니다.

다음 예제를 진행하기 전에 [Visual Studio용 Data Lake 도구를 설치 및 시도](hadoop/apache-hadoop-visual-studio-tools-get-started.md)합니다.

다음은 Visual Studio용 Data Lake 도구와 Visual Studio를 사용할 수 있는 작업 예제입니다.
* [Visual Studio에서 Storm 토폴로지 배포 및 관리](storm/apache-storm-deploy-monitor-topology-linux.md)
* [Visual Studio를 사용하여 Storm용 C# 토폴로지를 개발합니다](storm/apache-storm-develop-csharp-visual-studio-topology.md). 비트에는 Azure Cosmos DB 및 SQL Database와 같은 데이터베이스에 연결할 수 있는 Storm 토폴로지를 위한 예제 템플릿이 포함됩니다.

## <a name="visual-studio-and-the-net-sdk"></a>Visual Studio 및 .NET SDK

.NET SDK와 함께 Visual Studio를 사용하여 클러스터를 관리하고 빅 데이터 애플리케이션을 개발할 수 있습니다. 다음 작업에 대해 다른 IDE를 사용할 수 있으나 예제는 Visual Studio에 표시됩니다.

Visual Studio에서 .NET SDK와 함께 수행할 수 있는 작업의 예:
* [.Net 용 Azure HDINSIGHT SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet)
* [.NET SDK를 사용 하 여 Apache Hive 쿼리를 실행](hadoop/apache-hadoop-use-hive-dotnet-sdk.md)합니다.
* [Apache Hadoop에서 Apache Hive 및 Apache Pig streaming과 함께 c # 사용자 정의 함수를 사용](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)합니다.

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>Spark 클러스터에 대한 Intellij IDEA 및 Eclipse IDE

[Intellij IDEA](https://www.jetbrains.com/idea/download) 및 [Eclipse IDE](https://www.eclipse.org/downloads/)는 다음에 사용할 수 있습니다.
* HDInsight Spark 클러스터에서 Scala Spark 애플리케이션을 개발 및 제출합니다.
* Spark 클러스터 리소스에 액세스합니다.
* Scala Spark 애플리케이션을 로컬로 개발 및 실행합니다.

다음 문서에 방법이 나와 있습니다.
* Intellij 아이디어: [Intellij 플러그 인과 SCALA SDK 용 Azure 도구 키트를 사용 하 여 Apache Spark 응용 프로그램을 만듭니다.](spark/apache-spark-intellij-tool-plugin.md)
* Eclipse IDE 또는 Eclipse 용 Scala IDE: [Apache Spark 응용 프로그램 및 Azure Toolkit for Eclipse 만들기](spark/apache-spark-eclipse-tool-plugin.md)

## <a name="notebooks-on-spark-for-data-scientists"></a>데이터 과학자들을 위한 Spark의 Notebook

HDInsight의 Apache Spark 클러스터는 Jupyter Notebook과 함께 사용할 수 있는 Apache Zeppelin Notebook 및 커널을 포함합니다.

* [Apache Spark 클러스터에서 Jupyter Notebook과 함께 커널을 사용하여 Spark 애플리케이션을 테스트하는 방법 알아보기](spark/apache-spark-zeppelin-notebook.md)
* [Apache Spark 클러스터에서 Apache Zeppelin Notebook을 사용하여 Spark 작업을 실행하는 방법 알아보기](spark/apache-spark-jupyter-notebook-kernels.md)

## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Windows에서 Linux 기반 도구 및 기술 실행

Linux 에서만 사용할 수 있는 도구 또는 기술을 사용 해야 하는 상황이 발생 하는 경우 다음 옵션을 고려 하십시오.

* **Windows 10의 Ubuntu에 있는 Bash** 는 Windows에서 Linux 하위 시스템을 제공 합니다. Bash를 사용하면 전용 Linux 설치를 유지하지 않고도 Linux 유틸리티를 직접 실행할 수 있습니다. [Windows 10을 위한 Linux용 Windows 하위 시스템 설치 가이드](https://docs.microsoft.com/windows/wsl/install-win10)에서 설치 단계를 참조하세요.  다른 [Unix 셸](https://www.gnu.org/software/bash/)도 작동합니다.
* **Windows용 Docker**는 대부분의 Linux 기반 도구에 대한 액세스를 제공하며 Windows에서 직접 실행할 수 있습니다. 예를 들어, Docker를 사용하여 Hive에 대한 Beeline 클라이언트를 Windows에서 직접 실행할 수 있습니다. 또한 Docker를 사용하여 로컬 Jupyter Notebook을 실행하고 HDInsight의 Spark에 원격으로 연결할 수 있습니다. [Windows용 Docker 시작](https://docs.docker.com/docker-for-windows/)
* **[MobaXTerm](https://mobaxterm.mobatek.net/)** 을 사용하면 그래픽 방식으로 SSH 연결을 통해 클러스터 파일 시스템을 찾아볼 수 있습니다.

## <a name="cross-platform-tools"></a>플랫폼 간 도구

Azure CLI(명령줄 인터페이스)는 Azure 리소스를 관리하기 위한 Microsoft의 플랫폼 간 명령줄 환경입니다.  자세한 내용은 [Azure 명령줄 인터페이스 (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

Linux 기반 클러스터에서 작업하는 데 익숙하지 않은 경우 다음 문서를 참조하세요.
* [Apache Hadoop, Apache Kafka, Apache Spark 또는 기타 클러스터 설정](hdinsight-hadoop-provision-linux-clusters.md)
* [Linux의 HDInsight 클러스터에 대한 팁](hdinsight-hadoop-linux-information.md)
