---
title: 데이터 엔터티 | Microsoft Docs
description: 데이터 엔터티의 개요입니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: c7b321ab04df405c56cab0952942b0d6e142da6d
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48807675"
---
# <a name="data-entities"></a>데이터 엔터티

이 문서에서는 데이터 엔터티의 개요를 정의하고 제공합니다. 여기에는 데이터 엔터티의 기능, 지원하는 시나리오, 사용되는 범주 및 만드는 방법에 대한 정보가 포함됩니다.

## <a name="overview"></a>개요

데이터 엔터티는 데이터베이스 테이블의 물리적 구현에서 생성된 추상화입니다. 예를 들어, 정규화된 테이블에서 각 고객에 대한 데이터는 대부분 고객 테이블에 저장되고 나머지 데이터는 작은 관련 테이블 집합에 분산될 수 있습니다. 이 경우 고객 개념에 대한 데이터 엔터티는 하나의 비정규화된 뷰로 표시되며, 각 행에 고객 테이블 및 관련 테이블의 모든 데이터가 포함됩니다. 데이터 엔터티는 개발 및 통합을 용이하게 하는 형식으로 비즈니스 개념을 캡슐화합니다. 데이터 엔터티의 추상화된 특성 때문에 응용 프로그램 개발 및 사용자 지정을 간소화할 수 있습니다. 나중에, 추상화는 버전 간 물리적 테이블의 필연적인 변동에서 응용 프로그램 코드를 보호합니다.

요약하면, 데이터 엔터티는 주요 데이터 개념 및 기능을 나타내기 위해 기본 테이블 스키마의 개념적 추상화 및 캡슐화(비정규화된 뷰)를 제공합니다.

## <a name="capabilities"></a>기능

데이터 엔터티의 기능은 다음과 같습니다.

- AXD, DIXF(데이터 가져오기/내보내기 프레임워크) 엔터티 및 집계 쿼리의 다양하고 조각화된 개념을 단일 개념으로 바꿉니다.
- 비즈니스 논리를 캡처하고 가져오기/내보내기, 통합 및 프로그래밍 기능과 같은 시나리오를 가능하게 하는 단일 스택을 제공합니다.
- ALM(애플리케이션 수명 주기 관리) 및 데모 데이터 시나리오를 위한 기본 데이터 패키지 내보내기/가져오기 메커니즘으로 사용됩니다.
- OData 서비스로 노출한 다음, 테이블 형식 동기 통합 시나리오 및 Microsoft Office 통합에 사용할 수 있습니다.

자세한 내용은 [데이터 엔터티](https://docs.microsoft.com/dynamics365/operations/dev-itpro/data-entities/data-entities)를 참조하세요.
