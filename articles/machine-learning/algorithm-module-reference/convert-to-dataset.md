---
title: '데이터 집합으로 변환: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning의 데이터 집합으로 변환 모듈을 사용 하 여 Microsoft Azure Machine Learning에서 사용 하는 내부 데이터 집합 형식으로 데이터 입력을 변환 하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 9b2d003ef4938681229317b625aae4526787ac15
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90898705"
---
# <a name="convert-to-dataset"></a>데이터 세트로 변환

이 문서에서는 Azure Machine Learning 디자이너에서 데이터 집합으로 변환 모듈을 사용 하 여 파이프라인에 대 한 모든 데이터를 디자이너의 내부 형식으로 변환 하는 방법을 설명 합니다.
  
대부분의 경우에는 변환이 필요 하지 않습니다. Azure Machine Learning는 데이터에 대해 작업이 수행 될 때 암시적으로 데이터를 네이티브 데이터 집합 형식으로 변환 합니다. 

데이터 집합에 대해 일종의 정규화 또는 정리를 수행 하 고 다른 파이프라인에서 변경 내용을 사용 하도록 하려면 데이터 집합 형식에 데이터를 저장 하는 것이 좋습니다.  
  
> [!NOTE]
> 데이터 집합으로 변환은 데이터 형식만 변경 합니다. 작업 영역에 데이터의 새 복사본을 저장 하지 않습니다. 데이터 집합을 저장 하려면 출력 포트를 두 번 클릭 하 고 **데이터 집합으로 저장**을 선택한 다음 새 이름을 입력 합니다.  
  
## <a name="how-to-use-convert-to-dataset"></a>데이터 집합으로 변환 사용 방법  

데이터 집합으로 변환을 사용 하기 전에 [메타 데이터 편집](edit-metadata.md) 모듈을 사용 하 여 데이터 집합을 준비 하는 것이 좋습니다. 열 이름을 추가 하거나 변경 하 고, 데이터 형식을 조정 하 고, 필요에 따라 다른 변경 작업을 수행할 수 있습니다.

1.  데이터 집합으로 변환 모듈을 파이프라인에 추가 합니다. 이 모듈은 디자이너의 **데이터 변환** 범주에서 찾을 수 있습니다. 

2. 데이터 집합을 출력 하는 모듈에 연결 합니다.   

    데이터가 [테이블](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true)형식이 면 데이터 집합으로 변환할 수 있습니다. 여기에는 데이터 [가져오기를](import-data.md)통해 로드 된 데이터, [수동으로 입력 데이터](enter-data-manually.md)를 통해 생성 된 데이터 또는 [적용 변환을](apply-transformation.md)통해 변환 된 데이터 집합이 포함 됩니다.

3.  **작업** 드롭다운 목록에서 데이터 집합을 저장 하기 전에 데이터를 정리할 것인지 여부를 지정 합니다.  
  
    - **None**: 데이터를 있는 그대로 사용 합니다.  
  
    - **SetMissingValue**: 데이터 집합에서 특정 값을 누락 된 값으로 설정 합니다. 기본 자리 표시자는 물음표 문자 (?) 이지만  **사용자 지정 누락 값** 옵션을 사용 하 여 다른 값을 입력할 수 있습니다. 예를 들어 **사용자 지정 누락 값**에 **Taxi** 를 입력 하면 데이터 집합에 있는 **Taxi** 의 모든 인스턴스가 누락 된 값으로 변경 됩니다.
  
    - **ReplaceValues**:이 옵션을 사용 하 여 정확 하 게 일치 하는 값으로 바꿀 단일 값을 지정 합니다. **Replace** 메서드를 설정 하 여 누락 값 또는 사용자 지정 값을 바꿀 수 있습니다.

      - **Missing**: 입력 데이터 집합에서 누락 값을 바꾸려면이 옵션을 선택 합니다. **새 값**에 대해 값을 입력 하 여 누락 된 값을로 바꿉니다.
      - **사용자 지정**: 입력 데이터 집합에서 사용자 지정 값을 바꾸려면이 옵션을 선택 합니다. **사용자 지정 값**에 찾으려는 값을 입력 합니다. 예를 들어 데이터에 `obs` 누락 된 값에 대 한 자리 표시자로 사용 되는 문자열이 포함 된 경우을 입력 `obs` 합니다. **새 값**에 새 값을 입력 하 여 원래 문자열을로 바꿉니다.
  
    **ReplaceValues** 작업은 정확히 일치 하는 항목에만 적용 됩니다. 예를 들어, 이러한 문자열에는 영향을 주지 않습니다. `obs.` `obsolete`  
 
  
5.  파이프라인을 제출합니다.  

## <a name="results"></a>결과

+  결과 데이터 집합을 새 이름으로 저장 하려면 모듈의 오른쪽 패널에 있는 **출력** 탭에서 아이콘 **레지스터 데이터 집합** 을 선택 합니다.  
  
## <a name="technical-notes"></a>기술 정보  

-   데이터 집합을 입력으로 사용 하는 모듈은 CSV 파일 또는 TSV 파일에서 데이터를 가져올 수도 있습니다. 모듈 코드를 실행 하기 전에 입력이 전처리 됩니다. 전처리는 입력에 대 한 데이터 집합으로 변환 모듈을 실행 하는 것과 같습니다.  
  
-   SVMLight 형식에서 데이터 집합으로 변환할 수 없습니다.  
  
-   사용자 지정 바꾸기 작업을 지정 하는 경우 검색 및 바꾸기 작업이 전체 값에 적용 됩니다. 부분 일치는 허용 되지 않습니다. 예를 들어 3을-1 또는 33로 바꿀 수 있지만, 3을 35와 같은 두 자리 숫자로 바꿀 수는 없습니다.  
  
-   사용자 지정 바꾸기 작업에서는 열의 현재 데이터 형식을 따르지 않는 문자를 대체 값으로 사용하면 대체가 자동으로 실패합니다.  

  
## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요. 
