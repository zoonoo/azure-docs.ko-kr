<properties title="Azure Machine Learning Sample: Flight delay prediction" pageTitle="기계 학습 샘플: 비행 지연 예측 | Azure" description="예약된 항공편이 15분 넘게 지연될지 여부를 예측하는 모델을 개발할 샘플 Azure 기계 학습 실험입니다." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Azure 기계 학습 샘플: 비행 지연 예측

>[AZURE.NOTE]
>이 모델과 관련된 [샘플 실험] 및 [샘플 데이터 집합]은 기계 학습 스튜디오에서 사용할 수 있습니다. 자세한 내용은 다음을 참조하세요.
[샘플 실험]: #sample-experiment
[샘플 데이터 집합]: #sample-datasets


## 문제 설명  ##

기록 운항정시성 및 날씨 데이터를 사용하여 예정된 여객기의 도착이 15분 넘게 지연되는지 예측합니다. 

## 데이터 ##

**미국 교통국(Department of Transportation)에서 제공하는 TranStats 데이터 모음의 여객기 정시성 데이터:**  [http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time](http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)  

데이터 집합은 2013년 4월-10월 데이터를 포함합니다. Azure 기계 학습 스튜디오로 업그레이드하기 전에:  

- 데이터 집합은 미국 본토에서 비행이 가장 많은 공항 70곳을 포함하도록 필터링되었습니다.  
- 다음 열이 선택되었습니다. 'Year', 'Month', 'DayofMonth', 'DayOfWeek','Carrier', 'OriginAirportID','DestAirportID','CRSDepTime','DepDelay','DepDel15', 'CRSArrTime','ArrDelay','ArrDel15','Cancelled'  
- 취소된 비행은 15분 초과 지연으로 레이블이 지정되었습니다.  
- 우회 비행은 필터링되었습니다.  

**NOAA의 매시간 지상 기상 관측:** http://cdo.ncdc.noaa.gov/qclcd_ascii/  

날씨 데이터는 각 공항과 연계된 기상 관측소의 2013년 4월-10월 관측을 포함합니다. 기계 학습 스튜디오로 업그레이드하기 전에: 

- 기상 관측소 ID가 해당 공항 ID에 매핑되었습니다. 
- 비행이 가장 많은 공항 70곳과 연계되지 않은 기상 관측소가 필터링되었습니다.  
- Date 열은 Year, Month 및 Day 열로 분할되었습니다.  
- 다음 열이 선택되었습니다. 'AirportID', 'Year','Month','Day', 'Time', 'TimeZone', 'SkyCondition', 'Visibility','WeatherType','DryBulbFarenheit', 'DryBulbCelsius',  'WetBulbFarenheit',  'WetBulbCelsius',  'DewPointFarenheit','DewPointCelsius',  'RelativeHumidity',  'WindSpeed',  'WindDirection',  'ValueForWindCharacter','StationPressure',  'PressureTendency',  'PressureChange',  'SeaLevelPressure',  'RecordType',  'HourlyPrecip', 'Altimeter'  

## 모델 ##

**비행 데이터 전처리**  

먼저 가능한 누설 대상인 DepDelay, DepDel15, ArrDelay, Cancelled 열은 데이터 집합에서 제외됩니다.  

'DayOfWeek','OriginAirportID' 및 'DestAirportID' 열은 범주 특성을 나타냅니다. 그러나 이러한 열은 정수 값이므로 처음에 연속 수로 구문 분석되고 메타데이터 편집기를 사용하여 범주로 변환되어야 합니다.  

예정 출발 시간을 조인 키의 하나로 사용하여 매시간 날씨 레코드와 비행 레코드를 조인하려고 합니다. 이 작업을 하기 위해 CSRDepTime 열은 가장 가까운 이전 시간으로 반내림됩니다.  

**날씨 데이터 전처리**  

먼저 값이 대부분 누락된 열이 제외됩니다. 다음 열이 포함됩니다.  

- 모든 문자열 값 열  
- ValueForWindCharacter,WetBulbFarenheit,WetBulbCelsius,PressureTendency,PressureChange,SeaLevelPressure,StationPressure  

그리고 나서 누락 값 스크러버가 나머지 열에 적용됩니다.  

기상 관측 시간은 가장 가까운 이후 1시간으로 반올림되므로 비행 예정 출발 시간과 동등 조인될 수 있습니다. 예정 비행 시간과 기상 관측 시간은 반대 방향으로 돌아갑니다. 이를 통해 모델에는 비행 시간을 기준으로 과거에 발생한 기상 관측만 사용됩니다.  

**데이터 집합 조인**

비행 레코드는 비행 출발지와 비행 도착지의 날씨 데이터와 조인됩니다.  

날씨 데이터는 로컬 시간으로 보고되지만 출발지와 도착지는 시간대가 다를 수 있습니다. 따라서 예정 출발 시간 및 기상 관측 시간에서 시간대 열을 빼서 시간대 차이를 조정합니다.  

**학습 및 유효성 검사 샘플 준비**  

학습 및 유효성 검사 샘플은 데이터를 학습에 대한 4월-9월 레코드 및 유효성 검사에 대한 10월 레코드로 분할하여 만듭니다. Year 및 Month 열은 데이터 집합에서 제거됩니다. 

또한 학습 데이터는 동일 높이 범주화로 양자화되고 같은 범주화가 유효성 검사 데이터에 적용됩니다.  

**모델 학습 및 유효성 검사** 

2클래스 향상된 의사 결정 트리 모델은 학습 샘플에 대해 학습됩니다. 모델은 10배 임의 매개 변수 스윕을 사용하여 가장 좋은 AUC를 위해 최적화됩니다. 비교를 위해 2클래스 로지스틱 회귀 모델이 같은 방식으로 최적화됩니다.  

학습된 모델은 유효성 검사 집합에 대해 점수가 매겨지고 평가 모델 모듈은 서로에 대해 모델 품질을 분석하는 데 사용됩니다. 

**후처리** 

결과를 더 쉽게 분석하도록 공항 ID는 사람이 읽을 수 있는 공항 이름 및 위치와 조인됩니다. 

## 결과 ##

향상된 의사 결정 트리 모델에서는 유효성 검사 집합에 대해 AUC가 0.697이고, 이 값은 AUC가 0.675인 로지스틱 회귀 모델보다 약간 더 효율적입니다. 



## 샘플 실험

이 모델과 연결된 다음 샘플 실험은 기계 학습 스튜디오의 **샘플** 탭 아래 **실험** 섹션에 있습니다.

> **샘플 실험 - 비행 지연 예측 - 개발**

## 샘플 데이터 집합

이 실험에서 사용되는 다음 샘플 데이터 집합은 기계 학습 스튜디오의 **저장된 데이터 집합** 아래 모듈 팔레트에 있습니다.

###공항 코드 데이터 집합
[AZURE.INCLUDE [machine-learning-sample-dataset-airport-codes](../includes/machine-learning-sample-dataset-airport-codes.md)]

###비행 지연 데이터
[AZURE.INCLUDE [machine-learning-sample-dataset-flight-delays-data](../includes/machine-learning-sample-dataset-flight-delays-data.md)]

###날씨 데이터 집합
[AZURE.INCLUDE [machine-learning-sample-dataset-weather-dataset](../includes/machine-learning-sample-dataset-weather-dataset.md)]

