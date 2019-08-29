---
title: 'HTTP를 통해 웹 URL에서 가져오기: 모듈 참조'
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 서비스의 HTTP 모듈을 통해 웹 URL에서 가져오기를 사용 하 여 Machine Learning 실험에서 사용 하기 위해 공용 웹 페이지에서 데이터를 읽는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 59b8e2e73b9904a503c16d8891e5a5bd771fc87f
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128749"
---
# <a name="import-from-web-url-via-http-module"></a>HTTP 모듈을 통해 웹 URL에서 가져오기

이 문서에서는 Azure Machine Learning 서비스에 대 한 시각적 인터페이스 (미리 보기)의 모듈을 설명 합니다.

이 모듈을 사용 하 여 machine learning 실험에서 사용 하기 위해 공용 웹 페이지의 데이터를 읽을 수 있습니다.

웹 페이지에 게시 된 데이터에는 다음과 같은 제한 사항이 적용 됩니다.

- 데이터는 지원 되는 형식 중 하나 여야 합니다. CSV, TSV, ARFF 또는 SvmLight입니다. 다른 데이터는 오류를 발생 시킵니다.
- 인증이 필요 하거나 지원 되지 않습니다. 데이터는 공개적으로 사용할 수 있어야 합니다. 

데이터를 가져오는 방법에는 두 가지가 있습니다. 마법사를 사용 하 여 데이터 원본을 설정 하거나 수동으로 구성할 수 있습니다.

## <a name="use-the-data-import-wizard"></a>데이터 가져오기 마법사 사용

1. **데이터 가져오기** 모듈을 실험에 추가 합니다. 인터페이스의 **데이터 입력 및 출력** 범주에서 모듈을 찾을 수 있습니다.

2. **데이터 가져오기 마법사 시작** 을 클릭 하 고 HTTP를 통해 웹 URL을 선택 합니다.

3. URL에 붙여 넣고 데이터 형식을 선택 합니다.

4. 구성이 완료 된 경우

기존 데이터 연결을 편집 하려면 마법사를 다시 시작 합니다. 마법사에서 모든 이전 구성 세부 정보를 로드 하므로 처음부터 다시 시작할 필요가 없습니다.

## <a name="manually-set-properties-in-the-import-data-module"></a>데이터 가져오기 모듈에서 수동으로 속성 설정

다음 단계에서는 가져오기 원본을 수동으로 구성 하는 방법을 설명 합니다.

1. [데이터 가져오기](import-data.md) 모듈을 실험에 추가 합니다. 인터페이스의 **데이터 입력 및 출력** 범주에서 모듈을 찾을 수 있습니다.

2. **데이터 원본**에 대해 **HTTP를 통해 웹 URL**을 선택 합니다.

3. **Url**에 로드 하려는 데이터가 포함 된 페이지의 전체 url을 입력 하거나 붙여 넣습니다.

    URL에는 로드할 데이터를 포함 하는 페이지에 대 한 사이트 URL 및 전체 경로와 파일 이름 및 확장명이 포함 되어야 합니다.

    예를 들어, 다음 페이지에는 캘리포니아의 대학 Irvine 기계 학습 리포지토리의 Iri 데이터 집합이 포함 되어 있습니다.

    `https://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data`

4. **데이터 형식**의 경우 목록에서 지원 되는 데이터 형식 중 하나를 선택 합니다.

    항상 데이터를 미리 확인 하 여 형식을 결정 하는 것이 좋습니다. UC Irvine 페이지는 CSV 형식을 사용 합니다. 지원 되는 기타 데이터 형식은 TSV, ARFF 및 SvmLight입니다.

5. 데이터가 CSV 또는 TSV 형식이 면 **파일에 헤더 행** 이 포함 되어 있는지 여부를 나타냅니다. 머리글 행은 열 이름을 할당 하는 데 사용 됩니다.

6. 데이터를 크게 변경 하지 않을 경우 또는 실험을 실행할 때마다 데이터를 다시 로드 하지 않으려는 경우에는 **캐시 된 결과 사용** 옵션을 선택 합니다.

    이 옵션을 선택 하면 실험은 모듈이 처음 실행 될 때 데이터를 로드 하 고 이후에 캐시 된 버전의 데이터 집합을 사용 합니다.

    실험 데이터 집합의 각 반복에서 데이터 집합을 다시 로드 하려면 **캐시 된 결과 사용** 옵션을 선택 취소 합니다. [데이터 가져오기](import-data.md)의 매개 변수가 변경 된 경우에도 결과가 다시 로드 됩니다.

7. 실험을 실행합니다.

## <a name="results"></a>결과

완료 되 면 출력 데이터 집합을 클릭 하 고 **시각화** 를 선택 하 여 데이터를 성공적으로 가져왔는지 확인 합니다.


## <a name="next-steps"></a>다음 단계

Azure Machine Learning 서비스에 [사용할 수 있는 모듈 집합](module-reference.md) 을 참조 하세요. 