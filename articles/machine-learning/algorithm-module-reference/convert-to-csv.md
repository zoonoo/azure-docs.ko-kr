---
title: CSV로 변환 합니다. 모듈 참조
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 서비스에서 CSV 모듈에 Convert를 사용 하 여 데이터 집합 다운로드, 내보낸 하거나 R 또는 Python 스크립트 모듈을 사용 하 여 공유할 수 있는 CSV 형식으로 변환 하는 방법에 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 8b8b6758cc2df7a092ce36e9507f84ac534d0e3d
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028727"
---
# <a name="convert-to-csv-module"></a>CSV 모듈 변환

이 문서에서는 Azure Machine Learning 서비스에 대 한 시각적 인터페이스 (미리 보기)의 모듈을 설명 합니다.

이 모듈을 사용 하 여 데이터 집합 다운로드, 내보낸 또는 R 또는 Python 스크립트 모듈을 사용 하 여 공유할 수 있는 CSV 형식으로 변환 합니다.

### <a name="more-about-the-csv-format"></a>CSV 형식에 대 한 자세한 정보 

"쉼표로 구분 된 값"의 약자인 CSV 형식으로는 많은 외부 기계 학습 도구에서 사용 하는 파일 형식입니다. CSV 일반 교환 형식인 경우 R 또는 Python과 같은 오픈 소스 언어를 사용 합니다.

대부분의 Azure Machine Learning에서 작업을 수행한 경우에 경우 것이 편리 하 게 데이터 집합에서 외부 도구를 사용 하는 CSV로 변환 하는 경우가 있습니다. 예를 들면 다음과 같습니다.

+ Excel을 사용 하 여 열을 CSV 파일을 다운로드 하거나 관계형 데이터베이스로 가져옵니다.  
+ 시각화를 만들려면 Power BI에서 연결 하 고 클라우드 저장소를 CSV 파일을 저장 합니다.  
+ CSV 형식을 사용 하 여 R 및 Python에 사용할 데이터를 준비 합니다. Python 또는 Jupyter notebook에서 직접 데이터에 액세스 하는 데 필요한 코드를 생성 하는 모듈의 출력 오른쪽 단추로 클릭 합니다. 

데이터 집합을 CSV로 변환 하면 파일은 Azure ML 작업 영역에 저장 됩니다. Azure storage 유틸리티를 사용 하 여 열고 파일을 직접 사용 하거나 모듈 출력을 마우스 오른쪽 단추로 클릭 하 고 CSV 파일을 컴퓨터에 다운로드 하거나 R 또는 Python 코드에서 사용할 수 있습니다.  

## <a name="how-to-configure-convert-to-csv"></a>CSV로 변환을 구성 하는 방법

1.  추가 된 [CSV로 변환](./convert-to-csv.md) 모듈을 실험 합니다. 이 모듈에서 찾을 수 있습니다 합니다 **데이터 형식 변환** 인터페이스의 그룹입니다. 

2. 데이터 집합을 출력 하는 모든 모듈에 연결 합니다.   
  
3.  실험을 실행합니다.

### <a name="results"></a>결과
  

출력을 두 번 클릭 [CSV로 변환](./convert-to-csv.md), 이러한 옵션 중 하나를 선택 합니다.  

 + **결과 데이터 집합 다운로드->**: 로컬 폴더에 저장할 수 있는 CSV 형식으로 데이터의 복사본을 엽니다. 폴더를 지정 하지 않으면 기본 파일 이름을 적용 되 고 CSV 파일을 로컬에 저장 됩니다 **다운로드** 라이브러리입니다.


 + **결과 데이터 집합->는 데이터 집합으로 저장**: CSV 파일을 별도 데이터 집합으로 Azure ML 작업 영역에 다시 저장합니다.

 + **데이터 액세스 코드 생성**: Azure ML Python을 사용 하거나 R을 사용 하 여 데이터를 액세스 하기 위한 코드의 두 집합 생성 데이터에 액세스 하려면 응용 프로그램에 코드 조각을 복사 합니다. (*데이터 액세스 코드 생성 곧 제공 될 예정입니다.* )

## <a name="next-steps"></a>다음 단계

참조를 [사용할 수 있는 모듈 집합](module-reference.md) Azure Machine Learning 서비스입니다. 