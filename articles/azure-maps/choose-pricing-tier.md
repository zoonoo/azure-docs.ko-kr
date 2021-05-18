---
title: Microsoft Azure Maps에 적절한 가격 책정 계층 선택
description: Azure Maps 가격 책정 계층에 대해 자세히 알아봅니다. 어떤 기능을 어떤 계층에서 제공하는지 확인하고 가격 책정 계층을 선택하기 위한 주요 고려 사항을 확인합니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: cb57e424642141e73588a61d026d1ff2f1a8b096
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96905301"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Azure Maps에서 적절한 가격 책정 계층 선택

Azure Maps는 S0 및 S1이라는 두 가지 가격 책정 계층을 제공합니다. 이 문서의 목적은 요구 사항에 적합한 가격 책정 계층을 선택하도록 도와주는 것입니다. 적절한 가격 책정 계층을 선택하려면 다음 두 질문에 대답합니다.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>동시 사용자를 몇 명이나 지원할 계획입니까?

S0 및 S1 가격 책정 계층이 처리할 수 있는 데이터 처리량은 서로 다릅니다. S0 가격 책정 계층은 **초당 최대 50개의 쿼리** 를 처리합니다. 반면 S1 가격 책정 계층은 **초당 50개 이상의 쿼리** 를 처리합니다.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>어떤 지리 공간적 기능을 사용할 계획입니까?

핵심 지리 공간적 API가 서비스 요구 사항을 충족하는 경우 S0 가격 책정 계층을 선택합니다. 애플리케이션에 대한 보다 고급 기능을 원하는 경우에는 S1 가격 책정 계층을 선택하는 것이 좋습니다. 고급 기능에는 지면 및 하이브리드 이미지, 경로 범위 가져오기 및 일괄 지오코딩이 포함됩니다. 애플리케이션에 가장 적합한 가격 책정 계층을 선택하려면 아래의 **가격 책정 계층 기능** 표를 검토합니다.

### <a name="pricing-tier-capabilities"></a>가격 책정 계층 기능

| 기능                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| 지도 렌더링                              | ✓                   | ✓       |
| 위성 이미지                       |                     | ✓        |
| 검색                                  | ✓                    | ✓        |
| 일괄 검색                            |                     | ✓        |
| 경로                                   | ✓                    |✓        |
| 일괄 라우팅                            |                    | ✓        |
| 행렬 라우팅                          |                     | ✓        |
| 경로 범위(등시성)                |                     | ✓        |
| 트래픽                                |✓                    |✓        |
| 표준 시간대                               |✓                    |✓        |
| 지리적 위치(미리 보기)                    |✓                   |✓        |
| Spatial Operations                        |                    |✓        |
| 지오펜싱                                |                    |✓        |
| Azure Maps 데이터(미리 보기)                |                     | ✓        |
| 이동성(미리 보기)                       |                     | ✓        |
| 날씨(미리 보기)                        |✓                    |✓        |
|  작성자(미리 보기)                         |                   |✓        |
|  권한 상승(미리 보기)                        |                   |✓        |

다음과 같은 추가 사항을 고려하세요.

* 어떤 유형의 엔터프라이즈가 있나요?
* 애플리케이션의 중요도는 어떻게 되나요?

### <a name="pricing-tier-targeted-customers"></a>가격 책정 계층 대상 고객

S0 및 S1 가격 책정 계층에 대해 자세히 알아보려면 **가격 책정 계층 대상 고객** 표를 참조하세요. 자세한 내용은 [Azure Maps 가격 책정](https://azure.microsoft.com/pricing/details/azure-maps/)을 참조하세요. 

| 가격 책정 계층  |     대상 고객                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    S0 가격 책정 계층은 개념 증명 개발 및 초기 단계 테스트부터 애플리케이션 프로덕션 및 배포에 이르는 모든 프로덕션 단계에 적용됩니다. 그러나 이 계층은 소규모 개발이나 동시 사용자가 적은 고객 또는 둘 다를 위해 설계되었습니다. 
| S1            |    S1 가격 책정 계층은 대규모 엔터프라이즈 애플리케이션, 중요 업무용 애플리케이션 또는 많은 동시 사용자가 있는 고객을 위한 것입니다. 고급 지리 공간 서비스를 필요로 하는 고객에게도 적합합니다.

## <a name="next-steps"></a>다음 단계

가격 책정 계층을 보고 변경하는 방법에 대해 자세히 알아보기:

> [!div class="nextstepaction"]
> [가격 책정 계층 관리](how-to-manage-pricing-tier.md)
