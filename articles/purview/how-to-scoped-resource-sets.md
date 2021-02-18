---
title: '방법: 범위 리소스 집합 구성 만들기'
description: 범위가 리소스 집합으로 그룹화 되는 방법을 덮어쓰도록 범위가 지정 된 리소스 집합 구성 규칙을 만드는 방법에 대해 알아봅니다.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/17/2021
ms.openlocfilehash: 517b07eecdbc63754f46fcf1051bf5b987dbc20e
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654447"
---
# <a name="create-scoped-resource-set-configuration-rules"></a>범위가 지정 된 리소스 집합 구성 규칙 만들기

규모에 맞게 데이터 처리 시스템은 일반적으로 디스크에 단일 테이블을 여러 파일로 저장 합니다. 이 개념은 리소스 집합을 사용 하 여 Azure 부서의 범위에 표시 됩니다. 리소스 집합은 저장소의 자산을 많이 나타내는 데이터 카탈로그의 단일 개체입니다. 자세히 알아보려면 [리소스 집합 이해](concept-resource-sets.md)를 참조 하세요.

저장소 계정을 스캔 하는 경우 Azure 부서의 범위는 정의 된 패턴 집합을 사용 하 여 자산 그룹이 리소스 집합 인지 여부를 확인 합니다. 경우에 따라 Azure 부서의 범위의 리소스 집합 그룹화에서 데이터 공간을 정확 하 게 반영 하지 못할 수 있습니다. 범위가 지정 된 리소스 집합 규칙을 사용 하 여 Azure 부서의 범위가 리소스 집합으로 그룹화 되는 자산과 카탈로그 내에 표시 되는 방식을 검색 하는 방법을 사용자 지정 하거나 재정의할 수 있습니다.

## <a name="how-to-create-a-scoped-resource-set-configuration"></a>범위가 지정 된 리소스 집합 구성을 만드는 방법

다음 단계를 수행 하 여 새 범위 지정 리소스 집합 구성을 만듭니다.

1. 관리 센터로 이동 합니다. 메뉴에서 **범위 지정 리소스 집합** 을 선택 합니다. **+ 새로** 만들기를 클릭 하 여 새 구성 규칙 집합을 만듭니다.
        :::image type="content" source="media/how-to-scoped-resource-sets/create-new-scoped-resource-set-rule.png" alt-text="새 범위 지정 리소스 집합 규칙 만들기" border="true":::

1. 범위가 지정 된 리소스 집합 구성의 범위를 입력 합니다. 저장소 계정 유형 및 규칙 집합을 만들려는 저장소 계정의 이름을 선택 합니다. 각 규칙 집합은 **폴더 경로** 필드에 지정 된 폴더 경로 범위를 기준으로 적용 됩니다. 
        :::image type="content" source="media/how-to-scoped-resource-sets/create-new-scoped-resource-set-scope.png" alt-text="새 범위 지정 리소스 집합 규칙 만들기" border="true":::

1. 구성 범위에 대 한 규칙을 입력 하려면 **+ 새 규칙** 을 선택 합니다.
1. 다음 필드에를 입력 하 여 규칙을 만듭니다.
    1. **규칙 이름:** 구성 규칙의 이름입니다. 이 필드는 규칙이 적용 되는 자산에는 영향을 주지 않습니다.
    1. **정규화 된 이름:** 텍스트, 동적 replacers 및 정적 replacers 조합을 사용 하 여 자산을 구성 규칙에 일치 시키는 정규화 된 경로입니다. 이 경로는 구성 규칙의 범위를 기준으로 합니다. 정규화 된 이름을 지정 하는 방법에 대 한 자세한 지침은 아래 [구문](#syntax) 섹션을 참조 하세요. 
    1. **표시 이름:** 자산의 표시 이름입니다. 이 필드는 선택 사항입니다. 일반 텍스트 및 정적 replacers를 사용 하 여 카탈로그에 자산이 표시 되는 방식을 사용자 지정할 수 있습니다. 자세한 내용은 아래의 [구문](#syntax) 섹션을 참조 하십시오.
    1. **리소스 집합으로 그룹화 안 함:** 사용 하도록 설정 하면 일치 하는 리소스가 리소스 집합으로 그룹화 되지 않습니다. 
        :::image type="content" source="media/how-to-scoped-resource-sets/scoped-resource-set-rule-example.png" alt-text="새 범위 지정 리소스 집합 규칙 만들기" border="true"::: 
1. **추가** 를 클릭 하 여 규칙을 저장 합니다. 

## <a name="scoped-resource-set-syntax"></a><a name="syntax"></a> 범위가 지정 된 리소스 집합 구문

범위가 지정 된 리소스 집합 규칙을 만드는 경우 다음 구문을 사용 하 여에 적용 되는 자산 규칙을 지정 합니다.

### <a name="static-replacers-single-brackets"></a>Static replacers (대괄호)

범위가 지정 된 리소스 집합 규칙에서 하나의 대괄호가 **동적 replacers** 사용 됩니다. 형식을 사용 하 여 정규화 된 이름에 동적 치환를 지정 `{<replacerName:<replacerType>}` 합니다. 일치 하는 경우 동적 replacers는 자산을 리소스 집합으로 표시 해야 함을 나타내는 그룹화 조건으로 사용 됩니다. 자산이 리소스 집합으로 그룹화 되는 경우 리소스 집합 정규화 된 경로에는 `{replacerName}` 치환가 지정 된 위치가 포함 됩니다.

예를 들어 두 자산이 `folder1/file-1.csv` `folder2/file-2.csv` 규칙과 일치 하는 경우 `{folder:string}/file-{NUM:int}.csv` 리소스 집합은 단일 엔터티가 됩니다 `{folder}/file-{NUM}.csv` .

#### <a name="special-case-dynamic-replacers-when-not-grouping-into-resource-set"></a>특수 한 경우: 동적 replacers 리소스 집합으로 그룹화 하지 않는 경우

범위가 지정 된 리소스 집합 규칙에 대 한 *리소스 집합이* 사용 하도록 설정 되어 있지 않은 경우 치환 이름은 선택적 필드입니다. `{:<replacerType>}` 유효한 구문입니다. 예를 들어, `file-{:int}.csv` 및에 대해가 성공적으로 일치 하 `file-1.csv` `file-2.csv` 고 리소스 집합 대신 두 개의 다른 자산을 만듭니다.

### <a name="static-replacers-double-brackets"></a>Static replacers (이중 대괄호)

범위 리소스 집합 규칙의 정규화 된 이름에는 이중 대괄호가 **정적 replacers** 사용 됩니다. 형식을 사용 하 여 정규화 된 이름에 정적 치환를 지정 합니다 `{{<replacerName>:<replacerType>}}` . 일치 하는 경우 고유한 정적 치환 값의 각 집합은 다른 리소스 집합 그룹화를 만듭니다.

예를 들어 두 자산 `folder1/file-1.csv` 및 `folder2/file-2.csv` 가 규칙에 일치 하는 경우 `{{folder:string}}/file-{NUM:int}.csv` 두 개의 리소스 집합이 생성 됩니다 `folder1/file-{NUM}.csv` `folder2/file-{NUM}.csv` .

Static replacers는 범위 지정 리소스 집합 규칙과 일치 하는 자산의 표시 이름을 지정 하는 데 사용할 수 있습니다. `{{<replacerName>}}`규칙의 표시 이름에를 사용 하면 자산 이름에 일치 하는 값이 사용 됩니다.

### <a name="available-replacement-types"></a>사용 가능한 대체 형식

다음은 정적 및 동적 replacers에서 사용할 수 있는 형식입니다.

| 유형 | 구조체 |
| ---- | --------- |
| 문자열 | 공백과 같은 구분 기호를 포함 하는 일련의 1 개 이상의 유니코드 문자입니다. |
| int | 1 개 이상의 0-9 ASCII 문자, 0 (예: 0001)이 될 수 있습니다. |
| guid | Defineddefa에 있는 UUID의 일련의 32 또는 8-4-4-4-12 문자열 표현입니다. https://tools.ietf.org/html/rfc4122 |
| date | 다음에 지정 된 선택적 구분 기호를 사용 하는 일련 6 또는 8 0-9 ASCII 문자 (yyyymmdd, yyyy-mm-dd, yymmdd, yy-mm-dd) https://tools.ietf.org/html/rfc3339 |
| time | 다음에 지정 된 선택적 구분 기호를 사용 하는 일련의 4 또는 6 0-9 ASCII 문자 (HHmm, HH: mm, HHmmss, HH: mm: ss) https://tools.ietf.org/html/rfc3339 |
| timestamp | 선택적 구분 기호를 사용 하는 일련 12 또는 14 0-9 ASCII 문자 (yyyy-mm-Yyyy-mm-ddthh: mm, yyyymmddhhmm, yyyy-Yyyy-mm-ddthh: mm: ss, yyyymmddHHmmss에 지정 되어 있습니다. https://tools.ietf.org/html/rfc3339 |
| boolean | 대/소문자를 구분 하지 않고 ' true ' 또는 ' f a l l '을 포함할 수 있습니다. |
| 숫자 | 0 개 이상의 일련의 0-9 ASCII 문자, 0 (예: 0001) 뒤에 선택적으로 점 '. '가 올 수 있습니다. 하나 이상의 0-9 ASCII 문자 시리즈는 0 되도록 후 위 (예: 100) 일 수 있습니다. | 
| hex | 집합 0-1 및 A-f의 일련의 1 개 이상의 ASCII 문자 값은 0 접두사가 될 수 있습니다. |
| locale | 에 지정 된 구문과 일치 하는 문자열입니다. https://tools.ietf.org/html/rfc5646 |

## <a name="order-of-scoped-resource-set-rules-getting-applied"></a>범위가 지정 된 리소스 집합 규칙이 적용 되는 순서입니다.

범위 리소스 집합 규칙을 적용 하는 작업의 순서는 다음과 같습니다.

1. 자산이 두 규칙에 일치 하는 경우 더 구체적인 범위가 우선적으로 적용 됩니다. 예를 들어 범위의 규칙 `container/folder` 은 범위 내 규칙 보다 먼저 적용 됩니다 `container` . 
1. 특정 범위 내에 있는 규칙의 순서입니다. 이는 UX에서 편집할 수 있습니다.
1. 자산이 지정 된 규칙과 일치 하지 않으면 기본 리소스 집합 추론을 적용 합니다.

## <a name="examples"></a>예제

### <a name="example-1"></a>예 1

전체 및 델타 로드로 SAP 데이터 추출

*입력*

Files:
-   `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_01.txt`
-   `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_02.txt`
-   `https://myazureblob.blob.core.windows.net/bar/customer/delta/2020/01/15/saptable_customer_20200101_20200102_01.txt`
-   `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_01.txt`
-   `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_02.txt`


*범위가 지정 된 리소스 집합 규칙*

**범위:**https://myazureblob.blob.core.windows.net/bar/

**표시 이름:** ' 외부 고객 '

**정규화 된 이름:**`customer/{extract:string}/{year:int}/{month:int}/{day:int}/saptable_customer_{date_from:date}_{date_to:time}_{sequence:int}.txt`

**리소스 집합:** true

*출력*

하나의 리소스 집합 자산

**표시 이름:** 외부 고객

**정규화 된 이름:**`https://myazureblob.blob.core.windows.net/bar/customer/{extract}/{year}/{month}/{day}/saptable_customer_{date_from}_{date_to}_{sequence}.txt`

### <a name="example-2"></a>예제 2

Avro 형식의 IoT 데이터

*입력*

Files:
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

*범위가 지정 된 리소스 집합 규칙*

**범위:**https://myazureblob.blob.core.windows.net/bar/

규칙 1

**표시 이름:** ' 컴퓨터-89 '

**정규화 된 이름:**`raw/machinename-89/{date:date}/{time:time}-{id:int}.avro`

**리소스 집합:** true

규칙 2

**표시 이름:** ' 컴퓨터-90 '

**정규화 된 이름:**`raw/machinename-90/{date:date}/{time:time}-{id:int}.avro`

**리소스 집합: true**

*출력*

리소스 집합 2 개 

리소스 집합 1

**표시 이름:** 컴퓨터-89

**정규화 된 이름:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

리소스 집합 2

**표시 이름:** 컴퓨터-90

**정규화 된 이름:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-3"></a>예제 3

Avro 형식의 IoT 데이터

*입력*

Files:
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
-   `https://myazureblob.blob.core.windows.netbar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

*범위가 지정 된 리소스 집합 규칙*

**범위:**https://myazureblob.blob.core.windows.net/bar/

**표시 이름:** ' Machine-{{machineid}} '

**정규화 된 이름:**`raw/machinename-{{machineid:int}}/{date:date}/{time:time}-{id:int}.avro`

**리소스 집합:** true

*출력*

리소스 집합 1

**표시 이름:** 컴퓨터-89

**정규화 된 이름:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

리소스 집합 2

**표시 이름:** 컴퓨터-90

**정규화 된 이름:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-4"></a>예제 4

리소스 집합으로 그룹화 안 함

*입력*

Files:
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

*범위가 지정 된 리소스 집합 규칙*

**범위:**https://myazureblob.blob.core.windows.net/bar/

**표시 이름:** ' Machine-{{machineid}} '

**정규화 된 이름:**`raw/machinename-{{machineid:int}}/{{:date}}/{{:time}}-{{:int}}.avro`

**리소스 집합:** false

*출력*

개별 자산 4 개

자산 1

**표시 이름:** 컴퓨터-89

**정규화 된 이름:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`

자산 2

**표시 이름:** 컴퓨터-89

**정규화 된 이름:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`

자산 3

**표시 이름:** 컴퓨터-89

**정규화 된 이름:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`

자산 4

**표시 이름:** 컴퓨터-90

**정규화 된 이름:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

## <a name="next-steps"></a>다음 단계

[Azure Data Lake Gen2 storage 계정을 등록 하 고 검사](register-scan-adls-gen2.md)하 여 시작 하세요.