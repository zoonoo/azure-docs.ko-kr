---
title: 매핑 데이터 흐름 문제 해결
description: Azure Data Factory의 데이터 흐름 문제를 해결하는 방법을 알아봅니다.
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 03/25/2021
ms.openlocfilehash: 72ab685b58f7d940fe4d682cacba6212fe80ced8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933086"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Azure Data Factory의 매핑 데이터 흐름 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 매핑 데이터 흐름에 관한 일반적인 문제 해결 방법을 살펴봅니다.

## <a name="common-error-codes-and-messages"></a>일반적인 오류 코드 및 메시지 

### <a name="error-code-df-executor-sourceinvalidpayload"></a>오류 코드: DF-Executor-SourceInvalidPayload
- **메시지**: 컨테이너가 없어 데이터 미리 보기, 디버그, 파이프라인 데이터 흐름 실행 실패
- **원인**: 데이터 세트에 스토리지에 없는 컨테이너가 포함되어 있습니다.
- **권장 사항**: 데이터 세트에서 참조되는 컨테이너가 있으며 액세스할 수 있는지 확인합니다.

### <a name="error-code-df-executor-systeminvalidjson"></a>오류 코드: DF-Executor-SystemInvalidJson

- **메시지**: JSON 구문 분석 오류, 지원되지 않는 인코딩 또는 여러 줄
- **원인**: JSON 파일의 가능한 문제: 지원되지 않은 인코딩, 손상된 바이트, 여러 중첩 줄에 단일 문서로 JSON 원본 사용.
- **권장 사항**: JSON 파일의 인코딩이 지원되는지 확인합니다. JSON 데이터 세트를 사용하는 원본 변환에서 **JSON 설정** 을 확장하고 **단일 문서** 를 설정합니다.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>오류 코드: DF-Executor-BroadcastTimeout

- **메시지**: 브로드캐스트 조인 시간 초과 오류, 브로드캐스트 스트림이 디버그 실행에서 60초, 작업 실행에서 300초 안에 데이터를 생성하는지 확인
- **원인** : 브로드캐스트에는 디버그 실행 시 60초, 작업 실행 시 300초의 기본 시간 제한이 있습니다. 브로드캐스트용으로 선택한 스트림이 너무 커서 이 한도 안에 데이터를 생성하지 못합니다.
- **권장 사항**: 데이터 흐름 변환에서 조인, 존재 및 조회에 대해 **최적화** 탭을 선택합니다. 브로드캐스트의 기본 옵션은 **자동** 입니다. **자동** 이 설정되어 있거나 **고정** 에서 브로드캐스트하도록 왼쪽이나 오른쪽을 수동으로 설정할 경우 더 큰 Azure IR(통합 런타임) 구성을 설정하거나 브로드캐스트를 끌 수 있습니다. 데이터 흐름에서 최상의 성능을 얻으려면 **자동** 을 사용하여 Spark가 브로드캐스트하도록 허용하고 메모리 최적화 Azure IR을 사용하는 것이 좋습니다. 
 
  디버그 파이프라인 실행의 디버그 테스트 실행에서 데이터 흐름을 실행하는 경우 이 조건이 더 자주 발생할 수 있습니다. 더 빠른 디버깅 환경을 유지 관리하기 위해 Azure Data Factory에서 브로드캐스트 시간 제한을 60초로 제한하기 때문입니다. 시간 제한을 트리거된 실행의 시간 제한인 300초로 연장할 수 있습니다. 이렇게 하려면 **디버그** > **활동 런타임 사용** 옵션을 사용하여 데이터 흐름 실행 파이프라인 작업에 정의된 Azure IR을 사용할 수 있습니다.

- **메시지**: 브로드캐스트 조인 시간 제한 오류입니다. 조인/존재/ 조회 변환에서 브로드캐스트 옵션의 ‘끄기’를 선택하여 이 이슈를 방지할 수 있습니다. 성능 향상을 위해 조인 옵션을 브로드캐스트하려는 경우 브로드캐스트 스트림이 디버그 실행 시 60초, 작업 실행 시 300초 이내에 데이터를 생성할 수 있는지 확인합니다.
- **원인** : 브로드캐스트에는 디버그 실행 시 60초, 작업 실행 시 300초의 기본 시간 제한이 있습니다. 브로드캐스트 조인에서 브로드캐스트용으로 선택한 스트림이 너무 커서 이 한도 안에 데이터를 생성하지 못합니다. 브로드캐스트 조인을 사용하지 않으면 데이터 흐름별 기본 브로드캐스트가 같은 한도에 도달할 수 있습니다.
- **권장 사항**: 브로드캐스트 옵션을 끄거나, 처리하는 데 60초가 넘게 걸릴 수 있는 대용량 데이터 스트림은 브로드캐스트하지 않습니다. 더 작은 스트림을 브로드캐스트하도록 선택합니다. 대형 Azure SQL Data Warehouse 테이블과 원본 파일을 선택하는 것은 일반적으로 좋지 않습니다. 브로드캐스트 조인이 없는 경우 이 오류가 발생하면 더 큰 클러스터를 사용합니다.

### <a name="error-code-df-executor-conversion"></a>오류 코드: DF-Executor-Conversion

- **메시지**: 잘못된 문자로 인해 날짜 또는 시간 변환 실패
- **원인**: 데이터가 예상된 형식이 아닙니다.
- **권장 사항**: 올바른 데이터 형식을 사용합니다.

### <a name="error-code-df-executor-invalidcolumn"></a>오류 코드: DF-Executor-InvalidColumn
- **메시지**: 쿼리에 열 이름을 지정해야 합니다. SQL 함수를 사용하는 경우 별칭 설정
- **원인**: 열 이름이 지정되지 않았습니다.
- **권장 사항**: min() 또는 max()와 같은 SQL 함수를 사용하는 경우 별칭을 설정합니다.

### <a name="error-code-df-executor-drivererror"></a>오류 코드: DF-Executor-DriverError
- **메시지**: INT96은 ADF Dataflow에서 지원하지 않는 레거시 타임스탬프 형식입니다. 열 형식을 최신 형식으로 업그레이드하는 것이 좋습니다.
- **원인**: 드라이버 오류입니다.
- **권장 사항**: INT96은 Azure Data Factory 데이터 흐름에서 지원하지 않는 레거시 타임스탬프 형식입니다. 열 형식을 최신 형식으로 업그레이드하는 것이 좋습니다.

### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>오류 코드: DF-Executor-BlockCountExceedsLimitError
- **메시지**: 커밋되지 않은 블록 수는 최대 한도인 100,000개의 블록을 초과할 수 없습니다. Blob 구성을 확인하세요.
- **원인**: blob에서 커밋되지 않은 블록의 최대수는 100,000입니다.
- **권장 사항**:이 문제에 관한 자세한 내용은 Microsoft 제품 팀에 문의하세요.

### <a name="error-code-df-executor-partitiondirectoryerror"></a>오류 코드: DF-Executor-PartitionDirectoryError
- **메시지**: 지정된 원본 경로에 파티션된 여러 디렉터리(예: <Source Path>/<Partition Root Directory 1>/a=10/b=20, <Source Path>/<Partition Root Directory 2>/c=10/d=30) 또는 다른 파일이나 파티션되지 않은 디렉터리가 포함된 파티션된 디렉터리(예: <Source Path>/<Partition Root Directory 1>/a=10/b=20, <Source Path>/Directory 2/file1)가 있습니다. 원본 경로에서 파티션 루트 디렉터리를 제거하고 개별 원본 변환을 통해 읽습니다.
- **원인**: 원본 경로에 여러 개의 파티션된 디렉터리가 있거나 다른 파일 또는 파티션되지 않은 디렉터리가 있는 파티션된 디렉터리가 있습니다.
- **권장 사항**: 원본 경로에서 파티션된 루트 디렉터리를 제거하고 별도의 원본 변환을 통해 읽습니다.

### <a name="error-code-df-executor-invalidtype"></a>오류 코드: DF-Executor-InvalidType
- **메시지**: 매개 변수 형식이 전달된 값 형식과 일치하는지 확인합니다. 파이프라인에서 부동 매개 변수를 전달하는 기능은 현재 지원되지 않습니다.
- **원인**: 선언된 형식의 데이터 형식이 실제 매개 변수 값과 호환되지 않습니다.
- **권장 사항**: 데이터 흐름에 전달된 매개 변수 값이 선언된 형식과 일치하는지 확인합니다.

### <a name="error-code-df-executor-parseerror"></a>오류 코드: DF-Executor-ParseError
- **메시지**: 식을 구문 분석할 수 없습니다.
- **원인**: 잘못된 형식으로 인해 식에서 구문 분석 오류가 발생했습니다.
- **권장 사항**: 식의 형식을 확인합니다.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>오류 코드: DF-Executor-SystemImplicitCartesian
- **메시지**: INNER 조인에 대한 암시적 카테시안 제품은 지원되지 않습니다. 대신 CROSS 조인을 사용하세요. 조인에 사용된 열은 행에 대해 고유 키를 만들어야 합니다.
- **원인**: 논리 플랜 간 내부 조인의 암시적 카티전 제품은 지원되지 않습니다. 조인에 열을 사용하는 경우 고유 키를 만듭니다.
- **권장 사항**: 같지 않음 기반 조인의 경우 CROSS JOIN을 사용합니다.

### <a name="error-code-getcommand-outputasync-failed"></a>오류 코드: GetCommand OutputAsync 실패
- **메시지**: 데이터 흐름 디버그 및 데이터 미리 보기 중: GetCommand OutputAsync 실패 ...
- **원인**: 이 오류는 백 엔드 서비스 오류입니다. 
- **권장 사항**: 작업을 다시 시도하고 디버깅 세션을 다시 시작합니다. 다시 시도하고 다시 시작해도 문제가 해결되지 않으면 고객 지원에 문의하세요. 

### <a name="error-code-df-executor-outofmemoryerror"></a>오류 코드: DF-Executor-OutOfMemoryError
 
- **메시지**: 실행 중 클러스터의 메모리 부족 이슈가 발생했습니다. 코어 수가 더 큰 통합 런타임 및/또는 메모리 최적화 컴퓨팅 형식을 사용하여 다시 시도합니다.
- **원인**: 클러스터의 메모리가 부족합니다.
- **권장 사항**: 디버그 클러스터는 개발용입니다. 데이터 샘플링 및 적절한 컴퓨팅 형식과 크기를 사용하여 페이로드를 실행합니다. 성능 팁은 [매핑 데이터 흐름 성능 가이드](concepts-data-flow-performance.md)을 참조하세요.

### <a name="error-code-df-executor-illegalargument"></a>오류 코드: DF-Executor-illegalArgument

- **메시지**: 연결된 서비스의 액세스 키가 올바른지 확인합니다.
- **원인**: 계정 이름 또는 액세스 키가 잘못되었습니다.
- **권장 사항**: 연결된 서비스에 지정된 계정 이름 또는 액세스 키가 올바른지 확인합니다. 

### <a name="error-code-df-executor-columnunavailable"></a>오류 코드: DF-Executor-ColumnUnavailable
- **메시지**: 식에 사용된 열 이름을 사용할 수 없거나 잘못되었습니다.
- **원인**: 식에 잘못되었거나 사용할 수 없는 열 이름이 사용되었습니다.
- **권장 사항**: 식에 사용된 열 이름을 확인합니다.

 ### <a name="error-code-df-executor-outofdiskspaceerror"></a>오류 코드: DF-Executor-OutOfDiskSpaceError
- **메시지**: 내부 서버 오류
- **원인**: 클러스터의 디스크 공간이 부족합니다.
- **권장 사항**: 파이프라인을 다시 시도합니다. 그래도 문제가 해결되지 않으면 고객 지원에 문의하세요.


 ### <a name="error-code-df-executor-storeisnotdefined"></a>오류 코드: DF-Executor-StoreIsNotDefined
- **메시지**: 저장소 구성이 정의되어 있지 않습니다. 이 오류는 파이프라인의 잘못된 매개 변수 할당 때문에 발생할 수 있습니다.
- **원인**: 결정되지 않았습니다.
- **권장 사항**: 파이프라인에서 매개 변수 값 할당을 확인합니다. 매개 변수 식에 잘못된 문자가 포함되어 있을 수 있습니다.


### <a name="error-code-4502"></a>오류 코드: 4502
- **메시지**: Integration Runtime의 제한으로 인해 실패를 초래하는 동시 MappingDataflow 실행이 상당히 있습니다.
- **원인**: 많은 수의 데이터 흐름 작업 실행이 통합 런타임에서 동시에 발생합니다. 자세한 내용은 [Azure Data Factory 한도](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits)를 참조하세요.
- **권장 사항**: 추가 데이터 흐름 작업을 병렬로 실행하려면 여러 통합 런타임에 배포합니다.


### <a name="error-code-invalidtemplate"></a>오류 코드: InvalidTemplate
- **메시지**: 파이프라인 식을 평가할 수 없습니다.
- **원인**: 구문 오류 때문에 데이터 흐름 작업에 전달된 파이프라인 식이 올바르게 처리되지 않습니다.
- **권장 사항**: 작업 모니터링에서 작업을 확인하여 식을 확인합니다.

### <a name="error-code-2011"></a>오류 코드: 2011
- **메시지**: 작업이 Azure Integration Runtime에서 실행 중이었고 자체 호스팅 통합 런타임을 통해 연결된 데이터 저장소 또는 컴퓨팅의 자격 증명을 암호 해독하지 못했습니다. 이 작업과 연관된 연결된 서비스의 구성을 확인하고 적절한 통합 런타임 형식을 사용하는지 확인합니다.
- **원인**: 데이터 흐름은 자체 호스팅 통합 런타임에서 연결된 서비스를 지원하지 않습니다.
- **권장 사항**: 관리되는 Virtual Network 통합 런타임에서 실행되도록 데이터 흐름을 구성합니다.

### <a name="error-code-df-xml-invalidvalidationmode"></a>오류 코드: DF-Xml-InvalidValidationMode
- **메시지**: 잘못된 xml 유효성 검사 모드가 제공되었습니다.
- **권장 사항**: 매개 변수 값을 확인하고 올바른 유효성 검사 모드를 지정합니다.

### <a name="error-code-df-xml-invaliddatafield"></a>오류 코드: DF-Xml-InvalidDataField
- **메시지**: 손상된 레코드의 필드는 문자열 형식이어야 하며 null을 허용해야 합니다.
- **권장 사항**: 원본 프로젝트의 `\"_corrupt_record\"` 열에 문자열 데이터 형식이 있는지 확인합니다.

### <a name="error-code-df-xml-malformedfile"></a>오류 코드: DF-Xml-MalformedFile
- **메시지**: ‘FailFastMode’의 xml 형식이 잘못되었습니다.
- **권장 사항**: XML 파일의 콘텐츠를 올바른 형식으로 업데이트합니다.

### <a name="error-code-df-xml-invaliddatatype"></a>오류 코드: DF-Xml-InvalidDataType
- **Message**: XML 요소에 하위 요소 또는 특성이 있기 때문에 이 요소를 변환할 수 없습니다.

### <a name="error-code-df-xml-invalidreferenceresource"></a>오류 코드: DF-Xml-InvalidReferenceResource
- **메시지**: xml 데이터 파일의 참조 리소스를 확인할 수 없습니다.
- **권장 사항**: XML 데이터 파일에서 참조 리소스를 확인해야 합니다.

### <a name="error-code-df-xml-invalidschema"></a>오류 코드: DF-Xml-InvalidSchema
- **메시지**: 스키마 유효성 검사에 실패했습니다.

### <a name="error-code-df-xml-unsupportedexternalreferenceresource"></a>오류 코드: DF-Xml-UnsupportedExternalReferenceResource
- **메시지**: xml 데이터 파일의 외부 참조 리소스는 지원되지 않습니다.
- **권장 사항**: 현재 외부 참조 리소스가 지원되지 않는 경우 XML 파일 콘텐츠를 업데이트합니다.

### <a name="error-code-df-gen2-invalidaccountconfiguration"></a>오류 코드: DF-GEN2-InvalidAccountConfiguration
- **Message**: 계정 키, tenant/spnId/spnCredential/spnCredentialType 또는 miServiceUri/miServiceToken 중 하나를 지정해야 합니다.
- **권장 사항**: 관련 GEN2 연결된 서비스에서 올바른 계정을 구성합니다.

### <a name="error-code-df-gen2-invalidauthconfiguration"></a>오류 코드: DF-GEN2-InvalidAuthConfiguration
- **메시지**: 세 가지 인증 메서드(Key, ServicePrincipal 및 MI) 중 하나만 지정할 수 있습니다. 
- **권장 사항**: 관련 GEN2 연결된 서비스에서 올바른 인증 형식을 선택합니다.

### <a name="error-code-df-gen2-invalidserviceprincipalcredentialtype"></a>오류 코드: DF-GEN2-InvalidServicePrincipalCredentialType
- **메시지**: ServicePrincipalCredentialType이 잘못되었습니다.

### <a name="error-code-df-gen2-invaliddatatype"></a>오류 코드: DF-GEN2-InvalidDataType
- **메시지**: 클라우드 형식이 잘못되었습니다.

### <a name="error-code-df-blob-invalidaccountconfiguration"></a>오류 코드: DF-Blob-InvalidAccountConfiguration
- **메시지**: 계정 키 또는 sas_token 중 하나를 지정해야 합니다.

### <a name="error-code-df-blob-invalidauthconfiguration"></a>오류 코드: DF-Blob-InvalidAuthConfiguration
- **메시지**: 두 가지 인증 방법(키, SAS) 중 하나만 지정할 수 있습니다.

### <a name="error-code-df-blob-invaliddatatype"></a>오류 코드: DF-Blob-InvalidDataType
- **메시지**: 클라우드 형식이 잘못되었습니다.

### <a name="error-code-df-cosmos-partitionkeymissed"></a>오류 코드: DF-Cosmos-PartitionKeyMissed
- **메시지**: 업데이트 및 삭제 작업의 파티션 키 경로를 지정해야 합니다.
- **권장 사항**: Cosmos 싱크 설정에서 제공하는 파티션 키를 사용합니다.

### <a name="error-code-df-cosmos-invalidpartitionkey"></a>오류 코드: DF-Cosmos-InvalidPartitionKey
- **메시지**: 업데이트 및 삭제 작업의 파티션 키 경로는 비워둘 수 없습니다.
- **권장 사항**: Cosmos 싱크 설정에서 제공하는 파티션 키를 사용합니다.

### <a name="error-code-df-cosmos-idpropertymissed"></a>오류 코드: DF-Cosmos-IdPropertyMissed
- **메시지**: 삭제 및 업데이트 작업의 ‘id’ 속성을 매핑해야 합니다.
- **권장 사항**: Cosmos 싱크 설정에서 입력 열에 `id` 열이 있는지 확인합니다. 그렇지 않으면 **선택 또는 파생 변환** 을 사용하여 싱크 전에 이 열을 생성합니다.

### <a name="error-code-df-cosmos-invalidpartitionkeycontent"></a>오류 코드: DF-Cosmos-InvalidPartitionKeyContent
- **메시지**: 파티션 키는 /로 시작해야 합니다.
- **권장 사항**: Cosmos 싱크 설정에서 파티션 키가 `/`로 시작되는지 확인합니다(예: `/movieId`).

### <a name="error-code-df-cosmos-invalidpartitionkey"></a>오류 코드: DF-Cosmos-InvalidPartitionKey
- **메시지**: 삭제 및 업데이트 작업용 싱크에 partitionKey가 매핑되지 않았습니다.
- **권장 사항**: Cosmos 싱크 설정에서 컨테이너의 파티션 키와 같은 파티션 키를 사용합니다.

### <a name="error-code-df-cosmos-invalidconnectionmode"></a>오류 코드: DF-Cosmos-InvalidConnectionMode
- **메시지**: connectionMode가 잘못되었습니다.
- **권장 사항**: Cosmos 설정에서 지원되는 모드가 **Gateway** 와 **DirectHttps** 인지 확인합니다.

### <a name="error-code-df-cosmos-invalidaccountconfiguration"></a>오류 코드: DF-Cosmos-InvalidAccountConfiguration
- **메시지**: accountName이나 accountEndpoint를 지정해야 합니다.

### <a name="error-code-df-github-writenotsupported"></a>오류 코드: DF-Github-WriteNotSupported
- **메시지**: Github 저장소에서는 쓰기를 허용하지 않습니다.

### <a name="error-code-df-pgsql-invalidcredential"></a>오류 코드: DF-PGSQL-InvalidCredential
- **메시지**: 사용자/암호를 지정해야 합니다.
- **권장 사항**: 관련 postgresql 연결된 서비스에 올바른 자격 증명 설정이 있는지 확인합니다.

### <a name="error-code-df-snowflake-invalidstageconfiguration"></a>오류 코드: DF-Snowflake-InvalidStageConfiguration
- **메시지**: blob 스토리지 형식만 snowflake 읽기/쓰기 작업의 스테이지로 사용할 수 있습니다.

### <a name="error-code-df-snowflake-invalidstageconfiguration"></a>오류 코드: DF-Snowflake-InvalidStageConfiguration
- **메시지**: Azure blob + sas 인증을 사용하여 snowflake 스테이지 속성을 지정해야 합니다.

### <a name="error-code-df-snowflake-invaliddatatype"></a>오류 코드: DF-Snowflake-InvalidDataType
- **메시지**: spark 형식은 snowflake에서 지원되지 않습니다.
- **권장 사항**: **파생 변환** 을 사용하여 snowflake 싱크 전에 입력 데이터의 관련 열을 문자열 형식으로 변경합니다. 

### <a name="error-code-df-hive-invalidblobstagingconfiguration"></a>오류 코드: DF-Hive-InvalidBlobStagingConfiguration
- **메시지**: Blob Storage 준비 속성을 지정해야 합니다.

### <a name="error-code-df-hive-invalidgen2stagingconfiguration"></a>오류 코드: DF-Hive-InvalidGen2StagingConfiguration
- **메시지**: ADLS Gen2 스토리지 준비에서는 서비스 주체 키 자격 증명만 지원합니다.
- **권장 사항**: 준비로 사용되는 ADLS Gen2 연결된 서비스에서 서비스 주체 키 자격 증명을 적용하는지 확인합니다.

### <a name="error-code-df-hive-invalidgen2stagingconfiguration"></a>오류 코드: DF-Hive-InvalidGen2StagingConfiguration
- **메시지**: ADLS Gen2 스토리지 준비 속성을 지정해야 합니다. 키, tenant/spnId/spnKey 또는 miServiceUri/miServiceToken 중 하나가 필요합니다.
- **권장 사항**: 관련 ADLS Gen2 연결된 서비스의 하이브에서 준비로 사용되는 올바른 자격 증명을 적용합니다. 

### <a name="error-code-df-hive-invaliddatatype"></a>오류 코드: DF-Hive-InvalidDataType
- **메시지**: 지원되지 않는 열입니다.
- **권장 사항**: 하이브에서 지원되는 데이터 형식과 일치하도록 입력 데이터의 열을 업데이트합니다.

### <a name="error-code-df-hive-invalidstoragetype"></a>오류 코드: DF-Hive-InvalidStorageType
- **메시지**: 스토리지 형식은 blob 또는 gen2일 수 있습니다.

### <a name="error-code-df-delimited-invalidconfiguration"></a>오류 코드: DF-Delimited-InvalidConfiguration
- **메시지**: 빈 줄 또는 사용자 지정 헤더 중 하나를 지정해야 합니다.
- **권장 사항**: CSV 설정에서 빈 줄 또는 사용자 지정 헤더를 지정합니다.

### <a name="error-code-df-delimited-columndelimitermissed"></a>오류 코드: DF-Delimited-ColumnDelimiterMissed
- **메시지**: 구문 분석에는 열 구분 기호가 필요합니다.
- **권장 사항**: CSV 설정에 열 구분 기호가 있는지 확인합니다.

### <a name="error-code-df-mssql-invalidcredential"></a>오류 코드: DF-MSSQL-InvalidCredential
- **메시지**: user/pwd, tenant/spnId/spnKey 또는 miServiceUri/miServiceToken 중 하나를 지정해야 합니다.
- **권장 사항**: 관련된 MSSQL 연결된 서비스에 올바른 자격 증명을 적용합니다.

### <a name="error-code-df-mssql-invaliddatatype"></a>오류 코드: DF-MSSQL-InvalidDataType
- **메시지**: 지원되지 않는 필드입니다.
- **권장 사항**: MSSQL에서 지원하는 데이터 형식과 일치하도록 입력 데이터 열을 수정합니다.

### <a name="error-code-df-mssql-invalidauthconfiguration"></a>오류 코드: DF-MSSQL-InvalidAuthConfiguration
- **메시지**: 세 가지 인증 메서드(Key, ServicePrincipal 및 MI) 중 하나만 지정할 수 있습니다.
- **권장 사항**: 관련 MSSQL 연결된 서비스에서 세 가지 인증 방법(키, serviceprincipal 및 MI) 중 하나만 지정할 수 있습니다.

### <a name="error-code-df-mssql-invalidcloudtype"></a>오류 코드: DF-MSSQL-InvalidCloudType
- **메시지**: 클라우드 형식이 잘못되었습니다.
- **권장 사항**: 관련된 MSSQL 연결된 서비스에서 클라우드 형식을 확인합니다.

### <a name="error-code-df-sqldw-invalidblobstagingconfiguration"></a>오류 코드: DF-SQLDW-InvalidBlobStagingConfiguration
- **메시지**: Blob Storage 준비 속성을 지정해야 합니다.

### <a name="error-code-df-sqldw-invalidstoragetype"></a>오류 코드: DF-SQLDW-InvalidStorageType
- **메시지**: 스토리지 형식은 blob 또는 gen2일 수 있습니다.

### <a name="error-code-df-sqldw-invalidgen2stagingconfiguration"></a>오류 코드: DF-SQLDW-InvalidGen2StagingConfiguration
- **메시지**: ADLS Gen2 스토리지 준비에서는 서비스 주체 키 자격 증명만 지원합니다.

### <a name="error-code-df-sqldw-invalidconfiguration"></a>오류 코드: DF-SQLDW-InvalidConfiguration
- **메시지**: ADLS Gen2 스토리지 준비 속성을 지정해야 합니다. 키, tenant/spnId/spnCredential/spnCredentialType 또는 miServiceUri/miServiceToken 중 하나가 필요합니다.

### <a name="error-code-df-delta-invalidconfiguration"></a>오류 코드: DF-DELTA-InvalidConfiguration
- **메시지**: 타임스탬프와 버전은 동시에 설정할 수 없습니다.

### <a name="error-code-df-delta-keycolumnmissed"></a>오류 코드: DF-DELTA-KeyColumnMissed
- **메시지**: 삽입 불가능 작업의 키 열을 지정해야 합니다.

### <a name="error-code-df-delta-invalidtableoperationsettings"></a>오류 코드: DF-DELTA-InvalidTableOperationSettings
- **메시지**: 다시 만들기 및 자르기 옵션을 둘 다 지정할 수는 없습니다.

### <a name="error-code-df-excel-worksheetconfigmissed"></a>오류 코드: DF-Excel-WorksheetConfigMissed
- **메시지**: Excel 시트 이름이나 인덱스가 필요합니다.
- **권장 사항**: 매개 변수 값을 확인하고 excel 데이터를 읽을 시트 이름 또는 인덱스를 지정합니다.

### <a name="error-code-df-excel-invalidworksheetconfiguration"></a>오류 코드: DF-Excel-InvalidWorksheetConfiguration
- **메시지**: Excel 시트 이름과 인덱스는 동시에 존재할 수 없습니다.
- **권장 사항**: 매개 변수 값을 확인하고 excel 데이터를 읽을 시트 이름 또는 인덱스를 지정합니다.

### <a name="error-code-df-excel-invalidrange"></a>오류 코드: DF-Excel-InvalidRange
- **메시지**: 잘못된 범위를 제공했습니다.
- **권장 사항**: 매개 변수 값을 확인하고 올바른 범위를 [Azure Data Factory-Dataset 속성의 Excel 형식](./format-excel.md#dataset-properties) 참조로 지정합니다.

### <a name="error-code-df-excel-worksheetnotexist"></a>오류 코드: DF-Excel-WorksheetNotExist
- **메시지**: Excel 워크시트가 없습니다.
- **권장 사항**: 매개 변수 값을 확인하고 excel 데이터를 읽을 올바른 시트 이름이나 인덱스를 지정합니다.

### <a name="error-code-df-excel-differentschemanotsupport"></a>오류 코드: DF-Excel-DifferentSchemaNotSupport
- **메시지**: 스키마가 다른 읽기 Excel 파일은 현재 지원되지 않습니다.

### <a name="error-code-df-excel-invaliddatatype"></a>오류 코드: DF-Excel-InvalidDataType
- **메시지**: 데이터 형식이 지원되지 않습니다.

### <a name="error-code-df-excel-invalidfile"></a>오류 코드: DF-Excel-InvalidFile
- **메시지**: .xlsx와 .xls만 지원되는 중에 잘못된 excel 파일이 제공되었습니다.


## <a name="miscellaneous-troubleshooting-tips"></a>기타 문제 해결 팁
- **이슈**: 예기치 않은 예외가 발생하여 실행에 실패했습니다.
    - **메시지**: 데이터 흐름 활동 실행 중: 예기치 않은 예외로 실행에 실패했습니다.
    - **원인**: 이 오류는 백 엔드 서비스 오류입니다. 작업을 다시 시도하고 디버깅 세션을 다시 시작합니다.
    - **권장 사항**: 다시 시도하고 다시 시작해도 문제가 해결되지 않으면 고객 지원에 문의하세요.

-  **이슈**: 디버그 데이터 미리 보기 중에 조인에 관한 출력 데이터가 없습니다.
    - **메시지**: 샘플링된 행이 너무 적기 때문에 발생할 수 있는 null 값이나 누락된 값이 많이 있습니다. 디버그 행 한도를 업데이트하고 데이터를 새로 고쳐 보세요.
    - **원인**: 조인 조건이 행과 일치하지 않았거나 데이터 미리 보기 중에 많은 수의 null 값이 발생했습니다.
    - **권장 사항**: **디버그 설정** 에서 원본 행 한도의 행 수를 늘립니다. 추가 데이터를 처리할 수 있을 만큼 큰 데이터 흐름 클러스터가 있는 Azure IR을 선택해야 합니다.
    
- **이슈**: 여러 줄로 된 CSV 파일이 있는 원본에서 유효성 검사 오류가 발생했습니다. 
    - **메시지**: 다음 오류 메시지 중 하나가 표시될 수 있습니다.
        - 마지막 열이 null이거나 누락되었습니다.
        - 원본에서 스키마 유효성 검사가 실패합니다.
        - 스키마 가져오기는 UX에 올바르게 표시되지 않으며 마지막 열의 이름에는 줄 바꿈 문자가 있습니다.
    - **원인**: 매핑 데이터 흐름에서 \r\n을 행 구분 기호로 사용하면 여러 줄 CSV 원본 파일이 현재 작동하지 않습니다. 캐리지 리턴 시 추가 줄이 있으면 오류가 발생할 수 있습니다. 
    - **권장 사항**: \r\n이 아닌 \n을 행 구분 기호로 사용하여 원본에서 파일을 생성합니다. 또는 복사 작업을 사용하여 행 구분 기호로 \n을 사용하도록 CSV 파일을 변환합니다.

## <a name="general-troubleshooting-guidance"></a>일반 문제 해결 지침
1. 데이터 세트 연결의 상태를 확인합니다. 각 원본 및 싱크 변환에서 사용 중인 데이터 세트마다 연결된 서비스로 이동하여 연결을 테스트합니다.
2. 데이터 흐름 디자이너에서 파일과 테이블의 연결 상태를 확인합니다. 디버그 모드로 원본 변환에서 **데이터 미리 보기** 를 선택하여 데이터에 액세스할 수 있는지 확인합니다.
3. 데이터 미리 보기에서 모든 항목이 올바르면 파이프라인 디자이너로 이동하여 파이프라인 활동에 데이터 흐름을 배치합니다. 엔드투엔드 테스트에 대해 파이프라인을 디버깅합니다.

### <a name="improvement-on-csvcdm-format-in-data-flow"></a>데이터 흐름의 CSV/CDM 형식 개선 

**Azure Data Factory V2에서 매핑 데이터 흐름의 구분된 텍스트 또는 CDM 형식** 을 사용하는 경우 **2021년 5월 1일** 부터 데이터 흐름에서 구분된 텍스트/CDM의 개선 때문에 기존 파이프라인의 동작이 변경될 수 있습니다. 

개선 전에 다음과 같은 이슈가 발생할 수 있지만, 개선 후 이슈가 수정되었습니다. 이 개선 사항이 영향을 미치는지 확인하려면 다음 콘텐츠를 읽어 보세요. 

#### <a name="scenario-1-encounter-the-unexpected-row-delimiter-issue"></a>시나리오 1: 예기치 않은 행 구분 기호 이슈 발생

 다음 조건에 해당하는 경우 영향을 받습니다.
 - 여러 줄 설정이 True 또는 CDM으로 설정된 구분된 텍스트를 원본으로 사용합니다.
 - 첫 번째 행에 128자가 넘게 있습니다. 
 - 데이터 파일의 행 구분 기호가 `\n`이 아닙니다.

 개선 전에 기본 행 구분 기호 `\n`은 구분된 텍스트 파일을 구문 분석하는 데 예기치 않게 사용될 수 있습니다. 여러 줄 설정이 True로 설정되면 행 구분 기호 설정이 무효화되고 행 구분 기호가 처음 128자를 기준으로 자동 감지되기 ​​때문입니다. 실제 행 구분 기호를 검색하지 못하면 다시 `\n`으로 대체됩니다.  

 개선 후에 세 개의 행 구분 기호, 즉 `\r`, `\n`, `\r\n` 중 하나가 작동해야 합니다.
 
 다음 예제는 개선 후 파이프라인의 동작 변경 내용을 보여 줍니다.

 **예제**:<br/>
   다음 열의 경우:<br/>
    `C1, C2, {long first row}, C128\r\n `<br/>
    `V1, V2, {values………………….}, V128\r\n `<br/>
 
   개선 전에 `\r`은 열 값에 저장됩니다. 구문 분석된 열 결과는 다음과 같습니다.<br/>
   `C1 C2 {long first row} C128`**`\r`**<br/>
   `V1 V2 {values………………….} V128`**`\r`**<br/> 

   개선 후 구문 분석된 열 결과는 다음과 같아야 합니다.<br/>
   `C1 C2 {long first row} C128`<br/>
   `V1 V2 {values………………….} V128`<br/>
  
#### <a name="scenario-2-encounter-an-issue-of-incorrectly-reading-column-values-containing-rn"></a>시나리오 2: ‘\r\n’이 포함된 열 값을 잘못 읽는 이슈 발생

 다음 조건에 해당하는 경우 영향을 받습니다.
 - 여러 줄 설정이 True 또는 CDM으로 설정된 구분된 텍스트를 원본으로 사용합니다. 
 - 행 구분 기호는 `\r\n`입니다.

 개선 전에 열 값을 읽을 때, 열 값의 `\r\n`은 `\n`으로 잘못 바뀔 수 있습니다. 

 개선 후 열 값의 `\r\n`은 `\n`으로 바뀌지 않습니다.

 다음 예제는 개선 후 파이프라인의 동작 변경 내용을 보여 줍니다.
 
 **예제**:<br/>
  
 다음 열의 경우:<br/>
  **`"A\r\n"`**`, B, C\r\n`<br/>

 개선 전에 구문 분석된 열 결과는 다음과 같습니다.<br/>
  **`A\n`**` B C`<br/>

 개선 후 구문 분석된 열 결과는 다음과 같아야 합니다.<br/>
  **`A\r\n`**` B C`<br/>  

#### <a name="scenario-3-encounter-an-issue-of-incorrectly-writing-column-values-containing-n"></a>시나리오 3: ‘\n’이 포함된 열 값을 잘못 쓰는 이슈 발생

 다음 조건에 해당하는 경우 영향을 받습니다.
 - 구분된 텍스트를 싱크로 사용합니다.
 - 열 값에 `\n`이 포함되어 있습니다.
 - 행 구분 기호가 `\r\n`로 설정되었습니다.
 
 개선 전에 열 값을 쓸 때, 열 값의 `\n`은 `\r\n`으로 잘못 바뀔 수 있습니다. 

 개선 후 열 값의 `\n`은 `\r\n`으로 바뀌지 않습니다.
 
 다음 예제는 개선 후 파이프라인의 동작 변경 내용을 보여 줍니다.

 **예제**:<br/>

 다음 열의 경우:<br/>
 **`A\n`**` B C`<br/>

 개선 전, CSV 싱크는 다음과 같습니다.<br/>
  **`"A\r\n"`**`, B, C\r\n` <br/>

 개선 후, CSV 싱크는 다음과 같아야 합니다.<br/>
  **`"A\n"`**`, B, C\r\n`<br/>

#### <a name="scenario-4-encounter-an-issue-of-incorrectly-reading-empty-string-as-null"></a>시나리오 4: 빈 문자열을 NULL로 잘못 읽을 때 이슈 발생
 
 다음 조건에 해당하는 경우 영향을 받습니다.
 - 구분된 텍스트를 원본으로 사용합니다. 
 - NULL 값은 비어 있지 않은 값으로 설정됩니다. 
 - 열 값이 빈 문자열이며 따옴표가 없습니다. 
 
 개선 전에, 따옴표가 없는 빈 문자열의 열 값은 NULL로 읽습니다. 

 개선 후, 빈 문자열은 NULL 값으로 구문 분석되지 않습니다. 
 
 다음 예제는 개선 후 파이프라인의 동작 변경 내용을 보여 줍니다.

 **예제**:<br/>

 다음 열의 경우:<br/>
  `A, ,B, `<br/>

 개선 전에 구문 분석된 열 결과는 다음과 같습니다.<br/>
  `A null B null`<br/>

 개선 후 구문 분석된 열 결과는 다음과 같아야 합니다.<br/>
  `A "" (empty string) B "" (empty string)`<br/>


## <a name="next-steps"></a>다음 단계

문제 해결에 관한 자세한 내용은 다음 리소스를 참조하세요.

*  [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 기능 요청](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
