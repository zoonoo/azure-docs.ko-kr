---
title: Microsoft Azure의 안전한 개발 모범 사례
description: 보다 안전한 코드를 개발하고 클라우드에 보다 안전한 응용 프로그램을 배포하는 데 도움이 되는 모범 사례입니다.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: c4314a0dcbbcb907ef4d6de0a2788cf04dfe1641
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68934859"
---
# <a name="secure-development-best-practices-on-azure"></a>Azure에서 안전한 개발 모범 사례
이 문서 시리즈는 클라우드용 응용 프로그램을 개발할 때 고려해야 할 보안 활동 및 컨트롤을 제공합니다. SDL(Microsoft 보안 개발 수명 주기) 단계와 수명 주기의 각 단계에서 고려해야 할 보안 질문 및 개념에 대해 다룹니다. 목표는 수명 주기의 각 단계에서 보다 안전한 응용 프로그램을 설계, 개발 및 배포하는 데 사용할 수 있는 활동 및 Azure 서비스를 정의하는 데 도움을 주는 것입니다.

이 문서의 권장 사항은 Azure 보안에 대한 경험과 고객의 경험에서 비롯됩니다. 이러한 문서는 개발 프로젝트의 특정 단계에서 고려해야 할 사항에 대한 참조로 사용할 수 있지만 처음부터 끝까지 한 번 이상 모든 문서를 읽는 것이 좋습니다. 모든 기사를 읽으면 프로젝트의 초기 단계에서 놓쳤을 수 있는 개념을 소개합니다. 제품을 릴리스하기 전에 이러한 개념을 구현하면 보안 소프트웨어를 구축하고 보안 규정 준수 요구 사항을 해결하며 개발 비용을 절감할 수 있습니다.

이 문서는 보안 Azure 응용 프로그램을 빌드하고 배포하는 모든 수준의 소프트웨어 디자이너, 개발자 및 테스터를 위한 리소스입니다.

## <a name="overview"></a>개요

보안은 모든 응용 프로그램의 가장 중요한 측면 중 하나이며 올바른 것을 얻는 것은 간단하지 않습니다. 다행히 Azure는 클라우드에서 응용 프로그램을 보호하는 데 도움이 되는 많은 서비스를 제공합니다. 이 문서에서는 소프트웨어 개발 수명 주기의 각 단계에서 구현할 수 있는 활동 및 Azure 서비스를 다루며 보다 안전한 코드를 개발하고 클라우드에 보다 안전한 응용 프로그램을 배포하는 데 도움을 줍니다.

## <a name="security-development-lifecycle"></a>보안 개발 수명 주기

보안 소프트웨어 개발을 위한 모범 사례를 따르려면 프로젝트[방법론(폭포,](https://en.wikipedia.org/wiki/Waterfall_model) [민첩성](https://en.wikipedia.org/wiki/Agile_software_development)또는 [DevOps)에](https://en.wikipedia.org/wiki/DevOps)관계없이 요구 사항 분석에서 유지 관리에 이르기까지 소프트웨어 개발 수명 주기의 각 단계에 보안을 통합해야 합니다. 중요한 데이터 유출과 운영 보안 결함의 악용으로 인해 더 많은 개발자가 개발 프로세스 전반에 걸쳐 보안을 해결해야 한다는 것을 이해하고 있습니다.

나중에 개발 수명 주기에서 문제를 해결할수록 더 많은 비용이 발생합니다. 보안 문제도 예외는 아닙니다. 소프트웨어 개발 초기 단계에서 보안 문제를 무시하면 다음 단계의 각 단계가 이전 단계의 취약점을 상속받을 수 있습니다. 최종 제품에는 여러 보안 문제와 위반 가능성이 누적됩니다. 개발 수명 주기의 각 단계에 보안을 구축하면 문제를 조기에 파악할 수 있으며 개발 비용을 절감할 수 있습니다.

[Microsoft 보안 개발 수명 주기(SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) 단계를 수행하여 수명 주기의 각 단계에서 안전한 소프트웨어 개발 방법을 수행하는 데 사용할 수 있는 활동 및 Azure 서비스를 소개합니다.

SDL 단계는 다음과 같습니다.

  - 학습
  - 요구 사항
  - 디자인
  - 구현
  - 확인
  - 해제
  - 응답

![보안 개발 수명 주기](./media/secure-dev-overview/01-sdl-phase.png)

이 문서에서는 SDL 단계를 설계, 개발 및 배포로 그룹화합니다.

## <a name="engage-your-organizations-security-team"></a>조직의 보안 팀 참여

조직에는 개발 수명 주기 동안 처음부터 끝까지 보안 활동을 지원하는 공식적인 응용 프로그램 보안 프로그램이 있을 수 있습니다. 조직에 보안 및 규정 준수 팀이 있는 경우 응용 프로그램 개발을 시작하기 전에 해당 팀에 참여해야 합니다. SDL의 각 단계에서 놓친 작업이 있는지 물어봅니다.

많은 독자가 참여할 보안 또는 규정 준수 팀이 없을 수도 있다는 것을 알고 있습니다. 이 문서는 SDL의 각 단계에서 고려해야 할 보안 질문과 결정을 안내하는 데 도움이 될 수 있습니다.

## <a name="resources"></a>리소스

다음 리소스를 사용하여 보안 응용 프로그램 개발에 대해 자세히 알아보고 Azure에서 응용 프로그램을 보호하는 데 도움이 됩니다.

[Microsoft 보안 개발 수명 주기(SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) – SDL은 개발자가 보다 안전한 소프트웨어를 빌드하는 데 도움이 되는 Microsoft의 소프트웨어 개발 프로세스입니다. 개발 비용을 줄이면서 보안 규정 준수 요구 사항을 해결하는 데 도움이 됩니다.

[OWASP(개방형 웹 응용 프로그램 보안 프로젝트)](https://www.owasp.org/index.php/Main_Page) – OWASP는 웹 응용 프로그램 보안 분야에서 자유롭게 사용할 수 있는 문서, 방법론, 문서, 도구 및 기술을 생산하는 온라인 커뮤니티입니다.

[왼쪽으로 밀어, 보스처럼](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca) – 개발자가 더 안전한 코드를 만들기 위해 완료해야 하는 다양한 유형의 응용 프로그램 보안 활동을 설명하는 일련의 온라인 문서입니다.

[Microsoft ID 플랫폼](../../active-directory/develop/index.yml) - Microsoft ID 플랫폼은 Azure AD ID 서비스 및 개발자 플랫폼의 진화입니다. 인증 서비스, 오픈 소스 라이브러리, 응용 프로그램 등록 및 구성, 전체 개발자 설명서, 코드 샘플 및 기타 개발자 콘텐츠로 구성된 모든 기능을 갖춘 플랫폼입니다. Microsoft ID 플랫폼은 OAuth 2.0 및 OpenID Connect와 같은 업계 표준 프로토콜을 지원합니다.

[Azure 솔루션에 대한 보안 모범 사례](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/) - Azure를 사용하여 클라우드 솔루션을 디자인, 배포 및 관리할 때 사용할 보안 모범 사례 모음입니다. 이 백서는 IT 프로를 위한 리소스입니다. 여기에는 보안 Azure 솔루션을 빌드하고 배포하는 디자이너, 설계자, 개발자 및 테스터가 포함될 수 있습니다.

[Azure의 보안 및 규정 준수 청사진](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview) - Azure 보안 및 규정 준수 Blueprint는 엄격한 규정 및 표준을 준수하는 클라우드 기반 응용 프로그램을 빌드하고 시작하는 데 도움이 되는 리소스입니다.

## <a name="next-steps"></a>다음 단계
다음 문서에서는 보안 응용 프로그램을 설계, 개발 및 배포하는 데 도움이 되는 보안 제어 및 활동을 권장합니다.

- [안전한 애플리케이션 설계](secure-design.md)
- [보안 애플리케이션 개발](secure-develop.md)
- [보안 애플리케이션 배포](secure-deploy.md)
