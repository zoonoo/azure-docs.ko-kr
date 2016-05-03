<properties
	pageTitle="Azure HDInsight에서 Spark를 사용하는 데이터 과학 개요 | Microsoft Azure"
	description="Spark MLlib 도구 키트를 통해 많은 기계 학습 모델링 기능을 이 분산 HDInsight 환경에서 사용할 수 있습니다."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/18/2016"
	ms.author="bradsev" />

# Azure HDInsight에서 Spark를 사용하는 데이터 과학 개요

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

## 소개

Azure HDInsight에서 Spark는 메모리 내 데이터를 HDInsight(Hadoop) 클러스터에서 분산 방식으로 처리하는 동안 해당 메모리를 조작할 수 있습니다. 따라서 이 Spark 클러스터는 속도도 빨라지고 용량도 증가합니다. 또한 Azure Blob(WASB)에 저장된 데이터를 변환, 필터링 및 시각화하기 위해 Spark SQL 대화형 쿼리를 실행할 수 있는 Spark 클러스터 상의 Jupyter Notebook에 대한 지원도 포함하고 있습니다.

[Spark](http://spark.apache.org/)는 메모리 내 처리를 지원하여 빅데이터 분석 응용 프로그램의 성능을 향상하는 오픈 소스 병렬 처리 프레임워크입니다. 속도, 간편한 사용 및 정교한 분석을 위해 Spark 처리 엔진이 빌드되었습니다. Spark는 메모리 내 계산 기능을 지원하여 기계 학습 및 그래프 계산의 반복 알고리즘에 적합합니다. [MLlib](http://spark.apache.org/mllib/)는 Spark의 확장성 있는 기계 학습 라이브러리입니다.

HDInsight Spark는 Azure에서 호스트하는 오픈 소스 Spark의 제품입니다. 이 연습에 제공되는 설치 단계와 코드는 HDInsight Spark용입니다. 그러나 이 코드는 일반적이므로 아무 Spark 클러스터에서나 작동할 것입니다. HDInsight Spark를 사용하지 않는 경우 클러스터 설치 및 관리 단계가 여기에 나오는 내용과 약간 다를 수 있습니다.

Spark MLlib 도구 키트를 통해 많은 기계 학습(ML) 모델링 기능을 이 분산 환경에서 사용할 수 있습니다. 메뉴에 연결된 토픽 컬렉션은 이진 분류 및 회귀 태스크를 통해 NYC 택시 여정 및 요금 2013 데이터 집합에 대해 작업하여 이 기능을 보여 주며 모델 결과를 WASB에 저장합니다. 모델 빌드는 로지스틱 및 선형 회귀, 임의 포리스트 및 점진적 향상 트리를 포함하고 있습니다. 또한 이러한 모델 결과를 사용하여 점수를 매기고 WASB에 저장된 다른 데이터 집합에 대해 이를 평가하는 방법도 보여 줍니다. 고급 토픽에서는 교차 유효성 검사 및 하이퍼 매개 변수 스위핑을 사용하여 모델을 학습할 수 있는 방법을 다룹니다.

이러한 토픽의 모델링 단계는 각 모델 유형을 학습, 평가, 저장 및 사용하는 방법을 보여 주는 코드를 포함하고 있습니다. Python을 사용하여 솔루션을 코딩하고 관련 포트를 보여 주었으며 Spark 클러스터에 설치된 Jupyter Notebook에서 실행할 수 있는 Python Notebook을 제공했습니다.


## 필수 조건

1\. 이러한 토픽을 시작하기 전에 Azure 구독이 있어야 합니다. 아직 가지고 있지 않은 경우 [Azure 무료 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.

2\. HDInsight Spark 클러스터를 만들려면 [시작하기: Azure HDInsight에서 Apache Spark 만들기](../hdinsight/hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md) 참조


[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## NYC 2013 Taxi 데이터

NYC Taxi Trip 데이터는 1억 7,300만 개가 넘는 개별 여정 및 각 여정의 요금으로 구성된 약 20GB의 압축된 CSV(쉼표로 구분된 값) 파일(압축되지 않은 경우 약 48GB)입니다. 각 여정 레코드는 승차 및 하차 위치, 익명 처리된 hack(기사) 면허증 번호 및 medallion(택시의 고유 ID) 번호를 포함합니다. 데이터는 2013년의 모든 여정을 포괄하며, 매월 다음 두 개의 데이터 집합으로 제공됩니다.

1. 'trip\_data' CSV 파일은 승객 수, 승차 및 하차 지점, 여정 기간, 여정 거리 등 여정 세부 정보를 포함합니다. 다음은 몇 가지 샘플 레코드입니다.

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. 'trip\_fare' CSV 파일은 지불 유형, 금액, 추가 요금 및 세금, 팁 및 통행료, 총 지불 금액 등 각 여정의 요금에 대한 세부 정보를 포함합니다. 다음은 몇 가지 샘플 레코드입니다.

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5


trip\_data와 trip\_fare를 조인할 고유 키는 medallion, hack\_licence 및 pickup\_datetime 필드로 구성됩니다.


우리는 이러한 파일의 0.1% 샘플을 선택하고 이들을 이 연습을 위해 입력된 데이터 집합으로 사용할 단일 데이터 집합으로 연결했습니다. trip\_data와 trip\_fare를 조인할 고유 키는 medallion, hack\_licence 및 pickup\_datetime 필드로 구성됩니다. 데이터 집합의 각 레코드는 NYC Taxi 여정을 나타내는 다음과 같은 특성을 포함하고 있습니다.


|필드| 간략한 설명
|------|---------------------------------
| medallion |익명 처리된 택시 medallion(고유 택시 id)
| hack\_license |	익명 처리된 Hackney 운전 면허 번호
| vendor\_id |	택시 공급 업체 id
| rate\_code | NYC 택시 요율
| store\_and\_fwd\_flag | 저장소 및 전달 플래그
| pickup\_datetime |	태운 날짜 및 시간
| dropoff\_datetime | 내린 날짜 및 시간
| pickup\_hour |	태운 시간
| pickup\_week |	태운 연간 누적 주
| weekday |	요일(범위 1-7)
| passenger\_count |	택시 여정의 승객 수
| trip\_time\_in\_secs | 여정 시간(초)
| trip\_distance | 주행한 여정 거리(마일 단위)
| pickup\_longitude | 태운 경도
| pickup\_latitude |	태운 위도
| dropoff\_longitude | 내린 경도
| dropoff\_latitude | 내린 위도
| direct\_distance |	태운 위치와 내린 위치 사이의 직접 거리
| payment\_type | 지불 유형(cas, 신용 카드 등)
| fare\_amount | 요금 금액
| surcharge | 할증금
| mta\_tax |	Mta 세금
| tip\_amount | 팁 금액
| tolls\_amount | 통행료 금액
| total\_amount | 총 금액
| tipped | 왕복 여정(아니오 또는 예에 대한 0/1)
| tip\_class | 팁 클래스(0: $0, 1: $0-5, 2: $6-10, 3: $11-20, 4: > $20)


## Spark 클러스터의 Jupyter Notebook에서 코드 실행 

Azure 포털에서 Jupyter Notebook을 시작할 수 있습니다: Spark 클러스터를 찾아서 클릭하여 상세 클러스터 관리 페이지로 이동합니다. 해당 페이지의 **Cluster 대시보드**에서 Spark 클러스터와 연결된 Jupyter Notebook에 대한 아이콘을 클릭할 수 있습니다.

![](./media/machine-learning-data-science-spark-overview/spark-jupyter-on-portal.png)

또한 **https://CLUSTERNAME.azurehdinsight.net/jupyter** *를 탐색하여 Jupyter Notebook에 액세스할 수도 있습니다. Notebook에 액세스하려면 관리자 계정에 대한 암호가 필요합니다.

![](./media/machine-learning-data-science-spark-overview/spark-jupyter-notebook.png)

Python을 탐색하여 Python 스크립트를 실행하는 기존 Notebook을 확인합니다. 미리 패키지된 Notebook의 몇 가지 예제가 들어 있는 디렉터리가 있을 것입니다. 이 토픽의 코드 샘플을 포함하고 있는 Notebook은 [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Python)에서 사용할 수 있습니다.

Notebook을 Github에서 Spark 클러스터의 Jupyter Notebook 서버에 직접 업로드할 수 있습니다. Jupyter의 홈 페이지에서 화면 오른쪽의 **업로드** 단추를 클릭합니다. 파일 탐색기가 열립니다. 여기서 Notebook의 Github(원시 콘텐츠) URL을 붙여넣고 **열기**를 클릭할 수 있습니다. **업로드** 단추를 다시 사용하여 Jupyter 파일 목록에서파일 이름을 확인합니다. 이 **업로드** 단추를 클릭합니다. 이제 Notebook을 가져왔습니다. 이 단계를 반복하여 이 연습에서 다음과 같은 Notebook을 업로드합니다.

1.	machine-learning-data-science-spark-data-exploration-modeling.ipynb
2.	machine-learning-data-science-spark-model-consumption.ipynb
3.	machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb

이제 다음을 수행할 수 있습니다.

- Notebook을 클릭하여 코드 확인
- **SHIFT-ENTER**를 눌러 각 셀 실행
- **셀** -> **실행**을 클릭하여 전체 Notebook 실행


## 다음 작업

이제 HDInsight Spark 클러스터를 설치하고 Jupyter Notebook을 업로드했으므로 이 세 가지 Notebook에 해당하고 교차 유효성 검사 및 하이퍼 매개 변수 스위핑을 사용하여 만들기, 사용 및 모델링하는 방법을 보여 주는 토픽을 통해 작업할 준비가 되었습니다.

**모델 만들기:** [Spark MLlib 도구 키트를 사용하여 데이터에 대한 이진 분류 및 회귀 모델 만들기](machine-learning-data-science-spark-data-exploration-modeling.md) 토픽을 통해 작업하여 여기서 점수를 매기고 평가할 기계 학습 모델을 만듭니다..

**모델 사용:** 이 토픽에서 만든 분류 및 회귀 모델의 점수를 매기고 평가하는 방법을 알아보려면 [Spark에서 만든 기계 학습 모델 점수 매기기 및 평가](machine-learning-data-science-spark-model-consumption.md)를 참조하세요.

<!---HONumber=AcomDC_0420_2016-->