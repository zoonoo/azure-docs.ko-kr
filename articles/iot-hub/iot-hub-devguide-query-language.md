---
title: 개발자 가이드 - 쿼리 언어 | Microsoft Docs
description: Azure IoT Hub 개발자 가이드 - IoT Hub에서 쌍, 메서드 및 작업에 대한 정보를 검색하는 데 사용되는 쿼리 언어에 대한 설명
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: elioda

---
# <a name="reference-query-language-for-twins-and-jobs"></a>참조 - 쌍 및 작업에 대한 쿼리 언어
## <a name="overview"></a>개요
IoT Hub는 [장치 쌍][lnk-twins] 및 [작업][lnk-jobs]과 관련된 정보 검색을 위한 강력한 SQL 유형의 언어를 제공합니다. 이 문서에 제공되는 내용:

* IoT Hub 쿼리 언어의 주요 기능 소개
* 언어에 대한 자세한 설명

## <a name="getting-started-with-twin-queries"></a>쌍 쿼리 시작
[장치 쌍][lnk-twins]은 임의의 JSON 개체를 태그와 속성으로 포함할 수 있습니다. IoT Hub는 모든 쌍 정보를 포함하는 단일 JSON 문서로 장치 쌍을 쿼리하도록 허용합니다.
예를 들어 IoT hub 쌍에 다음과 같은 구조가 있다고 가정하겠습니다.

        {                                                                      
            "deviceId": "myDeviceId",                                            
            "etag": "AAAAAAAAAAc=",                                              
            "tags": {                                                            
                "location": {                                                      
                    "region": "US",                                                  
                    "plant": "Redmond43"                                             
                }                                                                  
            },                                                                   
            "properties": {                                                      
                "desired": {                                                       
                    "telemetryConfig": {                                             
                        "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",            
                        "sendFrequencyInSecs": 300                                          
                    },                                                               
                    "$metadata": {                                                   
                    ...                                                     
                    },                                                               
                    "$version": 4                                                    
                },                                                                 
                "reported": {                                                      
                    "connectivity": {                                                
                        "type": "cellular"                            
                    },                                                               
                    "telemetryConfig": {                                             
                        "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",            
                        "sendFrequencyInSecs": 300,                                         
                        "status": "Success"                                            
                    },                                                               
                    "$metadata": {                                                   
                    ...                                                
                    },                                                               
                    "$version": 7                                                    
                }                                                                  
            }                                                                    
        }

IoT Hub는 **devices**라는 문서 컬렉션으로 장치 쌍을 노출합니다.
따라서 다음 쿼리는 전체적인 장치 쌍을 검색합니다.

        SELECT * FROM devices

> [!NOTE]
> [IoT Hub SDK][lnk-hub-sdks]는 큰 결과에 대한 페이징을 지원합니다.
> 
> 

IoT Hub는 임의의 조건으로 쌍 필터링을 검색하도록 허용합니다. 예를 들어

        SELECT * FROM devices
        WHERE tags.location.region = 'US'

위 코드는 **location.region** 태그가 **US**로 설정된 쌍을 검색합니다.
예를 들어, 부울 연산자 및 산술 비교도 지원됩니다.

        SELECT * FROM devices
        WHERE tags.location.region = 'US'
            AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60

위 코드는 미국 내에 있는 1분 이하의 간격으로 원격 분석을 보내도록 구성된 모든 쌍을 검색합니다. 편의를 위해 **IN** 및**NIN**(IN이 아님) 연산자와 함께 배열 상수를 사용할 수도 있습니다. 예를 들어

        SELECT * FROM devices
        WHERE property.reported.connectivity IN ['wired', 'wifi']

위 코드는 WiFi 또는 유선 연결을 보고한 모든 쌍을 검색합니다. 필터링 기능에 대한 전체 참조는 [WHERE 절][lnk-query-where] 섹션을 참조하세요.

그룹화 및 집계도 지원됩니다. 예를 들어

        SELECT properties.reported.telemetryConfig.status AS status,
            COUNT() AS numberOfDevices
        FROM devices
        GROUP BY properties.reported.telemetryConfig.status

위 코드는 원격 분석 구성 상태 각각의 장치 수를 반환합니다.

        [
            {
                "numberOfDevices": 3,
                "status": "Success"
            },
            {
                "numberOfDevices": 2,
                "status": "Pending"
            },
            {
                "numberOfDevices": 1,
                "status": "Error"
            }
        ]

위의 예제는 장치 3대가 구성 성공을 보고했고 2대는 아직 구성을 적용 중이고 1대는 오류를 보고한 상황을 보여줍니다.

### <a name="c-example"></a>C# 예제
쿼리 기능은 **RegistryManager** 클래스에서 [C# 서비스 SDK][lnk-hub-sdks]에 의해 노출됩니다.
다음은 간단한 예제 쿼리입니다.

        var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
        while (query.HasMoreResults)
        {
            var page = await query.GetNextAsTwinAsync();
            foreach (var twin in page) 
            {
                // do work on twin object
            }
        }

**query** 개체가 페이지 크기(최대 1000)로 인스턴스화되는 방식과, **GetNextAsTwinAsync** 메서드를 여러 번 호출하여 여러 페이지를 가져오는 방식에 주목합니다.
쿼리에 필요한 역직렬화 옵션에 따라서 쿼리 개체가 **Next\***를 여러 번 노출하는 것에 유의해야 합니다(예: 프로젝션을 사용할 때 사용될 쌍 또는 작업 개체 또는 일반 JSON).

### <a name="node-example"></a>노드 예제
쿼리 기능은 **Registry** 개체에서 [Node 서비스 SDK][lnk-hub-sdks]에 의해 노출됩니다.
다음은 간단한 예제 쿼리입니다.

        var query = registry.createQuery('SELECT * FROM devices', 100);
        var onResults = function(err, results) {
            if (err) {
                console.error('Failed to fetch the results: ' + err.message);
            } else {
                // Do something with the results
                results.forEach(function(twin) {
                    console.log(twin.deviceId);
                });

                if (query.hasMoreResults) {
                    query.nextAsTwin(onResults);
                }
            }
        };
        query.nextAsTwin(onResults);

**query** 개체가 페이지 크기(최대 1000)로 인스턴스화되는 방식과, **nextAsTwin** 메서드를 여러 번 호출하여 여러 페이지를 가져오는 방식에 주목합니다.
쿼리에 필요한 역직렬화 옵션에 따라서 쿼리 개체가 **next\***를 여러 번 노출하는 것에 유의해야 합니다(예: 프로젝션을 사용할 때 사용될 쌍 또는 작업 개체 또는 일반 JSON).

### <a name="limitations"></a>제한 사항
현재 프로젝션은 집계를 사용하는 경우에만 지원됩니다. 예: 집계되지 않은 쿼리는 `SELECT *`만 사용할 수 있습니다. 집계는 그룹화와 함께 사용하는 경우에만 지원됩니다.

## <a name="getting-started-with-jobs-queries"></a>작업 쿼리 시작
[작업][lnk-jobs]은 장치 집합에 대해 작업을 실행하는 방법을 제공합니다. 각 장치 쌍은 작업에 대한 정보를 포함하며 이것은 **jobs**라는 컬렉션에 속합니다.
로컬에서,

        {                                                                      
            "deviceId": "myDeviceId",                                            
            "etag": "AAAAAAAAAAc=",                                              
            "tags": {                                                            
                ...                                                              
            },                                                                   
            "properties": {                                                      
                ...                                                                 
            },
            "jobs": [
                { 
                    "deviceId": "myDeviceId",
                    "jobId": "myJobId",    
                    "jobType": "scheduleTwinUpdate",            
                    "status": "completed",                    
                    "startTimeUtc": "2016-09-29T18:18:52.7418462",
                    "endTimeUtc": "2016-09-29T18:20:52.7418462",
                    "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
                    "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
                    "outcome": {
                        "deviceMethodResponse": null   
                    }                                         
                },
                ...
            ]                                                             
        }

현재 이 컬렉션은 IoT Hub 쿼리 언어를 사용하여 **devices.jobs**로 쿼리할 수 있습니다.

예를 들어 단일 장치에 영향을 미치는 모든 작업(지난 작업 및 예정된 작업)을 가져오려면 다음 쿼리를 사용합니다.

        SELECT * FROM devices.jobs
        WHERE devices.jobs.deviceId = 'myDeviceId'

이 쿼리가 반환된 각 작업의 장치별 상태(및 가능한 경우 직접 메서드 응답)를 제공하는 방법에 유의합니다.
**devices.jobs** 컬렉션에 속하는 모든 개체의 속성에 대해 임의의 부울 조건을 사용하여 필터링을 할 수도 있습니다.
예를 들어, 다음 쿼리는:

        SELECT * FROM devices.jobs
        WHERE devices.jobs.deviceId = 'myDeviceId'
            AND devices.jobs.jobType = 'scheduleTwinUpdate'
            AND devices.jobs.status = 'completed'
            AND devices.jobs.createdTimeUtc > '2016-09-01'

2016년 9월 이후에 생성된 장치 **myDeviceId**에 대해 완료된 모든 쌍 업데이트 작업을 검색합니다.

단일 작업의 장치별 출력을 검색할 수도 있습니다.

        SELECT * FROM devices.jobs
        WHERE devices.jobs.jobId = 'myJobId'

### <a name="limitations"></a>제한 사항
현재 **devices.jobs**에 대한 쿼리는 다음을 지원하지 않습니다.

* 프로젝션(즉, `SELECT *`만 가능함)
* 위에 표시된 바와 같이 작업 속성 외에 장치 쌍을 참조하는 조건
* count, avg, group by와 같은 집계 수행

## <a name="basics-of-an-iot-hub-query"></a>IoT Hub 쿼리의 기초
모든 IoT Hub 쿼리는 SELECT 및 FROM 절로 이루어지며 선택적으로 WHERE 및 GROUP BY 절을 포함합니다. 모든 쿼리는 JSON 문서(예: 장치 쌍) 컬렉션에 대해 실행됩니다. FROM 절은 반복이 수행될 문서 컬렉션을 나타냅니다(예: **devices** 또는 **devices.jobs**). 그런 다음 WHERE 절의 필터가 적용됩니다. 집계의 경우 이러한 단계의 결과는 GROUP BY 절에 지정된 대로 그룹화되며 각 그룹에 대해서는 SELECT 절에 명시된 대로 행이 생성됩니다.

        SELECT <select_list>
        FROM <from_specification>
        [WHERE <filter_condition>]
        [GROUP BY <group_specification>]

## <a name="from-clause"></a>FROM 절
**FROM <from_specification>** 절은 두 가지 값만 가정할 수 있습니다. **FROM devices**는 장치 쌍을 쿼리하기 위해 **FROM devices.jobs**는 장치별 작업 세부 정보를 쿼리하기 위해 가정합니다.

## <a name="where-clause"></a>WHERE 절
**WHERE <filter_condition>** 절은 선택 사항입니다. FROM 컬렉션의 JSON 문서가 결과의 일부로 포함되기 위해 충족해야 하는 조건을 지정합니다. JSON 문서가 결과에 포함되려면 지정된 조건을 "true"로 평가해야 합니다.

허용되는 조건은 [식 및 조건][lnk-query-expressions] 섹션에 설명되어 있습니다.

## <a name="select-clause"></a>SELECT 절
SELECT 절(**SELECT <select_list>**)은 필수이며 쿼리를 통해 검색해서 가져올 값을 지정합니다. 새로운 JSON 개체를 생성하는 데 사용될 JSON 값을 지정합니다. FROM 컬렉션의 필터링된(그리고 선택적으로 그룹화된) 하위 집합의 각 요소에 대해 프로젝션 단계는 SELECT 절에 지정된 값을 사용하여 구성된 새 JSON 개체를 생성합니다.

다음은 SELECT 절의 문법입니다.

        SELECT [TOP <max number>] <projection list>

        <projection_list> ::=
            '*'
            | <projection_element> AS alias [, <projection_element> AS alias]+

        <projection_element> :==
            attribute_name
            | <projection_element> '.' attribute_name
            | <aggregate>

        <aggregate> :==
            count(<projection_element>) | count()
            | avg(<projection_element>) | avg()
            | sum(<projection_element>) | sum()
            | min(<projection_element>) | min()
            | max(<projection_element>) | max()

**attribute_name**은 FROM 컬렉션에 있는 JSON 문서의 속성을 말합니다. SELECT 절에 대한 예제는 [쌍 쿼리 시작][lnk-query-getstarted] 섹션에서 찾을 수 있습니다.

현재 **SELECT \***와 다른 선택 절은 쌍에 대한 집계 쿼리에서만 지원됩니다.

## <a name="group-by-clause"></a>GROUP BY 절
**GROUP BY <group_specification>** 절은 WHERE 절에 지정된 필터링 후에, 그리고 SELECT에 지정된 프로젝션 전에 실행될 수 있는 선택적인 단계입니다. 특성의 값을 기반으로 문서를 그룹화합니다. 이러한 그룹은 SELECT 절에 지정된 대로 집계된 값을 생성하는 데 사용됩니다.

GROUP BY를 사용한 쿼리의 예:

        SELECT properties.reported.telemetryConfig.status AS status,
            COUNT() AS numberOfDevices
        FROM devices
        GROUP BY properties.reported.telemetryConfig.status

GROUP BY에 대한 형식 구문:

        GROUP BY <group_by_element>
        <group_by_element> :==
            attribute_name
            | < group_by_element > '.' attribute_name

**attribute_name**은 FROM 컬렉션에 있는 JSON 문서의 속성을 말합니다. 

현재 GROUP BY 절은 쌍을 쿼리하는 경우에만 지원됩니다.

## <a name="expressions-and-conditions"></a>식 및 조건
높은 수준에서 *식*은:

* JSON 형식(예: 부울, 숫자, 문자열, 배열 또는 개체)의 인스턴스로 평가됩니다.
* 기본 제공되는 연산자와 함수를 사용하여 상수 및 장치 JSON 문서에서 오는 조작 데이터에 의해 정의됩니다.

*조건*은 부울로 평가되는 식입니다. 예를 들어, 부울 **true**와 다른 모든 상수는 **false**로 간주됩니다(**null**, **undefined**, 모든 개체 또는 배열 인스턴스, 모든 문자열 및 명확한 부울 **false** 포함).

식에 대한 구문:

        <expression> ::=
            <constant> |
            attribute_name |
            unary_operator <expression> |
            <expression> binary_operator <expression> |
            <create_array_expression> |
            '(' <expression> ')'

        <constant> ::=
            <undefined_constant>
            | <null_constant> 
            | <number_constant> 
            | <string_constant> 
            | <array_constant> 

        <undefined_constant> ::= undefined
        <null_constant> ::= null
        <number_constant> ::= decimal_literal | hexadecimal_literal
        <string_constant> ::= string_literal
        <array_constant> ::= '[' <constant> [, <constant>]+ ']'

설명:

| 기호 | 정의 |
| --- | --- |
| attribute_name |FROM 컬렉션에 있는 JSON 문서의 모든 속성입니다. |
| unary_operator |연산자 섹션의 모든 단항 연산자입니다. |
| binary_operator |연산자 섹션의 모든 이항 연산자입니다. |
| decimal_literal |소수 표기법으로 표현되는 부동 float입니다. |
| hexadecimal_literal |뒤에 16진수 문자열이 붙는 ‘0x’ 문자열로 표현되는 숫자입니다. |
| string_literal |문자열 리터럴은 연속적인 0이상의 유니코드 문자 또는 이스케이프 시퀀스로 표현되는 유니코드 문자열입니다. 문자열 리터럴은 작은따옴표(아포스트로피: ') 또는 큰따옴표(따옴표: ")로 묶어야 합니다. 허용되는 이스케이프: `\'`, `\"`, `\\`, 4개의 16진수로 정의되는 유니코드 문자인 경우 `\uXXXX` |

### <a name="operators"></a>연산자
다음과 같은 연산자가 지원됩니다.

| 패밀리 | 연산자 |
| --- | --- |
| 산술 |+,-,*,/,% |
| 논리 |AND, OR, NOT |
| 비교 |=, !=, <, >, <=, >=, <> |

## <a name="next-steps"></a>다음 단계
[IoT Hub SDK][lnk-hub-sdks]를 사용하여 앱에서 쿼리를 수행하는 방법을 알아봅니다.

[lnk-query-where]: iot-hub-devguide-query-language.md#where-clause
[lnk-query-expressions]: iot-hub-devguide-query-language.md#expressions-and-conditions
[lnk-query-getstarted]: iot-hub-devguide-query-language.md#getting-started-with-twin-queries

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md


<!--HONumber=Oct16_HO2-->


