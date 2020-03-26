---
title: Azure Machine Learning 디자이너에서 샘플 데이터 세트 사용
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 디자이너에 포함된 샘플 데이터 세트에 대해 자세히 알아보세요.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.date: 03/03/2020
ms.openlocfilehash: 943e23aa9d83df33bbe636ce7aee62e6e5a2950e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79037300"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-designer-preview"></a>Azure Machine Learning 디자이너에서 샘플 데이터 세트 사용(미리 보기)

Azure Machine Learning 디자이너(미리 보기)에서 새 파이프라인을 만들 때 다양한 샘플 데이터 세트가 기본적으로 포함됩니다. 이러한 샘플 데이터 세트는 디자이너 홈페이지의 샘플 파이프라인에서 사용됩니다. 

샘플 데이터 세트는 **데이터 세트**-**샘플** 범주에서 사용할 수 있습니다. 디자이너의 캔버스 왼쪽에 있는 모듈 팔레트에서 찾을 수 있습니다. 캔버스로 끌어와 이러한 데이터 세트를 파이프라인에서 사용할 수 있습니다.

## <a name="datasets"></a>데이터 세트


| 데이터 세트&nbsp;이름&nbsp;&nbsp;&nbsp;&nbsp;| 데이터 세트 설명 |
|-------------|:--------------------|
| 성인 인구 조사 소득 이진 분류 데이터 세트 | 조정 소득 지수가 100보다 큰 16세 이상 취업한 성인을 대상으로 한 1994 인구 조사 데이터베이스의 하위 집합입니다.<br/>**사용 현황** 인구 통계를 기반으로 사람을 분류하여 개인의 소득이 연간 50,000을 초과할지 예측합니다.<br/> **관련 조사**: Kohavi, R., Becker, B.,(1996). [UCI Machine Learning 리포지토리](https://archive.ics.uci.edu/ml) Irvine, CA: University of California, School of Information and Computer Science|
|자동차 가격 데이터(원시)|가격, 실린더 및 MPG 수와 같은 기능, 보험 위험 점수를 포함하여 상표 및 모델별 자동차에 대한 정보입니다.<br/> 위험 점수는 처음에 자동차 가격과 연관이 있습니다. 그런 다음 보험 회계사에게 기호화로 알려진 프로세스에서 실제 위험에 맞게 조정됩니다. +3 값은 자동차가 위험함을 나타내고 -3 값은 안전함을 나타냅니다.<br/>**사용량**:</b> 회귀 및 다변수 분류를 사용하여 기능별 위험 점수를 예측합니다.<br/>**관련 조사**:</b> Schlimmer, J.C. (1987). [UCI Machine Learning 리포지토리](https://archive.ics.uci.edu/ml) Irvine, CA: University of California, School of Information and Computer Science. |
| CRM 욕구 레이블 공유 |KDD Cup 2009 고객 관계 예측 챌린지의 레이블([orange_small_train_appetency.labels](http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels)).|
|CRM 이탈 레이블 공유|KDD Cup 2009 고객 관계 예측 챌린지의 레이블([orange_small_train_churn.labels](http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels)).|
|CRM 데이터 세트 공유 | 이 데이터는 KDD Cup 2009 고객 관계 예측 챌린지([orange_small_train.data.zip](http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip))에서 제공됩니다. <br/>데이터 세트에는 French Telecom 회사인 Orange의 고객 50,000명이 포함됩니다. 각 고객은 익명으로 처리되는 230개 기능을 가지며, 이 중 190개는 숫자이고 40개는 범주입니다. 기능이 매우 희박합니다. |
|CRM 상향 판매 레이블 공유|KDD Cup 2009 고객 관계 예측 챌린지의 레이블([orange_large_train_upselling.labels](http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels))|
|비행 지연 데이터|미국 운수부 TranStats 데이터 컬렉션에서 가져온 여객기 운항정시성 데이터([정시](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time))<br/>데이터 세트는 2013년 4월-10월 기간에 해당합니다. 디자이너로 업로드하기 전에 데이터 세트는 다음과 같이 처리되었습니다. <br/>- 데이터 세트는 미국 본토에서 비행이 가장 많은 공항 70곳만을 포함하도록 필터링되었습니다. <br/>- 취소된 비행은 15분 초과 지연으로 레이블이 지정되었습니다. <br/>- 우회 비행은 필터링되었습니다. <br/>- 다음 열이 선택되었습니다. Year, Month, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Canceled|
|독일 신용 카드 UCI 데이터 세트|german.data 파일을 사용한 UCI Statlog(독일 신용 카드) 데이터 세트([Statlog+German+Credit+Data](https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data))).<br/>데이터 세트는 특성 집합으로 설명된 사람을 낮은 신용 위험 또는 높은 신용 위험으로 분류합니다. 각 예제는 개인을 나타냅니다. 숫자 및 범주의 기능 20개와 이진 레이블(신용 위험 값)이 있습니다. 높은 신용 위험 항목의 레이블은 2이고, 낮은 신용 위험 항목의 레이블은 1입니다. 낮은 위험 예제를 높은 위험으로 잘못 분류한 비용은 1이지만, 높은 위험 예제를 낮은 위험으로 분류한 비용은 5입니다.|
|IMDB 영화 제목|이 데이터 세트에는 Twitter 트윗에서 평가된 영화에 대한 정보가 포함됩니다. (IMDB 영화 ID, 영화 제목, 장르 및 제작 연도) 이 데이터 세트에는 170,000 개의 영화가 있습니다. 데이터 세트는 논문 "S. Dooms, T. De Pessemier 및 L. Martens. MovieTweetings: a Movie Rating Dataset Collected From Twitter. Workshop on Crowdsourcing and Human Computation for Recommender Systems, CrowdRec at RecSys 2013"에서 소개되었습니다.|
|영화 등급|이 데이터 세트는 Movie Tweetings 데이터 세트의 확장된 버전입니다. 이 데이터 세트에는 잘 구성된 Twitter 트윗에서 추출한 170,000개의 영화 등급이 있습니다. 각 인스턴스는 트윗을 나타내며 사용자 ID, IMDB 영화 ID, 등급, 타임 스탬프, 해당 트윗에 대한 즐겨찾기 수, 해당 트윗의 리트윗 수 등과 같은 튜플입니다. 이 데이터 세트는 A. Said, S. Dooms, B. Loni 및 D. Tikk가 Recommender Systems Challenge 2014를 위해 제공했습니다.|
|날씨 데이터 세트|NOAA에서 제공한 시간별 지상 기상 관측([201304부터 201310까지의 병합된 데이터](https://az754797.vo.msecnd.net/data/WeatherDataset.csv)).<br/>기상 데이터는 2013년 4월-10월 기간에 공항 기상 관측소에서 수행된 관측을 포함합니다. 디자이너로 업로드하기 전에 데이터 세트는 다음과 같이 처리되었습니다.    <br/> - 날씨 관측소 ID가 해당 공항 ID에 매핑되었습니다.    <br/> - 비행이 가장 많은 공항 70곳과 연계되지 않은 기상 관측소가 필터링되었습니다.    <br/> - Date 열은 Year, Month 및 Day 열로 분할되었습니다.    <br/> - 다음 열이 선택되었습니다. AirportID, Year, Month, Day, Time, TimeZone, SkyCondition, Visibility, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter|
|Wikipedia SP 500 데이터 세트|데이터는 XML 데이터로 저장되는 각 S&P 500 회사의 자료에 따라 Wikipedia(https://www.wikipedia.org/) 에서 파생됩니다.    <br/>디자이너로 업로드하기 전에 데이터 세트는 다음과 같이 처리되었습니다.    <br/> - 각 특정 회사에 대한 텍스트 콘텐츠 추출    <br/> - 위치 형식 지정 제거    <br/> - 영숫자가 아닌 문자 제거    <br/> - 모든 텍스트를 소문자로 변환    <br/> - 알려진 회사 범주가 추가됨    <br/>일부 회사의 경우 문서를 찾을 수 없으므로 레코드 수가 500개 미만입니다.|

## <a name="next-steps"></a>다음 단계

* 다음을 사용하여 예측 분석 및 기계 학습의 기본 사항에 대해 알아봅니다. [자습서: 디자이너를 사용하여 자동차 가격 예측](tutorial-designer-automobile-price-train-score.md)을 참조하세요.

* 기존 [디자이너 샘플](samples-designer.md)을 수정하여 요구에 맞게 조정하는 방법을 알아봅니다.
