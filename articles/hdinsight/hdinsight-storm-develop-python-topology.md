<properties
   pageTitle="HDinsight에서 Storm 토폴로지에 Python 구성 요소 사용 | Microsoft Azure"
   description="Azure HDInsight에서 Apache Storm과 함께 Python 구성 요소를 사용하는 방법에 대해 알아봅니다. Java 기반 및 Clojure 기반 Storm 토폴로지에서 Python 구성 요소를 사용하는 방법에 대해 알아보겠습니다."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="python"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/08/2015"
   ms.author="larryfr"/>

#HDInsight에서 Python을 사용하여 Apache Storm 토폴로지 개발

Apache Storm은 여러 언어를 지원하여 한 토폴로지에 여러 언어의 구성 요소를 결합할 수 있습니다. 이 문서에서는 HDInsight에서 Java 및 Clojure 기반 Storm 토폴로지에서 Python 구성 요소를 사용하는 방법을 배웁니다.

##필수 조건

* Python 2.7 이상

* Java JDK 1.7 이상

##Storm 다중 언어 지원

Storm은 모든 프로그래밍 언어로 작성된 구성 요소와 함께 작동하도록 설계되었지만 이를 위해서는 구성 요소에서 [Storm에 대한 Thrift 정의](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift)가 작동하는 방식을 이해해야 합니다. Python의 경우 모듈은 Apache Storm 프로젝트의 일부로 제공되므로 Storm과 쉽게 인터페이스할 수 있습니다. 이 모듈을 [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py)에서 찾을 수 있습니다.

Apache Storm은 JVM(Java Virtual Machine)에서 실행되는 Java 프로세스이므로 다른 언어로 작성된 구성 요소는 하위 프로세스로 실행됩니다. JVM에서 실행되는 Storm 비트는 stdin/stdout을 통해 전송되는 JSON 메시지를 사용하여 이러한 하위 프로세스와 통신합니다. 구성 요소 간의 통신에 대한 자세한 내용은 [다중 언어 프로토콜](https://storm.apache.org/documentation/Multilang-protocol.html)(영문) 설명서에서 확인할 수 있습니다.

###Storm 모듈

Storm 모듈(https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py,)은 Storm과 함께 작동하는 Python 구성 요소를 만드는 데 필요한 비트를 제공합니다.

`storm.emit`를 제공하여 튜플을 내보내고 `storm.logInfo`를 제공하여 로그에 기록합니다. 이 파일을 검토하여 제공하는 내용을 확인해보세요.

##과제

__storm.py__ 모듈을 사용하면 데이터를 사용하는 Python Spout와 데이터를 처리하는 Bolt를 만들 수 있습니다. 하지만 구성 요소 간 통신을 연결하는 전반적인 Storm 토폴로지 정의는 여전히 Java 또는 Clojure로 작성됩니다. 또한 Java를 사용하면 Python 구성 요소에 대한 인터페이스 역할을 하는 Java 구성 요소도 만들어야 합니다.

또한 Storm 클러스터는 분산된 형태로 실행되므로 Python 구성 요소에 필요한 모든 모듈이 클러스터의 모든 작업자 노드에서 사용 가능한지 확인해야 합니다. Storm은 다중 언어 리소스에 대해 이 작업을 수행하는 간단한 방법을 제공하지 않으므로 토폴로지에 대한 jar 파일의 일부로 모든 종속성을 포함하거나 클러스터의 각 작업자 노드에 종속성을 직접 설치해야 합니다.

[Pyleus](https://github.com/Yelp/pyleus) 및 [Streamparse](https://github.com/Parsely/streamparse)와 같은 일부 프로젝트에는 이러한 단점을 극복하기 위한 시도가 있습니다. 둘 다 Linux 기반 HDInsight 클러스터에서 실행할 수 있지만 클러스터 설치 중에 사용자 지정이 필요하며 HDInsight 클러스터에서 완전히 테스트되지 않았으므로 이 문서의 논점을 벗어납니다. HDInsight와 함께 이러한 프레임워크 사용에 대한 참고 사항은 이 문서의 끝부분에 포함됩니다.

###Java 및 Clojure 토폴로지 정의

토폴로지를 정의하는 두 방법 중에서 Clojure는 토폴로지 정의에서 python 구성 요소를 직접 참조할 수 있으므로 가장 쉽고/명확한 방법입니다. Java 기반 토폴로지 정의인 경우, Python 구성 요소에서 반환되는 튜플에 필드 선언 등을 처리하는 Java 구성 요소도 정의해야 합니다.

두 방법 모두 이 문서에서 예제 프로젝트와 함께 설명됩니다.

##Java 토폴로지를 포함하는 Python 구성 요소

> [AZURE.NOTE]이 예제는 __JavaTopology__ 디렉터리의 https://github.com/Blackmist/hdinsight-python-storm-wordcount에서 사용할 수 있습니다. Maven 기반 프로젝트입니다. Maven에 대해 잘 모르는 경우 Storm 토폴로지에 대한 Maven 프로젝트를 만드는 방법에 대한 자세한 내용은 [HDInsight에서 Apache Storm으로 Java 기반 토폴로지 개발](hdinsight-storm-develop-java-topology.md)을 참조하세요.

Python(또는 기타 JVM 언어 구성 요소)을 사용하는 Java 기반 토폴로지는 Java 구성 요소를 사용하는 것으로 처음에 나타나지만 각 Java Spout/Bolt를 들여다 보면 다음과 유사한 코드가 표시됩니다.

    public SplitBolt() {
        super("python", "countbolt.py");
    }

여기서 Java는 Python을 호출하고 실제 Bolt 논리가 포함된 스크립트를 실행합니다. 이 예에서 Java Spout/Bolt는 기본 Python 구성 요소가 내보내는 튜플에 필드를 선언하기만 합니다.

실제 Python 파일은 이 예에서 `/multilang/resources` 디렉터리에 저장됩니다. `/multilang` 디렉터리는 __pom.xml__에서 참조됩니다.

<resources> <resource> <!-- Where the Python bits are kept --> <directory>${basedir}/multilang</directory> </resource> </resources>

여기에는 이 프로젝트에서 작성될 jar의 `/multilang` 폴더에 있는 모든 파일이 포함됩니다.

> [AZURE.IMPORTANT]여기서는 `/multilang/resources`가 아닌 `/multilang` 디렉터리만 지정합니다. Storm은 `resources` 디렉터리에서 비JVM 리소스를 필요로 하므로 내부적으로 기존 항목을 검색합니다. 이 폴더에 구성 요소를 배치하면 Java 코드에서 이름만으로도 참조할 수 있습니다. 예: `super("python", "countbolt.py");` 이에 대한 다른 생각은 다중 언어 리소스에 액세스할 때 `resources` 디렉터리를 루트(/)로 여긴다는 점입니다.
>
> 이 예제 프로젝트에서 `storm.py` 모듈은 `/multilang/resources` 디렉터리에 포함됩니다.

###프로젝트 빌드 및 실행

이 프로젝트를 로컬로 실행하려면 다음 Maven 명령을 사용하여 로컬 모드에서 빌드 및 실행합니다.

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCount

Ctrl + c를 사용하여 해당 프로세스를 중지합니다.

Apache Storm을 실행하는 HDInsight 클러스터에\\로 프로젝트를 배포하려면 다음 단계를 수행합니다.

1. uber jar을 빌드합니다.

        mvn package

    그러면 __WordCount--1.0-SNAPSHOT.jar__이라는 파일이 이 프로젝트에 대한 `/target` 디렉터리에 만들어집니다.

2. 다음 방법 중 하나를 사용하여 Hadoop 클러스터에 jar 파일을 업로드합니다.

    * __Linux 기반__ HDInsight 클러스터: `scp WordCount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:WordCount-1.0-SNAPSHOT.jar`을 사용하여 jar 파일을 클러스터로 복사합니다. 이때 USERNAME은 SSH 사용자 이름으로, CLUSTERNAME은 HDInsight 클러스터 이름으로 바꿉니다.

        파일 업로드가 완료되면 SSH를 사용하여 클러스터에 연결하고 `storm jar WordCount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount wordcount`를 사용하여 토폴로지를 시작합니다.

    * __Windows 기반__ HDInsight 클러스터: 브라우저에서 HTTPS://CLUSTERNAME.azurehdinsight.net/으로 이동하여 Storm 대시보드에 연결합니다. CLUSTERNAME을 HDInsight 클러스터 이름으로 바꾸고 메시지가 표시되면 관리자 이름 및 암호를 제공합니다.

        양식을 사용하여 다음 작업을 수행합니다.

        * __Jar 파일__: __찾아보기__를 선택한 후 __WordCount-1.0-SNAPSHOT.jar__을 선택합니다.
        * __클래스 이름__: `com.microsoft.example.WordCount`를 입력합니다.
        * __추가 매개 변수__: `wordcount`와 같은 토폴로지를 식별하는 친숙한 이름을 입력합니다.

        마지막으로, __제출__을 선택하여 토폴로지를 시작합니다.

> [AZURE.NOTE]토폴로지가 시작되면 Storm 토폴로지가 중지될 때까지 실행됩니다. 토폴로지를 중지하려면 명령줄(예를 들어 Linux 클러스터에 대한 SSH 세션)에서 `storm kill TOPOLOGYNAME` 명령을 사용하거나 Storm UI를 사용하여 토폴로지를 선택한 후 __중지__ 단추를 선택합니다.

##Clojure 토폴로지를 포함하는 Python 구성 요소

> [AZURE.NOTE]이 예제는 __ClojureTopology__ 디렉터리의 https://github.com/Blackmist/hdinsight-python-storm-wordcount에서 사용할 수 있습니다.

이 토폴로지는 [새 Clojure 프로젝트를 만드는](https://github.com/technomancy/leiningen/blob/stable/doc/TUTORIAL.md#creating-a-project) [Leiningen](http://leiningen.org)을 사용하여 만듭니다. 이후, 스캐폴드 프로젝트에 수정된 내용은 다음과 같습니다.

* __project.clj__: Storm에 대한 종속성과 HDInsight 서버에 배포할 때 문제를 일으킬 수 있는 항목에 대한 제외 사항이 추가되었습니다.
* __resources/resources__: Leiningen에서 기본 `resources` 디렉터리를 생성하지만 여기에 저장된 파일이 이 프로젝트에서 생성된 jar 파일의 루트에 추가된 것으로 나타나며 Storm은 `resources`라는 하위 디렉터리의 파일을 필요로 합니다. 따라서 하위 디렉터리가 추가되고 Python 파일이 `resources/resources`에 저장됩니다. 런타임 시 이 디렉터리는 Python 구성 요소에 액세스하기 위한 루트(/)로 처리됩니다.
* __src/wordcount/core.clj__: 이 파일은 토폴로지 정의를 포함하며 __project.clj__ 파일에서 참조됩니다. Clojure를 사용하여 Storm 토폴로지를 정의하는 방법에 대한 자세한 내용은 [Clojure DSL](https://storm.apache.org/documentation/Clojure-DSL.html)을 참조하세요.

###프로젝트 빌드 및 실행

__프로젝트를 로컬로 빌드 및 실행하려면__ 다음 명령을 사용합니다.

    lein do clean, run

토폴로지를 중지하려면 __Ctrl+C__를 사용합니다.

__uberjar을 빌드하고 HDInsight에 배포하려면__ 다음 단계를 수행합니다.

1. 토폴로지 및 필요한 종속성을 포함하는 uberjar을 만듭니다.

        lein uberjar

    `target\uberjar+uberjar` 디렉터리에 `wordcount-1.0-SNAPSHOT.jar`이라는 새 파일이 생성됩니다.
    
2. 다음 방법 중 하나를 사용하여 토폴로지를 HDInsight 클러스터에 배포하고 실행합니다.

    * __Linux 기반 HDInsight__
    
        1. `scp`를 사용하여 파일을 HDInsight 클러스터 헤드 노드에 복사합니다. 예:
        
                scp wordcount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:wordcount-1.0-SNAPSHOT.jar
                
            USERNAME을 클러스터에 대한 SSH 사용자로 바꾸고 CLUSTERNAME을 HDInsight 클러스터의 이름으로 바꿉니다.
            
        2. 파일이 클러스터에 복사된 후에는 SSH를 사용하여 클러스터에 연결하고 작업을 제출합니다. HDInsight에서 SSH 사용에 대한 자세한 내용은 다음을 참조하세요.
        
            * [Linux, Unix 또는 OS X에서 Linux 기반 HDInsight와 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)
            * [Windows에서 Linux 기반 HDInsight와 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)
            
        3. 연결되면 다음을 사용하여 토폴로지를 시작합니다.
        
                storm jar wordcount-1.0-SNAPSHOT.jar wordcount.core wordcount
    
    * __Windows 기반 HDInsight__
    
        1. 브라우저에서 HTTPS://CLUSTERNAME.azurehdinsight.net/으로 이동하여 Storm 대시보드에 연결합니다. CLUSTERNAME을 HDInsight 클러스터 이름으로 바꾸고 메시지가 표시되면 관리자 이름 및 암호를 제공합니다.

        2. 양식을 사용하여 다음 작업을 수행합니다.

            * __Jar 파일__: __찾아보기__를 선택한 후 __wordcount-1.0-SNAPSHOT.jar__을 선택합니다.
            * __클래스 이름__: `wordcount.core`를 입력합니다.
            * __추가 매개 변수__: `wordcount`와 같은 토폴로지를 식별하는 친숙한 이름을 입력합니다.

            마지막으로, __제출__을 선택하여 토폴로지를 시작합니다.

> [AZURE.NOTE]토폴로지가 시작되면 Storm 토폴로지가 중지될 때까지 실행됩니다. 토폴로지를 중지하려면 명령줄(Linux 클러스터에 대한 SSH 세션)에서 `storm kill TOPOLOGYNAME` 명령을 사용하거나 Storm UI를 사용하여 토폴로지를 선택한 후 __중지__ 단추를 선택합니다.

##Pyleus 프레임워크

[Pyleus](https://github.com/Yelp/pyleus)는 다음을 제공하여 Storm과 Python을 쉽게 사용할 수 있도록 해주는 프레임워크입니다.

* __YAML 기반 토폴로지 정의__: Java 또는 Clojure에 대한 지식 없이도 토폴로지를 정의할 수 있는 편리한 방법을 제공합니다.
* __MessagePack 기반 직렬 변환기__: MessagePack은 JSON 대신 기본 직렬화로 사용됩니다. 구성 요소 간 메시징이 빨라질 수 있습니다.
* __종속성 관리__: Virtualenv는 모든 작업자 노드에 배포되는 Python 종속성을 확인하는 데 사용됩니다. 이를 위해서는 작업자 노드에 Virtualenv가 설치되어 있어야 합니다.

> [AZURE.IMPORTANT]Pyleus는 개발 환경에서 Storm을 필요로 합니다. 기본 Apache Storm 0.9.3 배포를 사용할 경우 HDInsight와 함께 제공된 Storm 버전과 호환되지 않는 jar이 발생할 수 있습니다. 따라서 다음 단계에서는 개발 환경으로 HDInsight 클러스터를 사용합니다.

빌드 환경으로 HDInsight 헤드 노드를 사용하여 예제 Pyleus 토폴로지를 성공적으로 빌드할 수 있습니다.

1. HDInsight 클러스터에서 새 Storm을 프로비전하는 경우 클러스터 노드에 Python Virtualenv가 있는지 확인해야 합니다. 새 Linux 기반 HDInsight 클러스터를 만들 때는 [클러스터 사용자 지정](hdinsight-hadoop-customize-cluster.md)을 통해 다음 스크립트 동작 설정을 사용합니다.

    * __이름__: 여기에 친숙한 이름을 제공합니다.
    * \_\_ 스크립트 URI\_\_: 값으로 `https://hditutorialdata.blob.core.windows.net/customizecluster/pythonvirtualenv.sh`를 사용합니다. 이 스크립트는 노드에 Python Virtualenv를 설치합니다.
    
        > [AZURE.NOTE]이 문서의 뒷부분에 나오는 Streamparse 프레임워크에 사용되는 일부 디렉터리도 만들어집니다.
        
    * __Nimbus__: 이 항목을 선택하여 스크립트가 Nimbus(헤드) 노드에 적용되도록 합니다.
    * __Supervisor__: 이 항목을 선택하여 스크립트가 supervisor(작업자) 노드에 적용되도록 합니다.
    
    다른 항목은 비워둡니다.

1. 클러스터가 만들어졌으면 SSH를 사용하여 연결합니다.

    * [Linux, Unix 또는 OS X에서 Linux 기반 HDInsight와 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Windows에서 Linux 기반 HDInsight와 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)

2. SSH 연결에서 다음을 사용하여 새 가상 환경을 만들고 Pyleus를 설치합니다.

        virtualenv pyleus_venv
        source pyleus_venv
        pip install pyleus

3. 다음으로 Pyleus git 리포지토리를 다운로드하고 WordCount 예제를 빌드합니다.

        sudo apt-get install git
        git clone https://github.com/Yelp/pyleus.git
        pyleus build pyleus/examples/word_count/pyleus_topology.yaml
    
    빌드가 완료되면 현재 디렉터리에 `word_count.jar`이라는 새 파일이 생깁니다.
    
4. 토폴로지를 Storm 클러스터에 제출하려면 다음 명령을 사용합니다.

        pyleus submit -n localhost word_count.jar
    
    `-n` 매개 변수는 Nimbus 호스트를 지정합니다. 헤드 노드에 있으므로 `localhost`를 사용할 수 있습니다.
    
    `pyleus` 명령을 사용하여 다른 Storm 작업을 수행할 수도 있습니다. 다음을 사용하여 실행 중인 토폴로지를 나열한 후 `word_count` 토폴로지를 중지합니다.
    
        pyleus list -n localhost
        pyleus kill -n localhost word_count

##Streamparse 프레임워크

[Streamparse](https://github.com/Parsely/streamparse)는 다음을 제공하여 Storm과 Python을 쉽게 사용할 수 있도록 해주는 프레임워크입니다.

* __스캐폴딩__: 프로젝트에 대한 스캐폴딩을 쉽게 만든 후 파일을 수정하여 논리를 추가할 수 있습니다.
* __Clojure DSL 함수__: Clojure 토폴로지 정의에서 Python 구성 요소 사용 시 번거로운 작업을 줄여줍니다.
* __종속성 관리__: Virtualenv는 모든 작업자 노드에 배포되는 Python 종속성을 확인하는 데 사용됩니다. 이를 위해서는 작업자 노드에 Virtualenv가 설치되어 있어야 합니다.
* __원격 배포__: Streamparse는 SSH 자동화를 사용하여 구성 요소를 작업자 노드에 배포할 수 있으며 Nimbus와 통신하는 SSH 터널을 만들 수 있습니다. 따라서 개발 환경에서 HDInsight와 같은 Linux 기반 클러스터에 쉽게 배포할 수 있습니다.

> [AZURE.IMPORTANT]Streamparse는 Windows에서는 사용할 수 없는 [Unix 신호](https://en.wikipedia.org/wiki/Unix_signal)를 필요로 하는 구성 요소를 기반으로 합니다. 개발 환경은 Linux, Unix, 또는 OS X여야 하며 HDInsight 클러스터는 Linux 기반이어야 합니다.

1. HDInsight 클러스터에서 새 Storm을 프로비전하는 경우 클러스터 노드에 Python Virtualenv가 있는지 확인해야 합니다. 새 Linux 기반 HDInsight 클러스터를 만들 때는 [클러스터 사용자 지정](hdinsight-hadoop-customize-cluster.md)을 통해 다음 스크립트 동작 설정을 사용합니다.

    * __이름__: 여기에 친숙한 이름을 제공합니다.
    * \_\_ 스크립트 URI\_\_: 값으로 `https://hditutorialdata.blob.core.windows.net/customizecluster/pythonvirtualenv.sh`를 사용합니다. 이 스크립트는 노드에 Python Virtualenv를 설치하고 Streamparse에서 사용하는 디렉터리를 생성합니다.
    * __Nimbus__: 이 항목을 선택하여 스크립트가 Nimbus(헤드) 노드에 적용되도록 합니다.
    * __Supervisor__: 이 항목을 선택하여 스크립트가 supervisor(작업자) 노드에 적용되도록 합니다.
    
    다른 항목은 비워둡니다.
    
    > [AZURE.WARNING]또한 Streamparse를 사용하여 원격으로 배포하려면 __공개 키__를 사용하여 HDInsight 클러스터에 대한 SSH 사용자를 보호해야 합니다.
    >
    > HDInsight에서 SSH와 함께 키 사용에 대한 자세한 내용은 다음 문서 중 하나를 참조하세요.
    >
    > * [Linux, Unix 또는 OS X에서 Linux 기반 HDInsight와 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)
    > * [Windows에서 Linux 기반 HDInsight와 SSH 사용](hdinsight-hadoop-linux-use-ssh-windows.md)

2. 클러스터가 프로비전되는 동안 다음 명령을 사용하여 개발 환경에 Streamparse를 설치합니다.

        pip install streamparse
        
3. Streamparse 설치가 완료되면 다음 명령을 사용하여 예제 프로젝트를 만듭니다.

        sparse quickstart wordcount
        
    그러면 `wordcount`라는 새 디렉터리가 생성되고 Word Count 예제 프로젝트로 채워집니다.

4. 디렉터리를 `wordcount` 디렉터리로 변경하고 로컬 모드에서 토폴로지를 시작합니다.

        cd wordcount
        sparse run

    Ctrl+C를 사용하여 토폴로지를 중지합니다.

###토폴로지 배포

Linux 기반 HDInsight 클러스터가 만들어졌으면 다음 단계에 따라 토폴로지를 클러스터에 배포합니다.

1. 다음 명령을 사용하여 클러스터에 대한 작업자 노드의 정규화된 도메인 이름을 찾습니다.

        curl -u admin:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts | grep '"host_name" : "worker'
    
    그러면 클러스터에 대한 호스트 정보를 검색하여 grep로 파이프하고 작업자 노드에 대한 항목을 반환합니다. 다음과 유사한 결과가 표시됩니다.
    
        "host_name" : "workernode0.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net"
    
    다음 단계에서 사용되므로 `"workernode0.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net"` 정보를 저장합니다.

2. `wordcount` 디렉터리에서 __config.json__ 파일을 열고 다음 항목을 변경합니다.

    * __user__: HDInsight 클러스터에 대해 구성한 SSH 사용자 계정 이름으로 설정합니다. 프로젝트를 배포할 때 클러스터에 대한 인증에 사용됩니다.
    * __nimbus__: `CLUSTERNAME-ssh.azurehdinsight.net`으로 설정합니다. CLUSTERNAME은 클러스터의 이름으로 바꿉니다. 클러스터의 헤드 노드인 Nimbus 노드와 통신할 때 사용됩니다.
    * __workers__: curl을 사용하여 검색한 작업자 노드에 대한 호스트 이름으로 작업자 항목을 채웁니다. 예:
    
        ```
"workers": [
    "workernode0.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net",
    "workernode1.1kft5e4nx2tevg5b2pdwxqx1fb.jx.internal.cloudapp.net"
    ]
        ```
    
    * __virtualenv\_root__: "/virtualenv"로 설정합니다.
    
    그러면 스크립트 동작으로 프로비전하는 동안 생성된 `/virtualenv` 디렉터리를 포함하여HDInsight 클러스터에 대한 프로젝트가 구성됩니다.

4. HDInsight에 배포된 Streamparse는 헤드 노드를 통해 작업자에 인증을 전달해야 하므로 워크스테이션에서 `ssh-agent`를 시작해야 합니다. 대부분의 운영 체제에서 이 작업은 자동으로 시작됩니다. 다음 명령을 사용하여 실행 중인지 확인합니다.

        echo "$SSH_AUTH_SOCK"
    
    `ssh-agent`가 실행 중이면 다음과 유사한 응답이 반환됩니다.
    
        /tmp/ssh-rfSUL1ldCldQ/agent.1792
    
    > [AZURE.NOTE]전체 경로는 운영 체제에 따라 달라질 수 있습니다. 예를 들어, OS X에서는 경로가 `/private/tmp/com.apple.launchd.vq2rfuxaso/Listeners`와 유사할 수 있습니다. 하지만 에이전트가 실행 중인 경우 일부 경로를 반환해야 합니다
    
    아무 것도 반환되지 않는 경우 `ssh-agent` 명령을 사용하여 에이전트를 시작합니다.
    
5. 에이전트가 HDInsight 서버 인증에 사용할 키에 대해 알고 있는지 확인합니다. 다음 명령을 사용하여 에이전트에 사용할 수 있는 키를 나열합니다.

        ssh-add -L
    
    그러면 에이전트에 추가된 개인 키가 반환됩니다. 이 결과를 HDInsight를 인증하기 위해 SSH 키를 만들 때 생성한 개인 키의 내용과 비교할 수 있습니다.
    
    반환되는 정보가 없거나 반환된 정보가 개인 키와 일치하지 않는 경우 다음을 사용하여 개인 키를 에이전트에 추가합니다.
    
        ssh-add /path/to/key/file
    
    예: `ssh-add ~/.ssh/id_rsa`.

4. 또한 HDInsight 클러스터에 전달이 사용된다는 것을 알 수 있도록 SSH를 구성해야 합니다. `~/.ssh/config`에 다음을 추가합니다. 이 파일이 없으면 만들고 내용으로 다음을 사용합니다.

        Host *.azurehdinsight.net
          ForwardAgent yes
        
        Host *.internal.cloudapp.net
          ProxyCommand ssh CLUSTERNAME-ssh.azurehdinsight.net nc %h %p
    
    CLUSTERNAME은 HDInsight 클러스터 이름을 바꿉니다.
    
    그러면 연결된 모든 *.azurehdinsight.net 시스템을 통해 SSH 자격 증명의 전달이 가능하도록 워크스테이션에서 SSH 에이전트가 구성됩니다. 이 경우 클러스터의 헤드 노드입니다. 다음으로, 헤드 노드에서 개별 작업자 노드(internal.cloudapp.net)로 SSH 트래픽을 프록시하는 데 사용된 명령을 구성합니다. 그러면 Streamparse는 SSH 계정에 대한 키 인증을 사용하여 헤드 노드에 연결한 후, 다시 각 작업자 노드로 연결할 수 있습니다.
    
5. 마지막으로, 다음 명령을 사용하여 로컬 개발 환경에서 HDInsight 클러스터로 토폴로지를 제출합니다.

        sparse submit
    
    그러면 HDInsight 클러스터에 연결되고 토폴로지 및 Python 종속성을 배포한 후 토폴로지를 시작합니다.

##다음 단계

이 문서에서는 Storm 토폴로지에서 Python 구성 요소를 사용하는 방법을 알아보았습니다. HDInsight와 함께 Python을 사용하는 다른 방법은 다음 문서를 참조하세요.

* [MapReduce 작업을 스트리밍하는 데 Python을 사용하는 방법](hdinsight-hadoop-streaming-python.md)
* [Pig 및 Hive에서 UDF(사용자 정의 함수)를 사용하는 방법](hdinsight-python.md)

<!---HONumber=Oct15_HO3-->