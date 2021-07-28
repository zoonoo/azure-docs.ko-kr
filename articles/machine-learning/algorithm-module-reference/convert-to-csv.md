---
title: 'CSV로 변환: 모듈 참조'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 디자이너에서 CSV로 변환 모듈을 사용하여 나중에 다시 사용할 수 있는 CSV 파일로 데이터 세트를 변환하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: cc58689e30e9b03e490c0871f3decd50372371fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421926"
---
# <a name="convert-to-csv-module"></a>CSV로 변환 모듈

이 문서에서는 Azure Machine Learning 디자이너의 모듈에 관해 설명합니다.

이 모듈을 사용하여 데이터 세트를 R 또는 Python 스크립트 모듈에서 다운로드, 내보내기 또는 공유할 수 있는 CSV 형식으로 변환할 수 있습니다.

### <a name="more-about-the-csv-format"></a>CSV 형식에 대한 자세한 정보 

‘쉼표로 구분된 값’을 의미하는 CSV 형식은 많은 외부 기계 학습 도구에서 사용되는 파일 형식입니다. CSV는 R 또는 Python과 같은 오픈 소스 언어 작업의 일반적인 교환 형식입니다.

Azure Machine Learning에서 대부분의 작업을 수행하더라도 외부 도구에서 사용할 수 있도록 데이터 세트를 CSV로 변환하는 것이 편리한 경우가 있습니다. 예를 들면 다음과 같습니다.

+ CSV 파일을 다운로드하여 Excel에서 열거나 관계형 데이터베이스로 가져옵니다.  
+ CSV 파일을 클라우드 스토리지에 저장하고 Power BI에서 연결하여 시각화를 만듭니다.  
+ CSV 형식을 사용하여 R 및 Python에서 사용할 데이터를 준비합니다. 

데이터 세트를 CSV로 변환하면 CSV가 Azure ML 작업 영역에 저장됩니다. Azure 스토리지 유틸리티를 사용하여 파일을 직접 열고 사용할 수 있습니다. **CSV로 변환** 모듈을 선택하여 디자이너에서 CSV에 액세스한 다음, 오른쪽 패널의 **출력** 탭에서 히스토그램 아이콘을 선택하여 출력을 볼 수도 있습니다. CSV를 결과 폴더에서 로컬 디렉터리로 다운로드할 수 있습니다.  

## <a name="how-to-configure-convert-to-csv"></a>CSV로 변환을 구성하는 방법


1.  CSV로 변환 모듈을 파이프라인에 추가합니다. 이 모듈은 디자이너의 **데이터 변환** 그룹에서 찾을 수 있습니다. 

2. 이를 데이터 세트를 출력하는 모듈에 연결합니다.   
  
3.  파이프라인을 제출합니다.

### <a name="results"></a>결과
  

**CSV로 변환** 의 오른쪽 패널에서 **출력** 탭을 선택하고 **포트 출력** 아래에서 다음 아이콘 중 하나를 선택합니다.  

+ **데이터 세트 등록**: 아이콘을 선택하고 CSV 파일을 별도의 데이터 세트로 Azure ML 작업 영역에 다시 저장합니다. **내 데이터 세트** 섹션 아래의 모듈 트리에서 데이터 세트를 모듈로 찾을 수 있습니다.

 + **출력 보기**: 눈 모양 아이콘을 선택하고 지침에 따라 **Results_dataset** 폴더를 찾아 data.csv 파일을 다운로드합니다.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning에서 [사용 가능한 모듈 세트](module-reference.md)를 참조하세요. 