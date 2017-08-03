---
title: "Java를 사용하여 HDInsight의 Storm으로 Event Hub에서 이벤트 처리 | Microsoft Docs"
description: "Maven으로 만든 Java Storm 토폴로지를 사용하여 이벤트 허브 데이터를 처리하는 방법을 알아봅니다."
services: hdinsight,notification hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 453fa7b0-c8a6-413e-8747-3ac3b71bed86
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/13/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 2e8ebbdab2be7bed224a67facec798820615bb22
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-java"></a>HDInsight의 Storm으로 Azure 이벤트 허브에서 이벤트 처리(Java)

HDInsight의 Storm에서 Azure Event Hubs를 사용하는 방법에 대해 알아봅니다. 이 예제는 Azure Event Hubs에서 데이터를 읽고 는 Java 기반 구성 요소를 사용합니다.

Azure 이벤트 허브를 사용하면 웹 사이트, 앱 및 장치에서 대량의 데이터를 처리할 수 있습니다. Event Hub spout를 사용하면 HDInsight에서 Apache Storm을 사용하여 이 데이터를 실시간으로 쉽게 분석할 수 있습니다. 또한 이벤트 허브 Bolt를 사용하여 Storm에서 이벤트 허브에 데이터를 기록할 수도 있습니다.

## <a name="prerequisites"></a>필수 조건

* HDInsight 클러스터 버전 3.6의 Apache Storm 자세한 내용은 [HDInsight 클러스터에서 Storm 시작](hdinsight-apache-storm-tutorial-get-started-linux.md)을 참조하세요.

    > [!IMPORTANT]
    > Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중지](hdinsight-component-versioning.md#hdinsight-windows-retirement)를 참조하세요.

* [Azure 이벤트 허브](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

* [OpenJDK](http://openjdk.java.net/)와 같은 [Oracle JDK(Java 개발자 키트) 버전 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 또는 그와 동등

* [Maven](https://maven.apache.org/download.cgi): Maven은 Java 프로젝트용 프로젝트 빌드 시스템입니다.

* 텍스트 편집기 또는 통합 개발 환경(IDE)입니다.

    > [!NOTE]
    > 편집기 또는 IDE에 이 문서에서 다루지 않은 Maven과 함께 동작하는 특정 기능이 있을 수 있습니다. 편집 환경 기능에 대한 내용은 사용 중인 제품의 설명서를 참조하세요.

    * SSH 클라이언트. 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

* `ssh` 및 `scp` 명령. 이러한 명령은 파일을 HDInsight 클러스터에 복사하는 데 사용됩니다. Windows의 경우 Windows 10의 Bash를 통해 이러한 명령을 사용할 수 있습니다.

## <a name="understanding-the-example"></a>예제 이해

[hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) 예제는 두 토폴로지를 포함합니다.

`resources/writer.yaml` 토폴로지는 임의 데이터를 Azure Event Hub에 기록합니다. 데이터는 `DeviceSpout` 구성 요소에서 생성되는 임의 장치 ID 및 장치 값입니다. 따라서 문자열 ID 및 숫자 값을 내보내는 하드웨어 일부를 시뮬레이션 중입니다.

`resources/reader.yaml` 토폴로지는 Event Hub에서 데이터(EventHubWriter에서 작성한 데이터)를 읽고 JSON 데이터를 구문 분석한 다음 `deviceId` 및 `deviceValue` 데이터를 기록합니다.

데이터는 이벤트 허브에 기록되기 전에 JSON 문서로 형식이 지정되고 판독기에서 읽을 때 JSON에서 튜플로 구문이 분석됩니다. JSON 형식은 다음과 같습니다.

    { "deviceId": "unique identifier", "deviceValue": some value }

### <a name="project-configuration"></a>프로젝트 구성

`POM.xml` 파일은 이 Maven 프로젝트에 대한 구성 정보를 포함합니다. 흥미로운 부분은 다음과 같습니다.

#### <a name="event-hub-components"></a>Event Hub 구성 요소

Azure Event Hubs를 읽고 Azure Event Hubs에 쓰는 구성 요소는 [HDInsight 리포지토리](https://github.com/hdinsight/mvn-rep)에 있습니다. `POM.xml` 파일의 다음 섹션은 이 리포지토리에서 구성 요소를 로드합니다.

```xml
<repositories>
    <repository>
        <id>hdinsight-examples</id>
        <url>http://raw.github.com/hdinsight/mvn-repo/master</url>
    </repository>
</repositories>
```

#### <a name="the-eventhubs-storm-spout-dependency"></a>EventHubs Storm Spout 종속성

```xml
<dependency>
    <groupId>com.microsoft</groupId>
    <artifactId>eventhubs</artifactId>
    <version>${storm.eventhub.version}</version>
</dependency>
```

이 xml은 eventhubs 패키지에 대한 종속성을 추가하며 이는 Event Hubs에서 읽기의 경우 Spout 및 쓰기의 경우 Bolt를 모두 포함합니다.

```xml
</source>
    <target>1.8</target>
    </configuration>
</plugin>
```

이 xml은 HDInsight 3.5 이상에서 사용되는 Java 8에 대한 출력을 생성하는 프로젝트를 구성합니다.

#### <a name="the-maven-shade-plugin"></a>maven-shade-plugin

```xml
<!-- build an uber jar -->
<plugin>
<groupId>org.apache.maven.plugins</groupId>
<artifactId>maven-shade-plugin</artifactId>
<version>2.3</version>
<configuration>
    <transformers>
    <!-- Keep us from getting a can't overwrite file error -->
    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer"/>
    <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer"/>
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

이 xml은 uber jar에 출력을 패키지하는 솔루션을 구성합니다. jar은 프로젝트 코드와 필수 종속성을 모두 포함합니다. 다음에 사용됩니다.

* 종속성에 대한 라이선스 파일의 이름을 바꿉니다.
* 보안/서명을 제외합니다.
* 동일한 인터페이스의 여러 구현이 하나의 항목으로 병합됩니다.

이러한 구성 설정은 런타임 시 오류를 방지합니다.

#### <a name="topology-definitions"></a>토폴로지 정의

이 예제에서는 [Flux](https://storm.apache.org/releases/1.1.0/flux.html) 프레임워크를 사용합니다. 이 프레임워크에서는 YAML을 사용하여 토폴로지를 정의합니다. 주요 이점은 Java 코드로 토폴로지를 하드 코드하지 않는다는 점입니다. 정의가 YAML이므로 모든 항목을 다시 컴파일할 필요 없이 토폴로지를 제출하기 전에 정의를 변경할 수 있습니다.

__writer.yaml__:

```yaml
---
# Topology that reads from Event Hubs
name: "eventhubwriter"

components:
  # Configure the Event Hub spout
  - id: "eventhubbolt-config"
    className: "org.apache.storm.eventhubs.bolt.EventHubBoltConfig"
    constructorArgs:
      # These are populated from the .properties file when the topology is started
      - "${eventhub.write.policy.name}"
      - "${eventhub.write.policy.key}"
      - "${eventhub.namespace}"
      - "servicebus.windows.net"
      - "${eventhub.name}"

spouts:
  - id: "device-emulator-spout"
    className: "com.microsoft.example.DeviceSpout"
    parallelism: ${eventhub.partitions}

bolts:
  - id: "eventhub-bolt"
    className: "org.apache.storm.eventhubs.bolt.EventHubBolt"
    constructorArgs:
      - ref: "eventhubbolt-config" # config declared in components section
    # parallelism hint. This should be the same as the number of partitions for your Event Hub, so we read it from the dev.properties file passed at run time.
    parallelism: ${eventhub.partitions}

  # Log information
  - id: "log-bolt"
    className: "org.apache.storm.flux.wrappers.bolts.LogInfoBolt"
    parallelism: 1

# How data flows through the components
streams:
  - name: "spout -> eventhub" # just a string used for logging
    from: "device-emulator-spout"
    to: "eventhub-bolt"
    grouping:
        type: SHUFFLE

  - name: "spout -> logger"
    from: "device-emulator-spout"
    to: "log-bolt"
    grouping:
        type: SHUFFLE
```

__reader.yaml__:

```yaml
---
# Topology that reads from Event Hubs
name: "eventhubreader"

components:
  # Configure the Event Hub spout
  - id: "eventhubspout-config"
    className: "org.apache.storm.eventhubs.spout.EventHubSpoutConfig"
    constructorArgs:
      # These are populated from the .properties file when the topology is started
      - "${eventhub.read.policy.name}"
      - "${eventhub.read.policy.key}"
      - "${eventhub.namespace}"
      - "${eventhub.name}"
      - ${eventhub.partitions}

spouts:
  - id: "eventhub-spout"
    className: "org.apache.storm.eventhubs.spout.EventHubSpout"
    constructorArgs:
      - ref: "eventhubspout-config" # config declared in components section
    # parallelism hint. This should be the same as the number of partitions for your Event Hub, so we read it from the dev.properties file passed at run time.
    parallelism: ${eventhub.partitions}

bolts:
  # Log information
  - id: "log-bolt"
    className: "org.apache.storm.flux.wrappers.bolts.LogInfoBolt"
    parallelism: 1

  # Parses from JSON into tuples
  - id: "parser-bolt"
    className: "com.microsoft.example.ParserBolt"
    parallelism: ${eventhub.partitions}

# How data flows through the components
streams:
  - name: "spout -> parser" # just a string used for logging
    from: "eventhub-spout"
    to: "parser-bolt"
    grouping:
        type: SHUFFLE

  - name: "parser -> log-bolt"
    from: "parser-bolt"
    to: "log-bolt"
    grouping:
        type: SHUFFLE
```

#### <a name="tell-the-topology-about-event-hub"></a>Event Hub에 대한 토폴로지 알리기

런타임에 `dev.properties` 파일은 Event Hub 구성을 토폴로지에 전달하는 데 사용됩니다. 다음 예제는 파일의 기본 내용입니다.

```yaml
eventhub.write.policy.name: writer
eventhub.write.policy.key: your_key_here
eventhub.read.policy.name: reader
eventhub.read.policy.key: your_key_here
eventhub.namespace: your_namespace_here
eventhub.name: storm
eventhub.partitions: 2
```

## <a name="configure-environment-variables"></a>환경 변수 구성

Java 및 JDK를 설치할 때 사용자의 개발 워크스테이션에 다음 환경 변수를 설정할 수 있습니다. 하지만 변수가 존재하며 시스템에 대한 올바른 값을 포함하는지 확인해야 합니다.

* **JAVA_HOME** - JRE(Java runtime environment)가 설치된 디렉터리를 가리켜야 합니다. 예를 들어 Unix 또는 Linux 배포에서는 `/usr/lib/jvm/java-7-oracle`과 유사한 값이어야 합니다. Windows에서는 `c:\Program Files (x86)\Java\jre1.7`
* **PATH** - 다음 경로를 포함해야 합니다.

  * **JAVA_HOME** 또는 그와 동등한 경로
  * **JAVA_HOME\bin** 또는 그와 동등한 경로
  * Maven이 설치된 디렉터리

## <a name="configure-event-hub"></a>이벤트 허브 구성

이벤트 허브는 이 예제의 데이터 원본입니다. 다음 단계에 따라 Event Hub를 만듭니다.

1. [Azure 클래식 포털](https://manage.windowsazure.com)에서 **새로 만들기** > **Service Bus** > **이벤트 허브** > **사용자 지정 만들기**를 선택합니다.

2. **새 Event Hub 추가** 화면에서 **Event Hub 이름**을 입력합니다. **지역**을 선택하여 허브를 만든 다음 네임스페이스를 만들거나 기존 항목을 선택합니다. 마지막으로 **화살표**를 클릭하여 계속합니다.

    ![마법사 페이지 1](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

   > [!NOTE]
   > 대기 시간 및 비용을 줄이려면 HDInsight 서버의 Storm과 동일한 **위치**를 선택합니다.

3. **이벤트 허브 구성** 화면에서 **파티션 개수** 및 **메시지 보존** 값을 입력합니다. 이 예에서는 파티션 개수로 10을, 메시지 보존으로는 1을 사용합니다. 파티션 개수 값은 나중에 필요하므로 기록해 둡니다.

    ![마법사 페이지 2](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

4. 이벤트 허브를 만든 후 네임스페이스를 선택하고 **이벤트 허브**를 선택한 다음 앞에서 만든 이벤트 허브를 선택합니다.
5. **구성**을 선택하고 다음 정보를 사용하여 새 액세스 정책 두 개를 만듭니다.

    <table>
    <tr><th>이름</th><th>권한</th></tr>
    <tr><td>기록기</td><td>보내기</td></tr>
    <tr><td>읽기 권한자</td><td>수신 대기</td></tr>
    </table>

    권한을 만든 후 페이지 아래쪽의 **저장** 아이콘을 선택합니다. 이러한 공유 액세스 정책은 Event Hub에 대한 읽기 및 쓰기에 사용됩니다.

    ![정책](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

6. 정책을 저장한 후 페이지 아래쪽의 **공유 액세스 키 생성기**를 사용하여 **기록기** 및 **판독기** 정책에 대한 키를 검색합니다. 이러한 키를 저장합니다.

## <a name="download-and-build-the-project"></a>프로젝트 다운로드 및 빌드

1. GitHub에서 프로젝트 다운로드: [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)입니다. zip 아카이브로 패키지를 다운로드하거나 [git](https://git-scm.com/) 를 사용하여 프로젝트를 로컬로 복제할 수 있습니다.

2. 해당 Event Hub에 대한 구성으로 `dev.properties` 파일을 수정합니다.

3. 다음을 사용하여 프로젝트를 빌드하고 패키징합니다.

        mvn package

    이 명령은 필수 종속성을 다운로드하고 프로젝트를 빌드한 다음 패키징합니다. 출력은 **/target** 디렉터리에 **EventHubExample-1.0-SNAPSHOT.jar**로 저장됩니다.

## <a name="test-locally"></a>로컬에서 테스트

이러한 토폴로지는 Event Hubs를 읽고 Event Hubs에 쓸 뿐이므로 [Storm 개발 환경](http://storm.apache.org/releases/current/Setting-up-development-environment.html)이 있는 경우 로컬에서 이러한 토폴로지를 테스트할 수 있습니다. 다음 단계를 사용하여 개발 환경에서 로컬로 실행합니다.

1. 기록기를 실행합니다.

        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /writer.yaml --filter dev.properties

2. 판독기를 실행합니다.

        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /reader.yaml --filter dev.properties

> [!TIP]
> * `--local`: 로컬 모드(비분산)에서 토폴로지를 실행합니다.
> * `-R /writer.yaml`: jar에 패키지된 `resources`에서 토폴로지 정의를 로드합니다. 토폴로지가 로컬 파일 시스템의 파일인 경우 대신에 해당 파일 경로를 마지막 매개 변수로 지정합니다.
> * `--filter dev.properties`: `dev.properties` 내용을 사용하여 토폴로지 정의의 값을 채웁니다. 예: `${eventhub.read.policy.name}`

로컬로 실행할 때 출력은 콘솔에 기록됩니다. __Ctrl+C__를 사용하여 토폴로지를 중지합니다.

## <a name="deploy-the-topologies"></a>토폴로지 배포

1. SCP를 사용하여 HDInsight 클러스터에 jar 패키지를 복사합니다. 클러스터에 SSH 사용자로 사용자 이름을 바꿉니다. CLUSTERNAME을 HDInsight 클러스터의 이름으로 바꿉니다.

        scp ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

    SSH 계정에 암호를 사용한 경우 암호를 입력하라는 메시지가 나타납니다. 계정에서 SSH 키를 사용한 경우 `-i` 매개 변수를 사용하여 키 파일에 대한 경로를 지정해야 할 수 있습니다. 예를 들어 `scp -i ~/.ssh/id_rsa ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`

    이 명령은 클러스터에 있는 SSH 사용자의 홈 디렉터리에 파일을 복사합니다.

2. 파일 업로드가 완료되면 SSH를 사용하여 HDInsight 클러스터에 연결할 수 있습니다. **USERNAME**을 SSH 로그인의 이름으로 바꿉니다. **CLUSTERNAME** 을 HDInsight 클러스터 이름으로 바꿉니다.

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [!NOTE]
    > SSH 계정에 암호를 사용한 경우 암호를 입력하라는 메시지가 나타납니다. 계정에서 SSH 키를 사용한 경우 `-i` 매개 변수를 사용하여 키 파일에 대한 경로를 지정해야 할 수 있습니다. 다음 예제에서는 `~/.ssh/id_rsa`에서 개인 키를 로드합니다.
    >
    > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

3. 다음 명령을 사용하여 토폴로지를 시작합니다.

        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties

    > [!TIP]
    > * `--remote`: Nimbus 서비스에 토폴로지를 제출합니다. 그러면 클러스터의 작업자 노드에서 토폴로지가 시작됩니다.

4. 기록된 데이터를 보려면 https://CLUSTERNAME.azurehdinsight.net/stormui로 이동합니다. 여기서 __CLUSTERNAME__은 HDInsight 클러스터의 이름입니다. 토폴로지를 선택하고 구성 요소로 드릴다운합니다. 기록된 정보를 볼 구성 요소의 인스턴스에 대한 __port__ 항목을 선택합니다.

5. 다음 명령을 사용하여 토폴로지를 중지합니다.

        storm kill reader
        storm kill writer

## <a name="delete-your-cluster"></a>클러스터 삭제

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>다음 단계

* [HDInsight의 Storm에 대한 예제 토폴로지](hdinsight-storm-example-topology.md)

