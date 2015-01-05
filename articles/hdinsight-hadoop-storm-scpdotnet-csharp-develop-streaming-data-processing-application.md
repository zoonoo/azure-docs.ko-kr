<properties title="Develop streaming data processing applications with SCP.NET and C# on Storm in HDInsight" pageTitle="Storm에 대해 SCP.NET을 사용하여 스트리밍 데이터 처리 앱 개발 | Azure" description="Learn how to develop streaming data processing applications with SCP.NET and C# on Storm in HDInsight." services="hdinsight" solutions="" documentationCenter="" authors="Qianlin Xia" videoId="" scriptId="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/15/2014" ms.author="qixia" />

#HDInsight에서 Stream Computing Platform 및 Storm에 대해 C#으로 스트리밍 데이터 처리 응용 프로그램 개발

SCP(Stream Computing Platform)는 .NET을 사용하여 실시간의 안정적이고 일관성 있는 분산 고성능 데이터 처리 응용 프로그램을 빌드하기 위한 플랫폼입니다. HDInsight에서 사용할 수 있는 오픈 소스 실시간 스트림 처리 시스템인 [Apache Storm](http://storm.incubator.apache.org/) 위에 빌드됩니다.

이 문서에서는 다음에 대해 알아봅니다.

* SCP의 개념 및 Storm에서의 작동 방식

* SCP 솔루션을 만드는 방법

* SCP 솔루션을 테스트하는 방법

* SCP 솔루션을 HDInsight Storm 클러스터에 배포하는 방법

##필수 조건

* Azure 구독

* HDInsight Storm 클러스터

* Visual Studio 2010 또는 2013

##목차

* [SCP 및 Storm](#scpandstorm)

##<a id="scpandstorm"></a>SCP 및 Storm

Apache Storm은 Hadoop 클러스터에서 실행되는 분산 계산 시스템이며, 이를 통해 실시간 데이터 처리를 수행할 수 있습니다. Storm은 JVM(Java Virtual Machine)에서 실행되는 동안 다양한 프로그래밍 언어로 **토폴로지**라는 솔루션을 구현할 수 있도록 설계되었습니다. 여러 언어로 작성된 구성 요소를 혼합하여 토폴로지를 만들 수도 있습니다.

SCP에서는 .NET을 사용하여 Storm 솔루션을 쉽게 만들 수 있는 라이브러리를 제공합니다. Azure HDInsight Storm 클러스터에는 만들려는 SCP 솔루션을 실행하는 데 필요한 서버 쪽 솔루션이 포함됩니다.

HDInsight Storm에 대한 자세한 내용은 [HDInsight Storm 개요](http://azure.microsoft.com/ko-kr/documentation/articles/hdinsight-storm-overview/)를 참조하세요.

###SCP 솔루션 디자인

SCP에서는 다음 Storm 구성 요소를 만드는 데 사용되는 인터페이스를 제공합니다.

* Spout - 원본(파일, 데이터베이스, API 등)의 데이터를 사용하고 하나 이상의 튜플 스트림(순서가 지정된 요소 목록)을 내보냅니다.
* Bolt - 하나 이상의 스트림을 사용하고 선택적으로 하나 이상의 스트림을 내보냅니다.
* 토폴로지 - 데이터가 Spout와 Bolt 간에 이동하는 방식, Spout 및 Bolt의 병렬 처리, 구성 정보를 정의합니다.

> [AZURE.NOTE] 또한 Spout 및 Bolt는 일반 처리를 구현합니다. 예를 들어 Spout는 수신 메시지를 여러 튜플로 나누거나, 하나의 튜플을 내보내고 필요한 값을 Bolt가 추출하도록 합니다. 마찬가지로 데이터를 데이터 저장소에 써야 할 경우 Bolt에 이를 구현합니다.

솔루션 디자인 시 고려해야 할 내용은 다음과 같습니다.

* 사용하고 있는 데이터 원본은 무엇인가요? 이 솔루션을 구현하는 Spout가 있어야 합니다.
* 어떤 처리가 필요한가요? Spout 및/또는 Bolt에서 이 솔루션을 구현해야 합니다.
* 처리가 여러 Bolt로 나뉠 경우 Bolt 간에 데이터가 어떻게 이동하나요? 토폴로지를 통해 이 솔루션을 설명해야 합니다.
* 처리는 HDInsight 클러스터의 노드 간에 어떻게 분산되나요? 토폴로지를 통해 이 솔루션을 설명해야 합니다.

##SCP SDK 설치

SCP SDK는 HDInsight Storm 클러스터에서 제공됩니다. [HDInsight Storm 클러스터를 만들고](http://azure.microsoft.com/ko-kr/documentation/articles/hdinsight-storm-getting-started/)나서 다음 단계에 따라 SDK를 로컬 개발 환경에 다운로드합니다.

1. [Azure 관리 포털](https://manage.windowsazure.com)에 로그인합니다.

2. 왼쪽 창에서 **HDINSIGHT**를 클릭합니다. 배포된 HDInsight 클러스터 목록이 표시됩니다.

3. 연결할 HDInsight 클러스터를 클릭합니다.

4. 페이지 맨 위에서 **구성**을 클릭합니다.

5. 페이지 맨 아래에서 **원격 사용**을 클릭합니다.

6. 원격 데스크톱 구성 마법사에서 원격 데스크톱의 **사용자 이름** 및 **암호**를 입력합니다. **만료 날짜** 상자에 만료 날짜를 입력하고 선택 아이콘을 클릭합니다.

7. 원격 데스크톱이 사용하도록 설정되면 페이지 맨 아래에서 **연결**을 클릭하고 지침에 따릅니다.

8. 원격 데스크톱을 통해 클러스터에 연결되고 나면 **파일 탐색기**를 열고 주소 표시줄에 **%storm_home%\examples**를 입력합니다.

9. **SDK** 폴더를 마우스 오른쪽 단추로 클릭하고 **복사**를 선택합니다.

10. 로컬 개발 환경에서 **파일 탐색기**를 열고 SDK를 저장할 위치로 이동합니다. 마우스 오른쪽 단추로 클릭하고 **붙여넣기**를 선택합니다.

##SCP 솔루션 만들기

1. Visual Studio에서 **콘솔 응용 프로그램**에 대한 새 프로젝트를 만듭니다. 이 솔루션의 이름을 **WordCount**로 지정합니다.

2. **솔루션 탐색기**에서 **참조**를 마우스 오른쪽 단추로 클릭하고 **참조 추가**를 선택합니다.

3. **참조 관리자 ** 맨 아래에서 **찾아보기** 단추를 선택하고 앞에서 다운로드한 SDK 폴더로 이동합니다. **Microsoft.SCP.dll** 및 **Microsoft.SCPLogger.dll**을 선택하고 **추가**를 클릭합니다. **확인**을 클릭하여 참조 관리자 창을 닫습니다.

###Spout 만들기

1. **솔루션 탐색기**에서 **WordCount**를 마우스 오른쪽 단추로 클릭하고 **추가 | 새 항목**을 선택합니다. **클래스**를 선택하고 이름으로 **WordSpout.cs**를 입력합니다. 마지막으로 **추가**를 클릭합니다.

2. **WordSpout.cs** 파일을 열고 기존 코드를 다음으로 바꿉니다. 이 코드의 작동 방식을 이해하려면 주석을 읽어야 합니다.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.SCP;
        using System.Threading;

        namespace WordCount
        {
            class WordSpout : ISCPSpout
            {
                //Context
                private Context ctx;

                private Random rand = new Random();
                //The sentences to emit
                string[] sentences = new string[] {
                                                  "the cow jumped over the moon",
                                                  "an apple a day keeps the doctor away",
                                                  "four score and seven years ago",
                                                  "snow white and the seven dwarfs",
                                                  "i am at two with nature"};

                //Constructor
                public WordSpout(Context ctx)
                {
                    //Log that we are starting
                    Context.Logger.Info("WordSpout constructor called");
                    //Store the context that was passed
                    this.ctx = ctx;

                    //Define the schema for the emitted tuples
                    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
                    //In this case, just a string tuple
                    outputSchema.Add("default", new List<Type>() { typeof(string) });
                    //Declare the schema for the stream
                    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
                }

                //Return a new instance of the spout
                public static WordSpout Get(Context ctx, Dictionary<string, Object> parms)
                {
                    return new WordSpout(ctx);
                }

                //Emit the next tuple
                //NOTE: When using data from an external data source
                //such as Service Bus, Event Hub, Twitter, etc.,
                //you would read and emit it in NextTuple
                public void NextTuple(Dictionary<string, object> parms)
                {
                    Context.Logger.Info("NextTuple enter");

                    //Get a random sentence
                    string sentence = sentences[rand.Next(0, sentences.Length - 1)];
                    Context.Logger.Info("Emit: {0}", sentence);
                    //Emit the sentence
                    this.ctx.Emit(new Values(sentence));

                    Context.Logger.Info("NextTuple exit");
                }

                //Ack's are not implemented
                public void Ack(long seqId, Dictionary<string, object> parms)
                {
                    throw new NotImplementedException();
                }

                //Ack's are not implemented, so
                //fail should never be called
                public void Fail(long seqId, Dictionary<string, object> parms)
                {
                    throw new NotImplementedException();
                }
            }
        }

###테스트 만들기

1. **솔루션 탐색기**에서 **WordCount**를 마우스 오른쪽 단추로 클릭하고 **추가 | 새 항목**을 선택합니다. **클래스**를 선택하고 이름으로 **LocalTest.cs**를 입력합니다. 마지막으로 **추가**를 클릭합니다.

2. **LocalTest.cs** 파일을 열고 기존 코드를 다음으로 바꿉니다. Spout를 테스트하는 데 사용되는 코드는 Bolt 테스트에 사용될 코드와 매우 비슷합니다.

        using Microsoft.SCP;
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;

        namespace WordCount
        {
            class LocalTest
            {
                //Run tests
                public void RunTestCase()
                {
                    Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();
                    //Spout tests
                    {
                        //Get local context
                        LocalContext spoutCtx = LocalContext.Get();
                        //Get an instance of the spout
                        WordSpout spout = WordSpout.Get(spoutCtx, emptyDictionary);
                        //Call NextTuple to emit data
                        for (int i = 0; i < 10; i++)
                        {
                            spout.NextTuple(emptyDictionary);
                        }
                        //Store the stream for the next component
                        spoutCtx.WriteMsgQueueToFile("spout.txt");
                    }
                }
            }
        }

3. **Program.cs**를 열고 기존 코드를 다음으로 바꿉니다.

        using Microsoft.SCP;
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;

        namespace WordCount
        {
            class Program
            {
                static void Main(string[] args)
                {
                    if (args.Count() > 0)
                    {
                        //Code to run on HDInsight cluster will go here
                    }
                    else
                    {
                        //Set log prefix information for the component being tested
                        System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
                        //Initialize the runtime
                        SCPRuntime.Initialize();

                        //If we are not running under the local context, throw an error
                        if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
                        {
                            throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
                        }
                        //Create an instance of LocalTest
                        LocalTest localTest = new LocalTest();
                        //Run the tests
                        localTest.RunTestCase();
                    }
                }
            }
        }

3. 응용 프로그램을 실행합니다. 실행이 완료된 후 Visual Studio 프로젝트 아래에서 **WordCount\bin\debug** 디렉터리를 확인합니다. 이 테스트 중에 Spout에서 내보낸 데이터가 들어 있는 **spout.txt** 파일이 있어야 합니다. 다음 텍스트와 같은 내용이 표시되어야 합니다.

        {"__isset":{"streamId":true,"tupleId":true,"evt":true,"data":true},"StreamId":"default","TupleId":"","Evt":1000,"Data":[[97,110,32,97,112,112,108,101,32,97,32,100,97,121,32,107,101,101,112,115,32,116,104,101,32,100,111,99,116,111,114,32,97,119,97,121]]}
        {"__isset":{"streamId":true,"tupleId":true,"evt":true,"data":true},"StreamId":"default","TupleId":"","Evt":1000,"Data":[[116,104,101,32,99,111,119,32,106,117,109,112,101,100,32,111,118,101,114,32,116,104,101,32,109,111,111,110]]}

    > [AZURE.NOTE] 위의 줄에서 "Data"는 Spout에서 내보낸 문자열이지만 바이트 배열로 저장됩니다. 예를 들어 `[[97,110,32,97,112,112,108,101,32,97,32,100,97,121,32,107,101,101,112,115,32,116,104,101,32,100,111,99,116,111,114,32,97,119,97,121]]`은 "하루 사과 한 알이면 의사가 필요 없다."를 나타냅니다.

###Bolt 만들기

1. **솔루션 탐색기**에서 **WordCount**를 마우스 오른쪽 단추로 클릭하고 **추가 | 새 항목**을 선택합니다. **클래스**를 선택하고 이름으로 **SplitterBolt.cs**를 입력합니다. 마지막으로 **추가**를 클릭합니다. 이 단계에 따라 **CounterBolt.cs**라는 클래스를 추가합니다.

2. **SplitterBolt.cs** 파일을 열고 기존 코드를 다음으로 바꿉니다.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.SCP;
        using System.Threading;

        namespace WordCount
        {
            class SplitterBolt : ISCPBolt
            {
                //Context
                private Context ctx;

                //Constructor
                public SplitterBolt(Context ctx)
                {
                    Context.Logger.Info("Splitter constructor called");
                    //Set context
                    this.ctx = ctx;
                    //Define the schema for the incoming tuples from spout
                    Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
                    //In this case, just a string tuple
                    inputSchema.Add("default", new List<Type>() {typeof (string)});
                    //Define the schema for tuples to be emitted from this bolt
                    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
                    //We are also only emitting a string tuple
                    outputSchema.Add("default", new List<Type>() {typeof (string)});
                    //Declare both incoming and outbound schemas
                    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
                }

                //Get a new instance
                public static SplitterBolt Get(Context ctx, Dictionary<string, Object> parms)
                {
                    return new SplitterBolt(ctx);
                }

                //Process a tuple from the stream
                public void Execute(SCPTuple tuple)
                {
                    Context.Logger.Info("Execute enter");
                    //Get the incomin tuple value
                    string sentence = tuple.GetString(0);
                    //Split it
                    foreach (string word in sentence.Split(' '))
                    {
                        //Emit each word to the outbound stream
                        Context.Logger.Info("Emit: {0}", word);
                        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word));
                    }
                    Context.Logger.Info("Execute exit");
                }
            }
        }

4. **CounterBolt.cs** 파일을 열고 기존 코드를 다음으로 바꿉니다.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.SCP;

        namespace WordCount
        {
            class CounterBolt : ISCPBolt
            {
                //Context
                private Context ctx;

                //Store each word and a count of how many times it has
                //been emitted by the splitter
                private Dictionary<string, int> counts = new Dictionary<string, int>();

                //Constructor
                public CounterBolt(Context ctx)
                {
                    Context.Logger.Info("Counter constructor called");

                    //Set context
                    this.ctx = ctx;
                    //Define the schema for the incoming tuples from spout
                    Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
                    //Just a string, which will contain a word from the splitter
                    inputSchema.Add("default", new List<Type>() { typeof(string) });
                    //Define the schema for tuples to be emitted from this bolt
                    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
                    //In this case, a string (containing a word) and an int (containing the count)
                    outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
                    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
                }

                //Get an instance
                public static CounterBolt Get(Context ctx, Dictionary<string, Object> parms)
                {
                    return new CounterBolt(ctx);
                }

                //Process a tuple from the stream
                public void Execute(SCPTuple tuple)
                {
                    Context.Logger.Info("Execute enter");
                    //Get the word that was emitted from the splitter
                    string word = tuple.GetString(0);
                    //Have we seen this word before?
                    int count = counts.ContainsKey(word) ? counts[word] : 0;
                    //Increment and store the count for this word
                    count++;
                    counts[word] = count;

                    Context.Logger.Info("Emit: {0}, count: {1}", word, count);
                    //Emit the word and the count
                    this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));

                    Context.Logger.Info("Execute exit");
                }
            }
        }

    > [AZURE.NOTE] 이 Bolt는 테스트에 유용한 스트림을 내보냅니다. 실제 솔루션에서는 처리가 끝나면 데이터를 데이터베이스, 큐 또는 기타 영구 저장소에 저장합니다.

3. **LocalTest.cs**를 열고 SplitterBolt 및 CounterBolt에 대한 다음 테스트를 이전 Spout 테스트 블록 앞에 추가합니다.

        //SplitterBolt tests
        {
            LocalContext splitterCtx = LocalContext.Get();
            SplitterBolt splitter = SplitterBolt.Get(splitterCtx, emptyDictionary);
            //Read from the 'stream' emitted by the spout
            splitterCtx.ReadFromFileToMsgQueue("spout.txt");
            List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
            foreach (SCPTuple tuple in batch)
            {
                splitter.Execute(tuple);
            }
            //Store the stream for the next component
            splitterCtx.WriteMsgQueueToFile("splitter.txt");
        }
        //CounterBolt tests
        {
            LocalContext counterCtx = LocalContext.Get();
            CounterBolt counter = CounterBolt.Get(counterCtx, emptyDictionary);
            //Read from the 'stream' emitted by the splitter
            counterCtx.ReadFromFileToMsgQueue("splitter.txt");
            List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
            foreach (SCPTuple tuple in batch)
            {
                counter.Execute(tuple);
            }
            //Store the stream for the next component
            counterCtx.WriteMsgQueueToFile("counter.txt");
        }

4. 솔루션을 실행합니다. 실행이 완료되면 **WordCount\bin\debug** 디렉터리에 다음 파일이 있어야 합니다.

    * **spout.txt** - WordSpout에서 내보낸 데이터
    * **splitter.txt** - SplitterBolt에서 내보낸 데이터
    * **counter.txt** - CounterBolt에서 내보낸 데이터

###클러스터에서 실행할 코드 추가

1. **Program.cs**를 열고 `//Code to run on HDInsight cluster will go here` 줄을 다음으로 바꾸고 나서 프로젝트를 다시 빌드합니다.

        //The component to run
        string compName = args[0];
        //Run the component
        if ("wordspout".Equals(compName))
        {
            //Set the prefix for logging
            System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-Spout");
            //Initialize the runtime
            SCPRuntime.Initialize();
            //Run the plugin (WordSpout)
            SCPRuntime.LaunchPlugin(new newSCPPlugin(WordSpout.Get));
        }
        else if ("splitterbolt".Equals(compName))
        {
            System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-Splitter");
            SCPRuntime.Initialize();
            SCPRuntime.LaunchPlugin(new newSCPPlugin(SplitterBolt.Get));
        }
        else if ("counterbolt".Equals(compName))
        {
            System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-Counter");
            SCPRuntime.Initialize();
            SCPRuntime.LaunchPlugin(new newSCPPlugin(CounterBolt.Get));
        }
        else
        {
            throw new Exception(string.Format("unexpected compName: {0}", compName));
        }

4. **WordCount.spec**라는 새 파일을 만들고 내용으로 다음을 사용합니다. 구성 요소, 클러스터의 여러 노드에서 만들 인스턴스 수, 노드 간 데이터 이동 방식을 포함한 토폴로지를 정의합니다. 이 파일은 [토폴로지 사양 언어](#spec)로 작성됩니다.

        {
          :name "WordCount"
          :topology
            (nontx-topolopy
              "WordCount"

              {
                "spout"

                (spout-spec
                  (scp-spout
                    {
                      "plugin.name" "WordCount.exe"
                      "plugin.args" ["wordspout"]
                      "output.schema" {"default" ["sentence"]}
                    })

                  :p 1)
              }

              {
                "splitter"

                (bolt-spec
                  {
                    "spout" :shuffle
                  }

                  (scp-bolt
                    {
                      "plugin.name" "WordCount.exe"
                      "plugin.args" ["splitterbolt"]
                      "output.schema" {"default" ["word"]}
                    })

                  :p 1)

                "counter"

                (bolt-spec
                  {
                    "splitter" :global
                  }

                  (scp-bolt
                    {
                      "plugin.name" "WordCount.exe"
                      "plugin.args" ["counterbolt"]
                      "output.schema" {"default" ["word" "count"]}
                    })

                  :p 1)
              })

          :config
            {
              "topology.kryo.register" ["[B"]
            }
        }

4. 원격 데스크톱을 사용하여 HDInsight Storm 클러스터에 연결하고 로컬 WordCount 프로젝트에 대한 **bin\debug** 폴더를 HDInsight Storm 클러스터에 복사합니다. 예를 들어 **%storm_home%\examples** 폴더에 복사하고 이름을 **WordCount**로 바꿉니다.

3. **WordCount.spec**를 HDInsight 서버에도 복사합니다. 파일을 **%storm_home%\examples** 디렉터리에 넣습니다.

4. HDInsight Storm 클러스터에서 데스크톱의 **Storm 명령줄** 아이콘을 사용하여 명령줄을 열고 다음 명령을 사용하여 WordCount 토폴로지를 시작합니다.

        bin\runspec examples\WordCount.spec temp examples\WordCount

    이 토폴로지는 **temp**라는 새 폴더를 만들고 WordCount 솔루션에 대한 파일과 사양을 사용하여 **WordCount.jar** 파일을 만듭니다. 이 파일은 나중에 Storm에 제출됩니다.

5. 명령이 완료되면 데스크톱에서 **Storm UI**를 엽니다. **WordCount** 토폴로지가 나열됩니다. **Storm UI**에서 토폴로지를 선택하여 통계를 볼 수 있습니다.

6. 토폴로지를 중지하려면 **Storm UI**에서 **WordCount**를 선택하고 **토폴로지 작업**에서 **중단**을 선택합니다.

##요약

위 단계에서는 Stream Computing Platform을 사용하여 만들어진 기본 단어 개수 토폴로지를 만들고 테스트 및 배포하는 방법을 알아보았습니다. 더 많은 예를 보려면 HDInsight 클러스터에서 **%storm_home%\examples** 디렉터리를 참조하세요. SCP에 대한 자세한 내용은 다음 참조를 참조하세요.

##SCP 참조

###SCP 플러그 인 인터페이스

SCP 응용 프로그램(플러그 인이라고 함)은 Storm 파이프라인에 연결됩니다. 플러그 인은 하나 이상의 다음 인터페이스를 구현하는 .NET 콘솔 응용 프로그램입니다.  

-	**ISCPSpout** - 비트랜잭션 Spout를 구현할 때 사용
- 	**ISCPTxSpout** - 트랜잭션 Spout를 구현할 때 사용
-	**ISCPBolt** - 비트랜잭션 Bolt를 구현할 때 사용
-	**ISCPBatchBolt** - 트랜잭션 Bolt를 구현할 때 사용

####ISCPPlugin

ISCPPlugin은 모든 기타 SCP 인터페이스가 상속하는 기본 인터페이스입니다. 현재는 더미 인터페이스입니다.  

    public interface ISCPPlugin
    {
    }  

####ISCPSpout

ISCPSpout는 비트랜잭션 Spout용 인터페이스입니다.  

    public interface ISCPSpout : ISCPPlugin
    {
        void NextTuple(Dictionary<string, Object> parms);
        void Ack(long seqId, Dictionary<string, Object> parms);
        void Fail(long seqId, Dictionary<string, Object> parms);
    }  

`NextTuple()`이 호출되면 코드가 하나 이상의 튜플을 내보낼 수 있습니다. 내보낼 튜플이 없으면 아무 항목도 내보내지 않고 이 메서드가 반환되어야 합니다.

`Ack()` 및 `Fail()`은 사양 파일에서 승인이 사용하도록 설정된 경우에만 호출됩니다. `seqId`는 승인되거나 실패한 튜플을 식별하는 데 사용됩니다. 비트랜잭션 토폴로지에서 승인이 사용하도록 설정되어 있으면 Spout에서 다음의 내보내기 함수를 사용해야 합니다.  

    public abstract void Emit(string streamId, List<object> values, long seqId);  

비트랜잭션 토폴로지에서 승인이 지원되지 않으면 'Ack()' 및 'Fail()'은 빈 함수로 유지될 수 있습니다.  

이러한 함수에 대한 `parms` 매개 변수는 빈 Dictionary 개체이고 나중에 사용하도록 예약됩니다.  

> [AZURE.NOTE] `NextTuple()`, `Ack()` 및 `Fail()`은 모두 단일 스레드에서 빽빽한 루프로 호출됩니다. 내보낼 튜플이 없으면 10밀리초 등의 짧은 시간 동안 'sleep'을 사용하여 CPU 주기를 절약해 보세요.

####ISCPTxSpout

ISCPTxSpout는 트랜잭션 Spout용 인터페이스입니다.  

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);
        void Ack(long seqId, Dictionary<string, Object> parms);
        void Fail(long seqId, Dictionary<string, Object> parms);
    }  

`NextTx()`가 호출되어 새 트랜잭션이 시작됩니다. `seqId`는 `Ack()` 및 `Fail()`에서도 사용되는 트랜잭션을 식별하는 데 사용됩니다. 재생을 지원하기 위해 `NextTx()`에서 내보낸 데이터는 ZooKeeper에 저장됩니다. ZooKeeper의 저장소 용량은 매우 제한되어 있으므로 트랜잭션 Spout에서 데이터를 대량으로 내보내서는 안 되며 메타데이터만 내보내야 합니다.  

Storm에서는 트랜잭션이 실패하면 자동으로 재생하므로 일반적으로 Fail()을 호출하면 안 됩니다. 그러나 SCP는 트랜잭션 Spout에서 내보낸 메타데이터를 확인할 수 있으면 메타데이터가 잘못된 경우 Fail()을 호출할 수 있습니다.

이러한 함수에 대한 `parms` 매개 변수는 빈 Dictionary 개체이고 나중에 사용하도록 예약됩니다.

> [AZURE.NOTE] `NextTx()`, `Ack()` 및 `Fail()`은 모두 단일 스레드에서 빽빽한 루프로 호출됩니다. 내보낼 튜플이 없으면 10밀리초 등의 짧은 시간 동안 'sleep'을 사용하여 CPU 주기를 절약해 보세요.

####ISCPBolt

ISCPBolt는 비트랜잭션 Bolt용 인터페이스입니다.  

    public interface ISCPBolt : ISCPPlugin
    {
        void Execute(SCPTuple tuple);
    }

새 튜플을 사용할 수 있으면 'Execute()' 함수가 호출되어 해당 튜플을 처리합니다.

####ISCPBatchBolt

ISCPBatchBolt는 트랜잭션 Bolt용 인터페이스입니다.  

    public interface ISCPBatchBolt : ISCPPlugin
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);
    }  

새 튜플을 사용할 수 있으면 'Execute()' 함수가 호출되어 해당 튜플을 처리합니다. 이 트랜잭션이 종료되면 `FinishBatch()`가 호출됩니다.

이러한 함수에 대한 `parms` 매개 변수는 빈 Dictionary 개체이고 나중에 사용하도록 예약됩니다.

> [AZURE.NOTE] `ISCPBatchBolt'를 구현하는 Bolt는 `parms`에서 `StormTxAttempt`를 가져올 수 있습니다. `StormTxAttempt`를 사용하여 튜플이 원래 또는 재생 시도인지를 결정할 수 있습니다. 이 작업은 일반적으로 Commit Bolt에서 수행됩니다. **HelloWorldTx** 예제에서 해당 작업을 확인할 수 있습니다.  

###개체 모델

SCP.NET에서는 개발자가 프로그래밍에 사용할 수 있는 주요 개체의 간단한 집합도 제공합니다.

* `Context` - 응용 프로그램에 대해 실행 중인 환경과 관련된 정보를 제공합니다.

* `StateStore` - 메타데이터 서비스, 단조 시퀀스 생성 및 비대기 조정 기능을 제공합니다. 'StateStore'를 기반으로 하여 분산 잠금, 분산 큐, 장벽 및 트랜잭션 서비스를 비롯한 높은 수준의 분산형 동시성 추상화를 작성할 수 있습니다.

* `SCPRuntime` - 런타임 환경을 초기화하고 Storm에서 솔루션을 실행할 때 플러그 인을 실행합니다.

* `LocalContext` - Visual Studio에서 로컬로 응용 프로그램을 테스트할 때 내보낸 튜플을 로컬 파일로 직렬화 및 역직렬화하는 방법을 제공합니다.

####Context

각 ISCPPlugin 인스턴스(ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt)에는 해당하는 Context 인스턴스가 있습니다. Context에서 제공하는 기능은 다음 두 부분으로 나뉩니다.

* **정적** -  전체 C# 프로세스에서 사용 가능
* **동적** - 특정 인스턴스에서 사용 가능  

**정적 컨텍스트**

* `public static ILogger Logger = null;` - 로깅용으로 제공됩니다.  

* `public static SCPPluginType pluginType;` - C# 프로세스의 플러그 인 유형을 가져옵니다. C# 프로세스가 Java 없이 로컬 테스트 모드에서 실행되면 플러그 인 유형은 "SCP_NET_LOCAL"입니다.

        public enum SCPPluginType
        {
            SCP_NET_LOCAL = 0,
            SCP_NET_SPOUT = 1,
            SCP_NET_BOLT = 2,
            SCP_NET_TX_SPOUT = 3,
            SCP_NET_BATCH_BOLT = 4
        }  

* `public static Config Config { get; set; }` - JVM에서 구성 매개 변수를 가져옵니다. 플러그 인을 초기화할 때 JVM에서 매개 변수가 전달됩니다. `Config`에는 다음 두 사전이 포함됩니다.

    * `public Dictionary<string, Object> stormConf { get; set; }` - Storm에서 정의된 매개 변수를 포함합니다.

    * `public Dictionary<string, Object> pluginConf { get; set; }` - SCP에서 정의된 매개 변수를 포함합니다.

* `public static TopologyContext TopologyContext { get; set; } ` - 토폴로지 컨텍스트를 가져옵니다. 다중 병렬 처리를 사용한 구성 요소에 가장 유용합니다. 토폴로지 컨텍스트에 액세스하는 방법은 다음과 같습니다.

        if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
        {
            Context.Logger.Info("TopologyContext info:");
            TopologyContext topologyContext = Context.TopologyContext;
            Context.Logger.Info("taskId: {0}", topologyContext.GetThisTaskId());
            taskIndex = topologyContext.GetThisTaskIndex();
            Context.Logger.Info("taskIndex: {0}", taskIndex);
            string componentId = topologyContext.GetThisComponentId();
            Context.Logger.Info("componentId: {0}", componentId);
            List<int> componentTasks = topologyContext.GetComponentTasks(componentId);
            Context.Logger.Info("taskNum: {0}", componentTasks.Count);
        }

**동적 컨텍스트**

* `public void DeclareComponentSchema(ComponentStreamSchema schema);` - 스트림에 대한 출력 및 입력 스키마를 선언합니다. 다음 예제에서는 단일 문자열 튜플로 구성된 입력 스키마와 문자열 튜플 및 정수 튜플로 구성된 출력 스키마를 선언합니다.

        this.ctx = context;
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));

* `public abstract void Emit(List<object> values);` - 하나 이상의 튜플을 기본 스트림으로 내보냅니다. 다음 예제에서는 두 튜플을 기본 스트림에 내보냅니다.

        this.ctx.Emit(new Values(word, count));

* `public abstract void Emit(string streamId, List<object> values);` - 하나 이상의 튜플을 지정된 스트림으로 내보냅니다. 다음 예제에서는 두 튜플을 'mystream' 스트림에 내보냅니다.

        this.ctx.Emit("mystream", new Values(word, count));

승인이 사용하도록 설정된 비트랜잭션 Spout 및 Bolt를 사용할 경우 다음을 사용합니다.

* `public abstract void Emit(string streamId, List<object> values, long seqId);` - 하나 이상의 튜플과 시퀀스 식별자를 **Spout에서 ** 지정된 스트림으로 내보냅니다. 다음 예제에서는 튜플과 시퀀스 식별자를 기본 스트림에 내보냅니다.

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(word), lastSeqId);

* `public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);` - 하나 이상의 튜플과 시퀀스 식별자를 **Bolt에서** 지정된 스트림으로 내보냅니다. 내보낸 튜플은 `앵커`로 지정된 수신 튜플에 앵커됩니다. 이를 통해 승인이 수신/발신 튜플의 체인에 따라 파이프라인 흐름을 백업할 수 있습니다. 다음 예제에서는 튜플과 시퀀스 식별자를 기본 스트림에 내보내고 '튜플'에 포함된 수신 튜플에 대해 내보낸 튜플을 앵커합니다.

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word));

* `public abstract void Ack(SCPTuple tuple);` - 튜플을 승인합니다. 원래 튜플을 생성한 Spout의 `ISCPSpout.Ack` 함수를 호출합니다.

* `public abstract void Fail(SCPTuple tuple);` - 튜플을 실패하게 합니다. 원래 튜플을 생성한 Spout의 `ISCPSpout.Fail` 함수를 호출합니다.

####StateStore

SCP 응용 프로그램은 `State` 개체를 사용하여 ZooKeeper에 정보를 영구 보존할 수 있습니다(특히 트랜잭션 토폴로지의 경우). 이렇게 하면 트랜잭션 Spout가 ZooKeeper에서 상태를 검색하고 작동 중단 시 파이프라인을 다시 시작할 수 있습니다.  

`StateStore` 개체는 다음 메서드를 제공합니다.

* `public static StateStore Get(string storePath, string connStr);` - 지정된 경로 및 연결 문자열에 대한 `StateStore`를 가져옵니다.

* `public State Create();` - 이 상태 저장소 인스턴스에서 새 `State` 개체를 만듭니다.

* `public IEnumerable<State> GetUnCommitted();` - 중단된 상태를 제외하고 커밋되지 않은 모든 `State` 개체를 가져옵니다.

* `public IEnumerable<State> States();` - `StateStore`에 있는 모든 'State' 개체를 가져옵니다.


* `public T Get<T>(string info = null);` - `State` 또는 `Registry` 개체를 가져옵니다.

    * `info` - 가져올 `Registry` 이름입니다. `Registry` 개체를 검색할 때만 사용됩니다.

    * `T` - `State` 또는 `Registry` 유형입니다.

* `public IEnumerable<Registry> Commited();` - 커밋된 `State`를 포함하는 `Registry` 개체를 가져옵니다.

* `public IEnumerable<Registry> Aborted();` - 중단된 `State`를 포함하는 `Registry` 개체를 가져옵니다.

* `public State GetState(long stateId)` - 지정된 상태 ID에 대한 `State` 개체를 가져옵니다.

**State**는 다음 메서드를 제공합니다.

* `public void Commit(bool simpleMode = true);` - `State` 개체의 상태를 커밋으로 설정합니다.

    > [AZURE.NOTE] `simpleMode`가 true로 설정되면 ZooKeeper에서 해당 ZNode를 삭제합니다. 그렇지 않은 경우에는 현재 ZNode를 삭제하고 'COMMITTED_PATH'에 새 노드를 추가합니다.

* `public void Abort();` - `State` 개체의 상태를 중단으로 설정합니다.

* `public void PutAttribute<T>(string key, T attribute);` - 지정된 키의 특성 값을 설정합니다.

    * `key` - 이 키는 특성을 설정합니다.

    * `attribute` - 특성 값입니다.

* `public T GetAttribute<T>(string key);` - 지정된 키의 특성 값을 가져옵니다.

####SCPRuntime

SCPRuntime은 다음 메서드를 제공합니다.

* `public static void Initialize();` - SCP 런타임 환경을 초기화합니다. 호출될 경우 C# 프로세스는 JVM에 연결되며 구성 매개 변수와 토폴로지 컨텍스트를 가져옵니다.

* `public static void LaunchPlugin(newSCPPlugin pluginDelegate);` - 메시지 처리 루프를 시작합니다. 이 루프에서 C# 플러그 인은 JVM에서 메시지를 수신하고(튜플 및 제어 신호 포함) 메시지를 처리합니다.

    * `pluginDelegate` - ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt를 구현하는 개체를 반환할 수 있는 대리자입니다.

####LocalContext


* `List<SCPTuple> RecvFromMsgQueue();` - 큐에서 튜플을 수신합니다.

* `void WriteMsgQueueToFile(string filepath, bool append = false);` - 튜플을 파일에 영구 보존합니다.

* `void ReadFromFileToMsgQueue(string filepath);` - 파일에서 튜플을 읽습니다.


###<a id="spec"></a>토폴로지 사양 언어

SCP 토폴로지 사양은 SCP 토폴로지를 설명하고 구성하기 위한 도메인별 언어로, Storm의 [Clojure DSL](http://storm.incubator.apache.org/documentation/Clojure-DSL.html)을 기반으로 합니다.  

다음을 사용하여 토폴로지를 정의합니다.

|새 함수|	매개 변수|	설명
|-------------|-----------|-----------
|**tx-topolopy**|	topology-name<br> spout-map<br> bolt-map|	토폴로지 이름, Spout 정의 맵 및 Bolt 정의 맵을 사용하여 트랜잭션 토폴로지를 정의합니다.
|**scp-tx-spout**|	exec-name<br> args<br> fields|	트랜잭션 Spout를 정의합니다. ***exec-name*** using ***args***를 사용하여 응용 프로그램을 실행합니다.<br><br>***fields***는 Spout의 출력 필드입니다.
|**scp-tx-batch-bolt**|	exec-name<br> args<br> fields| 	트랜잭션 Batch Bolt를 정의합니다. ***exec-name*** using ***args***를 사용하여 응용 프로그램을 실행합니다.<br><br>Fields는 Bolt의 출력 필드입니다.
|**scp-tx-commit-bolt**|	exec-name<br>args<br>fields|	xmfoswortus Committer Bolt를 정의합니다. ***exec-name*** using args를 사용하여 응용 프로그램을 실행합니다.<br><br>***fields***는 Bolt의 출력 필드입니다.
|**nontx-topolopy**|	topology-name<br> spout-map<br>bolt-map|	토폴로지 이름, Spout 정의 맵 및 Bolt 정의 맵을 사용하여 비트랜잭션 토폴로지를 정의합니다.
|**scp-spout**|	exec-name<br>args<br>fields<br>parameters|	비트랜잭션 Spout를 정의합니다. ***exec-name*** using ***args***를 사용하여 응용 프로그램을 실행합니다.<br><br>***fields***는 Spout의 출력 필드입니다.<br><br>***parameters***는 선택 사항이고, 이를 사용하여 "nontransactional.ack.enabled"와 같은 몇몇 매개 변수를 지정합니다.
|**scp-bolt**|	exec-name<br>args<br>fields<br>parameters|	비트랜잭션 Bolt를 정의합니다. ***exec-name*** using ***args***를 사용하여 응용 프로그램을 실행합니다.<br><br>***fields***는 Bolt의 출력 필드입니다.<br><br>***parameters***는 선택 사항이고, 이를 사용하여 "nontransactional.ack.enabled"와 같은 몇몇 매개 변수를 지정합니다.

토폴로지를 정의할 때 사용할 수 있는 키워드는 다음과 같습니다.

|키워드|	설명
|---------|------------
|**:name**|	토폴로지 이름을 정의합니다.
|**:topology**|	위의 함수와 기본 제공 함수를 사용하여 토폴로지를 정의합니다.
|**:p**|	각 Spout 또는 Bolt에 대해 병렬 처리 힌트를 정의합니다.
|**:config**|	구성 매개 변수를 정의하거나 기존 매개 변수를 업데이트합니다.
|**:schema**|	스트림의 스키마를 정의합니다.

질문과 대답

|매개 변수|	설명
|---------|------------
|**"plugin.name"**|	C# 플러그 인의 exe 파일 이름입니다.
|**"plugin.args"**|	플러그 인 인수입니다.
|**"output.schema"**|	출력 스키마입니다.
|**"nontransactional.ack.enabled"**|	비트랜잭션 토폴로지에 대해 승인이 사용하도록 설정되는지 여부입니다.

토폴로지 사양은 HDInsight Storm 클러스터의 **%storm_home%\bin** 디렉터리에 있는 ***runspec*** 명령을 통해 실행할 Storm 클러스터에 직접 제출할 수 있습니다.  

    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
     ex: runSpec examples\HelloWorld\HelloWorld.spec target examples\HelloWorld\Target

> [AZURE.NOTE] ***resource-dir*** 매개 변수는 선택 사항입니다. C# 응용 프로그램을 실행하려는 경우 이 매개 변수를 지정해야 합니다. 이 디렉터리에는 응용 프로그램, 종속성 및 구성이 포함됩니다.  

***classpath*** 매개 변수는 선택 사항입니다. 사양 파일에 Java Spout 또는 Bolt가 포함되는 경우 Java 클래스 경로를 지정하는 데 사용됩니다.  

###기타 기능

####입력 및 출력 스키마 선언

`Emit()`을 호출하면 플랫폼에서는 튜플을 바이트 배열로 직렬화하고 JVM으로 전송합니다. Storm에서 이 튜플을 대상에 전송합니다. 이제 Bolt가 튜플을 수신합니다. C# Bolt의 경우 튜플은 JVM에서 수신되고 다시 원래 유형으로 변환됩니다.

이 직렬화와 역직렬화를 지원하려면 입력과 출력의 스키마를 선언해야 합니다. 스키마는 `Dictionary<string, List<Type>` 개체로 정의됩니다. 여기서 키는 스트림 ID이고 값은 내보낼 튜플의 `Types`입니다. 구성 요소는 여러 스트림을 선언할 수 있습니다.

    public class ComponentStreamSchema
    {
        public Dictionary<string, List<Type>> InputStreamSchema { get; set; }
        public Dictionary<string, List<Type>> OutputStreamSchema { get; set; }
        public ComponentStreamSchema(Dictionary<string, List<Type>> input, Dictionary<string, List<Type>> output)
        {
            InputStreamSchema = input;
            OutputStreamSchema = output;
        }
    }

`Context` 개체는 직렬화/역직렬화에 대한 스키마를 선언하는 데 사용될 수 있는 `DeclareComponentSchema`를 제공합니다.

     public void DeclareComponentSchema(ComponentStreamSchema schema)

> [AZURE.NOTE] 내보낸 튜플이 해당 스트림에 대해 정의된 스키마를 따르는지 확인해야 합니다. 그렇지 않으면 런타임 예외가 발생합니다.  

####여러 스트림 지원

각 스트림을 쓸 대상 `streamId` 매개 변수를 지정하여 `Emit()`을 여러 번 호출하면 여러 스트림으로 내보낼 수 있습니다.

> [AZURE.NOTE] 존재하지 않는 스트림으로 내보내면 런타임 예외가 발생합니다.

####필드 그룹화

Strom의 기본 제공 필드 그룹화는 SCP.NET에서 제대로 작동하지 않습니다. 데이터를 JVM으로 프록시할 때 모든 필드 데이터 형식은 실제로 `byte[]`이고 필드 그룹화는 `byte[]` 개체 해시 코드를 사용하여 그룹화를 수행합니다. `byte[]` 개체 해시 코드는 메모리 내에서 이 개체의 주소이므로 같은 내용을 공유하지만 같은 주소를 공유하지 않는 두 `byte[]` 개체에 대한 그룹화가 잘못됩니다.

이 문제를 해결하려면 사양에서 `scp-field-group`을 사용합니다. 이 항목은 byte[]의 내용을 사용하여 그룹화를 수행합니다. 사양에서 이 항목을 사용하는 예는 다음과 같습니다.  

    (bolt-spec
        {
            "spout_test""" (scp-field-group :non-tx [0,1])
        }
        ...
    )  

이 예제에서는 다음을 수행합니다.

* `scp-field-group` - 사용자 지정 그룹화를 사용합니다.  
* `:tx` 또는 `:non-tx` - 트랜잭션 또는 비트랜잭션인지를 나타냅니다.
* `[0,1]` - 0부터 필드 ID의 해시 집합을 사용합니다.  

####하이브리드 토폴로지

대부분 기본 Storm Spout, Bolt 및 토폴로지는 Java로 구현됩니다. C# 구성 요소를 사용하는 솔루션에서 이러한 구성 요소를 다시 사용할 수 있도록 SCP에서는 사양에서 하이브리드 토폴로지를 만들도록 허용합니다.

* **Java Spout 또는 Bolt** - 사양 파일에서 `scp-spout` 및 `scp-bolt`를 사용하여 Java Spout 및 Bolt를 지정할 수도 있습니다. `microsoft.scp.example.HybridTopology.Generator`의 클래스 이름을 사용하여 Java Spout를 지정하는 방법은 다음과 같습니다.

        (spout-spec
          (microsoft.scp.example.HybridTopology.Generator.)
          :p 1)

* **Java 클래스 경로** - Java Spout 또는 Bolt를 사용할 때 먼저 Spout 또는 Bolt를 JAR 파일로 컴파일해야 합니다. 그리고 **runSpec** 명령을 사용할 때 '-cp' 매개변수를 통해 Java 클래스 경로를 추가합니다. 다음 예제에는 **examples\HybridTopology\java\target** 디렉터리 아래에 있는 JAR 파일이 포함됩니다.  

        bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*  


* **Java와 C# 간의 직렬화 및 역직렬화** - Java와 C# 간에 개체를 직렬화하려면 `CustomizedInteropJSONSerializer`를 사용합니다.

    > [WACOM.NOTE] 현재 `CustomizedInteropJSONSerializer`는 **Java Spout** 및 **C# Bolt**만 지원합니다.

    * 사양 파일에서 Java 구성 요소에 대한 직렬화기를 지정합니다.

            (scp-bolt
              {
                "plugin.name" "HybridTopology.exe"
                "plugin.args" ["displayer"]
                "output.schema" {}
                "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
              })  

    * C# 구성 요소에서 직렬화기를 사용합니다.

            Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
            inputSchema.Add("default", new List<Type>() { typeof(Person) });
            this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
            this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());  

    데이터 형식이 너무 복잡하지 않으면 기본 구현에서 대부분의 경우를 처리할 수 있습니다.  데이터 형식이 너무 복잡하거나 기본 구현의 성능이 요구 사항에 맞지 않을 경우 다음 인터페이스를 사용하여 필요에 맞게 사용자 지정 구현을 만들 수 있습니다.

    **Java의 직렬화 인터페이스**  

            public interface ICustomizedInteropJavaSerializer {
                public void prepare(String[] args);
                public List<ByteBuffer> serialize(List<Object> objectList);
            }  

    **C#의 역직렬화 인터페이스**

            public interface ICustomizedInteropCSharpDeserializer
            {
                List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
            }  

###SCP 호스트 모드

호스트 모드에서는 프로젝트를 DLL로 컴파일하고 **SCPHost.exe**를 사용하여 호스트할 수 있습니다. 프로덕션 설정에서 솔루션을 호스트할 때 이 방법을 사용하는 것이 좋습니다. 호스트 모드를 사용하면 사양 파일에 플러그 인으로 `SCPHost.exe`가 나열됩니다.

HelloWorld 예제에 호스트 모드를 사용한 사양 파일의 예는 다음과 같습니다.

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

* **HelloWorld.dll** - Spout와 Bolt를 포함하는 어셈블리입니다.
* **Scp.App.HelloWorld.Generator** - **HelloWorld.dll**에 포함된 Spout의 클래스 이름입니다.
* **Get** - Spout 인스턴스를 가져오기 위해 호출할 메서드입니다.


##SCP 프로그래밍 예제

SCP에서 작성된 다음 예제 응용 프로그램은 HDInsight Storm 클러스터의 **%storm_home%\examples**에서 찾을 수 있습니다.

* **HelloWorld** - HelloWorld는 SCP.Net의 간단한 예이고, 이 문서의 앞부분에서 사용한 단어 개수 예제와 비슷합니다. 여기에는 **generator**라는 Spout와 **splitter** 및 **counter**라는 Bolt 2개가 포함된 비트랜잭션 토폴로지가 사용됩니다. **generator** Spout는 일부 문장을 임의로 생성하여 **splitter**로 내보냅니다. **splitter** Bolt는 이러한 문장을 단어로 분할한 다음 **counter** Bolt로 단어를 내보냅니다. **counter** Bolt는 사전을 사용하여 각 단어가 나오는 횟수를 기록합니다.

    이 예제에는 **HelloWorld.spec** 및 **HelloWorld_EnableAck.spec**의 두 가지 사양 파일이 있습니다. **HelloWorld_EnableAck.spec**을 사용하면 샘플은 Bolt를 통과하는 튜플을 승인하지만 **splitter**는 몇몇 Bolt를 임의로 실패하게 하여 비트랜잭션 토폴로지에서 실패 처리를 보여 주도록 디자인되어 있습니다.

* **HelloWorldTx** - 트랜잭션 토폴로지를 구현하는 방법의 샘플입니다. 이 샘플에는 **generator**라는 트랜잭션 Spout, **partial-count**라는 Batch Bolt, **count-sum**이라는 Commit Bolt가 포함됩니다. 이 토폴로지에서 사용할 미리 만들어진 세 개의 txt 파일 **DataSource0.txt**, **DataSource1.txt** 및 **DataSource2.txt**도 있습니다.

    각 트랜잭션에서 Spout는 2개 파일을 임의로 선택하여 이 두 파일 이름을 **partial-count** Bolt로 내보냅니다. 그러면 Bolt는 먼저 수신된 튜플에서 파일 이름을 가져온 다음 파일을 열고 해당 파일의 단어 수를 계산합니다.  마지막으로 단어 수를 **count-sum** Bolt로 내보냅니다. **count-sum** Bolt는 총 단어 개수의 요약을 표시합니다.  

    "정확히 한 번" 의미 체계를 적용하려면 **count-sum** Bolt는 재생된 트랜잭션을 처리 중인지를 확인해야 합니다. 이 예제에서는 "count-sum"에 정적 멤버 변수가 있습니다.  

        public static long lastCommittedTxId = -1;  

    Bolt 인스턴스가 만들어지면 입력 매개 변수에서 트랜잭션 시도(`txAttempt`,)를 가져옵니다.  

        public static CountSum Get(Context ctx, Dictionary<string, Object> parms)
        {
            /* for transactional topology, we can get txAttempt from the input parms */
            if (parms.ContainsKey(Constants.STORM_TX_ATTEMPT))
            {
                StormTxAttempt txAttempt = (StormTxAttempt)parms[Constants.STORM_TX_ATTEMPT];
                return new CountSum(ctx, txAttempt);
            }
            else
            {
                throw new Exception("null txAttempt");
            }
        }  

    `FinishBatch()`를 호출할 때 재생된 트랜잭션이 아니면 `lastCommittedTxId`가 업데이트됩니다.  

        public void FinishBatch(Dictionary<string, Object> parms)
        {
            /* judge whether it is a replayed transaction? */
            bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

            if (!replay)
            {
                /* If it is not replayed, update the toalCount and lastCommittedTxId vaule */
                totalCount = totalCount + this.count;
                lastCommittedTxId = this.txAttempt.TxId;
            }
            ... ...
        }  

* **HybridTopology** - 이 토폴로지에는 Java Spout 및 C# Bolt가 포함됩니다. SCP에서 제공하는 기본 직렬화 및 역직렬화 구현을 사용합니다. 자세한 내용은 **%storm_home%examples\HybridTopology\HybridTopology.spec**을 참조하고 토폴로지를 제출할 때 Java 클래스 경로를 지정하는 방법에 대해서는 **SubmitTopology.bat**를 참조하세요.  


[1]: ./media/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application-01.png

<!--HONumber=35.1-->
