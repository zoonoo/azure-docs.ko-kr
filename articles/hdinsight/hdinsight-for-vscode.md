---
title: 비주얼 스튜디오 코드용 Azure HDInsight
description: 비주얼 스튜디오 코드에 스파크 & 하이브 도구(Azure HDInsight)를 사용하여 쿼리 및 스크립트를 만들고 제출하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 9a81868d678b4c0277e904e879c73185a378bf70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435679"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>비주얼 스튜디오 코드에 스파크 & 하이브 도구 사용

비주얼 스튜디오 코드에 스파크 & 하이브 도구를 사용하여 아파치 하이브 배치 작업, 대화형 하이브 쿼리 및 아파치 스파크용 PySpark 스크립트를 만들고 제출하는 방법을 알아봅니다. 먼저 비주얼 스튜디오 코드에 스파크 & 하이브 도구를 설치하는 방법을 설명한 다음, 스파크 & 하이브 도구에 작업을 제출하는 방법을 살펴보겠습니다.  

Windows, Linux 및 macOS를 포함하여 Visual Studio Code에서 지원되는 플랫폼에 Spark & Hive Tools를 설치할 수 있습니다. 다른 플랫폼에 대한 다음 필수 구성 조건을 참고하십시오.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 완료하려면 다음 항목이 필요합니다.

- Azure HDInsight 클러스터를 만듭니다. 클러스터를 만들려면 [HDInsight 시작](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)을 참조하세요. 또는 아파치 리비 엔드포인트를 지원하는 스파크 및 하이브 클러스터를 사용합니다.
- [비주얼 스튜디오 코드](https://code.visualstudio.com/).
- [모노](https://www.mono-project.com/docs/getting-started/install/). 모노는 리눅스와 맥 OS에 만 필요합니다.
- [비주얼 스튜디오 코드에 대한 PySpark 대화 형 환경.](set-up-pyspark-interactive-environment.md)
- 로컬 디렉터리입니다. 이 문서에서는 **C:\HD\HDexample**을 사용합니다.

## <a name="install-spark--hive-tools"></a>Spark & Hive Tools 설치

필수 구성 조건을 충족한 후에는 다음 단계를 수행하여 Visual Studio 코드에 & 하이브 도구를 설치할 수 있습니다.

1. Visual Studio Code를 엽니다.

2. 메뉴 모음에서 확장 **보기로** > **이동합니다.**

3. 검색 상자에 **Spark & Hive**를 입력합니다.

4. 검색 결과에서 **하이브 도구에 & 스파크를** 선택한 다음 **설치를**선택합니다.

   ![비주얼 스튜디오 코드 파이썬 설치에 대한 하이브에 & 스파크](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. 필요한 경우 **다시 로드를** 선택합니다.

## <a name="open-a-work-folder"></a>작업 폴더 열기

작업 폴더를 열고 Visual Studio 코드에서 파일을 만들려면 다음 단계를 따르십시오.

1. 메뉴 모음에서 **파일** > **열기 폴더로 이동합니다...**  >  **C:\HD\HD예제를**선택한 다음 **폴더 선택** 단추를 선택합니다. 왼쪽 **탐색기** 뷰에 폴더가 표시됩니다.

2. **탐색기** 보기에서 **HDexample** 폴더를 선택한 다음 작업 폴더 옆에 있는 **새 파일** 아이콘을 선택합니다.

   ![비주얼 스튜디오 코드 새 파일 아이콘](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. `.hql` (Hive 쿼리) 또는 (Spark 스크립트) `.py` 파일 확장명을 사용하여 새 파일의 이름을 지정합니다. 이 예제에서는 **HelloWorld.hql**을 사용합니다.

## <a name="set-the-azure-environment"></a>Azure 환경 설정

국가 클라우드 사용자의 경우 다음 단계를 수행하여 Azure 환경을 먼저 설정한 다음 **Azure: 로그인** 명령을 사용하여 Azure에 로그인합니다.

1. **파일** > **기본 설정으로 이동합니다.** > **Settings**
2. 다음 문자열에서 검색: **Azure: 클라우드**.
3. 목록에서 국가 클라우드를 선택합니다.

   ![기본 로그인 항목 구성 설정](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>Azure 계정에 연결

Visual Studio 코드에서 클러스터에 스크립트를 제출하려면 먼저 Azure 계정에 연결하거나 클러스터를 연결해야 합니다(아파치 Ambari 사용자 이름 및 암호 자격 증명 또는 도메인 가입 계정 사용). Azure에 연결하려면 다음 단계를 따릅니다.

1. 메뉴 모음에서**명령 팔레트** **보기로** > 이동한 다음 **Azure: 로그인을**입력합니다.

    ![스파크 & 하이브 도구 비주얼 스튜디오 코드 로그인](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. 로그인 지침에 따라 Azure에 로그인합니다. 연결되면 Azure 계정 이름이 Visual Studio 코드 창 하단의 상태 표시줄에 표시됩니다.  

## <a name="link-a-cluster"></a>클러스터 연결

### <a name="link-azure-hdinsight"></a>링크: Azure HDInsight

[Apache Ambari-관리되는](https://ambari.apache.org/)사용자 이름을 사용하여 일반 클러스터를 연결하거나 도메인 사용자 이름(예: `user1@contoso.com`)을 사용하여 엔터프라이즈 보안 팩 보안 Hadoop 클러스터를 연결할 수 있습니다.

1. 메뉴 모음에서**명령 팔레트** **보기로** > 이동하여 **스파크 /하이브: 클러스터 연결**을 입력합니다.

   ![명령 팔레트 링크 클러스터 명령](./media/hdinsight-for-vscode/link-cluster-command.png)

2. 연결된 클러스터 유형 **Azure HDInsight**를 선택합니다.

3. HDInsight 클러스터 URL을 입력합니다.

4. Ambari 사용자 이름을 입력합니다. 기본값은 **관리자**입니다.

5. Ambari 암호를 입력합니다.

6. 클러스터 유형을 선택합니다.

7. 클러스터의 표시 이름을 설정합니다(선택 사항).

8. 확인을 위해 **출력** 뷰를 검토합니다.

   > [!NOTE]  
   > 클러스터가 Azure 구독에 로그인하고 클러스터를 연결한 경우 연결된 사용자 이름과 암호가 사용됩니다.  

### <a name="link-generic-livy-endpoint"></a>링크: 일반 리비 끝점

1. 메뉴 모음에서**명령 팔레트** **보기로** > 이동하여 **스파크 /하이브: 클러스터 연결**을 입력합니다.

2. 연결된 클러스터 유형 **일반 Livy 엔드포인트**를 선택합니다.

3. 일반 Livy 끝점을 입력합니다. 예: http\://172.41.42:18080.

4. 인증 형식 **기본** 또는 **없음**을 선택합니다.  **기본을**선택하는 경우 :  
    &emsp;a. Ambari 사용자 이름을 입력합니다. 기본값은 **관리자**입니다.  
    &emsp;b. Ambari 암호를 입력합니다.

5. 확인을 위해 **출력** 뷰를 검토합니다.

## <a name="list-clusters"></a>클러스터 나열

1. 메뉴 모음에서**명령 팔레트** **보기로** > 이동한 다음 **스파크 /하이브: 목록 클러스터를**입력합니다.

2. 원하는 구독을 선택합니다.

3. **출력** 뷰를 검토합니다. 이 보기는 연결된 클러스터(또는 클러스터)와 Azure 구독 아래의 모든 클러스터를 보여 줍니다.

    ![기본 클러스터 구성 설정](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="set-the-default-cluster"></a>기본 클러스터 설정

1. [이전에](#open-a-work-folder)설명한 **HDexample** 폴더를 다시 엽니다.  

2. [이전에](#open-a-work-folder)만든 **HelloWorld.hql** 파일을 선택합니다. 스크립트 편집기에서 열립니다.

3. 스크립트 편집기를 마우스 오른쪽 단추로 클릭한 다음 **스파크/하이브: 기본 클러스터 설정**을 선택합니다.  

4. Azure 계정에 [연결하거나](#connect-to-an-azure-account) 아직 연결하지 않은 경우 클러스터를 연결합니다.

5. 현재 스크립트 파일의 기본 클러스터로 사용할 클러스터를 선택합니다. 도구는 자동으로 **을 업데이트합니다. VSCode\settings.json** 구성 파일:

   ![기본 클러스터 구성 설정](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>대화형 하이브 쿼리 및 Hive 배치 스크립트 제출

비주얼 스튜디오 코드에 대한 스파크 & 하이브 도구를 사용하면 클러스터에 대화형 하이브 쿼리 및 Hive 배치 스크립트를 제출할 수 있습니다.

1. [이전에](#open-a-work-folder)설명한 **HDexample** 폴더를 다시 엽니다.  

2. [이전에](#open-a-work-folder)만든 **HelloWorld.hql** 파일을 선택합니다. 스크립트 편집기에서 열립니다.

3. 다음 코드를 복사하여 Hive 파일에 붙여넣은 다음 저장합니다.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. Azure 계정에 [연결하거나](#connect-to-an-azure-account) 아직 연결하지 않은 경우 클러스터를 연결합니다.

5. 스크립트 편집기를 마우스 오른쪽 단추로 클릭하고 **Hive: 대화형을** 선택하여 쿼리를 제출하거나 Ctrl+Alt+I 키보드 단축키를 사용합니다.  **하이브: 일괄 처리를** 선택하여 스크립트를 제출하거나 Ctrl+Alt+H 키보드 단축키를 사용합니다.  

6. 기본 클러스터를 지정하지 않은 경우 클러스터를 선택합니다. 또한 이 도구를 사용하면 컨텍스트 메뉴를 사용하여 전체 스크립트 파일 대신 코드 블록을 제출할 수 있습니다. 잠시 후 쿼리 결과가 새 탭에 나타납니다.

   ![대화형 아파치 하이브 쿼리 결과](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - **결과** 패널: 전체 결과를 CSV, JSON 또는 Excel 파일로 로컬 경로에 저장하거나 여러 줄을 선택할 수 있습니다.

    - **메시지** 패널: **줄** 번호를 선택하면 실행 중인 스크립트의 첫 번째 줄로 이동합니다.

## <a name="submit-interactive-pyspark-queries"></a>대화형 PySpark 쿼리 제출

대화형 PySpark 쿼리를 제출하려면 다음 단계를 따르세요.

1. [이전에](#open-a-work-folder)설명한 **HDexample** 폴더를 다시 엽니다.  

2. [이전](#open-a-work-folder) 단계에 따라 새 **HelloWorld.py** 파일을 만듭니다.

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

4. Azure 계정에 [연결하거나](#connect-to-an-azure-account) 아직 연결하지 않은 경우 클러스터를 연결합니다.

5. 모든 코드를 선택하고 스크립트 편집기를 마우스 오른쪽 단추로 클릭하고 **Spark: PySpark Interactive를** 선택하여 쿼리를 제출합니다. 또는 Ctrl+Alt+I 바로 가기를 사용합니다.

   ![pyspark 대화형 상황에 맞는 메뉴](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. 기본 클러스터를 지정하지 않은 경우 클러스터를 선택합니다. 잠시 후 **파이썬 인터랙티브** 결과가 새 탭에 나타납니다. 또한 이 도구를 사용하면 컨텍스트 메뉴를 사용하여 전체 스크립트 파일 대신 코드 블록을 제출할 수 있습니다.

   ![pyspark 대화형 python 대화형 창](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png)

7. **%%정보를**입력한 다음 Shift+Enter를 눌러 작업 정보를 봅니다(선택 사항).

   ![pyspark 대화형 보기 작업 정보](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. 이 도구는 **Spark SQL** 쿼리도 지원합니다.

   ![파이스파크 인터랙티브 뷰 결과](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   쿼리를 실행할 때 제출 상태가 아래쪽 상태 표시줄왼쪽에 나타납니다. 상태가 **PySpark 커널(작업 중)** 이면 다른 쿼리를 제출하지 마세요.  

   > [!NOTE]
   >
   > **파이썬 확장 활성화가** 설정에서 지워지면 (기본적으로 선택됨) 제출 된 pyspark 상호 작용 결과는 이전 창을 사용합니다.
   >
   > ![pyspark 대화형 python 확장 사용 안 함](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)

## <a name="submit-pyspark-batch-job"></a>PySpark 일괄 작업 제출

1. [이전에](#open-a-work-folder)설명한 **HDexample** 폴더를 다시 엽니다.  

2. [이전](#open-a-work-folder) 단계에 따라 새 **BatchFile.py** 파일을 만듭니다.

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

4. Azure 계정에 [연결하거나](#connect-to-an-azure-account) 아직 연결하지 않은 경우 클러스터를 연결합니다.

5. 스크립트 편집기를 마우스 오른쪽 단추로 클릭한 다음 **스파크: PySpark 배치를**선택하거나 Ctrl+Alt+H 키보드 단축키를 사용합니다.

6. 클러스터를 선택하여 PySpark 작업을 다음으로 제출합니다.

   ![파이썬 작업 결과 출력 제출](./media/hdinsight-for-vscode/submit-pythonjob-result.png)

Python 작업을 제출하면 Visual Studio Code의 **출력** 창에 제출 로그가 표시됩니다. 스파크 UI URL과 원사 UI URL도 표시됩니다. 웹 브라우저에서 URL을 열어 작업 상태를 추적할 수 있습니다.

## <a name="apache-livy-configuration"></a>Apache Livy 구성

[아파치 리비](https://livy.incubator.apache.org/) 구성이 지원됩니다. 에서 구성할 수 **있습니다. 작업 영역 폴더의 VSCode\settings.json** 파일입니다. 현재, Livy 구성은 Python 스크립트만 지원합니다. 자세한 내용은 [리비 README](https://github.com/cloudera/livy/blob/master/README.rst )를 참조하십시오.

<a id="triggerlivyconf"></a>**리비 구성을 트리거하는 방법**

방법 1  
1. 메뉴 모음에서 **파일** > **기본 설정으로 이동합니다.** > **Settings**
2. 검색 **설정** 상자에서 **HDInsight 작업 제출: Livy Conf**.  
3. 관련 검색 결과에 대해 **settings.json에서 편집**을 선택합니다.

방법 2 파일을 제출하고 .vscode 폴더가 작업 폴더에 자동으로 추가된다는 것을 알 수 있습니다. **.vscode\settings.json**을 선택하여 Livy 구성을 볼 수 있습니다.

+ 프로젝트 설정:

    ![HDInsight 아파치 리비 구성](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

    >[!NOTE]
    >**드라이버메모리** 및 **실행기메모리** 설정의 경우 값과 단위를 설정합니다. 예를 들어: 1g 또는 1024m.

+ 지원되는 리비 구성:

    **포스트 /일괄 처리** 요청 본문

    | name | description | type |
    | :- | :- | :- |
    | 파일 | 실행할 애플리케이션이 포함된 파일 | 경로(필수) |
    | proxyUser | 작업을 실행할 때 가장할 사용자 | String |
    | className | 애플리케이션 Java/Spark 주 클래스 | String |
    | args | 응용 프로그램에 대한 명령줄 인수 | 문자열 목록 |
    | jars | 이 세션에서 사용할 항아리 | 문자열 목록 | 
    | pyFiles | 이 세션에서 사용할 Python 파일 | 문자열 목록 |
    | files | 이 세션에서 사용할 파일 | 문자열 목록 |
    | driverMemory | 드라이버 프로세스에 사용할 메모리 크기 | String |
    | driverCores | 드라이버 프로세스에 사용할 코어 수 | Int |
    | executorMemory | 실행기 프로세스당 사용할 메모리 크기 | String |
    | executorCores | 각 실행기에 사용할 코어 수 | Int |
    | numExecutors | 이 세션에서 시작할 실행기 수 | Int |
    | archives | 이 세션에서 사용할 보관 파일 | 문자열 목록 |
    | queue | 제출할 YARN 큐의 이름| String |
    | name | 이 세션의 이름 | String |
    | conf | Spark 구성 속성 | 키=값 맵 |

    응답 본문 생성된 배치 개체입니다.

    | name | description | type |
    | :- | :- | :- |
    | id | 세션 ID | Int |
    | appId | 이 세션의 응용 프로그램 ID | String |
    | appInfo | 자세한 응용 프로그램 정보 | 키=값 맵 |
    | log | 로그 라인 | 문자열 목록 |
    | state |일괄 처리 상태 | String |

    > [!NOTE]
    > 스크립트를 제출할 때 할당된 Livy 구성이 출력 창에 표시됩니다.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>탐색기에서 Azure HDInsight와 통합

**Azure HDInsight** 탐색기를 통해 클러스터에서 하이브 테이블을 직접 미리 볼 수 있습니다.

1. Azure 계정에 아직 연결하지 않은 경우 [연결](#connect-to-an-azure-account)합니다.

2. 맨 왼쪽 열에서 **Azure** 아이콘을 선택합니다.

3. 왼쪽 창에서 **AZURE를 확장합니다.** 사용 가능한 구독 및 클러스터가 나열됩니다.

4. 클러스터를 확장하여 Hive 메타데이터 데이터베이스 및 테이블 스키마를 봅니다.

5. 하이브 테이블을 마우스 오른쪽 단추로 클릭합니다. 예를 들어 **하이브 샘플 테이블**. **미리 보기**를 선택합니다.

   ![비주얼 스튜디오 코드 미리보기 하이브 테이블에 대한 하이브에 & 스파크](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. **미리 보기 결과** 창이 열립니다.

   ![비주얼 스튜디오 코드 미리 보기 결과 창에 대 한 하이브에 & 스파크](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)

- 결과 패널

   전체 결과를 CSV, JSON 또는 Excel 파일로 로컬 경로에 저장하거나 여러 줄을 선택할 수 있습니다.

- 메시지 패널
   1. 테이블의 행 수가 100개보다 크면 "처음 100개의 행이 Hive 테이블에 표시됩니다."
   2. 테이블의 행 수가 100보다 적거나 같으면 다음과 같은 메시지가 표시됩니다.
   3. 테이블에 내용이 없는 경우 "하이브 테이블에 0개의 행이 표시됩니다."

        >[!NOTE]
        >
        >Linux에서 xclip을 설치하여 복사 테이블 데이터를 활성화합니다.
        >
        >![리눅스에서 비주얼 스튜디오 코드에 대 한 하이브에 & 스파크](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)

## <a name="additional-features"></a>추가 기능

스파크 & 하이브 비주얼 스튜디오 코드는 다음과 같은 기능도 지원합니다.

- **인텔리센스 자동 완성**. 키워드, 메서드, 변수 및 기타 프로그래밍 요소에 대한 제안이 나타납니다. 다음과 같이 개체 형식마다 다른 아이콘으로 표시됩니다.

    ![비주얼 스튜디오 코드 intelliSense 개체에 대한 하이브 도구 에 & 스파크](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)

- **인텔리센스 오류 마커**. 언어 서비스는 Hive 스크립트의 편집 오류를 강조합니다.     
- **구문 강조 표시됩니다.** 언어 서비스는 다양한 색상을 사용하여 변수, 키워드, 데이터 유형, 함수 및 기타 프로그래밍 요소를 구분합니다.

    ![Visual Studio Code용 Spark & Hive Tools 구문 강조 표시](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>독자 전용 역할

클러스터에 대한 판독기 전용 역할이 할당된 사용자는 더 이상 HDInsight 클러스터에 작업을 제출할 수 없으며 Hive 데이터베이스를 볼 수 없습니다. Azure [포털에서](https://ms.portal.azure.com/)역할을 [**HDInsight 클러스터 연산자로**](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) 업그레이드하려면 클러스터 관리자에게 문의하십시오. 유효한 Ambari 자격 증명이 있는 경우 다음 지침을 사용하여 클러스터를 수동으로 연결할 수 있습니다.

### <a name="browse-the-hdinsight-cluster"></a>HDInsight 클러스터 찾아보기  

HdInsight 클러스터를 확장하기 위해 Azure HDInsight 탐색기를 선택하면 클러스터에 대한 판독기 전용 역할이 있는 경우 클러스터를 연결하라는 메시지가 표시됩니다. 다음 방법을 사용하여 Ambari 자격 증명을 사용하여 클러스터에 연결합니다.

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>HDInsight 클러스터에 작업 제출

HDInsight 클러스터에 작업을 제출할 때 클러스터의 판독기 전용 역할에 있는 경우 클러스터를 연결하라는 메시지가 표시됩니다. 다음 단계를 사용하여 Ambari 자격 증명을 사용하여 클러스터에 연결합니다.

### <a name="link-to-the-cluster"></a>클러스터에 대한 링크

1. 유효한 Ambari 사용자 이름을 입력합니다.
2. 유효한 암호를 입력합니다.

   ![비주얼 스튜디오 코드 사용자 이름에 대 한 하이브 도구를 & 스파크](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![비주얼 스튜디오 코드 암호에 대 한 하이브 도구에 & 스파크](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

  > [!NOTE]
  >
  >연결된 클러스터를 확인하는 데 사용할 `Spark / Hive: List Cluster` 수 있습니다.
  >
  >![스파크 & 하이브 도구 비주얼 스튜디오 코드 리더 링크에 대 한](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="browse-a-data-lake-storage-gen2-account"></a>데이터 레이크 스토리지 Gen2 계정 찾아보기

Azure HDInsight 탐색기를 선택하여 데이터 레이크 저장소 Gen2 계정을 확장하면 Azure 계정에 Gen2 저장소에 액세스할 수 없는 경우 저장소 액세스 키를 입력하라는 메시지가 표시됩니다. 액세스 키의 유효성을 검사한 후 Data Lake Storage Gen2 계정이 자동으로 확장됩니다.

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>데이터 레이크 스토리지 Gen2를 사용하여 HDInsight 클러스터에 작업 제출

Data Lake Storage Gen2를 사용하여 HDInsight 클러스터에 작업을 제출하면 Azure 계정에 Gen2 저장소에 대한 쓰기 액세스 권한이 없는 경우 저장소 액세스 키를 입력하라는 메시지가 표시됩니다. 액세스 키의 유효성을 검사하면 작업이 성공적으로 제출됩니다.

![비주얼 스튜디오 코드 액세스 키에 대 한 하이브 도구에 & 스파크](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)

> [!NOTE]
>
> Azure 포털에서 저장소 계정에 대한 액세스 키를 얻을 수 있습니다. 자세한 내용은 [스토리지 계정 액세스 키 관리](../storage/common/storage-account-keys-manage.md)를 참조하세요.

## <a name="unlink-cluster"></a>클러스터 연결 해제

1. 메뉴 모음에서**명령 팔레트** **보기로** > 이동한 다음 **스파크 /하이브: 클러스터 연결 해제를**입력합니다.  

2. 연결을 해제할 클러스터를 선택합니다.  

3. 확인은 **출력** 보기를 참조하십시오.  

## <a name="sign-out"></a>로그아웃  

메뉴 모음에서**명령 팔레트** **보기로** > 이동한 다음 **Azure: 로그아웃**을 입력합니다.

## <a name="next-steps"></a>다음 단계

비주얼 스튜디오 코드에 스파크 & 하이브를 사용하는 것을 보여 주는 비디오는 [비주얼 스튜디오 코드의 스파크 & 하이브를](https://go.microsoft.com/fwlink/?linkid=858706)참조하십시오.
