---
title: Apache Storm 예제 Java 토폴로지 - Azure HDInsight
description: 예제 단어 개수 토폴로지를 만들어 Java에서 Apache Storm 토폴로지를 만드는 방법에 대해 배웁니다.
author: hrasheed-msft
ms.reviewer: jasonh
keywords: apache storm,apache storm example,storm java,storm topology example
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.openlocfilehash: 89cee70c9d7c5dffdb3078756cf4fa94d7cd1a9a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078217"
---
# <a name="create-an-apache-storm-topology-in-java"></a>Java에서 Apache Storm 토폴로지 만들기

[Apache Storm](https://storm.apache.org/)에 대한 Java 기반 토폴로지를 만드는 방법을 알아봅니다. 여기에서 단어 개수 응용 프로그램을 구현 하는 Storm 토폴로지를 만들 있습니다. [Apache Maven](https://maven.apache.org/)을 사용하여 프로젝트를 빌드하고 패키징합니다. 사용 하 여 토폴로지를 정의 하는 방법을 차례로 합니다 [Apache Storm Flux](https://storm.apache.org/releases/2.0.0/flux.html) 프레임 워크입니다.

이 문서의 단계를 완료한 후에 HDInsight에서 Apache Storm에 토폴로지를 배포할 수 있습니다.

> [!NOTE]  
> 이 문서에서 만든 Storm 토폴로지 예제의 전체 버전은 [https://github.com/Azure-Samples/hdinsight-java-storm-wordcount](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount)에서 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* [JDK(Java Developer Kit) 버전 8](https://aka.ms/azure-jdks)

* Apache에 따라 올바르게 [설치된](https://maven.apache.org/install.html) [Apache Maven](https://maven.apache.org/download.cgi)  Maven은 Java 프로젝트용 프로젝트 빌드 시스템입니다.

## <a name="test-environment"></a>테스트 환경
이 문서에 사용 되는 환경에서 Windows 10을 실행 하는 컴퓨터.  명령 프롬프트에서 실행 된 명령 및 다양 한 파일을 메모장으로 편집 합니다.

명령 프롬프트에서 작업 환경을 만들려면 아래 명령을 입력 합니다.

```cmd
mkdir C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Maven 프로젝트 만들기

라는 Maven 프로젝트를 만들려면 다음 명령을 입력 **WordCount**:

```cmd
mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

cd WordCount
mkdir resources
```

이 명령은 기본 Maven 프로젝트를 포함하는 현재 위치에 `WordCount`라는 디렉터리를 만듭니다. 현재 작업 디렉터리를 변경 하는 두 번째 명령은 `WordCount`합니다. 세 번째 명령은 새 디렉터리를 만드는 `resources`, 나중에 사용 됩니다.  `WordCount` 디렉터리에는 다음과 같은 항목이 포함됩니다.

* `pom.xml`: Maven 프로젝트에 대한 설정을 포함합니다.
* `src\main\java\com\microsoft\example`: 애플리케이션 코드를 포함합니다.
* `src\test\java\com\microsoft\example`: 애플리케이션에 대한 테스트를 포함합니다.  

### <a name="remove-the-generated-example-code"></a>생성된 예제 코드 제거

생성 된 테스트 및 응용 프로그램 파일을 삭제 `AppTest.java`, 및 `App.java` 아래 명령을 입력 하 여:

```cmd
DEL src\main\java\com\microsoft\example\App.java
DEL src\test\java\com\microsoft\example\AppTest.java
```

## <a name="add-maven-repositories"></a>Maven 리포지토리 추가

HDInsight는 HDP(Hortonworks Data Platform)를 기반으로 하므로 Hortonworks 리포지토리를 사용하여 Apache Storm 프로젝트에 대한 종속성을 다운로드하는 것이 좋습니다.  

열기 `pom.xml` 아래 명령을 입력 하 여:

```cmd
notepad pom.xml
```

추가한 후 다음 XML은 `<url> https://maven.apache.org</url>` 줄:

```xml
<repositories>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPReleases</id>
        <name>HDP Releases</name>
        <url>https://repo.hortonworks.com/content/repositories/releases/</url>
        <layout>default</layout>
    </repository>
    <repository>
        <releases>
            <enabled>true</enabled>
            <updatePolicy>always</updatePolicy>
            <checksumPolicy>warn</checksumPolicy>
        </releases>
        <snapshots>
            <enabled>false</enabled>
            <updatePolicy>never</updatePolicy>
            <checksumPolicy>fail</checksumPolicy>
        </snapshots>
        <id>HDPJetty</id>
        <name>Hadoop Jetty</name>
        <url>https://repo.hortonworks.com/content/repositories/jetty-hadoop/</url>
        <layout>default</layout>
    </repository>
</repositories>
```

## <a name="add-properties"></a>속성 추가

Maven을 사용하면 속성이라고 하는 프로젝트 수준 값을 정의할 수 있습니다. `pom.xml`, 한 후 다음 텍스트를 추가 합니다 `</repositories>` 줄:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--
    This is a version of Storm from the Hortonworks repository that is compatible with HDInsight 3.6.
    -->
    <storm.version>1.1.0.2.6.1.9-1</storm.version>
</properties>
```

이제 `pom.xml`의 다른 섹션에서 이 값을 사용할 수 있습니다. 예를 들어 Storm 구성 요소의 버전을 지정할 때 값을 하드 코딩하는 대신 `${storm.version}`을 사용할 수 있습니다.

## <a name="add-dependencies"></a>종속성 추가

Storm 구성 요소에 대한 종속성을 추가합니다. `pom.xml`에서 다음 텍스트를 추가 합니다 `<dependencies>` 섹션:

```xml
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-core</artifactId>
    <version>${storm.version}</version>
    <!-- keep storm out of the jar-with-dependencies -->
    <scope>provided</scope>
</dependency>
```

컴파일 시간에 Maven은 이 정보를 사용하여 Maven 리포지토리에서 `storm-core`를 찾습니다. 먼저 로컬 컴퓨터의 리포지토리에서 찾습니다. 파일이 없으면 Maven은 공개 Maven 리포지토리에서 파일을 다운로드하여 로컬 리포지토리에 저장합니다.

> [!NOTE]  
> 이 섹션에서 `<scope>provided</scope>` 줄을 확인합니다. 이 설정은 Maven이 만든 JAR 파일에서 **storm-core**를 제외하도록 요청합니다. 시스템을 통해 제공되기 때문입니다.

## <a name="build-configuration"></a>빌드 구성

Maven 플러그 인을 사용하면 프로젝트의 빌드 단계를 사용자 지정할 수 있습니다. 예를 들어 프로젝트 컴파일 방법 또는 JAR 파일로 패키지하는 방법입니다. `pom.xml`, 바로 위에 다음 텍스트를 추가 합니다 `</project>` 줄.

```xml
<build>
    <plugins>
    </plugins>
    <resources>
    </resources>
</build>
```

이 섹션은 플러그 인, 리소스 및 다른 빌드 구성 옵션을 추가하는 데 사용됩니다. 전체 참조는 `pom.xml` 파일을 참조 하세요 [ https://maven.apache.org/pom.html ](https://maven.apache.org/pom.html)합니다.

### <a name="add-plug-ins"></a>플러그 인 추가

* **Exec Maven 플러그 인**

    Java에서 구현된 Apache Storm 토폴로지의 경우 [Exec Maven 플러그 인](https://www.mojohaus.org/exec-maven-plugin/)을 사용하면 개발 환경에서 토폴로지를 로컬에서 쉽게 실행할 수 있어 유용합니다. Exec Maven 플러그 인을 추가하려면 `pom.xml` 파일의 `<plugins>` 섹션에 다음을 추가합니다.
    
    ```xml
    <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
        <version>1.6.0</version>
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
            <cleanupDaemonThreads>false</cleanupDaemonThreads> 
        </configuration>
    </plugin>
    ```

* **Apache Maven Compiler 플러그 인**

    다른 유용한 플러그 인은 컴파일 옵션을 변경하는 데 사용되는 [Apache Maven 컴파일러 플러그 인](https://maven.apache.org/plugins/maven-compiler-plugin/)입니다. 원본 및 대상 응용 프로그램에 대 한 Maven이 사용 하는 Java 버전을 변경 합니다.
    
  * HDInsight __3.4 이하__의 경우 원본과 대상의 Java 버전을 __1.7__로 설정합니다.
    
  * HDInsight __3.5__의 경우 원본과 대상의 Java 버전을 __1.8__로 설정합니다.
    
    Apache Maven Compiler 플러그 인을 포함하려면 `pom.xml` 파일의 `<plugins>` 섹션에 다음 텍스트를 추가합니다. 이 예에서는 1.8을 지정하므로 대상 HDInsight 버전은 3.5가 됩니다.
    
    ```xml
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>3.3</version>
      <configuration>
      <source>1.8</source>
      <target>1.8</target>
      </configuration>
    </plugin>
    ```

### <a name="configure-resources"></a>리소스 구성

리소스 섹션을 사용하면 토폴로지에 구성 요소에 필요한 구성 파일과 같은 비코드 리소스를 포함할 수 있습니다. 예를 들어에 다음 텍스트를 추가 합니다 `<resources>` 부분을 `pom.xml` 파일.

```xml
<resource>
    <directory>${basedir}/resources</directory>
    <filtering>false</filtering>
    <includes>
        <include>log4j2.xml</include>
    </includes>
</resource>
```

이 예제는 프로젝트의 루트에 있는 리소스 디렉터리(`${basedir}`)를 리소스를 포함하는 위치로 추가하고 `log4j2.xml`이라는 파일을 포함합니다. 이 파일은 토폴로지에서 기록하는 정보를 구성하는 데 사용됩니다.

## <a name="create-the-topology"></a>토폴로지 만들기

Java 기반 Apache Storm 토폴로지는 사용자가 작성자이거나 종속성으로 참조되는 세 개의 구성 요소로 이루어져 있습니다.

* **Spout**: 외부 소스에서 데이터를 읽고 데이터의 스트림을 토폴로지로 내보냅니다.

* **Bolt**: Spout 또는 다른 Bolt가 내보낸 스트림에서 처리를 수행하고 하나 이상의 스트림을 내보냅니다.

* **토폴로지**: Spout 및 Bolt 배열 방식을 정의하고 토폴로지에 대한 진입점을 제공합니다.

### <a name="create-the-spout"></a>Spout 만들기

외부 데이터 소스 설정에 대한 요구를 줄이기 위해 다음 spout가 임의의 문장을 내보냅니다. 이는 [Storm-Starter 예제](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter)와 함께 제공된 Spout의 수정된 버전입니다.  이 토폴로지는 하나의 spout만 사용하지만 다른 토폴로지는 다른 소스에서 해당 토폴로지로 데이터를 피드하는 여러 spout를 사용할 수 있습니다.

만들고 새 파일을 열고 아래 명령을 입력 `RandomSentenceSpout.java`:

```cmd
notepad src\main\java\com\microsoft\example\RandomSentenceSpout.java
```

복사 하 고 새 파일에 다음 java 코드를 붙여 넣습니다.  그런 다음 파일을 닫습니다.

```java
package com.microsoft.example;

import org.apache.storm.spout.SpoutOutputCollector;
import org.apache.storm.task.TopologyContext;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseRichSpout;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Values;
import org.apache.storm.utils.Utils;

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
    //The sentences that are randomly emitted
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
```

> [!NOTE]  
> 외부 데이터 소스에서 읽는 Spout의 예는 다음 예제 중 하나를 참조하세요.
>
> * [TwitterSampleSPout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java): Twitter에서 읽는 예제 Spout입니다.
> * [Storm-Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka): Kafka에서 읽는 Spout입니다.


### <a name="create-the-bolts"></a>Bolt 만들기

Bolt는 데이터 처리를 다룹니다. Bolt는 계산, 지속성, 외부 구성 요소에 말하기 등 모든 작업을 수행할 수 있습니다. 이 토폴로지는 두 개의 bolt를 사용합니다.

* **SplitSentence**: **RandomSentenceSpout**를 통해 내보낸 문장을 개별 단어로 분리합니다.

* **WordCount**: 각각의 단어가 발생한 횟수를 계산합니다.


#### <a name="splitsentence"></a>SplitSentence

만들고 새 파일을 열고 아래 명령을 입력 `SplitSentence.java`:

```cmd
notepad src\main\java\com\microsoft\example\SplitSentence.java
```

복사 하 고 새 파일에 다음 java 코드를 붙여 넣습니다.  그런 다음 파일을 닫습니다.

```java
package com.microsoft.example;

import java.text.BreakIterator;

import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;

//There are a variety of bolt types. In this case, use BaseBasicBolt
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

  //Declare that emitted tuples contain a word field
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word"));
  }
}
```

#### <a name="wordcount"></a>WordCount

만들고 새 파일을 열고 아래 명령을 입력 `WordCount.java`:

```cmd
notepad src\main\java\com\microsoft\example\WordCount.java
```

복사 하 고 새 파일에 다음 java 코드를 붙여 넣습니다.  그런 다음 파일을 닫습니다.

```java
package com.microsoft.example;

import java.util.HashMap;
import java.util.Map;
import java.util.Iterator;

import org.apache.storm.Constants;
import org.apache.storm.topology.BasicOutputCollector;
import org.apache.storm.topology.OutputFieldsDeclarer;
import org.apache.storm.topology.base.BaseBasicBolt;
import org.apache.storm.tuple.Fields;
import org.apache.storm.tuple.Tuple;
import org.apache.storm.tuple.Values;
import org.apache.storm.Config;

// For logging
import org.apache.logging.log4j.Logger;
import org.apache.logging.log4j.LogManager;

//There are a variety of bolt types. In this case, use BaseBasicBolt
public class WordCount extends BaseBasicBolt {
  //Create logger for this class
  private static final Logger logger = LogManager.getLogger(WordCount.class);
  //For holding words and counts
  Map<String, Integer> counts = new HashMap<String, Integer>();
  //How often to emit a count of words
  private Integer emitFrequency;

  // Default constructor
  public WordCount() {
      emitFrequency=5; // Default to 60 seconds
  }

  // Constructor that sets emit frequency
  public WordCount(Integer frequency) {
      emitFrequency=frequency;
  }

  //Configure frequency of tick tuples for this bolt
  //This delivers a 'tick' tuple on a specific interval,
  //which is used to trigger certain actions
  @Override
  public Map<String, Object> getComponentConfiguration() {
      Config conf = new Config();
      conf.put(Config.TOPOLOGY_TICK_TUPLE_FREQ_SECS, emitFrequency);
      return conf;
  }

  //execute is called to process tuples
  @Override
  public void execute(Tuple tuple, BasicOutputCollector collector) {
    //If it's a tick tuple, emit all words and counts
    if(tuple.getSourceComponent().equals(Constants.SYSTEM_COMPONENT_ID)
            && tuple.getSourceStreamId().equals(Constants.SYSTEM_TICK_STREAM_ID)) {
      for(String word : counts.keySet()) {
        Integer count = counts.get(word);
        collector.emit(new Values(word, count));
        logger.info("Emitting a count of " + count + " for word " + word);
      }
    } else {
      //Get the word contents from the tuple
      String word = tuple.getString(0);
      //Have we counted any already?
      Integer count = counts.get(word);
      if (count == null)
        count = 0;
      //Increment the count and store it
      count++;
      counts.put(word, count);
    }
  }

  //Declare that this emits a tuple containing two fields; word and count
  @Override
  public void declareOutputFields(OutputFieldsDeclarer declarer) {
    declarer.declare(new Fields("word", "count"));
  }
}
```

### <a name="define-the-topology"></a>토폴로지 정의

토폴로지는 spout 및 bolt를 그래프로 묶습니다. 이 그래프는 구성 요소 사이의 데이터 흐름 방식을 정의합니다. Storm이 클러스터 내에서 구성 요소의 인스턴스를 만들 대 사용하는 병렬 처리 힌트도 제공합니다.

다음 이미지는 이 토폴로지에 대한 구성 요소 그래프의 기본 다이어그램입니다.

![Spout 및 Bolt 배열을 보여 주는 다이어그램](./media/apache-storm-develop-java-topology/wordcount-topology.png)

토폴로지를 구현 하려면를 만들고 새 파일을 열고 아래 명령을 입력 `WordCountTopology.java`:

```cmd
notepad src\main\java\com\microsoft\example\WordCountTopology.java
```

복사 하 고 새 파일에 다음 java 코드를 붙여 넣습니다.  그런 다음 파일을 닫습니다.

```java
package com.microsoft.example;

import org.apache.storm.Config;
import org.apache.storm.LocalCluster;
import org.apache.storm.StormSubmitter;
import org.apache.storm.topology.TopologyBuilder;
import org.apache.storm.tuple.Fields;

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
    //Set to false to disable debug information when
    // running in production on a cluster
    conf.setDebug(false);

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
```

### <a name="configure-logging"></a>로깅 구성

Storm은 [Apache Log4j 2](https://logging.apache.org/log4j/2.x/)를 사용하여 정보를 기록합니다. 로깅을 구성하지 않으면 토폴로지는 진단 정보를 내보냅니다. 로깅되는 내용을 제어, 라는 파일을 만듭니다 `log4j2.xml` 에 `resources` 아래 명령을 입력 하 여 디렉터리:

```cmd
notepad resources\log4j2.xml
```

복사 후 새 파일에 아래에 있는 XML 텍스트를 붙여넣습니다.  그런 다음 파일을 닫습니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
<Appenders>
    <Console name="STDOUT" target="SYSTEM_OUT">
        <PatternLayout pattern="%d{HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
    </Console>
</Appenders>
<Loggers>
    <Logger name="com.microsoft.example" level="trace" additivity="false">
        <AppenderRef ref="STDOUT"/>
    </Logger>
    <Root level="error">
        <Appender-Ref ref="STDOUT"/>
    </Root>
</Loggers>
</Configuration>
```

이 XML은 이 예제 토폴로지의 구성 요소를 포함하는 `com.microsoft.example` 클래스에 대한 새 로거를 구성합니다. 수준은 이 로거에 대한 추적으로 설정되며, 이 토폴로지의 구성 요소에서 내보낸 모든 로깅 정보가 캡처됩니다.

`<Root level="error">` 섹션은 루트 수준의 로깅(모든 항목이 `com.microsoft.example`에 있지는 않음)을 오류 정보를 기록하도록 구성합니다.

Log4j 2의 로깅 구성에 대한 자세한 내용은 [https://logging.apache.org/log4j/2.x/manual/configuration.html](https://logging.apache.org/log4j/2.x/manual/configuration.html)을 참조하세요.

> [!NOTE]  
> Storm 버전 0.10.0 이상은 Log4j 2.x를 사용합니다. 이전 버전의 Storm은 로그 구성에 다른 형식을 사용하는 Log4j 1.x를 사용합니다. 오래된 구성에 대한 자세한 내용은 [https://wiki.apache.org/logging-log4j/Log4jXmlFormat](https://wiki.apache.org/logging-log4j/Log4jXmlFormat)을 참조하세요.

## <a name="test-the-topology-locally"></a>로컬에서 토폴로지 테스트

파일을 저장한 후 다음 명령을 사용하여 토폴로지를 로컬로 테스트할 수 있습니다.

```cmd
mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology
```

실행할 때 토폴로지가 시작 정보를 표시합니다. 다음 텍스트는 단어 개수 출력의 예제입니다.

    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow

이 예제 로그는 단어 ‘and’가 113번 내보내졌음을 나타냅니다. Spout가 계속해서 동일한 문장을 내보내기 때문에 토폴로지를 실행하면 개수는 계속 증가합니다.

단어 내보내기와 개수 사이에는 5초의 간격이 있습니다. **WordCount** 구성 요소는 틱 튜플이 도착할 때 정보만 내보내도록 구성되어 있습니다. 틱 튜플은 5초마다 배달되어야 합니다.

## <a name="convert-the-topology-to-flux"></a>토폴로지를 Flux로 변환

[Flux](https://storm.apache.org/releases/2.0.0/flux.html)는 Storm 0.10.0 이상에서 사용할 수 있는 새로운 프레임워크로서 구성을 구현과 분리할 수 있습니다. 구성 요소가 여전히 Java로 정의되지만 토폴로지는 YAML 파일을 사용하여 정의됩니다. 프로젝트를 통해 기본 토폴로지 정의를 패키지하거나 토폴로지를 제출할 때 독립 실행형 파일을 사용할 수 있습니다. Storm에 토폴로지를 제출할 때 환경 변수 또는 구성 파일을 사용하여 YAML 토폴로지 정의에서 값을 채울 수 있습니다.

YAML 파일은 토폴로지 및 구성 요소 간 데이터 흐름에 사용할 구성 요소를 정의합니다. jar 파일의 일부로 YAML 파일을 포함하거나 외부 YAML 파일을 사용할 수 있습니다.

Flux에 대한 자세한 내용은 [Flux 프레임워크(https://storm.apache.org/releases/1.0.6/flux.html)](https://storm.apache.org/releases/1.0.6/flux.html)를 참조하세요.

> [!WARNING]  
> Storm 1.0.1의 [버그(https://issues.apache.org/jira/browse/STORM-2055)](https://issues.apache.org/jira/browse/STORM-2055)로 인해 [Storm 개발 환경](https://storm.apache.org/releases/current/Setting-up-development-environment.html)을 설치하여 Flux 토폴로지를 로컬로 실행해야 합니다.

1. 이전에 `WordCountTopology.java` 토폴로지를 정의 하지만 Flux를 사용 하 여 필요 하지 않습니다. 다음 명령 사용 하 여 파일을 삭제 합니다.

    ```cmd
    DEL src\main\java\com\microsoft\example\WordCountTopology.java
    ```

2. 만들고 새 파일을 열고 아래 명령을 입력 `topology.yaml`:

    ```cmd
    notepad resources\topology.yaml
    ```

    복사 후 새 파일에 아래 텍스트를 붙여넣습니다.  그런 다음 파일을 닫습니다.

    ```yaml
    name: "wordcount"       # friendly name for the topology

    config:                 # Topology configuration
      topology.workers: 1     # Hint for the number of workers to create

    spouts:                 # Spout definitions
    - id: "sentence-spout"
      className: "com.microsoft.example.RandomSentenceSpout"
      parallelism: 1      # parallelism hint

    bolts:                  # Bolt definitions
    - id: "splitter-bolt"
      className: "com.microsoft.example.SplitSentence"
      parallelism: 1
        
    - id: "counter-bolt"
      className: "com.microsoft.example.WordCount"
      constructorArgs:
        - 10
      parallelism: 1

    streams:                # Stream definitions
    - name: "Spout --> Splitter" # name isn't used (placeholder for logging, UI, etc.)
      from: "sentence-spout"       # The stream emitter
      to: "splitter-bolt"          # The stream consumer
      grouping:                    # Grouping type
        type: SHUFFLE
    
    - name: "Splitter -> Counter"
      from: "splitter-bolt"
      to: "counter-bolt"
      grouping:
        type: FIELDS
        args: ["word"]           # field(s) to group on
    ```

3. 열려는 아래 명령을 입력 `pom.xml` 아래 설명 된 수정 하려면:

    ```cmd
    notepad pom.xml
    ```

   * `<dependencies>` 섹션에서 다음 새 종속성을 추가합니다.

        ```xml
        <!-- Add a dependency on the Flux framework -->
        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>flux-core</artifactId>
            <version>${storm.version}</version>
        </dependency>
        ```

   * `<plugins>` 섹션에 다음 플러그 인을 추가합니다. 이 플러그 인은 프로젝트에 대한 패키지(jar 파일)를 만들도록 처리하고 패키지를 만들 때 Flux에 특정된 일부 변환을 적용합니다.

        ```xml
        <!-- build an uber jar -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-shade-plugin</artifactId>
            <version>3.2.1</version>
            <configuration>
                <transformers>
                    <!-- Keep us from getting a "can't overwrite file error" -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer" />
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer" />
                    <!-- We're using Flux, so refer to it as main -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                        <mainClass>org.apache.storm.flux.Flux</mainClass>
                    </transformer>
                </transformers>
                <!-- Keep us from getting a bad signature error -->
                <filters>
                    <filter>
                        <artifact>*:*</artifact>
                        <excludes>
                            <exclude>META-INF/*.SF</exclude>
                            <exclude>META-INF/*.DSA</exclude>
                            <exclude>META-INF/*.RSA</exclude>
                        </excludes>
                    </filter>
                </filters>
            </configuration>
            <executions>
                <execution>
                    <phase>package</phase>
                    <goals>
                        <goal>shade</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
        ```

   * 에 **exec maven 플러그인** `<configuration>` 섹션에서 값을 변경 `<mainClass>` 에서 `${storm.topology}` 에 `org.apache.storm.flux.Flux`입니다. 이 설정을 통해 Flux가 개발 중에 로컬로 토폴로지 실행을 처리하도록 할 수 있습니다.

   * 에 `<resources>` 섹션에서 다음을 추가 합니다 `<includes>`합니다. 이 XML에는 토폴로지를 프로젝트의 일환으로 정의하는 YAML 파일이 포함됩니다.

        ```xml
        <include>topology.yaml</include>
        ```

## <a name="test-the-flux-topology-locally"></a>로컬에서 Flux 토폴로지 테스트

1. 컴파일 및 Maven을 사용 하 여 Flux 토폴로지를 실행 하려면 다음 명령을 입력 합니다.

    ```cmd
    mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
    ```

    > [!WARNING]  
    > 토폴로지가 Storm 1.0.1 비트를 사용하는 경우 이 명령은 실패합니다. 이 실패는 [https://issues.apache.org/jira/browse/STORM-2055](https://issues.apache.org/jira/browse/STORM-2055)로 인한 것입니다. 대신, [개발 환경에서 Storm을 설치](https://storm.apache.org/releases/current/Setting-up-development-environment.html)하고 다음 단계를 사용합니다.
    >
    > [개발 환경에서 Storm을 설치](https://storm.apache.org/releases/current/Setting-up-development-environment.html)한 경우 다음 명령을 대신 사용할 수 있습니다.
    >
    > ```cmd
    > mvn compile package
    > storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml
    > ```

    `--local` 매개 변수는 개발 환경에서 토폴로지를 로컬 모드로 실행합니다. `-R /topology.yaml` 매개 변수는 jar 파일에서 `topology.yaml` 파일 리소스를 사용하여 토폴로지를 정의합니다.

    실행할 때 토폴로지가 시작 정보를 표시합니다. 다음 텍스트는 출력의 예제입니다.

        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
        17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs

    기록된 정보 배치 간에 10초의 지연이 있습니다.

2. 프로젝트에서 새 토폴로지 yaml을 만듭니다.
 
    a. 열려는 아래 명령을 입력 `topology.xml`:

    ```cmd
    notepad resources\topology.yaml
    ```

    b. 찾은 다음 섹션의 값을 변경 `10` 에 `5`입니다. 이렇게 수정하면 단어 수의 배치를 내보내는 간격이 10초에서 5초로 변경됩니다.  

    ```yaml
    - id: "counter-bolt"
      className: "com.microsoft.example.WordCount"
      constructorArgs:
        - 5
      parallelism: 1  
    ```  

    c. 파일로 저장 `newtopology.yaml`합니다.

3. 토폴로지를 실행 하려면 다음 명령을 입력 합니다.

    ```cmd
    mvn exec:java -Dexec.args="--local resources/newtopology.yaml"
    ```

    또는 개발 환경에서 Storm을 설치한 경우:

    ```cmd
    storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local resources/newtopology.yaml
    ```

     이 명령은 사용는 `newtopology.yaml` 토폴로지 정의 합니다. `compile` 매개 변수를 포함하지 않았기 때문에 Maven은 이전 단계에서 빌드한 프로젝트의 버전을 사용합니다.

    토폴로지가 시작 되 면 내보낸된 배치 간의 시간 값을 반영 하도록 변경 되었는지 확인 해야 하면 `newtopology.yaml`합니다. 따라서 토폴로지를 다시 컴파일하지 않고도 YAML 파일을 통해 구성을 변경할 수 있습니다.

Flux 프레임워크의 다른 기능에 대한 자세한 내용은 [Flux(https://storm.apache.org/releases/current/flux.html)](https://storm.apache.org/releases/current/flux.html)를 참조하세요.

## <a name="trident"></a>Trident

[Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html)는 Storm에서 제공하는 높은 수준의 추상화이며 상태 저장 처리를 지원합니다. Trident의 주요 이점은 토폴로지가 입력하는 모든 메시지가 한 번만 처리된다는 점입니다. Trident를 사용하지 않으면 토폴로지는 메시지가 최소한 한 번은 처리된다는 것만 보장할 수 있습니다. Bolt를 만드는 대신 사용할 수 있는 기본 제공 구성 요소와 같은 다른 차이점도 있습니다. 사실 Bolt는 필터, 프로젝션 및 함수와 같이 덜 일반적인 구성 요소로 대체됩니다.

Trident 애플리케이션은 Maven 프로젝트를 사용하여 만들 수 있습니다. 이 문서의 앞부분에 제공된 것과 동일한 기본 단계를 거치며 코드만 다릅니다. Trident도 현재 Flux 프레임워크에서 사용할 수 없습니다.

Trident에 대한 자세한 내용은 [Trident API 개요](https://storm.apache.org/releases/current/Trident-API-Overview.html)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Java를 사용하여 Apache Storm 토폴로지를 만드는 방법을 알아봤으면 이제 다음으로 이동합니다.

* [HDInsight에서 Apache Storm 토폴로지 배포 및 관리](apache-storm-deploy-monitor-topology-linux.md)

* [Visual Studio를 사용하여 HDInsight에서 Apache Storm에 대한 C# 토폴로지 개발](apache-storm-develop-csharp-visual-studio-topology.md)

Apache Storm 토폴로지에 대한 추가 예제는 [HDInsight의 Apache Storm에 대한 예제 토폴로지](apache-storm-example-topology.md)를 참조하세요.
