<properties
   pageTitle="Visual Studio를 사용하여 HDInsight에서 Apache Storm에 대한 C# 토폴로지 개발 | Azure"
   description="Visual Studio에 HDInsight 도구를 사용하여 Visual Studio에서 단순한 단어 개수 토폴로지를 만들어 C#로 Storm 토폴로지를 만드는 방법에 대해 알아봅니다."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

#Visual Studio를 사용하여 HDInsight에서 Apache Storm에 대한 C# 토폴로지 개발

Visual Studio에 HDInsight 도구를 사용하여 C# Storm 토폴로지를 만드는 방법에 대해 알아봅니다. 이 문서는 Visual Studio에서 새 Storm 프로젝트를 만들고, 프로젝트를 로컬로 테스트하고, HDInsight 클러스터에서 Apache Storm에 배포하는 과정을 보여 줍니다.

C# 및 Java 구성 요소 둘 다 사용하는 하이브리드 토폴로지를 만드는 방법에 대해서도 배웁니다.

##필수 조건

* 다음과 같은 Visual Studio 버전 중 하나

	* Visual Studio 2012 <a href="http://www.microsoft.com/download/details.aspx?id=39305" target="_blank">업데이트 4</a>

	* Visual Studio 2013 <a href="http://www.microsoft.com/download/details.aspx?id=44921" target="_blank">업데이트 4</a> 또는 <a href="http://go.microsoft.com/fwlink/?LinkId=517284" target="_blank">Visual Studio 2013 Community</a>

	* <a href="http://visualstudio.com/downloads/visual-studio-2015-ctp-vs" target="_blank">Visual Studio 2015 CTP6</a>

* Azure SDK 2.5.1 이상

* Visual Studio용 HDInsight 도구 - Visual Studio용 HDInsight 도구를 설치하고 구성하려면 <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Visual Studio용 HDInsight 도구 사용 시작</a>을 참조하세요.

* HDInsight 클러스터의 Apache Storm - 클러스터를 만드는 단계는 <a href="../hdinsight-storm-getting-started/" target="_blank">HDInsight에서 Apache Storm 시작</a>을 참조하세요.

	> [AZURE.NOTE] 현재 Visual Studio용 HDInsight 도구는 HDInsight 클러스터 버전 3.2의 Storm만 지원합니다.

##C# 토폴로지 만들기

1. 최신 버전의 Visual Studio용 HDInsight 도구를 아직 설치하지 않은 경우 <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Visual Studio용 HDInsight 도구 사용 시작</a>을 참조하세요.

2. Visual Studio를 열고 **파일**, **새로 만들기**, **프로젝트**를 차례로 선택합니다.

3. **새 프로젝트** 대화 상자에서 **설치됨**, **템플릿**을 확장하고 **HDInsight**를 선택합니다. 템플릿 목록에서 **Storm 응용 프로그램**을 선택합니다. 대화 상자 아래쪽에서 응용 프로그램 이름으로 **WordCount**를 입력합니다.

	![image](./media/hdinsight-storm-develop-csharp-visual-studio/new-project.png)

4. 프로젝트를 만들면 다음과 같은 파일이 생깁니다.

	* **Program.cs** - 프로젝트에 대한 토폴로지를 정의합니다. 기본적으로 하나의 spout 및 하나의 bolt로 구성된 기본 토폴로지가 만들어집니다.

	* **Spout.cs** - 임의의 숫자를 내보내는 예시 spout입니다.

	* **Bolt.cs** - spout가 내보낸 숫자의 개수를 유지하는 예시 bolt입니다.

	프로젝트 만들기의 일부로 최신 <a href="https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/" target="_blank">SCP.NET 패키지</a>가 Nuget에서 다운로드됩니다.

다음 섹션에서 이 프로젝트를 기본 WordCount 응용 프로그램으로 수정합니다.

###spout 만들기

5. **Spout.cs** 열기 - spout는 외부 소스에서 토폴로지로 데이터를 읽는 데 사용됩니다. spout의 주요 구성 요소:

	* **NextTuple** - Spout이 새 튜플 내보낼 수 있도록 허용되면 Storm이 호출합니다.

	* **Ack** - (트랜잭션 토폴로지만 해당) - 이 spout에서 보낸 튜플의 경우 토폴로지에서 다른 구성 요소에 의해 시작된 승인을 처리합니다. 튜플을 승인하면 spout가 다운스트림 구성 요소에 의해 성공적으로 처리되었음을 알 수 있습니다.

	* **Fail** - (트랜잭션 토폴로지만 해당) - 토폴로지에서 다른 구성 요소 처리에 실패한 튜플을 처리합니다. 튜플을 다시 내보낼 기회를 제공하여 다시 처리할 수 있도록 합니다.

6. Spout 클래스의 구성 요소를 다음과 같이 바꿉니다. 이는 문장을 토폴로지에 임의로 내보낼 spout를 만듭니다.

		private Context ctx;
        private Random r = new Random();
        string[] sentences = new string[] {
            "the cow jumped over the moon",
            "an apple a day keeps the doctor away",
            "four score and seven years ago",
            "snow white and the seven dwarfs",
            "i am at two with nature"
        };

        public Spout(Context ctx)
        {
			// Set the instance context
            this.ctx = ctx;

            Context.Logger.Info("Generator constructor called");

            // Declare Output schema
            Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
            // The schema for the default output stream is
            // a tuple that contains a string field
            outputSchema.Add("default", new List<Type>() { typeof(string) });
            this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
        }

		// Get an instance of the spout
        public static Spout Get(Context ctx, Dictionary<string, Object> parms)
        {
            return new Spout(ctx);
        }

        public void NextTuple(Dictionary<string, Object> parms)
        {
            Context.Logger.Info("NextTuple enter");
            // The sentence to be emitted
            string sentence;

            // Get a random sentence
            sentence = sentences[r.Next(0, sentences.Length - 1)];
            Context.Logger.Info("Emit: {0}", sentence);
            // Emit it
            this.ctx.Emit(new Values(sentence));

            Context.Logger.Info("NextTuple exit");
        }

        public void Ack(long seqId, Dictionary<string, Object> parms)
        {
			// Only used for transactional topologies
        }

        public void Fail(long seqId, Dictionary<string, Object> parms)
        {
			// Only used for transactional topologies
        }

	주석을 읽어보면 이 코드의 내용을 이해할 수 있습니다.

###bolt 구현

1. 프로젝트에서 기존의 **Bolt.cs**를 삭제합니다.

2. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가**, **새 항목**을 선택합니다. 목록에서 **Storm Bolt**를 선택하고 이름으로 **Splitter.cs**를 입력합니다. 이를 반복하여 **Counter.cs**라는 두 번째 bolt를 만듭니다.

	* **Splitter.cs** - 개별 단어로 문장을 나누고 단어의 새 스트림으로 내보내는 bolt를 구현합니다.

	* **Counter.cs** - 각각의 단어 수를 세고 단어의 새 스트림 및 각 단어의 개수를 내보내는 bolt를 구현합니다.

	> [AZURE.NOTE] 이러한 bolt가 스트림을 읽고 쓰는 동안, 데이터베이스, 서비스 등과 통신하는 데에도 bolt를 사용할 수 있습니다.

6. **Splitter.cs**를 엽니다. 기본적으로 **Execute**라는 하나의 메서드만 있습니다. 이는 bolt가 처리에 대한 튜플을 받을 때 호출됩니다. 여기에서 들어오는 튜플을 읽고 처리하며 나가는 튜플을 내보낼 수 있습니다.

4. **Splitter** 클래스의 구성 요소를 다음과 같이 바꿉니다.

		private Context ctx;

        // Constructor
        public Splitter(Context ctx)
        {
            Context.Logger.Info("Splitter constructor called");
            this.ctx = ctx;

            // Declare Input and Output schemas
            Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
            // Input contains a tuple with a string field (the sentence)
            inputSchema.Add("default", new List<Type>() { typeof(string) });
            Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
            // Outbound contains a tuple with a string field (the word)
            outputSchema.Add("default", new List<Type>() { typeof(string) });
            this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
        }

        // Get a new instance of the bolt
        public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
        {
            return new Splitter(ctx);
        }

        // Called when a new tuple is available
        public void Execute(SCPTuple tuple)
        {
            Context.Logger.Info("Execute enter");

            // Get the sentance from the tuple
            string sentence = tuple.GetString(0);
            // Split at space characters
            foreach (string word in sentence.Split(' '))
            {
                Context.Logger.Info("Emit: {0}", word);
                //Emit each word
                this.ctx.Emit(new Values(word));
            }

            Context.Logger.Info("Execute exit");
        }

	코드를 읽어보면 이 코드의 내용을 이해할 수 있습니다.

6. **Counter.cs**를 열고 클래스 콘텐츠를 다음으로 바꿉니다.

		private Context ctx;

        // Dictionary for holding words and counts
        private Dictionary<string, int> counts = new Dictionary<string, int>();

        // Constructor
        public Counter(Context ctx)
        {
            Context.Logger.Info("Counter constructor called");
            // Set instance context
            this.ctx = ctx;

            // Declare Input and Output schemas
            Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
            // A tuple containing a string field - the word
            inputSchema.Add("default", new List<Type>() { typeof(string) });

            Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
            // A tuple containing a string and integer field - the word and the word count
            outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
            this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
        }

        // Get a new instance
        public static Counter Get(Context ctx, Dictionary<string, Object> parms)
        {
            return new Counter(ctx);
        }

        // Called when a new tuple is available
        public void Execute(SCPTuple tuple)
        {
            Context.Logger.Info("Execute enter");

            // Get the word from the tuple
            string word = tuple.GetString(0);
            // Do we already have an entry for the word in the dictionary?
            // If no, create one with a count of 0
            int count = counts.ContainsKey(word) ? counts[word] : 0;
            // Increment the count
            count++;
            // Update the count in the dictionary
            counts[word] = count;

            Context.Logger.Info("Emit: {0}, count: {1}", word, count);
            // Emit the word and count information
            this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));

            Context.Logger.Info("Execute exit");
        }

	코드를 읽어보면 이 코드의 내용을 이해할 수 있습니다.

###토폴로지 정의

Spout 및 bolt는 그래프로 정렬되며, 이는 구성 요소 간에 데이터의 흐름 방식을 정의합니다. 이 토폴로지의 경우 그래프는 다음과 같습니다.

![image of how components are arranged](./media/hdinsight-storm-develop-csharp-visual-studio/wordcount-topology.png)

Splitter bolt의 인스턴스로 배포되는 spout가 문장을 내보냅니다. Splitter bolt는 Counter bolt로 배포된 단어로 문장을 나눕니다.

단어 개수가 Counter 인스턴스에서 로컬로 처리되기 때문에 특정 단어가 동일한 Counter bolt 인스턴스로 흐르도록 하려고 하므로 특정 단어의 추적을 유지하는 한 인스턴스만 있게 됩니다. 하지만 Splitter bolt의 경우 어느 bolt가 어느 문장을 받는지는 중요하지 않기 때문에 해당 인스턴스로 문장의 부하를 분산하려고 합니다.

**Program.cs**를 엽니다. 여기에서 중요한 메서드는 Storm에 제출되는 토폴로지를 정의하는 데 사용하는 **ITopologyBuilder**입니다.  위에서 설명한 토폴로지를 구성하려면 **ITopologyBuilder**의 구성 요소를 다음과 같이 바꿉니다.

		// Create a new topology named 'WordCount'
        TopologyBuilder topologyBuilder = new TopologyBuilder("WordCount");

        // Add the spout to the topology.
        // Name the component 'sentences'
        // Name the field that is emitted as 'sentence'
        topologyBuilder.SetSpout(
            "sentences",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
            },
            1);
        // Add the splitter bolt to the topology.
        // Name the component 'splitter'
        // Name the field that is emitted 'word'
        // Use suffleGrouping to distribute incoming tuples
        //   from the 'sentences' spout across instances
        //   of the splitter
        topologyBuilder.SetBolt(
            "splitter",
            Splitter.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
            },
            1).shuffleGrouping("sentences");

        // Add the counter bolt to the topology.
        // Name the component 'counter'
        // Name the fields that are emitted 'word' and 'count'
        // Use fieldsGrouping to ensure that tuples are routed
        //   to counter instances based on the contents of field
        //   position 0 (the word). This could also have been 
        //   List<string>(){"word"}.
        //   This ensures that the word 'jumped', for example, will always
        //   go to the same instance
        topologyBuilder.SetBolt(
            "counter",
            Counter.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
            },
            1).fieldsGrouping("splitter", new List<int>() { 0 });

        // Add topology config
        topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
        {
            {"topology.kryo.register","[\"[B\"]"}
        });

        return topologyBuilder;

코드를 읽어보면 이 코드의 내용을 이해할 수 있습니다.

##토폴로지 제출

1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **HDInsight에서 Storm에 제출**을 선택합니다.

	> [AZURE.NOTE] 메시지가 표시되면 Azure 구독에 대한 로그인 자격 증명을 입력합니다. 하나 이상의 구독이 있는 경우 HDInsight 클러스터의 Storm을 포함하는 자격 증명으로 로그인합니다.

2. **Storm 클러스터** 드롭다운에서 HDInsight 클러스터의 Storm을 선택한 다음 **제출**을 선택합니다. **출력** 창을 통해 제출의 성공 여부를 모니터링할 수 있습니다.

3. 토폴로지 제출에 성공하면 클러스터에 대한 **Storm 토폴로지**가 나타납니다. 실행 중인 토폴로지에 대한 정보를 보려면 목록에서 **WordCount** 토폴로지를 선택합니다.

	> [AZURE.NOTE] **Azure**, **HDInsight**를 확장한 다음 HDInsight 클러스터의 Storm을 마우스 오른쪽 단추로 클릭하고 **Storm 토폴로지 보기**를 선택하여 **서버 탐색기**에서 **Storm 토폴로지**를 볼 수도 있습니다.

	이러한 구성 요소에 대한 정보를 보려면 spout 또는 bolt 링크를 사용합니다. 선택한 각각의 항목에 대해 새 창이 열립니다.

4. 토폴로지를 중단하려면 **토폴로지 요약** 보기에서 **중단**을 선택합니다.

	> [AZURE.NOTE] Storm 토폴로지는 중단되거나 클러스터가 삭제될 때까지 계속 실행됩니다.

##트랜잭션 토폴로지

이전 토폴로지는 비트랜잭션입니다. 토폴로지 내의 구성 요소는 토폴로지에서 구성 요소에 의해 처리에 실패하는 경우 메시지 재생 기능을 구현하지 않습니다. 예를 들어, 트랜잭션 토폴로지는 새 프로젝트를 만들고 프로젝트 유형으로 **Storm 샘플**을 선택합니다.

트랜잭션 토폴로지는 데이터 재생을 지원하기 위해 다음과 같이 구현합니다.

* **메타데이터 캐싱** - spout는 내보낸 데이터에 대한 메타데이터를 저장해야 하므로 오류가 발생하는 경우 해당 데이터를 검색하거나 내보낼 수 있습니다. 샘플로 내보낸 데이터는 작기 때문에 각 튜플의 원시 데이터는 재생을 위해 사전에 저장됩니다.

* **Ack** - 토폴로지에서 각각의 bolt는 `this.ctx.Ack(tuple)`를 호출하여 튜플을 성공적으로 처리했음을 승인할 수 있습니다. 모든 bolt가 튜플을 승인하면 spout의 `Ack` 메서드가 호출됩니다. 데이터가 완전히 처리되었기 때문에 이를 통해 spout는 재생을 위해 캐시된 데이터를 제거할 수 있습니다.

* **Fail** - 각 bolt는 `this.ctx.Fail(tuple)`을 호출하여 튜플 처리에 실패했음을 나타냅니다. 캐시된 메타데이터를 사용하여 튜플을 재생할 수 있는 spout의 `Fail` 메서드로 오류가 전파됩니다.

* **Sequence ID** - 튜플을 내보낼 때 시퀀스 ID를 지정할 수 있습니다. 재생(Ack 및 Fail) 처리를 위해 튜플을 식별하는 값이어야 합니다. 예를 들어 **Storm 샘플** 프로젝트의 spout는 데이터를 내보낼 때 다음과 같이 사용합니다.

		this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);

	이는 lastSeqId에 포함되는 시퀀스 ID 값과 함께 문장을 포함하는 새 튜플을 기본 스트림으로 내보냅니다. 이 예시에서 lastSeqId는 제출된 모든 튜플마다 단순히 증가합니다.

**Storm 샘플** 프로젝트의 설명대로, 구성 요소가 트랜잭션인지 아닌지는 구성에 따라 런타임으로 설정할 수 있습니다.

##하이브리드 토폴로지

Visual Studio용 HDInsight 도구는 다른 구성 요소가 Java일 때 일부 구성 요소가 C#인 하이브리드 토폴로지를 만드는 데 사용할 수도 있습니다.

예시 하이브리드 토폴로지의 경우 새 프로젝트를 만들고 **Storm 하이브리드 샘플**을 선택합니다. 다음을 설명하는 여러 토폴로지를 포함하는 완전히 주석 처리된 샘플을 만들게 됩니다.

* **Java spout** 및 **C# bolt** - **HybridTopology_javaSpout_csharpBolt**에서 정의됩니다.

	* 트랜잭션 버전은 **HybridTopologyTx_javaSpout_csharpBolt**에서 정의됩니다.

* **C# spout** 및 **Java bolt** - **HybridTopology_csharpSpout_javaBolt**에서 정의됩니다.

	* 트랜잭션 버전은 **HybridTopologyTx_csharpSpout_javaBolt**에서 정의됩니다.

		> [AZURE.NOTE] 이 버전은 텍스트 파일에서 clojure 코드를 Java 구성 요소로 사용하는 방법을 설명하기도 합니다.

프로젝트가 제출될 때 사용된 토폴로지 사이에서 전환하려면 클러스터에 제출하기 전에 사용하려는 토폴로지로 `[Active(true)]` 문을 이동합니다.

> [AZURE.NOTE] 필요한 모든 Java 파일이 **JavaDependency** 폴더에서 이 프로젝트의 일부로 제공됩니다.

다음은 하이브리드 토폴로지를 만들고 제출할 때 사용됩니다.

* **JavaComponentConstructor**는 spout 또는 bolt에 대해 Java 클래스의 새 인스턴스를 만드는 데 사용되어야 합니다.

* **microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer**는 Java 개체에서 JSON으로 Java 구성 요소의 데이터 입/출력을 직렬화하는 데 사용해야 합니다.

* 서버에 토폴로지를 **제출할 때** **Java 파일 경로**를 지정하려면 **추가 구성** 옵션을 사용해야 합니다. 지정된 경로는 Java 클래스를 포함하는 jar 파일이 들어 있는 디렉터리여야 합니다.

##문제 해결

###로컬로 토폴로지 테스트

토폴로지를 클러스터로 배포하는 것은 쉽지만, 일부 경우에 토폴로지를 로컬로 테스트해야 할 수 있습니다. 이 기사의 예제 토폴로지를 사용자의 개발 환경에서 로컬로 실행 및 테스트하려면 다음 단계를 참조하세요.

1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다. 프로젝트 속성에서 **출력 유형**을 **콘솔 응용 프로그램**으로 변경합니다.

	![output type](./media/hdinsight-storm-develop-csharp-visual-studio/outputtype.png)

	> [AZURE.NOTE] 토폴로지를 클러스터로 배포하기 전에 **출력 유형**을 **클래스 라이브러리**로 다시 변경해야 합니다.

2. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **추가**, **새 항목**을 차례로 선택합니다. **클래스**를 선택하고 클래스 이름으로 **LocalTest.cs**를 입력합니다. 마지막으로 **추가**를 선택합니다.

3. **LocalTest.cs**를 열고 다음 `using` 문을 맨 위에 추가합니다.

		using Microsoft.SCP;

3. **LocalTest** 클래스의 내용으로 다음을 사용합니다.

		// Drives the topology components
		public void RunTestCase()
		{
		    // An empty dictionary for use when creating components
		    Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();
		
		    #region Test the spout
		    {
		        Console.WriteLine("Starting spout");
		        // LocalContext is a local-mode context that can be used to initialize
		        // components in the development environment.
		        LocalContext spoutCtx = LocalContext.Get();
		        // Get a new instance of the spout, using the local context
		        Spout sentences = Spout.Get(spoutCtx, emptyDictionary);
		
		        // Emit 10 tuples
		        for (int i = 0; i < 10; i++)
		        {
		            sentences.NextTuple(emptyDictionary);
		        }
		        // Use LocalContext to persist the data stream to file
		        spoutCtx.WriteMsgQueueToFile("sentences.txt");
		        Console.WriteLine("Spout finished");
		    }
		    #endregion
		
		    #region Test the splitter bolt
		    {
		        Console.WriteLine("Starting splitter bolt");
		        // LocalContext is a local-mode context that can be used to initialize
		        // components in the development environment.
		        LocalContext splitterCtx = LocalContext.Get();
		        // Get a new instance of the bolt
		        Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);
		
		        // Set the data stream to the data created by the spout
		        splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
		        // Get a batch of tuples from the stream
		        List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
		        // Process each tuple in the batch
		        foreach (SCPTuple tuple in batch)
		        {
		            splitter.Execute(tuple);
		        }
		        // Use LocalContext to persist the data stream to file
		        splitterCtx.WriteMsgQueueToFile("splitter.txt");
		        Console.WriteLine("Splitter bolt finished");
		    }
		    #endregion
		
		    #region Test the counter bolt
		    {
		        Console.WriteLine("Starting counter bolt");
		        // LocalContext is a local-mode context that can be used to initialize
		        // components in the development environment.
		        LocalContext counterCtx = LocalContext.Get();
		        // Get a new instance of the bolt
		        Counter counter = Counter.Get(counterCtx, emptyDictionary);
		
		        // Set the data stream to the data created by splitter bolt
		        counterCtx.ReadFromFileToMsgQueue("splitter.txt");
		        // Get a batch of tuples from the stream
		        List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
		        // Process each tuple in the batch
		        foreach (SCPTuple tuple in batch)
		        {
		            counter.Execute(tuple);
		        }
		        // Use LocalContext to persist the data stream to file
		        counterCtx.WriteMsgQueueToFile("counter.txt");
		        Console.WriteLine("Counter bolt finished");
		    }
		    #endregion
		}

	코드 주석을 읽어보세요. 이 코드는 로컬 드라이브의 텍스트 파일로 구성 요소 사이의 데이터 스트림을 지속하는 개발 환경에서 구성 요소를 실행하기 위해 **LocalContext**를 사용합니다.

2. **Program.cs**를 열고 **주** 메서드에 다음을 추가합니다.

        Console.WriteLine("Starting tests");
        System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
        // Initialize the runtime
        SCPRuntime.Initialize();

        //If we are not running under the local context, throw an error
        if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
        {
            throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
        }
		// Create test instance
        LocalTest tests = new LocalTest();
		// Run tests
        tests.RunTestCase();
        Console.WriteLine("Tests finished");
        Console.ReadKey();

3. 변경 내용을 저장한 다음 프로젝트를 시작하려면 **F5** 또는 **Debug**, **Start Debugging**을 사용합니다. 콘솔 창이 나타나며 로그 상태가 테스트로 진행됩니다. **Tests finished**가 나타나면 아무 키를 눌러서 창을 닫습니다.

4. **탐색기**를 사용하면 프로젝트를 포함하는 디렉터리로 이동합니다. 예를 들어 **C:\Users\<your_user_name>\Documents\Visual Studio 2013\Projects\WordCount\WordCount**입니다. 이 디렉터리에서**Bin**, **Debug**를 차례로 엽니다. 테스트가 실행되었을 때 처리한 텍스트 파일이 보입니다(sentences.txt, counter.txt, 및 splitter.txt). 각각의 텍스트 파일을 열고 데이터를 검사합니다.

	> [AZURE.NOTE] 문자열 데이터는 이러한 파일에서 10진수 값의 배열로 유지됩니다. 예를 들어, **splitter.txt** 파일에서 [[97,103,111]]은 단어 '및'입니다.

기본 단어 개수 응용 프로그램 로컬 테스트는 상당히 간단하지만, 실제 값은 데이터 소스와 연결되는 복잡한 토폴로지가 있거나 복잡한 데이터 분석을 수행할 때 들어옵니다. 이러한 프로젝트를 작업할 때 문제를 격리하기 위해 구성 요소의 코드에 중단점 및 단계를 설정해야 할 수 있습니다.

> [AZURE.NOTE] HDInsight의 Storm으로 배포하기 전에 **프로젝트 유형**을 **클래스 라이브러리**로 다시 설정해야 합니다.

###로그 정보

 `Context.Logger`를 사용하여 토폴로지 구성 요소에서 정보를 쉽게 로그할 수 있습니다. 예를 들어, 다음은 정보 제공용 로그 항목을 만듭니다.

	Context.Logger.Info("Component started");

로그된 정보는 **서버 탐색기**에서 찾을 수 있는 **Hadoop 서비스 로그**에서 볼 수 있습니다. HDInsight 클러스터의 Storm에 대한 항목을 확장한 다음 **Hadoop 서비스 로그**를 확장합니다. 마지막으로 보려는 로그 파일을 선택합니다.

> [AZURE.NOTE] 로그는 사용자의 클러스터에서 사용되는 Azure 저장소 계정에 저장됩니다. Visual Studio 사용 시 로그인하는 것과 다른 구독인 경우, 이 정보를 보려면 저장소 계정을 포함하는 구독으로 로그인해야 합니다.

###오류 정보 보기

실행 중인 토폴로지에서 발행한 오류를 보려면 다음 단계를 따릅니다.

1. **서버 탐색기**에서 HDInsight 클러스터의 Storm을 마우스 오른쪽 단추로 클릭하고 **Storm 토폴로지 보기**를 선택합니다.

2. **Spout** 및 **Bolt**의 경우, **마지막 오류** 열에 마지막으로 발생한 오류 정보가 있습니다.

2. 오류가 나열된 구성 요소의 **Spout Id** 또는 **Bolt Id**를 선택합니다. 표시되는 세부 사항 페이지에서 추가 오류 정보는 페이지 아래쪽의 **오류** 섹션에 나열됩니다.

3. 더 많은 정보를 가져오려면 페이지의 **Executor** 섹션에서 **Port**를 선택하여 마지막 몇 분 동안의 Storm 작업자 로그를 볼 수 있습니다.


##다음 단계

Visual Studio용 HDInsight 도구에서 Storm 토폴로지를 개발하고 배포하는 방법을 배웠으므로 [HDInsight의 Storm으로 Azure 이벤트 허브에서 이벤트를 처리](../hdinsight-storm-develop-csharp-event-hub-topology/)하는 방법을 알아봅니다.

C# 토폴로지를 만들기에 대한 자세한 정보를 검색하려면 [https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md)를 방문하세요.

Hdinsight 사용 방법 및 HDinsight의 Storm 샘플에 대한 자세한 내용은 다음을 참조하세요.

**HDInsight의 Apache Storm**

* [HDInsight에서 Apache Storm을 사용하는 토폴로지 배포 및 모니터링](../hdinsight-storm-deploy-monitor-topology/)

* [HDInsight의 Storm으로 Azure 이벤트 허브에서 이벤트 처리](../hdinsight-storm-develop-csharp-event-hub-topology/)

* [HDInsight에서 Apache Storm에 대한 Java 기반 토폴로지 개발](../hdinsight-storm-develop-java-topology/)

* [HDInsight에서 Apache Storm을 사용하는 Twitter 추세](../hdinsight-storm-twitter-trending/)

* [HDInsight의 Storm 예](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md)

**HDInsight의 Apache Hadoop**

* [HDInsight에서 Hadoop과 Hive 사용](../hdinsight-use-hive/)

* [HDInsight에서 Hadoop과 Pig 사용](../hdinsight-use-pig/)

* [HDInsight에서 Hadoop과 MapReduce 사용](../hdinsight-use-mapreduce/)

**HDInsight의 Apache HBase**

* [HDInsight에서 HBase 시작](../hdinsight-hbase-get-started/)

<!--HONumber=47-->
