---
title: Azure Service Fabric CLI- sfctl choas| Microsoft Docs
description: "Service Fabric CLI sfctl chaos 명령을 설명합니다."
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/22/2017
ms.author: ryanwi
ms.openlocfilehash: dbea84511c37cf52c3d98f0247e5ce3c0b2a05c3
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/18/2018
---
# <a name="sfctl-chaos"></a>sfctl chaos
비정상 상황 테스트 서비스를 시작하고, 중지하고 보고합니다.

## <a name="commands"></a>명령

|명령|설명|
| --- | --- |
|    report| 전달된 연속 토큰 또는 전달된 시간 범위를 기반으로 Chaos 보고서의 다음 세그먼트를 가져옵니다.|
|    start | Chaos가 아직 클러스터에 실행되고 있지 않다면 Chaos 매개 변수에 지정된 값으로 Chaos 실행을 시작합니다.|
|    stop(정지)  | Chaos가 이미 실행 중이라면 클러스트에 있는 Chaos를 중단하고, 그렇지 않은 경우 아무 것도 하지 않습니다.|


## <a name="sfctl-chaos-report"></a>sfctl chaos report
전달된 연속 토큰 또는 전달된 시간 범위를 기반으로 Chaos 보고서의 다음 세그먼트를 가져옵니다.

비정상 상황 보고서의 다음 세그먼트를 가져오도록 ContinuationToken을 지정하거나, StartTimeUtc 및 EndTimeUtc를 통해 시간 범위를 지정할 수 있지만 ContinuationToken과 시간 범위를 같은 호출 내에서 지정할 수는 없습니다. 100개가 넘는 비정상 상황 이벤트가 있는 경우 비정상 상황 보고서는 세그먼트에 100개 이하의 비정상 상황 이벤트가 포함되어 있는 세그먼트로 반환됩니다. 

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --continuation-token| 연속 토큰 매개 변수는 다음 결과 집합을 얻는 데 사용됩니다. 공백 값이 아닌 연속 토큰은 시스템의 결과가 단일 응답에 맞지 않는 경우 API의 응답에 포함됩니다. 이 값이 다음 API 호출에 전달되면 API는 다음 결과 집합을 반환합니다. 결과가 더 이상 없으면 연속 토큰에 값이 포함되지 않습니다. 이 매개 변수의 값은 URL 인코딩이 되지 말아야 합니다.|
| --end-time-utc   | Chaos 보고서가 생성될 시간 범위의 종료 시간을 나타내는 틱 수입니다. 틱에 대한 자세한 내용은 [DateTime.Ticks 속성](https://msdn.microsoft.com/en-us/library/system.datetime.ticks%28v=vs.110%29)을 참조하세요.|
| --start-time-utc | Chaos 보고서가 생성될 시간 범위의 시작 시간을 나타내는 틱 수입니다. 틱에 대한 자세한 내용은 [DateTime.Ticks 속성](https://msdn.microsoft.com/en-us/library/system.datetime.ticks%28v=vs.110%29)을 참조하세요.|
| --timeout -t     | 서버 시간 제한(초).  기본값: 60.|

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug          | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
| --help -h        | 이 도움말 메시지 및 종료를 표시합니다.|
| --output -o      | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.  기본값: json.|
| --query          | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http://jmespath.org/를 참조하세요.|
| --verbose        | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="sfctl-chaos-start"></a>sfctl chaos start
Chaos가 아직 클러스터에 실행되고 있지 않다면 Chaos 매개 변수에 지정된 값으로 Chaos 실행을 시작합니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --app-type-health-policy-map  | 특정 응용 프로그램 형식에 대한 비정상 응용 프로그램 최대 비율을 사용하여 JSON 인코딩된 목록입니다. 각 항목은 지정된 응용 프로그램 형식의 응용 프로그램을 평가하는 데 사용된 MaxPercentUnhealthyApplications 백분율을 나타내는 응용 프로그램 형식 이름을 키로 그리고 정수를 값으로 지정합니다.|
| --disable-move-replica-faults | 기본 이동을 사용하지 않도록 설정하고 보조 오류를 이동합니다.|
| --max-cluster-stabilization| 모든 클러스터 엔티티가 안정적이고 정상화가 될 때까지 기다리는 최대 시간입니다.  기본값: 60.|
| --max-concurrent-faults    | 반복당 유도되는 동시 오류 최대 수           기본값: 1.|
| --max-percent-unhealthy-apps  | Chaos가 실행하는 동안 클러스터 상태를 평가할 때, 오류를 보고하기 전에 허용되는 비정상 응용 프로그램의 최대 백분율입니다.|
| --max-percent-unhealthy-nodes | Chaos가 실행하는 동안 클러스터 상태를 평가할 때, 오류를 보고하기 전에 허용되는 비정상 노드의 최대 백분율입니다.|
| --time-to-run              | Chaos가 자동으로 중지되기 전에 실행되는 총 시간(초)입니다. 최대 허용된 값은 4294967295(System.UInt32.MaxValue).  기본값: 4294967295.|
| --timeout -t               | 서버 시간 제한(초).  기본값: 60.|
| --wait-time-between-faults | 단일 반복 내에서 연속 오류 사이의 대기 시간(초)입니다.  기본값: 20.|
| --wait-time-between-iterations| 두 차례의 Chaos 연속 반복 간 시간-구분(초)입니다.  기본값: 30.|
| --warning-as-error         | Chaos가 실행하는 동안 클러스터 상태를 평가할 때 같은 심각도의 경고를 오류로 처리합니다.|

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug                    | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
| --help -h                  | 이 도움말 메시지 및 종료를 표시합니다.|
| --output -o                | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.           기본값: json.|
| --query                    | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http://jmespath.org/를 참조하세요.|
| --verbose                  | 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="sfctl-chaos-stop"></a>sfctl chaos stop
Chaos가 이미 실행 중이라면 클러스트에 있는 Chaos를 중단하고, 그렇지 않은 경우 아무 것도 하지 않습니다.

Chaos가 더 이상의 오류 작업 일정을 잡지 못하도록 막으나 처리 중인 오류에는 영향이 없습니다.

### <a name="arguments"></a>인수

|인수|설명|
| --- | --- |
| --timeout -t| 서버 시간 제한(초).  기본값: 60.|

### <a name="global-arguments"></a>전역 인수

|인수|설명|
| --- | --- |
| --debug  | 모든 디버그 로그를 표시하기 위해 로깅의 자세한 정도를 늘립니다.|
| --help -h| 이 도움말 메시지 및 종료를 표시합니다.|
| --output -o | 출력 형식.  허용되는 값: json, jsonc, 테이블, tsv.  기본값: json.|
| --query  | JMESPath 쿼리 문자열. 자세한 내용 및 예제는 http://jmespath.org/를 참조하세요.|
| --verbose| 로깅의 자세한 정도를 늘립니다. 전체 디버그 로그에 --debug을 사용합니다.|

## <a name="next-steps"></a>다음 단계
- Service Fabric CLI [설정](service-fabric-cli.md)
- [샘플 스크립트](/azure/service-fabric/scripts/sfctl-upgrade-application)를 사용하여 Microsoft Azure Service Fabric CLI를 사용하는 방법에 대해 알아봅니다.