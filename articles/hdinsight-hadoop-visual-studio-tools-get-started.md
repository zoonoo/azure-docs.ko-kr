<properties 
	pageTitle="Visual Studio용 HDInsight 도구 사용 시작 | Azure" 
	description="Visual Studio용 HDInsight 도구를 설치한 후 사용하여 HDInsight에 연결하고 Hive 쿼리를 실행하는 방법에 대해 알아봅니다." 
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
	ms.date="2/17/2015" 
	ms.author="jgao"/>

# Visual Studio용 HDInsight Hadoop 도구 사용 시작

Visual Studio용 HDInsight 도구를 사용하여 HDInsight 클러스터에 연결하고 Hive 쿼리를 제출하는 방법에 대해 알아봅니다. HDInsight 사용에 대한 자세한 내용은 [HDInsight 소개][hdinsight.introduction] 및 [HDInsight 시작][hdinsight.get.started]을 참조하세요. Storm 클러스터에 연결하는 방법에 대한 자세한 정보는 [개발 C# Visual Studio를 사용하여 HDInsight에서 Apache Storm에 대한 토폴로지][hdinsight.storm.visual.studio.tools]를 참조하세요. 

>[WACOM.NOTE] 오늘날 일부 Hive 작업의 YARN 로그와 같이 Humboldt에서 지원하지 않는 새로운 기능은 제외하고 Windows 클러스터에서 사용할 수 있는 HDInsight 도구의 모든 기능은 Linux 클러스터에서도 작동합니다. 

>가장 최신 릴리스는 Hive 편집기 Intellisense 지원, Hive 스크립트 로컬 유효성 검사 및 Yarn 로그에 액세스와 같은 새로운 기능을 소개했습니다.

## 이 문서에서는
+ [설치] 
+ [Azure 구독에 연결]
+ [연결된 리소스 탐색]
+ [Hive 쿼리 실행]
+ [다음 단계]


## 필수 조건

- 다음이 설치된 워크스테이션

	- Windows 7, Windows 8, 또는 Windows 8.1
	- 다음 버전의 Visual Studio:
		- [Update 4](http://www.microsoft.com/ko-kr/download/details.aspx?id=39305)가 설치된 Visual Studio 2012 Professional/Premium/Ultimate
		- [Update 4](https://www.microsoft.com/ko-kr/download/details.aspx?id=44921)가 설치된 Visual Studio 2013 Community/Professional/Premium/Ultimate
		- Visual Studio 2015 Preview.

	>[WACOM.NOTE] 현재 이 도구는 영어 버전만 제공됩니다. 


## 설치

Visual Studio용 HDInsight 도구는 [웹 플랫폼 설치 관리자](http://go.microsoft.com/fwlink/?LinkId=255386)를 사용하여 설치할 수 있습니다. "hdinsight" 키워드를 사용하여 도구를 쿼리할 수 있습니다. 각각의 Visual Studio 버전마다 하나의 패키지가 있습니다. 가지고 있는 Visual Studio에 일치하는 것을 선택해야 합니다.  패키지가 Microsoft Hive ODBC 드라이버 32 비트 및 64 비트를 모두 설치합니다.

![HDinsight Tools for Visual Studio Web Platform installer][1]


>[WACOM.NOTE] visual studio 2012 또는 2015를 가지고 있고 이전 버전의 Azure SDK가 설치되어 있는 경우 최신 버전을 설치하기 전에 이전 버전을 수동으로 제거해야 합니다. Visual Studio 2013은 직접 업데이트를 지원합니다.

## Azure 구독에 연결
Visual Studio용 HDInsight 도구를 사용하면 HDInsight 클러스터에 연결하고, 기본적인 관리 작업을 수행하고, Hive 쿼리를 실행할 수 있습니다.

**Azure 구독에 연결하려면**

1.	Visual Studio를 실행합니다.
2.	**보기** 메뉴에서 **서버 탐색기**를 클릭하여 서버 탐색기 창을 엽니다.
3.	**Azure**를 확장한 후 **HDInsight**를 확장합니다. 

	>[WACOM.NOTE]**HDInsight 작업 목록** 창이 열립니다. 이 창이 표시되지 않는 경우 **보기** 메뉴에서 **다른 창**을 클릭한 후 **HDInsight Task List Window(HDInsight 작업 목록 창)**를 클릭하여 열 수 있습니다.  
4.	Azure 구독 자격 증명을 입력한 후 **로그인**을 클릭합니다. 이 과정은 이 워크스테이션에서 Visual Studio를 통해 Azure 구독에 연결한 적이 없는 경우 필요합니다.
5.	서버 탐색기에서 기존 HDInsight 클러스터 목록이 표시됩니다. 클러스터가 없는 경우 관리 포털, Azure PowerShell 또는 HDInsight SDK를 사용하여 클러스터를 프로비전할 수 있습니다.  자세한 내용은 [HDInsight 클러스터 프로비전][hdinsight-provision]을 참조하세요.

	![HDInsight Tools for visual studio server explorer cluster list][5]
6.	HDInsight 클러스터를 확장합니다. **Hive 데이터베이스**, 기본 저장소 계정, 연결된 저장소 계정 및 **Hadoop 서비스 로그**를 볼 수 있습니다. 엔터티를 더 확장할 수 있습니다. 

Azure 구독에 연결한 후에는 다음을 수행할 수 있습니다.

**Visual Studio에서 관리 포털에 연결하려면**

- 서버 탐색기에서 **Azure**, **HDInsight**를 확장하고, HDInsight 클러스터를 마우스 오른쪽 단추로 클릭한 후 **Azure 포털에서 클러스터 관리**를 클릭합니다.

**Visual Studio에서 질문하고 피드백을 제공하려면**

- **도구** 메뉴에서 **HDInsight**를 클릭한 후 **MSDN 포럼**을 클릭하여 질문을 하거나 **피드백을 제공**합니다.

## 연결된 리소스 탐색 

서버 탐색기에서 기본 저장소 계정 및 연결된 저장소 계정을 확인할 수 있습니다. 기본 저장소 계정을 확장하여 저장 소 계정의 컨테이너를 확인할 수 있습니다. 기본 저장소 계정과 기본 컨테이너가 모두 표시되어 있습니다. 컨테이너를 마우스 오른쪽 단추로 클릭하여 컨테이너를 볼 수도 있습니다.

![HDInsight Tools for visual studio server explorer cluster list][2]

## Hive 쿼리 실행
[Apache Hive][apache.hive](영문)는 Hadoop 위에 구축된 데이터 웨어하우스 인프라로, 데이터 요약, 쿼리 및 분석 기능을 제공합니다. Visual Studio용 HDInsight 도구를 사용하면 Visual Studio에서 Hive 쿼리를 실행할 수 있습니다. Hive에 대한 자세한 내용은 [HDInsight에서 Hive 사용][hdinsight.hive]을 참조하세요.

HDInsight 클러스터에 대해 Hive 스크립트를 테스트하려면 많은 시간이 걸립니다. 몇 분 이상 걸릴 수 있습니다.  Visual Studio용 HDInsight 도구는 로컬로 라이브 클러스터에 연결하지 않고 Hive 스크립트 문법의 유효성 검사를 할 수 있습니다.

Visual Studio용 HDInsight 도구를 사용하여 일부 Hive 작업의 Yarn 로그를 수집하고 표시하여 사용자가 Hive 작업 내에 무엇이 있는지 볼 수 있습니다.

### hivesampletable Hive 테이블을 보기
모든 HDInsight 클러스터에는 *hivesampletable*이라는 샘플 Hive 테이블이 함께 제공됩니다. 여기서는 이 테이블을 사용하여 Hive 테이블을 나열하고, 테이블 스키마를 보고, Hive 테이블의 행을 나열하는 방법을 보여 줍니다.



**Hive 테이블을 나열하고 Hive 테이블 스키마를 보려면**

1.	**서버 탐색기**에서 **Azure**, **HDInsight**를 차례로 확장한 후 클러스터를 확장하고 **Hive 데이터베이스**, **기본값**을 차례로 확장한 후 **hivesampletable**을 확장하여 테이블 스키마를 표시합니다.
4.	**hivesampletable**을 마우스 오른쪽 단추로 클릭한 후 **View Top 100 Rows(상위 100개 행 보기)**를 클릭하여 행을 나열합니다. 이는 Hive ODBC 드라이버를 사용하여 다음 Hive 쿼리를 실행하는 것과 동일합니다.

		SELECT * FROM hivesampletable LIMIT 100

	행 개수를 사용자 지정할 수 있습니다. 
 
	![HDinsight Hive Visual Studio schema query][6]

### Hive 테이블 만들기

GUI를 사용하여 Hive 테이블을 작성하거나 Hive 쿼리를 사용할 수 있습니다. Hive 쿼리를 사용할 경우 [Hive 쿼리 실행]을(#run.queries)참조하세요.

**Hive 테이블을 만들려면**

1. **서버 탐색기**에서 **Azure**, **HDInsight 클러스터**, HDInsight 클러스터, **Hive 데이터베이스**를 차례로 확장한 후 **기본값**을 마우스 오른쪽 단추로 클릭하고 **테이블 만들기**를 클릭합니다.
2. 테이블을 구성합니다.
3. **테이블 만들기**를 클릭하여 새 Hive 테이블을 만들기 위한 작업을 제출합니다.

	![hdinsight visual studio tools create hive table][7]

### <a name="run.queries"></a>Hive 쿼리 유효성 검사 및 실행
다음 두 가지 방법으로 Hive 쿼리를 만들고 실행할 수 있습니다.

- 임시 쿼리 만들기
- Hive 응용 프로그램 만들기

**임시 쿼리를 만들고 유효성을 검사하고 실행하려면,**

1. **서비스 탐색기**에서 **Azure**를 확장한 후 **HDInsight 클러스터**를 확장합니다.
2. 쿼리를 실행할 클러스터를 마우스 오른쪽 단추로 클릭하고 **Write a Hive Query(Hive 쿼리 작성)**를 클릭합니다. 
3. Hive 쿼리를 입력합니다. Hive 편집기는 Intellisense를 지원합니다.
4. (선택사항) **스크립트 유효성 검사**를 클릭하여 스크립트 구문 오류를 확인합니다.

	![hdinsight tools for Visual Studio local validation][10]

4. **제출** 또는 **Submit (Advanced)(제출(고급))**을 클릭합니다. 고급 제출을 사용할 경우 스크립트에 대한 **작업 이름**, **인수**, **추가 구성** 및 **Status Directory(상태 디렉터리)**를 구성합니다.

	![hdinsight hadoop hive query][9]

	작업을 제출하면 **Hive Job Summary(Hive 작업 요약)** 창을 볼 수 있습니다.

	![hdinsight hadoop hive query][8]
5. 작업 상태가 **완료**로 변경될 때까지 **새로고침** 단추를 사용하여 상태를 새로 고칩니다.
6. 아래 쪽의 링크를 클릭하여 **작업 쿼리**, **작업 출력**, **작업 로그** 또는 **Yarn 로그**를 표시합니다.



**Hive 솔루션을 만들고 실행하려면**

1. **파일** 메뉴에서 **새로 만들기**와 **프로젝트**를 차례로 클릭합니다.
2. 왼쪽 창에서 **HDInsight**를 선택하고 가운데 창에서 **Hive 응용 프로그램**을 선택한 후 속성을 입력하고 **확인**을 클릭합니다.
3. **솔루션 탐색기**에서 **Script.hql**를 두 번 클릭하여 엽니다. 
4. Hive 스크립트의 유효성을 검사하려면 스크립트 유효성 검사 단추를 클릭하거나 Hive 편집기에서 스크립트를 마우스 오른쪽 단추로 클릭하거나 컨텍스트 메뉴에서 "스크립트 유효성 검사"를 클릭합니다.

 
### Hive 작업 보기
Hive 작업에 대한 작업 쿼리, 작업 출력, 작업 로그 및 Yarn 로그를 볼 수 있습니다.  이전 스크린샷을 참조하세요.

가장 최신 릴리스 도구를 사용하여 Yarn 로그를 수집하고 표시하여 사용자가 Hive 작업 내에 무엇이 있는지 볼 수 있습니다. Yarn 로그를 사용하여 성능 문제를 조사할 수 있습니다. HDInsight가 Yarn 로그를 수집하는 방법에 대한 자세한 내용은 [프로그래밍 방식으로 HDInsight 응용 프로그램 로그에 액세스][hdinsight.access.application.logs]를 참조합니다.

**Hive 작업을 보려면**

1. **서비스 탐색기**에서 **Azure**를 확장한 후 **HDInsight**를 확장합니다. 
2. HDInsight 클러스터를 마우스 오른쪽 단추로 클릭한 다음 **View Hive Jobs(Hive 작업 보기)**를 클릭합니다. 클러스터에서 실행한 Hive 작업 목록이 표시됩니다. 
3. 작업 목록에서 작업을 클릭하여 선택하고 **Hive Job Summary(Hive 작업 요약)** 창을 사용하여 **작업 쿼리**, **작업 출력**, **작업 로그** 또는**Yarn 로그**를 사용합니다.

## 다음 단계
이 문서에서는 Visual Studio에서 HDInsight 클러스터에 연결하고 Hive 쿼리를 실행하는 방법에 대해 알아보았습니다. 자세한 내용은 다음을 참조하세요.

- [HDInsight에서 Hadoop Hive 사용][hdinsight.hive]
- [HDInsight에서 Hadoop 사용 시작][hdinsight.get.started]
- [HDInsight에서 Hadoop 작업 제출][hdinsight.submit.jobs]
- [HDInsight의 Hadoop에서 Twitter 데이터 분석][hdinsight.analyze.twitter.data]


<!--Anchors-->
[설치]: #installation
[Azure 구독에 연결]: #connect-to-your-azure-subscription
[연결된 리소스 탐색]: #navigate-the-linked-resources
[Hive 쿼리 실행]: #run-hive-queries
[다음 단계]: #next-steps

<!--Image references-->
[1]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.wpi.png
[2]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png
[5]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png
[6]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png
[7]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png
[8]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.run.hive.job.summary.png
[9]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png
[10]: ./media/hdinsight-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.validate.hive.script.png


<!--Link references-->
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight.introduction]: ../hdinsight-introduction/
[hdinsight.get.started]: ../hdinsight-get-started/
[hdinsight.hive]: ../hdinsight-use-hive/
[hdinsight.submit.jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight.analyze.twitter.data]: ../hdinsight-analyze-twitter-data/
[hdinsight.storm.visual.studio.tools]: ../hdinsight-storm-develop-csharp-visual-studio-topology/
[hdinsight.access.application.logs]: ../hdinsight-hadoop-access-yarn-app-logs/

[apache.hive]: http://hive.apache.org
<!--HONumber=45--> 
