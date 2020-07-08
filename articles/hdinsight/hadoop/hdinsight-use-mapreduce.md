---
title: HDInsight에서 Apache Hadoop과 MapReduce
description: HDInsight 클러스터의 Apache Hadoop에서 Apache MapReduce 작업을 실행 하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 238f37cc1e8cbc8d3260693181d6e54ac5f592f7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "75435715"
---
# <a name="use-mapreduce-in-apache-hadoop-on-hdinsight"></a>HDInsight의 Apache Hadoop에서 MapReduce 사용

HDInsight 클러스터에서 MapReduce 작업을 실행하는 방법에 대해 알아보세요.

## <a name="example-data"></a>예제 데이터

HDInsight는 `/example/data` 및 `/HdiSamples` 디렉터리에 저장되는 다양한 예제 데이터 집합을 제공합니다. 이러한 디렉터리는 클러스터의 기본 스토리지에 있습니다. 이 문서에서는 `/example/data/gutenberg/davinci.txt` 파일을 사용합니다. 이 파일에는 레오나르도 da 다빈치의 노트북이 포함 되어 있습니다.

## <a name="example-mapreduce"></a>예제 MapReduce

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

고유한 MapReduce 응용 프로그램을 작성 하는 방법에 대 한 지침은 [HDInsight 용 Java MapReduce 응용 프로그램 개발](apache-hadoop-develop-deploy-java-mapreduce-linux.md)을 참조 하세요.

## <a name="run-the-mapreduce"></a>MapReduce 실행

HDInsight는 다양한 메서드를 사용하여 HiveQL 작업을 실행할 수 있습니다. 어떤 메서드가 적합한지 결정하는 다음 테이블을 사용하여 연습할 수 있는 링크를 따르세요.

| **사용**방법 | **... 이렇게 하려면** |  ... **클라이언트 운영 체제** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |**SSH** |Linux, Unix, Mac OS X, 또는 Windows |
| [Curl](apache-hadoop-use-mapreduce-curl.md) |**REST** |Linux, Unix, Mac OS X, 또는 Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |**Windows PowerShell**  |Windows |

## <a name="next-steps"></a>다음 단계

HDInsight에서 데이터를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [HDInsight용 Java MapReduce 프로그램 개발](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [HDInsight에서 Apache Hive 사용](./hdinsight-use-hive.md)
