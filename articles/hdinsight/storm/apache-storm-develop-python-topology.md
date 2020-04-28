---
title: Python 구성 요소 Apache Storm-Azure HDInsight
description: Azure HDInsight에서 Python 구성 요소를 사용 하는 Apache Storm 토폴로지를 만드는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/16/2019
ms.openlocfilehash: 20e4827b1a86bff338646ef71f0dd732255c09c9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77460027"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>HDInsight에서 Python을 사용하여 Apache Storm 토폴로지 개발

Python 구성 요소를 사용하는 [Apache Storm](https://storm.apache.org/) 토폴로지를 만드는 방법에 대해 알아봅니다. Apache Storm은 여러 언어를 지원하여 한 토폴로지에 여러 언어의 구성 요소를 결합할 수 있습니다. [Flux](https://storm.apache.org/releases/current/flux.html) 프레임워크(Storm 0.10.0에서 소개)를 사용하면 Python 구성 요소를 사용하는 솔루션을 쉽게 만들 수 있습니다.

> [!IMPORTANT]  
> 이 문서의 정보는 HDInsight 3.6에서 Storm을 사용하여 테스트했습니다.

## <a name="prerequisites"></a>사전 요구 사항

* HDInsight의 Apache Storm 클러스터. [Azure Portal을 사용하여 Apache Hadoop 클러스터 만들기](../hdinsight-hadoop-create-linux-clusters-portal.md)를 참조하고 **클러스터 유형**에 **Storm**을 선택합니다.

* 로컬 스톰 개발 환경 (선택 사항). 로컬 Storm 환경은 토폴로지를 로컬로 실행하려는 경우에만 필요합니다. 자세한 내용은 [개발 환경 설정](https://storm.apache.org/releases/current/Setting-up-development-environment.html)(영문)을 참조하세요.

* [Python 2.7 이상](https://www.python.org/downloads/).

* [JDK (Java Developer Kit) 버전 8](https://aka.ms/azure-jdks)입니다.

* Apache에 따라 올바르게 [설치된](https://maven.apache.org/install.html)[Apache Maven](https://maven.apache.org/download.cgi)  Maven은 Java 프로젝트용 프로젝트 빌드 시스템입니다.

## <a name="storm-multi-language-support"></a>Storm 다중 언어 지원

Apache Storm은 모든 프로그래밍 언어로 작성된 구성 요소를 사용하도록 설계되었습니다. 구성 요소에서 Storm에 대한 Thrift 정의를 사용하는 방법을 이해해야 합니다. Python의 경우 모듈은 Apache Storm 프로젝트의 일부로 제공되므로 Storm과 쉽게 인터페이스할 수 있습니다. 이 모듈은에서 [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py)찾을 수 있습니다.

Storm은 JVM(Java Virtual Machine)에서 실행되는 Java 프로세스입니다. 다른 언어로 작성된 구성 요소는 하위 프로세스로 실행됩니다. Storm은 stdin/stdout을 통해 전송되는 JSON 메시지를 사용하여 이러한 하위 프로세스와 통신합니다. 구성 요소 간의 통신에 대한 자세한 내용은 [다중 언어 프로토콜](https://storm.apache.org/releases/current/Multilang-protocol.html) (영문) 설명서에서 확인할 수 있습니다.

## <a name="python-with-the-flux-framework"></a>Flux 프레임워크를 사용하는 Python

Flux 프레임워크를 사용하면 구성 요소와 별도로 Storm 토폴로지를 정의할 수 있습니다. Flux 프레임워크는 YAML을 사용하여 Storm 토폴로지를 정의합니다. 다음 텍스트는 YAML 문서에서 Python 구성 요소를 참조하는 방법의 예제입니다.

```yaml
# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "org.apache.storm.flux.wrappers.spouts.FluxShellSpout"
    constructorArgs:
      # Command line
      - ["python", "sentencespout.py"]
      # Output field(s)
      - ["sentence"]
    # parallelism hint
    parallelism: 1
```

`FluxShellSpout` 클래스는 Spout를 구현하는 `sentencespout.py` 스크립트를 시작하는 데 사용됩니다.

Flux에서는 토폴로지를 포함하는 jar 파일 내의 `/resources` 디렉터리에 Python 스크립트가 있어야 합니다. 따라서 이 예제는 Python 스크립트를 `/multilang/resources` 디렉터리에 저장합니다. `pom.xml`은 다음 XML을 사용하여 이 파일을 포함합니다.

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

앞에서 설명한 것 처럼 스톰에 `storm.py` 대 한 Thrift 정의를 구현 하는 파일이 있습니다. Flux 프레임워크는 프로젝트를 빌드할 때 `storm.py`를 자동으로 포함하므로 파일을 포함하는 것에 대해 걱정할 필요가 없습니다.

## <a name="build-the-project"></a>프로젝트 빌드

1. 에서 [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount)프로젝트를 다운로드 합니다.

1. 명령 프롬프트를 열고 프로젝트 루트: `hdinsight-python-storm-wordcount-master`로 이동 합니다. 다음 명령을 입력합니다.

    ```cmd
    mvn clean compile package
    ```

    이 명령은 컴파일된 토폴로지를 포함하는 `target/WordCount-1.0-SNAPSHOT.jar` 파일을 만듭니다.

## <a name="run-the-storm-topology-on-hdinsight"></a>HDInsight에서 Storm 토폴로지 실행

1. [Ssh 명령을](../hdinsight-hadoop-linux-use-ssh-unix.md) 사용 하 여 HDInsight `WordCount-1.0-SNAPSHOT.jar` 클러스터의 스톰에 파일을 복사 합니다. CLUSTERNAME을 클러스터의 이름으로 바꿔서 아래 명령을 편집 하 고 명령을 입력 합니다.

    ```cmd
    scp target/WordCount-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. 파일이 업로드되면 SSH를 사용하여 클러스터에 연결합니다.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. SSH 세션에서 다음 명령을 사용하여 클러스터에서 토폴로지를 시작합니다.

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

    Storm 토폴로지가 시작되면 중지될 때까지 실행됩니다.

1. 스톰 UI를 사용 하 여 클러스터의 토폴로지를 볼 수 있습니다. Storm UI는 `https://CLUSTERNAME.azurehdinsight.net/stormui`에 있습니다. `CLUSTERNAME`를 클러스터 이름으로 바꿉니다.

1. 스톰 토폴로지를 중지 합니다. 다음 명령을 사용 하 여 클러스터에서 토폴로지를 중지 합니다.

    ```bash
    storm kill wordcount
    ```

    또는 스톰 UI를 사용할 수 있습니다. 토폴로지에 대 한 **토폴로지 작업** 에서 **중지**를 선택 합니다.

## <a name="run-the-topology-locally"></a>로컬로 토폴로지 실행

토폴로지를 로컬로 실행하려면 다음 명령을 사용합니다.

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]  
> 이 명령에는 로컬 Storm 개발 환경이 필요합니다. 자세한 내용은 [개발 환경 설정](https://storm.apache.org/releases/current/Setting-up-development-environment.html)(영문)을 참조하세요.

토폴로지가 시작되면 다음 텍스트와 비슷한 정보를 로컬 콘솔로 내보냅니다.

```output
24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}
```

토폴로지를 중지하려면 __Ctrl + C__를 사용합니다.

## <a name="next-steps"></a>다음 단계

HDInsight에서 Python을 사용 하는 다른 방법에 대해서는 다음 문서를 참조 하세요. [Apache Pig 및 Apache Hive에서 PYTHON UDF (사용자 정의 함수)를 사용 하는 방법](../hadoop/python-udf-hdinsight.md)입니다.
