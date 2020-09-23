---
title: '수동으로 데이터 입력: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 수동으로 데이터 입력 모듈을 사용 하 여 값을 입력 하 여 작은 데이터 집합을 만드는 방법에 대해 알아봅니다. 데이터 집합에는 여러 개의 열이 있을 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 3bff9a91f06649487560faef3ab554b2a3d56af3
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90908073"
---
# <a name="enter-data-manually-module"></a>수동으로 데이터 입력 모듈

이 문서에서는 Azure Machine Learning 디자이너의 모듈을 설명 합니다.

**데이터 직접 입력** 모듈을 사용 하 여 값을 입력 하 여 작은 데이터 집합을 만듭니다. 데이터 집합에는 여러 개의 열이 있을 수 있습니다.
  
이 모듈은 다음과 같은 시나리오에서 유용할 수 있습니다.  
  
- 테스트를 위해 작은 값 집합을 생성 합니다.  
- 레이블의 짧은 목록을 만듭니다.  
- 데이터 집합에 삽입할 열 이름 목록을 입력 합니다.

## <a name="create-a-dataset"></a>데이터 세트 만들기 
  
1. [수동으로 데이터 입력](./enter-data-manually.md) 모듈을 파이프라인에 추가 합니다. 이 모듈은 Azure Machine Learning의 **데이터 입력 및 출력** 범주에서 찾을 수 있습니다. 
  
1. **DataFormat**의 경우 다음 옵션 중 하나를 선택 합니다. 이러한 옵션은 사용자가 제공 하는 데이터를 구문 분석 해야 하는 방법을 결정 합니다. 각 형식에 대 한 요구 사항은 크게 다르므로 관련 항목을 참조 하십시오.  
  
   - **Arff**: weka에서 사용 되는 특성 관계 파일 형식입니다.   
   - **CSV**: 쉼표로 구분 된 값 형식입니다. 자세한 내용은 [CSV로 변환](./convert-to-csv.md)을 참조 하세요.    
   - **SVMLight**: Vowpal Wabbit 및 기타 기계 학습 프레임 워크에서 사용 하는 형식입니다.    
   - **TSV**: 탭으로 구분 된 값 형식입니다.

   형식을 선택 하 고 형식 사양을 만족 하는 데이터를 제공 하지 않는 경우 런타임 오류가 발생 합니다.
  
1. 데이터 입력을 시작 하려면 **데이터** 텍스트 상자 내부를 클릭 합니다. 다음 형식에는 특별 한 주의가 필요 합니다.  
  
   - **CSV**: 여러 열을 만들려면 쉼표로 구분 된 텍스트로 붙여넣거나 필드 사이에 쉼표를 사용 하 여 여러 열을 입력 합니다.
  
     **Hasheader** 옵션을 선택 하는 경우 값의 첫 번째 행을 열 머리글로 사용할 수 있습니다.  
  
     이 옵션의 선택을 취소 하면 열 이름 (Col1, Col2 등)이 사용 됩니다. 나중에 [메타 데이터 편집](./edit-metadata.md)을 사용 하 여 열 이름을 추가 하거나 변경할 수 있습니다.  
  
   - **TSV**: 여러 열을 만들려면 탭으로 구분 된 텍스트에 붙여넣거나 필드 사이에 탭을 사용 하 여 여러 열을 입력 합니다.  
  
     **Hasheader** 옵션을 선택 하는 경우 값의 첫 번째 행을 열 머리글로 사용할 수 있습니다.  
  
     이 옵션의 선택을 취소 하면 열 이름 (Col1, Col2 등)이 사용 됩니다. 나중에 [메타 데이터 편집](./edit-metadata.md)을 사용 하 여 열 이름을 추가 하거나 변경할 수 있습니다.  
  
   - **Arff**: 기존 arff 서식 파일에 붙여넣습니다. 값을 직접 입력 하는 경우 데이터 시작 부분에 선택적 헤더 및 필수 특성 필드를 추가 해야 합니다. 

     예를 들어 다음 헤더 및 특성 행을 간단한 목록에 추가할 수 있습니다. 열 머리글은 `SampleText` 입니다. 문자열 형식은 지원 되지 않습니다.
    
     ```text
     % Title: SampleText.ARFF  
     % Source: Enter Data module  
     @ATTRIBUTE SampleText NUMERIC  
     @DATA  
     \<type first data row here>  
     ```

   - **SVMLight**: SVMLight 형식을 사용 하 여 값을 입력 하거나 붙여 넣습니다.  
  
     예를 들어 다음 샘플은 SVMLight 형식으로 된 블러드 기부 데이터 집합의 처음 몇 줄을 나타냅니다.  
  
     ```text  
     # features are [Recency], [Frequency], [Monetary], [Time]  
     1 1:2 2:50 3:12500 4:98   
     1 1:0 2:13 3:3250 4:28   
     ```  
  
     [수동으로 데이터 입력](./enter-data-manually.md) 모듈을 실행 하는 경우 이러한 줄은 다음과 같이 열 및 인덱스 값의 데이터 집합으로 변환 됩니다.  
  
     |Col1|Col2|Col3|Col4|레이블|  
     |-|-|-|-|-|  
     |0.00016|0.004|0.999961|0.00784|1|  
     |0|0.004|0.999955|0.008615|1|  
  
1. 각 행 뒤에서 Enter 키를 선택 하 여 새 줄을 시작 합니다.      
     
   여러 번 입력을 여러 번 선택 하 여 빈 후행 행을 여러 개 추가 하는 경우 빈 행이 제거 되거나 잘립니다.  
  
   누락 값이 있는 행을 만드는 경우 나중에 언제 든 지 필터링 할 수 있습니다.  
  
1. 출력 포트를 다른 모듈에 연결 하 고 파이프라인을 실행 합니다.  
  
   데이터 집합을 보려면 모듈을 마우스 오른쪽 단추로 클릭 하 고 **시각화**를 선택 합니다.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요. 