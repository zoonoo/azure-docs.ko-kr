---
title: 데이터 탐색 및 시각화 도구 - Azure | Microsoft Docs
description: 데이터 과학 Virtual Machine용 데이터 탐색 및 시각화 도구
keywords: 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: vijetaj
ms.openlocfilehash: d60acdf483d418e458f51ef6cf31b17c43b7379f
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70065899"
---
# <a name="data-exploration-and-visualization-tools-on-the-data-science-virtual-machine"></a>데이터 과학 Virtual Machine용 데이터 탐색 및 시각화 도구

데이터 과학에서 키는 데이터를 이해 하는 것입니다. 시각화 및 데이터 탐색 도구는 데이터 이해를 가속화합니다. Data Science Virtual Machine (DSVM)에 제공 되는 다음 도구를 사용 하 여이 키의 단계를 더 쉽게 수행할 수 있습니다.

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| 무엇인가요?   | 빅 데이터에 대 한 오픈 소스 SQL 쿼리 엔진    |
| 지원 되는 DSVM 버전      | Windows, Linux  |
| DSVM에 구성 및 설치 하는 방법      |  `/dsvm/tools/drill*`에 포함 모드 전용으로만 설치됩니다.   |
| 일반적인 사용 용도      |  ETL (추출, 변환, 로드)을 요구 하지 않고 내부 데이터 탐색을 수행 합니다. CSV, JSON, 관계형 테이블 및 Hadoop을 비롯 한 다양 한 데이터 원본 및 형식을 쿼리 합니다.     |
| 사용 및 실행 방법      | 바탕 화면 바로 가기  <br/> [10분 내에 드릴 시작](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| DSVM의 관련 도구      |   Rattle, Weka, SQL Server Management Studio      |

## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| 무엇인가요?   |  데이터 마이닝 작업에 대 한 기계 학습 알고리즘의 컬렉션입니다. 이러한 알고리즘은 데이터 집합에 직접 적용 하거나 사용자 고유의 Java 코드에서 호출할 수 있습니다. Weka에는 데이터 전처리, 분류, 회귀, 클러스터링, 연결 규칙 및 시각화를 위한 도구가 포함 되어 있습니다. |
| 지원되는 DSVM 버전     | Windows, Linux     |
| 일반적인 사용 용도      | 일반 기계 학습 도구     |
| 사용 및 실행 방법      | Windows인 경우 시작 메뉴에서 Weka를 검색하세요. Linux에서 X2Go를 사용 하 여 로그인 한 다음 응용 프로그램 > 개발 > Weka으로 이동 합니다. |
| 샘플에 대한 링크      | [Weka 샘플](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| DSVM의 관련 도구      |LightGBM, Rattle, Xgboost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| 무엇인가요?   |   R을 사용 하는 데이터 마이닝용 GUI (그래픽 사용자 인터페이스)   |
| 지원되는 DSVM 버전     | Windows, Linux     |
| 일반적인 사용 용도      | R에 대한 일반 UI 데이터 마이닝 도구    |
| 사용 및 실행 방법      | UI 도구. Windows에서 명령 프롬프트를 열고 R을 실행 한 다음 R 내에서를 실행 `rattle()`합니다. Linux에서 X2Go를 사용 하 여 연결 하 고, 터미널을 시작 하 고, R을 실행 `rattle()`한 후 r, 실행을 실행 합니다. |
| 샘플에 대한 링크      | [Rattle](https://togaware.com/onepager/) |
| DSVM의 관련 도구      |LightGBM, Weka, Xgboost   |

## <a name="power-bi-desktop"></a>Power BI Desktop 
|    |           |
| ------------- | ------------- |
| 무엇인가요?   | 대화형 데이터 시각화 및 BI 도구    |
| 지원 되는 DSVM 버전      | Windows  |
| 일반적인 사용 용도      |  데이터 시각화 및 대시보드 작성   |
| 사용 및 실행 방법      | 바탕 화면 바로`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`가기 ()      |
| DSVM의 관련 도구      |   Visual Studio 2019, Visual Studio Code, Juno      |

