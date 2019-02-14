---
title: Web API 인터페이스 - Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: Web API 인터페이스를 사용하여 KES(Knowledge Exploration Service) API에서 풍부한 의미 체계 검색 환경을 만들 수 있습니다.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 970db5984eedebf98bbb087cfd0b4a35a21a0f54
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55861190"
---
# <a name="web-api-interface"></a>Web API 인터페이스

Knowledge Exploration Service에서 만드는 모델 파일은 Web API 집합을 통해 호스트 및 액세스할 수 있습니다.  이 API는 [`host_service`](CommandLine.md#host_service-command) 명령을 사용하여 로컬 컴퓨터에 호스트하거나, [`deploy_service`](CommandLine.md#deploy_service-command) 명령을 사용하여 Azure 클라우드 서비스에 배포할 수 있습니다.  두 기술 모두 다음과 같은 API 엔드포인트를 노출합니다.

* [*interpret*](interpretMethod.md) – 자연어 쿼리 문자열을 해석합니다. 주석이 추가된 해석을 반환하여 사용자가 입력하는 내용을 예측하는 풍부한 검색 상자 자동 완성 환경을 지원합니다.
* [*evaluate*](evaluateMethod.md) – 구조화된 쿼리 식의 출력을 평가 및 반환합니다.
* [*calchistogram*](calchistogramMethod.md) – 구조화된 쿼리 식에서 반환한 개체의 특성 값 히스토그램을 계산합니다.

함께 사용되는 이러한 API 메서드로 풍부한 의미 체계 검색 환경을 만들 수 있습니다.  자연어 쿼리 문자열이 지정되면 *interpret* 메서드는 기본 문법 및 인덱스 데이터를 기반으로 구조화된 쿼리식을 사용하여 입력 쿼리의 주석이 추가된 버전을 제공합니다.  *evaluate* 메서드는 구조화된 쿼리 식을 계산하고, 일치하는 인덱스 개체를 반환하여 표시합니다.  *calchistogram* 메서드는 필터링 및 구체화가 가능하도록 특성 값 분포를 계산합니다.

**예제**

학술 저서 도메인에서 사용자가 "latent s"라는 문자열을 입력하면 *interpret* 메서드는 사용자에게 키워드 "잠재 의미 체계 분석", 제목 "잠재 구조 분석" 또는 "latent s"로 시작하는 다른 식을 검색하도록 제안하는 순위별 해석 집합을 제공할 수 있습니다.  이 정보는 사용자를 원하는 검색 결과로 신속하게 안내하는 데 사용할 수 있습니다.

이 도메인에서 *evaluate* 메서드는 학술 인덱스에서 일치하는 저서 집합을 검색하는 데 사용할 수 있고, *calchistogram* 메서드는 일치하는 저서의 특성 값 분포를 계산하는 데 사용할 수 있으며, 이러한 값은 검색 결과를 추가로 필터링하고 구체화하는 데 사용할 수 있습니다.

예제의 가독성을 높이기 위해 REST API 호출에는 URL로 인코딩되지 않은 문자(예: 공간)가 포함됩니다. 사용자 코드는 적절한 URL 인코딩을 적용해야 합니다.
