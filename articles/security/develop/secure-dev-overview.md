---
title: 안전한 개발 모범 사례에 Microsoft Azure
description: 보다 안전한 코드를 개발 하 고 클라우드에서 보다 안전한 응용 프로그램을 배포 하는 데 대 한 모범 사례입니다.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: ee5c043038fbb747e90bca9530cbe2e94c8a95c2
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144465"
---
# <a name="secure-development-best-practices-on-azure"></a>안전한 개발 Azure에 대 한 모범 사례
이 문서 시리즈에서는 보안 작업 및 클라우드 용 응용 프로그램을 개발할 때 고려해 야 할 컨트롤을 표시 합니다. Microsoft Security Development Lifecycle (SDL) 및 보안 질문 및 수명 주기의 각 단계 중 고려해 야 할 개념 단계 설명 되어 있습니다. 작업 및 디자인, 개발 및 보다 안전한 응용 프로그램을 배포 하려면 수명 주기의 각 단계에서 사용할 수 있는 Azure 서비스를 정의 하는 데 목표가입니다.

문서에서 권장 하는 Azure 보안에 대 한 경험에서 고객의 경험을 제공합니다. 개발 프로젝트의 특정 단계에서 고려해 야 하는 것에 대 한 참조로 다음이 문서를 사용할 수 있지만 읽어 문서 끝까지 한 번 이상부터 모든 것이 좋습니다. 모든 문서를 읽을 수 있는 프로젝트의 초기 단계에서 기회는 개념을 소개 합니다. 제품을 릴리스하기 전에 이러한 개념을 구현 도움이 될 수 있습니다 안전한 소프트웨어를 빌드, 보안 규정 준수 요구 및 개발 비용을 절감 합니다.

이러한 문서는 소프트웨어 디자이너, 개발자를 위한 리소스 이며 테스터 모든 수준에서 빌드하고 보안 Azure 응용 프로그램을 배포 하는 합니다.

## <a name="overview"></a>개요

보안을 사용 하면 모든 응용 프로그램의 가장 중요 한 측면 중 하나인 및 이해 하는 간단한 작업이 아닙니다. 다행 스럽게도 Azure 클라우드에서 응용 프로그램을 보호 하는 도움이 될 수 있는 많은 서비스를 제공 합니다. 이러한 문서는 활동 및 보다 안전한 코드를 개발 하 고 클라우드에서 보다 안전한 응용 프로그램을 배포 하는 데 소프트웨어 개발 수명 주기의 각 단계에서 구현할 수 있습니다 하는 Azure 서비스를 다룹니다.

## <a name="security-development-lifecycle"></a>보안 개발 수명 주기

보안 소프트웨어 개발 프로젝트 방법론에 관계 없이 유지 관리 요구 사항 분석을 통해 소프트웨어 개발 수명 주기의 각 단계에 보안을 통합 필요에 대 한 모범 사례를 따르면 ([폭포](https://en.wikipedia.org/wiki/Waterfall_model)하십시오 [agile](https://en.wikipedia.org/wiki/Agile_software_development), 또는 [DevOps](https://en.wikipedia.org/wiki/DevOps)). 상위 프로필 데이터 위반의 절전 모드 해제 및 운영 보안 결함의 악용 더 많은 개발자가 보안 개발 프로세스를 통해 해결 해야 함을 이해 됩니다.

문제를 해결 하는 나중에 개발 수명 주기의 수정 하는 비용 있습니다. 보안 문제에도 마찬가지입니다. 소프트웨어 개발의 초기 단계에서 보안 문제를 무시 하는 경우 뒤에 오는 각 단계는 이전 단계의 취약점으로 인 한 경우 상속 될 수 있습니다. 여러 보안 문제 및 위반의 가능성이 최종 제품 누적 됩니다. 보안 개발 수명 주기의 각 단계에 빌드를 사용 하면 문제를 조기에 파악할 수 및 개발 비용을 줄일 수 있습니다.

Microsoft의 단계 따릅니다 [수명 주기 SDL (Security Development)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) 활동 및 수명 주기의 각 단계에서 보안 소프트웨어 개발 사례를 충족 하는 데 사용할 수 있는 Azure 서비스를 소개 합니다.

SDL 단계는 다음과 같습니다.

  - 교육
  - 요구 사항
  - 디자인
  - 구현
  - 확인
  - 해제
  - response

![보안 개발 수명 주기](./media/secure-dev-overview/01-sdl-phase.png)

이 문서에서는 우리가 SDL 디자인 그룹, 개발 및 배포 합니다.

## <a name="engage-your-organizations-security-team"></a>조직의 보안 팀을 참여

조직에는 개발 수명 주기 동안 처음부터 보안 활동을 사용 하 여 도움이 되는 공식적인 애플리케이션 보안 프로그램을 사용할 수 있습니다. 조직에서는 보안 및 규정 준수 팀, 응용 프로그램 개발을 시작 하기 전에 사용자에 게 유도 해야 합니다. 요청 SDL의 각 단계에서 놓친 작업 인지 합니다.

많은 독자 의견을 교환 하려면 보안 또는 규정 준수 팀 없을 이해 합니다. 이러한 문서는 보안 질문에 SDL의 각 단계에서 고려해 야 하는 의사 결정을 안내 하는 데 도움이 수 있습니다.

## <a name="resources"></a>리소스

다음 리소스를 사용 하 여 보안 응용 프로그램을 개발 하는 방법에 대 한 자세한 정보를 Azure에 응용 프로그램을 보호 하려면:

[Microsoft Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) – The SDL은 개발자가 더 안전한 소프트웨어를 빌드하는 데 도움이 되는 Microsoft의 소프트웨어 개발 프로세스입니다. 이 통해 개발 비용을 절감 하면서 보안 준수 요구 사항을 해결 합니다.

[응용 프로그램 프로젝트 OWASP (Web Security)를 열고](https://www.owasp.org/index.php/Main_Page) – OWASP는 무료로 사용할 수 있는 문서, 방법, 설명서, 도구 및 웹 응용 프로그램 보안 분야의 기술을 생성 하는 온라인 커뮤니티입니다.

[상사와 같은 푸시 왼쪽](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca) – 다양 한 유형의 개발자가 보다 안전한 코드를 만들려면 완료 해야 하는 응용 프로그램 보안 작업을 설명 하는 일련의 온라인 기사입니다.

[Microsoft id 플랫폼](https://docs.microsoft.com/azure/active-directory/develop/) – Microsoft id 플랫폼은 Azure AD는 id 서비스와 개발자 플랫폼의 발전 합니다. 이 인증 서비스, 오픈 소스 라이브러리, 응용 프로그램 등록 및 구성, 완전 한 개발자 설명서, 코드 샘플 및 기타 개발자 콘텐츠 구성 된 모든 기능을 갖춘 플랫폼. Microsoft id 플랫폼 OAuth 2.0 및 OpenID Connect 같은 산업 표준 프로토콜을 지원합니다.

[Azure 솔루션에 대 한 보안 모범 사례](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/) – 디자인, 배포 및 Azure를 사용 하 여 클라우드 솔루션을 관리할 때 사용할 보안 모범 사례 컬렉션입니다. 이 문서는 IT 전문가 위한 리소스입니다. 여기에는 보안 Azure 솔루션을 빌드하고 배포하는 디자이너, 설계자, 개발자 및 테스터가 포함될 수 있습니다.

[보안 및 Azure에서 규정 준수 청사진](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview) – Azure 보안 및 규정 준수 청사진은 도움이 될 수 있는 리소스 빌드 및 엄격한 규정 및 표준을 준수 하는 클라우드 기반 응용 프로그램을 시작 합니다.

## <a name="next-steps"></a>다음 단계
다음 문서에서는 권장 보안 제어 및 도움이 될 수 있는 활동을 디자인, 개발 및 보안 응용 프로그램을 배포 합니다.

- [보안 응용 프로그램 디자인](secure-design.md)
- [안전한 응용 프로그램 개발](secure-develop.md)
- [안전한 응용 프로그램 배포](secure-deploy.md)
