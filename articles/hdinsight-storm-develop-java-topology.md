<properties
   pageTitle="HDInsight에서 Apache Storm에 대한 Java 기반 토폴로지 개발 | Azure"
   description="간단한 단어 개수 토폴로지를 만들어 Java에서 Storm 토폴로지를 만드는 방법에 대해 배웁니다."
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

#HDInsight에서 Apache Storm에 대한 Java 기반 토폴로지 개발

Maven을 사용하여 HDInsight에서 Apache Storm에 대한 Java 기반 토폴로지를 만드는 기본 과정에 대해 배웁니다. Maven 및 Java를 사용하여 기본 단어 개수 응용 프로그램을 만드는 과정을 안내합니다. 지침은 Eclipse 사용을 제공하지만 선택에 따라 텍스트 편집기를 사용할 수도 있습니다.

이 문서의 단계를 완료하면 HDInsight에서 Apache Storm에 배포할 수 있는 기본 토폴로지가 생깁니다.

##필수 조건

* <a href="https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html" target="_blank">JDK(Java Developer Kit) 버전 7</a>

* <a href="https://maven.apache.org/download.cgi" target="_blank">Maven</a> - Maven은 Java 프로젝트용 프로젝트 빌드 시스템입니다.

* <a href="http://www.gnu.org/software/emacs/" target="_blank">Emacs<a>, <a href="http://www.sublimetext.com/" target="_blank">Sublime Text</a>, <a href="https://atom.io/" target="_blank">Atom.io</a>, <a href="http://brackets.io/" target="_blank">Brackets.io</a> 또는 메모장과 같은 텍스트 편집기. 또는 <a href="https://eclipse.org/" target="_blank">Eclipse</a>(Luna 버전 이상) 같은 IDE(통합 개발 환경).

	> [AZURE.NOTE] 편집기 또는 IDE에 이 문서에서 다루지 않은 Eclipse와 함께 동작하는 특정 기능이 있을 수 있습니다. 편집 환경 기능에 대한 내용은 사용 중인 제품의 설명서를 참조하세요.

##환경 변수 구성

다음 환경 변수는 Java 및 JDK 설치를 통해 설정할 수 있습니다. 하지만 변수가 존재하며 시스템에 대한 올바른 값을 포함하는지 확인해야 합니다.

* **JAVA_HOME** - JRE(Java runtime environment)가 설치된 디렉터리를 가리킵니다. 예를 들어 Unix 또는 Linux 배포에서는  `/usr/lib/jvm/java-7-oracle`과 유사한 값이어야 합니다. Windows에서는 `c:\Program Files (x86)\Java\jre1.7`과 유사한 값이어야 합니다.

* **PATH** - 다음 경로를 포함해야 합니다.

	* **JAVA_HOME** 또는 그와 동등한 경로

	* **JAVA_HOME\bin** 또는 그와 동등한 경로

	* Maven이 설치된 디렉터리

##새 Maven 프로젝트 만들기

명령줄에서 **WordCount**라는 새 Maven을 만들려면 다음을 참조하세요.

	mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

이는 기본 Maven 프로젝트를 포함하는 현재 위치에 **WordCount**라는 새 디렉터리를 만듭니다.

**WordCount** 디렉터리는 다음 항목을 포함합니다.

* **pom.xml** - Maven 프로젝트에 대한 설정을 포함합니다.

* **src\main\java\com\microsoft\example** - 응용 프로그램 코드를 포함합니다.

* **src\test\java\com\microsoft\example** - 응용 프로그램에 대한 테스트를 포함합니다. 이 예제에서 테스트는 만들지 않습니다.

###예제 코드를 제거합니다.

스크래치로부터 응용 프로그램을 만들기 때문에 생성된 테스트 및 응용 프로그램 필드는 삭제합니다.

*  **src\test\java\com\microsoft\example\AppTest.java**

*  **src\main\java\com\microsoft\example\App.java**

##종속성 추가

이는 Storm 토폴로지이므로 Storm 구성 요소에 대한 종속성을 추가해야 합니다. **pom.xml**을 열고 **&lt;dependencies>** 섹션에 다음을 추가합니다.

	<dependency>
	  <groupId>org.apache.storm</groupId>
	  <artifactId>storm-core</artifactId>
	  <version>0.9.2-incubating</version>
	  <!-- keep storm out of the jar-with-dependencies -->
	  <scope>provided</scope>
	</dependency>

컴파일 시간에 Maven 리포지토리에서 **storm-core**를 찾기 위해 Maven은 이 정보를 사용합니다. 먼저 로컬 컴퓨터의 리포지토리에서 찾습니다. 파일이 없으면 공개 Maven 리포지토리에서 이를 다운로드하여 로컬 리포지토리에 저장합니다.

> [AZURE.NOTE] 추가한 섹션에서 `<scope>provided</scope>` 줄을 확인합니다. 이는 Maven에 만든 Jar 파일에서 storm-core을 제외하도록 요청합니다. 시스템을 통해 제공되기 때문입니다. 이를 통해 만든 패키지를 좀 더 작게 할 수 있으며 이는 HDInsight에서 Storm에 포함된 storm-core 비트를 사용합니다.

##빌드 구성

Maven 플러그인을 사용하면 프로젝트를 컴파일하는 방법 또는 jar 파일로 패키지하는 방법과 같은 프로젝트 빌드 단계를 사용자 지정할 수 있습니다. **pom.xml**을 열고 `</project>` 줄 바로 위에 다음을 추가합니다.

	<build>
	  <plugins>
	  </plugins>
	</build>

이 섹션은 플러그 및 다른 빌드 구성 옵션을 추가하는 데 사용됩니다.

###플러그인 추가

Storm 토폴로지의 경우 <a href="http://mojo.codehaus.org/exec-maven-plugin/" target="_blank">Exec 플러그 인</a>을 사용하면 개발 환경에서 로컬로 토폴로지를 쉽게 실행할 수 있으므로 유용합니다. exec 플러그 인을 추가하려면 **pom.xml**의 `<plugins>` 섹션에 다음을 추가합니다.

	<plugin>
      <groupId>org.codehaus.mojo</groupId>
      <artifactId>exec-maven-plugin</artifactId>
      <executions>
        <execution>
        <goals>
          <goal>exec</goal>
        </goals>
        </execution>
      </executions>
      <configuration>
        <executable>java</executable>
        <includeProjectDependencies>true</includeProjectDependencies>
        <includePluginDependencies>false</includePluginDependencies>
        <classpathScope>compile</classpathScope>
        <mainClass>${storm.topology}</mainClass>
      </configuration>
    </plugin>

다른 유용한 플러그 인은 컴파일 옵션을 변경하는 데 사용되는 <a href="http://maven.apache.org/plugins/maven-compiler-plugin/" target="_blank">컴파일러 플러그 인</a>입니다. 이 플러그인이 필요한 주요한 이유는 사용자의 응용 프로그램에 대한 소스 및 타겟에 Maven이 사용하는 Java 버전을 변경하기 위해서입니다. 1.7이 필요합니다.

Compiler 플러그 인을 포함하고 원본 및 대상 버전을 1.7로 설정하려면 **pom.xml**의 `<plugins>` 섹션에 다음을 추가합니다.

	<plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <configuration>
        <source>1.7</source>
        <target>1.7</target>
      </configuration>
    </plugin>

##토폴로지 만들기

Java 기반 Storm 토폴로지는 사용자가 작성자이거나 종속성으로 참조되는 세 개의 구성 요소로 이루어져 있습니다.

* **Spout** - 외부 소스에서 데이터를 읽고 데이터의 스트림을 토폴로지로 내보냅니다.

* **Bolt** - spout 또는 다른 bolt가 내보낸 스트림에서 처리를 수행하고 하나 이상의 스트림을 내보냅니다.

* **토폴로지** - spout 및 bolt 배열 방식을 정의하고 토폴로지에 대한 진입점을 제공합니다.

###Spout 만들기

외부 데이터 소스 설정에 대한 요구를 줄이기 위해 다음 spout가 임의의 문장을 내보냅니다. 이는 Storm-Starter 예제(<a href="https://github.com/apache/storm/blob/master/examples/storm-starter/" target="_blank">https://github.com/apache/storm/blob/master/examples/storm-starter/</a>)와 함께 제공된 spout의 수정된 버전입니다.

> [AZURE.NOTE] 외부 데이터 소스에서 읽는 spout의 예는 다음 예제 중 하나를 참조하세요.
>
> * <a href="https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java" target="_blank">TwitterSampleSpout</a> - Twitter에서 읽는 예제 spout
>
> * <a href="https://github.com/apache/storm/tree/master/external/storm-kafka" target="_blank">Storm Kafka</a> - Kafka에서 읽는 spout

spout의 경우, **src\main\java\com\microsoft\example** 디렉터리에 **RandomSentenceSpout.java**라는 새 파일을 만들고 내용은 다음과 같습니다.

    /**
     * Licensed to the Apache Software Foundation (ASF) under one
     * or more contributor license agreements.  See the NOTICE file
     * distributed with this work for additional information
     * regarding copyright ownership.  The ASF licenses this file
     * to you under the Apache License, Version 2.0 (the
     * "License"); you may not use this file except in compliance
     * with the License.  You may obtain a copy of the License at
     *
     * http://www.apache.org/licenses/LICENSE-2.0
     *
     * Unless required by applicable law or agreed to in writing, software
     * distributed under the License is distributed on an "AS IS" BASIS,
     * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
     * See the License for the specific language governing permissions and
     * limitations under the License.
     */

     /**
      * Original is available at https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/storm/starter/spout/RandomSentenceSpout.java
      */

    package com.microsoft.example;

    import backtype.storm.spout.SpoutOutputCollector;
    import backtype.storm.task.TopologyContext;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseRichSpout;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Values;
    import backtype.storm.utils.Utils;

    import java.util.Map;
    import java.util.Random;

    //This spout randomly emits sentences
    public class RandomSentenceSpout extends BaseRichSpout {
      //Collector used to emit output
      SpoutOutputCollector _collector;
      //Used to generate a random number
      Random _rand;

      //Open is called when an instance of the class is created
      @Override
      public void open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
      //Set the instance collector to the one passed in
        _collector = collector;
        //For randomness
        _rand = new Random();
      }

      //Emit data to the stream
      @Override
      public void nextTuple() {
      //Sleep for a bit
        Utils.sleep(100);
        //The sentences that will be randomly emitted
        String[] sentences = new String[]{ "the cow jumped over the moon", "an apple a day keeps the doctor away",
            "four score and seven years ago", "snow white and the seven dwarfs", "i am at two with nature" };
        //Randomly pick a sentence
        String sentence = sentences[_rand.nextInt(sentences.length)];
        //Emit the sentence
        _collector.emit(new Values(sentence));
      }

      //Ack is not implemented since this is a basic example
      @Override
      public void ack(Object id) {
      }

      //Fail is not implemented since this is a basic example
      @Override
      public void fail(Object id) {
      }

      //Declare the output fields. In this case, an sentence
      @Override
      public void declareOutputFields(OutputFieldsDeclarer declarer) {
        declarer.declare(new Fields("sentence"));
      }
    }

코드 주석을 읽어보면 이 spout가 어떻게 동작하는지 이해할 수 있습니다.

> [AZURE.NOTE] 이 토폴로지는 하나의 spout만 사용하지만 다른 토폴로지는 다른 소스에서 해당 토폴로지로 데이터를 피드하는 여러 spout를 사용할 수 있습니다.

###Bolt 만들기

Bolt는 데이터 처리를 다룹니다. 이 토폴로지의 경우, 다음과 같이 2가지입니다.

* **SplitSentence** - RandomSentenceSpout를 통해 내보낸 문장을 개별 단어로 분리합니다.

* **WordCount** - 각각의 단어가 발생한 횟수를 계산합니다.

> [AZURE.NOTE] Bolt는 계산, 지속성, 외부 구성 요소에 말하기 등 문자 그대로 아무 것도 할 수 없습니다.

새로운 두 파일, **SplitSentence.java** 및 **WordCount.Java**를 **src\main\java\com\microsoft\example** 디렉터리에 만듭니다. 파일의 내용으로 다음을 사용합니다.

**SplitSentence**

    package com.microsoft.example;

    import java.text.BreakIterator;

    import backtype.storm.topology.BasicOutputCollector;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseBasicBolt;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Tuple;
    import backtype.storm.tuple.Values;

    //There are a variety of bolt types. In this case, we use BaseBasicBolt
    public class SplitSentence extends BaseBasicBolt {

      //Execute is called to process tuples
      @Override
      public void execute(Tuple tuple, BasicOutputCollector collector) {
        //Get the sentence content from the tuple
        String sentence = tuple.getString(0);
        //An iterator to get each word
        BreakIterator boundary=BreakIterator.getWordInstance();
        //Give the iterator the sentence
        boundary.setText(sentence);
        //Find the beginning first word
        int start=boundary.first();
        //Iterate over each word and emit it to the output stream
        for (int end=boundary.next(); end != BreakIterator.DONE; start=end, end=boundary.next()) {
          //get the word
          String word=sentence.substring(start,end);
          //If a word is whitespace characters, replace it with empty
          word=word.replaceAll("\\s+","");
          //if it's an actual word, emit it
          if (!word.equals("")) {
            collector.emit(new Values(word));
          }
        }
      }

      //Declare that emitted tuples will contain a word field
      @Override
      public void declareOutputFields(OutputFieldsDeclarer declarer) {
        declarer.declare(new Fields("word"));
      }
    }

**WordCount**

    package com.microsoft.example;

    import java.util.HashMap;
    import java.util.Map;

    import backtype.storm.topology.BasicOutputCollector;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseBasicBolt;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Tuple;
    import backtype.storm.tuple.Values;

    //There are a variety of bolt types. In this case, we use BaseBasicBolt
    public class WordCount extends BaseBasicBolt {
      //For holding words and counts
        Map<String, Integer> counts = new HashMap<String, Integer>();

        //execute is called to process tuples
        @Override
        public void execute(Tuple tuple, BasicOutputCollector collector) {
          //Get the word contents from the tuple
          String word = tuple.getString(0);
          //Have we counted any already?
          Integer count = counts.get(word);
          if (count == null)
            count = 0;
          //Increment the count and store it
          count++;
          counts.put(word, count);
          //Emit the word and the current count
          collector.emit(new Values(word, count));
        }

        //Declare that we will emit a tuple containing two fields; word and count
        @Override
        public void declareOutputFields(OutputFieldsDeclarer declarer) {
          declarer.declare(new Fields("word", "count"));
        }
      }

코드 주석을 읽어보면 각각의 볼트가 어떻게 동작하는지 이해할 수 있습니다.

###토폴로지 만들기

토폴로지는 spout 및 bolt를 그래프로 묶습니다. 이 그래프는 구성 요소 사이의 데이터 흐름 방식을 정의합니다. Storm이 클러스터 내에서 구성 요소의 인스턴스를 만들 대 사용하는 병렬 처리 힌트도 제공합니다.

다음은 이 토폴로지에 대한 구성 요소 그래프의 기본 다이어그램입니다.

![diagram showing the spouts and bolts arrangement](./media/hdinsight-storm-develop-java-topology/wordcount-topology.png)

토폴로지를 구현하려면 **WordCountTopology.java**라는 새 파일을 **src\main\java\com\microsoft\example** 디렉터리에 만듭니다. 파일의 내용으로 다음을 사용합니다.

	package com.microsoft.example;

    import backtype.storm.Config;
    import backtype.storm.LocalCluster;
    import backtype.storm.StormSubmitter;
    import backtype.storm.topology.TopologyBuilder;
    import backtype.storm.tuple.Fields;

    import com.microsoft.example.RandomSentenceSpout;

    public class WordCountTopology {

      //Entry point for the topology
      public static void main(String[] args) throws Exception {
      //Used to build the topology
        TopologyBuilder builder = new TopologyBuilder();
        //Add the spout, with a name of 'spout'
        //and parallelism hint of 5 executors
        builder.setSpout("spout", new RandomSentenceSpout(), 5);
        //Add the SplitSentence bolt, with a name of 'split'
        //and parallelism hint of 8 executors
        //shufflegrouping subscribes to the spout, and equally distributes
        //tuples (sentences) across instances of the SplitSentence bolt
        builder.setBolt("split", new SplitSentence(), 8).shuffleGrouping("spout");
        //Add the counter, with a name of 'count'
        //and parallelism hint of 12 executors
        //fieldsgrouping subscribes to the split bolt, and
        //ensures that the same word is sent to the same instance (group by field 'word')
        builder.setBolt("count", new WordCount(), 12).fieldsGrouping("split", new Fields("word"));

        //new configuration
        Config conf = new Config();
        conf.setDebug(true);

        //If there are arguments, we are running on a cluster
        if (args != null && args.length > 0) {
          //parallelism hint to set the number of workers
          conf.setNumWorkers(3);
          //submit the topology
          StormSubmitter.submitTopology(args[0], conf, builder.createTopology());
        }
        //Otherwise, we are running locally
        else {
          //Cap the maximum number of executors that can be spawned
          //for a component to 3
          conf.setMaxTaskParallelism(3);
          //LocalCluster is used to run locally
          LocalCluster cluster = new LocalCluster();
          //submit the topology
          cluster.submitTopology("word-count", conf, builder.createTopology());
          //sleep
          Thread.sleep(10000);
          //shut down the cluster
          cluster.shutdown();
        }
      }
    }

코드 주석을 읽어보면 토폴로지가 정의되고 클러스터로 제출되는 방식을 이해할 수 있습니다.

##로컬에서 토폴로지 테스트

파일을 저장한 후 다음 명령을 사용하여 토폴로지를 로컬로 테스트할 수 있습니다.

	mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology

실행할 때 토폴로지가 시작 정보를 표시한 다음, 문장을 spout에서 내보낸 다음 bolt로 처리했기 때문에 아래와 유사한 줄을 표시하기 시작합니다.

    15398 [Thread-16-split] INFO  backtype.storm.daemon.executor - Processing received message source: spout:10, stream: default, id: {}, [an apple a day keeps thedoctor away]]
    15398 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [an]
    15399 [Thread-10-count] INFO  backtype.storm.daemon.executor - Processing received message source: split:6, stream: default, id: {}, [an]
    15399 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [apple]
    15400 [Thread-8-count] INFO  backtype.storm.daemon.executor - Processing received message source: split:6, stream: default, id: {}, [apple]
    15400 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [a]
    15399 [Thread-10-count] INFO  backtype.storm.daemon.task - Emitting: count default [an, 53]
    15400 [Thread-12-count] INFO  backtype.storm.daemon.executor - Processing received message source: split:6, stream: default, id: {}, [a]
    15400 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [day]
    15400 [Thread-8-count] INFO  backtype.storm.daemon.task - Emitting: count default [apple, 53]
    15401 [Thread-10-count] INFO  backtype.storm.daemon.executor - Processing received message source: split:6, stream: default, id: {}, [day]
    15401 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [keeps]
    15401 [Thread-12-count] INFO  backtype.storm.daemon.task - Emitting: count default [a, 53]

이 출력에서 볼 수 있듯이 다음과 같이 발행합니다.

1. Spout가 "an apple a day keeps the doctor away"를 내보냅니다.

2. Split bolt가 문장에서 개별 단어 내보내기를 시작합니다.

3. Count bolt가 각각의 단어 및 내보낸 횟수를 내보내기 시작합니다.

count bolt가 내보낸 데이터를 보면 'apple'을 53번 내보냈습니다. 동일한 문장을 계속해서 임의로 내보내고 횟수는 재설정되지 않기 때문에 토폴로지가 실행되는 동안 횟수는 계속해서 증가합니다.

##Trident

Trident는 Storm이 지원하는 높은 수준의 추상화이며 상태 저장 처리를 지원합니다. Trident의 주요 이점은 토폴로지가 입력하는 모든 메시지가 한 번만 처리된다는 점입니다. 해당 메시지를 한 번 이상 처리하는 원시 Java 토폴로지에서는 이루기 어렵습니다. bolt를 만드는 대신 사용할 수 있는 기본 제공 구성 요소와 같은 다른 차이점도 있습니다. 사실 bolt는 필터, 프로젝션 및 함수와 같이 덜 일반적인 구성 요소로 완전히 대체됩니다.

Trident 응용 프로그램은 위와 동일한 기본 단계를 거치는 Maven 프로젝트를 사용하여 만들 수 있으며 코드만 다릅니다.

Trident에 대한 자세한 내용은 <a href="http://storm.apache.org/documentation/Trident-API-Overview.html" target="_blank">Trident API 개요</a>를 참조하세요.

Trident 응용 프로그램의 예제는 [HDInsight에서 Apache Storm을 사용하는 Twitter 추세 항목]을 참조하세요.(../hdinsight-storm-twitter-trending/)

##다음 단계

Java를 사용하여 Storm 토폴로지를 만드는 방법을 배웠으므로 이제 다음으로 이동합니다.

* [HDInsight에서 Apache Storm 토폴로지 배포 및 관리](../hdinsight-storm-deploy-monitor-topology/)

* [Visual Studio를 사용하여 HDInsight에서 Apache Storm에 대한 C# 토폴로지 개발](../hdinsight-storm-develop-csharp-visual-studio-topology/)

* [HDInsight에서 Apache Storm을 사용하는 Twitter 추세 항목 분석](../hdinsight-storm-twitter-trending)

<!--HONumber=47-->
