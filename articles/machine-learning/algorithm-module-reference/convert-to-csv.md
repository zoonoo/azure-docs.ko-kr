---
title: 'CSV로 변환: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 CSV로 변환 모듈을 사용하여 데이터 집합을 R 또는 Python 스크립트 모듈과 다운로드, 내보내거나 공유할 수 있는 CSV 형식으로 변환하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: fc2a043e8f1565cf5fe45ba0b072ad015076635e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477683"
---
# <a name="convert-to-csv-module"></a>CSV 모듈로 변환

이 문서에서는 Azure 기계 학습 디자이너(미리 보기)의 모듈에 대해 설명합니다.

이 모듈을 사용하여 데이터 집합을 R 또는 Python 스크립트 모듈과 다운로드, 내보내기 또는 공유할 수 있는 CSV 형식으로 변환합니다.

### <a name="more-about-the-csv-format"></a>CSV 형식에 대해 자세히 알아보기 

"쉼표로 구분된 값"을 의미하는 CSV 형식은 많은 외부 기계 학습 도구에서 사용하는 파일 형식입니다. CSV는 R 또는 Python과 같은 오픈 소스 언어로 작업할 때 일반적인 교환 형식입니다.

Azure 기계 학습에서 대부분의 작업을 수행하더라도 데이터 집합을 CSV로 변환하여 외부 도구에서 사용하는 것이 편리할 수 있습니다. 예를 들어:

+ CSV 파일을 다운로드하여 Excel로 열거나 관계형 데이터베이스로 가져옵니다.  
+ CSV 파일을 클라우드 저장소에 저장하고 Power BI에서 연결하여 시각화를 만듭니다.  
+ CSV 형식을 사용하여 R 및 파이썬에서 사용할 데이터를 준비합니다. 

데이터 집합을 CSV로 변환하면 csv가 Azure ML 작업 영역에 저장됩니다. Azure 저장소 유틸리티를 사용하여 파일을 직접 열고 사용할 수 있습니다. CSV로 변환 모듈을 선택하여 디자이너의 **CSV에** 액세스한 다음 오른쪽 패널의 출력 탭 아래에 있는 히스토그램 **아이콘을** 선택하여 출력을 볼 수도 있습니다. 결과 폴더에서 로컬 디렉터리로 CSV를 다운로드할 수 있습니다.  

## <a name="how-to-configure-convert-to-csv"></a>CSV로 변환을 구성하는 방법


1.  파이프라인에 CSV로 변환 모듈을 추가합니다. 이 모듈은 디자이너의 **데이터 변환** 그룹에서 찾을 수 있습니다. 

2. 데이터 집합을 출력하는 모든 모듈에 연결합니다.   
  
3.  파이프라인을 제출합니다.

### <a name="results"></a>결과
  

**CSV로 변환의**오른쪽 패널에서 **출력 탭을** 선택하고 포트 출력 아래에서 이러한 아이콘 중 하나를 **선택합니다.**  

+ **데이터 집합 등록**: 아이콘을 선택하고 CSV 파일을 별도의 데이터 집합으로 Azure ML 작업 영역에 다시 저장합니다. 내 데이터 집합 섹션 아래의 모듈 트리에서 데이터 **집합을** 모듈로 찾을 수 있습니다.

 + **출력 보기**: 눈 아이콘을 선택하고 지시에 따라 **Results_dataset** 폴더를 탐색하고 data.csv 파일을 다운로드합니다.

## <a name="next-steps"></a>다음 단계

Azure 기계 학습에 사용할 수 있는 [모듈 집합을](module-reference.md) 참조하십시오. 