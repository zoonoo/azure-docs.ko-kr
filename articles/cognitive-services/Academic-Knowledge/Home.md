---
title: Academic Knowledge API란?
titlesuffix: Azure Cognitive Services
description: Academic Knowledge API를 사용하여 사용자 쿼리를 해석하고 Academic Graph에서 다양한 정보를 검색합니다.
services: cognitive-services
author: darrine
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: overview
ms.date: 10/30/2018
ms.author: darrine
ms.openlocfilehash: 02675b8cdc8c509764b20893bde54b50bec74a50
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51242149"
---
# <a name="academic-knowledge-api"></a>Academic Knowledge API

Academic Knowledge API에 오신 것을 환영합니다. 이 서비스를 사용하면 교육 목적에 대한 사용자 쿼리를 해석하고 MAG(Microsoft Academic Graph)에서 다양한 정보를 검색할 수 있습니다. MAG 정보 자료는 연구 분야, 작성자, 기관, 논문, 장소 및 이벤트 등의 학술 활동을 모델링하는 엔터티로 구성된 다른 유형의 웹 규모 엔터티 그래프입니다. 

MAG 데이터는 Bing에서 한 사내 기술 자료와 Bing 웹 인덱스에서 마이닝됩니다. 지속적인 Bing 인덱싱의 결과로 이 API에는 Bing의 인덱싱 및 검색을 수행하여 웹의 새로운 정보가 포함됩니다. 이 데이터 세트에 따라 Academic Knowledge API를 사용하면 반응형 검색을 능동적 제안 환경, 풍부한 연구 논문 그래프 검색 결과와 문서 집합 및 관련 엔터티에 대한 특성 값의 히스토그램 배포와 원활하게 결합하는 지식 기반의 대화형 대화를 할 수 있습니다.

Microsoft Academic Graph에 대한 자세한 내용은 [http://aka.ms/academicgraph](https://aka.ms/academicgraph)을 참조합니다.

Academic Knowledge API는 Cognitive Services 미리 보기에서 Cognitive Services 랩으로 이동했습니다. 프로젝트의 새 홈 페이지는 [https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge](https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge)입니다. 기존 API 키는 2018년 5월 24일까지만 서비스됩니다. 그 후에는 새 API 키를 생성하세요. 유료 미리 보기는 기존 키가 만료되면 더 이상 사용할 수 없으니 유의하십시오. API의 무료 계층이 사용자 용도에 적합하지 않은 경우 당사 팀에 문의하십시오. 

## <a name="features"></a>기능
Academic Knowledge API는 관련 REST 엔드포인트 4개로 이뤄졌습니다.  
  1. **해석** – 자연어 사용자 쿼리 문자열을 해석합니다. 주석이 추가된 해석을 반환하여 사용자가 입력하는 내용을 예측하는 풍부한 검색 상자 자동 완성 환경을 지원합니다.  
  2. **평가** – 쿼리 식을 평가하고 Academic Knowledge 엔터티 결과를 반환합니다.  
  3. **calchistogram** – 쿼리 식에서 반환한 교육 기관의 특성 값 분포에 대한 히스토그램을 계산합니다(예: 지정된 저자의 연도별 인용 수 분포).  
  
함께 사용되는 이러한 API 메서드는 사용자에게 풍부한 의미 체계 검색 환경을 만들 수 있게 해줍니다. 사용자 쿼리 문자열을 고려해 볼 때 **해석** 메서드는 주석이 포함된 쿼리 버전 및 구조화된 쿼리 식을 제공하는 반면 기본 학술 데이터의 의미 체계에 따라 사용자의 쿼리를 선택적으로 완료합니다. 예를 들어 사용자가 문자열 *latent s*를 입력하면 **해석** 메서드는 해당 사용자가 *잠재 의미 체계 분석* 연구 분야, *잠재 구조 분석* 논문 또는 *latent s*로 시작하는 다른 엔터티 식을 검색하도록 제안하면서 순위별 해석 집합을 제공할 수 있습니다. 이 정보는 사용자를 원하는 검색 결과로 신속하게 안내하는 데 사용할 수 있습니다.

**평가** 메서드는 학술 정보 자료에서 논문 엔터티와 일치하는 집합을 검색하는 데 사용할 수 있으며 **calchistogram** 메서드는 더 많은 검색 결과를 필터링하는 데 사용되는 논문 엔터티 집합에 대한 특성 값의 분포를 계산하는 데 사용할 수 있습니다.        

## <a name="getting-started"></a>시작하기 
자세한 설명은 왼쪽에 있는 하위 항목을 참조하세요.  예제의 가독성을 높이기 위해 REST API 호출에는 URL로 인코딩되지 않은 문자(예: 공간)가 포함됩니다.  사용자 코드는 적절한 URL 인코딩을 적용해야 합니다.
