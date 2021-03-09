---
title: 데이터 흐름 매핑 문제 해결
description: Azure Data Factory에서 데이터 흐름 문제를 해결 하는 방법에 대해 알아봅니다.
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/11/2020
ms.openlocfilehash: f8a852a8c4197169061a9c7633f4f363ad057337
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102505803"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Azure Data Factory에서 데이터 흐름 매핑 문제 해결

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 Azure Data Factory의 데이터 흐름 매핑을 위한 일반적인 문제 해결 방법을 살펴봅니다.

## <a name="common-error-codes-and-messages"></a>일반적인 오류 코드 및 메시지 

### <a name="error-code-df-executor-sourceinvalidpayload"></a>오류 코드: DF-Executor-SourceInvalidPayload
- **메시지**: 컨테이너가 없어 데이터 미리 보기, 디버그, 파이프라인 데이터 흐름 실행 실패
- **원인**: 데이터 집합에 저장소에 없는 컨테이너가 포함 되어 있습니다.
- **권장 사항**: 데이터 집합에서 참조 되는 컨테이너가 있고 액세스할 수 있는지 확인 합니다.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>오류 코드: DF-Executor-SystemImplicitCartesian

- **메시지**: INNER 조인에 대한 암시적 카테시안 제품은 지원되지 않습니다. 대신 CROSS 조인을 사용하세요. 조인에 사용된 열은 행에 대해 고유 키를 만들어야 합니다.
- **원인**: 논리 요금제 간의 내부 조인에 대 한 암시적 데카르트 제품은 지원 되지 않습니다. 조인에서 열을 사용 하는 경우 관계의 양쪽에서 하나 이상의 열이 있는 고유 키를 만듭니다.
- **권장 사항**: 같지 않음 기반 조인의 경우 사용자 지정 크로스 조인을 사용 합니다.

### <a name="error-code-df-executor-systeminvalidjson"></a>오류 코드: DF-Executor-SystemInvalidJson

- **메시지**: JSON 구문 분석 오류, 지원되지 않는 인코딩 또는 여러 줄
- **원인**: json 파일에 문제가 있을 수 있습니다. 지원 되지 않는 인코딩, 손상 된 바이트 또는 json 소스를 여러 중첩 된 줄에서 단일 문서로 사용할 수 있습니다.
- **권장 사항**: JSON 파일의 인코딩이 지원 되는지 확인 합니다. JSON 데이터 집합을 사용 하는 원본 변환에서 **Json 설정** 을 확장 하 고 **단일 문서** 를 설정 합니다.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>오류 코드: DF-Executor-BroadcastTimeout

- **메시지**: 브로드캐스트 조인 시간 초과 오류, 브로드캐스트 스트림이 디버그 실행에서 60초, 작업 실행에서 300초 안에 데이터를 생성하는지 확인
- **원인**: 브로드캐스트는 디버그 실행 시 기본 시간 제한은 60 초이 고 작업 실행 시 300 초입니다. 브로드캐스트에 대해 선택한 스트림이 너무 커서이 제한 내에 데이터를 생성할 수 없습니다.
- **권장 사항**: 조인, 존재 및 조회에 대 한 데이터 흐름 변환의 **최적화** 탭을 선택 합니다. 브로드캐스트의 기본 옵션은 **Auto** 입니다. **Auto** 가 설정 되어 있거나 **고정** 의 왼쪽 또는 오른쪽으로 브로드캐스트를 수동으로 설정 하는 경우 더 큰 IR (Azure integration runtime) 구성을 설정 하거나 브로드캐스트를 해제할 수 있습니다. 데이터 흐름에서 최상의 성능을 위해 Spark에서 **Auto** 를 사용 하 여 브로드캐스트할 수 있도록 하 고 메모리 최적화 Azure IR를 사용 하는 것이 좋습니다. 
 
  디버그 파이프라인 실행의 디버그 테스트 실행에서 데이터 흐름을 실행 하는 경우이 조건이 더 자주 실행 될 수 있습니다. 더 빠른 디버깅 환경을 유지 하기 위해 Azure Data Factory 브로드캐스트 시간 제한을 60 초로 제한 때문입니다. 트리거된 실행의 300 초 제한 시간으로 시간 제한을 확장할 수 있습니다. 이렇게 하려면 **디버그**  >  **사용 활동 런타임** 옵션을 사용 하 여 데이터 흐름 파이프라인 실행 작업에 정의 된 Azure IR를 사용할 수 있습니다.

- **메시지**: 브로드캐스트 조인 시간 초과 오류입니다 .이 문제를 방지 하려면 조인/존재/조회 변환에서 브로드캐스트 옵션의 ' 해제 '를 선택할 수 있습니다. 성능을 향상 시키기 위해 조인 옵션을 브로드캐스트하려면 브로드캐스트 스트림이 디버그 실행에서 60 초 내에 데이터를 생성할 수 있고 작업 실행에서 300 초 내에 데이터를 생성할 수 있는지 확인 합니다.
- **원인**: 브로드캐스트는 디버그 실행의 기본 시간 제한이 60 초이 고 작업 실행에서 300 초가 있습니다. 브로드캐스트 조인에서 브로드캐스트에 대해 선택한 스트림은 너무 커서이 제한 내에 데이터를 생성할 수 없습니다. 브로드캐스트 조인을 사용 하지 않는 경우 데이터 흐름의 기본 브로드캐스트는 동일한 제한에 도달할 수 있습니다.
- **권장 사항**: 브로드캐스트 옵션을 해제 하거나, 처리 하는 데 60 초 이상이 소요 될 수 있는 대량 데이터 스트림의 브로드캐스팅을 방지 합니다. 브로드캐스트할 더 작은 스트림을 선택 합니다. 많은 Azure SQL Data Warehouse 테이블 및 소스 파일은 일반적으로 좋은 선택이 아닙니다. 브로드캐스트 조인이 없으면이 오류가 발생 하는 경우 더 큰 클러스터를 사용 합니다.

### <a name="error-code-df-executor-conversion"></a>오류 코드: DF-Executor-Conversion

- **메시지**: 잘못된 문자로 인해 날짜 또는 시간 변환 실패
- **원인**: 데이터가 예상 된 형식이 아닙니다.
- **권장 사항**: 올바른 데이터 형식을 사용 합니다.

### <a name="error-code-df-executor-invalidcolumn"></a>오류 코드: DF-Executor-InvalidColumn
- **메시지**: 쿼리에 열 이름을 지정해야 합니다. SQL 함수를 사용하는 경우 별칭 설정
- **원인**: 열 이름이 지정 되지 않았습니다.
- **권장 사항**: min () 또는 max ()와 같은 SQL 함수를 사용 하는 경우 별칭을 설정 합니다.

### <a name="error-code-df-executor-drivererror"></a>오류 코드: DF-실행자-DriverError
- **메시지**: INT96은 ADF 데이터 흐름에서 지원 하지 않는 레거시 타임 스탬프 유형입니다. 열 유형을 최신 유형으로 업그레이드 하십시오.
- **원인**: 드라이버 오류입니다.
- **권장 사항**: INT96는 Azure Data Factory 데이터 흐름에서 지원 하지 않는 레거시 타임 스탬프 유형입니다. 열 형식을 최신 형식으로 업그레이드 하는 것이 좋습니다.

### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>오류 코드: DF-BlockCountExceedsLimitError
- **메시지**: 커밋되지 않은 블록 수는 최대 제한인 10만 블록을 초과할 수 없습니다. Blob 구성을 확인 하세요.
- **원인**: blob에서 커밋되지 않은 블록의 최대 수는 10만입니다.
- **권장 사항**:이 문제에 대 한 자세한 내용은 Microsoft 제품 팀에 문의 하세요.

### <a name="error-code-df-executor-partitiondirectoryerror"></a>오류 코드: DF
- **메시지**: 지정 된 원본 경로에 분할 된 디렉터리가 여러 개 있습니다 (예: <Source Path> /<Partition Root Directory 1>/a = 10/b = 20). <Source Path> /<파티션 루트 디렉터리 2>/c = 10/d = 30) 또는 다른 파일 또는 분할 되지 않은 디렉터리 (예: <Source Path> /<파티션 루트 디렉터리 1>/a = 10/b = 20,/ <Source Path> 디렉터리 2/file1)를 사용 하 여 파티션 루트 디렉터리를 제거 하 고 별도의 원본 변환을 통해 읽습니다.
- **원인**: 소스 경로에 분할 된 디렉터리가 여러 개 있거나 분할 된 디렉터리에 다른 파일이 나 분할 되지 않은 디렉터리가 있습니다.
- **권장 사항**: 원본 경로에서 분할 된 루트 디렉터리를 제거 하 고 별도의 원본 변환을 통해 읽습니다.

### <a name="error-code-df-executor-invalidtype"></a>오류 코드: DF-InvalidType
- **메시지**: 매개 변수 유형이 전달 된 값 유형과 일치 하는지 확인 하세요. 파이프라인에서 float 매개 변수를 전달 하는 것은 현재 지원 되지 않습니다.
- **원인**: 선언 된 형식에 대 한 데이터 형식이 실제 매개 변수 값과 호환 되지 않습니다.
- **권장 사항**: 데이터 흐름에 전달 된 매개 변수 값이 선언 된 형식과 일치 하는지 확인 합니다.

### <a name="error-code-df-executor-columnunavailable"></a>오류 코드: DF-Executor-ColumnUnavailable 수 없음
- **메시지**: 식에 사용 된 열 이름을 사용할 수 없거나 잘못 되었습니다.
- **원인**: 식에 사용 되는 열 이름이 잘못 되었거나 사용할 수 없습니다.
- **권장 사항**: 식에서 열 이름을 확인 합니다.

### <a name="error-code-df-executor-parseerror"></a>오류 코드: DF-실행자-ParseError
- **메시지**: 식을 구문 분석할 수 없습니다.
- **원인**: 잘못 된 형식으로 인해 식에서 구문 분석 오류가 발생 했습니다.
- **권장 사항**: 식의 형식을 확인 합니다.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>오류 코드: DF-Executor-SystemImplicitCartesian
- **메시지**: INNER 조인에 대한 암시적 카테시안 제품은 지원되지 않습니다. 대신 CROSS 조인을 사용하세요. 조인에 사용된 열은 행에 대해 고유 키를 만들어야 합니다.
- **원인**: 논리 요금제 간의 내부 조인에 대 한 암시적 데카르트 제품은 지원 되지 않습니다. 조인에 열을 사용 하는 경우 고유 키를 만듭니다.
- **권장 사항**: 같지 않음 기반 조인의 경우 CROSS JOIN을 사용 합니다.

### <a name="error-code-df-executor-systeminvalidjson"></a>오류 코드: DF-Executor-SystemInvalidJson
- **메시지**: JSON 구문 분석 오류, 지원되지 않는 인코딩 또는 여러 줄
- **원인**: json 파일에 문제가 있을 수 있습니다. 지원 되지 않는 인코딩, 손상 된 바이트 또는 json 소스를 여러 중첩 된 줄에서 단일 문서로 사용할 수 있습니다.
- **권장 사항**: JSON 파일의 인코딩이 지원 되는지 확인 합니다. JSON 데이터 집합을 사용 하는 원본 변환에서 **Json 설정** 을 확장 하 고 **단일 문서** 를 설정 합니다.



### <a name="error-code-df-executor-conversion"></a>오류 코드: DF-Executor-Conversion
- **메시지**: 잘못된 문자로 인해 날짜 또는 시간 변환 실패
- **원인**: 데이터가 예상 된 형식이 아닙니다.
- **권장 사항**: 올바른 데이터 형식을 사용 합니다.


### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>오류 코드: DF-BlockCountExceedsLimitError
- **메시지**: 커밋되지 않은 블록 수는 최대 제한인 10만 블록을 초과할 수 없습니다. Blob 구성을 확인 하세요.
- **원인**: blob에서 커밋되지 않은 블록의 최대 수는 10만입니다.
- **권장 사항**:이 문제에 대 한 자세한 내용은 Microsoft 제품 팀에 문의 하세요.

### <a name="error-code-df-executor-partitiondirectoryerror"></a>오류 코드: DF
- **메시지**: 지정 된 원본 경로에 분할 된 디렉터리가 여러 개 있습니다 (예: *<Source Path> /<Partition Root Directory 1>/a = 10/b = 20). <Source Path> /<파티션 루트 디렉터리 2>/c = 10/d = 30*) 또는 분할 된 디렉터리 (예: *<Source Path> /<파티션 루트 디렉터리 1>/A = 10/b = 20, <Source Path> /디렉터리 2/file1*), 소스 경로에서 파티션 루트 디렉터리를 제거 하 고 별도의 원본 변환을 통해 읽습니다.
- **원인**: 소스 경로에 분할 된 디렉터리가 여러 개 있거나 분할 된 디렉터리에 다른 파일이 나 분할 되지 않은 디렉터리가 있습니다. 
- **권장 사항**: 원본 경로에서 분할 된 루트 디렉터리를 제거 하 고 별도의 원본 변환을 통해 읽습니다.

### <a name="error-code-getcommand-outputasync-failed"></a>오류 코드: GetCommand OutputAsync 실패
- **메시지**: 데이터 흐름 디버그 및 데이터 미리 보기 중: GetCommand OutputAsync 실패 ...
- **원인**:이 오류는 백 엔드 서비스 오류입니다. 
- **권장 사항**: 작업을 다시 시도 하 고 디버깅 세션을 다시 시작 합니다. 다시 시도 하 고 다시 시작 해도 문제가 해결 되지 않으면 고객 지원에 문의 하세요. 

### <a name="error-code-df-executor-outofmemoryerror"></a>오류 코드: DF-OutOfMemoryError
 
- **메시지**: 실행 하는 동안 클러스터에 메모리 부족 문제가 발생 했습니다. 더 큰 코어 개수 및/또는 메모리 최적화 계산 형식으로 통합 런타임을 사용 하 여 다시 시도 하세요.
- **원인**: 클러스터에 메모리가 부족 합니다.
- **권장 사항**: 디버그 클러스터는 개발을 위한 것입니다. 데이터 샘플링 및 적절 한 계산 유형과 크기를 사용 하 여 페이로드를 실행 합니다. 성능 팁은 [데이터 흐름 성능 가이드 매핑](concepts-data-flow-performance.md)을 참조 하세요.

### <a name="error-code-df-executor-illegalargument"></a>오류 코드: DF-illegalArgument

- **메시지**: 연결 된 서비스의 액세스 키가 올바른지 확인 하세요.
- **원인**: 계정 이름 또는 액세스 키가 잘못 되었습니다.
- **권장 사항**: 연결 된 서비스에 지정 된 계정 이름 또는 액세스 키가 올바른지 확인 합니다. 

### <a name="error-code-df-executor-invalidtype"></a>오류 코드: DF-InvalidType
- **메시지**: 매개 변수 유형이 전달 된 값 유형과 일치 하는지 확인 하세요. 파이프라인에서 float 매개 변수를 전달 하는 것은 현재 지원 되지 않습니다.
- **원인**: 선언 된 형식에 대 한 데이터 형식이 실제 매개 변수 값과 호환 되지 않습니다. 
- **권장 사항**: 올바른 데이터 형식을 제공 합니다.

### <a name="error-code-df-executor-columnunavailable"></a>오류 코드: DF-Executor-ColumnUnavailable 수 없음
- **메시지**: 식에 사용 된 열 이름을 사용할 수 없거나 잘못 되었습니다.
- **원인**: 식에 잘못 되었거나 사용할 수 없는 열 이름이 사용 되었습니다.
- **권장 사항**: 식에 사용 된 열 이름을 확인 합니다.


### <a name="error-code-df-executor-parseerror"></a>오류 코드: DF-실행자-ParseError
- **메시지**: 식을 구문 분석할 수 없습니다.
- **원인**: 잘못 된 형식으로 인해 식에서 구문 분석 오류가 발생 했습니다. 
- **권장 사항**: 식의 형식을 확인 합니다.


 ### <a name="error-code-df-executor-outofdiskspaceerror"></a>오류 코드: DF-OutOfDiskSpaceError
- **메시지**: 내부 서버 오류
- **원인**: 클러스터의 디스크 공간이 부족 합니다.
- **권장 사항**: 파이프라인을 다시 시도 합니다. 그래도 문제가 해결 되지 않으면 고객 지원에 문의 하세요.


 ### <a name="error-code-df-executor-storeisnotdefined"></a>오류 코드: DF-StoreIsNotDefined
- **메시지**: 저장소 구성이 정의 되어 있지 않습니다. 이 오류는 파이프라인에서 잘못 된 매개 변수 할당으로 인해 발생할 수 있습니다.
- **원인**: 결정 되지 않음
- **권장 사항**: 파이프라인의 매개 변수 값 할당을 확인 합니다. 매개 변수 식에 잘못 된 문자가 포함 되어 있을 수 있습니다.

### <a name="error-code-df-excel-invalidconfiguration"></a>오류 코드: DF-Excel-InvalidConfiguration
- **메시지**: Excel 시트 이름 또는 인덱스가 필요 합니다.
- **원인**: 결정 되지 않음
- **권장 사항**: 매개 변수 값을 확인 합니다. Excel 데이터를 읽기 위한 워크시트 이름 또는 인덱스를 지정 합니다.

- **메시지**: Excel 시트 이름 및 인덱스는 동시에 존재할 수 없습니다.
- **원인**: 결정 되지 않음
- **권장 사항**: 매개 변수 값을 확인 합니다. Excel 데이터를 읽기 위한 워크시트 이름 또는 인덱스를 지정 합니다.

- **메시지**: 잘못 된 범위를 제공 했습니다.
- **원인**: 결정 되지 않음
- **권장 사항**: 매개 변수 값을 확인 합니다. 올바른 범위를 참조로 지정 하십시오. 자세한 내용은 [Excel 속성](./format-excel.md#dataset-properties)을 참조 하세요.

- **메시지**: .xlsx 및 .xls만 지원 되는 동안 잘못 된 excel 파일이 제공 됩니다.
- **원인**: 결정 되지 않음
- **권장 사항**: Excel 파일 확장명이 .xlsx 또는 .Xls 인지 확인 합니다.


 ### <a name="error-code-df-excel-invaliddata"></a>오류 코드: DF-Excel-InvalidData
- **메시지**: Excel 워크시트가 없습니다.
- **원인**: 결정 되지 않음
- **권장 사항**: 매개 변수 값을 확인 합니다. Excel 데이터를 읽기 위한 올바른 워크시트 이름 또는 인덱스를 지정 하십시오.

- **메시지**: 다른 스키마를 사용 하 여 excel 파일 읽기는 현재 지원 되지 않습니다.
- **원인**: 결정 되지 않음
- **권장 사항**: 지원 되는 Excel 파일을 사용 합니다.

- **메시지**: 데이터 형식이 지원 되지 않습니다.
- **원인**: 결정 되지 않음
- **권장 사항**: 지원 되는 Excel 파일 데이터 형식을 사용 합니다.

### <a name="error-code-4502"></a>오류 코드: 4502
- **메시지**: Integration Runtime의 제한으로 인해 오류가 발생 하는 상당한 동시 MappingDataflow 실행이 있습니다.
- **원인**: 많은 수의 데이터 흐름 작업 실행이 통합 런타임에서 동시에 발생 합니다. 자세한 내용은 [Azure Data Factory 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits)을 참조 하세요.
- **권장 사항**: 더 많은 데이터 흐름 작업을 병렬로 실행 하려면 여러 통합 런타임에 분산 합니다.


### <a name="error-code-invalidtemplate"></a>오류 코드: InvalidTemplate
- **메시지**: 파이프라인 식을 평가할 수 없습니다.
- **원인**: 구문 오류가 발생 하 여 데이터 흐름 작업에 전달 된 파이프라인 식이 올바르게 처리 되지 않습니다.
- **권장 사항**: 활동 모니터링에서 활동을 확인 하 여 식을 확인 합니다.

### <a name="error-code-2011"></a>오류 코드: 2011
- **메시지**: 작업이 Azure Integration Runtime에서 실행 중 이며, 자체 호스팅 Integration Runtime를 통해 연결 된 데이터 저장소 또는 계산의 자격 증명을 해독 하지 못했습니다. 이 작업과 연결 된 연결 된 서비스의 구성을 확인 하 고 적절 한 통합 런타임 유형을 사용 해야 합니다.
- **원인**: 데이터 흐름은 자체 호스팅 통합 런타임에서 연결 된 서비스를 지원 하지 않습니다.
- **권장 사항**: 관리 되는 Virtual Network integration runtime에서 실행 되도록 데이터 흐름을 구성 합니다.

## <a name="miscellaneous-troubleshooting-tips"></a>기타 문제 해결 팁
- **문제**: 예기치 않은 예외가 발생 하 여 실행이 실패 했습니다.
    - **메시지**: 데이터 흐름 활동 실행 중: 예기치 않은 예외로 실행에 실패했습니다.
    - **원인**:이 오류는 백 엔드 서비스 오류입니다. 작업을 다시 시도 하 고 디버깅 세션을 다시 시작 합니다.
    - **권장 사항**: 다시 시도 하 고 다시 시작 해도 문제가 해결 되지 않으면 고객 지원에 문의 하세요.

-  **문제**: 디버그 데이터 미리 보기 중에 조인에 대 한 출력 데이터가 없습니다.
    - **메시지**: 너무 적은 수의 샘플링으로 인해 발생할 수 있는 null 값 또는 누락 된 값이 많습니다. 디버그 행 제한을 업데이트 하 고 데이터를 새로 고쳐 보세요.
    - **원인**: 조인 조건이 행과 일치 하지 않거나 데이터 미리 보기 중에 많은 수의 null 값을 생성 했습니다.
    - **권장 사항**: **디버그 설정** 에서 원본 행 제한의 행 수를 늘립니다. 더 많은 데이터를 처리 하기에 충분 한 크기의 데이터 흐름 클러스터가 있는 Azure IR를 선택 해야 합니다.
    
- **문제**: 여러 줄로 된 CSV 파일이 있는 원본에서 유효성 검사 오류가 발생 했습니다. 
    - **메시지**: 다음 오류 메시지 중 하나가 표시 될 수 있습니다.
        - 마지막 열이 null 이거나 누락 되었습니다.
        - 원본에서 스키마 유효성 검사가 실패 합니다.
        - 스키마 가져오기는 UX에 올바르게 표시 되지 않으며 마지막 열에는 이름에 줄 바꿈 문자가 있습니다.
    - **원인**: 매핑 데이터 흐름에서는 \r\n이 행 구분 기호로 사용 될 때 여러 줄 CSV 원본 파일이 현재 작동 하지 않습니다. 캐리지 리턴 시 추가 줄이 있으면 오류가 발생할 수 있습니다. 
    - **권장 사항**: \r\n이 아닌 행 구분 기호로 \n을 사용 하 여 원본에서 파일을 생성 합니다. 또는 복사 작업을 사용 하 여 CSV 파일을 행 구분 기호로 \n을 사용 하도록 변환 합니다.

## <a name="general-troubleshooting-guidance"></a>일반 문제 해결 지침
1. 데이터 세트 연결의 상태를 확인합니다. 각 원본 및 싱크 변환에서 사용 중인 각 데이터 집합에 대 한 연결 된 서비스로 이동 하 고 연결을 테스트 합니다.
2. 데이터 흐름 디자이너에서 파일 및 테이블 연결의 상태를 확인 합니다. 디버그 모드에서 원본 변환의 **데이터 미리 보기** 를 선택 하 여 데이터에 액세스할 수 있는지 확인 합니다.
3. 데이터 미리 보기에서 모든 항목이 올바른 경우 파이프라인 디자이너로 이동 하 여 파이프라인 활동에 데이터 흐름을 배치 합니다. 엔드투엔드 테스트에 대해 파이프라인을 디버깅합니다.

## <a name="next-steps"></a>다음 단계

문제 해결에 대 한 자세한 내용은 다음 리소스를 참조 하세요.

*  [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 기능 요청](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)

