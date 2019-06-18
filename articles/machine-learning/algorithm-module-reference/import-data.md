---
title: 데이터를 가져옵니다. 모듈 참조
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 서비스에서 데이터 가져오기 모듈을 사용 하 여 기존 클라우드 데이터 서비스에서 machine learning 실험에 데이터를 로드 하는 방법에 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ed51c4e7b6c7d226c7827d1ba00bc96a7be1e6b0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028307"
---
# <a name="import-data-module"></a>데이터 모듈 가져오기

이 문서에서는 Azure Machine Learning 서비스에 대 한 시각적 인터페이스 (미리 보기)의 모듈을 설명 합니다.

이 모듈을 사용 하 여 기존 클라우드 데이터 서비스에서 machine learning 실험에 데이터를 로드 합니다.  
모듈에는 이제 저장소 옵션을 선택 하 고 신속 하 게 모든 옵션을 구성 하려면 기존 구독 및 계정 중에서 선택할 수 있도록 하는 마법사를 갖추고 있습니다. 기존 데이터 연결을 편집 하려면 필요 합니까? 문제 없습니다. 마법사를 처음부터 다시 시작 하지 않아도 되도록 모든 이전 구성 세부 정보를 로드 합니다. 
  
원본에 연결 하려면 데이터를 정의 하 고 나면 [데이터 가져오기](./import-data.md) 에서 값을 포함 하 고 Azure Machine Learning 작업 영역에 데이터를 로드 하는 각 열의 데이터 형식을 유추 합니다. 출력 [데이터 가져오기](./import-data.md) 모든 실험에 사용할 수 있는 데이터입니다.

  
원본 데이터가 변경 된 경우에 데이터 집합을 새로 다시 실행 하 여 새 데이터를 추가할 수 있습니다 [데이터 가져오기](./import-data.md)합니다. 그러나 실험을 실행할 때마다를 원본에서 다시 읽을 하지 않으려는 경우 선택 합니다 **캐시 된 결과 사용 하 여** 옵션을 true로 합니다. 이 옵션을 선택 하면 모듈을 실험에 이전에 동일한 소스와 같은 입력된 옵션을 사용 하 여 실행 여부를 확인 합니다. 이전 실행 발견 되 면 캐시의 데이터 원본에서 데이터를 다시 로드 하는 대신 사용 됩니다.
 

## <a name="data-sources"></a>데이터 원본

데이터 가져오기 모듈에는 다음 데이터 원본을 지원합니다. 자세한 지침과 각 데이터 소스를 사용 하는 예제에 대 한 링크를 클릭 합니다. 
 
데이터를 저장 해야 하는 방법 또는 where 확실 하지 않은 경우이 가이드는 데이터 과학 프로세스의 일반적인 데이터 시나리오를 보려면  [Azure Machine Learning의 고급 분석 시나리오](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-plan-sample-scenarios)합니다. 


|데이터 원본| 사용|
|-----------|-----------|  
|[HTTP 통한 웹 URL](./import-from-web-url-via-http.md)|HTTP를 사용 하 고 CSV, TSV, ARFF 또는 SvmLight 형식에서 제공 하는 웹 URL에서 호스트 되는 데이터 가져오기|  
|[Azure Blob Storage에서 가져오기](./import-from-azure-blob-storage.md) |Azure blob service에 저장 된 데이터 가져오기|  

## <a name="how-to-use-import-data"></a>데이터 가져오기를 사용 하는 방법
 
1. 추가 된 **데이터 가져오기** 모듈을 실험 합니다. 이 모듈에서 찾을 수 있습니다 합니다 **데이터 입력 및 출력** 인터페이스에는 범주입니다.

2. 클릭 **데이터 가져오기 마법사 시작** 마법사를 사용 하 여 데이터 원본을 구성 하 여 합니다.

    마법사는 계정 이름 및 자격 증명 및 기타 옵션을 구성 하는 도움말을 가져옵니다. 기존 구성을 편집 하는 경우 현재 값을 먼저 로드 합니다.

3. 마법사를 사용 하려면 클릭 **데이터 원본**, 클라우드 기반 저장소에서 읽는 유형을 선택 합니다. 

    추가 설정은 선택 하면 저장소의 유형에 따라 달라 집니다 여부 저장소 보호 되는 여부 및 합니다. 계정 이름, 파일 형식 또는 자격 증명을 제공 해야 합니다. 일부 소스 인증 필요 하지 않습니다. 다른 사용자에 대 한 계정 이름, 키 또는 컨테이너 이름을 알아야 할 수 있습니다.

4. 선택 된 **사용 하 여 결과 캐시** 연속 실행에 다시 사용할 수 있도록 데이터 집합을 캐시 하려는 경우 옵션입니다.

    모듈 매개 변수 없는 다른 변경 사항이 가정 하 고, 실험 모듈이 실행 되 고 이후에 데이터 집합의 캐시 된 버전을 사용 하 여 첫 번째 데이터 시간을 로드 합니다.

    실험을 실행할 때마다 데이터를 다시 로드 해야 할 경우이 옵션의 선택을 취소 합니다.

5. 실험을 실행합니다.

    에 포함 된 값을 기반으로 각 열의 데이터 형식을 유추한 경우 인터페이스에 데이터를 로드 하는 데이터 가져오기, 숫자 또는 범주입니다.

    - 헤더가 있으면 헤더는 출력 데이터 집합의 열 이름을 사용 됩니다.

    - 데이터의 기존 열 머리글이 없는 경우 형식 col1, col2를 사용 하 여 새 열 이름은 생성 하는 중... coln * 합니다.

## <a name="results"></a>결과

가져오기가 완료 되 면 출력 데이터 집합을 클릭 하 고 선택 **시각화** 데이터를 성공적으로 가져온 경우를 확인 합니다.

다시 사용 하기 위해 데이터를 저장 하려는 경우 새 데이터 집합이 실험 실행 될 때마다를 가져오는 대신 출력 마우스 **데이터 집합으로 저장**합니다. 데이터 집합에 대 한 이름을 선택 합니다. 저장된 된 데이터 집합 저장 시 데이터를 유지 하 고 데이터가 업데이트 되지 않습니다 실험을 다시 실행 하는 경우 실험에서 데이터 집합을 변경 하는 경우에 키를 누릅니다. 데이터의 스냅숏을 만들기 위한 편리할 수 있습니다.

데이터를 가져온 후 일부 추가 준비 모델링 및 분석을 위해 필요할 수 있습니다.


- 사용 하 여 [메타 데이터 편집](./edit-metadata.md) 을 다른 데이터 형식으로 열을 처리 하거나 되도록 하려면 일부 열 레이블 또는 기능 열 이름을 변경 합니다.

- 사용 하 여 [Select Columns in Dataset](./select-columns-in-dataset.md) 를 변환 하거나 모델링에 사용 하 여 열의 하위 집합을 선택 합니다. 변환 되거나 제거 된 열 수를 사용 하 여 쉽게 원래 데이터 집합에 가입 하는 수를 [열 추가](./add-columns.md) 모듈입니다.  

- 사용 하 여 [분할 및 샘플링](./partition-and-sample.md) 데이터 집합을 분할 샘플링을 수행 하거나 상위 n 개 행을 가져옵니다.

## <a name="next-steps"></a>다음 단계

참조를 [사용할 수 있는 모듈 집합](module-reference.md) Azure Machine Learning 서비스입니다. 