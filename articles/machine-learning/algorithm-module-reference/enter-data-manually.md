---
title: '수동으로 데이터 입력: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure 기계 학습에서 데이터 수동으로 입력 모듈을 사용하여 값을 입력하여 작은 데이터 집합을 만드는 방법을 알아봅니다. 데이터 집합에는 여러 열이 있을 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 35e31e5ace53654e8aad794dd3e25fc04bd9a088
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367519"
---
# <a name="enter-data-manually-module"></a>데이터 수동 모듈 입력

이 문서에서는 Azure 기계 학습 디자이너(미리 보기)의 모듈에 대해 설명합니다.

수동으로 **데이터 입력** 모듈을 사용하여 값을 입력하여 작은 데이터 집합을 만듭니다. 데이터 집합에는 여러 열이 있을 수 있습니다.
  
이 모듈은 다음과 같은 시나리오에서 유용할 수 있습니다.  
  
- 테스트를 위한 작은 값 집합생성.  
- 레이블의 짧은 목록을 만듭니다.  
- 데이터 집합에 삽입할 열 이름 목록을 입력합니다.

## <a name="create-a-dataset"></a>데이터 세트 만들기 
  
1. 파이프라인에 [데이터 수동으로 입력](./enter-data-manually.md) 모듈을 추가합니다. 이 모듈은 Azure 기계 학습의 **데이터 입력 및 출력** 범주에서 찾을 수 있습니다. 
  
1. **DataFormat의**경우 다음 옵션 중 하나를 선택합니다. 이러한 옵션은 제공한 데이터를 구문 분석하는 방법을 결정합니다. 각 형식에 대한 요구 사항은 크게 다르므로 관련 항목을 읽어보시기 합니다.  
  
   - **ARFF**: Weka에서 사용하는 특성 관계 파일 형식입니다.   
   - **CSV**: 쉼표 로 구분된 값 형식입니다. 자세한 내용은 [CSV로 변환](./convert-to-csv.md)을 참조하십시오.    
   - **SVMLight**: Vowpal Wabbit 및 기타 기계 학습 프레임 워크에서 사용하는 형식.    
   - **TSV**: 탭 분리 값 형식입니다.

   형식을 선택하고 형식 사양을 충족하는 데이터를 제공하지 않으면 런타임 오류가 발생합니다.
  
1. **데이터** 텍스트 상자 내부를 클릭하여 데이터 입력을 시작합니다. 다음 형식은 특별한 주의가 필요합니다.  
  
   - **CSV**: 여러 열을 만들려면 쉼표로 구분 된 텍스트에 붙여 넣거나 필드 간에 쉼표를 사용하여 여러 열을 입력합니다.
  
     **HasHeader** 옵션을 선택하면 첫 번째 값 행을 열 제목으로 사용할 수 있습니다.  
  
     이 옵션을 선택 취소하면 열 이름(Col1, Col2 등)이 사용됩니다. [메타데이터 편집을](./edit-metadata.md)사용하여 나중에 열 이름을 추가하거나 변경할 수 있습니다.  
  
   - **TSV**: 여러 열을 만들려면 탭으로 구분된 텍스트에 붙여넣거나 필드 사이의 탭을 사용하여 여러 열을 입력합니다.  
  
     **HasHeader** 옵션을 선택하면 첫 번째 값 행을 열 제목으로 사용할 수 있습니다.  
  
     이 옵션을 선택 취소하면 열 이름(Col1, Col2 등)이 사용됩니다. [메타데이터 편집을](./edit-metadata.md)사용하여 나중에 열 이름을 추가하거나 변경할 수 있습니다.  
  
   - **ARFF**: 기존 ARFF 형식 파일에 붙여넣습니다. 값을 직접 입력하는 경우 데이터 시작 부분에 선택적 헤더와 필수 특성 필드를 추가해야 합니다. 

     예를 들어 다음 헤더 및 특성 행을 간단한 목록에 추가할 수 있습니다. 열 제목은 `SampleText`입니다. 문자열 형식은 지원되지 않습니다.
    
     ```text
     % Title: SampleText.ARFF  
     % Source: Enter Data module  
     @ATTRIBUTE SampleText NUMERIC  
     @DATA  
     \<type first data row here>  
     ```

   - **SVMLight**: SVMLight 형식을 사용하여 값을 입력하거나 붙여넣습니다.  
  
     예를 들어 다음 샘플은 SVMLight 형식으로 혈액 기증 데이터 집합의 처음 몇 줄을 나타냅니다.  
  
     ```text  
     # features are [Recency], [Frequency], [Monetary], [Time]  
     1 1:2 2:50 3:12500 4:98   
     1 1:0 2:13 3:3250 4:28   
     ```  
  
     데이터 수동으로 [입력](./enter-data-manually.md) 모듈을 실행하면 다음과 같이 이러한 줄이 열 및 인덱스 값의 데이터 집합으로 변환됩니다.  
  
     |Col1|Col2|Col3|Col4|레이블|  
     |-|-|-|-|-|  
     |0.00016|0.004|0.999961|0.00784|1|  
     |0|0.004|0.999955|0.008615|1|  
  
1. 각 행 다음에 Enter 키를 선택하여 새 줄을 시작합니다.      
     
   여러 번 Enter를 클릭하여 여러 개의 빈 후행행을 추가하면 빈 행이 제거되거나 잘립니다.  
  
   값이 없는 행을 만드는 경우 나중에 언제든지 필터링할 수 있습니다.  
  
1. 출력 포트를 다른 모듈에 연결하고 파이프라인을 실행합니다.  
  
   데이터 집합을 보려면 모듈을 마우스 오른쪽 단추로 클릭하고 **시각화를**선택합니다.

## <a name="next-steps"></a>다음 단계

Azure 기계 학습에 사용할 수 있는 [모듈 집합을](module-reference.md) 참조하십시오. 