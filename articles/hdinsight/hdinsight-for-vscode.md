---
title: Azure HDInsight Tools - Hive, LLAP 또는 PySpark용 Visual Studio Code 사용 | Microsoft Docs
description: Azure HDInsight Tools for Visual Studio Code를 사용하여 쿼리와 스크립트를 만들고 제출하는 방법에 대해 알아봅니다.
Keywords: VS Code,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: ''
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/27/2017
ms.openlocfilehash: 23cbdb4d72a6b88563d9d2c2bd0e4e3c8223377f
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53723674"
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Azure HDInsight Tools for Visual Studio Code 사용

Azure HDInsight Tools for Visual Studio Code(VS Code)를 사용하여 Apache Hive 일괄 작업, Apache Spark용 대화형 Hive 쿼리 및 PySpark 스크립트를 만들고 제출하는 방법에 대해 알아봅니다. 먼저 VS Code에서 HDInsight 도구를 설치하는 방법을 설명한 다음, Hive 및 Spark에 작업을 제출하는 방법을 살펴보겠습니다. 

Azure HDInsight Tools는 Windows, Linux 및 macOS를 포함하여 VS Code에서 지원하는 플랫폼에 설치할 수 있습니다. 아래에서 다양한 플랫폼에 대한 필수 조건을 찾을 수 있습니다.


## <a name="prerequisites"></a>필수 조건

이 문서의 단계를 완료하려면 다음 항목이 필요합니다.

- HDInsight 클러스터. 클러스터를 만들려면 [HDInsight 시작](hadoop/apache-hadoop-linux-tutorial-get-started.md)을 참조하세요.
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono는 Linux 및 macOS에만 필요합니다.

## <a name="install-the-hdinsight-tools"></a>HDInsight Tools 설치
   
필수 구성 요소를 설치한 후 Azure HDInsight Tools for VS Code를 설치할 수 있습니다. 

### <a name="to-install-azure-hdinsight-tools"></a>Azure HDInsight Tools를 설치하려면

1. Visual Studio Code를 엽니다.

2. 왼쪽 창에서 **확장**을 선택합니다. 검색 상자에 **HDInsight**를 입력합니다.

3. **Azure HDInsight Tools** 옆에 있는 **설치**를 선택합니다. 몇 초 후에 **설치** 단추가 **다시 로드**로 변경됩니다.

4. **다시 로드**를 선택하여 **Azure HDInsight Tools** 확장을 활성화합니다.

5. **창 다시 로드**를 선택하여 확인합니다. **확장** 창에서 **Azure HDInsight Tools**를 확인할 수 있습니다.

   ![HDInsight for Visual Studio Code Python 설치](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

## <a name="open-hdinsight-workspace"></a>HDInsight 작업 영역 열기

Azure에 연결하려면 먼저 VS Code에서 작업 영역을 만듭니다.

### <a name="to-open-a-workspace"></a>작업 영역을 열려면

1. **파일** 메뉴에서 **폴더 열기**를 선택합니다. 그런 다음 기존 폴더를 작업 폴더로 지정하거나 새 작업 폴더를 만듭니다. 해당 폴더가 왼쪽 창에 나타납니다.

2. 왼쪽 창에서 작업 폴더 옆의 **새 파일** 아이콘을 선택합니다.

   ![새 파일](./media/hdinsight-for-vscode/new-file.png)

3. .hql(Hive 쿼리) 또는 .py(Spark 스크립트) 파일 확장명 중 하나를 사용하여 새 파일 이름을 지정합니다. 

## <a name="connect-to-hdinsight-cluster"></a>HDInsight 클러스터에 연결

VS Code에서 HDInsight 클러스터에 스크립트를 제출하려면 먼저 Azure 계정에 연결하거나 클러스터를 연결해야 합니다(Ambari 사용자 이름/비밀번호 또는 도메인 가입 계정 사용).

### <a name="to-connect-to-azure"></a>Azure에 연결하려면

1. 새 작업 폴더 및 새 스크립트 파일을 만듭니다(아직 없는 경우).

2. 스크립트 편집기를 마우스 오른쪽 단추로 클릭한 다음 상황에 맞는 메뉴에서 **HDInsight: Login**을 선택합니다. **Ctrl+Shift+P**를 누른 다음, **HDInsight: Login**을 입력할 수도 있습니다.

    ![HDInsight Tools for Visual Studio Code 로그인](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

3. 로그인하려면 **출력** 창의 로그인 지침을 따릅니다.
    + 전역 환경의 경우 HDInsight 로그인은 Azure 로그인 프로세스를 트리거합니다.

        ![Azure에 대한 로그인 지침](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin.png)

    + 기타 환경의 경우에는 로그인 지침을 따릅니다.

        ![기타 환경에 대한 로그인 지침](./media/hdinsight-for-vscode/hdi-azure-hdinsight-hdinsight-signin.png)

    일단 연결되면 Azure 계정 이름이 VS Code 창 왼쪽 하단 상태 표시줄에 표시됩니다. 

    > [!NOTE]  
    > 알려진 Azure 인증 문제 때문에 개인 모드 또는 incognito 모드에서 브라우저를 열어야 합니다. Azure 계정에서 두 가지 요소를 사용하도록 설정한 경우 PIN 인증 대신 전화 인증을 사용하는 것이 좋습니다.
  

4. 스크립트 편집기를 마우스 오른쪽 단추로 클릭하여 상황에 맞는 메뉴를 엽니다. 상황에 맞는 메뉴에서 다음 작업을 수행할 수 있습니다.

    - 로그아웃
    - 클러스터 나열
    - 기본 클러스터 설정
    - 대화형 Hive 쿼리 제출
    - Hive 배치 스크립트 제출
    - 대화형 PySpark 쿼리 제출
    - PySpark 배치 스크립트 제출
    - 구성 설정

<h3 id="linkcluster">클러스터를 연결하려면</h3>

[Apache Ambari](https://ambari.apache.org/) 관리형 사용자 이름을 사용하여 정상적인 클러스터를 연결하거나, 도메인 사용자 이름(예: user1@contoso.com)을 사용하여 Enterprise Security Pack 보안 Hadoop 클러스터를 연결할 수 있습니다.
1. **CTRL+SHIFT+P**를 선택하여 명령 팔레트를 연 다음, **HDInsight: Link a Cluster**를 입력합니다.

   ![클러스터 연결 명령](./media/hdinsight-for-vscode/link-cluster-command.png)

2. HDInsight 클러스터 URL 입력 -> 사용자 이름 입력 -> 암호 입력 -> 클러스터 유형 선택 -> 확인을 통과하면 성공 정보가 표시됩니다.
   
   ![클러스터 연결 대화 상자](./media/hdinsight-for-vscode/link-cluster-process.png)

   > [!NOTE]  
   > 클러스터가 Azure 구독 및 연결된 클러스터 모두에 로그인되어 있으면, 연결된 사용자 이름 및 암호가 사용됩니다. 
   
3. **List Cluster** 명령을 사용하여 연결된 클러스터를 볼 수 있습니다. 이제 연결된 클러스터에 스크립트를 제출할 수 있습니다.

   ![연결된 클러스터](./media/hdinsight-for-vscode/linked-cluster.png)

4. 명령 팔레트에서 **HDInsight: Unlink a Cluster**를 입력하여 클러스터의 연결을 끊을 수도 있습니다.


### <a name="to-link-a-generic-apache-livy-endpoint"></a>일반 Apache Livy 엔드포인트를 연결하려면

1. **CTRL+SHIFT+P**를 선택하여 명령 팔레트를 연 다음, **HDInsight: Link a Cluster**를 입력합니다.
2. **일반 Livy 엔드포인트**를 선택합니다.
3. 일반 Livy 엔드포인트(예: http://10.172.41.42:18080)를 입력합니다.
4. 일반 Livy 엔드포인트에 대한 권한 부여가 필요한 경우 **기본**을 선택하고, 그렇지 않으면 **없음**을 선택합니다.
5. 4단계에서 **기본**을 선택할 때 사용자 이름을 입력합니다.
6. 4단계에서 **기본**을 선택할 때 암호를 입력합니다.
7. 일반 Livy 엔드포인트가 연결되었습니다.

   ![연결된 일반 Livy 클러스터](./media/hdinsight-for-vscode/link-cluster-process-generic-livy.png)

## <a name="list-hdinsight-clusters"></a>HDInsight 클러스터 나열

연결을 테스트하기 위해 HDInsight 클러스터를 나열할 수 있습니다.

### <a name="to-list-hdinsight-clusters-under-your-azure-subscription"></a>Azure 구독의 HDInsight 클러스터를 나열하려면
1. 작업 영역을 열고 Azure에 연결합니다. 자세한 내용은 [HDInsight 작업 영역 열기](#open-hdinsight-workspace) 및 [Azure에 연결](#connect-to-hdinsight-cluster)을 참조하세요.

2. 스크립트 편집기를 마우스 오른쪽 단추로 클릭한 다음, 상황에 맞는 메뉴에서 **HDInsight: List Cluster**를 선택합니다. 

3. **출력** 창에 HDInsight 클러스터가 표시됩니다.

    ![기본 클러스터 구성 설정](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-a-default-cluster"></a>기본 클러스터 설정
1. 작업 영역을 열고 Azure에 연결합니다. [HDInsight 작업 영역 열기](#open-hdinsight-workspace) 및 [Azure에 연결](#connect-to-hdinsight-cluster)을 참조하세요.

2. 스크립트 편집기를 마우스 오른쪽 단추로 클릭한 다음, **HDInsight: Set Default Cluster**를 선택합니다. 

3. 현재 스크립트 파일에 대한 기본 클러스터로 사용할 클러스터를 선택합니다. **.VSCode\settings.json** 구성 파일이 자동으로 업데이트됩니다. 

   ![기본 클러스터 구성 설정](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Azure 환경 설정
1. **Ctrl+Shift+P**를 선택하여 명령 팔레트를 엽니다.

2. **HDInsight: Set Azure Environment**를 입력합니다.

3. "Azure" 또는 "AzureChina"와 같은 환경을 기본 로그인 항목으로 선택합니다.

4. 그렇지만 사용자의 기본 로그인 항목은 **.VSCode\settings.json**이미 저장되어 있습니다. 이 구성 파일에서 기본 로그인 항목을 직접 업데이트할 수도 있습니다. 

   ![기본 로그인 항목 구성 설정](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>대화형 Hive 쿼리, Hive 배치 스크립트 제출

HDInsight Tools for VS Code를 사용하면 대화형 HDInsight 쿼리, Hive 배치 스크립트를 HDInsight 클러스터에 제출할 수 있습니다.

1. 새 작업 폴더 및 새 Hive 스크립트 파일을 만듭니다(아직 없는 경우).

2. Azure 계정에 연결하거나 클러스터를 연결합니다.

3. 다음 코드를 복사하여 Hive 파일에 붙여넣은 후 파일을 저장합니다.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
4. 스크립트 편집기를 마우스 오른쪽 단추로 클릭하고, **HDInsight: Hive Interactive**를 선택하여 쿼리를 제출하거나 바로 가기 **Ctrl + Alt + I**를 사용합니다. **HDInsight: Hive Batch**를 선택하여 스크립트를 제출하거나 바로 가기 **Ctrl + Alt + H**를 사용합니다. 

5. 기본 클러스터를 지정하지 않은 경우 클러스터를 선택합니다. 이 도구에서는 상황에 맞는 메뉴를 사용하여 전체 스크립트 파일 대신 코드 블록을 제출할 수도 있습니다. 잠시 후 새 탭에 쿼리 결과가 표시됩니다.

   ![대화형 Hive 결과](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **결과** 패널: 전체 결과를 CSV, JSON 또는 Excel 파일로 로컬 경로에 저장하거나 여러 줄을 선택할 수 있습니다.

    - **메시지** 패널: **줄** 번호를 선택하면 실행 중인 스크립트의 첫 번째 줄로 이동합니다.

## <a name="submit-interactive-pyspark-queries"></a>대화형 PySpark 쿼리 제출

### <a name="to-submit-interactive-pyspark-queries-to-hdinsight-spark-clusters"></a>대화형 PySpark 쿼리를 HDInsight Spark 클러스터에 제출하려면 다음을 수행합니다.

1. 새 작업 폴더 및 확장명이 .py인 새 스크립트 파일을 만듭니다(아직 없는 경우).

2. Azure 계정에 아직 연결하지 않은 경우 연결합니다.

3. 다음 코드를 복사하여 스크립트 파일에 붙여넣습니다.
   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```
4. 이 스크립트를 강조 표시합니다. 그런 다음, 스크립트 편집기를 마우스 오른쪽 단추로 클릭하고 **HDInsight: PySpark Interactive**를 선택하거나 바로 가기 **Ctrl + Alt + I**를 사용합니다.

5. VS Code에서 **Python** 확장을 아직 설치하지 않은 경우 다음 그림과 같이 **설치** 단추를 선택합니다.

    ![HDInsight for Visual Studio Code Python 설치](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

6. 시스템에 Python 환경을 아직 설치하지 않은 경우 설치합니다. 
   - Windows에서 [Python](https://www.python.org/downloads/)을 다운로드하고 설치합니다. 그런 다음 시스템 경로에 `Python` 및 `pip`가 있는지 확인합니다.

   - macOS 및 Linux용 지침은 [Visual Studio Code용 PySpark 대화형 환경 설정](set-up-pyspark-interactive-environment.md)을 참조하세요.

7. PySpark 쿼리를 제출할 클러스터를 선택합니다. 잠시 후 다음과 같이 오른쪽의 새 탭에 쿼리 결과가 표시됩니다.

   ![Python 작업 결과 제출](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 
8. 이 도구는 **SQL 절** 쿼리도 지원합니다.

   ![Python 결과 작업 제출](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) 쿼리를 실행할 때 제출 상태가 아래쪽 상태 표시줄의 왼쪽에 표시됩니다. 상태가 **PySpark 커널(작업 중)** 이면 다른 쿼리를 제출하지 마세요. 

>[!NOTE]  
>클러스터는 세션 정보를 유지할 수 있습니다. 정의된 변수, 함수 및 해당 값은 세션에 유지되므로 동일한 클러스터에 대한 여러 서비스 호출에서 참조될 수 있습니다. 

### <a name="to-disable-environment-check"></a>환경 검사를 사용하지 않으려면

기본적으로 HDInsight 도구는 대화형 PySpark 쿼리를 제출할 때 환경을 검사하고 종속 패키지를 설치합니다. 환경 검사를 사용하지 않으려면 **사용자 설정**에서 **hdinsight.disablePysparkEnvironmentValidation**을 **예**로 설정합니다.

   ![설정에서 환경 검사 설정](./media/hdinsight-for-vscode/hdi-azure-hdinsight-environment-check.png)

또는 대화 상자가 표시되면 **유효성 검사 사용 안 함** 단추를 클릭합니다.

   ![대화 상자에서 환경 검사 설정](./media/hdinsight-for-vscode/hdi-azure-hdinsight-environment-check-dialog.png)

### <a name="pyspark3-is-not-supported-with-spark2223"></a>PySpark3은 Spark2.2/2.3에서 지원되지 않습니다.

PySpark3은 Spark 2.2 클러스터 및 Spark2.3 클러스터에서 더 이상 지원되지 않고, “PySpark”만 Python에서 지원됩니다. Python3에서 Spark 2.2/2.3에 대한 제출이 실패하는 것은 알려진 문제입니다.

   ![Python3에 제출 시 오류 발생](./media/hdinsight-for-vscode/hdi-azure-hdinsight-py3-error.png)

Python2.x를 사용하려면 다음 단계를 수행합니다. 

1. Python 2.7을 로컬 컴퓨터에 설치하고 시스템 경로에 추가합니다.

2. VSCode를 다시 시작합니다.

3. 상태 표시줄에서 **Python XXX**를 클릭하여 Python 2로 전환하고 대상 Python을 선택합니다.

   ![Python 버전 선택](./media/hdinsight-for-vscode/hdi-azure-hdinsight-select-python.png)

## <a name="submit-pyspark-batch-job"></a>PySpark 배치 작업 제출

1. 새 작업 폴더 및 확장명이 .py인 새 스크립트 파일을 만듭니다(아직 없는 경우).

2. Azure 계정에 아직 연결하지 않은 경우 연결합니다.

3. 다음 코드를 복사하여 스크립트 파일에 붙여넣습니다.

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```
4. 스크립트 편집기를 마우스 오른쪽 단추로 클릭한 다음, **HDInsight: PySpark Batch**를 선택하거나 바로 가기 **Ctrl + Alt + H**를 사용합니다. 

5. PySpark 작업을 제출할 클러스터를 선택합니다. 

   ![Python 작업 결과 제출](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Python 작업을 제출한 후 전송 로그가 VS Code의 **출력** 창에 나타납니다. **Spark UI URL** 및 **Yarn UI URL**도 표시됩니다. 웹 브라우저에서 URL을 열어 작업 상태를 추적할 수 있습니다.

## <a name="apache-livy-configuration"></a>Apache Livy 구성

[Apache Livy](https://livy.incubator.apache.org/) 구성은 지원되며, 작업 영역 폴더의 **.VSCode\settings.json**에서 설정할 수 있습니다. 현재 Livy 구성은 Python 스크립트만 지원합니다. 자세한 내용은 [Livy 추가 정보](https://github.com/cloudera/livy/blob/master/README.rst )를 참조하세요.

<a id="triggerlivyconf"></a>**Livy 구성을 트리거하는 방법**
   
**파일** 메뉴를 찾고, **기본 설정**을 선택하고, 상황에 맞는 메뉴에서 **설정**을 선택할 수 있습니다. **작업 영역 설정** 탭을 클릭하고 Livy 구성 설정을 시작할 수 있습니다.

파일을 제출할 수도 있습니다. .vscode 폴더가 작업 폴더에 자동으로 추가됩니다. **.vscode\settings.json**을 클릭하여 Livy 구성을 찾을 수 있습니다.

+ 프로젝트 설정:

    ![Livy 구성](./media/hdinsight-for-vscode/hdi-livyconfig.png)

>[!NOTE]
>**driverMomory** 및 **executorMomry** 설정의 경우 단위가 있는 값을 설정합니다(예: 1g 또는 1024m). 

+ 지원되는 Livy 구성:   

    **POST/일괄 처리**   
    요청 본문

    | 이름 | description | 형식 | 
    | :- | :- | :- | 
    | file | 실행할 애플리케이션을 포함하는 파일 | 경로(필수) | 
    | proxyUser | 작업을 실행할 때 가장하는 사용자 | string | 
    | className | 애플리케이션 Java/Spark 주 클래스 | string |
    | args | 애플리케이션에 대한 명령줄 인수 | 문자열 목록 | 
    | ./jars | 이 세션에 사용할 jars | 문자열 목록 | 
    | pyFiles | 이 세션에 사용할 Python 파일 | 문자열 목록 |
    | 업로드 | 이 세션에 사용할 파일 | 문자열 목록 |
    | driverMemory | 드라이버 프로세스에 사용할 메모리의 양 | string |
    | driverCores | 드라이버 프로세스에 사용할 코어 수 | int |
    | executorMemory | 실행기 프로세스당 사용할 메모리의 양 | string |
    | executorCores | 각 실행기에 사용할 코어 수 | int |
    | numExecutors | 이 세션에 대해 시작할 실행기 수 | int |
    | 아카이브 | 이 세션에 사용할 아카이브 | 문자열 목록 |
    | 큐 | 제출되는 YARN 큐의 이름 | string |
    | 이름 | 이 세션의 이름 | string |
    | conf | Spark 구성 속성 | key=val의 맵 |

    응답 본문   
    만든 일괄 처리 개체입니다.

    | 이름 | description | 형식 | 
    | :- | :- | :- | 
    | id | 세션 ID | int | 
    | appId | 이 세션의 애플리케이션 ID |  문자열 |
    | appInfo | 자세한 애플리케이션 정보 | key=val의 맵 |
    | 로그 | 로그 줄 | 문자열 목록 |
    | state |   일괄 처리 상태 | string |

>[!NOTE]
>스크립트를 제출하면 할당된 Livy 구성이 출력 창에 표시됩니다.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>탐색기에서 Azure HDInsight와 통합

Azure HDInsight가 왼쪽 패널에 추가되었습니다. 클러스터를 직접 찾아보고 관리할 수 있습니다.

1. **AZURE HDINSIGHT**를 확장합니다. 로그인하지 않은 경우 **Azure에 로그인...** 링크가 표시됩니다.

    ![로그인 링크 이미지](./media/hdinsight-for-vscode/hid-azure-hdinsight-sign-in.png)

2. **Azure에 로그인**을 클릭합니다. 오른쪽 아래에 로그인 링크 및 코드가 표시됩니다.

    ![기타 환경에 대한 로그인 지침](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin-code.png)

3. **복사 및 열기** 단추를 클릭하면 브라우저가 열립니다. 코드를 붙여넣고 **계속** 단추를 클릭하면 로그인 성공에 대한 힌트가 표시됩니다.

4. 로그인한 후 사용 가능한 구독 및 클러스터(Spark, Hadoop 및 HBase가 지원됨)가 **AZURE HDINSIGHT**에 나열됩니다. 

   ![Azure HDInsight 구독](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

5. 클러스터를 확장하여 Hive 메타데이터 데이터베이스 및 테이블 스키마를 확인합니다.

   ![Azure HDInsight 클러스터](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)

## <a name="additional-features"></a>추가 기능

HDInsight for VS Code에서 지원하는 기능은 다음과 같습니다.

- **IntelliSense 자동 완성**. 키워드, 메서드, 변수 등에 대한 추천 항목이 팝업됩니다. 다음과 같이 다양한 아이콘이 여러 형식의 개체를 나타냅니다.

    ![HDInsight Tools for Visual Studio Code IntelliSense 개체 형식](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **IntelliSense 오류 표식**. 언어 서비스에서 Hive 스크립트에 대한 편집 오류에 밑줄을 표시합니다.     
- **구문 강조 표시**. 언어 서비스는 변수, 키워드, 데이터 형식, 함수 등을 구분하기 위해 서로 다른 색상을 사용합니다. 

    ![HDInsight Tools for Visual Studio Code 구문 강조 표시](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>다음 단계

### <a name="demo"></a>데모
* HDInsight for VS Code: [비디오](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>도구 및 확장

* [Azure Toolkit for IntelliJ를 사용하여 VPN을 통해 원격으로 Apache Spark 애플리케이션 디버그](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Azure Toolkit for IntelliJ를 사용하여 SSH를 통해 원격으로 Apache Spark 애플리케이션 디버그](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Hortonworks 샌드박스에서 IntelliJ용 HDInsight Tools 사용](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Azure Toolkit for Eclipse의 HDInsight 도구를 사용하여 Apache Spark 애플리케이션 만들기](spark/apache-spark-eclipse-tool-plugin.md)
* [HDInsight에서 Apache Spark 클러스터와 함께 Apache Zeppelin Notebook 사용](spark/apache-spark-zeppelin-notebook.md)
* [HDInsight의 Apache Spark 클러스터에서 Jupyter Notebook에 사용할 수 있는 커널](spark/apache-spark-jupyter-notebook-kernels.md)
* [Jupyter 노트북에서 외부 패키지 사용](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [컴퓨터에 Jupyter를 설치하고 HDInsight Spark 클러스터에 연결](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Azure HDInsight에서 Microsoft Power BI를 사용하여 Apache Hive 데이터 시각화](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Azure HDInsight에서 Power BI를 사용하여 대화형 쿼리 Hive 데이터 시각화](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)
* [Visual Studio Code용 PySpark 대화형 환경 설정](set-up-pyspark-interactive-environment.md)
* [Azure HDInsight에서 Apache Zeppelin을 사용하여 Apache Hive 쿼리 실행](./hdinsight-connect-hive-zeppelin.md)

### <a name="scenarios"></a>시나리오
* [BI와 Apache Spark: BI 도구와 함께 HDInsight의 Spark를 사용하여 대화형 데이터 분석 수행](spark/apache-spark-use-bi-tools.md)
* [Machine Learning과 Apache Spark: HDInsight의 Spark를 사용하여 HVAC 데이터로 건물 온도 분석](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Machine Learning과 Apache Spark: HDInsight의 Spark를 사용하여 식품 검사 결과 예측](spark/apache-spark-machine-learning-mllib-ipython.md)
* [HDInsight의 Apache Spark를 사용한 웹 사이트 로그 분석](spark/apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-running-applications"></a>애플리케이션 만들기 및 실행
* [Scala를 사용하여 독립 실행형 애플리케이션 만들기](spark/apache-spark-create-standalone-application.md)
* [Apache Livy를 사용하여 Apache Spark 클러스터에서 원격으로 작업 실행](spark/apache-spark-livy-rest-interface.md)

### <a name="manage-resources"></a>리소스 관리
* [Azure HDInsight에서 Apache Spark 클러스터에 대한 리소스 관리](spark/apache-spark-resource-manager.md)
* [HDInsight의 Apache Spark 클러스터에서 실행되는 작업 추적 및 디버그](spark/apache-spark-job-debugging.md)



