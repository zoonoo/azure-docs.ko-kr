---
title: 산업 표준 온톨로지 채택
titleSuffix: Azure Digital Twins
description: Azure Digital Twins에 채택할 수 있는 기존 업계 온톨로지 알아보기
author: baanders
ms.author: baanders
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: c4f003fc9e418501af76281c10277fce3606e24c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102124230"
---
# <a name="adopting-an-industry-ontology"></a>업계 온톨로지 채택

빈 페이지에서 시작하는 것보다 오픈 소스 DTDL 온톨로지로 시작하는 것이 더 쉬울 수 있으므로 Microsoft는 도메인 전문가와 협력하여 널리 승인된 업계 규칙을 나타내며 다양한 고객 사용 사례를 지원하는 온톨로지를 게시합니다. 

그 결과 업계 표준을 빌드, 학습 또는 직접 사용하는 오픈 소스 DTDL 기반 온톨로지 세트가 만들어졌습니다. 온톨로지는 다운스트림 개발자의 요구 사항을 충족하도록 설계되었으며, 업계에서 널리 채택 및/또는 확장될 수 있습니다.

현재 Microsoft는 파트너와 협력하여 [스마트 빌딩](#realestatecore-smart-building-ontology)에 대한 온톨로지 및 [스마트 도시](#smart-cities-ontology)를 위한 온톨로지를 개발했습니다. 이 온톨로지는 재귀를 피하기 위해 이러한 업계 표준에 기초한 모델링을 위한 공통 기반을 제공합니다. 

## <a name="realestatecore-smart-building-ontology"></a>RealEstateCore 스마트 빌딩 온톨로지

*여기서 온톨로지 찾기: [**스마트 빌딩에 대한 Digital Twins 정의 언어 기반 RealEstateCore 온톨로지**](https://github.com/Azure/opendigitaltwins-building)* .

Microsoft는 부동산 소유자, 소프트웨어 공급업체 및 연구 기관의 스웨덴 컨소시엄인 [RealEstateCore](https://www.realestatecore.io/)와 협력하여 이 오픈 소스 DTDL 온톨로지를 부동산 업계에 제공합니다.

이 스마트 빌딩 온톨로지는 업계 표준(예: [BRICK 스키마](https://brickschema.org/ontology/) 또는  [W3C 빌딩 토폴로지 온톨로지)](https://w3c-lbd-cg.github.io/bot/index.html)을 사용하여 스마트 빌딩을 모델링하기 위한 공통 기반을 제공하여 재귀를 방지합니다. 또한 온톨로지에는 사용 및 적절한 확장 방법에 대한 모범 사례가 함께 제공됩니다. 

온톨로지의 구조 및 모델링 규칙, 사용 방법, 확장 방법 및 기여 방법에 대해 자세히 알아보려면 GitHub의 온톨로지 리포지토리인 [Azure/opendigitaltwins-building](https://github.com/Azure/opendigitaltwins-building)을 방문하세요. 

또한 이 블로그 게시물 및 함께 제공되는 비디오: [RealEstateCore, a smart building ontology for digital twins, is now available](https://techcommunity.microsoft.com/t5/internet-of-things/realestatecore-a-smart-building-ontology-for-digital-twins-is/ba-p/1914794)에서 이 이니셔티브를 위한 RealEstateCore와의 파트너십과 목표에 대해 자세히 알아볼 수 있습니다.

## <a name="smart-cities-ontology"></a>스마트 도시 온톨로지

*여기서 온톨로지 찾기: [**스마트 도시를 위한 DTDL(Digital Twins 정의 언어) 온톨로지**](https://github.com/Azure/opendigitaltwins-smartcities)* .

Microsoft는 [ETSI CIM NGSI-LD](https://www.etsi.org/committee/cim)부터 스마트 도시에 DTDL 기반 온톨로지를 제공하기 위해 [OASC(Open Agile Smart Cities)](https://oascities.org/) 및 [Sirus](https://sirus.be/)와 협업하고 있습니다. ETSI NGSI-LD 외에도 Saref4City, CityGML, ISO 등에 대한 평가도 진행했습니다.

온톨로지의 현재 릴리스는 초기 모델 세트에 초점을 맞추고 있습니다. 온톨로지 저자는 여러분이 초기 사용 사례 세트를 확장하는 것은 물론 기존 모델을 개선하는 데 기여해 주시기를 바랍니다. 

온톨로지, 사용 방법 및 기여 방법에 대해 자세히 알아보려면 GitHub에서 온톨로지의 리포지토리인 [Azure/opendigitaltwins-smartcities](https://github.com/Azure/opendigitaltwins-smartcities)를 방문해 보세요. 

또한 이 블로그 게시물 및 관련 동영상: [Smart Cities Ontology for Digital Twins](https://techcommunity.microsoft.com/t5/internet-of-things/smart-cities-ontology-for-digital-twins/ba-p/2166585)에서 스마트 도시를 위한 파트너십 및 접근 방식에 대해 자세히 알아볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 사양에 맞게 업계 표준 온톨로지를 확장하는 것에 대해 자세히 알아봅니다([*개념: 업계 온톨로지 확장*](concepts-ontologies-extend.md)).

* 또는 온톨로지 기반의 모델 개발을 위해 경로를 계속합니다([*모델 개발 경로에서 온톨로지 전략 사용*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path)).