---
title: 산업 표준 온톨로지 채택
titleSuffix: Azure Digital Twins
description: Azure Digital Twins에 대해 채택할 수 있는 기존 업계 온톨로지에 대해 알아보기
author: baanders
ms.author: baanders
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: c4f003fc9e418501af76281c10277fce3606e24c
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124230"
---
# <a name="adopting-an-industry-ontology"></a>업계 ontology 채택

빈 페이지에서 시작 하는 것 보다 오픈 소스 DTDL ontology 시작 하는 것이 더 쉬울 수 있으므로 Microsoft는 도메인 전문가와 협력 하 여 널리 승인 된 산업 규칙을 나타내며 다양 한 고객 사용 사례를 지원 합니다. 

결과는 업계 표준에서 학습, 빌드, 학습 또는 직접 사용 하는 오픈 소스 DTDL 기반 온톨로지 집합입니다. 온톨로지는 업계에서 널리 채택 하 고/또는 확장할 수 있는 다운스트림 개발자의 요구를 충족 하도록 설계 되었습니다.

현재 Microsoft는 파트너와 협력 하 여 reinvention을 방지 하기 위해 이러한 업계의 표준을 기반으로 모델링에 대 한 일반적인 접지를 제공 하 [는 스마트 도시의 ontology 및 스마트](#realestatecore-smart-building-ontology) [도시의](#smart-cities-ontology)ontology를 개발 했습니다. 

## <a name="realestatecore-smart-building-ontology"></a>RealEstateCore smart 빌딩 ontology

*[**스마트 건물에 대 한 디지털 Twins 정의 언어 기반 RealEstateCore ontology**](https://github.com/Azure/opendigitaltwins-building)에서 ontology을 찾습니다*.

Microsoft는 부동산 업계를 위한 오픈 소스 DTDL ontology를 제공 하기 위해 부동산 소유자, 소프트웨어 공급 업체 및 연구 기관의 [RealEstateCore](https://www.realestatecore.io/)와 제휴 했습니다.

이 smart 빌딩 ontology는 업계 표준 (예: [벽돌 스키마](https://brickschema.org/ontology/) 또는 [W3C 빌딩 토폴로지 ontology](https://w3c-lbd-cg.github.io/bot/index.html))을 사용 하 여 reinvention을 방지 하는 스마트 건물 모델링에 대 한 일반적인 접지를 제공 합니다. 또한 ontology는이를 사용 하 고 적절히 확장 하는 방법에 대 한 모범 사례도 제공 합니다. 

Ontology의 구조 및 모델링 규칙, 사용 방법, 확장 하는 방법 및 참가 방법에 대 한 자세한 내용은 GitHub에서 ontology의 리포지토리: [Azure/opendigitaltwins-빌드](https://github.com/Azure/opendigitaltwins-building)를 참조 하세요. 

또한이 블로그 게시물 및 함께 제공 되는 비디오에서이 이니셔티브의 RealEstateCore 및 목표와 파트너 관계에 대 한 자세한 내용을 확인할 수 있습니다. [RealEstateCore, 스마트 빌딩 ontology for digital twins를 사용할 수](https://techcommunity.microsoft.com/t5/internet-of-things/realestatecore-a-smart-building-ontology-for-digital-twins-is/ba-p/1914794)있습니다.

## <a name="smart-cities-ontology"></a>스마트 도시 ontology

*[**스마트 도시의 Dtdl (디지털 Twins 정의 언어) ontology**](https://github.com/Azure/opendigitaltwins-smartcities)ontology를 찾습니다*.

Microsoft는 [ETSI CIM NGSI-LD](https://www.etsi.org/committee/cim)부터 스마트 도시에 dtdl 기반 ontology를 제공 하기 위해 [오픈 asc (협력)](https://oascities.org/) 및 [sirus](https://sirus.be/) 를 사용 하 고 있습니다. ETSI NGSI-LD 외에도 Saref4City, CityGML, ISO 및 기타를 평가 했습니다.

Ontology의 현재 릴리스는 초기 모델 집합에 초점을 맞추고 있습니다. Ontology 저자는 초기 사용 사례 집합을 확장 하는 것은 물론 기존 모델을 개선 하는 데 도움이 됩니다. 

Ontology에 대 한 자세한 내용, 사용 방법 및 참가 방법에 대 한 자세한 내용은 GitHub에서 ontology의 리포지토리 ( [Azure/opendigitaltwins-smartcities)](https://github.com/Azure/opendigitaltwins-smartcities)를 참조 하세요. 

이 블로그 게시물 및 관련 동영상: [디지털 쌍에 대 한 스마트 도시 Ontology](https://techcommunity.microsoft.com/t5/internet-of-things/smart-cities-ontology-for-digital-twins/ba-p/2166585)에서 스마트 도시의 파트너 관계 및 접근 방식에 대해 자세히 알아볼 수도 있습니다.

## <a name="next-steps"></a>다음 단계

* 산업 표준 온톨로지 확장에 대 한 자세한 내용은 [*개념: 산업 온톨로지 확장*](concepts-ontologies-extend.md)을 준수 하세요.

* 또는 온톨로지을 기반으로 하는 모델을 개발 하는 경로에서 계속 합니다. [*모델 개발 경로에 ontology 전략을 사용*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path)합니다.