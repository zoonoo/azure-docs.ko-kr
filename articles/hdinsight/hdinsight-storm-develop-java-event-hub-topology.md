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
ms.date: 02/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 110f3aa9ce4848c9350ea2e560205aa762decf7a
ms.openlocfilehash: 85f56e223210f14615f7e4e1c87e35111b238aac
ms.lasthandoff: 02/21/2017


---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-java"></a>HDInsight의 Storm으로 Azure 이벤트 허브에서 이벤트 처리(Java)

HDInsight의 Storm에서 Azure Event Hubs를 사용하는 방법에 대해 알아봅니다. 이 예제는 Azure Event Hubs에서 데이터를 읽고 는 Java 기반 구성 요소를 사용합니다.

Azure 이벤트 허브를 사용하면 웹 사이트, 앱 및 장치에서 대량의 데이터를 처리할 수 있습니다. Event Hub spout를 사용하면 HDInsight에서 Apache Storm을 사용하여 이 데이터를 실시간으로 쉽게 분석할 수 있습니다. 또한 이벤트 허브 Bolt를 사용하여 Storm에서 이벤트 허브에 데이터를 기록할 수도 있습니다.

## <a name="prerequisites"></a>필수 조건

* HDInsight 클러스터 버전 3.5의 Apache Storm 자세한 내용은 [HDInsight 클러스터에서 Storm 시작](hdinsight-apache-storm-tutorial-get-started-linux.md)을 참조하세요.
    
    > [!IMPORTANT]
    > Linux는 HDInsight 버전 3.4 이상에서 사용되는 유일한 운영 체제입니다. 자세한 내용은 [Windows에서 HDInsight 사용 중단](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)을 참조하세요.

* [Azure 이벤트 허브](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

* [OpenJDK](http://openjdk.java.net/)와 같은 [Oracle JDK(Java 개발자 키트) 버전 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) 또는 그와 동등

* [Maven](https://maven.apache.org/download.cgi): Maven은 Java 프로젝트용 프로젝트 빌드 시스템입니다.

* 텍스트 편집기 또는 통합 개발 환경(IDE)입니다.
  
  > [!NOTE]
  > 편집기 또는 IDE에 이 문서에서 다루지 않은 Maven과 함께 동작하는 특정 기능이 있을 수 있습니다. 편집 환경 기능에 대한 내용은 사용 중인 제품의 설명서를 참조하세요.
  
  * SSH 클라이언트. 자세한 내용은 다음 문서 중 하나를 참조하세요.
    
    * [Linux, Unix, OS X 및 Windows 10의 Bash의 HDInsight에서 Linux 기반 Hadoop과 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Windows의 HDInsight에서 Linux 기반 Hadoop과 SSH(PuTTY) 사용](hdinsight-hadoop-linux-use-ssh-windows.md)

* SCP 클라이언트입니다. `scp` 명령은 Windows 10의 Bash를 포함한 모든 Linux, Unix 및 OS X 시스템에서 제공됩니다 `scp` 명령이 포함되지 않은 Windows 시스템의 경우 PSCP를 권장합니다. PSCP는 [PuTTY 다운로드 페이지](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)에서 사용할 수 있습니다.

## <a name="understanding-the-example"></a>예제 이해

[hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) 예제는 두 토폴로지를 포함합니다.

**com.microsoft.example.EventHubWriter** 는 임의 데이터를 Azure 이벤트 허브에 기록합니다. 데이터는 Spout에서 생성되는 임의의 장치 ID 및 장치 값입니다. 따라서 문자열 ID 및 숫자 값을 내보내는 하드웨어 일부를 시뮬레이션 중입니다.

**com.microsoft.example.EventHubReader**는 Event Hub의 데이터를 읽고 /devicedata 디렉터리의 클러스터 기본 저장소에 저장합니다.

데이터는 이벤트 허브에 기록되기 전에 JSON 문서로 형식이 지정되고 판독기에서 읽을 때 JSON에서 튜플로 구문이 분석됩니다. JSON 형식은 다음과 같습니다.

    { "deviceId": "unique identifier", "deviceValue": some value }

### <a name="project-configuration"></a>프로젝트 구성

**POM.xml** 파일은 이 Maven 프로젝트에 대한 구성 정보를 포함합니다. 흥미로운 부분은 다음과 같습니다.

#### <a name="the-eventhubs-storm-spout-dependency"></a>EventHubs Storm Spout 종속성

```xml
<dependency>
    <groupId>com.microsoft</groupId>
    <artifactId>eventhubs</artifactId>
    <version>1.0.2</version>
</dependency>
```

이 xml은 eventhubs 패키지에 대한 종속성을 추가하며 이는 Event Hubs에서 읽기의 경우 Spout 및 쓰기의 경우 Bolt를 모두 포함합니다.

> [!NOTE]
> 이 문서의 뒷부분에서 이 패키지를 설치합니다.

#### <a name="the-hdfsbolt-and-wasb-components"></a>HdfsBolt 및 WASB 구성 요소

HdfsBolt는 일반적으로 Hadoop 분산 파일 시스템 HDFS에 데이터를 저장하는 데 사용됩니다. 그러나 HDInsight 클러스터는 Azure 저장소 (WASB)의 기본 데이터 저장소로 사용하므로 HdfsBolt가 WASB 파일 시스템을 이해할 수 있는 여러 구성 요소를 로드해야 합니다.

```xml
<!--HdfsBolt stuff -->
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-hdfs</artifactId>
    <exclusions>
    <exclusion>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-client</artifactId>
    </exclusion>
    <exclusion>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-hdfs</artifactId>
    </exclusion>
    </exclusions>
    <version>${storm.version}</version>
</dependency>
<!--So HdfsBolt knows how to talk to WASB -->
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-client</artifactId>
    <version>${hadoop.version}</version>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-hdfs</artifactId>
    <version>${hadoop.version}</version>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-azure</artifactId>
    <version>${hadoop.version}</version>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-common</artifactId>
    <version>${hadoop.version}</version>
    <exclusions>
    <exclusion>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-log4j12</artifactId>
    </exclusion>
    </exclusions>
</dependency>
```

> [!NOTE]
> 이전 버전의 Hdinsight(예: 버전 3.2)를 사용하는 경우 이러한 구성 요소를 수동으로 등록해야 합니다. 예제는 예제 리포지토리의 [Storm v0.9.3](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub/tree/Storm_v0.9.3) 분기를 참조하세요.

#### <a name="the-maven-compiler-plugin"></a>maven-compiler-plugin

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>2.3.2</version>
    <configuration>
    <source>1.8</source>
    <target>1.8</target>
    </configuration>
</plugin>
```

Java 8에 대한 출력을 생성하는 프로젝트를 구성하며 HDInsight 3.5에서 사용됩니다.

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
    <!-- Keep us from getting errors when trying to use WASB from the storm-hdfs bolt -->
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

uber jar에 출력을 패키지하는 솔루션을 구성합니다. jar은 프로젝트 코드와 필수 종속성을 모두 포함합니다. 다음에 사용됩니다.

* 종속성에 대한 라이선스 파일의 이름을 바꿉니다.
* 보안/서명을 제외합니다.
* 동일한 인터페이스의 여러 구현이 하나의 항목으로 병합됩니다.

이러한 구성 설정은 런타임 시 오류를 방지합니다.

#### <a name="the-exec-maven-plugin"></a>exec-maven-plugin

```xml
<plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>exec-maven-plugin</artifactId>
    <version>1.2.1</version>
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
```

이 구성을 사용하면 사용자의 개발 환경에서 토폴로지를 로컬로 쉽게 실행할 수 있습니다. 다음 구문을 사용하여 로컬에서 토폴로지를 실행할 수 있습니다.

    mvn compile exec:java -Dstorm.topology=<CLASSNAME>

예: `mvn compile exec:java -Dstorm.topology=com.microsoft.example.EventHubWriter`.

#### <a name="the-resources-section"></a>리소스 섹션

```xml
<resources>
    <resource>
    <directory>${basedir}/conf</directory>
    <filtering>false</filtering>
    <includes>
        <include>EventHubs.properties</include>
    </includes>
    </resource>
</resources>
```

이 구성은 프로젝트에 포함된 Java가 아닌 리소스를 정의합니다. 예를 들어, **EventHubs.properties** 파일은 Azure Event Hub에 연결하는 데 사용되는 정보를 포함합니다.

## <a name="configure-environment-variables"></a>환경 변수 구성

Java 및 JDK를 설치할 때 사용자의 개발 워크스테이션에 다음 환경 변수를 설정할 수 있습니다. 하지만 변수가 존재하며 시스템에 대한 올바른 값을 포함하는지 확인해야 합니다.

* **JAVA_HOME** - JRE(Java runtime environment)가 설치된 디렉터리를 가리켜야 합니다. 예를 들어 Unix 또는 Linux 배포에서는 `/usr/lib/jvm/java-7-oracle`과 유사한 값이어야 합니다. Windows에서는 `c:\Program Files (x86)\Java\jre1.7`
* **PATH** - 다음 경로를 포함해야 합니다.
  
  * **JAVA_HOME** 또는 그와 동등한 경로
  * **JAVA_HOME\bin** 또는 그와 동등한 경로
  * Maven이 설치된 디렉터리

## <a name="download-and-register-the-eventhub-components"></a>Event Hub 구성 요소 다운로드 및 등록

1. [https://000aarperiscus.blob.core.windows.net/certs/storm-eventhubs-1.0.2-jar-with-dependencies.jar](https://000aarperiscus.blob.core.windows.net/certs/storm-eventhubs-1.0.2-jar-with-dependencies.jar)에서 `storm-eventhubs-1.0.2-jar-with-dependencies.jar`을 다운로드합니다. 이 파일은 Event Hubs의 읽기와 쓰기에 대한 spout 및 bolt 구성 요소를 포함합니다.

2. 다음 명령을 사용하여 로컬 maven 리포지토리에 있는 구성 요소를 등록합니다.
    
        mvn install:install-file -Dfile=storm-eventhubs-1.0.2-jar-with-dependencies.jar -DgroupId=com.microsoft -DartifactId=eventhubs -Dversion=1.0.2 -Dpackaging=jar
    
    `-Dfile=` 매개 변수를 수정하여 다운로드한 파일 위치를 가리키도록 합니다.

    이 명령은 로컬 Maven 리포지토리에 파일을 설치하고 여기서 Maven에 의한 컴파일 시점을 찾을 수 있습니다.

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

2. 다음을 사용하여 프로젝트를 빌드하고 패키징합니다.
   
        mvn package
   
    이 명령은 필수 종속성을 다운로드하고 프로젝트를 빌드한 다음 패키징합니다. 출력은 **/target** 디렉터리에 **EventHubExample-1.0-SNAPSHOT.jar**로 저장됩니다.

## <a name="deploy-the-topologies"></a>토폴로지 배포

이 프로젝트에서 만든 jar는 **com.microsoft.example.EventHubWriter** 및 **com.microsoft.example.EventHubReader**라는 같은 두 개의 토폴로지를 포함합니다. EventHubReader에서 읽은 이벤트 허브에 이벤트를 쓰기 때문에 EventHubWriter 토폴로지를 먼저 시작해야 합니다.

1. SCP를 사용하여 HDInsight 클러스터에 jar 패키지를 복사합니다. 클러스터에 SSH 사용자로 사용자 이름을 바꿉니다. CLUSTERNAME을 HDInsight 클러스터의 이름으로 바꿉니다.
   
        scp ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.
   
    SSH 계정에 암호를 사용한 경우 암호를 입력하라는 메시지가 나타납니다. 계정에서 SSH 키를 사용한 경우 `-i` 매개 변수를 사용하여 키 파일에 대한 경로를 지정해야 할 수 있습니다. 예: `scp -i ~/.ssh/id_rsa ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`.
   
   > [!NOTE]
   > 클라이언트가 Windows 워크스테이션이면 설치된 SCP 명령이 없을 수 있습니다. [PuTTY 다운로드 페이지](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)에서 다운로드할 수 있는 PSCP를 사용하는 것이 좋습니다.
   
    이 명령은 클러스터에 있는 SSH 사용자의 홈 디렉터리에 파일을 복사합니다.

2. 파일 업로드가 완료되면 SSH를 사용하여 HDInsight 클러스터에 연결할 수 있습니다. **USERNAME**을 SSH 로그인의 이름으로 바꿉니다. **CLUSTERNAME** 을 HDInsight 클러스터 이름으로 바꿉니다.
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
   > [!NOTE]
   > SSH 계정에 암호를 사용한 경우 암호를 입력하라는 메시지가 나타납니다. 계정에서 SSH 키를 사용한 경우 `-i` 매개 변수를 사용하여 키 파일에 대한 경로를 지정해야 할 수 있습니다. 다음 예제에서는 `~/.ssh/id_rsa`에서 개인 키를 로드합니다.
   > 
   > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    PuTTY를 사용하는 경우 **호스트 이름(또는 IP 주소)** 필드에 `CLUSTERNAME-ssh.azurehdinsight.net`를 입력한 다음 **열기**를 클릭하여 연결합니다. SSH 계정 이름을 입력하라는 메시지가 표시됩니다.
   
   > [!NOTE]
   > SSH 계정에 암호를 사용한 경우 암호를 입력하라는 메시지가 나타납니다. 계정으로 SSH 키를 사용하는 경우 다음 단계를 사용하여 키를 선택해야 합니다.
   > 
   > 1. **Category**에서 **Connection**, **SSH**를 차례로 확장하고 **Auth**를 선택합니다.
   > 2. **찾아보기** 를 클릭하고 개인 키가 포함된 .ppk 파일을 선택합니다.
   > 3. **열기** 를 클릭하여 연결합니다.

3. 다음 명령을 사용하여 토폴로지를 시작합니다.
   
        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubWriter writer
        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubReader reader
   
    이러한 명령은 "읽기 권한자"와 "쓰기 권한자"의 이름을 사용하여 토폴로지를 시작합니다.

4. 데이터를 생성하기 위해 잠시 토폴로지를 기다립니다. 다음 명령을 사용하여 HDInsight 저장소에 데이터가 기록되어 있는지 확인합니다.
   
        hadoop fs -ls /devicedata
   
    이 명령은 다음 텍스트와 유사한 파일 목록을 반환합니다.
   
        -rw-r--r--   1 storm supergroup      10283 2015-08-11 19:35 /devicedata/wasbbolt-14-0-1439321744110.txt
        -rw-r--r--   1 storm supergroup      10277 2015-08-11 19:35 /devicedata/wasbbolt-14-1-1439321748237.txt
        -rw-r--r--   1 storm supergroup      10280 2015-08-11 19:36 /devicedata/wasbbolt-14-10-1439321760398.txt
        -rw-r--r--   1 storm supergroup      10267 2015-08-11 19:36 /devicedata/wasbbolt-14-11-1439321761090.txt
        -rw-r--r--   1 storm supergroup      10259 2015-08-11 19:36 /devicedata/wasbbolt-14-12-1439321762679.txt
   
   > [!NOTE]
   > EventHubReader에서 만들어진 대로 일부 파일의 크기는 0이지만 아직 해당 파일에 데이터가 저장되지 않았습니다.
   
    다음 명령을 사용하여 파일의 내용을 볼 수 있습니다.
   
        hadoop fs -text /devicedata/*.txt
   
    여기서는 다음 텍스트와 비슷한 데이터를 반환합니다.
   
        3409e622-c85d-4d64-8622-af45e30bf774,848981614
        c3305f7e-6948-4cce-89b0-d9fbc2330c36,-1638780537
        788b9796-e2ab-49c4-91e3-bc5b6af1f07e,-1662107246
        6403df8a-6495-402f-bca0-3244be67f225,275738503
        d7c7f96c-581a-45b1-b66c-e32de6d47fce,543829859
        9a692795-e6aa-4946-98c1-2de381b37593,1857409996
        3c8d199b-0003-4a79-8d03-24e13bde7086,-1271260574
   
    첫 번째 열은 장치 ID 값을 포함하고 두 번째 열은 장치 값입니다.

5. 다음 명령을 사용하여 토폴로지를 중지합니다.
   
        storm kill reader
        storm kill writer

## <a name="delete-your-cluster"></a>클러스터 삭제

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshooting"></a>문제 해결

/devicedata 디렉터리에서 파일이 표시되지 않으면 Storm UI를 사용하여 토폴로지에서 반환한 오류를 찾아봅니다.

Storm UI 사용에 대한 자세한 내용은 다음 항목을 참조하세요.

* HDInsight 클러스터에서 **Linux 기반** Storm을 사용하고 있다면 [Deploy and manage Apache Storm topologies on Linux 기반 HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

* HDInsight 클러스터에서 **Windows 기반** Storm을 사용하고 있다면 [Deploy and manage Apache Storm topologies on Windows 기반 HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

## <a name="next-steps"></a>다음 단계

* [HDInsight의 Storm에 대한 예제 토폴로지](hdinsight-storm-example-topology.md)


