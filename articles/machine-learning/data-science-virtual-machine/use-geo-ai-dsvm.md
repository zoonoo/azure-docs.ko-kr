---
title: 지역 AI 사용
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
ms.openlocfilehash: 01238885e68a39ff713bd346a9206668e16e7ee4
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195289"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>지리적 인공 지능 데이터 과학 가상 머신 사용

지리적 AI 데이터 과학 VM을 사용하여 분석을 위해 데이터를 가져오고, 데이터 랭글링을 수행하고, 지리 공간적 정보를 사용하는 AI 애플리케이션에 대한 모델을 구축합니다. 지역 AI Data Science VM 프로 비전 하 고 ArcGIS 계정을 통해 ArcGIS Pro에 로그인 한 후 ArcGIS desktop 및 ArcGIs online과의 상호 작용을 시작할 수 있습니다. 또한 Python 인터페이스 및 지역 Data Science VM에 미리 구성 된 R 언어 브리지에서 ArcGIS에 액세스할 수 있습니다. 다양 한 AI 응용 프로그램을 빌드하려면 지역 Data Science VM를 기계 학습 및 심층 학습 프레임 워크와 함께 사용할 수 있는 기타 데이터 과학 소프트웨어와 결합 합니다.  


## <a name="configuration-details"></a>구성 세부 정보

ArcGIS와의 인터페이스에 사용 되는 Python 라이브러리 [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm)는에 ```c:\anaconda```있는 Data Science VM의 전역 루트 conda 환경에 설치 됩니다.

- 명령 프롬프트에서 Python을 실행 하는 경우를 실행 ```activate``` 하 여 conda 루트 Python 환경으로 정품 인증 합니다.
- IDE 또는 Jupyter 노트북을 사용 하는 경우 환경이 나 커널을 선택 하 여 올바른 conda 환경에 있는지 확인할 수 있습니다.

ArcGIS에 대 한 R 브리지는에 ```C:\Program Files\Microsoft\ML Server\R_SERVER```있는 주 Microsoft Machine Learning Server 독립 실행형 인스턴스에서 [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) 라는 r 라이브러리로 설치 됩니다. Visual Studio, rstudio 및 jupyter는 이미이 r 환경을 사용 하도록 미리 구성 되어 있으며 ```arcgisbinding``` r 라이브러리에 액세스할 수 있습니다. 


## <a name="geo-ai-data-science-vm-samples"></a>지리적 AI 데이터 과학 VM 샘플

기본 Data Science VM의 기계 학습 및 심층 학습 프레임 워크 기반 샘플 외에도 지리 AI 샘플 집합은 지역 AI Data Science VM의 일부로 제공 됩니다. 이러한 샘플을 통해 지리 공간적 데이터와 ArcGIS 소프트웨어를 사용 하 여 AI 응용 프로그램 개발을 바로 시작할 수 있습니다.


1. [Python을 사용 하 여 지리 공간적 분석 시작](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): ArcGIS에 대 한 Python 인터페이스를 통해 지리 공간적 데이터를 사용 하는 방법을 보여 주는 소개 샘플은 [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) 라이브러리에서 제공 합니다. 또한 기존 기계 학습을 지리 공간적 데이터와 결합 하 고 ArcGIS의 지도에 대 한 결과를 시각화 하는 방법을 보여 줍니다.

2. [R을 사용 하 여 지리 공간적 분석 시작](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): [Arcgisbinding](https://github.com/R-ArcGIS/r-bridge) 라이브러리에서 제공 하는 ArcGIS에 대 한 R 인터페이스를 사용 하 여 지리 공간적 데이터로 작업 하는 방법을 보여 주는 소개 샘플입니다. 

3. [픽셀 수준의 토지 용도 분류](https://github.com/Azure/pixel_level_land_classification): 항공 이미지를 입력으로 사용하고 토지 레이블을 반환하는 심층 신경망 모델을 만드는 방법을 보여 주는 자습서입니다. 땅 표지 레이블의 예는 *삼림* 및 *물*입니다. 이 모델은 이미지의 모든 픽셀에 대해 이러한 레이블을 반환합니다. 


## <a name="next-steps"></a>다음 단계

데이터 과학 VM을 사용하는 추가 샘플은 다음에서 사용할 수 있습니다.

* [샘플](dsvm-samples-and-walkthroughs.md)

