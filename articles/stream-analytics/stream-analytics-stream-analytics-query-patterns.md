<properties
	pageTitle="Azure 스트림 분석 쿼리 패턴 | Microsoft Azure"
	description="일반적인 Azure 스트림 분석 쿼리 패턴"
	keywords="stream analytics, sample, query, language, guide, patterns"
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data"
	ms.date="10/05/2015"
	ms.author="jeffstok"/>


# 일반적인 Azure 스트림 분석 쿼리 패턴  #

## 소개 ##
Azure Stream 분석의 쿼리는 [여기](https://msdn.microsoft.com/library/azure/dn834998.aspx)에 문서화된 SQL과 유사한 언어로 표현됩니다. 이 문서에서는 실제 시나리오에 따른 몇가지 일반적인 쿼리 패턴에 대한 솔루션을 간략적으로 설명합니다. 이 작업은 진행중이며 새 패턴이 지속적으로 업데이트됩니다.

## 기본 사항 ##

## 데이터 형식 변환 ##
**설명**: 입력 스트림의 속성 형식을 정의합니다. 예를 들어 자동차 무게가 입력 스트림에 문자열 형식으로 설정되면 합계를 수행하기 위해 INT로 변환해야 합니다.

**입력**:

| 계정을 | Time | 무게 |
| --- | --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z | "1000" |
| Honda | 2015-01-01T00:00:02.0000000Z | "2000" |

**출력**:

| 계정을 | 무게 |
| --- | --- |
| Honda | 3000 |

**솔루션**:

	SELECT
    	Make,
    	SUM(CAST(Weight AS BIGINT)) AS Weight
	FROM
    	Input TIMESTAMP BY Time
	GROUP BY
		Make,
    	TumblingWindow(second, 10)

**설명**: 무게 필드의 CAST 문을 사용하여 해당 형식을 지정합니다(지원되는 데이터 형식의 목록을 보려면[여기](https://msdn.microsoft.com/library/azure/dn835065.aspx)를 참조).

## 패턴을 일치시키기 위해 Like/Not like를 사용합니다. ##
**설명**: 특정 패턴과 일치시키기 위해 이벤트의 필드 값을 확인합니다. 예를들어 A로 시작하고 9로 끝나는 번호판을 반환합니다.

**입력**:

| 계정을 | LicensePlate | Time |
| --- | --- | --- |
| Honda | ABC 123 | 2015-01-01T00:00:01.0000000Z |
| Toyota | AAA 999 | 2015-01-01T00:00:02.0000000Z |
| Nissan | ABC 369 | 2015-01-01T00:00:03.0000000Z |

**출력**:

| 계정을 | LicensePlate | Time |
| --- | --- | --- |
| Toyota | AAA 999 | 2015-01-01T00:00:02.0000000Z |
| Nissan | ABC 369 | 2015-01-01T00:00:03.0000000Z |

**솔루션**:

	SELECT
    	*
	FROM
    	Input TIMESTAMP BY Time
	WHERE
    	LicensePlate LIKE 'A%9'

**설명**: LIKE 문을 사용하여 LicensePlate 필드 값이 A로 시작하고 0개 이상 문자의 모든 문자열이 있는지와 9로 끝나는지 확인합니다.

## 다른 사례/값(CASE 문)에 대한 논리를 지정합니다. ##
**설명**: 일부 조건에 따른 필드에 대한 다른 계산을 제공합니다. 예를 들어, 1이라는 특수 사례를 가진 동일한 브랜드의 차가 몇 대 통과했는지에 대한 문자열 설명을 제공합니다.

**입력**:

| 계정을 | Time |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**출력**:

| CarsPassed | Time |
| --- | --- | --- |
| 1 Honda | 2015-01-01T00:00:10.0000000Z |
| 2 Toyotas | 2015-01-01T00:00:10.0000000Z |

**솔루션**:

    SELECT
    	CASE
			WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
			ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
		END AS CarsPassed,
		System.TimeStamp AS Time
	FROM
		Input TIMESTAMP BY Time
	GROUP BY
		Make,
		TumblingWindow(second, 10)

**설명**: CASE 절을 사용하면 일부 조건에 따른 다른 계산을 제공할 수 있습니다(이 경우에는 집계 창에 있는 자동차의 수).

## 데이터를 여러 출력으로 보내기 ##
**설명**: 데이터를 단일 작업에서 여러 출력 대상으로 보냅니다. 예를 들어 임계점 기반 경고에 대한 데이터를 분석하고 blob 저장소에 모든 이벤트를 보관합니다.

**입력**:

| 계정을 | Time |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**출력1**:

| 계정을 | Time |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**출력2**:

| 계정을 | Time | 개수 |
| --- | --- | --- |
| Toyota | 2015-01-01T00:00:10.0000000Z | 3 |

**솔루션**:

	SELECT
		*
	INTO
		ArchiveOutput
	FROM
		Input TIMESTAMP BY Time

	SELECT
		Make,
		System.TimeStamp AS Time,
		COUNT(*) AS [Count]
	INTO
		AlertOutput
	FROM
		Input TIMESTAMP BY Time
	GROUP BY
		Make,
		TumblingWindow(second, 10)
	HAVING
		[Count] >= 3

**설명**: INTO 절은 이 문의 데이터를 어떤 출력에 쓸 것인지에 대한 스트림 분석을 알려줍니다. 첫 번째 쿼리는 ArchiveOutput이라고 지정한 출력에 받은 통과한 데이터입니다. 두 번째 쿼리는 일부 간단한 집계와 필터링을 하고 다운스트림 경고 시스템에 결과를 보냅니다. *참고*: 여려 출력문에서 CTE(즉, WITH 문)의 결과를 재사용할 수 있습니다. 입력 원본의 판독기보다 더 적게 열 수 있는 추가적 이점을 가집니다.

	WITH AllRedCars AS (
		SELECT
			*
		FROM
			Input TIMESTAMP BY Time
		WHERE
			Color = 'red'
	)
	SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
	SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'

## 패턴 ##

## 고유 값 계산
**설명**: 시간 창 안의 스트림에 나타나는 고유 필드 값 개수를 계산합니다. 예를 들어, 2초 창 안에 톨게이트를 통과한 고유한 브랜드의 차는 몇 대입니까?

**입력**:

| 계정을 | Time |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**출력:**

| 개수 | Time |
| --- | --- |
| 2 | 2015-01-01T00:00:02.000Z |
| 1 | 2015-01-01T00:00:04.000Z |

**해결 방법:**

	WITH Makes AS (
	    SELECT
	        Make,
	        COUNT(*) AS CountMake
	    FROM
	        Input TIMESTAMP BY Time
	    GROUP BY
	          Make,
	          TumblingWindow(second, 2)
	)
	SELECT
	    COUNT(*) AS Count,
	    System.TimeStamp AS Time
	FROM
	    Makes
	GROUP BY
	    TumblingWindow(second, 1)


**설명:** 창 위에 개수를 포함한 고유한 값을 얻기 위해 초기 집계를 합니다. 그리고 얻은 결과에 대한 집계를 합니다. 창에서 주어진 모든 고유 값이 동일한 타임스탬프를 얻은 경우 두 번째 집계 창은 첫 번째 단계의 2개의 창을 집계하지 않기 위해 최소값이 되어야 합니다.

## 값이 변경되었는지 여부를 확인합니다. ##
**설명**:현재 값과 다른지 확인하기 위해 이전 값을 살펴봅니다. 예를 들어, 유료 도로에 있는 이전 차의 결과와 현재 찾의 결과가 같은 브랜드인지 살펴봅니다.

**입력**:

| 계정을 | Time |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |

**출력**:

| 계정을 | Time |
| --- | --- |
| Toyota | 2015-01-01T00:00:02.0000000Z |

**솔루션**:

	SELECT
		Make,
		Time
	FROM
		Input TIMESTAMP BY Time
	WHERE
		LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make

**설명**: LAG을 사용하여 지난 이벤트의 입력 스트림을 살펴보고, Make 값을 얻습니다. 그런 다음 현재 이벤트와 출력 이벤트의 Make를 비교합니다.

## 창에서 첫 번째 이벤트 찾기 ##
**설명**: 매 10분 간격으로 첫 번째 자동차를 찾습니다.

**입력**:

| LicensePlate | 계정을 | Time |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| RMV 8282 | Honda | 2015-07-27T00:05:01.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**출력**:

| LicensePlate | 계정을 | Time |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |

**솔루션**:

	SELECT 
		LicensePlate,
		Make,
		Time
	FROM 
		Input TIMESTAMP BY Time
	WHERE 
		IsFirst(minute, 10) = 1

이제 10분 간격마다 특정 브랜드의 첫 번째 차를 찾도록 문제를 변경해보겠습니다.

| LicensePlate | 계정을 | Time |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**솔루션**:

	SELECT 
		LicensePlate,
		Make,
		Time
	FROM 
		Input TIMESTAMP BY Time
	WHERE 
		IsFirst(minute, 10) OVER (PARTITION BY Make) = 1

## 창에서 마지막 이벤트 찾기 ##
**설명**: 매 10분 간격마다 마지막 자동차를 찾습니다.

**입력**:

| LicensePlate | 계정을 | Time |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| RMV 8282 | Honda | 2015-07-27T00:05:01.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**출력**:

| LicensePlate | 계정을 | Time |
| --- | --- | --- |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**솔루션**:

	WITH LastInWindow AS
	(
		SELECT 
			MAX(Time) AS LastEventTime
		FROM 
			Input TIMESTAMP BY Time
		GROUP BY 
			TumblingWindow(minute, 10)
	)
	SELECT 
		Input.LicensePlate,
		Input.Make,
		Input.Time
	FROM
		Input TIMESTAMP BY Time 
		INNER JOIN LastInWindow
		ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
		AND Input.Time = LastInWindow.LastEventTime

**설명**: 쿼리에는 두 단계가 있습니다. - 첫 번째는 10분 창에서 최신 타임스탬프를 찾는 것입니다. 두 번째 단계는 각 창의 최신 타임스탬프와 일치하는 이벤트를 찾기 위해 원본 스트림을 사용한 첫 번째 쿼리의 결과를 조인합니다.

## 이벤트의 부재를 검색합니다. ##
**설명**: 스트림이 특정 조건에 일치하지 않는 값이 있는지 확인합니다. 예를 들어, 동일한 브랜드의 연속된 2대의 차가 90초 이내에 유료 도로로 진입했는지 확인합니다.

**입력**:

| 계정을 | LicensePlate | Time |
| --- | --- | --- |
| Honda | ABC 123 | 2015-01-01T00:00:01.0000000Z |
| Honda | AAA 999 | 2015-01-01T00:00:02.0000000Z |
| Toyota | DEF 987 | 2015-01-01T00:00:03.0000000Z |
| Honda | GHI 345 | 2015-01-01T00:00:04.0000000Z |

**출력**:

| 계정을 | Time | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda | 2015-01-01T00:00:02.0000000Z | AAA 999 | ABC 123 | 2015-01-01T00:00:01.0000000Z |

**솔루션**:

	SELECT
	    Make,
	    Time,
	    LicensePlate AS CurrentCarLicensePlate,
	    LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
	    LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
	FROM
	    Input TIMESTAMP BY Time
	WHERE
	    LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make

**설명**: LAG을 사용하여 지난 이벤트의 입력 스트림을 살펴보고, Make 값을 얻습니다. 그런 다음 현재 이벤트와 출력 이벤트가 같은지 Make를 비교하고, LAG을 사용하여 이전 자동차에 대한 데이터를 얻습니다.

## 조건의 기간 감지 ##
**설명**: 조건의 시간이 얼마인지 알아봅니다. 예를 들어 버그가 생겨서 모든 자동차 결과가 정확하지 않은 경우(20.000파운드 이상) 버그의 기간을 계산하고자 합니다.

**입력**:

| 계정을 | Time | 무게 |
| --- | --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z | 2000 |
| Toyota | 2015-01-01T00:00:02.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:03.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:04.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:05.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:06.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:07.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:08.0000000Z | 2000 |

**출력**:

| StartFault | EndFault | FaultDurationSeconds |
| --- | --- | --- |
| 2015-01-01T00:00:01.0000000Z | 2015-01-01T00:00:08.0000000Z | 7 |
| 2015-01-01T00:00:01.0000000Z | 2015-01-01T00:00:08.0000000Z | 7 |
| 2015-01-01T00:00:01.0000000Z | 2015-01-01T00:00:08.0000000Z | 7 |
| 2015-01-01T00:00:01.0000000Z | 2015-01-01T00:00:08.0000000Z | 7 |
| 2015-01-01T00:00:01.0000000Z | 2015-01-01T00:00:08.0000000Z | 7 |
| 2015-01-01T00:00:01.0000000Z | 2015-01-01T00:00:08.0000000Z | 7 |

**솔루션**:

	SELECT
	    PrevGood.Time AS StartFault,
	    ThisGood.Time AS Endfault,
	    DATEDIFF(second, PrevGood.Time, ThisGood.Time) AS FaultDuraitonSeconds
	FROM
	    Input AS ThisGood TIMESTAMP BY Time
	    INNER JOIN Input AS PrevGood TIMESTAMP BY Time
	    ON DATEDIFF(second, PrevGood, ThisGood) BETWEEN 1 AND 3600
	    AND PrevGood.Weight < 20000
	    INNER JOIN Input AS Bad TIMESTAMP BY Time
	    ON DATEDIFF(second, PrevGood, Bad) BETWEEN 1 AND 3600
	    AND DATEDIFF(second, Bad, ThisGood) BETWEEN 1 AND 3600
	    AND Bad.Weight >= 20000
	    LEFT JOIN Input AS MidGood TIMESTAMP BY Time
	    ON DATEDIFF(second, PrevGood, MidGood) BETWEEN 1 AND 3600
	    AND DATEDIFF(second, MidGood, ThisGood) BETWEEN 1 AND 3600
	    AND MidGood.Weight < 20000
	WHERE
	    ThisGood.Weight < 20000
	    AND MidGood.Weight IS NULL

**설명**: 2개의 정상 이벤트 사이에 잘못된 이벤트가 있는 경우와 정상 이벤트가 없는 경우를 찾으려고 합니다. 이 말은, 2개의 이벤트는 처음 이벤트이고, 전후로 잘못된 이벤트가 최소한 1개 있어야 한다는 뜻입니다. 1개의 잘못된 이벤트가 중간에 있는 2개의 정상 이벤트를 얻으려면 간단히 2개의 JOIN을 사용하거나, 무게를 확인하고 타임스탬프를 비교하여 good->bad->good을 얻는 유효성 검사를 실행하여 얻을 수 있습니다.

“NULL을 포함하거나 이벤트 부재를 수행하도록 LEFT Outer 조인”에서 배웠던 선택한 2개의 정상 이벤트 사이에 잘못된 이벤트가 발생했는지 확인하는 방법을 사용합니다.

이러한 것들을 조합하여 중간에 잘못된 이벤트가 없는 good->bad->good을 얻습니다. 이제 버그의 기간을 알려주는 첫 번째 정상 이벤트와 마지막 정상 이벤트 사이의 기간을 계산할 수 있습니다.

## 도움말 보기
추가 지원이 필요할 경우 [Azure 스트림 분석 포럼](https://social.msdn.microsoft.com/Forums/KO-KR/home?forum=AzureStreamAnalytics)을 참조하세요.

## 다음 단계

- [Azure 스트림 분석 소개](stream-analytics-introduction.md)
- [Azure 스트림 분석 사용 시작](../stream.analytics.get.started.md)
- [Azure 스트림 분석 작업 규모 지정](stream-analytics-scale-jobs.md)
- [Azure 스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 스트림 분석 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 

<!---HONumber=Oct15_HO2-->