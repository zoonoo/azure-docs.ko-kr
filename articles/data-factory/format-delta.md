---
title: Azure Data Factory의 델타 형식
description: 델타 형식을 사용하여 델타 레이크에서 데이터를 변환하고 이동합니다.
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: makromer
ms.openlocfilehash: 38138be8ddd6babd9740a81ffcbedb9088a412f4
ms.sourcegitcommit: b4032c9266effb0bf7eb87379f011c36d7340c2d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107906173"
---
# <a name="delta-format-in-azure-data-factory"></a>Azure Data Factory의 델타 형식

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 델타 형식을 사용하여 [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) 또는 [Azure Blob Storage](connector-azure-blob-storage.md)에 저장된 델타 레이크에서 데이터를 복사하는 방법을 강조합니다. 이 커넥터는 원본 및 싱크인 매핑 데이터 흐름에서 [인라인 데이터 세트](data-flow-source.md#inline-datasets)로 사용할 수 있습니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4ALTs]

## <a name="mapping-data-flow-properties"></a>매핑 데이터 흐름 속성

이 커넥터는 원본 및 싱크인 매핑 데이터 흐름에서 [인라인 데이터 세트](data-flow-source.md#inline-datasets)로 사용할 수 있습니다.

### <a name="source-properties"></a>원본 속성

아래 표에는 델타 원본에서 지원하는 속성이 나와 있습니다. 이러한 속성은 **원본 옵션** 탭에서 편집할 수 있습니다.

| Name | Description | 필수 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 서식 | 형식은 `delta`여야 합니다. | 예 | `delta` | format |
| 파일 시스템 | 델타 레이크의 컨테이너/파일 시스템 | 예 | String | fileSystem |
| 폴더 경로 | 델타 레이크의 직접 경로 | 예 | String | folderPath |
| 압축 유형 | 델타 테이블의 압축 유형 | 아니요 | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | compressionType |
| 압축 수준 | 압축이 가능한 빨리 완료되는지 또는 결과 파일이 최적으로 압축되어야 하는지 여부를 선택합니다. | `compressedType`이 지정되면 필수입니다. | `Optimal` 또는 `Fastest` | compressionLevel |
| 시간 이동 | 델타 테이블의 이전 스냅샷을 쿼리하는지 여부를 선택합니다. | 아니요 | 타임스탬프별 쿼리: 타임스탬프 <br> 버전별 쿼리: 정수 | timestampAsOf <br> versionAsOf |
| 파일을 찾을 수 없음 허용 | true이면 파일이 없는 경우 오류가 throw되지 않습니다. | 아니요 | `true` 또는 `false` | ignoreNoFilesFound |

#### <a name="import-schema"></a>스키마 가져오기

델타는 인라인 데이터 세트로만 사용할 수 있으며, 기본적으로 연결된 스키마가 없습니다. 열 메타데이터를 가져오려면 **프로젝션** 탭에서 **스키마 가져오기** 단추를 클릭합니다. 이렇게 하면 모음에서 지정한 열 이름과 데이터 형식을 참조할 수 있습니다. 스키마를 가져오려면 [데이터 흐름 디버그 세션](concepts-data-flow-debug-mode.md)이 활성 상태여야 하고 가리킬 기존 CDM 엔터티 정의 파일이 있어야 합니다.
 

### <a name="delta-source-script-example"></a>델타 원본 스크립트 예제

```
source(output(movieId as integer,
            title as string,
            releaseDate as date,
            rated as boolean,
            screenedOn as timestamp,
            ticketPrice as decimal(10,2)
            ),
    store: 'local',
    format: 'delta',
    versionAsOf: 0,
    allowSchemaDrift: false,
    folderPath: $tempPath + '/delta'
  ) ~> movies
```

### <a name="sink-properties"></a>싱크 속성

다음 표에는 델타 싱크에서 지원하는 속성이 나와 있습니다. 이러한 속성은 **설정** 탭에서 편집할 수 있습니다.

| Name | Description | 필수 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 서식 | 형식은 `delta`여야 합니다. | 예 | `delta` | format |
| 파일 시스템 | 델타 레이크의 컨테이너/파일 시스템 | 예 | String | fileSystem |
| 폴더 경로 | 델타 레이크의 직접 경로 | 예 | String | folderPath |
| 압축 유형 | 델타 테이블의 압축 유형 | 아니요 | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | compressionType |
| 압축 수준 | 압축이 가능한 빨리 완료되는지 또는 결과 파일이 최적으로 압축되어야 하는지 여부를 선택합니다. | `compressedType`이 지정되면 필수입니다. | `Optimal` 또는 `Fastest` | compressionLevel |
| Vacuum | 이전 버전의 테이블에 대한 보존 임계값(시간)을 지정합니다. 0 이하의 기본값은 30일입니다. | 예 | 정수 | vacuum |
| Update 메서드 | 델타 레이크에서 허용되는 업데이트 작업을 지정합니다. 삽입되지 않은 메서드의 경우 행을 표시하려면 선행 행 변경 변환이 필요합니다. | 예 | `true` 또는 `false` | deletable <br> insertable <br> updateable <br> merge |
| 최적화된 쓰기 | Spark 실행기에서 내부 순서 섞기를 최적화하여 쓰기 작업 처리량을 높입니다. 결과적으로 더 큰 크기의 일부 파티션 및 파일을 볼 수 있습니다. | 아니요 | `true` 또는 `false` | optimizedWrite: true |
| 자동 압축 | 쓰기 작업이 완료되면 Spark에서 ```OPTIMIZE``` 명령을 자동으로 실행하여 데이터를 다시 구성하고, 향후 읽기 성능을 향상시키기 위해 필요한 경우 더 많은 파티션을 생성합니다. | 아니요 | `true` 또는 `false` |    autoCompact: true |

### <a name="delta-sink-script-example"></a>델타 싱크 스크립트 예제

연결된 데이터 흐름 스크립트는 다음과 같습니다.

```
moviesAltered sink(
          input(movieId as integer,
                title as string
            ),
           mapColumn(
                movieId,
                title
            ),
           insertable: true,
           updateable: true,
           deletable: true,
           upsertable: false,
           keys: ['movieId'],
            store: 'local',
           format: 'delta',
           vacuum: 180,
           folderPath: $tempPath + '/delta'
           ) ~> movieDB
```

### <a name="known-limitations"></a>알려진 제한 사항

델타 싱크에 쓸 때 기록된 행 수가 모니터링 출력에 반환되지 않는 알려진 제한 사항이 있습니다.

## <a name="next-steps"></a>다음 단계

* 매핑 데이터 흐름의 [원본 변환](data-flow-source.md)을 만듭니다.
* 매핑 데이터 흐름의 [싱크 변환](data-flow-sink.md)을 만듭니다.
* 행을 삽입, 업데이트, upsert 또는 삭제로 표시하는 [행 변경 변환](data-flow-alter-row.md)을 만듭니다.
