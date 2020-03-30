---
title: 지오 AI 사용
titleSuffix: Azure Data Science Virtual Machine
description: 지리적 AI Data Science Virtual Machine을 사용하여 지리 공간적 데이터를 기반으로 데이터를 분석하고 모델을 빌드하는 방법을 알아봅니다.
keywords: 심화 학습, AI, 데이터 과학 도구, 데이터 과학 가상 머신, 지리 공간적 분석
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: 2d7532c51e2963c0dc9f8d02e7a0e32864e80f92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70278426"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>지리적 인공 지능 데이터 과학 가상 머신 사용

지리적 AI 데이터 과학 VM을 사용하여 분석을 위해 데이터를 가져오고, 데이터 랭글링을 수행하고, 지리 공간적 정보를 사용하는 AI 애플리케이션에 대한 모델을 구축합니다. 지리적 AI 데이터 과학 VM을 프로비전하고 ArcGIS 계정을 통해 ArcGIS Pro에 로그인한 후 ArcGIS 데스크톱 및 ArcGIS온라인과 상호 작용을 시작할 수 있습니다. 또한 Python 인터페이스와 지리적 데이터 과학 VM에서 미리 구성된 R 언어 브리지에서 ArcGIS에 액세스할 수 있습니다. 풍부한 AI 애플리케이션을 구축하려면 지오 데이터 사이언스 VM을 기계 학습 및 딥 러닝 프레임워크 및 기타 데이터 과학 소프트웨어와 결합합니다.  


## <a name="configuration-details"></a>구성 세부 정보

ArcGIS와 [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm)인터페이스하는 데 사용되는 파이썬 라이브러리는 에서 ```c:\anaconda```찾을 수 있는 데이터 과학 VM의 전역 루트 콘다 환경에 설치됩니다.

- 명령 프롬프트에서 파이썬을 실행하는 ```activate``` 경우 conda 루트 파이썬 환경으로 활성화하려면 실행하십시오.
- IDE 또는 Jupyter 노트북을 사용하는 경우 환경 또는 커널을 선택하여 올바른 콘다 환경에 있는지 확인할 수 있습니다.

ArcGIS에 대한 R 브리지는 에 ```C:\Program Files\Microsoft\ML Server\R_SERVER```있는 기본 Microsoft 기계 학습 서버 독립 실행형 인스턴스에서 [arcgisbinding이라는](https://github.com/R-ArcGIS/r-bridge) R 라이브러리로 설치됩니다. Visual Studio, RStudio 및 Jupyter는 이미 이 R 환경을 사용하도록 미리 ```arcgisbinding``` 구성되어 있으며 R 라이브러리에 액세스할 수 있습니다. 


## <a name="geo-ai-data-science-vm-samples"></a>지리적 AI 데이터 과학 VM 샘플

기본 데이터 과학 VM의 머신 러닝 및 딥 러닝 프레임워크 기반 샘플 외에도 지리 공간 샘플 세트가 Geo AI 데이터 과학 VM의 일부로 제공됩니다. 이러한 샘플을 통해 지리 공간 데이터와 ArcGIS 소프트웨어를 사용하여 AI 응용 프로그램 개발을 시작할 수 있습니다.


1. [파이썬으로 지리 공간 분석을 시작하기](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): ArcGIS에 파이썬 인터페이스를 통해 지리 공간 데이터로 작업하는 방법을 보여주는 소개 샘플은 [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) 라이브러리에서 제공됩니다. 또한 기존의 기계 학습을 지리 공간 데이터와 결합한 다음 ArcGIS의 맵에서 결과를 시각화하는 방법을 보여 주기도 합니다.

2. [R을 사용하여 지리 공간 분석을 시작하기](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) 라이브러리에서 제공하는 ArcGIS에 R 인터페이스를 사용하여 지리 공간 데이터로 작업하는 방법을 보여 주는 소개 샘플입니다. 

3. [픽셀 수준의 토지 용도 분류](https://github.com/Azure/pixel_level_land_classification): 항공 이미지를 입력으로 사용하고 토지 레이블을 반환하는 심층 신경망 모델을 만드는 방법을 보여 주는 자습서입니다. 토지 표지 라벨의 예로는 삼림과 *물이* *있습니다.* 이 모델은 이미지의 모든 픽셀에 대해 이러한 레이블을 반환합니다. 


## <a name="next-steps"></a>다음 단계

데이터 과학 VM을 사용하는 추가 샘플은 다음에서 사용할 수 있습니다.

* [샘플](dsvm-samples-and-walkthroughs.md)