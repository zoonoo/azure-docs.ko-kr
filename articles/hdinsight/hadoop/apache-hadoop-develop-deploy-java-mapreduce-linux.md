---
title: Apache Hadoop용 Java MapReduce 만들기 - Azure HDInsight
description: Apache Maven을 사용하여 Java 기반 MapReduce 애플리케이션을 만든 다음 Azure HDInsight의 Hadoop과 함께 실행하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-java
ms.date: 01/16/2020
ms.openlocfilehash: 84d9253b865ddac6d97395af3d8632e29cc2ea24
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87323811"
---
# <a name="develop-java-mapreduce-programs-for-apache-hadoop-on-hdinsight"></a>HDInsight에서 Apache Hadoop용 Java MapReduce 프로그램 개발

Apache Maven을 사용하여 Java 기반 MapReduce 애플리케이션을 만든 다음, Azure HDInsight의 Hadoop으로 실행하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

* [JDK (Java Developer Kit) 버전 8](https://aka.ms/azure-jdks)입니다.

* Apache에 따라 올바르게 [설치된](https://maven.apache.org/install.html)[Apache Maven](https://maven.apache.org/download.cgi)  Maven은 Java 프로젝트용 프로젝트 빌드 시스템입니다.

## <a name="configure-development-environment"></a>개발 환경 구성

이 문서에 사용 되는 환경은 Windows 10을 실행 하는 컴퓨터 였습니다. 명령은 명령 프롬프트에서 실행 되었으며 다양 한 파일이 메모장을 사용 하 여 편집 되었습니다. 사용자 환경에 맞게 수정 합니다.

명령 프롬프트에서 아래 명령을 입력 하 여 작업 환경을 만듭니다.

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Maven 프로젝트 만들기

1. 다음 명령을 입력 하 여 **wordcountjava**라는 Maven 프로젝트를 만듭니다.

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    이 명령은 매개 변수에 지정 된 이름으로 디렉터리를 만듭니다 `artifactID` (이 예제에서는**wordcountjava** ). 이 디렉터리에는 다음 항목이 포함 됩니다.

    * `pom.xml` - [프로젝트 개체 모델(POM)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)은 프로젝트를 빌드하는 데 사용된 정보 및 구성 세부 정보를 포함합니다.
    * src\main\java\org\apache\hadoop\examples: 응용 프로그램 코드를 포함 합니다.
    * src\test\java\org\apache\hadoop\examples: 응용 프로그램에 대 한 테스트를 포함 합니다.

1. 생성 된 예제 코드를 제거 합니다. `AppTest.java` `App.java` 다음 명령을 입력 하 여 생성 된 테스트 및 응용 프로그램 파일을 삭제 합니다.

    ```cmd
    cd wordcountjava
    DEL src\main\java\org\apache\hadoop\examples\App.java
    DEL src\test\java\org\apache\hadoop\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>프로젝트 개체 모델 업데이트

pom.xml 파일에 대한 전체 참조를 보려면 https://maven.apache.org/pom.html을 참조하세요. `pom.xml`아래 명령을 입력 하 여를 엽니다.

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>종속성 추가

에서 `pom.xml` 섹션에 다음 텍스트를 추가 합니다 `<dependencies>` .

```xml
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-mapreduce-examples</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-mapreduce-client-common</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-common</artifactId>
    <version>2.7.3</version>
    <scope>provided</scope>
</dependency>
```

이 항목은 특정 버전(&lt;version\>에 나열됨)을 사용하는 필수 라이브러리(&lt;artifactId\> 내에 나열됨)를 정의합니다. 컴파일 시 이러한 종속성이 기본 Maven 리포지토리에서 다운로드됩니다. [Maven 리포지토리 검색](https://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) 을 사용하여 자세한 정보를 볼 수 있습니다.

`<scope>provided</scope>`는 이러한 종속성은 런타임에 HDInsight 클러스터에서 제공되므로 애플리케이션과 함께 패키징해서는 안 된다는 점을 Maven에 알려 줍니다.

> [!IMPORTANT]
> 사용되는 버전은 클러스터에 있는 Hadoop 버전과 일치해야 합니다. 버전에 대한 자세한 내용은 [HDInsight 구성 요소 버전 관리](../hdinsight-component-versioning.md) 문서를 참조하세요.

### <a name="build-configuration"></a>빌드 구성

Maven 플러그 인을 사용하면 프로젝트의 빌드 단계를 사용자 지정할 수 있습니다. 이 섹션은 플러그 인, 리소스 및 다른 빌드 구성 옵션을 추가하는 데 사용됩니다.

파일에 다음 코드를 추가 하 고 `pom.xml` 파일을 저장 한 후 닫습니다. 이 텍스트는 파일의 `<project>...</project>` 태그 내에 있어야 합니다. 예를 들어 `</dependencies>`와 `</project>` 사이에 있어야 합니다.

```xml
<build>
    <plugins>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>2.3</version>
        <configuration>
        <transformers>
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
            </transformer>
        </transformers>
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
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.6.1</version>
        <configuration>
        <source>1.8</source>
        <target>1.8</target>
        </configuration>
    </plugin>
    </plugins>
</build>
```

이 섹션에서는 Apache Maven 컴파일러 플러그 인 및 Apache Maven 음영 플러그 인을 구성 합니다. 컴파일러 플러그 인은 토폴로지를 컴파일하는 데 사용됩니다. 음영 플러그 인은 Maven으로 빌드된 JAR 패키지에서 라이선스 중복을 방지하는 데 사용됩니다. 이 플러그 인은 HDInsight 클러스터에서 런타임에 "중복 라이선스 파일" 오류가 발생하지 않도록 하는 데 사용됩니다. `ApacheLicenseResourceTransformer` 구현에서 maven-shade-plugin을 사용하면 이 오류가 방지됩니다.

또한 maven-shade-plugin은 애플리케이션에 필요한 모든 종속성을 포함하는 uber jar도 생성합니다.

`pom.xml` 파일을 저장합니다.

## <a name="create-the-mapreduce-application"></a>MapReduce 애플리케이션 만들기

1. 다음 명령을 입력 하 여 새 파일을 만들고 엽니다 `WordCount.java` . 프롬프트에서 **예** 를 선택 하 여 새 파일을 만듭니다.

    ```cmd
    notepad src\main\java\org\apache\hadoop\examples\WordCount.java
    ```

2. 그런 다음 아래 java 코드를 복사 하 여 새 파일에 붙여넣습니다. 그런 다음 파일을 닫습니다.

    ```java
    package org.apache.hadoop.examples;

    import java.io.IOException;
    import java.util.StringTokenizer;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.IntWritable;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapreduce.Job;
    import org.apache.hadoop.mapreduce.Mapper;
    import org.apache.hadoop.mapreduce.Reducer;
    import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
    import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
    import org.apache.hadoop.util.GenericOptionsParser;

    public class WordCount {

        public static class TokenizerMapper
            extends Mapper<Object, Text, Text, IntWritable>{

        private final static IntWritable one = new IntWritable(1);
        private Text word = new Text();

        public void map(Object key, Text value, Context context
                        ) throws IOException, InterruptedException {
            StringTokenizer itr = new StringTokenizer(value.toString());
            while (itr.hasMoreTokens()) {
            word.set(itr.nextToken());
            context.write(word, one);
            }
        }
    }

    public static class IntSumReducer
            extends Reducer<Text,IntWritable,Text,IntWritable> {
        private IntWritable result = new IntWritable();

        public void reduce(Text key, Iterable<IntWritable> values,
                            Context context
                            ) throws IOException, InterruptedException {
            int sum = 0;
            for (IntWritable val : values) {
            sum += val.get();
            }
            result.set(sum);
            context.write(key, result);
        }
    }

    public static void main(String[] args) throws Exception {
        Configuration conf = new Configuration();
        String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
        if (otherArgs.length != 2) {
            System.err.println("Usage: wordcount <in> <out>");
            System.exit(2);
        }
        Job job = new Job(conf, "word count");
        job.setJarByClass(WordCount.class);
        job.setMapperClass(TokenizerMapper.class);
        job.setCombinerClass(IntSumReducer.class);
        job.setReducerClass(IntSumReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
        FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
        System.exit(job.waitForCompletion(true) ? 0 : 1);
        }
    }
    ```

    패키지 이름은 `org.apache.hadoop.examples`이며 클래스 이름은 `WordCount`입니다. MapReduce 작업을 제출할 때 이 이름을 사용합니다.

## <a name="build-and-package-the-application"></a>애플리케이션 빌드 및 패키지화

`wordcountjava` 디렉터리에서 다음 명령을 사용하여 애플리케이션을 포함하는 JAR 파일을 빌드합니다.

```cmd
mvn clean package
```

이 명령은 이전 빌드 아티팩트를 정리하고, 아직 설치되지 않은 모든 종속성을 다운로드한 후 애플리케이션을 빌드 및 패키지화합니다.

명령이 마무리되면 `wordcountjava/target` 디렉터리는 `wordcountjava-1.0-SNAPSHOT.jar` 파일을 포함합니다.

> [!NOTE]
> `wordcountjava-1.0-SNAPSHOT.jar` 파일은 는 WordCount 작업뿐만 아니라 런타임 시 작업에서 필요로 하는 종속성을 포함하는 uberjar입니다.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>JAR 업로드 및 작업 실행(SSH)

다음 단계에서는 `scp`를 사용하여 HDInsight 클러스터에서 Apache HBase의 기본 헤드 노드에 JAR을 복사합니다. 그런 후 `ssh` 명령은 클러스터에 연결하고 헤드 노드에서 직접 예제를 실행하는 데 사용됩니다.

1. 클러스터에 jar을 업로드 합니다. 을 `CLUSTERNAME` HDInsight 클러스터 이름으로 바꾸고 다음 명령을 입력 합니다.

    ```cmd
    scp target/wordcountjava-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. 클러스터에 연결 합니다. 을 `CLUSTERNAME` HDInsight 클러스터 이름으로 바꾸고 다음 명령을 입력 합니다.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. SSH 세션에서 다음 명령을 사용하여 MapReduce 애플리케이션을 실행합니다.

   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```

    이 명령은 WordCount MapReduce 애플리케이션을 시작합니다. 입력된 파일은 `/example/data/gutenberg/davinci.txt`이며 출력 디렉터리는 `/example/data/wordcountout`입니다. 입력 파일과 출력 모두 클러스터의 기본 스토리지에 저장됩니다.

1. 작업이 완료되면 다음 명령을 사용하여 결과를 확인합니다.

   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    다음 텍스트와 유사한 값을 가진 단어 및 개수 목록이 표시됩니다.

    ```output
    zeal    1
    zelus   1
    zenith  2
    ```

## <a name="next-steps"></a>다음 단계

이 문서에서는 Java MapReduce 작업을 개발하는 방법에 대해 알아보았습니다. HDInsight로 작업하는 다른 방법은 다음 문서를 참조하세요.

* [HDInsight에서 Apache Hive 사용](hdinsight-use-hive.md)
* [HDInsight와 함께 MapReduce 사용](hdinsight-use-mapreduce.md)
* [Java 개발자 센터](https://azure.microsoft.com/develop/java/)
