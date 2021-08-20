---
title: Metrics Advisor 클라이언트 라이브러리 REST API
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작을 사용하여 Azure Cognitive Services에서 애플리케이션을 Metrics Advisor API에 연결합니다.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 07/06/2021
ms.author: mbullwin
zone_pivot_groups: programming-languages-metrics-monitor
ms.openlocfilehash: 7f7f9c69de17779f483ce4e4155a8953f9c9da91
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114342454"
---
# <a name="quickstart-use-the-client-libraries-or-rest-apis-to-customize-your-solution"></a>빠른 시작: 클라이언트 라이브러리 또는 REST API를 사용하여 솔루션 사용자 지정

Metrics Advisor REST API 또는 클라이언트 라이브러리를 시작합니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다.

Metrics Advisor를 사용하여 다음을 수행합니다.

* 데이터 원본에서 데이터 피드 추가
* 수집 상태 확인
* 탐지 및 경고 구성 
* 변칙 검색 결과 쿼리
* 변칙 진단


::: zone pivot="programming-language-csharp"

[!INCLUDE [REST API quickstart](../includes/quickstarts/csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [REST API quickstart](../includes/quickstarts/java.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [REST API quickstart](../includes/quickstarts/javascript.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [REST API quickstart](../includes/quickstarts/python.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../../cognitive-services/cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services/cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>다음 단계

- [웹 포털 사용](web-portal.md)
- [데이터 피드 온보딩](../how-tos/onboard-your-data.md)
    - [데이터 피드 관리](../how-tos/manage-data-feeds.md)
    - [여러 데이터 원본에 대한 구성](../data-feeds-from-different-sources.md)
- [메트릭을 구성하고 검색 구성 미세 조정](../how-tos/configure-metrics.md)
- [피드백을 사용하여 변칙 검색 조정](../how-tos/anomaly-feedback.md)