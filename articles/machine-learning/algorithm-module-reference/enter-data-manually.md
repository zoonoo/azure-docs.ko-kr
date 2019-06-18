---
title: 데이터를 수동으로 입력 합니다. 모듈 참조
titleSuffix: Azure Machine Learning service
description: 값을 입력 하 여 작은 데이터 집합을 만들려면 Azure Machine Learning 서비스에서 수동으로 데이터 입력 모듈을 사용 하는 방법을 알아봅니다. 데이터 집합에는 여러 열 있을 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ee15b6fb7160ece907d55e790b0ae38ee458ab96
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028607"
---
# <a name="enter-data-manually-module"></a>수동으로 데이터 모듈 입력

이 문서에서는 Azure Machine Learning 서비스에 대 한 시각적 인터페이스 (미리 보기)의 모듈을 설명 합니다.

값을 입력 하 여 작은 데이터 집합을 만들려면이 모듈을 사용 합니다. 데이터 집합에는 여러 열 있을 수 있습니다.
  
이 모듈은 같은 시나리오에서 유용할 수 있습니다.  
  
- 테스트에 대 한 값의 작은 집합을 생성합니다.  
  
- 레이블의 짧은 목록 만들기
  
- 데이터 집합에 삽입할 열 이름 목록을 입력 합니다.

## <a name="enter-data-manually"></a>수동으로 데이터 입력 
  
1.  추가 된 [수동으로 데이터 입력](./enter-data-manually.md) 모듈을 실험 합니다. 이 모듈에서 찾을 수 있습니다 합니다 **데이터 입력 및 출력** Azure Machine Learning의 범주입니다. 
  
2.  에 대 한 **DataFormat**, 다음 옵션 중 하나를 선택 합니다. 이러한 옵션은 사용자가 제공한 데이터 구문 분석 되어야 하는 방법을 결정 합니다. 각 형식에 대 한 요구 사항을 크게 다를, 관련된 항목을 참조 해야 합니다.  
  
    -   **ARFF**. 특성 관계 파일 형식으로 Weka 사용 합니다.   
  
    -   **CSV**. 쉼표로 구분 된 값 형식입니다. 자세한 내용은 [CSV로 변환](./convert-to-csv.md)합니다.  
  
    -   **SVMLight**. Vowpal Wabbit과 다른 기계 학습 프레임 워크에서 사용 하는 형식입니다.  
  
    -   **TSV**. 탭으로 구분 된 값 형식입니다.

     형식을 선택 하 고 서식 사양을 충족 하는 데이터가 제공 되지 않는 경우 런타임 오류가 발생 합니다.
  
3.  내부를 클릭 합니다 **데이터** 데이터 입력을 시작 하려면 텍스트 상자입니다. 다음 형식에는 특별 한 주의가 필요합니다.  
  
    - **CSV**:  여러 열을 만들려면 쉼표로 구분 된 텍스트로 붙여 넣거나 필드 간에 쉼표를 사용 하 여 여러 열을 입력 합니다.
  
        선택 하는 경우는 **HasHeader** 옵션을 열 머리글로 첫 번째 행의 값을 사용할 수 있습니다.  
  
        이 옵션을 열 이름, Col1, Col2, 및 등을 선택 취소 하는 경우 사용 됩니다. 추가 하거나 열을 변경할 수 있습니다 나중에 사용 하 여 이름을 [메타 데이터 편집](./edit-metadata.md)합니다.  
  
    - **TSV**: 여러 열을 만들려면 탭으로 구분 된 텍스트로 붙여 넣거나 필드 간의 탭을 사용 하는 여러 열을 입력 합니다.  
  
        선택 하는 경우는 **HasHeader** 옵션을 열 머리글로 첫 번째 행의 값을 사용할 수 있습니다.  
  
        이 옵션을 열 이름, Col1, Col2, 및 등을 선택 취소 하는 경우 사용 됩니다. 추가 하거나 열을 변경할 수 있습니다 나중에 사용 하 여 이름을 [메타 데이터 편집](./edit-metadata.md)합니다.  
  
    -   **ARFF**:  기존 ARFF 형식 파일에 붙여 넣습니다. 값을 직접 입력 하 고, 데이터의 시작 부분에는 선택적 헤더 및 필수 특성 필드를 추가 해야 합니다. 
    
        예를 들어 다음 헤더 및 특성 행을 간단한 목록에 추가할 수 없습니다. 열 머리글 것 `SampleText`입니다.
    
        ```text
        % Title: SampleText.ARFF  
        % Source: Enter Data module  
        @ATTRIBUTE SampleText STRING  
        @DATA  
        \<type first data row here>  
        ```

    -   **SVMLight**: 입력 하거나 SVMLight 형식을 사용 하 여 붙여 넣습니다.  
  
        예를 들어, 다음 샘플 SVMight 형태로 헌 혈 데이터 집합의 처음 몇 줄을 나타냅니다.  
  
        ```text  
        # features are [Recency], [Frequency], [Monetary], [Time]  
        1 1:2 2:50 3:12500 4:98   
        1 1:0 2:13 3:3250 4:28   
        ```  
  
        실행 하는 경우는 [수동으로 데이터 입력](./enter-data-manually.md) 모듈 이러한 열의 데이터 집합으로 변환 됩니다 선과 같이 값을 인덱스.  
  
        |Col1|Col2|Col3|Col4|레이블|  
        |-|-|-|-|-|  
        |0.00016|0.004|0.999961|0.00784|1|  
        |0|0.004|0.999955|0.008615|1|  
  
4.  각 행에 새 줄을 시작 하려면 ENTER 키를 누릅니다.  
  
     **마지막 행 뒤 enter를 사용 해야 합니다.** 
     
     ENTER 키를 누르면 여러 추가를 여러 번 행 후행 빈, 비어 있는 마지막 행 잘린 제거 되지만 다른 빈 행이 누락 된 값으로 처리 됩니다.  
  
     값이 누락 된 행을 만드는 경우 필터링 할 수 있습니다 항상 해당 나중입니다.  
  
5.  모듈을 마우스 오른쪽 단추로 클릭 **선택 항목 실행** 데이터를 구문 분석 하 고 데이터 집합으로 작업 영역으로 로드 합니다.  
  
     데이터 집합을 보려면 출력 포트를 클릭 하 고 선택 **시각화**합니다.  
## <a name="next-steps"></a>다음 단계

참조를 [사용할 수 있는 모듈 집합](module-reference.md) Azure Machine Learning 서비스입니다. 