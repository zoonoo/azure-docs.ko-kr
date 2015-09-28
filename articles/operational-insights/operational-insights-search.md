<properties
   pageTitle="Operational Insights의 데이터 검색"
   description="Microsoft Azure Operational Insights의 로그 검색을 사용하여 검색 중인 데이터를 찾을 수 있습니다."
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="09/10/2015"
   ms.author="banders" />

# Operational Insights의 데이터 검색

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Microsoft Azure Operational Insights의 핵심은 사용자 환경 내에서 여러 원본의 모든 컴퓨터 데이터를 서로 연결하고 결합할 수 있게 하는 로그 검색 기능입니다. 솔루션도 로그 검색에서 제공되어 특정 문제 영역 주위에 피벗된 메트릭을 가져옵니다.

검색 페이지에서, 쿼리를 만든 다음 검색 시 패싯 컨트롤을 사용하여 결과를 필터링할 수 있습니다. 변환, 필터링 및 결과를 보고하는 고급 쿼리를 만들 수도 있습니다.

일반적인 로그 검색 쿼리는 대부분의 솔루션 페이지에 표시됩니다. Operational Insights 콘솔에서는 로그 검색을 사용하여 타일을 클릭하거나 다른 항목을 확인하여 항목에 대한 세부 정보를 볼 수 있습니다.

이 자습서에서는 로그 검색을 사용하는 경우 모든 기본 사항을 설명하는 예제를 살펴봅니다.

간단하고 실용적인 예제부터 시작하고 이를 확장하므로 구문을 사용하여 데이터에서 원하는 통찰력을 추출하는 방법에 대한 실용적인 사용 사례를 이해할 수 있습니다.

Operational Insights에서 로그 검색을 수행할 때 다음과 같은 기술을 사용합니다.

- 기본 필터 사용
- 추가 필터 사용
- 검색 결과 조작
- 측정값 명령 사용
- 측정값 명령과 최대 및 최소 함수 사용
- 측정값 명령과 평균 함수 사용
- Where 명령 사용

검색 기술을 숙지한 후에 [구문 참조 검색](#search-syntax-reference) 및 [필드 및 패싯 참조 검색](#search-field-and-facet-reference)을 검토할 수 있습니다.

## 기본 필터 사용

먼저 알아야 할 점은 검색 쿼리의 첫번째 부분이며 "|" 세로줄 문자 앞에는 항상 *필터*가 있습니다. TSQL에서 WHERE 절로 생각할 수 있으며 이는 Operational Insights 데이터 저장소에서 *어떤* 데이터의 하위 집합을 가져올지 결정합니다. 데이터 저장소에서 검색은 대개 추출하려는 데이터의 특성을 지정하므로 쿼리가 WHERE 절과 함께 시작하는 것이 자연스럽습니다.

사용할 수 있는 가장 기본적인 필터는 ‘오류’ 또는 ‘시간 제한’ 또는 컴퓨터 이름과 같은 *키워드*입니다. 이러한 유형의 간단한 쿼리는 일반적으로 동일한 결과 집합 내에서 다양한 모양의 데이터를 반환합니다. Operational Insights에 시스템 중 데이터의 *형식*이 있기 때문입니다.


### 단순 검색을 수행하려면
1. Operational Insights 포털에서 **데이터 탐색기 검색**을 클릭합니다.![검색 타일](./media/operational-insights-search/overview-search.png)
2. 쿼리 필드에서 `error`을 입력하고 **검색**을 클릭합니다. ![검색 오류](./media/operational-insights-search/search-error.png)예를 들어, 다음 이미지에서 `error`에 대한 쿼리는 100,000건의 **이벤트** 레코드(로그 관리에서 수집), 18건의 **ConfigurationAlert** 레코드(구성 평가에서 생성) 및 12건의 **ConfigurationChange** 레코드(변경 내용 추적에서 캡처)를 반환합니다.![검색 결과](./media/operational-insights-search/results01.png)

이러한 필터는 실제로 개체 유형/클래스가 아닙니다. *유형*은 한 개의 태그, 속성 또는 문자열/이름/범주이며 하나의 데이터에 첨부됩니다. 시스템에서 일부 문서도 **유형: ConfigurationAlert**로 태그가 지정되고 일부는 **유형: PerfHourly** 또는 **유형: 이벤트** 등으로 태그가 지정됩니다. 각 검색 결과, 문서, 레코드 또는 항목은 각 데이터에 대한 원시 속성 및 값을 표시하고 필드에 지정된 값이 있는 레코드만 검색하려는 경우 필드 이름을 사용하여 필터에 지정할 수 있습니다.

*유형*은 실제로 모든 레코드가 있는 필드이며 다른 필드와 다르지 않습니다. 형식 필드의 값을 기반으로 설정되었습니다. 해당 레코드를 다른 형태 또는 모습을 갖습니다. 우연히 **Type=PerfHourly** 또는 **Type=Event**는 매시간 성능 데이터 집계 또는 이벤트를 쿼리하는 방법을 알아보아야 하는 구문입니다.

필드 이름 뒤 또는 값 앞에 콜론 (:) 또는 등호 (=) 중 하나를 사용할 수 있습니다. **Type:Event** 및 **Type=Event** 는 의미가 동일하며 선호하는 스타일을 선택할 수 있습니다.

따라서 Type=PerfHourly 레코드에 'CounterName'라는 필드가 있는 경우 `Type=PerfHourly CounterName="% Processor Time"`와 비슷한 쿼리를 작성할 수 있습니다.

이렇게 하면 성능 카운터 이름이 "% 프로세서 시간"인 성능 데이터만 얻을 수 있습니다.

### 프로세서 시간 성능 데이터를 검색 하려면
- 검색 쿼리 필드에 `Type=PerfHourly CounterName="% Processor Time"`를 입력합니다.

또한 쿼리에서 **InstanceName=\_'Total'**를 더 정확히 지정하고 사용할 수 있으며 이는 Windows 성능 카운터입니다. 또한 패싯 및 다른 **field:value**을 선택할 수 있습니다. 필터는 쿼리 표시줄의 필터에 자동으로 추가됩니다. 다음 그림에서 볼 수 있습니다. 아무것도 입력하지 않고 쿼리에 **InstanceName:’\_Total’**을 추가하려면 클릭할 위치를 보여줍니다.

![검색 패싯](./media/operational-insights-search/search-facet.png)

쿼리는 `Type=PerfHourly CounterName=”% Processor Time” InstanceName=”_Total”`가 됩니다.

이 예제에서 이 결과를 얻으려면 **Type=PerfHourly**를 지정할 필요가 없습니다. 필드 CounterName 및 InstanceName는 Type=PerfHourly의 레코드에만 존재하므로 쿼리는 더 긴 이전과 같은 결과를 반환할 만큼 구체적입니다. ```
CounterName=”% Processor Time” InstanceName=”_Total”
```

쿼리에서 모든 필터가 서로 *AND*에 있는 것으로 평가되기 때문입니다. 효과적으로 기준에 더 많은 필드를 더할수록 더 구체적이고 구체화된 결과는 적어집니다.

예를 들어 쿼리 `Type=Event EventLog="Windows PowerShell"`는 `Type=Event AND EventLog="Windows PowerShell"`와 동일합니다. Windows PowerShell 이벤트 로그에서 로그인되고 수집된 모든 이벤트를 반환합니다. 동일한 패싯을 반복해서 선택하여 필터를 여러 번 추가하는 경우 이는 순수하게 외관상의 문제입니다. 검색 표시줄을 채울 수 있지만 암시적 AND 연산자가 항상 있기 때문에 여전히 동일한 결과를 반환합니다.

NOT 연산자를 명시적으로 사용하여 쉽게 암시적 AND 연산자를 역방향으로 사용할 수 있습니다. 예:

`Type:Event NOT(EventLog:"Windows PowerShell")` 또는 동일한 `Type=Event EventLog!="Windows PowerShell"`는 Windows PowerShell 로그되지 않은 모든 로그에서 모든 이벤트를 반환합니다.

또는 'OR'같은 다른 부울 연산자를 사용할 수 있습니다. 다음 쿼리는 어떤 EventLog가 응용 프로그램 OR 시스템인지에 대한 레코드를 반환합니다.

```
EventLog=Application OR EventLog=System
```

위의 쿼리를 사용하여 동일한 결과 집합에서 두 로그 항목을 얻을 수 있습니다.

그러나 암시적 AND 상태로 두어 OR을 제거하면 BOTH 로그에 속하는 이벤트 로그 항목이 없기 때문에 다음의 쿼리는 결과를 생성하지 않습니다. 각 이벤트 로그 항목은 두 로그 중 하나에 작성되었습니다.

```
EventLog=Application EventLog=System
```


## 추가 필터 사용

다음 쿼리는 데이터를 전송하는 모든 컴퓨터에 대한 2개의 이벤트 로그에 항목을 반환합니다.

```
EventLog=Application OR EventLog=System
```

![검색 결과](./media/operational-insights-search/search-results03.png)

필드 또는 필터 중 하나를 선택하면 다른 모두를 제외하고 특정 컴퓨터에 대한 쿼리 범위가 좁혀집니다. 결과 쿼리는 다음과 유사합니다.

```
EventLog=Application OR EventLog=System Computer=SERVER1.contoso.com
```

암시적 AND 때문에 다음과 같습니다.

```
EventLog=Application OR EventLog=System AND Computer=SERVER1.contoso.com
```

각 쿼리는 다음과 같은 명시적 순서로 평가됩니다. 괄호에 유의하십시오.

```
(EventLog=Application OR EventLog=System) AND Computer=SERVER1.contoso.com
```

이벤트 로그 필드와 마찬가지로 OR을 추가하여 특정 컴퓨터 집합에 대한 데이터를 검색할 수 있습니다. 예:

```
(EventLog=Application OR EventLog=System) AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com OR Computer=SERVER3.contoso.com)
```

마찬가지로 다음 쿼리는 선택한 두 컴퓨터에 **CPU 시간 (%)**을 반환합니다.

```
CounterName=”% Processor Time”  AND InstanceName=”_Total” AND (Computer=SERVER1.contoso.com OR Computer=SERVER2.contoso.com)
```


### 부울 연산자
날짜/시간 및 숫자 필드를 사용하여 *이상*, *미만*, 및 *이하*를 사용하는 값을 검색할 수 있습니다. 쿼리 검색 표시줄에서 >, < , >=, <= , != 와 같은 간단한 연산자를 사용할 수 있습니다.


특정 기간에 특정 이벤트 로그를 쿼리할 수 있습니다. 예를 들어 지난 24시간을 다음 mnemonic 식으로 표현합니다.

```
EventLog=System TimeGenerated>NOW-24HOURS
```


#### 부울 연산자를 사용하여 검색하려면
- 검색 쿼리 필드에 `EventLog=System TimeGenerated>NOW-24HOURS"` ![부울 값을 사용하여 검색](./media/operational-insights-search/search-boolean.png)를 입력합니다.

시간 간격을 그래픽으로 제어할 수 있지만, 그렇게 하려는 대부분의 시간에 쿼리에 직접 하는 시간 필터를 포함하여 장점이 있습니다. 예를 들어 이 방식은 대시보드 페이지에서 *전역* 시간 선택기와 무관하게 각 타일에 대한 시간을 재정의할 수 있는 경우 대시보드와 잘 작동합니다. 자세한 내용은 [대시보드에서 시간 문제](http://cloudadministrator.wordpress.com/2014/10/19/system-center-advisor-restarted-time-matters-in-dashboard-part-6/)를 참조하십시오.

시간별로 필터링하는 경우 두 기간의 *교집합*에 대한 결과를 얻는 것을 기억하십시오. 하나는 Operational Insights 포털(S1)에서 지정되고 다른 하나는 쿼리(S2)에 지정됩니다.

![교집합](./media/operational-insights-search/intersection.png)

즉, 기간이 교차하지 않으면, 예를 들어 **이번 주**를 선택할 수 있는 Operational Insights 포털 및 **지난주**를 정의할 수 있는 쿼리에서, 교차가 없으면 어떤 결과도 받을 수 없습니다.

TimeGenerated 필드에 사용되는 비교 연산자가 다른 경우에도 유용합니다. 예를 들어 숫자 필드를 사용하는 경우입니다.

예를 들어 구성 평가 경고는 다음의 심각도 값을 가진다고 가정합니다.

- 0 = 정보
- 1 = 경고
- 2 = 중요

경고 및 중요한 알림을 쿼리하고 다음 쿼리로 정보를 제외할 수 있습니다.

```
Type=ConfigurationAlert  Severity>=1
```


범위 쿼리를 사용할 수 있습니다. 즉, 시퀀스에서 값의 시작 및 끝 범위를 제공할 수 있습니다. 예를 들어 EventID가 보다 2100 이상이지만 2199 미만일 때 Operations Manager 이벤트 로그에서 이벤트를 하려는 경우 다음의 쿼리는 이벤트를 반환합니다.

```
Type=Event EventLog="Operations Manager" EventID:[2100..2199]
```


>[AZURE.NOTE]사용해야 하는 범위 구문은 콜론(:) field:value 구분이고 등호(=)는 *아닙니다*. 아래쪽과 위쪽의 범위 끝을 대괄호로 묶고 두 마침표(..)로 구분합니다.

## 검색 결과 조작

데이터를 검색할 때 검색 쿼리를 구체화하고 결과를 어느 정도 제어합니다. 결과를 검색할 때 이를 변환하는 명령을 적용할 수 있습니다.

Operational Insights 검색에서 명령은 세로줄 문자(|) 뒤에 *와야* 합니다. 필터는 쿼리 문자열의 첫번째 부분에 항상 있어야 합니다. 작업하는 데이터 집합을 정의하고 이러한 결과를 명령으로 "파이프"합니다. 다음 명령을 추가하려면 파이프를 사용할 수 있습니다. 이것은 Windows PowerShell 파이프라인과 대강 비슷합니다.

일반적으로 Operational Insights 검색 언어는 PowerShell 스타일을 따르고 지침도 IT 전문가와 비슷하게 하며 학습 곡선을 보다 쉽게 하려고 합니다.

명령은 동사의 이름이 있으므로 수행할 작업을 쉽게 알 수 있습니다.

### 정렬

정렬 명령을 사용하면 하나 또는 여러 필드에서 정렬 순서를 정의할 수 있습니다. 기본적으로 사용하지 않아도 시간 내림차순으로 정렬되도록 적용됩니다. 가장 최근의 결과가 검색 결과 위쪽에 항상 표시됩니다. 즉, `Type=Event EventID=1234`로 검색을 실행하면 실제로 실행되는 작업은 다음과 같습니다.

```
Type=Event EventID=1234 **| Sort TimeGenerated desc**
```

로그를 잘 알고 있다면 경험 형식 때문입니다. 예를 들어 Windows 이벤트 뷰어에서 그렇습니다.

정렬을 사용하여 결과를 반환하는 방식을 변경할 수 있습니다. 다음 예제는 이 작동 방식을 보여 줍니다.

```
Type=Event EventID=1234 | Sort TimeGenerated asc
```

```
Type=Event EventID=1234 | Sort Computer asc
```

```
Type=Event EventID=1234 | Sort Computer asc,TimeGenerated desc
```


위의 간단한 예제는 명령이 작동하는 방법을 보여줍니다. 필터가 반환하는 결과의 형태를 변경합니다.

### 제한 및 위쪽
덜 알려진 다른 명령에는 제한이 있습니다. 제한은 PowerShell같은 동사입니다. 제한은 TOP 명령과 기능적으로 동일합니다. 다음 쿼리는 동일한 결과를 반환합니다.

```
Type=Event EventID=2110 | Limit 1
```

```
Type=Event EventID=2110 | Top 1
```


#### Top을 사용하여 검색하려면
- 검색 쿼리 필드에 `Type=Event EventID=2110 | Top 1` ![상위 검색](./media/operational-insights-search/search-top.png)를 입력합니다.

위의 그림에서 EventID=2110를 사용하여 988건의 레코드가 있습니다. 왼쪽에서 필드, 패싯 및 필터는 항상 쿼리의 *필터 부분으로* 반환된 결과 대한 정보를 표시하며 이는 모든 파이프 문자 앞에 오는 부분입니다. **결과** 창은 예제 명령이 결과를 모양을 만들고 변환하기 때문에 가장 최근 1개의 결과를 반환합니다.

### 여기서

SELECT 명령은 PowerShell에서 Select-Object 처럼 동작합니다. 자신의 원래 속성을 모두 갖지 않는 필터된 결과를 반환합니다. 대신 지정한 속성을 선택합니다.

#### 선택 명령을 사용하는 검색을 실행하려면

1. 검색에서 `Type=Event`을 입력하고 **검색**을 클릭합니다.
2. 있는 모든 속성을 보려면 결과 중 하나에서 **+자세히 표시**를 클릭합니다.
3. 그 중 일부를 명시적으로 선택하고 쿼리가 `Type=Event | Select Computer,EventID,RenderedDescription`로 변경됩니다. ![선택 검색](./media/operational-insights-search/search-select.png)

검색 출력을 제어하고 탐색을 위한 중요 데이터의 일부만 선택하려는 경우 특히 유용한 명령으로 종종 전체 레코드는 아닙니다. 또한 다양한 종류의 레코드에 *일부* 공용 속성이 있지만 *모든* 속성의 공통되는 경우 유용합니다. CSV 파일로 내보낸 다음 Excel에서 massaged되는 경우 테이블처럼 더 자연스럽게 보이거나 잘 작동하는 출력을 생성할 수 있습니다.

[AZURE.INCLUDE [operational-insights-export](../../includes/operational-insights-export.md)]

## 측정값 명령 사용

측정값은 Operational Insights 검색에서 가장 다양한 기능을 갖춘 명령 중 하나입니다. 데이터에 통계 *함수*를 적용하고 지정된 필드가 그룹화된 결과를 집계할 수 있습니다. 측정값이 지원하는 여러 통계 함수가 있습니다.

### 개수() 측정

작업할 첫번째 통계 함수에서 이해하기 가장 간단한 것은 *count()* 함수입니다.

`Type=Event`과 같은 모든 검색 쿼리에서 나온 결과는 검색 결과의 왼쪽에 패싯이라고 하는 필터를 표시합니다. 필터는 실행된 검색에서 결과에 지정된 필드에 의해 값의 분포를 표시합니다.

![측정값 개수 검색](./media/operational-insights-search/search-measure-count01.png)

예를 들어 위의 그림에서 **컴퓨터** 필드가 표시되고 결과에서 거의 3백만개까지 이벤트를 보여줍니다. 해당 레코드에서 **컴퓨터** 필드로 20개의 고유하고 고유한 값이 있습니다. 타일은 **컴퓨터** 필드에 기록되는 가장 일반적인 5개의 값인 상위 5개만을 표시하며) 해당 필드에 특정 값을 포함하는 문서 수로 정렬됩니다. 표시되는 거의 3백만 개의 이벤트 중 이미지에서 88만은 DE 컴퓨터에서, 60만 2천은 DM 컴퓨터에서 제공합니다.


타일이 상위 5개만 표시하므로 모든 값을 보려면 어떻게 해야 합니까?

개수() 함수를 사용하여 측정값 명령으로 수행할 수 있는 작업입니다. 이 함수는 매개 변수를 사용하지 않습니다. 방금 이 경우에 **컴퓨터** 필드를 기준으로 그룹화하려는 필드를 지정했습니다.

`Type=Event | Measure count() by Computer`

![측정값 개수 검색](./media/operational-insights-search/search-measure-count-computer.png)

그러나 **컴퓨터**는 각 데이터 조각*에서* 사용되는 필드일 뿐이며 관련된 관계형 데이터베이스가 없고 어디에도 별도 **컴퓨터** 개체가 없습니다. 데이터*에서* 값은 엔터티가 생성한 것, 기타 특성의 숫자 및 데이터의 측면 – 따라서 용어 *패싯*을 설명합니다. 그러나 다른 필드에 의해 그룹화할 수 있습니다. 또한 측정값 명령으로 파이프되는 거의 3백만개 이벤트의 원래 결과에 **EventID**라는 필드가 있기 때문에 동일한 기법을 적용하여 해당 필드로 그룹화하고 EventID로 이벤트의 개수를 가져올 수 있습니다.

```
Type=Event | Measure count() by EventID
```

특정 값을 포함하는 실제 레코드 개수에 관심이 없지만 대신 값 자체의 목록을 원하는 경우 끝부분에 *선택* 명령을 추가하고 첫 열을 선택할 수 있습니다.

```
Type=Event | Measure count() by EventID | Select EventID
```

쿼리에서 더 복잡해 지고 미리 결과를 정렬할 수 있습니다. 또는 그리드의 열을 클릭할 수 있습니다.

```
Type=Event | Measure count() by EventID | Select EventID | Sort EventID asc
```

#### 측정값 개수를 사용하여 검색

- 검색 쿼리 필드에 `Type=Event | Measure count() by EventID`를 입력합니다.
- 쿼리의 끝에 `| Select EventID`를 추가합니다.
- 마지막으로 쿼리의 끝에 `| Sort EventID asc`을 추가합니다.


몇가지 중요한 사항 및 강조를 알아봅니다.

첫 번째로 표시되는 결과는 원래 원시 결과가 아닙니다. 대신 집계된 결과이며 기본적으로 그룹의 결과입니다. 이는 문제가 아니지만 집계/통계 함수의 결과로서 즉석에서 생성되는 원래 원시 모양과 달라진 매우 다른 모양의 데이터를 조작한다는 점을 이해해야 합니다.

두 번째로 **개수 측정**은 현재 상위 100개의 고유한 결과만 반환합니다. 이 제한은 다른 통계 함수에 적용되지 않습니다. 따라서 측정값 개수()를 적용하기 전에 특정 항목을 검색하려면 일반적으로 보다 정밀한 필터를 사용해야 합니다.

## 측정값 명령과 최대 및 최소 함수 사용

**측정값 최대()** 및 **측정값 최소()**가 유용한 다양한 시나리오가 있습니다. 그러나 각 함수는 서로 반대이므로 최대()를 설명하고 최소()를 직접 시험해 보겠습니다.

구성 평가 경고를 쿼리하는 경우 정보, 경고 및 중요를 나타내는 0, 1 또는 2인 **심각도** 속성이 있습니다. 예:

```
Type=ConfigurationAlert
```

![측정값 개수 시작 검색](./media/operational-insights-search/search-measure-max01.png)

필드에 의한 그룹, 공용 컴퓨터를 지정한 경고에 대한 가장 높은 값을 확인하려는 경우 다음을 사용할 수 있습니다.

```
Type=ConfigurationAlert | Measure Max(Severity) by Computer
```

![측정값 최대 컴퓨터 검색](./media/operational-insights-search/search-measure-max02.png)

**경고** 레코드가 있는 컴퓨터에서 이를 보여줍니다. 대부분은 중요한 경고가 하나 이상 있고 Bacc 컴퓨터에 최악의 심각도인 경고가 있습니다.

```
Type=ConfigurationAlert | Measure Max(Severity) by Computer
```

![최대 시간이 생성된 컴퓨터 검색](./media/operational-insights-search/search-measure-max03.png)

이 함수는 숫자와 잘 작동하지만 DateTime 필드와도 작동합니다. 각 컴퓨터에 인덱싱된 데이터의 모든 부분에 대한 마지막 또는 가장 최근 타임스탬프를 확인하는 것이 유용합니다. 예를 들어 각 컴퓨터에 대한 변경 내용 추적 솔루션이 가장 최근에 구성 변경을 보고한 때는 언제입니까?

```
Type=ConfigurationChange | Measure Max(TimeGenerated) by Computer
```

## 측정값 명령과 평균 함수 사용

측정값을 사용한 평균() 통계 함수를 사용하면 일부 필드에 대한 평균값을 계산하고 동일한 또는 다른 필드 결과를 그룹화할 수 있습니다. 다양한 성능 데이터와 같은 경우에 유용합니다.

성능 데이터를 시작하겠습니다. 그러나 Operational Insights은 현재 특정 패브릭과 관련된 가상 컴퓨터 관리자 및 Hyper-V 호스트용 성능 카운터만을 용량 관리 솔루션의 일부로 수집합니다.

*모든* 성능 데이터를 검색하려면 가장 기본적인 쿼리는 다음과 같습니다.

```
Type=PerfHourly
```

![avg 시작 검색](./media/operational-insights-search/search-avg01.png)

가장 먼저 알 수 있는 것은 Operational Insights가 성능 카운터 차트를 보여준다는 점입니다. 결과의 아래에서 차트의 뒤에 실제 레코드가 표시됩니다.

![avg 시작 검색](./media/operational-insights-search/search-avg02.png)

위 이미지에서 다음과 같은 내용을 나타내는 표시된 필드의 두 집합이 있습니다.

- 첫번째 집합은 쿼리 필터에서 Windows 성능 카운터 이름, 개체 이름 및 인스턴스 이름을 식별합니다. 가장 일반적으로 사용할 패싯/필터 필드입니다.
- **SampleValue**는 카운터의 실제 값입니다.
- 쿼리에서 **Type=PerfHourly**는 매시간 집계됩니다.
- **TimeGenerated**는 24시간 형식에서 21:00시입니다. 20:00시에서 21:00시까지 시간에 대한 집계입니다.
- **SampleCount**는 12개의 샘플을 사용하여 계산된 집계입니다(5분 당 한 번).
- 시간당 **최소**, **최대**, 및 **Percentile95**가 가상 컴퓨터의 메모리에 대한 이 예제에서 6144(메가바이트)였습니다.
- **SampleValue**는 매시간 집계하고 시간에 대한 *평균*으로 채워지며 성능 차트를 그리는 데 사용됩니다.

PerfHourly 레코드 모양을 읽고 다른 검색 방법에 대해 읽은 후 이러한 종류의 숫자 데이터를 집계하려면 측정값 평균()을 사용할 수 있습니다.

간단한 예는 다음과 같습니다.

```
Type=PerfHourly  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" | Measure Avg(SampleValue) by Computer
```

![avg samplevalue 검색](./media/operational-insights-search/search-avg03.png)

이 예제에서 CPU 총 시간 성능 카운터를 선택하고 컴퓨터에 의해 평균을 냅니다. **SampleValue**는 이미 평균이기 때문에 실제로 평균의 평균을 쿼리합니다. 이 시점에서 Type=PerfHourly로 올바릅니다. 항상 TimeGenerated에 필터를 사용하여 7일이 아닌 마지막 4시간과 소규모 또는 최근 데이터 집합에 작업을 제한해야 합니다.

따라서 위의 쿼리는 다음과 같게 됩니다.

```
Type=PerfHourly  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-4HOURS | Measure Avg(SampleValue) by Computer
```

### 측정값 명령과 평균 함수 사용를 사용하여 검색하려면
1. 검색 쿼리 박스에 `Type=PerfHourly  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-4HOURS | Measure Avg(SampleValue) by Computer`를 입력합니다.
2. 최근 평균이 일반적으로 클 수 있습니다.
3. `Type=PerfHourly  ObjectName:Processor  InstanceName:_Total  CounterName:"% Processor Time" TimeGenerated>NOW-4HOURS | Measure Avg(Max) by Computer`와 함께 검색 쿼리를 수정하여 최대 시간 값의 평균을 계산합니다.

컴퓨터 *간에* 데이터를 집계하고 상호 연결할 수 있습니다. 예를 들어 각 노드가 서로 동일한 종류의 일부 팜에서 호스트 집합이 있고 이들이 모두 같은 유형의 작업을 한다고 가정하면 부하가 대략적으로 분산되어야 합니다. 다음의 쿼리로 이동하는 하나에 모든 카운터를 가져오고 전체 팜에 대한 평균을 가져올 수 있습니다. 다음 예제로 컴퓨터를 선택하여 시작할 수 있습니다.

```
Type=PerfHourly AND (Computer=”SERVER1.contoso.com” OR Computer=”SERVER2.contoso.com” OR Computer=”SERVER3.contoso.com”)
```

컴퓨터가 있으므로 또한 2개의 핵심 성과 지표 (KPI) 즉, CPU 사용량 % 및 % 사용 가능한 디스크 공간을 선택하려 합니다. 따라서 쿼리의 부분은 다음과 같이 됩니다.

```
Type=PerfHourly  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS
```

이제 다음 예제로 컴퓨터 및 카운터를 추가할 수 있습니다.

```
Type=PerfHourly  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer=”SERVER1.contoso.com” OR Computer=”SERVER2.contoso.com” OR Computer=”SERVER3.contoso.com”)
```

매우 구체적인 선택을 하기 때문에 **평균() 측정** 명령이 컴퓨터로 평균이 아닌 팜 전체에서 단순히 CounterName로 그룹화하여 반환할 수 있습니다. 예:

```
Type=PerfHourly  InstanceName:_Total  ((ObjectName:Processor AND CounterName:"% Processor Time") OR (ObjectName="LogicalDisk" AND CounterName="% Free Space")) AND TimeGenerated>NOW-4HOURS AND (Computer=”SERVER1.contoso.com” OR Computer=”SERVER2.contoso.com” OR Computer=”SERVER3.contoso.com”) | Measure Avg(SampleValue) by CounterName
```

사용자 환경 KPI의 간단한 보기를 제공하므로 유용합니다.

![avg 그룹화 검색](./media/operational-insights-search/search-avg04.png)


대시보드에서 쉽게 사용할 수 있습니다. 대시보드를 사용하는 방법에 대해 자세히 알려면 [Operational Insights 대시보드](operational-insights-use-dashboards)를 참조하세요.

![avg 대시보드 검색](./media/operational-insights-search/search-avg05.png)

### 측정값 명령과 합계 함수 사용

합계 함수는 측정값 명령의 다른 함수와 비슷합니다. 합계 함수를 사용하는 방법에 대한 예제는 [Microsoft Azure Operational Insights의 W3C IIS 로그 검색](http://blogs.msdn.com/b/dmuscett/archive/2014/09/20/w3c-iis-logs-search-in-system-center-advisor-limited-preview.aspx)에서 볼 수 있습니다.

숫자, datetime 및 텍스트 문자열이 포함된 최대() 및 최소()를 사용할 수 있습니다. 텍스트 문자열을 이용하여 사전순으로 정렬하고 처음 및 마지막을 가져옵니다.

그러나 숫자 필드가 아닌 어느 것도 합계()를 사용할 수 없습니다. 평균()에도 적용됩니다.

## Where 명령 사용

명령이 필터와 같이 작동하지만 쿼리의 시작 부분에서 필터링된 원시 결과와 반대로 파이프라인에 적용하여 측정값 명령이 생성한 집계된 결과를 자세히 필터링할 수 있습니다.

예:

```
Type=PerfHourly  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(SampleValue) as AVGCPU by Computer
```

다음 예제로 다른 파이프 "|" 문자 및 Where 명령을 추가하여 평균 CPU가 80% 이상인 컴퓨터를 가져올 수 있습니다.

```
Type=PerfHourly  CounterName="% Processor Time"  InstanceName="_Total" | Measure Avg(SampleValue) as AVGCPU by Computer | Where AVGCPU>80
```

Microsoft System Center Operations Manager에 익숙하다면 where 명령을 관리 팩 측면에서 생각할 수 있습니다. 예제가 규칙인 경우 쿼리의 첫번째 부분이 데이터 원본이고 where 명령은 조건 검색입니다.

**내 대시보드**에서 컴퓨터 CPU가 과도하게 사용되는 경우 참조하는 모니터로서 쿼리를 타일로 사용할 수 있습니다. 대시보드에 대해 자세히 알려면 [Operational Insights 대시보드](operational-insights-use-dashboards)를 참조하세요. 또한 모바일 앱을 사용하여 대시보드를 만들고 사용할 수 있습니다. 자세한 내용은 [Azure Operational Insights 모바일 앱](http://www.windowsphone.com/ko-KR/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865)을 참조하세요. 다음 이미지의 아래쪽 두 타일에서 목록을 표시한 모니터를 숫자로 볼 수 있습니다 . 기본적으로 항상 수는 0으로, 목록은 비어 있어야 합니다. 그렇지 않은 경우 경고 조건을 나타냅니다. 필요한 경우 이것을 사용하여 압력을 받는 컴퓨터를 볼 수 있습니다.

![모바일 대시보드](./media/operational-insights-search/search-mobile.png)

## 검색 구문 참조

검색 언어에 대한 다음의 참조 섹션은 데이터및 필터링 식을 검색 할 때 사용할 수 있는 일반 쿼리 구문 옵션을 설명하여 검색 범위를 좁힐 수 있도록 합니다. 검색된 데이터에 조치로 사용할 수 있는 명령을 설명합니다.

[필드 및 패싯 참조 검색](#Search-field-and-facet-reference)에서 데이터와 유사한 범주를 dill-into하는 검색 및 패싯을 반환하는 필드에 대해 알 수 있습니다.

### 일반 쿼리 구문

구문

filterExpression | command1 | command2 …

필터 식(**filterExpression**)은 쿼리에 "where" 조건을 정의합니다. 명령은 쿼리에 의해 반환된 결과에 적용됩니다. 여러 명령은 세로줄 문자(|)로 구분해야 합니다.

#### 일반 구문 예제

예제:

	system

이 쿼리는 전체 텍스트 또는 검색 조건에 인덱싱된 모든 필드에서 단어 "시스템"을 포함하는 결과를 반환합니다.

>[AZURE.NOTE]모든 필드가 이러한 방식으로 인덱싱되지 않지만 가장 일반적으로 텍스트 필드(예: 설명 및 이름)는 일반적으로 그렇습니다.

	system error

이 쿼리는 단어 "시스템"과 "오류"를 포함하는 결과를 반환합니다.

	system error | sort ManagementGroupName, TimeGenerated desc | top 10

이 쿼리는 단어 "시스템"과 "오류"를 포함하는 결과를 반환합니다. **ManagementGroupName** 필드(오름차순)의 결과를 정렬한 다음 **TimeGenerated**(내림차순)의 결과를 정렬합니다. 처음 10개의 결과만 표시합니다.

>[AZURE.IMPORTANT]모든 필드 이름과 문자열 및 텍스트 필드에 대한 값은 대/소문자를 구분합니다.

### 필터 식

다음 하위 섹션에서 필터 식을 설명합니다.

#### 문자열 리터럴

리터럴 문자열은 키워드 또는 미리 정의된 데이터 형식(예: 숫자 또는 날짜)으로 파서에서 인식되지 않은 모든 문자열입니다.

예제:

	These all are string literals


이 쿼리는 발생하는 5개 단어를 모두 포함하는 결과를 검색합니다. 복잡한 문자열 검색을 수행하려면 다음과 같이 문자열 리터럴을 큰따옴표로 묶습니다.

	" Windows Server"

"Windows Server"와 정확히 일치하는 결과를 반환합니다.

#### 숫자

파서는 숫자 필드에 10 진수 정수 및 부동 소수점 수 구문을 지원합니다.

예제:

	Type:PerfHourly 0.5


	HTTP 500

#### 날짜/시간

시스템에서 데이터의 모든 부분에 원래 날짜와 레코드의 시간을 나타내는 **TimeGenerated** 속성이 있습니다. 또한 일부 데이터 형식에 더 많은 날짜/시간 필드가 있을 수 있습니다.(예를 들어 **LastModified**)

Operational Insights에서 타임라인 차트/시간 선택기는 **TimeGenerated** 필드에 기반하여 시간에 따라.(현재 실행되는 쿼리에 따라) 결과의 분포를 보여 줍니다. 날짜/시간 필드는 쿼리에 사용할 수 있는 특정 문자열 형식이 있어서 특정 기간에 쿼리를 제한합니다. 또한 상대적 시간 간격을 참조하도록 구문을 사용할 수 있습니다.(예를 들어 "3일 전 및 2시간 전")

구문

	yyyy-mm-ddThh:mm:ss.dddZ


	yyyy-mm-ddThh:mm:ss.ddd


	yyyy-mm-ddThh:mm:ss


	yyyy-mm-ddThh:mm:ss


	yyyy-mm-ddThh:mm


	yyyy-mm-dd



예제:

	TimeGenerated:2013-10-01T12:20

이전 명령은 정확하게 2013년 10월 1일 12시 20분이라는 **TimeGenerated** 값으로 레코드를 반환합니다. 어떤 결과를 제공할 가능성이 있지만 개념은 이해했을 것입니다.

또한 파서가 mnemonic 날짜/시간 값을 지원합니다.

예제:


다시 데이터가 빠른 시스템을 통과하지 않기 때문에 모든 결과를 생성할 가능성이 적습니다.

이러한 예제는 상대 및 절대 날짜에 사용할 구성 요소입니다. 다음 세 하위 섹션에서 상대 날짜 범위를 사용 하는 예제를 고급 필터에서 사용하는 방법에 대해 설명합니다.

#### 날짜/시간 수학

간단한 날짜/시간 계산을 사용하여 날짜/시간 수학 연산자로 날짜/시간 값을 오프셋 또는 반올림합니다.

구문

	datetime/unit


	datetime[+|-]count unit




<table border="1" cellspacing="4" cellpadding="4">
	<tr>
		<th>연산자</th>
		<th>설명</th>
	</tr>
	<tr>
		<td>
		<p>/</p>
		</td>
		<td>
		<p>날짜/시간을 지정된 단위로 반올림합니다. </p>
		<p>예를 들어 지금/일은 현재 날짜의 자정으로 현재 날짜/시간을 반올림합니다. </p>
		</td>
	</tr>
	<tr>
		<td>
		<p>+ 또는-</p>
		</td>
		<td>
		<p>단위의 지정한 수 만큼 날짜/시간을 오프셋합니다</p>
		<p>예제:&#160; </p>
		<ul>
			<li class="unordered">이제 +1시간은 한 시간 빠르게 현재 날짜/시간을 오프셋합니다.<br><br></li>
			<li class="unordered">2013-10-01T12:00-10DAYS은 10 일 만큼 날짜 값을 다시 오프셋합니다.</li>
		</ul>
		</td>
	</tr>
</table>





다음과 같이 날짜/시간 산술 연산자를 함께 연결할 수 있습니다.

	NOW+1HOUR-10MONTHS/MINUTE

다음 테이블에서 지원되는 날짜/시간 단위를 나열합니다.

날짜/시간 단위|설명
---|---
YEAR, YEARS|현재 연도를 반올림하거나 지정된 년 수 만큼 오프셋합니다.
MONTH, MONTHS|현재 월을 반올림하거나 지정된 개월 수 만큼 오프셋합니다.
DAY, DAYS, DATE|현재 날을 반올림하거나 지정된 일 수 만큼 오프셋합니다.
HOUR, HOURS|현재 시간을 반올림하거나 지정된 시간 수 만큼 오프셋합니다.
MINUTE, MINUTES|현재 분을 반올림하거나 지정된 분 수 만큼 오프셋합니다.
SECOND, SECONDS|현재 초를 반올림하거나 지정된 초 수 만큼 오프셋합니다.
MILLISECOND, MILLISECONDS, MILLI, MILLIS|현재 밀리초를 반올림하거나 지정된 밀리초 수 만큼 오프셋합니다.


#### 필드 패싯

패싯 필드를 사용하여 인덱스 전체에서 다양한 용어에 "자유 텍스트" 쿼리를 작성하는 대신 특정 필드 및 정확한 값에 대한 검색 조건을 지정할 수 있습니다. 이 구문은 이전 단락에서 몇가지 예제에 이미 사용했습니다. 여기에서 더 복잡한 예제를 제공합니다.

**구문**

*field:value*

*field=value*

**설명**

특정 값에 대한 필드를 검색합니다. 값은 문자열 리터럴, 숫자 또는 날짜/시간일 수 있습니다.

예제:


	TimeGenerated:NOW


	ObjectDisplayName:"server01.contoso.com"


	SampleValue:0.3

**구문**

*field>value*

*field<value*

*field>=value*

*field<=value*

*field!=value*

**설명**

비교를 제공합니다.

예제:

	TimeGenerated>NOW+2HOURS


**구문**

*field:[from..to]*

**설명**

패싯 범위를 제공합니다.

예제:

	TimeGenerated:[NOW..NOW+1DAY]


	SampleValue:[0..2]
#### 논리 연산자

쿼리 언어가 논리 연산자(AND, OR, and NOT) 및 C-스타일 별칭을 (&&, ||, 및 !)을 각각 지원합니다. 이러한 연산자를 그룹화하려면 괄호를 사용할 수 있습니다.

예제:

	system OR error


	Type:Alert AND NOT(Severity:1 OR ObjectId:"8066bbc0-9ec8-ca83-1edc-6f30d4779bcb8066bbc0-9ec8-ca83-1edc-6f30d4779bcb")
최상위 필터 인수에서 논리 연산자를 생략할 수 있습니다. 이 경우 AND 연산자를 가정합니다.


필터 식|Equivalent to
---|---
시스템 오류|시스템 AND 오류
시스템 "Windows Server" OR 심각도:1|시스템 AND ("Windows Server" OR 심각도:1)



### 명령

명령은 쿼리에 의해 반환된 결과에 적용됩니다. 파이프 문자(|)를 사용하여 검색된 결과에 명령을 적용합니다. 여러 명령은 세로줄 문자로 구분해야 합니다.

>[AZURE.NOTE]명령 이름은 필드 이름 및 데이터와는 달리 대문자 또는 소문자로 작성할 수 있습니다.

#### 정렬

구문

	sort field1 asc|desc, field2 asc|desc, …

특정 필드에 의한 결과를 정렬합니다. asc/desc 접두사는 선택 사항입니다. 생략된 경우에 "asc" 정렬 순서를 가정합니다. 쿼리가 **정렬** 명령을 명시적으로 사용하지 않는 경우 정렬 **TimeGenerated** desc는 기본 동작이고 가장 최근의 결과를 항상 먼저 반환합니다.

#### 위쪽/제한

구문

	top number


	limit number
상위 N개 결과에 대한 응답을 제한합니다.

예제:

	Type:Alert errors detected | top 10

상위 10개의 일치하는 결과를 반환합니다.

#### Skip

구문

	skip number

나열된 결과의 수를 건너뜁니다.

예제:

	Type:Alert errors detected | top 10 | skip 200

200개 결과에서 시작하여 상위 10개의 일치하는 결과를 반환합니다.

#### 여기서

구문

	select field1, field2, ...

선택한 필드에 대한 결과를 제한합니다.

예제:

	Type:Alert errors detected | select Name, Severity

**이름** 및 **심각도**로 반환된 결과 필드를 제한합니다.

#### 측정값

**측정값** 명령은 통계 함수를 원시 검색 결과에 적용하는 데 사용됩니다. 이 명령은 데이터에서 *group-by* 보기를 가져오는 데 매우 유용합니다. **측정값** 명령을 사용하는 경우 Operational Insights는 집계된 결과가 포함된 테이블을 표시합니다.

구문

	measure aggregateFunction([aggregatedField]) [as fieldAlias] by groupField [interval interval]


	measure aggregateFunction([aggregatedField])  interval interval

**groupField**로 결과를 집계하고 **aggregatedField**를 사용하여 집계된 측정값을 계산합니다.


<table border="1" cellspacing="4" cellpadding="4"><table> <tr> <th>통계 함수 측정 </th> <th>설명 </th> </tr> <tr> <td> <p><em>aggregateFunction</em> </p> <p></p> </td> <td> <p>집계 함수의 이름입니다(대소문자 구분 안 함). 다음 집계 함수가 지원됩니다.</p> <ul> <li class="unordered">COUNT<br><br></li> <li class="unordered">MAX<br><br></li> <li class="unordered">MIN<br><br></li> <li class="unordered">SUM<br><br></li> <li class="unordered">AVG<br><br></li> <li class="unordered">STDDEV<br><br></li> </ul> </td> </tr> <tr> <td> <p><em>aggregatedField</em> </p> </td> <td> <p>집계된 필드입니다. 이 필드는 COUNT 집계 함수에 대해 선택적이지만 SUM, MAX, MIN, AVG 또는 STDDEV에 대한 기존 숫자 필드여야 합니다.</p> </td> </tr> <tr> <td> <p><em>fieldAlias</em> </p> </td> <td> <p>계산되고 집계된 값에 대한 별칭(선택 사항)입니다. 지정하지 않으면 필드 이름은 <em>AggregatedValue</em>입니다.</p> </td> </tr> <tr> <td> <p><em>groupField</em> </p> </td> <td> <p>결과 집합이 그룹화된 필드의 이름입니다. </p> </td> </tr> <tr> <td> <p><em>Interval</em> </p> </td> <td> <p>다음 형식의 시간 간격입니다. </p> <p><em>nnnNAME</em> </p> <p></p> <p>여기서, </p> <p>nnn은 양의 정수입니다.</p> <p><em>NAME</em> 은 간격 이름입니다.</p> <p>지원되는 간격 이름은 다음을 포함합니다.(대/소문자 구분)</p> <ul> <li class="unordered">MILLISECOND[S]<br><br></li> <li class="unordered">SECOND[S]<br><br></li> <li class="unordered">MINUTE[S]<br><br></li> <li class="unordered">HOUR[S]<br><br></li> <li class="unordered">DAY[S]<br><br></li> <li class="unordered">MONTH[S]<br><br></li> <li class="unordered">YEAR[S]<br></li> </ul> </td> </tr> </table>



날짜/시간 그룹 필드에서만 간격 옵션을 사용할 수 있습니다(예: **TimeGenerated** 및 **TimeCreated**). 현재 서비스에 의해 적용되지 않지만 백 엔드로 전달되는 날짜/시간 없는 필드에 런타임 오류가 발생합니다. 스키마 유효성 검사를 구현하는 경우 서비스 API는 집계 간격에 대해 날짜/시간 없이 필드를 사용하는 쿼리를 거부합니다. 현재 **측정값** 구현은 **Count** 집계 함수를 그룹화하는 간격만 지원합니다.

BY 절을 생략하지만 간격을 지정하는 경우(두 번째 구문처럼) 기본적으로 **TimeGenerated** 필드를 가정합니다.

예제:

**예 1**

	Type:Alert | measure count() as Count by ObjectId

*설명*

**ObjectID**에서 경고를 그룹화하고 각 그룹에 대한 경고의 수를 계산합니다. 집계된 값을 **Count** 필드(별칭)로 반환합니다.

**예 2**

	Type:Alert | measure count() interval 1HOUR

*설명*

**TimeGenerated** 필드를 사용하여 1시간 간격으로 경고를 그룹화하고 각 간격의 경고 수를 반환합니다.

**예 3**

	Type:Alert | measure count() as AlertsPerHour interval 1HOUR

*설명*

이전 예제와 동일하지만 집계된 필드 별칭을 사용합니다.(**AlertsPerHour**)

**예제 4**

	* | measure count() by TimeCreated interval 5DAYS

*설명*

**TimeCreated** 필드를 사용하여 5일 간격으로 결과를 그룹화하고 각 간격의 결과 수를 반환합니다.

**예제 5**

	Type:Alert | measure max(Severity) by WorkflowName

*설명*

워크로드 이름 별로 경고를 그룹화하고 각 워크플로에 대한 최대 경고 심각도 값을 반환합니다.

**예제 6**

	Type:Alert | measure min(Severity) by WorkflowName

*설명*

이전 예제와 동일하지만 **Min** 집계 함수를 사용합니다.

**예제 7**

	Type:PerfHourly | measure avg(SampleValue) by ObjectId

*설명*

ObjectId로 PerfHourly를 그룹화하고 평균(평균)을 계산합니다.

**예제 8**

	Type:PerfHourly | measure sum(SampleValue) by ObjectId

*설명*

이전 예제와 동일하지만 **합계**를 사용합니다.

**예제 9**

	Type:PerfHourly | measure stddev(SampleValue) by ObjectId

*설명*

이전 예제와 동일하지만 **STDDEV**를 사용합니다.

**예제 10**

	Type:Alert | measure count() as Count by WorkflowName | sort Count desc | top 5

*설명*

경고의 최대 수와 상위 5개 워크플로를 가져옵니다.

#### Where

구문

**where** AggregatedValue>20

**측정값** 명령 뒤에 사용하여 **측정값** 집계 함수가 만든 집계된 결과를 자세히 필터링할 수 있습니다.

예제:

	Type:PerfHourly CounterName:"% Total Run Time" | Measure max(Max) as MAXCPU by

	Type:PerfHourly CounterName:"% Total Run Time" | Measure max(Max) by RootObjectName | where (AggregatedValue>50 and AggregatedValue<90)




## 검색 필드 및 패싯 참조

검색을 사용하여 데이터를 찾을 때 결과가 다양한 필드 및 패싯을 표시합니다. 그러나 표시하는 정보 중 일부는 매우 자세히 나타나지 않을 수 있습니다. 다음 정보를 사용하여 결과를 이해할 수 있습니다.

<table border="1" cellspacing="4" cellpadding="4">
	<tr>
		<th><b>필드</b></th>
		<th><b>검색 유형<b></th>
		<th><b>설명</b></th>
	</tr>
	<tr>
		<td>
		<p>TenantId</p>
		</td>
		<td>
		<p>모두</p>
		</td>
		<td>
		<p>데이터를 분할하는 데 사용됨</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>TimeGenerated</p>
		</td>
		<td>
		<p>모두</p>
		</td>
		<td>
		<p>타임라인을 구동하는 데 사용되는 timeselectors입니다.(검색 및 다른 화면에서) 데이터의 일부가 생성된 때를 나타냅니다.(일반적으로 에이전트에서) ISO 형식으로 표현되는 시간이며 항상 UTC입니다. 기존 계측을 기반으로 하는 '형식'의 경우  (즉, 로그의 이벤트) 이는 일반적으로 실시간으로 로그 항목/선/레코드를 기록합니다. 관리 팩을 통해 또는 클라우드에서 생성되는 일부 다른 유형(즉, 권장 사항/경고/updateagent/등) 이는 일종의 구성 스냅숏을 사용하여 새 데이터 조각이 수집되거나 그에 기반하여 권장 사항/경고를 발생시키는 시간입니다.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventID</p>
		</td>
		<td>
		<p>이벤트</p>
		</td>
		<td>
		<p>Windows 이벤트 로그에서 EventID</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventLog</p>
		</td>
		<td>
		<p>이벤트</p>
		</td>
		<td>
		<p>Windows에서 이벤트를 로그한 이벤트 로그</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventLevelName</p>
		</td>
		<td>
		<p>이벤트</p>
		</td>
		<td>
		<p>중요 / 경고 / 정보 / 성공</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventLevel</p>
		</td>
		<td>
		<p>이벤트</p>
		</td>
		<td>
		<p>중요 / 경고 / 정보 / 성공에 대한 숫자 값(보다 쉽게/보다 읽기 쉬운 쿼리 대신 EventLevelName 사용)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SourceSystem</p>
		</td>
		<td>
		<p>모두</p>
		</td>
		<td>
		<p>데이터가 제공된 위치(서비스의 '연결' 관점에서 - 즉, Operations Manager 운영 통찰력(=데이터가 클라우드에서 생성됨), Azure 저장소 (WAD에서 가져온 데이터) 등</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ObjectName</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Windows 성능 개체 이름</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>InstanceName</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Windows 성능 카운터 인스턴스 이름</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>CounteName</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>Windows 성능 카운터 이름</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ObjectDisplayName</p>
		</td>
		<td>
		<p>PerfHourly, ConfigurationAlert, ConfigurationObject, ConfigurationObjectProperty</p>
		</td>
		<td>
		<p>Operations Manager에서 성능 수집 규칙이 대상으로 하는 개체 또는 Operational Insights에서 발견된 개체 또는 경고가 생성된 데에 반하는 표시 이름</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>RootObjectName</p>
		</td>
		<td>
		<p>PerfHourly, ConfigurationAlert, ConfigurationObject, ConfigurationObjectProperty</p>
		</td>
		<td>
		<p>Operations Manager에서 성능 수집 규칙이 대상으로 하는 개체 또는 Operational Insights에서 발견된 개체 또는 경고가 생성된 데에 반하는 부모의 표시 이름(이중 호스팅 관계 즉, Windows 컴퓨터에서 호스팅하는 SqlInstance에서 호스팅하는 SqlDatabase)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>컴퓨터</p>
		</td>
		<td>
		<p>대부분의 형식 </p>
		</td>
		<td>
		<p>데이터가 속하는 컴퓨터 이름</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>DeviceName</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>데이터가 속한 컴퓨터 이름('Computer'와 동일)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>DetectionId</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ThreatStatusRank</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>위협 상태 순위는 위협 상태에 대한 숫자 표현이며 HTTP 응답 코드와 유사합니다. 숫자 사이의 간격을 남겨두었으므로(100 또는 0이 아닌 150은 위협이 아닌 이유) 새 상태를 추가할 공간이 있습니다. 위협 상태 및 보호 상태를 롤업하는 경우 선택된 기간 동안 컴퓨터에 있었던 최악의 상태를 표시하려고 합니다. 숫자를 사용하여 다양한 상태 순위를 지정하므로 가장 높은 번호와 레코드를 찾을 수 있습니다.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ThreatStatus</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>Description of ThreatStatus,  ThreatStatusRank와 1:1로 매핑</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>TypeofProtection</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>컴퓨터에서 검색된 맬웨어 방지 제품은 없음, Microsoft 맬웨어 제거 도구, Forefront, 등입니다.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ScanDate</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p></p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SourceHealthServiceId</p>
		</td>
		<td>
		<p>ProtectionStatus, RequiredUpdate</p>
		</td>
		<td>
		<p>이 컴퓨터의 에이전트에 대한 HealthService ID</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>HealthServiceId</p>
		</td>
		<td>
		<p>대부분의 형식 </p>
		</td>
		<td>
		<p>이 컴퓨터의 에이전트에 대한 HealthService ID</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ManagementGroupName</p>
		</td>
		<td>
		<p>대부분의 형식 </p>
		</td>
		<td>
		<p>Operations Manager에 연결된 에이전트용 관리 그룹 이름입니다. 그렇지 않은 경우 null/blank가 됩니다.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ObjectType</p>
		</td>
		<td>
		<p>ConfigurationObject</p>
		</td>
		<td>
		<p>Operational Insights 구성 평가에서 발견한 개체의 유형(Operations Manager 관리 팩의 'type'/클래스처럼)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>UpdateTitle</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>설치되지 않고 발견된 업데이트의 이름</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>PublishDate</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>Microsoft update에 게시된 업데이트는 언제입니까?</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>서버</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>데이터가 속한 컴퓨터 이름('Computer'와 동일)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>제품</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>업데이트가 적용되는 제품</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>UpdateClassification</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>업데이트의 유형(업데이트 롤업, 서비스 팩 등)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>KBID</p>
		</td>
		<td>
		<p>RequiredUpdate</p>
		</td>
		<td>
		<p>모범 사례 또는 업데이트를 설명하는 KB 문서 ID</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>WorkflowName</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>경고를 생성하는 모니터 또는 규칙의 이름</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>심각도</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>경고의 심각도</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>우선 순위</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>경고의 우선 순위</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>IsMonitorAlert</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>이 경고는 모니터(true) 또는 규칙(false)에 의해 생성되었습니까?</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AlertParameters</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>Operational Insights 경고의 매개 변수를 사용 하는 XML</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Context</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>Operational Insights 경고의 '컨텍스트'를 사용 하는 XML</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>워크로드</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>경고를 참조하는 기술 또는 '워크로드' </p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AdvisorWorkload</p>
		</td>
		<td>
		<p>권장 사항</p>
		</td>
		<td>
		<p>권장 사항을 참조하는 기술 또는 '워크로드'</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>설명</p>
		</td>
		<td>
		<p>ConfigurationAlert</p>
		</td>
		<td>
		<p>경고 설명(간략)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>DaysSinceLastUpdate</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>WSUS/Microsoft Update에서 이 에이전트가 업데이트를 설치한 날은 언제입니까?(이 레코드의 'TimeGenerated'과 관련)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>DaysSinceLastUpdateBucket</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>DaysSinceLastUpdate에 따라 얼마나 경과했는지 '시간 버킷'에서 분류하는 것은 WSUS/Microsoft Update에서 마지막으로 업데이트를 설치한 컴퓨터였습니다.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AutomaticUpdateEnabled</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>자동 업데이트가 이 에이전트에서 활성화 또는 비활성화를 확인하고 있습니까?</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AutomaticUpdateValue</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>자동 업데이트가 자동으로 다운로드 및 설치, 다운로드만 또는 확인만 하도록 설정을 확인하고 있습니까?</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>WindowsUpdateAgentVersion</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>Microsoft 업데이트 에이전트의 버전 번호</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>WSUSServer</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>어떤 WSUS 서버가 이 업데이트 에이전트를 대상으로 합니까?</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>OSVersion</p>
		</td>
		<td>
		<p>UpdateAgent</p>
		</td>
		<td>
		<p>이 업데이트 에이전트에서 실행 중인 운영 체제의 버전</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>이름</p>
		</td>
		<td>
		<p>Recommendation, ConfigurationObjectProperty</p>
		</td>
		<td>
		<p>권장 사항의 이름/제목, 또는 Operational Insights 구성 평가에서 속성의 이름 </p>
		</td>
	</tr>
	<tr>
		<td>
		<p>값</p>
		</td>
		<td>
		<p>ConfigurationObjectProperty</p>
		</td>
		<td>
		<p>Operational Insights 구성 평가에서 속성의 값</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>KBLink</p>
		</td>
		<td>
		<p>권장 사항</p>
		</td>
		<td>
		<p>모범 사례 또는 업데이트를 설명하는 KB 문서의 URL</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>RecommendationStatus</p>
		</td>
		<td>
		<p>권장 사항</p>
		</td>
		<td>
		<p>권장 사항은 레코드가 '업데이트'를 얻는 몇가지 형식 사이에 있으며 검색 인덱스에 추가되지 않습니다. 권장 구성을 액티브/열기로 했는지 또는 Operational Insights이 해결된 것을 감지했는지를 이 상태가 변경합니다.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>RenderedDescription</p>
		</td>
		<td>
		<p>이벤트</p>
		</td>
		<td>
		<p>Windows 이벤트의 렌더링된 설명(채워진 매개 변수와 재사용된 텍스트)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ParameterXml</p>
		</td>
		<td>
		<p>이벤트</p>
		</td>
		<td>
		<p>Windows 이벤트(이벤트 뷰어처럼)의 '데이터' 섹션에서 매개 변수를 사용하는 XML</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventData</p>
		</td>
		<td>
		<p>이벤트</p>
		</td>
		<td>
		<p>Windows 이벤트(이벤트 뷰어처럼)의 전체 '데이터' 섹션을 사용하는 XML</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>원본</p>
		</td>
		<td>
		<p>이벤트</p>
		</td>
		<td>
		<p>이벤트를 생성하는 이벤트 로그 원본</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>EventCategory</p>
		</td>
		<td>
		<p>이벤트</p>
		</td>
		<td>
		<p>Windows 이벤트 로그에서 직접 이벤트의 범주</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>사용자 이름</p>
		</td>
		<td>
		<p>이벤트</p>
		</td>
		<td>
		<p>Windows 이벤트의 사용자 이름(일반적으로 NT AUTHORITY\LOCALSYSTEM)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SampleValue</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>성능 카운터의 시간별 집계에 대한 평균 값 </p>
		</td>
	</tr>
	<tr>
		<td>
		<p>최소</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>성능 카운터 매시간 집계의 시간 간격의 최소값</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>최대</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>성능 카운터 매시간 집계의 시간 간격의 최대값</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Percentile95</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>성능 카운터 매시간 집계의 시간 간격의 95번째 백분위 값</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SampleCount</p>
		</td>
		<td>
		<p>PerfHourly</p>
		</td>
		<td>
		<p>얼마나 많은 '원시' 성능 카운터 샘플이 매시간 집계 레코드를 생성하는데 사용됩니까</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>위협</p>
		</td>
		<td>
		<p>ProtectionStatus</p>
		</td>
		<td>
		<p>발견한 맬웨어의 이름</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>StorageAccount</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>로그를 읽은 azure 저장소 계정</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>AzureDeploymentID</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>로그가 속한 클라우드 서비스의 Azure 배포 ID</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>역할</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>로그가 속한 Azure 클라우드 서비스의 역할</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>RoleInstance</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>로그가 속한 Azure 역할의 RoleInstance</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>sSiteName</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>로그가 속한 IIS 웹 사이트(메타베이스 표기법);원래 로그에서 s-sitename 필드</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>sComputerName</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>원래 로그에서 s-computername 필드</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>sIP</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>HTTP 요청이 지정된 서버 IP 주소입니다. 원래 로그에서 s-ip 필드</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csMethod</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>HTTP 요청에서 클라이언트가 사용한 HTTP 메서드(GET/POST/등)입니다. 원래 로그에서 cs-method</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>cIP</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>HTTP 요청이 발생한 클라이언트 IP 주소 원래 로그에서 c-ip 필드</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csUserAgent</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>클라이언트가 선언한 HTTP 사용자 에이전트(브라우저 또는 그렇지 않은 경우) 원래 로그에서 cs-user-agent</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>scStatus</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>서버에서 클라이언트에 반환한 HTTP 상태 코드(200/403/500/등)입니다. 원래 로그에서 cs-status</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>TimeTaken</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>요청이 완료되는데 얼마나 걸립니까(밀리초). 원래 로그에서 timetaken 필드</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csUriStem</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>요청된 상대 Uri( 호스트 주소 즉, '/검색'없이)입니다. 원래 로그에서 cs-uristem 필드</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csUriQuery</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>URI 쿼리입니다. URI 쿼리는 ASP 페이지와 같은 동적 페이지에만 필요하므로 이 필드는 일반적으로 정적 페이지에 대한 하이픈을 포함합니다.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>sPort</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>HTTP 요청이 전송된 서버 포트(선택되었기 때문에 IIS에서 수신함)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csUserName</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>인증된 사용자 이름, 요청이 인증되고 익명이 아닌 경우</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csVersion</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>요청에 사용되는 HTTP 프로토콜 버전(즉,  'HTTP/1.1')</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csCookie</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>쿠키 정보</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csReferer</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>사용자가 마지막으로 방문한 사이트입니다. 이 사이트는 현재 사이트에 링크를 제공했습니다. </p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csHost</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>요청된 호스트 헤더(예:' www.mysite.com')</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>scSubStatus</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>하위 상태 오류 코드</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>scWin32Status</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>Windows 상태 코드</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>csBytes</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>클라이언트에서 서버로 요청에서 보낸 바이트</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>scBytes</p>
		</td>
		<td>
		<p>W3CIISLog</p>
		</td>
		<td>
		<p>서버에서 클라이언트로 응답을 다시 반환된 바이트</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ConfigChangeType </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>변경의 유형(WindowsServices / 소프트웨어 / 등)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>ChangeCategory </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>변경의 범주(수정 / 추가 / 제거됨)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SoftwareType </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>소프트웨어의 종류 (업데이트 / 응용 프로그램)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SoftwareName </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>소프트웨어의 이름(소프트웨어 변경에 적용) </p>
		</td>
	</tr>
	<tr>
		<td>
		<p>게시자 </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>소프트웨어를 게시하는 공급 업체(소프트웨어 변경에 적용) </p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcChangeType </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Windows 서비스에 적용된 변경의 유형(상태 / StartupType / 경로 / ServiceAccount) - Windows 서비스 변경 사항에만 적용</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcDisplayName </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>변경된 서비스의 표시 이름입니다</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcName </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>변경된 서비스의 이름</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcState </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>서비스의 새 (현재) 상태입니다.</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPreviousState</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>이전에 알려진 서비스의 상태(서비스 상태를 변경하는 경우에 해당) </p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcStartupType </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>서비스 시작 유형</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPreviousStartupType</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>이전 서비스 시작 유형(서비스 시작 유형을 변경하는 경우에 해당)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcAccount </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>서비스 계정</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPreviousAccount</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>이전 서비스 계정(서비스 계정이 변경되는 경우에 해당)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPath</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Windows 서비스의 실행 파일 경로</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcPreviousPath</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>Windows 서비스에 대한 실행 파일의 이전 경로(이것이 변경된 경우에 해당)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>SvcDescription</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>서비스에 대한 설명</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>이전 </p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>이 소프트웨어의 이전 상태(설치됨 / 설치되지 않음 / 이전 버전)</p>
		</td>
	</tr>
	<tr>
		<td>
		<p>Current</p>
		</td>
		<td>
		<p>ConfigurationChange</p>
		</td>
		<td>
		<p>이 소프트웨어의 최근 상태(설치됨 / 설치되지 않음 / 현재 버전)</p>
		</td>
	</tr>
</table>

## 블로그 게시물 - 검색 사용 사례
- [Microsoft Azure Operational Insights의 W3C IIS 로그 검색](http://blogs.msdn.com/b/dmuscett/archive/2014/09/20/w3c-iis-logs-search-in-system-center-advisor-limited-preview.aspx)
- [Azure Operational Insights 검색 및 대시보드를 사용한 SQL 백업 기능 모니터링](http://blogs.msdn.com/b/dmuscett/archive/2015/02/21/monitoring-sql-backup-failures-with-azure-operational-insights-search-and-dashboards.aspx)
- [IIS 관리 팩 이벤트-경고 규칙의 OpInsights 검색과 동일](http://blogs.msdn.com/b/dmuscett/archive/2014/11/05/iis-mp-event-alerting-rules-s-opinsights-searches-equivalents.aspx)
- [유용한 Operational Insights 검색 쿼리 컬렉션](http://blogs.msdn.com/b/dmuscett/archive/2014/10/19/advisor-searches-collection.aspx)

## 기타 리소스
Stefan Roth가 편리한 검색 치트 시트를 만들었습니다. 자세한 내용을 확인하고 치트 시트를 다운로드하려면 [블로그](http://stefanroth.net/2014/11/05/microsoft-azure-operational-insights-search-data-explorer-cheat-sheet/)를 확인하세요.

<!---HONumber=Sept15_HO3-->