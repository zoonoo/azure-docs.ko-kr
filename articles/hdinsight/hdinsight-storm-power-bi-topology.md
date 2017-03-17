---
title: "Power BI로 Apache Storm 사용 | Microsoft Docs"
description: "HDInsight의 Apache Storm 클러스터에서 실행 중인 C# 토폴로지로부터 데이터를 사용하여 Power BI 보고서를 만듭니다."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 36fe3b9c-5232-4464-8d75-95403b6da7a1
ms.service: hdinsight
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/01/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 7c28fda22a08ea40b15cf69351e1b0aff6bd0a95
ms.openlocfilehash: 75ab31176abaeed2865a77689a5733666f95a253
ms.lasthandoff: 03/07/2017


---
# <a name="use-power-bi-to-visualize-data-from-an-apache-storm-topology"></a>Power BI를 사용하여 Apache Storm 토폴로지에서 데이터 시각화

Power BI를 사용하면 데이터를 보고서로 시각적으로 표시할 수 있습니다. 이 문서는 HDInsight의 Apache Storm을 사용하여 Power BI에 대한 데이터를 생성하는 방법의 예를 제공합니다.

> [!NOTE]
> 이 문서의 단계는 Visual Studio가 설치된 Windows 개발 환경을 사용하지만 컴파일된 프로젝트는 Linux 또는 Windows 기반 HDInsight 클러스터로 전송될 수 있습니다. 2016년 10월 28일 이후 생성된 Linux 기반 클러스터만 SCP.NET 토폴로지를 지원합니다.
> 
> Linux 기반 클러스터에 C# 토폴로지를 사용하려면 프로젝트에 사용되는 Microsoft.SCP.Net.SDK NuGet 패키지를 0.10.0.6 버전 이상으로 업데이트합니다. 패키지 버전은 HDInsight에 설치된 Storm의 주 버전과도 일치해야 합니다. 예를 들어 HDInsight에서 Storm 버전 3.3 및 3.4는 Storm 버전 0.10.x를 사용하는 반면, HDInsight 3.5는 Storm 1.0.x를 사용합니다.
> 
> Linux 기반 클러스터의 C# 토폴로지는 .NET 4.5를 사용해야 하며 Mono를 사용하여 HDInsight 클러스터에서 실행해야 합니다. 대부분의 항목이 작동합니다. 하지만 [Mono 호환성](http://www.mono-project.com/docs/about-mono/compatibility/) 문서에서 잠재적인 비호환성이 있는지 확인해야 합니다.
> 
> Linux 기반 또는 Windows 기반 HDInsight로 작동하는 이 프로젝트의 Java 버전에 대해서는 [HDInsight의 Storm으로 Azure 이벤트 허브에서 이벤트 처리(Java)](hdinsight-storm-develop-java-event-hub-topology.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

* [Power BI](https://powerbi.com) 액세스 권한이 있는 Azure Active Directory 사용자
* HDInsight 클러스터. 자세한 내용은 [HDInsight에서 Storm 시작](hdinsight-apache-storm-tutorial-get-started-linux.md)을 참조하세요.

  > [!IMPORTANT]
  > Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중단](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)을 참조하세요.

* Visual Studio(다음 버전 중 하나)
  
  * Visual Studio 2012 [업데이트 4](http://www.microsoft.com/download/details.aspx?id=39305)
  * Visual Studio 2013 [업데이트 4](http://www.microsoft.com/download/details.aspx?id=44921) 또는 [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?linkid=517284&clcid=0x409)
  * [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
  * Visual Studio 2017(모든 버전)

* HDInsight Tools for Visual Studio: 설치 정보는 [HDInsight Tools for Visual Studio 사용 시작](hdinsight-hadoop-visual-studio-tools-get-started.md) 을 참조하세요.

## <a name="how-it-works"></a>작동 방법

이 예제에서는 IIS(인터넷 정보 서비스) 로그 데이터를 무작위로 생성하는 C# Storm 토폴로지를 포함합니다. 이 데이터는 SQL 데이터베이스에 기록되고 해당 위치에서 Power BI에 보고서를 생성하는 데 사용됩니다.

다음 파일은 이 예제의 주요 기능을 구현합니다.

* **SqlAzureBolt.cs**: SQL 데이터베이스에 대한 Storm 토폴로지에서 생성된 정보를 작성합니다.
* **IISLogsTable.sql**: 데이터가 저장되어 있는 데이터베이스를 생성하는 데 사용되는 Transact-SQL 문입니다.

> [!WARNING]
> HDInsight 클러스터에서 토폴로지를 시작하기 전에 SQL Database에 테이블을 만듭니다.

## <a name="download-the-example"></a>예제 다운로드

[HDInsight C# Storm Power BI 예제](https://github.com/Azure-Samples/hdinsight-dotnet-storm-powerbi)를 다운로드합니다. 다운로드하려면 [git](http://git-scm.com/)를 사용하여 포크/복제하거나, **다운로드** 링크를 사용하여 .zip 보관 파일을 다운로드합니다.

## <a name="create-a-database"></a>데이터베이스 만들기

1. 데이터베이스를 만들려면 [SQL Database 자습서](../sql-database/sql-database-get-started.md) 문서의 단계를 사용합니다.

2. [Visual Studio와 함께 SQL Database에 연결](../sql-database/sql-database-connect-query.md) 문서의 단계를 수행하여 데이터베이스에 연결합니다.

3. 개체 탐색기에서 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 선택합니다. 다운로드한 프로젝트에 포함된 **IISLogsTable.sql** 파일의 내용을 쿼리 창에 붙여 넣은 다음 Ctrl + Shift + E를 사용하여 쿼리를 실행합니다. 명령이 성공적으로 완료되었다는 메시지가 표시됩니다.

## <a name="configure-the-sample"></a>샘플 구성

1. [Azure 포털](https://portal.azure.com)에서 SQL 데이터베이스를 선택합니다. SQL 데이터베이스 블레이드의 **Essentials** 섹션에서 **데이터베이스 연결 문자열 표시**를 선택합니다. 표시되는 목록에서 **ADO.NET(SQL 인증)** 정보를 복사합니다.

2. Visual Studio에서 샘플을 엽니다. **솔루션 탐색기**에서 **App.config** 파일을 열고 다음 항목을 찾습니다.
   
        <add key="SqlAzureConnectionString" value="##TOBEFILLED##" />
   
    **##TOBEFILLED##** 값을 이전 단계에서 복사된 데이터베이스 연결 문자열과 바꿉니다. **{your\_username}** 및 **{your\_password}**를 데이터베이스에 대한 사용자 이름 및 암호와 바꿉니다.

3. 파일을 저장하고 닫습니다.

## <a name="deploy-the-sample"></a>샘플 배포

1. **솔루션 탐색기**에서 **StormToSQL** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **HDInsight에서 Storm에 제출**을 선택합니다. **Storm 클러스터** 드롭다운 대화 상자에서 HDInsight 클러스터를 선택합니다.
   
   > [!NOTE]
   > **Storm 클러스터** 드롭다운을 서버 이름으로 채우는 데 몇 초 정도 걸릴 수 있습니다.
   > 
   > 메시지가 표시되면 Azure 구독에 대한 로그인 자격 증명을 입력합니다. 하나 이상의 구독이 있는 경우 HDInsight 클러스터의 Storm을 포함하는 자격 증명으로 로그인합니다.

2. 토폴로지가 제출되었으면 __토폴로지 뷰어__가 나타납니다. 이 토폴로지를 보려면 목록에서 SqlAzureWriterTopology 항목을 선택합니다.
   
    ![토폴로지가 선택된 토폴로지](./media/hdinsight-storm-power-bi-topology/topologyview.png)
   
    이 보기를 사용하여 토폴로지에 대한 정보를 확인하거나 항목(예: SqlAzureBolt)을 두 번 클릭하여 토폴로지에서 구성 요소와 관련된 정보를 확인할 수 있습니다.

3. 몇 분 동안 토폴로지를 실행한 후에 데이터베이스를 만드는 데 사용한 SQL 쿼리 창으로 돌아갑니다. 기존 문을 다음 쿼리로 바꿉니다.
   
        select * from iislogs;
   
    Ctrl + Shift + E를 사용하여 쿼리를 실행하면 다음과 유사한 결과가 표시됩니다.
   
        1    2016-05-27 17:57:14.797    255.255.255.255    /bar    GET    200
        2    2016-05-27 17:57:14.843    127.0.0.1    /spam/eggs    POST    500
        3    2016-05-27 17:57:14.850    123.123.123.123    /eggs    DELETE    200
        4    2016-05-27 17:57:14.853    127.0.0.1    /foo    POST    404
        5    2016-05-27 17:57:14.853    10.9.8.7    /bar    GET    200
        6    2016-05-27 17:57:14.857    192.168.1.1    /spam    DELETE    200
   
    이 데이터는 Storm 토폴로지에서 작성되었습니다.

## <a name="create-a-report"></a>보고서 만들기

1. Power BI용 [Azure SQL 데이터베이스 커넥터](https://app.powerbi.com/getdata/bigdata/azure-sql-database-with-live-connect) 에 연결합니다.

2. **데이터베이스** 내에서 **가져오기**를 선택합니다.

3. **Azure SQL Database**를 선택한 다음 **연결**을 선택합니다.

4. 정보를 입력하여 Azure SQL 데이터베이스에 연결합니다. [Azure Portal](https://portal.azure.com)에 방문하고 SQL 데이터베이스를 선택하여 이 정보를 찾을 수 있습니다.
   
   > [!NOTE]
   > 연결 대화 상자에서 **고급 옵션 사용** 을 사용하여 새로 고침 간격 및 사용자 지정 필터를 설정할 수도 있습니다.
 
5. 연결한 후에 연결한 데이터베이스와 동일한 이름을 가진 새 데이터 집합이 표시됩니다. 데이터 집합을 선택하여 보고서를 만들기 시작합니다.

6. **필드**에서 **IISLOGS** 항목을 확장합니다. **URISTEM**에 대한 확인란을 선택합니다. 그러면 데이터베이스에 로그인한 URI 형태소(/foo, /bar, 등)를 나열한 보고서가 만들어집니다.
   
    ![보고서 만들기](./media/hdinsight-storm-power-bi-topology/createreport.png)

7. 다음으로 **메서드** 보고서를 끌어옵니다. 보고서는 HTTP 요청에 사용된 형태소 및 해당 HTTP 메서드를 목록에 업데이트합니다.
   
    ![메서드 데이터 추가](./media/hdinsight-storm-power-bi-topology/uristemandmethod.png)

8. **시각화** 열에서 **필드** 아이콘을 선택한 다음 **값** 섹션의 **메서드** 옆에 있는 아래쪽 화살표를 선택합니다. 표시되는 목록에서 **수**를 선택합니다. 이렇게 하면 보고서가 특정 URI에 액세스하는 횟수를 나열하도록 변경됩니다.
   
    ![메서드의 수 변경](./media/hdinsight-storm-power-bi-topology/count.png)

9. 다음으로 **누적 세로 막대형 차트** 를 선택하여 정보가 표시되는 방식을 변경합니다.
   
    ![누적된 차트 변경](./media/hdinsight-storm-power-bi-topology/stackedcolumn.png)

10. 보고서를 저장하려면 **저장**을 선택하고 보고서의 이름을 입력합니다.

## <a name="stop-the-topology"></a>토폴로지 중지

토폴로지는 사용자가 중지하거나 HDInsight 클러스터에서 Storm을 삭제할 때까지 계속 실행됩니다. 토폴로지를 중지하려면 다음 단계를 수행합니다.

1. Visual Studio에서 토폴로지 뷰어로 돌아가서 토폴로지를 선택합니다.

2. **Kill** 단추를 선택하여 토폴로지를 중지합니다.
   
    ![토폴로지 요약의 Kill 단추](./media/hdinsight-storm-power-bi-topology/killtopology.png)

## <a name="delete-your-cluster"></a>클러스터 삭제

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>다음 단계

이 문서에서는 Storm 토폴로지에서 SQL 데이터베이스로 데이터를 보낸 다음 Power BI를 사용하여 데이터를 시각화하는 방법을 알아보았습니다. HDInsight에서 Storm을 사용하여 다른 Azure 기술을 사용하는 방법에 대한 자세한 내용은 다음을 참조하세요.

* [HDInsight의 Storm에 대한 예제 토폴로지](hdinsight-storm-example-topology.md)


