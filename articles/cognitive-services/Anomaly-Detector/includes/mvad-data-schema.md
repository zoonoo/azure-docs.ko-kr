---
title: MVAD 데이터 스키마
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: quying
manager: tonyxin
ms.service: cognitive-services
ms.topic: include
ms.date: 7/1/2021
ms.author: yingqunpku
ms.openlocfilehash: 3be9d5d50d34eee35de38b7b28dc1d42217ace24
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292644"
---
### <a name="input-data-schema"></a>입력 데이터 스키마

MVAD는 메트릭 그룹에서 변칙을 검색하며 각 메트릭을 **변수** 또는 시계열이라고 합니다.

* Microsoft에서 샘플 데이터 파일을 다운로드하여 [https://aka.ms/AnomalyDetector/MVADSampleData](https://aka.ms/AnomalyDetector/MVADSampleData)에서 허용되는 스키마를 확인할 수 있습니다.
* 각 변수에는 `timestamp` 및 `value`라는 두 필드만 있어야 하며 각 변수는 CSV(쉼표로 구분된 값) 파일로 저장해야 합니다.
* CSV 파일의 열 이름은 대/소문자를 구분하여 정확하게 `timestamp` 및 `value`이어야 합니다.
* `timestamp` 값은 ISO 8601을 준수해야 합니다. `value`는 소수 자릿수가 있는 정수 또는 소수일 수 있습니다.
    CSV 파일의 콘텐츠에 대한 좋은 예는 다음과 같습니다.

    |timestamp | 값|
    |-------|-------|
    |2019-04-01T00:00:00Z| 5|
    |2019-04-01T00:01:00Z| 3.6|
    |2019-04-01T00:02:00Z| 4|
    |...| ...|

    > [!NOTE]
    > 타임스탬프에 시간, 분 및/또는 초가 있는 경우 API를 호출하기 전에 타임스탬프가 올바르게 반올림되었는지 확인합니다.
    >
    > 예를 들어, 데이터 빈도가 30초마다 하나의 데이터 요소여야 하지만 “12:00:01” 및 “12:00:28”과 같은 타임스탬프가 표시되는 경우 이는 타임스탬프를 “12:00:00” 및 “12:00:30”과 같은 새 값으로 전처리해야 한다는 강력한 신호입니다.
    >
    > 자세한 내용은 모범 사례 문서의 [“타임스탬프 반올림” 섹션](../concepts/best-practices-multivariate.md#timestamp-round-up)을 참조하세요.
* Csv 파일의 이름은 변수 이름으로 사용되며 고유해야 합니다. 예를 들면 "temperature.csv" 및 "humidity.csv"입니다.
* 학습 관련 변수와 유추 관련 변수는 일관되어야 합니다. 예를 들어 `series_1`, `series_2`, `series_3`, `series_4`, `series_5`를 학습에 사용하는 경우 유추를 위해 똑같은 변수를 제공해야 합니다.
* CSV 파일은 zip 파일로 압축되고 Azure Blob 컨테이너에 업로드되어야 합니다. zip 파일에는 원하는 이름을 사용할 수 있습니다.

#### <a name="folder-structure"></a>폴더 구조

데이터 준비 시 일반적인 실수는 zip 파일에 추가 폴더가 있는 것입니다. 예를 들어, zip 파일의 이름이 `series.zip`이라고 가정합니다. 새 폴더 `./series`에 파일의 압축을 푼 후 CSV 파일의 **올바른** 경로는 `./series/series_1.csv`이며 **잘못된** 경로는 `./series/foo/bar/series_1.csv`일 수 있습니다.

Windows에서 zip 파일의 압축을 푼 후 디렉터리 트리의 올바른 예제

```bash
.
└── series
    ├── series_1.csv
    ├── series_2.csv
    ├── series_3.csv
    ├── series_4.csv
    └── series_5.csv
```

Windows에서 zip 파일의 압축을 푼 후 디렉터리 트리의 잘못된 예제

```bash
.
└── series
    └── series
        ├── series_1.csv
        ├── series_2.csv
        ├── series_3.csv
        ├── series_4.csv
        └── series_5.csv
```