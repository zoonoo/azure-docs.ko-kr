---
title: 'HTTP 통한 웹 URL에서 가져오기: 모듈 참조'
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 서비스에서 HTTP 모듈을 통한 웹 URL에서 가져오기를 사용 하 여 machine learning 실험에에서 사용할 공용 웹 페이지에서 데이터를 읽는 방법에 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 2f0847e9dd90267d985b75be3c3a07ce8fae98a9
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029672"
---
# <a name="import-from-web-url-via-http-module"></a>HTTP 모듈을 통한 웹 URL에서 가져오기

이 문서에서는 Azure Machine Learning 서비스에 대 한 시각적 인터페이스 (미리 보기)의 모듈을 설명 합니다.

이 모듈을 사용 하 여 machine learning 실험에에서 사용할 공용 웹 페이지에서 데이터를 읽습니다.

웹 페이지에 게시 된 데이터에 다음과 같은 제한 사항이 적용 됩니다.

- 데이터는 지원 되는 형식 중 하나 여야 합니다. CSV, TSV, ARFF 또는 SvmLight입니다. 다른 데이터에는 오류가 발생 합니다.
- 인증 안 함 필요 하지 않거나 지원 합니다. 데이터는 공개적으로 사용할 수 있어야 합니다. 

두 가지 방법으로 데이터를 가져오는: 데이터 소스를 설정 하려면 마법사를 사용 하거나 수동으로 구성 합니다.

## <a name="use-the-data-import-wizard"></a>데이터 가져오기 마법사를 사용 합니다.

1. 추가 된 **데이터 가져오기** 모듈을 실험 합니다. 인터페이스에서 모듈을 찾을 수 있습니다 합니다 **데이터 입력 및 출력** 범주입니다.

2. 클릭 **데이터 가져오기 마법사 시작** HTTP 통한 웹 URL을 선택 합니다.

3. URL을 붙여넣고 데이터 형식을 선택 합니다.

4. 경우 구성이 완료 되었습니다.

기존 데이터 연결을 편집 하려면 마법사를 다시 시작 합니다. 마법사를 처음부터 다시 시작 하지 않아도 되도록 모든 이전 구성 세부 정보 로드

## <a name="manually-set-properties-in-the-import-data-module"></a>데이터 가져오기 모듈의 속성을 수동으로 설정 합니다.

다음 단계를 수동으로 가져올 소스를 구성 하는 방법에 설명 합니다.

1. 추가 된 [데이터 가져오기](import-data.md) 모듈을 실험 합니다. 인터페이스에서 모듈을 찾을 수 있습니다 합니다 **데이터 입력 및 출력** 범주입니다.

2. 에 대 한 **데이터 원본**를 선택 **HTTP 통한 웹 URL**합니다.

3. 에 대 한 **URL**, 입력 또는 로드 하려는 데이터를 포함 하는 페이지의 전체 URL을 붙여 넣습니다.

    사이트 URL 및 파일 이름과 확장명을 로드할 데이터를 포함 하는 페이지를 사용 하 여 전체 경로 URL에 포함 되어야 합니다.

    예를 들어, 다음 페이지는 기계 학습 리포지토리 University of california, Irvine에서에서 붓 꽃 데이터 집합을 포함:

    `http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data`

4. 에 대 한 **데이터 형식을**, 지원 되는 데이터 형식 목록에서 선택 합니다.

    형식을 결정 하는 데이터를 미리를 항상 확인 하는 것이 좋습니다. 에서는 UC Irvine 페이지 CSV 형식을 사용 합니다. 기타 지원 되는 데이터 형식은 TSV, ARFF 및 SvmLight입니다.

5. CSV 또는 TSV 형식으로 데이터 인 경우 사용 합니다 **파일에 머리글 행** 원본 데이터에 머리글 행 포함 여부를 나타내려면이 옵션을 합니다. 머리글 행은 열 이름을 할당 하는 데 사용 됩니다.

6. 선택 합니다 **캐시 된 결과 사용 하 여** 옵션 데이터를 많이 변경 하지 않을 경우 각 데이터 다시 로드 하지 않으려는 경우 때마다 실험을 실행 합니다.

    이 옵션을 선택 하면 모듈 실행 되 고 이후에 데이터 집합의 캐시 된 버전을 사용 하 여 첫 번째 데이터에 실험에 로드 합니다.

    실험 데이터 집합의 각 반복에 있는 데이터 집합을 다시 로드 하려는 경우 선택 취소 합니다 **캐시 된 결과 사용 하 여** 옵션입니다. 결과 매개 변수를 변경한 경우에 리로드 됩니다 [데이터 가져오기](import-data.md)합니다.

7. 실험을 실행합니다.

## <a name="results"></a>결과

완료 되 면 출력 데이터 집합을 클릭 하 고 선택 **시각화** 데이터를 성공적으로 가져온 경우를 확인 합니다.


## <a name="next-steps"></a>다음 단계

참조를 [사용할 수 있는 모듈 집합](module-reference.md) Azure Machine Learning 서비스입니다. 