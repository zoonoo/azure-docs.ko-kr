---
title: Azure Data Factory의 델타 형식
description: 델타 형식을 사용 하 여 델타 lake에서 데이터 변환 및 이동
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: daperlov
ms.openlocfilehash: e9df7b00a384859fb29577be0ad05da233683f46
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044523"
---
# <a name="delta-format-in-azure-data-factory"></a>Azure Data Factory의 델타 형식

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 델타 형식을 사용 하 여 [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) 또는 [Azure Blob Storage](connector-azure-blob-storage.md) 에 저장 된 델타 lake로 데이터를 복사 하는 방법을 중점적으로 설명 합니다. 이 커넥터는 데이터 흐름을 원본 및 싱크로 매핑하는 [인라인 데이터 집합](data-flow-source.md#inline-datasets) 으로 사용할 수 있습니다.

> [!NOTE]
> 데이터 흐름 매핑을 위한 델타 형식 커넥터는 현재 공개 미리 보기로 제공 됩니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4ALTs]

## <a name="mapping-data-flow-properties"></a>매핑 데이터 흐름 속성

이 커넥터는 데이터 흐름을 원본 및 싱크로 매핑하는 [인라인 데이터 집합](data-flow-source.md#inline-datasets) 으로 사용할 수 있습니다.

### <a name="source-properties"></a>원본 속성

다음 표에서는 델타 원본에서 지 원하는 속성을 나열 합니다. 이러한 속성은 **원본 옵션** 탭에서 편집할 수 있습니다.

| Name | 설명 | 필수 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 서식 | 형식은 이어야 합니다.`delta` | 예 | `delta` | format |
| 파일 시스템 | 델타 lake의 컨테이너/파일 시스템 | 예 | String | fileSystem |
| 폴더 경로 | 델타 lake의 직접 | 예 | String | folderPath |
| 압축 유형 | 델타 테이블의 압축 유형입니다. | 아니요 | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | compressionType |
| 압축 수준 | 압축이 최대한 빠르게 완료 되는지 또는 결과 파일이 최적으로 압축 되어야 하는지 여부를 선택 합니다. | `compressedType`가 지정 된 경우 필수 사항입니다. | `Optimal` 또는 `Fastest` | compressionLevel |
| 시간 이동 | 델타 테이블의 이전 스냅숏을 쿼리할 지 여부를 선택 합니다. | 아니요 | Timestamp 별 쿼리: 타임 스탬프 <br> 버전별 쿼리: Integer | timestampAsOf <br> versionAsOf |

#### <a name="import-schema"></a>스키마 가져오기

델타는 인라인 데이터 집합 으로만 사용할 수 있으며 기본적으로 연결 된 스키마가 없습니다. 열 메타 데이터를 가져오려면 **프로젝션** 탭에서 **스키마 가져오기** 단추를 클릭 합니다. 이렇게 하면 모음에서 지정한 열 이름과 데이터 형식을 참조할 수 있습니다. 스키마를 가져오려면 [데이터 흐름 디버그 세션이](concepts-data-flow-debug-mode.md) 활성 상태 여야 하 고 기존 CDM 엔터티 정의 파일을 가리켜야 합니다.
 

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

아래 표에는 델타 싱크에 의해 지원 되는 속성이 나와 있습니다. 이러한 속성은 **설정** 탭에서 편집할 수 있습니다.

| Name | 설명 | 필수 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 서식 | 형식은 이어야 합니다.`delta` | 예 | `delta` | format |
| 파일 시스템 | 델타 lake의 컨테이너/파일 시스템 | 예 | String | fileSystem |
| 폴더 경로 | 델타 lake의 직접 | 예 | String | folderPath |
| 압축 유형 | 델타 테이블의 압축 유형입니다. | 아니요 | `bzip2`<br>`gzip`<br>`deflate`<br>`ZipDeflate`<br>`snappy`<br>`lz4` | compressionType |
| 압축 수준 | 압축이 최대한 빠르게 완료 되는지 또는 결과 파일이 최적으로 압축 되어야 하는지 여부를 선택 합니다. | `compressedType`가 지정 된 경우 필수 사항입니다. | `Optimal` 또는 `Fastest` | compressionLevel |
| Vacuum | 이전 버전의 테이블에 대 한 보존 임계값을 시간 단위로 지정 합니다. 0 이하의 기본값은 30 일입니다. | 예 | 정수 | 진공 |
| Update 메서드 | 델타 lake에서 허용 되는 업데이트 작업을 지정 합니다. 삽입 되지 않는 메서드의 경우 행을 표시 하려면 이전 alter row 변환이 필요 합니다. | 예 | `true` 또는 `false` | 삭제할 <br> 삽입 가능한 <br> 있는 <br> upsertable |

### <a name="delta-sink-script-example"></a>델타 싱크 스크립트 예제

연결 된 데이터 흐름 스크립트는 다음과 같습니다.

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

델타 싱크에 쓸 때 기록 되는 행 수가 모니터링 출력에 반환 되지 않는 알려진 제한 사항이 있습니다.

## <a name="next-steps"></a>다음 단계

* 매핑 데이터 흐름에서 [원본 변환을](data-flow-source.md) 만듭니다.
* 매핑 데이터 흐름에서 [싱크 변환을](data-flow-sink.md) 만듭니다.
* 행을 insert, update, upsert 또는 delete로 표시 하려면 [alter row 변환을](data-flow-alter-row.md) 만듭니다.
