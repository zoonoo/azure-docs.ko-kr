---
title: Custom Decision Service란?
titlesuffix: Azure Cognitive Services
description: 이 문서에서는 Custom Decision Service에 대한 개요를 제공합니다.
services: cognitive-services
author: alekh
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: overview
ms.date: 05/08/2018
ms.author: slivkins
ms.openlocfilehash: 1792e279283608c81c0d4a08b77f9822816ed76d
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55221354"
---
# <a name="what-is-custom-decision-service"></a>Custom Decision Service란?

일반 웹 또는 모바일 애플리케이션에서, 첫 페이지는 여러 문서 또는 다른 형식의 콘텐츠와 연결됩니다. 첫 페이지가 로드될 때 Custom Decision Service를 요청하여 첫 페이지에 포함된 문서의 순위를 지정할 수 있습니다. 따라서 사용자가 문서를 클릭하여 문서를 선택할 때 Custom Decision Service로 두 번째 요청을 보낼 수 있으며, 해당 사용자가 내린 의사 결정의 결과가 기록됩니다.

Custom Decision Service는 콘텐츠에 대한 RSS 피드와 애플리케이션에 추가할 JavaScript 몇 줄만 있으면 되므로 사용하기 쉽습니다.

Custom Decision Service는 콘텐츠를 기계 학습용 기능으로 변환합니다. 시스템에서는 이러한 기능을 사용하여 텍스트, 이미지, 비디오 및 전반적인 정서의 측면에서 콘텐츠를 이해합니다. [Entity Linking](../entitylinking/home.md), [Text Analytics](../text-analytics/overview.md), [Emotion](../emotion/home.md), [Computer Vision](../computer-vision/home.md) 등 여러 다른 [Microsoft Cognitive Services](https://www.microsoft.com/cognitive-services)를 사용합니다.

다음은 Custom Decision Service의 몇 가지 일반적인 사용 사례입니다.

* 뉴스 웹 사이트의 문서 개인 설정
* 미디어 포털의 비디오 콘텐츠 개인 설정
* 광고 위치 또는 광고가 안내하는 웹 페이지 최적화
* 쇼핑 웹 사이트의 순위 추천 항목.

Custom Decision Service는 현재 *공개 미리 보기*입니다. 웹 사이트 또는 앱의 문서 목록을 개인 설정할 수 있습니다. 기능 추출은 영어 콘텐츠에서 가장 잘 작동합니다. 스페인어, 프랑스어, 독일어, 포르투갈어 및 일본어에서는 [기능이 제한](../text-analytics/overview.md)됩니다. 새 기능이 추가되면 이 설명서도 수정됩니다.

Custom Decision Service는 콘텐츠 개인 설정 도메인에 없는 애플리케이션에 사용할 수 있습니다. 이러한 애플리케이션은 사용자 지정 미리 보기에 잘 맞을 수도 있습니다. 자세한 내용을 알아보려면 [문의](https://azure.microsoft.com/overview/sales-number/)하세요.

## <a name="api-usage-modes"></a>API 사용 모드

Custom Decision Service는 웹 페이지 및 모바일 앱에 모두 적용할 수 있습니다. 브라우저 또는 앱에서 API를 호출할 수 있습니다. API 사용 방법은 둘 다 비슷하지만, 일부 세부 정보가 다릅니다.

## <a name="glossary-of-terms"></a>용어집

이 설명서에서 자주 나오는 몇 가지 용어가 있습니다.

* **작업 세트**: Custom Decision Service에서 순위를 지정하는 콘텐츠 항목 세트입니다. 이 집합은 *RSS* 또는 *Atom* 엔드포인트로 지정할 수 있습니다.
* **순위**: ustom Decision Service에 대한 각 요청은 하나 이상의 작업 세트를 지정합니다. 시스템에서는 이러한 집합의 모든 콘텐츠 옵션을 선택하여 응답한 후 지정된 순위대로 반환합니다.
* **콜백 함수**: 사용자가 지정하는 이 함수는 UI의 콘텐츠를 렌더링합니다. 콘텐츠는 Custom Decision Service에서 반환한 순위를 기준으로 순서대로 정렬됩니다.
* **보상**: 사용자가 렌더링된 콘텐츠에 응답하는 방식에 대한 측정값입니다. Custom Decision Service는 클릭을 사용하여 사용자 응답을 측정합니다. 클릭은 애플리케이션에 삽입된 사용자 지정 코드를 사용하여 시스템에 보고됩니다.

## <a name="next-steps"></a>다음 단계

* Custom Decision Service에 [애플리케이션 등록](custom-decision-service-get-started-register.md)
* [웹 페이지](custom-decision-service-get-started-browser.md) 또는 [스마트폰 앱](custom-decision-service-get-started-app.md) 최적화를 시작하세요.
* 제공되는 기능에 대한 자세한 내용은 [API 참조](custom-decision-service-api-reference.md)에서 확인할 수 있습니다.
