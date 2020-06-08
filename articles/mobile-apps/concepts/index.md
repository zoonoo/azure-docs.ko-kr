---
title: Visual Studio App Center 및 Azure 서비스를 사용하여 모바일 애플리케이션 빌드
description: 다른 Azure 서비스와 함께 실제 모바일 애플리케이션을 빌드할 수 있는 Visual Studio App Center와 같은 서비스를 사용하는 방법을 알아봅니다.
author: codemillmatt
ms.assetid: 98899889-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/05/2020
ms.author: masoucou
ms.openlocfilehash: ffbc5fa47dccb50c201c26ac830197c4ff585147
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2020
ms.locfileid: "84482457"
---
# <a name="mobile-application-development-in-microsoft"></a>Microsoft의 모바일 애플리케이션 개발
의사 결정자, 개발 팀 및 IT 부서는 다음과 같은 견고한 모바일 애플리케이션 개발 전략을 수립해야 합니다.
- 비즈니스 목표와 일치하는 애플리케이션 유형을 대상으로 합니다(예: B2E(Business to Enterprise), B2B(Business to Business) 또는 B2C(Business to Consumer)).
- 단일 플랫폼 언어를 사용하여 iOS, Android 또는 Windows용 애플리케이션에 투자하여 빌드할지, 아니면 플랫폼 간 접근 방식을 사용할지를 결정합니다.
- 크기를 조정할 수 있는 백 엔드 서비스를 제공합니다.
- 온라인 또는 오프라인 상태와 관계없이 백 엔드 데이터와 원활하게 통합합니다.
- 인증 및 디바이스 관리 기능을 통해 보안 애플리케이션을 빌드합니다.
- 지속적으로 빠르게 제공합니다.
- 클라우드 또는 온-프레미스에서 백 엔드 시스템에 연결합니다.
- 기존 도구 및 프로세스와 통합합니다.
- 비즈니스와 함께 애플리케이션을 발전시킵니다.
- 출시 시간을 줄이고, 개발자가 고품질 애플리케이션 빌드에 집중할 수 있도록 합니다.

이 설명서 시리즈는 안전하고, 안정적이며, 확장 가능한 클라우드 기반 모바일 애플리케이션을 빌드하는 데 사용할 수 있는 Microsoft 제공 서비스에 대해 설명합니다.

**대상 그룹**

이 설명서 시리즈는 모바일 애플리케이션을 빌드하는 데 익숙한 전문 개발자이거나 그렇지 않은 미숙련 개발자로서 애플리케이션을 성공적으로 개발하는 데 도움이 되는 클라우드 서비스에 대해 알아보려는 경우에 유용합니다. 알고 있어야 하는 모든 Microsoft 서비스, 도구 및 기술에 대한 간략한 개요를 확인할 수 있습니다. 이 설명서에는 코드 샘플 또는 데모가 추가되어 있지 않습니다. 이는 애플리케이션 개발 프로세스 및 관련 서비스에 대한 개략적인 개요를 제공하기 위한 것입니다. 각 문서에 대한 자세한 내용은 다른 리소스에 연결되어 있습니다. 시리즈의 각 부분을 개별적으로 참조하여 관심 있는 문서만 선택할 수 있습니다.
