---
title: HDInsight에서 Apache Hadoop과 MapReduce
description: HDInsight 클러스터의 Apache Hadoop에서 MapReduce 작업을 실행하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: a1bb7a6737115f903391997a5430c32f9a40465f
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207101"
---
# <a name="use-mapreduce-in-apache-hadoop-on-hdinsight"></a>HDInsight의 Apache Hadoop에서 MapReduce 사용

HDInsight 클러스터에서 MapReduce 작업을 실행하는 방법에 대해 알아보세요.

## <a id="data"></a>예제 데이터

HDInsight는 `/example/data` 및 `/HdiSamples` 디렉터리에 저장되는 다양한 예제 데이터 집합을 제공합니다. 이러한 디렉터리는 클러스터의 기본 스토리지에 있습니다. 이 문서에서는 `/example/data/gutenberg/davinci.txt` 파일을 사용합니다. 이 파일에는 레오나르도 da 다빈치의 노트북이 포함 되어 있습니다.

## <a id="job"></a>예제 MapReduce

MapReduce 단어 수 세기 애플리케이션 예가 HDInsight 클러스터에 포함되어 있습니다. 이 예제는 클러스터의 기본 스토리지인 `/example/jars/hadoop-mapreduce-examples.jar`에 있습니다.

다음 Java 코드는 `hadoop-mapreduce-examples.jar` 파일에 포함된 MapReduce 애플리케이션의 원본입니다.

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

고유한 MapReduce 응용 프로그램을 작성 하는 방법에 대 한 지침은 다음 문서를 참조 하세요.

* [HDInsight용 Java MapReduce 애플리케이션 개발](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

## <a id="run"></a>MapReduce 실행

HDInsight는 다양한 메서드를 사용하여 HiveQL 작업을 실행할 수 있습니다. 어떤 메서드가 적합한지 결정하는 다음 테이블을 사용하여 연습할 수 있는 링크를 따르세요.

| **사용 기능**... | **...다음을 수행합니다** | ... **클러스터 운영 체제** | ... **클라이언트 운영 체제** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |**SSH** |Linux |Linux, Unix, Mac OS X, 또는 Windows |
| [Curl](apache-hadoop-use-mapreduce-curl.md) |**REST** |Linux 또는or Windows |Linux, Unix, Mac OS X, 또는 Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |**Windows PowerShell** |Linux 또는or Windows |Windows |

## <a id="nextsteps"></a>다음 단계

HDInsight에서 데이터를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [HDInsight용 Java MapReduce 프로그램 개발](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [HDInsight에서 Apache Hive 사용][hdinsight-use-hive]

* [HDInsight에서 Apache Pig 사용][hdinsight-use-pig]

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: apache-hadoop-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-pig]:hdinsight-use-pig.md


[powershell-install-configure]: /powershell/azureps-cmdlets-docs
