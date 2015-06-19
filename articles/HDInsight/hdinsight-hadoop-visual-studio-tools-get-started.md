<properties 
	pageTitle="HDInsight용 Visual Studio Hadoop 도구를 사용하는 방법 알아보기 | Microsoft Azure" 
	description="HDInsight용 Visual Studio Hadoop 도구를 설치하고 이를 사용하여 Hadoop 클러스터에 연결한 후 Hive 쿼리를 실행하는 방법에 대해 알아봅니다." 
	keywords="hadoop tools,hive query,visual studio"
	services="HDInsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="big-data" 
	ms.date="04/08/2015" 
	ms.author="jgao"/>

# HDInsight용 Visual Studio Hadoop 도구를 사용하여 Hive 쿼리 실행 시작

Visual Studio용 HDInsight 도구를 사용하여 HDInsight 클러스터에 연결하고 Hive 쿼리를 제출하는 방법에 대해 알아봅니다. HDInsight 사용에 대한 자세한 내용은 [HDInsight 소개][hdinsight.introduction] 및 [HDInsight 시작][hdinsight.get.started]을 참조하세요. Storm 클러스터에 연결하는 방법에 대한 자세한 내용은 Visual Studio를 사용하여 [HDInsight에서 Apache Storm용 C# 토폴로지 개발][hdinsight.storm.visual.studio.tools]을 참조하세요.

>[AZURE.NOTE]최신 릴리스에는 Hive 편집기 Intellisense 지원, Hive 스크립트 로컬 유효성 검사 및 Yarn 로그 액세스와 같은 몇 가지 새로운 기능이 도입되었습니다.


## 필수 조건

이 자습서를 완료하고 Visual Studio에서 Hadoop 도구를 사용하려면 다음이 필요합니다.

- 다음 소프트웨어가 설치된 워크스테이션

	- Windows 8.1, Windows 8 또는 Windows 7
	- Visual Studio(다음 버전 중 하나)
		- Visual Studio 2012 Professional/Premium/Ultimate [업데이트 4](http://www.microsoft.com/download/details.aspx?id=39305)
		- Visual Studio 2013 Community/Professional/Premium/Ultimate [업데이트 4](https://www.microsoft.com/download/details.aspx?id=44921)
		- Visual Studio 2015 RC(Community/Enterprise)

	>[AZURE.NOTE]현재 Visual Studio용 HDInsight 도구는 영어 버전으로만 제공됩니다.


## Visual Studio용 Hadoop 도구 설치

Visual Studio용 HDInsight 도구는 Microsoft Azure SDK for .NET 버전 2.5.1 이상과 함께 제공됩니다. [웹 플랫폼 설치 관리자](http://go.microsoft.com/fwlink/?LinkId=255386)를 사용하여 설치할 수 있습니다. 사용 중인 Visual Studio 버전과 일치하는 버전을 선택해야 합니다. 이 Hadoop 도구 패키지는 Microsoft Hive ODBC 드라이버(32비트 및 64비트)도 설치합니다.

![Hadoop 도구: Visual Studio용 HDInsight 도구 웹 플랫폼 설치 관리자][1]


>[AZURE.NOTE]Visual Studio 2015 또는 2012가 있고 Azure SDK 2.5를 설치한 경우 최신 버전을 설치하기 전에 이전 버전을 수동으로 제거해야 합니다. Visual Studio 2013은 직접 업데이트를 지원합니다.

## Azure 구독에 연결
Visual Studio용 HDInsight 도구를 사용하면 HDInsight 클러스터에 연결하고, 기본적인 관리 작업을 수행하고, Hive 쿼리를 실행할 수 있습니다.

>[AZURE.NOTE]HDInsight Emulator 연결에 대한 자세한 내용은 [HDInsight Emulator 시작](../hdinsight-get-started-emulator.md/#vstools)을 참조하세요.

>[AZURE.NOTE]일반 Hadoop 클러스터(미리 보기) 연결에 대한 자세한 내용은 [Visual Studio를 사용하여 Hive 쿼리 작성 및 제출](http://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx)을 참조하세요.


**Azure 구독에 연결하려면**

1.	Visual Studio를 엽니다.
2.	**보기** 메뉴에서 **서버 탐색기**를 클릭하여 서버 탐색기 창을 엽니다.
3.	**Azure**를 확장한 다음 **HDInsight**를 확장합니다. 

	>[AZURE.NOTE]**HDInsight 작업 목록****다른 창****보기****HDInsight 작업 목록 창**  
4.	Azure 구독 자격 증명을 입력한 후 **로그인**을 클릭합니다. 이 과정은 이 워크스테이션에서 Visual Studio를 통해 Azure 구독에 연결한 적이 없는 경우에만 필요합니다.
5.	서버 탐색기에서 기존 HDInsight 클러스터 목록이 표시됩니다. 클러스터가 없는 경우 Azure 포털, Azure PowerShell 또는 HDInsight SDK를 사용하여 클러스터를 프로비전할 수 있습니다. 자세한 내용은 [HDInsight 클러스터 프로비전][hdinsight-provision]을 참조하세요.

	![Hadoop 도구: Visual Studio용 HDInsight 도구 서버 탐색기 클러스터 목록][5]
6.	HDInsight 클러스터를 확장합니다. **Hive 데이터베이스**, 기본 저장소 계정, 연결된 저장소 계정 및 **Hadoop 서비스 로그**를 볼 수 있습니다. 엔터티를 더 확장할 수 있습니다. 

Azure 구독에 연결한 후에는 다음을 수행할 수 있습니다.

**Visual Studio에서 Azure 포털에 연결하려면**

- 서버 탐색기에서 **Azure** > **HDInsight**를 확장하고, HDInsight 클러스터를 마우스 오른쪽 단추로 클릭한 후 **Azure 포털에서 클러스터 관리**를 클릭합니다.

**Visual Studio에서 질문하고 피드백을 제공하려면**

- **도구** 메뉴에서 **HDInsight**를 클릭한 후 **MSDN 포럼**을 클릭하여 질문하거나 **피드백 제공**을 클릭합니다.

## 연결된 리소스 탐색 

서버 탐색기에서 기본 저장소 계정 및 연결된 저장소 계정을 확인할 수 있습니다. 기본 저장소 계정을 확장한 경우 저장소 계정의 컨테이너를 확인할 수 있습니다. 기본 저장소 계정과 기본 컨테이너가 표시되어 있습니다. 컨테이너를 마우스 오른쪽 단추로 클릭하여 내용을 확인할 수도 있습니다.

![Visual Studio용 HDInsight 도구 서버 탐색기 클러스터 목록][2]

## HIVE 쿼리 실행
[Apache Hive][apache.hive]는 Hadoop을 기반으로 하는 데이터 웨어하우스 인프라로, 데이터 요약, 쿼리 및 분석 기능을 제공합니다. Visual Studio용 HDInsight 도구를 사용하면 Visual Studio에서 Hive 쿼리를 실행할 수 있습니다. Hive에 대한 자세한 내용은 [HDInsight와 함께 Hive 사용][hdinsight.hive]을 참조하십시오.

HDInsight 클러스터에 대해 Hive 스크립트를 테스트하려면 많은 시간이 걸립니다. 몇 분 이상 걸릴 수 있습니다. Visual Studio용 HDInsight 도구는 로컬로 라이브 클러스터에 연결하지 않고 Hive 스크립트의 유효성 검사를 할 수 있습니다.

Visual Studio용 HDInsight 도구를 사용하여 일부 Hive 작업의 YARN 로그를 수집하고 표시하여 사용자가 Hive 작업 내에 무엇이 있는지 볼 수 있습니다.

### **hivesampletable** 보기 
모든 HDInsight 클러스터에는 *hivesampletable*이라는 샘플 Hive 테이블이 함께 제공됩니다. 여기서는 이 테이블을 사용하여 Hive 테이블을 나열하고, 테이블 스키마를 보고, Hive 테이블의 행을 나열하는 방법을 보여 줍니다.



**Hive 테이블을 나열하고 Hive 테이블 스키마를 보려면**

1.	**서버 탐색기**에서 **Azure** > **HDInsight** > 선택한 클러스터 > **Hive 데이터베이스** > **기본값** > **hivesampletable**을 확장하여 테이블 스키마를 표시합니다.
4.	**hivesampletable**을 마우스 오른쪽 단추로 클릭한 후 **상위 100개 행 보기**를 클릭하여 행을 나열합니다. 이는 Hive ODBC 드라이버를 사용하여 다음 Hive 쿼리를 실행하는 것과 동일합니다.

		SELECT * FROM hivesampletable LIMIT 100

	행 개수를 사용자 지정할 수 있습니다.
 
	![Hadoop 도구: HDinsight Hive Visual Studio 스키마 쿼리][6]

### Hive 테이블 만들기

GUI를 사용하여 Hive 테이블을 만들거나 Hive 쿼리를 사용할 수 있습니다. Hive 쿼리 사용에 대한 자세한 내용은 [Hive 쿼리 실행](#run.queries)을 참조하세요.

**Hive 테이블을 만들려면**

1. **서버 탐색기**에서 **Azure** > **HDInsight 클러스터** > **Hive 데이터베이스**를 확장한 후 **기본값**을 마우스 오른쪽 단추로 클릭하고 **테이블 만들기**를 클릭합니다.
2. 테이블을 구성합니다.
3. **테이블 만들기**를 클릭하여 새 Hive 테이블을 만들기 위한 작업을 제출합니다.

	![Hadoop 도구: hdinsight visual studio 도구 hive 테이블 만들기][7]

### <a name="run.queries"></a>Hive 쿼리 유효성 검사 및 실행
다음 두 가지 방법으로 Hive 쿼리를 만들고 실행할 수 있습니다.

- 임시 쿼리 만들기
- Hive 응용 프로그램 만들기

**임시 쿼리를 만들고 유효성을 검사하고 실행하려면**

1. **서비스 탐색기**에서 **Azure**를 확장한 후 **HDInsight 클러스터**를 확장합니다.
2. 쿼리를 실행할 클러스터를 마우스 오른쪽 단추로 클릭하고 **Hive 쿼리 작성**을 클릭합니다. 
3. Hive 쿼리를 입력합니다. Hive 편집기는 IntelliSense를 지원합니다. Visual Studio용 HDInsight 도구는 Hive 스크립트를 편집할 때 원격 메타데이터 로드를 지원합니다. 예를 들어 "SELECT * FROM"을 입력하면 제안된 테이블 이름이 모두 나열됩니다. 테이블 이름을 지정하면 열 이름이 나열됩니다. 

	![Hadoop 도구: HDInsight Visual Studio 도구 Intellisense][13]

	![Hadoop 도구: HDInsight Visual Studio 도구 Intellisense][14]

	> [AZURE.NOTE]
4. (선택 사항) **스크립트 유효성 검사**를 클릭하여 스크립트 구문 오류를 확인합니다.

	![Hadoop 도구: Visual Studio용 HDInsight 도구 로컬 유효성 검사][10]

4. **제출** 또는 **제출(고급)**을 클릭합니다. 고급 제출 옵션을 사용할 경우 스크립트에 대한 **작업 이름**, **인수**, **추가 구성** 및 **상태 디렉터리**를 구성합니다.

	![hdinsight hadoop hive 쿼리][9]

	작업을 제출하면 **Hive 작업 요약** 창을 볼 수 있습니다.

	![HDInsight Hadoop Hive 쿼리 요약][8]
5. 작업 상태가 **완료**로 변경될 때까지 **새로 고침** 단추를 사용하여 상태를 새로 고칩니다.
6. 아래 쪽의 링크를 클릭하여 **작업 쿼리**, **작업 출력**, **작업 로그** 또는 **Yarn 로그**를 표시합니다.



**Hive 솔루션을 만들고 실행하려면**

1. **파일** 메뉴에서 **새로 만들기**와 **프로젝트**를 차례로 클릭합니다.
2. 왼쪽 창에서 **HDInsight**를 선택하고 가운데 창에서 **Hive 응용 프로그램**을 선택한 후 속성을 입력하고 **확인**을 클릭합니다.

	![Hadoop 도구: hdinsight visual studio 도구 새 hive 프로젝트][11]
3. **솔루션 탐색기**에서 **Script.hql**을 두 번 클릭하여 엽니다. 
4. Hive 스크립트의 유효성을 검사하려면 **스크립트 유효성 검사** 단추를 클릭하거나 Hive 편집기에서 스크립트를 마우스 오른쪽 단추로 클릭한 후 상황에 맞는 메뉴에서 **스크립트 유효성 검사**를 클릭합니다.

 
### Hive 작업 보기
Hive 작업에 대한 작업 쿼리, 작업 출력, 작업 로그 및 Yarn 로그를 볼 수 있습니다. 자세한 내용은 이전 스크린샷을 참조하세요.

최신 릴리스의 도구에서는 YARN 로그를 수집하고 표시하여 Hive 작업 내의 항목을 확인할 수 있습니다. YARN 로그를 사용하여 성능 문제를 조사할 수 있습니다. HDInsight가 YARN 로그를 수집하는 방법에 대한 자세한 내용은 [프로그래밍 방식으로 HDInsight 응용 프로그램 로그에 액세스][hdinsight.access.application.logs]를 참조하세요.

**Hive 작업을 보려면**

1. **서비스 탐색기**에서 **Azure**를 확장한 후 **HDInsight**를 확장합니다. 
2. HDInsight 클러스터를 마우스 오른쪽 단추로 클릭한 다음 **Hive 작업 보기**를 클릭합니다. 클러스터에서 실행한 Hive 작업 목록이 표시됩니다. 
3. 작업 목록에서 작업을 클릭하여 선택하고 **Hive 작업 요약** 창을 사용하여 **작업 쿼리**, **작업 출력**, **작업 로그** 또는 **Yarn 로그**를 엽니다.

	![Hadoop 도구: HDInsight Visual Studio 도구 새 Hive 작업][12]
## 다음 단계
이 문서에서는 Hadoop 도구 패키지를 사용하여 Visual Studio에서 HDInsight 클러스터에 연결하는 방법 및 Hive 쿼리를 실행하는 방법에 대해 알아보았습니다. 자세한 내용은 다음을 참조하세요.

- [HDInsight에서 Hadoop Hive 사용][hdinsight.hive]
- [HDInsight에서 Hadoop 사용 시작][hdinsight.get.started]
- [HDInsight에서 Hadoop 작업 제출][hdinsight.submit.jobs]
- [HDInsight에서 Hadoop으로 Twitter 데이터 분석][hdinsight.analyze.twitter.data]


<!--Anchors-->
[Installation]: #installation
[Connect to your Azure subscription]: #connect-to-your-azure-subscription
[Navigate the linked resources]: #navigate-the-linked-resources
[Run Hive queries]: #run-hive-queries
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.wpi.png
[2]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png
[5]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png
[6]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png
[7]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png
[8]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.run.hive.job.summary.png
[9]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png
[10]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.validate.hive.script.png
[11]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.new.hive.project.png
[12]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.view.hive.jobs.png
[13]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.table.names.png
[14]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.column.names.png

<!--Link references-->
[hdinsight-provision]: ../hdinsight/hdinsight-provision-clusters.md
[hdinsight.introduction]: ../hdinsight-introduction.md
[hdinsight.get.started]: ../hdinsight-get-started.md
[hdinsight.hive]: ../hdinsight/hdinsight-use-hive.md
[hdinsight.submit.jobs]: ../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight.analyze.twitter.data]: ../hdinsight/hdinsight-analyze-twitter-data.md
[hdinsight.storm.visual.studio.tools]: ../hdinsight/hdinsight-storm-develop-csharp-visual-studio-topology.md
[hdinsight.access.application.logs]: ../hdinsight/hdinsight-hadoop-access-yarn-app-logs.md

[apache.hive]: http://hive.apache.org

<!--HONumber=54--> 