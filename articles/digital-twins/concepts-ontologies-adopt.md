---
title: 업계 표준 온톨로지 도입
titleSuffix: Azure Digital Twins
description: Azure Digital Twins에 대해 채택할 수 있는 기존 업계 온톨로지에 대해 알아보기
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 71795e9dc439d5f634fc4d777aa6b5cdd66e8f5c
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561662"
---
# <a name="adopting-an-industry-ontology"></a>업계 ontology 채택

빈 페이지에서 시작 하는 것 보다 오픈 소스 DTDL ontology 시작 하는 것이 더 쉬울 수 있으므로 Microsoft는 도메인 전문가와 협력 하 여 널리 승인 된 산업 규칙을 나타내며 다양 한 고객 사용 사례를 지원 합니다. 

결과는 업계 표준에서 학습, 빌드, 학습 또는 직접 사용 하는 오픈 소스 DTDL 기반 온톨로지 집합입니다. 온톨로지는 업계에서 널리 채택 하 고/또는 확장할 수 있는 다운스트림 개발자의 요구를 충족 하도록 설계 되었습니다.

현재 Microsoft는 부동산 파트너와 협력 하 여 reinvention을 방지 하기 위해 업계 표준을 기반으로 하는 스마트 건물을 모델링 하는 일반적인 접지를 제공 하는 스마트 건물에 대 한 ontology를 개발 했습니다. 

## <a name="realestatecore-smart-building-ontology"></a>RealEstateCore smart 빌딩 ontology

Microsoft는 부동산 업계를 위한 오픈 소스 DTDL ontology, [**스마트 빌딩 용 dtdl 기반 RealEstateCore ontology**](https://github.com/Azure/opendigitaltwins-building)를 제공 하기 위해 부동산 소유자, 소프트웨어 공급 업체 및 연구 기관의 [RealEstateCore](https://www.realestatecore.io/)와 제휴 했습니다.

이 smart 빌딩 ontology는 업계 표준 (예: [벽돌 스키마](https://brickschema.org/ontology/) 또는 [W3C 빌딩 토폴로지 ontology](https://w3c-lbd-cg.github.io/bot/index.html))을 사용 하 여 reinvention을 방지 하는 스마트 건물 모델링에 대 한 일반적인 접지를 제공 합니다. 또한 ontology는이를 사용 하 고 적절히 확장 하는 방법에 대 한 모범 사례도 제공 합니다. 

Ontology의 구조 및 모델링 규칙, 사용 방법, 확장 방법 및 기여 방법에 대 한 자세한 내용은 [GitHub에서](https://github.com/Azure/opendigitaltwins-building)ontology의 리포지토리를 참조 하세요. 

또한이 블로그 게시물 및 함께 제공 되는 비디오에서이 이니셔티브의 RealEstateCore 및 목표와 파트너 관계에 대 한 자세한 내용을 확인할 수 있습니다. [*RealEstateCore, 스마트 빌딩 ontology for digital twins를 사용할 수*](https://techcommunity.microsoft.com/t5/internet-of-things/realestatecore-a-smart-building-ontology-for-digital-twins-is/ba-p/1914794)있습니다.

## <a name="next-steps"></a>다음 단계

* 산업 표준 온톨로지 확장에 대 한 자세한 내용은 [*개념: 산업 온톨로지 확장*](concepts-ontologies-extend.md)을 준수 하세요.

* 또는 온톨로지을 기반으로 하는 모델을 개발 하는 경로에서 계속 합니다. [*모델 개발 경로에 ontology 전략을 사용*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path)합니다.