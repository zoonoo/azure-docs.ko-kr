---
title: Common Data Model 형식
description: Common Data Model metadata system을 사용 하 여 데이터 변환
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: daperlov
ms.openlocfilehash: 5e75f2203552a69e50ed16176525429c6c9d8810
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84807811"
---
# <a name="common-data-model-format-in-azure-data-factory"></a>Azure Data Factory의 공통 데이터 모델 형식
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

CDM (Common Data Model) 메타 데이터 시스템을 사용 하면 데이터 및 해당 의미가 응용 프로그램 및 비즈니스 프로세스에서 쉽게 공유 될 수 있습니다. 자세히 알아보려면 [Common Data Model](https://docs.microsoft.com/common-data-model/) 개요를 참조 하세요.

Azure Data Factory 사용자는 매핑 데이터 흐름을 사용 하 여 ADLS Gen2 ( [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) )에 저장 된 CDM 엔터티 간에 변환할 수 있습니다.

> [!NOTE]
> ADF 데이터 흐름에 대 한 CDM (Common Data Model) 형식 커넥터는 현재 공개 미리 보기로 제공 됩니다.

## <a name="mapping-data-flow-properties"></a>매핑 데이터 흐름 속성

공통 데이터 모델은 데이터 흐름을 원본 및 싱크로 매핑하는 [인라인 데이터 집합](data-flow-source.md#inline-datasets) 으로 사용할 수 있습니다.

### <a name="source-properties"></a>원본 속성

아래 표에서는 CDM 원본에서 지 원하는 속성을 나열 합니다. 이러한 속성은 **원본 옵션** 탭에서 편집할 수 있습니다.

| 이름 | 설명 | 필요한 공간 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 서식 | 형식은 이어야 합니다.`cdm` | 예 | `cdm` | format |
| 메타 데이터 형식 | 데이터에 대 한 엔터티 참조가 있는 위치입니다. CDM 버전 1.0을 사용 하는 경우 매니페스트를 선택 합니다. 1.0 이전에 CDM 버전을 사용 하는 경우 model.json을 선택 합니다. | 예 | `'manifest'` 또는 `'model'` | manifestType |
| 루트 위치: 컨테이너 | CDM 폴더의 컨테이너 이름입니다. | 예 | String | fileSystem |
| 루트 위치: 폴더 경로 | CDM 폴더의 루트 폴더 위치 | 예 | String | folderPath |
| 매니페스트 파일: 엔터티 경로 | 루트 폴더에 있는 엔터티의 폴더 경로 | no | String | entityPath |
| 매니페스트 파일: 매니페스트 이름 | 매니페스트 파일의 이름입니다. 기본값은 ' 기본값 '입니다.  | 예 | String | manifestName |
| 마지막으로 수정한 사람 필터링 | 마지막으로 변경 된 시간에 따라 파일을 필터링 하도록 선택 | no | 타임스탬프 | modifiedAfter <br> modifiedBefore | 
| 스키마 연결 된 서비스 | 모음가 있는 연결 된 서비스입니다. | 예, 매니페스트를 사용 하는 경우 | `'adlsgen2'` 또는 `'github'` | corpusStore | 
| 엔터티 참조 컨테이너 | 컨테이너 모음는에 있습니다. | 예 (매니페스트 및 모음를 사용 하는 경우 ADLS Gen2 | String | adlsgen2_fileSystem |
| 엔터티 참조 리포지토리 | GitHub 리포지토리 이름 | 예, GitHub에서 매니페스트 및 모음를 사용 하는 경우 | String | github_repository |
| 엔터티 참조 분기 | GitHub 리포지토리 분기 | 예, GitHub에서 매니페스트 및 모음를 사용 하는 경우 | String |  github_branch |
| 모음 폴더 | 모음의 루트 위치 | 예, 매니페스트를 사용 하는 경우 | String | corpusPath |
| 모음 엔터티 | 엔터티 참조 경로 | 예 | String | 엔터티 |
| 파일을 찾을 수 없음 | True 이면 파일이 없는 경우 오류가 throw 되지 않습니다. | no | `true` 또는 `false` | ignoreNoFilesFound |

#### <a name="import-schema"></a>스키마 가져오기

CDM는 인라인 데이터 집합 으로만 사용할 수 있으며 기본적으로 연결 된 스키마가 없습니다. 열 메타 데이터를 가져오려면 **프로젝션** 탭에서 **스키마 가져오기** 단추를 클릭 합니다. 이렇게 하면 모음에서 지정한 열 이름과 데이터 형식을 참조할 수 있습니다. 스키마를 가져오려면 [데이터 흐름 디버그 세션이](concepts-data-flow-debug-mode.md) 활성 상태 여야 합니다.


### <a name="cdm-source-data-flow-script-example"></a>CDM 원본 데이터 흐름 스크립트 예제

```
source(output(
        ProductSizeId as integer,
        ProductColor as integer,
        CustomerId as string,
        Note as string,
        LastModifiedDate as timestamp
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    manifestType: 'manifest',
    manifestName: 'ProductManifest',
    entityPath: 'Product',
    corpusPath: 'Products',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    folderPath: 'ProductData',
    fileSystem: 'data') ~> CDMSource
```

### <a name="sink-properties"></a>싱크 속성

아래 표에서는 CDM 싱크에서 지 원하는 속성을 나열 합니다. 이러한 속성은 **설정** 탭에서 편집할 수 있습니다.

| 이름 | 설명 | 필요한 공간 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 서식 | 형식은 이어야 합니다.`cdm` | 예 | `cdm` | format |
| 루트 위치: 컨테이너 | CDM 폴더의 컨테이너 이름입니다. | 예 | String | fileSystem |
| 루트 위치: 폴더 경로 | CDM 폴더의 루트 폴더 위치 | 예 | String | folderPath |
| 매니페스트 파일: 엔터티 경로 | 루트 폴더에 있는 엔터티의 폴더 경로 | no | String | entityPath |
| 매니페스트 파일: 매니페스트 이름 | 매니페스트 파일의 이름입니다. 기본값은 ' 기본값 '입니다. | 예 | String | manifestName |
| 스키마 연결 된 서비스 | 모음가 있는 연결 된 서비스입니다. | 예 | `'adlsgen2'` 또는 `'github'` | corpusStore | 
| 엔터티 참조 컨테이너 | 컨테이너 모음는에 있습니다. | 예, 모음의 경우에는 ADLS Gen2 | String | adlsgen2_fileSystem |
| 엔터티 참조 리포지토리 | GitHub 리포지토리 이름 | 예 (GitHub의 모음 인 경우) | String | github_repository |
| 엔터티 참조 분기 | GitHub 리포지토리 분기 | 예 (GitHub의 모음 인 경우) | String |  github_branch |
| 모음 폴더 | 모음의 루트 위치 | 예 | String | corpusPath |
| 모음 엔터티 | 엔터티 참조 경로 | 예 | String | 엔터티 |
| 파티션 경로 | 파티션이 기록 될 위치 | no | String | 파티션 경로 |
| 폴더 지우기 | 쓰기 전에 대상 폴더를 지운 경우 | no | `true` 또는 `false` | truncate |
| 형식 유형 | Parquet 형식을 지정 하려면 선택 합니다. | no | `parquet`지정 된 경우 | subformat |
| 열 구분 기호 | DelimitedText에 쓰는 경우 열을 구분 하는 방법 | 예 (DelimitedText에 쓰는 경우) | String | columnDelimiter |
| 첫 번째 행을 머리글로 | DelimitedText를 사용 하는 경우 열 이름이 헤더로 추가 되는지 여부 | no | `true` 또는 `false` | columnNamesAsHeader |

### <a name="cdm-sink-data-flow-script-example"></a>CDM sink 데이터 흐름 스크립트 예제

연결 된 데이터 흐름 스크립트는 다음과 같습니다.

```
CDMSource sink(allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    entityPath: 'ProductSize',
    manifestName: 'ProductSizeManifest',
    corpusPath: 'Products',
    partitionPath: 'adf',
    folderPath: 'ProductSizeData',
    fileSystem: 'cdm',
    subformat: 'parquet',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CDMSink

```

## <a name="next-steps"></a>다음 단계

매핑 데이터 흐름에서 [원본 변환을](data-flow-source.md) 만듭니다.
