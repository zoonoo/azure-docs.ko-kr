---
title: Azure HDInsight 구성 설정 참조
description: Azure HDInsight 확장의 구성을 소개합니다.
ms.service: hdinsight
ms.topic: how-to
ms.date: 04/07/2021
ms.custom: devx-track-python
ms.openlocfilehash: e5bfa66f7c7d22617c17c6bd57eff373574021f4
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108142066"
---
# <a name="azure-hdinsight-configuration-settings-reference"></a>Azure HDInsight 구성 설정 참조

Visual Studio Code용 Spark & Hive 도구 확장은 매우 다양하게 구성할 수 있습니다. 이 페이지에서는 사용할 수 있는 주요 설정에 대해 설명합니다.  

VS Code의 설정 작업에 대한 일반적인 정보는 [User and workspace settings](https://code.visualstudio.com/docs/getstarted/settings)(사용자 및 작업 영역 설정)를 참조하고, 미리 정의된 변수 지원에 대한 정보는 [Variables reference](https://code.visualstudio.com/docs/editor/variables-reference)(변수 참조)를 참조하세요.

## <a name="open-the-azure-hdinsight-configuration"></a>Azure HDInsight 구성 열기

1. 먼저 폴더를 열어 작업 영역 설정을 만듭니다.
2. **Ctrl+Shift+P** 를 누르거나 **보기** -> **명령 팔레트...** 로 이동하여 모든 명령을 표시합니다.
3. **구성 설정** 을 검색합니다.
4. 왼쪽 디렉터리에서 **확장** 을 확장하고 **HDInsight 구성** 을 선택합니다.

 ![hdi 구성 이미지](./media/HDInsight-config-for-vscode/HDInsight-config-for-vscode.png)

## <a name="general-settings"></a>일반 설정   

|  속성   | 기본값 | Description   |
| ----- | ----- |----- |
| HDInsight: Azure 환경 | Azure | Azure 환경 |
| HDInsight: 설문 조사 열기 링크 사용 안 함 | 선택 | HDInsight 설문 조사 열기 사용/사용 안 함 |
| HDInsight: Pyspark 설치 건너뛰기 사용 | 선택 취소됨 | pyspark 설치 건너뛰기 사용/사용 안 함 |
| HDInsight: 로그인 팁 사용 | 선택 취소됨 | 이 옵션을 선택하면 Azure에 로그인할 때 프롬프트 표시 |
| HDInsight: 이전 확장 버전 | 현재 확장의 버전 번호 표시 | 이전 확장 버전 표시|
| HDInsight: 결과 글꼴 패밀리 | -apple-system,BlinkMacSystemFont,Segoe WPC,Segoe UI,HelveticaNeue-Light,Ubuntu,Droid Sans,sans-serif | 결과 그리드의 글꼴 패밀리 설정, 편집기 글꼴을 사용하려면 빈 값으로 설정 |
| HDInsight: 결과 글꼴 크기 | 13 |결과 그리드의 글꼴 크기 설정, 편집기 크기를 사용하려면 빈 값으로 설정 |
| HDInsight 클러스터: 연결된 클러스터 | -- | 연결된 클러스터 URL. JSON 파일을 편집하여 설정할 수도 있음 |
| HDInsight Hive: 지역화 적용 | 선택 취소됨 | [선택 사항] VSCode의 구성된 로캘로 지역화하기 위한 구성 옵션(설정을 적용하려면 VSCode를 다시 시작해야 함)|
| HDInsight Hive: 헤더 포함 복사 | 선택 취소됨 | [선택 사항] 결과 뷰에서 결과를 복사하기 위한 구성 옵션 |
| HDInsight Hive: 새 줄 제거 복사 | 선택 | [선택 사항] 결과 뷰에서 여러 줄 결과를 복사하기 위한 구성 옵션 |
| HDInsight Hive › 형식: 열에서 열 정의 맞춤 | 선택 취소됨 | 열 정의를 맞춰야 하는지 여부 |
| HDInsight Hive › 형식: 데이터 형식 대/소문자 구분 | 없음 | 데이터 형식을 대문자, 소문자 또는 없음(서식 없음)으로 지정할지 여부 |
| HDInsight Hive › 형식: 키워드 대/소문자 구분 | 없음 | 키워드 형식을 대문자, 소문자 또는 없음(서식 없음)으로 지정할지 여부 |
| HDInsight Hive › 형식: Next 문 앞에 쉼표 표시 | 선택 취소됨 | 목록의 각 문 앞에 쉼표를 표시할지 여부(예: 끝에 ‘mycolumn1,’ 대신 ‘, mycolumn2’ 사용)|
| HDInsight Hive › 형식: 새 줄에 Select 문 참조 표시 | 선택 취소됨 | Select 문에서 개체에 대한 참조를 별도 줄에 분할할지 여부 예를 들어 ‘SELECT C1, C2 FROM T1’의 경우 C1과 C2를 별도의 줄에 표시함
| HDInsight Hive: 디버그 정보 로깅 | 선택 취소됨 | [선택 사항] VS Code 콘솔에 디버그 출력 로깅(도움말 -> 개발자 도구 토글) 
| HDInsight Hive: 메시지 기본값 열기 | 선택 | 메시지 창을 기본적으로 열어 두려면 true이고, 닫으려면 false|
| HDInsight Hive: 결과 글꼴 패밀리 | -apple-system,BlinkMacSystemFont,Segoe WPC,Segoe UI,HelveticaNeue-Light,Ubuntu,Droid Sans,sans-serif | 결과 그리드의 글꼴 패밀리 설정, 편집기 글꼴을 사용하려면 빈 값으로 설정 |
| HDInsight Hive: 결과 글꼴 크기 | 13 | 결과 그리드의 글꼴 크기 설정, 편집기 크기를 사용하려면 빈 값으로 설정 |
| HDInsight Hive › Csv로 저장: 헤더 포함 | 선택 | [선택 사항] true이면 결과를 CSV로 저장할 때 열 헤더 포함 |
| HDInsight Hive: 바로 가기 | -- | 결과 창과 관련된 바로 가기 |
| HDInsight Hive: 일괄 처리 시간 표시| 선택 취소됨 | [선택 사항] 개별 일괄 처리에 대한 실행 시간을 표시할지 여부 |
| HDInsight Hive: 분할 창 선택 | 다음 | [선택 사항] 새 결과 창이 열리는 열의 구성 옵션 |
| HDInsight 작업 제출: 클러스터 구성 | -- | 클러스터 구성 |
| HDInsight 작업 제출: Livy 구성 | -- | Livy 구성 POST/일괄 처리 |
| HDInsight Jupyter: 결과 추가| 선택 | 결과 창에 결과를 추가할지 여부, 추가하지 않으려면 선택 취소하고 표시 |
| HDInsight Jupyter: 언어 | -- | 언어별 기본 설정 |
| HDInsight Jupyter › 로그: 자세한 정보 표시 | 선택 취소됨 | 자세한 로깅을 사용할지 여부 |
| HDInsight Jupyter › Notebook: 시작 인수 | 항목을 추가할 수 있음 | ‘jupyter notebook’ 명령줄 인수. 각 인수는 배열에서 별도의 항목입니다. 전체 목록을 보려면 터미널 창에서 ‘jupyter notebook--help’를 입력하세요. |
| HDInsight Jupyter › Notebook: 시작 폴더 | ${workspaceRoot} |-- |
| HDInsight Jupyter: Python 확장 사용 | 선택 | pySpark 대화형 작업을 제출할 때 ms-python 확장의 Python-Interactive-Window를 사용합니다. 그러지 않으면 자체 jupyter 창을 사용합니다. |
| HDInsight Spark.NET: 7z | C:\Program Files\7-Zip | <7z.exe의 경로> |
| HDInsight Spark.NET: HADOOP_HOME | D:\winutils | <bin\winutils.exe의 경로> Windows OS만 해당 |
| HDInsight Spark.NET: JAVA_HOME | C:\Program Files\Java\jdk1.8.0_201\ | Java 홈의 경로|
| HDInsight Spark.NET: SCALA_HOME | C:\Program Files (x86)\scala\ | Scala 홈의 경로 |
| HDInsight Spark.NET: SPARK_HOME | D:\spark-2.3.3-bin-hadoop2.7\ | Spark 홈의 경로 |
| Hive: 쿼리 결과 탭 유지 | 선택 취소됨 | Hive PersistQueryResultTabs |
| Hive: 분할 창 선택 | 다음 | [선택 사항] 새 결과 창이 열리는 열의 구성 옵션 |
| Hive Interactive: 실행 가능한 폴더 복사 | 선택 취소됨 | hive interactive 서비스 런타임 폴더를 사용자의 tmp 폴더에 복사할지 여부 |
| Hql 대화형 서버: 래퍼 포트 | 13424 | Hive 대화형 서비스 포트 |
| Hql 언어 서버: 언어 래퍼 포트 | 12342 | 서버가 수신 대기하는 Hive 언어 서비스 포트입니다. |
| Hql 언어 서버: 최대 문제 수 | 100 | 서버에서 생성되는 최대 문제 수를 제어합니다. |
| Synapse Spark 컴퓨팅: Synapse Spark 컴퓨팅 Azure 환경 | 비어 있음 | synapse Spark 컴퓨팅 Azure 환경 |
| Synapse Spark 풀 작업 제출: Livy 구성 | -- | Livy 구성 POST/일괄 처리
| Synapse Spark 풀 작업 제출: Synapse Spark 풀 클러스터 구성 | -- | Synapse Spark 풀 구성 |


## <a name="next-steps"></a>다음 단계

- VSCode용 Azure HDInsight에 대한 자세한 내용은 [Visual Studio Code용 Spark & Hive 도구](/sql/big-data-cluster/spark-hive-tools-vscode)를 참조하세요.
- Visual Studio Code용 Spark & Hive 사용을 보여 주는 비디오는 [Spark & Hive for Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706)(Visual Studio Code용 Spark & Hive)를 참조하세요.