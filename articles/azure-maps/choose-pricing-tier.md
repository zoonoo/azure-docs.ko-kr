---
title: 적합한 가격 계층 선택 | 마이크로소프트 Azure 지도
description: 이 문서에서는 Microsoft Azure 지도에서 제공하는 가격 책정 계층에 대해 알아봅니다.
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: a8bf7ff9bacd4fe84ee5b64d0aed5cb271ce06f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335671"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Azure Maps에서 적절한 가격 책정 계층 선택

Azure Maps는 S0 및 S1의 두 가지 가격 책정 계층을 제공합니다. 이 문서의 목적은 요구 사항에 적합한 가격 책정 계층을 선택하도록 도와주는 것입니다. 올바른 가격 책정 계층을 선택하려면 다음 두 가지 질문을 스스로에게 문의하십시오.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>동시 사용자를 몇 명이나 지원할 계획입니까? 
S0 및 S1 가격 책정 계층이 처리할 수 있는 데이터 처리량은 서로 다릅니다. S0 가격 책정 계층은 **초당 최대 50개의 쿼리**를 처리합니다. S1 계층은 **초당 50개 이상의 쿼리를 처리합니다.**

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>어떤 지리 공간적 기능을 사용할 계획입니까?
핵심 지리 공간 API가 서비스 요구 사항을 충족하는 경우 S0 가격 책정 계층이 적합합니다. 애플리케이션에 대한 보다 고급 기능을 원하는 경우에는 S1 가격 책정 계층을 선택하는 것이 좋습니다. 고급 기능에는 에어리얼 플러스 하이브리드 이미지, 경로 범위 얻기 및 배치 지오코딩이 포함됩니다. 가격 **책정 계층 기능** 테이블을 검토하여 응용 프로그램에 가장 적합한 가격 책정 계층을 선택합니다.

### <a name="pricing-tier-capabilities"></a>가격 책정 계층 기능

| 기능                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| 맵 렌더링                              | ✓                   | ✓       |
| 위성 이미지                       |                     | ✓        |
| 검색                                  | ✓                    | ✓        |
| 일괄 처리 검색                            |                     | ✓        |
| 라우팅                                   | ✓                    |✓        |
| 일괄 라우팅                            |                    | ✓        |
| 행렬 라우팅                          |                     | ✓        |
| 경로 범위 (이소크론)                |                     | ✓        |
| 트래픽                                |✓                    |✓        |
| 표준 시간대                               |✓                    |✓        |
| 위치 정보(미리 보기)                    |✓                   |✓        |
| Spatial Operations                        |                    |✓        |
| 지오펜싱                                |                    |✓        |
| Azure 지도 데이터(미리 보기)                |                     | ✓        |
| 모빌리티(미리 보기)                       |                     | ✓        |
| 날씨(미리보기)                        |✓                    |✓        |

다음 추가 사항을 고려하십시오.
* 어떤 유형의 기업이 있습니까?
* 응용 프로그램은 얼마나 중요합니까?

### <a name="pricing-tier-targeted-customers"></a>가격 책정 계층 대상 고객

S0 및 S1 가격 책정 계층에 대해 자세히 알아보려면 **가격 책정 계층 대상 고객** 표를 참조하세요. 자세한 내용은 [Azure Maps 가격 책정](https://azure.microsoft.com/pricing/details/azure-maps/)을 참조하세요. 

| 가격 책정 계층  |     대상 고객                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>S0 가격 책정 계층은 개념 증명 개발 및 초기 단계 테스트부터 응용 프로그램 프로덕션 및 배포에 이르기까지 프로덕션의 모든 단계에서 응용 프로그램에 적용됩니다. 그러나 이 계층은 소규모 개발 또는 동시 사용자 가 낮은 고객 또는 둘 다용으로 설계되었습니다. <p>|
| S1            |    <p>S1 가격 책정 계층은 대규모 엔터프라이즈 응용 프로그램, 미션 크리티컬 애플리케이션 또는 대량의 동시 사용자를 보유한 고객을 위한 것입니다. 고급 지리 공간 서비스를 필요로 하는 고객에게도 적합합니다.</p>|

## <a name="next-steps"></a>다음 단계

가격 책정 계층을 보고 변경하는 방법에 대해 자세히 알아보기:

> [!div class="nextstepaction"] 
> [가격 책정 계층 관리](how-to-manage-pricing-tier.md)
