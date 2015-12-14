<properties 
	pageTitle="차량 원격 분석 솔루션 템플릿 PowerBI 대시보드 설정 지침 | Microsoft Azure" 
	description="Cortana Analytics의 기능을 사용하여 차량 상태 및 주행 습관에 대한 예측 가능한 통찰력 및 실시간 정보를 얻습니다." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="bradsev" />


# 차량 원격 분석 솔루션 템플릿 PowerBI 대시보드 설정 지침

이 **메뉴**는 이 플레이북의 장에 연결됩니다.

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]


차량 원격 분석 솔루션에서는 자동차 대리점, 자동차 제조업체 및 보험 회사가 Cortana Analytics의 기능을 이용하여 고객 경험, 연구 개발 및 마케팅 캠페인 분야의 개선을 추진하기 위해 차량 상태 및 운전 습관에 대한 실시간 및 예측 통찰력을 얻을 수 있습니다. 이 문서는 솔루션을 구독에 배포한 후 PowerBI 보고서 및 대시보드를 구성할 수 있는 방법에 관한 단계별 지침을 포함하고 있습니다.


## 필수 조건
1.	[https://gallery.cortanaanalytics.com/SolutionTemplate/Vehicle-Telemetry-Analytics-3](https://gallery.cortanaanalytics.com/SolutionTemplate/Vehicle-Telemetry-Analytics-3)로 이동하여 차량 원격 분석 솔루션을 배포합니다.  
2.	[Microsoft Power BI 데스크톱 설치](http://www.microsoft.com/download/details.aspx?id=45331)
3.	[Azure 구독](https://azure.microsoft.com/pricing/free-trial/). Azure 구독이 없으면 Azure 무료 구독 시작
4.	Microsoft PowerBI 계정
	

## Cortana Analytics 제품군 구성 요소
차량 원격 분석 솔루션 템플릿의 일부로 다음 Cortana Analytics 서비스가 구독에 배포됩니다.

- **이벤트 허브** - 수백만 개의 차량 원격 분석 이벤트를 Azure에 수집합니다.
- **스트림 분석** - 차량 상태에 대한 실시간 통찰력을 얻고 다양한 일괄 처리 분석을 위해 해당 데이터를 장기간용 저장소에 보관합니다.
- **기계 학습** - 실시간으로 이상을 탐지하고 일괄 처리하여 예측 가능한 통찰력을 얻습니다.
- **HDInsight** - 대규모로 데이터를 변환하는 데 이용됩니다.
- **Data Factory** - 일괄 처리 파이프라인의 오케스트레이션, 예약, 리소스 관리 및 모니터링을 처리합니다.

**Power BI** - 실시간 데이터 및 예측 분석 시각화를 위해 이 솔루션을 다양한 대시보드에 제공합니다.

이 솔루션은 두 개의 서로 다른 데이터 소스: **시뮬레이션된 차량 신호 및 진단 데이터 집합** 및 **차량 카탈로그**를 사용합니다.

차량 텔레매틱스 시뮬레이터가 이 솔루션의 일부로 포함되어 있습니다. 이 기능은 지정된 시기에 차량 상태 및 운전 패턴에 해당하는 신호를 내보냅니다.

차량 카탈로그는 모델 매핑에 대한 VIN이 포함된 참조 데이터 집합입니다.


## PowerBI 대시보드 준비

### 배포

배포가 완료되면 모든 구성 요소가 녹색으로 표시된 아래 다이어그램을 볼 수 있습니다.

- 녹색 노드의 오른쪽 위에 있는 화살표를 클릭하여 이러한 모든 구성 요소가 성공적으로 배포되었는지 여부를 확인하는 해당 서비스로 이동합니다.
- 차량 텔레매틱스 노드의 오른쪽 위에 있는 화살표를 클릭하여 데이터 시뮬레이터 패키지를 다운로드합니다. 파일을 저장하고 컴퓨터에 로컬로 추출합니다. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/1-deployed-components.png)

이제 차량 상태 및 운전 습관에 대한 실시간 및 예측 통찰력을 얻기 위해 시각화 효과가 풍부한 PowerBI 대시보드를 구성할 준비가 되었습니다. 모든 보고서를 만들고 대시보드를 구성하는 데 약 45분에서 한 시간쯤 걸립니다.


### Power BI에 대한 실시간 대시보드 설정

**시뮬레이션된 데이터 생성**

1. 로컬 컴퓨터에서 차량 텔레매틱스 시뮬레이터 패키지를 추출한 폴더로 이동합니다. 

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/2-vehicle-telematics-simulator-folder.png)

2.	응용 프로그램 ***CarEventGenerator.exe***를 실행합니다.
3.	이 기능은 지정된 시기에 차량 상태 및 운전 패턴에 해당하는 신호를 내보냅니다. 이는 배포의 일부로 구성된 Azure 이벤트 허브 인스턴스에 배포됩니다.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/3-vehicle-telematics-diagnostics.png)
	 
실시간 대시보드 응용 프로그램 시작 - PowerBI에는 솔루션의 일부로 실시간 대시보드를 생성하는 응용 프로그램이 포함되어 있습니다. 이 응용 프로그램은 스트림 분석이 이벤트를 지속적 패턴으로 게시하는 이벤트 허브 인스턴스를 수신합니다. 이 응용 프로그램이 수신한 모든 이벤트에 대해 기계 학습 요청-응답 점수 매기기 끝점을 사용하여 데이터를 처리하며 시각화를 위해 결과로 얻어진 데이터 집합을 PowerBI 푸시 API에 게시합니다. 응용 프로그램을 다운로드하려면:

1.	다이어그램 보기의 PowerBI 노드를 클릭하고 곡성 창에서 **실시간 대시보드 응용 프로그램 다운로드**’ 링크를 클릭합니다.

2.	압축을 풀고 로컬 컴퓨터에 저장

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/4-real-time-dashboard-application.png)

3.	응용 프로그램 ‘RealtimeDashboardApp.exe’ 실행
4.	유효한 Power BI 자격 증명을 제공하고 로그인하고 **동의** 클릭
	
![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/5-sign-into-powerbi.png)
	
![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/6-powerbi-dashboard-permissions.png)
 
## 실시간 Power BI 보고서 작성 

### PowerBI 보고서 구성

새 데이터 집합이 Power BI에 생성됩니다. **ConnectedCarsRealtime** 데이터 집합을 클릭합니다.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/7-select-connected-cars-realtime-dataset.png)

**Ctrl + s**를 사용하여 빈 보고서를 저장합니다.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/8-save-blank-report.png)

보고서 이름 *차량 원격 분석 실시간 보고서*를 제공합니다.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/9-provide-report-name.png)


### 실시간 보고서 작성: *운행 중인 차량*
  
**1 페이지**를 두 번 클릭하고 “운행 중인 차량”으로 이름 변경 ![연결된 자동차 - 운행 중인 차량](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4a.png)

**필드**에서 **vin**을 선택하고 시각화 유형을 **“카드”**로 선택합니다.

카드 시각화는 그림에 나와 있는 것처럼 생성됩니다. ![연결된 자동차 - vin 선택](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4b.png)

빈 영역을 클릭하여 새 시각화를 추가합니다.

필드에서 **시/군/구** 및 **vin**을 선택합니다. 시각화를 **"맵"**으로 변경합니다. **vin**을 값 영역으로 끕니다. 필드에서 **시/군/구**를 **범례** 영역으로 끕니다. ![연결된 자동차 - 카드 시각화](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4c.png)
  
**시각화**에서 **형식**을 선택하고 **제목**을 클릭한 다음 텍스트를 **“시/군/구별 운행 중인 차량”**으로 변경합니다. ![연결된 자동차 - 도시별 운행 중인 차량](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4d.png)

최종 시각화는 그림과 같이 표시됩니다. ![연결된 자동차 - 최종 시각화](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4e.png)

빈 영역을 클릭하여 새 시각화를 추가합니다.

**시/군/구** 및 **vin**을 선택하고 시각화 유형을 **묶은 세로 막대형 차트**로 변경합니다. **축 영역**의 **시/군/구** 필드 및 **값 영역**의 **vin** 필드 확인

**“vin 개수”**별로 차트 정렬 ![연결된 자동차 - vin 개수](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4f.png)

차트 **제목**을 **“시/군/구별 운행 중인 차량”**으로 변경

**형식** 섹션을 클릭한 다음 **데이터 색**을 선택하고 **모두 표시**에 대한 **“켜기”** 클릭 ![연결된 자동차 - 모든 데이터 색 표시](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4g.png)

색 아이콘을 클릭하여 개별 시/군/구의 색을 변경합니다. ![연결된 자동차 - 색 변경](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4h.png)

빈 영역을 클릭하여 새 시각화를 추가합니다.

시각화에서 **묶은 세로 막대형 차트** 시각화를 선택하고 **시/군/구** 필드를 **축** 영역에, **모델**을 **범례** 영역에, **vin**을 **값** 영역으로 끕니다. ![연결된 자동차 - 묶은 세로 막대형 차트](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4i.png) ![연결된 자동차 - 렌더링](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4j.png)
  
이 페이지의 모든 시각화를 페이지에 나와 있는 것처럼 다시 배열합니다. ![연결된 자동차 - 시각화](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4k.png)

### 새 보고서 추가: *유지보수가 필요한 차량*
  
![추가](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png)을 클릭하여 새 보고서를 추가하고 **“유지보수가 필요한 차량”**으로 이름 변경합니다.

![연결된 자동차 - 유지보수가 필요한 차량](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4l.png)

**vin** 필드를 선택하고 시각화 유형을 **카드**로 변경합니다. ![연결된 자동차 - Vin 카드 시각화](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4m.png)

데이터 집합에 **“MaintenanceLabel”**이라는 필드가 있습니다. 이 필드는 **“0”** 및 **“1”** 값을 가지고 있습니다. "1" 값은 유지보수가 필요한 차량을 의미합니다.

유지보수가 필요한 차량 데이터를 표시하기 위해 **페이지 수준** 필터를 추가할 것입니다.

1. **“MaintenanceLabel”** 필드를 **페이지 수준 필터**로 끕니다. ![연결된 자동차 - 페이지 수준 필터](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n1.png)  

2. MaintenanceLabel 페이지 수준 필터 아래쪽에 있는 **기본 필터링**을 클릭합니다. ![연결된 자동차 - 기본 필터링](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n2.png)

3.  필터 값을 **"1"**로 설정 ![연결된 자동차 - 필터 값](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4n3.png)
  
>[AZURE.NOTE]**비주얼 수준 필터**라는 차트 수준에서 필터를 설정할 수 있습니다. 이는 특정 시각화에만 적용할 수 있습니다. 페이지 수준 필터는 보고서의 모든 시각화에 적용됩니다.

빈 영역을 클릭하여 새 시각화를 추가합니다.

시각화에서 **묶은 세로 막대형 차트** 선택 ![연결된 자동차 - Vind 카드 시각화](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4o.png) ![연결된 자동차 - 묶은 세로 막대형 차트](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4p.png)

**모델** 필드를 **축** 영역으로, **Vin**을 **값** 영역으로 끕니다. 그런 다음 시각화를 **vin 개수**별로 정렬합니다. 차트 **제목**을 **“모델별 유지보수가 필요한 차량”**으로 변경

**vin** 필드를 **시각화** 탭의 **필드** ![필드](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4field.png) 섹션에 있는 **색 채도**로 끌기 ![연결된 자동차 - 색 채도](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4q.png)

**형식** 섹션에서 시각화의 **데이터 색** 변경 최소 대상 색: **F2C812** 최대 대상 색: **FF6300** ![연결된 자동차 - 색 변경](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4r.png) ![연결된 자동차 - 새 시각화 색](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4s.png)

빈 영역을 클릭하여 새 시각화를 추가합니다.

시각화에서 **묶은 세로 막대형 차트**를 선택하고 **vin** 필드를 **값** 영역으로, **시/군/구** 필드를 **축** 영역으로 끕니다. **“vin 개수”**별로 차트를 정렬합니다. 차트 **제목**을 **“시/군/구별 유지보수가 필요한 차량”**으로 변경 ![연결된 자동차 - 도시별 유지보수가 필요한 차량](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4t.png)

빈 영역을 클릭하여 새 시각화를 추가합니다.

시각화에서 **여러 행 카드** 시각화를 선택하고 **모델** 및 **vin**을 **필드** 영역으로 끕니다. ![연결된 자동차 - 여러 행 카드](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4u.png)

모든 시각화를 다시 배열하면 최종 보고서는 아래와 같이 표시됨 ![연결된 자동차 - 여러 행 카드](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4v.png)

### 새 보고서 추가: *차량 상태 통계*
  
![추가](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4add.png)을 클릭하여 새 보고서를 추가하고 **“차량 상태 통계”**로 이름 변경합니다.

시각화에서 **계기** 시각화를 선택한 다음 **속도** 필드를 **값, 최소값, 최대값** 영역으로 끕니다. ![연결된 자동차 - 여러 행 카드](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4w.png)

**값 영역**에서**속도**의 기본 집계를 **평균**으로 변경

**최소값 영역**에서**속도**의 기본 집계를 **최소값**으로 변경

**최대값 영역**에서**속도**의 기본 집계를 **최대값**으로 변경

![연결된 자동차 - 여러 행 카드](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4x.png)

**계기 제목**을 **“평균 속도”**로 이름 변경
 
![연결된 자동차 - 계기](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4y.png)

빈 영역을 클릭하여 새 시각화를 추가합니다.

마찬가지로 **평균 엔진 오일**, **평균 연료** 및 **평균 엔진 온도**에 대한 **계기**를 추가합니다.

**“평균 속도”** 계기에서 위의 각 단계에 따라 각 계기에서 필드의 기본 집계를 변경합니다.

![연결된 자동차 - 계기](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4z.png)

빈 영역을 클릭하여 새 시각화를 추가합니다.

시각화에서 **줄 및 묶은 세로 막대형 차트**를 선택한 다음 **시/군/구** 필드를 **공유 축**으로, **속도**, **tirepressure 및 engineoil 필드**를 **열 값** 영역으로 끌고 해당 집계 유형을 **평균**으로 변경합니다.

**engineTemperature** 필드를 **줄 값** 영역으로 끌고 집계 유형을 **평균**으로 변경합니다.

![연결된 자동차 - 시각화 필드](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4aa.png)

차트 **제목**을 **“평균 속도, 타이어 압력, 엔진 오일 및 엔진 온도”**로 변경

![연결된 자동차 - 시각화 필드](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4bb.png)

빈 영역을 클릭하여 새 시각화를 추가합니다.

시각화에서 **트리맵** 시각화를 선택하고 **모델** 필드를 **그룹** 영역으로, **MaintenanceProbability** 필드를 **값** 영역으로 끕니다.

차트 **제목**을 **“유지보수가 필요한 차량 모델”**로 변경

![연결된 자동차 - 차트 제목 변경](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4cc.png)

빈 영역을 클릭하여 새 시각화를 추가합니다.

시각화에서 **100% 누적 가로 막대형 차트**를 선택하고 **시/군/구** 필드를 **축** 영역으로, **MaintenanceProbability**, **RecallProbability** 필드를 **값** 영역으로 끌어옴

![연결된 자동차 - 새 시각화 추가](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4dd.png)

**형식**을 클릭하고 **데이터 색**을 선택하고 **MaintenanceProbability** 색을 값 **“F2C80F”**로 변경

차트의 **제목**을 **“시/군/구별 차량 유지보수 및 리콜 확률”**로 변경

![연결된 자동차 - 새 시각화 추가](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ee.png)

빈 영역을 클릭하여 새 시각화를 추가합니다.

시각화에서 **영역형 차트**를 선택하고 **모델** 필드를 **축** 영역으로, **engineOil, tirepressure, speed 및 MaintenanceProbability** 필드를 **값** 영역으로 끕니다. 해당 집계 유형을 **“평균”**으로 변경합니다.

![연결된 자동차 - 집계 유형 변경](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ff.png)

차트 제목을 **“모델별 엔진 오일, 타이어 압력, 속도 및 유지보수 확률 평균”**으로 변경

![연결된 자동차 - 차트 제목 변경](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4gg.png)

빈 영역을 클릭하여 새 시각화를 추가합니다.

1. 시각화에서 **분산형 차트** 시각화 선택
2. **모델** 필드를 **세부 정보** 및 **범례** 영역으로 끌어옴 
3. **연료** 필드를 **X 축** 영역으로 끌고 집계를 평균으로 변경
4. **engineTemparature**를 **Y 축 영역**으로 끌고 집계를 **평균**으로 변경
5. **vin** 필드를 **크기** 영역으로 끌어옴

![연결된 자동차 - 새 시각화 추가](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4hh.png)

차트 **제목**을 **“모델별 연료, 엔진 온도 평균”**으로 변경

![연결된 자동차 - 차트 제목 변경](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4ii.png)

최종 보고서는 아래와 같이 표시됩니다.

![연결된 자동차 - 최종 보고서](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-3.4jj.png)

### 보고서의 시각화를 실시간 대시보드에 고정
  
위 보고서의 시각화를 섹션 2 - **Power BI 대시보드 설정**에서 만든 실시간 대시보드에 고정합니다.

대시보드는 아래와 같이 표시되어야 합니다.

![연결된 자동차 - 대시보드](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/connected-cars-4.0.png)


##  Power BI 일괄 처리 대시보드 설정

>[AZURE.ALERT]종단 간 배치 처리 파이프라인이 실행을 완료하는 데 약 2시간이 걸립니다. 다음 단계를 계속하기 전에 잠시 기다려 주십시오.

**PowerBI 디자이너 파일 다운로드** • PowerBI 디자이너 파일은 배포의 일부로 포함되어 있음 • 다이어그램 보기에서 PowerBI 노드를 클릭하고 속성 창에서 ‘PowerBI 디자이너 파일 다운로드’ 링크를 클릭 • 로컬로 저장

**PowerBI 보고서 구성** • PowerBI 데스크톱을 사용하여 디자이너 파일 ‘VehicleTelemetryAnalytics - 데스크톱 Report.pbix’를 엽니다. 아직 없는 경우 - http://www.microsoft.com/ko-KR/download/details.aspx?id=45331에 PowerBI 데스크톱 설치 • **쿼리 편집**을 클릭합니다.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/10-edit-powerbi-query.png)

[AZURE.NOTE]Azure SQL 데이터베이스의 **이름/암호**는 배포 설정 중에 제공한 것입니다.

- **소스**를 두 번 클릭합니다.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/11-set-powerbi-source.png)

- 서버 연결 문자열 날짜 지정
- **데이터베이스**를 *connectedcar*로 설정합니다.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/12-set-powerbi-database.png)

- **확인**을 클릭합니다.
- 기본적으로 선택된 **Windows 자격 증명** 탭을 확인하고 오른쪽의 **데이터베이스**를 클릭하여 **데이터베이스 자격 증명**으로 변경합니다.
- **사용자 이름** 및 **암호** 제공

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/13-provide-database-credentials.png)

- **연결** 클릭
- 오른쪽 창에 있는 남은 쿼리 3개 각각에 대해 위 단계를 반복하고 데이터 소스 연결 세부 정보를 업데이트합니다.
- **닫기 및 로드**를 클릭합니다. Power BI 데스크톱 파일 데이터 집합은 SQL Azure 데이터베이스 테이블에 연결됩니다.
- Power BI 데스크톱 파일을 **닫습니다**.

![](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/14-close-powerbi-desktop.png)

- **저장** 단추를 클릭하여 변경 내용을 저장합니다. 
 
***powerbi.com*에 업로드** powerbi.com을 찾아서 로그인합니다. ![차량 원격 분석 PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/15-vehicle-telemetry-powerbi-site.png)

1.	데이터 가져오기 클릭  
2.	Power BI 데스크톱 파일을 업로드합니다.  
3.	업로드하려면 **데이터 가져오기 -> 파일 가져오기 -> 로컬 파일** 클릭  
4.	**“연결된 자동차 – 데스크톱Report.pbix”** 탐색  
5.	파일이 업로드된 후 Power BI 작업 공간으로 다시 이동합니다.  

	데이터 집합, 보고서 및 빈 대시보드가 생성됩니다.

	>**참고:** 대시보드 옆의 더하기 아이콘을 클릭하여 빈 대시보드를 만들어야 할 수 있습니다.

**대시보드에 차트 고정** Power BI에서, 위에서 만든 빈 대시보드를 클릭한 다음 보고서 섹션으로 이동하여 새로 업로드한 보고서를 클릭합니다.

![차량 원격 분석 PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard1.png)


**참고로 보고서에는 6 페이지가 있음:** 1페이지: 차량 밀도 2페이지: 실시간 차량 상태 3페이지: 적극적으로 운전한 차량 4페이지: 리콜된 차량 5페이지: 연료 효율이 높게 운전한 차량 6페이지: Contoso 로고

![연결된 자동차 PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard2.png)
 

**3페이지에서**, 다음을 고정 1. VIN 개수 ![연결된 자동차 PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard3.png) 2. 모델별 적극적으로 운전한 차량 - 폭포 차트 ![차량 원격 분석 - 차트 4 고정](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard4.png)

**5페이지에서**, 다음을 고정 1. vin 개수 ![차량 원격 분석 - 차트 5 고정](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard5.png) 2. 모델별 연료 효율이 좋은 차량: 묶은 세로 막대형 차트 ![차량 원격 분석 - 차트 6 고정](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard6.png)

**4페이지에서**, 다음을 고정

1.	vin 개수 ![차량 원격 분석 - 차트 7 고정](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard7.png) 

2.	시/군/구, 모델별 리콜된 차량: 트리맵 ![차량 원격 분석 - 차트 8 고정](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard8.png)

**6페이지에서**, 다음을 고정

1.	Contoso 모터 로고 ![차량 원격 분석 - 차트 9 고정](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard9.png)

**대시보드 구성**

1.	대시보드로 이동 ![차량 원격 분석 - 대시보드 1 구성](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard1.png)

2.	각 차트 위를 마우스로 가리키고 아래의 완전한 대시보드 이미지에 제공된 이름에 따라 이름을 변경합니다. 또한 아래 대시보드보드와 같이 표시되도록 차트를 이리저리 움직입니다. ![차량 원격 분석 - 대시보드 2 구성](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-organize-dashboard2.png)

![차량 원격 분석 PowerBI.com](./media/cortana-analytics-playbook-vehicle-telemetry-powerbi-dashboard/vehicle-telemetry-dashboard.png)

<!---HONumber=AcomDC_1203_2015-->