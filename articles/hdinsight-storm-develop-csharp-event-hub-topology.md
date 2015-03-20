<properties
   pageTitle="HDInsight의 Storm으로 이벤트 허브에서 이벤트 처리 | Azure"
   description="Visual Studio용 HDInsight 도구를 사용하여 Visual Studio에서 만든 C# Storm 토폴로지로 이벤트 허브 데이터를 처리하는 방법에 대해 알아봅니다."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/03/2015"
   ms.author="larryfr"/>

#HDInsight의 Storm으로 Azure 이벤트 허브에서 이벤트 처리(C#)

Azure 이벤트 허브를 사용하면 웹 사이트, 앱 및 장치에서 대량의 데이터를 처리할 수 있습니다. 이벤트 허브 Spout를 사용하면 HDInsight에서 Apache Storm을 사용하여 이 데이터를 실시간으로 분석하기가 쉽습니다. 또한 이벤트 허브 Bolt를 사용하여 Storm에서 이벤트 허브에 데이터를 기록할 수도 있습니다. 

이 문서에서는 Visual Studio용 HDInsight 도구 및 이벤트 허브 Spout와 Bolt를 사용하여 두 개의 하이브리드 C#/Java 토폴로지를 만드는 방법에 대해 알아봅니다.

* **EventHubWriter** - 무작위로 데이터를 생성하여 이벤트 허브에 기록합니다.

* **EventHubReader** - 이벤트 허브에서 데이터를 읽어 Azure 테이블 저장소에 저장합니다.

##필수 조건

* <a href="../hdinsight-storm-getting-started/" target="_blank">HDInsight 클러스터의 Apache Storm</a>

* <a href="../service-bus-event-hubs-csharp-ephcs-getstarted/" target="_blank">Azure 이벤트 허브</a>

* <a href="http://azure.microsoft.com/downloads/" target="_blank">Azure .NET SDK</a>

* <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Visual Studio용 HDInsight 도구</a> - 2/17/2015 이후 버전

##완성된 프로젝트

이 문서에서 만든 프로젝트의 전체 버전을 [https://github.com/Blackmist/eventhub-storm-hybrid](https://github.com/Blackmist/eventhub-storm-hybrid)에서 다운로드할 수 있습니다. 그러나 이 문서의 단계에 따라 구성 설정을 제공해야 합니다.

> [AZURE.NOTE] 완성된 프로젝트를 사용하는 경우 **NuGet 패키지 관리자**를 사용하여 이 솔루션에 필요한 패키지를 복원해야 합니다.

##이벤트 허브 spout 및 bolt

이벤트 허브 spout 및 bolt는 Apache Storm에서 이벤트 허브를 쉽게 사용할 수 있도록 하는 Java 구성 요소입니다. 이러한 구성 요소는 Java로 작성되지만 Visual Studio용 HDInsight 도구를 사용하면 C# 및 Java 구성 요소를 혼합하는 하이브리드 토폴로지를 만들 수 있습니다.

spout 및 bolt는 **eventhubs-storm-spout-0.9-jar-with-dependencies.jar**라는 단일 Java 보관(.jar) 파일로 분산됩니다.

###Jar 다운로드

최신 버전의 **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** 파일은 **lib** 폴더 아래의 <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">https://github.com/hdinsight/hdinsight-storm-examples</a> 프로젝트에 포함되어 있습니다. 이 파일을 다운로드하려면 다음 방법 중 하나를 사용합니다.

> [AZURE.NOTE] spout 및 bolt는 Apache Storm 프로젝트에 포함되도록 제출되었습니다. 자세한 내용은 <a href="https://github.com/apache/storm/pull/336/files">끌어오기 요청</a>을 참조하세요.

* **ZIP 파일 다운로드** - <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">https://github.com/hdinsight/hdinsight-storm-examples</a> 사이트에서 **Download ZIP** 단추를 선택하여 프로젝트가 포함된 .zip 파일을 다운로드합니다.

	![download zip button](./media/hdinsight-storm-develop-csharp-event-hub-topology/download.png)

	다운로드된 다음 보관 파일을 추출하면 파일이 **lib** 디렉터리에 저장됩니다.

* **프로젝트 복제** - <a href="http://git-scm.com/" target="_blank">Git</a>가 설치된 경우 다음 명령을 사용하여 로컬에서 리포지토리를 복제한 다음 **lib** 디렉터리에서 파일을 찾습니다.

		git clone https://github.com/hdinsight/hdinsight-storm-examples

##이벤트 허브 구성

이벤트 허브는 이 예제의 데이터 원본입니다. 다음 단계에 따라 새 이벤트 허브를 만듭니다.

1. [Azure 포털](https://manage.windowsazure.com)에서 **새로 만들기 | 서비스 버스 | 이벤트 허브 | 사용자 지정 만들기**를 선택합니다.

2. **새 이벤트 허브 추가** 대화 상자에서 **이벤트 허브 이름**을 입력하고 허브를 만들 하위 **지역**을 선택한 다음 새 네임스페이스를 만들거나 기존 네임스페이스를 선택합니다. 마지막으로 **화살표**를 클릭합니다.

	![wizard page 1](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

	> [AZURE.NOTE] 대기 시간 및 비용을 줄이려면 HDInsight 서버의 Storm과 동일한 **위치**를 선택해야 합니다.

2. **이벤트 허브 구성** 대화 상자에서 **파티션 개수** 및 **메시지 보존** 값을 입력합니다. 이 예에서는 파티션 개수로 10을, 메시지 보존으로는 1을 사용합니다. 파티션 개수 값은 나중에 필요하므로 기록해 둡니다.

	![wizard page 2](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

3. 이벤트 허브가 만들어지면 네임스페이스를 선택하고 **이벤트 허브**를 선택합니다. 그리고 마지막으로 앞에서 만든 이벤트 허브를 선택합니다.

4. **구성**을 선택하고 다음 정보를 사용하여 새 액세스 정책 두 개를 만듭니다.

	<table>
	<tr><th>이름</th><th>권한</th></tr>
	<tr><td>기록기</td><td>보내기</td></tr>
	<tr><td>판독기</td><td>수신</td></tr>
	</table>

	권한을 만든 후 페이지 아래쪽의 **저장** 아이콘을 선택합니다. 그러면 이 이벤트 허브로 보내고(기록기) 수신하는(판독기) 데 사용될 공유 액세스 정책이 만들어집니다.

	![policies](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

5. 정책을 저장한 후 페이지 아래쪽의 **공유 액세스 키 생성기**를 사용하여 **기록기** 및 **판독기** 정책에 대한 키를 검색합니다. 그런 다음 나중에 사용할 수 있도록 키를 저장합니다.

##테이블 저장소 구성

테이블 저장소는 Visual Studio 내부에서 **서버 탐색기**를 통해 쉽게 볼 수 있으므로 이벤트 허브에서 읽은 값을 저장하는 데 사용됩니다. 다음 단계를 사용하여 새 테이블 저장소를 만듭니다.

1. [Azure 포털](https://manage.windowsazure.com)에서 **새로 만들기 | 데이터 서비스 | 저장소 | 빠른 생성**을 선택합니다.

	![quick create storage](./media/hdinsight-storm-develop-csharp-event-hub-topology/storagecreate.png)

2. 저장소 계정의 **이름**을 입력하고 **위치**를 선택한 다음 **확인 표시**를 선택하여 저장소 계정을 만듭니다.

	> [AZURE.NOTE] 대기 시간 및 비용을 줄이려면 이벤트 허브 및 HDInsight 서버의 Storm과 동일한 **위치**를 선택해야 합니다.

3. 새 저장소 계정이 프로비전되면 계정을 선택하고 페이지 아래쪽에 있는 **액세스 키 관리** 링크를 사용하여 **저장소 계정 이름** 및 **기본 액세스 키**를 검색합니다. 이 정보는 나중에 사용되므로 저장합니다.

	![access keys](./media/hdinsight-storm-develop-csharp-event-hub-topology/managekeys.png)

##EventHubWriter 만들기

이 섹션에서는 이벤트 허브 Bolt를 사용하여 이벤트 허브에 데이터를 기록하는 토폴로지를 만듭니다.

1. 최신 버전의 Visual Studio용 HDInsight 도구를 아직 설치하지 않은 경우 <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Visual Studio용 HDInsight 도구 사용 시작</a>을 참조하세요.

2. Visual Studio를 열고 **파일**, **새로 만들기**, **프로젝트**를 차례로 선택합니다.

3. **새 프로젝트** 대화 상자에서 **설치됨**, **템플릿**을 확장하고 **HDInsight**를 선택합니다. 템플릿 목록에서 **Storm 응용 프로그램**을 선택합니다. 대화 상자 아래쪽에서 응용 프로그램 이름으로 **EventHubWriter**를 입력합니다.

	![image](./media/hdinsight-storm-develop-csharp-event-hub-topology/newproject.png)

4. 프로젝트를 만들면 다음과 같은 파일이 생깁니다.

	* **Program.cs** - 프로젝트에 대한 토폴로지를 정의합니다. 기본적으로 하나의 spout 및 하나의 bolt로 구성된 기본 토폴로지가 만들어집니다.

	* **Spout.cs** - 예제 spout입니다.

	* **Bolt.cs** - 예제 bolt입니다. 이벤트 허브 Bolt를 사용하여 이벤트 허브에 기록하면 삭제됩니다.

###구성

1. **솔루션 탐색기**에서 **EventHubWriter**를 마우스 오른쪽 단추로 클릭한 다음 **속성**을 선택합니다.

2. 프로젝트 속성에서 **설정** 선택한 다음 **이 프로젝트에는 기본 설정 파일이 없습니다. 기본 설정 파일을 만들려면 여기를 클릭하세요.**를 선택합니다.

3. 다음 설정을 입력합니다. 앞에서 만든 이벤트 허브에 대한 정보를 **값** 열에 사용합니다.

	<table>
	<tr><th style="text-align:left">이름</th><th style="text-align:left">유형</th><th style="text-align:left">범위</th></tr>
	<tr><td style="text-align:left">EventHubPolicyName</td><td style="text-align:left">string</td><td style="text-align:left">응용 프로그램</td></tr>
	<tr><td style="text-align:left">EventHubPolicyKey</td><td style="text-align:left">string</td><td style="text-align:left">응용 프로그램</td></tr>
	<tr><td style="text-align:left">EventHubNamespace</td><td style="text-align:left">string</td><td style="text-align:left">응용 프로그램</td></tr>
	<tr><td style="text-align:left">EventHubName</td><td style="text-align:left">string</td><td style="text-align:left">응용 프로그램</td></tr>
	<tr><td style="text-align:left">EventHubPartitionCount</td><td style="text-align:left">int</td><td style="text-align:left">응용 프로그램</td></tr>
	</table>

4. 속성 페이지를 저장하고 닫습니다.

###토폴로지 정의

1. **솔루션 탐색기**에서 **Bolt.cs**를 마우스 오른쪽 단추로 클릭하고 **삭제**를 선택합니다. Java 이벤트 허브 bolt를 사용하고 있으므로 이 파일이 필요하지 않습니다.

2. **Program.cs** 파일을 열고 `TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubWriter");` 줄 바로 뒤에 다음을 추가합니다.

		int partitionCount = Properties.Settings.Default.EventHubPartitionCount;
		List<string> javaDeserializerInfo =
            new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer", "java.lang.String" };

	첫 번째 줄은 앞에서 정의한 속성에서 파티션 개수를 읽습니다. 두 번째 줄은 이벤트 허브 Bolt에서 데이터를 사용할 수 있도록 spout에서 생성한 JSON 데이터를  `java.lang.String`로 역직렬화하는 데 사용되는 역직렬화기를 정의합니다.

4. 다음 코드를 찾습니다.

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"count"}}
            },
            1);

	Replace it with the following.

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"Event"}}
            },
            partitionCount).
            DeclareCustomizedJavaDeserializer(javaDeserializerInfo);

	이 코드는 spout를 만들고 이벤트 허브 파티션 개수를 이 구성 요소의 병렬 처리 힌트로 사용합니다. 각 파티션에 대해 spout의 인스턴스를 만들어야 합니다.
	
	또한 출력 스트림에서 이전에 만든 역직렬화기를 사용하도록 선언합니다.

5. 이전 코드 바로 뒤에 다음 코드를 추가합니다.

		JavaComponentConstructor constructor =
            JavaComponentConstructor.CreateFromClojureExpr(
            String.Format(@"(com.microsoft.eventhubs.bolt.EventHubBolt. (com.microsoft.eventhubs.bolt.EventHubBoltConfig. " +
            @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
            Properties.Settings.Default.EventHubPolicyName,
            Properties.Settings.Default.EventHubPolicyKey,
            Properties.Settings.Default.EventHubNamespace,
            "servicebus.windows.net", //suffix for servicebus fqdn
            Properties.Settings.Default.EventHubName, 
			"true"));

	이 코드는 Java bolt에 대한 새 생성자를 만듭니다. 이 생성자는 런타임에 bolt의 새 인스턴스를 구성하는 데 사용됩니다. 이 경우 <a href="http://storm.apache.org/documentation/Clojure-DSL.html" target="_blank">Apache Storm Clojure DSL</a>을 사용하여 이전에 추가한 이벤트 허브 구성 정보로 spout를 구성합니다. 더욱 구체적으로 설명하자면 이 코드는 런타임에 HDInsight에서 다음을 수행하는 데 사용됩니다.

	* 제공된 이벤트 허브 정보를 사용하여 **com.microsoft.eventhubs.bolt.EventHubBoltConfig**의 새 인스턴스를 만듭니다.
	* **com.microsoft.eventhubs.bolt.EventHubBolt**의 새 인스턴스를 만들어 **EventHubBoltConfig** 인스턴스에 전달합니다.

6. 다음 코드를 찾습니다.

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            1).shuffleGrouping("Spout");

	Replace it with the following.

		topologyBuilder.SetJavaBolt(
            "EventHubBolt",
            constructor,
            partitionCount).
            shuffleGrouping("Spout");

	이 코드는 이전 단계의 **JavaComponentConstructor**를 bolt로 사용하도록 토폴로지에 지시합니다. 이 토폴로지에서는 구성 요소를 "EventHubBolt"라는 이름으로 참조할 수 있습니다. 병렬 처리 힌트는 이벤트 허브에 대한 파티션 개수로 설정되고 spout("Spout")에 의해 생성된 데이터를 구독합니다.

이제 **Program.cs** 작업을 마쳤습니다. 토폴로지는 정의되었지만 이제 이벤트 허브 Bolt에서 사용할 수 있는 형식으로 데이터를 생성하도록 **Spout.cs**를 수정해야 합니다.

###spout 수정

이벤트 허브 Bolt에는 이벤트 허브로 라우팅되는 단일 문자열 값이 필요합니다. 다음 예제에서는 기본 **Spout.cs** 파일을 수정하여 JSON 문자열을 생성합니다.

1. **솔루션 탐색기**에서 **EventHubWriter** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Nuget 패키지 관리**를 선택합니다. **Json.Net** 패키지를 검색한 다음 솔루션에 추가합니다. 그러면 Bolt를 사용하여 이벤트 허브로 전송할 JSON 데이터를 쉽게 만들 수 있습니다.

1. **Spout.cs**를 열고 파일 맨 위에 다음을 추가합니다.

		using Newtonsoft.Json;
		using Newtonsoft.Json.Linq;

	이렇게 하면 JSON 데이터를 사용하여 보다 쉽게 작업할 수 있습니다.

3. 다음 코드를 찾습니다.

		Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));

	다음 코드로 바꿉니다.

		Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
        this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer());

	그러면 spout에서 만든 데이터 정의가 **문자열** 데이터 및 **사용자 지정 JSON 직렬화기**를 사용하도록 변경됩니다.

2. **NextTuple** 메서드를 다음과 같이 바꿉니다.

		public void NextTuple(Dictionary<string, Object> parms)
        {
            JObject eventData = new JObject();
            eventData.Add("deviceId", r.Next(10));
            eventData.Add("deviceValue", r.Next());
            ctx.Emit(new Values(eventData.ToString(Formatting.None)));
        }

	장치 ID, 값을 무작위로 생성한 다음 Json.net을 사용하여 이러한 값을 사용하는 JSON 개체를 내보냅니다.

3. **Spout.cs** 파일을 저장합니다.

이제 임의 데이터를 생성하고 이벤트 허브 Bolt를 사용하여 이벤트 허브에 저장하는 기본 토폴로지가 있습니다. 다음으로 판독기를 만듭니다.

##EventHubReader 만들기

이 섹션에서는 이벤트 허브 Spout를 사용하여 이벤트 허브에서 데이터를 읽는 토폴로지를 만듭니다.

2. Visual Studio를 열고 **파일**, **새로 만들기**, **프로젝트**를 차례로 선택합니다.

3. **새 프로젝트** 대화 상자에서 **설치됨**, **템플릿**을 확장하고 **HDInsight**를 선택합니다. 템플릿 목록에서 **Storm 응용 프로그램**을 선택합니다. 대화 상자 아래쪽에서 응용 프로그램 이름으로 **EventHubReader**를 입력합니다.

###구성

1. **솔루션 탐색기**에서 **EventHubReader**를 마우스 오른쪽 단추로 클릭한 다음 **속성**을 선택합니다.

2. 프로젝트 속성에서 **설정** 선택한 다음 **이 프로젝트에는 기본 설정 파일이 없습니다. 기본 설정 파일을 만들려면 여기를 클릭하세요.**를 선택합니다.

3. 다음 설정을 입력합니다. 앞에서 만든 이벤트 허브 및 저장소 계정에 대한 정보를 **값** 열에 사용합니다.

	<table>
	<tr><th style="text-align:left">이름</th><th style="text-align:left">유형</th><th style="text-align:left">범위</th></tr>
	<tr><th style="text-align:left">EventHubPolicyName</th><th style="text-align:left">string</th><th style="text-align:left">응용 프로그램</th></tr>
	<tr><th style="text-align:left">EventHubPolicyKey</th><th style="text-align:left">string</th><th style="text-align:left">응용 프로그램</th></tr>
	<tr><th style="text-align:left">EventHubNamespace</th><th style="text-align:left">string</th><th style="text-align:left">응용 프로그램</th></tr>
	<tr><th style="text-align:left">EventHubName</th><th style="text-align:left">string</th><th style="text-align:left">응용 프로그램</th></tr>
	<tr><th style="text-align:left">EventHubPartitionCount</th><th style="text-align:left">int</th><th style="text-align:left">응용 프로그램</th></tr>
	<tr><th style="text-align:left">StorageConnection</th><th style="text-align:left">(연결 문자열)</th><th style="text-align:left">응용 프로그램</th></tr>
	<tr><th style="text-align:left">TableName</th><th style="text-align:left">string</th><th style="text-align:left">응용 프로그램</th></tr>
	</table>

	**TableName**에 대해 이벤트를 저장하려는 테이블의 이름을 입력합니다.

	이러한 값은 토폴로지에서 이벤트 허브 및 테이블 저장소와 통신하는 데 사용됩니다.

4. 속성 페이지를 저장하고 닫습니다.

###토폴로지 정의

1. **솔루션 탐색기**에서 **Spout.cs**를 마우스 오른쪽 단추로 클릭하고 **삭제**를 선택합니다. Java 이벤트 허브 spout를 사용하고 있으므로 이 파일이 필요하지 않습니다.

2. **Program.cs** 파일을 열고 `TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubReader");` 줄 바로 뒤에 다음을 추가합니다.

		int partitionCount = Properties.Settings.Default.EventHubPartitionCount;
		JavaComponentConstructor constructor = JavaComponentConstructor.CreateFromClojureExpr(
            String.Format(@"(com.microsoft.eventhubs.spout.EventHubSpout. (com.microsoft.eventhubs.spout.EventHubSpoutConfig. " +
                @"""{0}"" ""{1}"" ""{2}"" ""{3}"" {4} ""{5}""))",
                Properties.Settings.Default.EventHubPolicyName,
                Properties.Settings.Default.EventHubPolicyKey,
                Properties.Settings.Default.EventHubNamespace,
                Properties.Settings.Default.EventHubName,
                partitionCount,
                "")); //Last value is the zookeeper connection string - leave empty

	파티션 개수는 여러 번 사용되므로 읽은 후 로컬 변수에 할당합니다.

	 `JavaComponentConstructor`는 런타임에 Java spout가 생성되는 방법을 정의합니다. 이 경우 <a href="http://storm.apache.org/documentation/Clojure-DSL.html" target="_blank">Apache Storm Clojure DSL</a>을 사용하여 이전에 추가한 이벤트 허브 구성 정보로 spout를 구성합니다. 더욱 구체적으로 설명하자면 이 코드는 런타임에 HDInsight에서 다음을 수행하는 데 사용됩니다.

	* 제공된 이벤트 허브 정보를 사용하여 **com.microsoft.eventhubs.spout.EventHubSpoutConfig**의 새 인스턴스를 만듭니다.
	
	* **com.microsoft.eventhubs.spout.EventHubSpout**의 새 인스턴스를 만들어 **EventHubSpoutConfig** 인스턴스에 전달합니다.

5. 다음 코드를 찾습니다.

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"count"}}
            },
            1);

	Replace it with the following.

        topologyBuilder.SetJavaSpout(
            "EventHubSpout",
            constructor,
            partitionCount);

	이 코드는 이전 단계의 **JavaComponentConstructor**를 spout로 사용하고 "EventHubSpout"의 이름을 지정하도록 토폴로지에 지시합니다. 또한 이 구성 요소에 대한 병렬 처리 힌트를 이벤트 허브의 파티션 개수로 설정합니다. 

2. 이전 코드 바로 뒤에 다음을 추가합니다.

         List<string> javaSerializerInfo = new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer" };

	그러면 Java spout에서 생성된 정보를 JSON 형식으로 직렬화하는 데 사용될 사용자 지정 직렬화기가 만들어집니다.

3. 다음 코드를 찾습니다.

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            1).shuffleGrouping("Spout");

	다음으로 바꿉니다.

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            partitionCount).
            DeclareCustomizedJavaSerializer(javaSerializerInfo).
            shuffleGrouping("EventHubSpout");

	이 코드는 bolt(Bolt.cs에 정의됨)를 사용하도록 토폴로지에 지시합니다. 또한 Java spout인 **EventHubSpout**에서 생성되는, 이 구성 요소에 사용된 데이터에 사용자 지정 직렬화기를 사용하도록 토폴로지에 지시합니다.

이제 **Program.cs** 작업을 마쳤습니다. 토폴로지는 정의되었지만 이제 테이블 저장소에 데이터를 기록하는 도우미 클래스를 만든 다음 spout에 의해 생성된 데이터를 이해할 수 있도록 **Bolt.cs**를 수정해야 합니다.

###도우미 클래스 만들기

테이블 저장소에 데이터를 기록하려면 기록할 데이터를 설명하는 클래스를 만들어야 합니다.

1. **솔루션 탐색기**에서 **EventHubReader** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가**, **새 클래스**를 선택합니다. 새 클래스 이름을 **Devices.cs**로 지정합니다.

2. **Devices.cs**를 열고 기본 코드를 다음으로 바꿉니다.

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using Microsoft.WindowsAzure.Storage.Table;
		
		namespace EventHubReader
		{
		    class Device : TableEntity
		    {
		        public int value { get; set; }
		
		        public Device() { }
		        public Device(int id)
		        {
		            this.PartitionKey = id.ToString();
		            this.RowKey = System.Guid.NewGuid().ToString();
		        }
		    }
		}

	그러면 테이블 저장소에 파티션 키(이벤트 허브에서 읽은 장치 ID로 설정됨), 고유한 행 키 및 이벤트 허브에서 읽은 값으로 구성되는 엔터티가 만들어집니다. 또한 각 엔터티에는 엔터티가 테이블에 삽입될 때 자동으로 만들어지는 타임스탬프가 있습니다.

###bolt 수정

1. **솔루션 탐색기**에서 **EventHubReader** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Nuget 패키지 관리**를 선택합니다. **Json.Net** 패키지를 검색한 다음 솔루션에 추가합니다. 그러면 spout에서 받은 JSON 데이터를 쉽게 처리할 수 있습니다. 또한 테이블 저장소에 기록할 수 있도록 하는 **Microsoft Azure 저장소** 패키지를 추가합니다.

1. **Bolt.cs**를 열고 파일 맨 위에 다음을 추가합니다.

		using Newtonsoft.Json.Linq;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Table;

	그러면 bolt에서 JSON 데이터를 사용하여 더 쉽게 작업하고 테이블 저장소에 데이터를 기록할 수 있습니다.

2. `private int count;` 문을 찾아 다음으로 바꿉니다.

        private CloudTable table;

	이 문은 테이블에 연결할 때 사용됩니다.

4. 다음 코드를 찾습니다.

		Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));

	다음 코드로 바꿉니다.

		Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
        this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());

	이 코드는 **int** 대신 **문자열** 값을 수신하고 **CustomizedInteropJSONDeserialzer**를 사용하여 데이터를 역직렬화하도록 bolt에 지시합니다.

3. 이전 코드 바로 뒤에 다음 코드를 추가합니다.

		CloudStorageAccount storageAccount = CloudStorageAccount.Parse(Properties.Settings.Default.StorageConnection);
        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
        table = tableClient.GetTableReference(Properties.Settings.Default.TableName);
        table.CreateIfNotExists();

	이전에 구성한 연결 문자열을 사용하여 **이벤트** 테이블에 연결합니다. 연결 문자열이 없으면 새로 만듭니다.

2. **Execute** 메서드를 찾아 다음으로 바꿉니다.

		public void Execute(SCPTuple tuple)
        {
            Context.Logger.Info("Processing events");
            string eventValue = (string)tuple.GetValue(0);
            if (eventValue != null)
            {
                JObject eventData = JObject.Parse(eventValue);

                Device device = new Device((int)eventData["deviceId"]);
                device.value = (int)eventData["deviceValue"];

                TableOperation insertOperation = TableOperation.Insert(device);

                table.Execute(insertOperation);
            }
        }

	Json.net을 사용하여 spout에서 JSON 데이터를 구문 분석한 다음 **deviceId** 및 **deviceValue** 필드를 선택합니다. 초기화하는 동안 **deviceId**를 사용하여 새 **Device** 개체를 만들고 테이블에 대해 파티션 키를 설정합니다. 그런 다음 값을 **deviceValue**로 설정하고 마지막으로 테이블에 엔터티를 삽입합니다.

이제 이벤트 허브에서 데이터를 읽어 테이블 저장소의 **이벤트** 테이블에 저장하는 전체 토폴로지가 있습니다.

##토폴로지 배포

1. **EventHubReader** 솔루션을 엽니다. **솔루션 탐색기**에서 **EventHubReader** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Submit to Storm on HDInsight**를 선택합니다.

	![submit to storm](./media/hdinsight-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. **Submit Topology** 대화 상자에서 **Storm Cluster**를 선택합니다. **추가 구성**을 확장하고 **Java File Paths**, **...**를 차례로 선택한 다음 앞에서 다운로드한 **eventhubs-storm-spout-0.9-jar-with-dependencies.jar**가 포함된 디렉터리를 선택합니다. 마지막으로 **제출**을 선택합니다.

	![Image of submission dialog](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)

3. 토폴로지가 제출되었으면 **Storm Topologies Viewer**가 나타납니다. 대화 상자의 왼쪽에서 **EventHubReader** 토폴로지를 선택하여 토폴로지에 대한 통계를 확인합니다. 현재 이벤트 허브에 기록된 이벤트가 없으므로 아무 것도 발생하지 않아야 합니다.

	![example storage view](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. **EventHubWriter** 솔루션을 엽니다. **솔루션 탐색기**에서 **EventHubWriter** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Submit to Storm on HDInsight**를 선택합니다.

2. **Submit Topology** 대화 상자에서 **Storm Cluster**를 선택합니다. **추가 구성**을 확장하고 **Java File Paths**, **...**를 차례로 선택한 다음 앞에서 다운로드한 **eventhubs-storm-spout-0.9-jar-with-dependencies.jar**가 포함된 디렉터리를 선택합니다. 마지막으로 **제출**을 선택합니다.

5. 토폴로지가 제출되었으면 **Storm Topologies Viewer**에서 토폴로지 목록을 새로 고쳐 둘 다 클러스터에서 실행되는지 확인합니다.

6. 둘 다 실행되고 있으면 **서버 탐색기**, **Azure**, **저장소**를 차례로 확장한 다음 마지막으로 앞에서 만든 저장소 계정을 확장합니다. 저장소 계정에서 **테이블**을 확장합니다. 마지막으로 **이벤트** 테이블을 두 번 클릭하여 테이블을 엽니다. **EventHubReader** 토폴로지에서 테이블에 저장된 데이터가 표시되어야 합니다.

	* 이벤트는 이벤트 허브에 이벤트를 기록하는 **EventHubWriter** 토폴로지에서 생성됩니다.

	* 그런 다음 **EventHubReader**가 이벤트 허브에서 이벤트를 읽어 테이블 저장소의 **이벤트** 테이블에 저장합니다.

##토폴로지 중지

토폴로지를 중지하려면 **Storm Topology Viewer**에서 각 토폴로지를 선택한 다음 **중단**을 선택합니다.

![image of killing a topology](./media/hdinsight-storm-develop-csharp-event-hub-topology/killtopology.png)

##요약

이 문서에서는 C# 토폴로지에서 Java 이벤트 허브 Spout 및 Bolt를 사용하여 Azure 이벤트 허브의 데이터로 작업하는 방법을 배웠습니다. C# 토폴로지 만들기에 대해 자세히 알아보려면 다음을 참조하세요.

* [Visual Studio를 사용하여 HDInsight에서 Apache Storm에 대한 C# 토폴로지 개발](../hdinsight-storm-develop-csharp-visual-studio-topology/)

* [HDInsight Storm 예](https://github.com/hdinsight/hdinsight-storm-examples)








<!--HONumber=47-->
