<properties 
	pageTitle="HDInsight에서 Apache Spark의 알려진 문제 | Microsoft Azure" 
	description="HDInsight에서 Apache Spark의 알려진 문제" 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/22/2015" 
	ms.author="jgao"/>

# Azure HDInsight(Linux)에서 Apache Spark의 알려진 문제

이 문서는 Spark 공개 미리 보기에 대한 모든 알려진 문제를 추적합니다.

##Livy 누수 대화형 세션
 
**증상:**

Livy가 여전히 활성 상태인 대화형 세션(Ambari에서 또는 헤드 노드 0 가상 컴퓨터 재부팅으로 인해)으로 시작하는 경우 대화형 작업 세션이 손실됩니다. 이로 인해 새 작업은 수락된 상태에 멈출 수 있으며 시작할 수 없습니다.

**해결 방법:**

다음 절차에 따라 문제를 해결합니다.

1. 헤드 노드로 ssh합니다. 
2. 다음 명령을 실행하여 Livy를 통해 시작한 대화형 작업의 응용 프로그램 ID를 찾습니다. 

        yarn application –list

    작업이 명시적 이름이 지정되지 않은 Livy 대화형 세션으로 시작된 경우 기본 작업 이름은 Livy가 되며 Jupyter 노트북으로 시작된 Livy 세션의 경우 작업 이름은 remotesparkmagics\_*로 시작됩니다.

3. 다음 명령을 실행하여 해당 작업을 중지합니다.

        yarn application –kill <Application ID>

새 작업 실행이 시작됩니다.

##Spark 기록 서버가 시작되지 않음 

**증상:**
 
클러스터가 만들어진 후 Spark 기록 서버가 자동으로 시작되지 않습니다.

**해결 방법:**

Ambari에서 기록 서버를 수동으로 시작합니다.

##노트북 초기 시작이 예상보다 오래 걸리는 경우 

**증상:**

Jupyter 노트북에서 Spark Magic을 사용한 첫 번째 문의 경우 1분 이상이 걸릴 수 있습니다.

**해결 방법:**
 
해결 방법이 없습니다. 경우에 따라 1분이 걸립니다.

##코어/메모리 구성을 사용자 지정할 수 없음

**증상:**
 
Spark/Pyspark 커널에서 기본값과 다른 코어/메모리 구성을 지정할 수 없습니다.

**해결 방법:**
 
이 기능은 제공될 예정입니다.

##세션 만들기에서 Jupyter 노트북 시간 제한

**증상:**

Spark 클러스터에 리소스가 부족할 때 Jupyter 노트북에서 Spark 및 Pyspark 커널은 세션을 만들려고 할 때 시간 초과가 됩니다. 해결 방법:

1. 다음과 같은 방법으로 Spark 클러스터의 리소스를 확보합니다.

    - Close and Halt 메뉴로 이동하거나 노트북 탐색기에서 종료를 클릭하여 다른 Spark 노트북을 중지합니다.
    - YARN에서 다른 Spark 응용 프로그램을 중지합니다.

2. 시작하려는 노트북을 다시 시작합니다. 이제 세션을 만들기 위한 충분한 리소스를 사용할 수 있습니다.

##노트북 출력 결과 형식 지정 문제

**증상:**
 
Spark 및 Pyspark Jupyter 커널에서 셀을 실행한 후 노트북 출력 결과 형식이 잘못 지정됩니다. Spark 스택 추적 또는 다른 오류뿐만 아니라 셀 실행의 성공적인 결과를 포함합니다.

**해결 방법:**
 
이 문제는 향후 릴리스에서 해결될 것입니다.

##샘플 노트북의 오타
 
- **Python 노트북 4(사용자 지정 라이브러리를 사용하여 Spark로 로그 분석)**

    "wasb:///example/data/iislogparser.py(으)로 복사했다고 가정해 보겠습니다."는 "wasb:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py"(으)로 복사했다고 가정해 보겠습니다"이어야 합니다.

- **Python 노트북 5(Spark 기계 학습 - MLLib를 사용하여 음식 검사 데이터에 대한 예측 분석)**

    "신속한 시각화는 이러한 결과의 분포에 대한 이유를 알 수 있게 해줍니다."에는 실행되지 않는 일부 잘못된 코드가 포함되어 있습니다. 다음과 같이 편집해야 합니다.

        countResults = df.groupBy('results').count().withColumnRenamed('count', 'cnt').collect() 
        labels = [row.results for row in countResults] 
        sizes = [row.cnt for row in countResults] 
        colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral'] 
        plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors) plt.axis('equal') 
        
- **Python 노트북 5(Spark 기계 학습 - MLLib를 사용하여 음식 검사 데이터에 대한 예측 분석)**

    거짓 부정 비율 및 거짓 긍정 비율에 대한 최종 주석 메모는 각각 12.6% 및 16.0%입니다. 이러한 번호는 부정확합니다. 코드를 실행하여 참 백분율로 원형 그래프를 표시합니다.

- **Python 노트북 6 및 7**

    첫 번째 셀이 노트북을 종료할 때 호출되는 sc.stop() 메서드 등록에 실패합니다. 특정 상황에서 Spark 리소스 누수가 발생할 수 있습니다. 종료하기 전에 해당 노트북에서 가져오기 atexit; atexit.register(lambda: sc.stop())를 실행하여 이를 방지할 수 있습니다. 실수로 리소스가 누수된 경우 위의 지침에 따라 누수된 YARN 응용 프로그램을 중지합니다.
     
## Spark 로그 디렉터리에 대한 사용 권한 문제 

**증상:**
 
hdiuser가 spark-제출로 작업을 제출하는 경우 오류 java.io.FileNotFoundException: /var/log/spark/sparkdriver\_hdiuser.log(사용 권한 거부됨)가 있고 드라이버 로그가 작성되지 않습니다.

**해결 방법:**
 
1. hdiuser를 Hadoop 그룹에 추가합니다. 
2. 클러스터를 만든 후에 /var/log/spark에 777 권한을 제공합니다. 
3. Ambari를 사용하여 777 권한으로 디렉터리가 되도록 spark 로그 위치를 업데이트합니다.  
4. sudo로 spark-제출을 실행합니다. 

##참고 항목

- [개요: Azure HDInsight에서 Apache Spark(Linux)](hdinsight-apache-spark-overview.md)
- [시작: Azure HDInsight(Linux)에서 Apache Spark 프로비전 및 Spark SQL을 사용하여 대화형 쿼리 실행](hdinsight-apache-spark-jupyter-spark-sql.md)

<!---HONumber=AcomDC_1223_2015-->