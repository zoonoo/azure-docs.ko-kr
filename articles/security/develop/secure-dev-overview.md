---
title: Microsoft Azure에 대 한 보안 개발 모범 사례
description: 더 안전한 코드를 개발 하 고 클라우드에서 보다 안전한 응용 프로그램을 배포 하는 데 도움이 되는 모범 사례입니다.
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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/26/2020
ms.locfileid: "68934859"
---
# <a name="secure-development-best-practices-on-azure"></a>Azure의 보안 개발 모범 사례
이 문서 시리즈에서는 클라우드의 응용 프로그램을 개발할 때 고려해 야 할 보안 작업 및 제어를 제공 합니다. 주기의 각 단계에서 고려해 야 하는 Microsoft SDL(Security Development Lifecycle) 단계와 보안 질문 및 개념에 대해 설명 합니다. 목표는 수명 주기의 각 단계에서 더 안전한 응용 프로그램을 디자인, 개발 및 배포 하는 데 사용할 수 있는 활동 및 Azure 서비스를 정의 하는 데 도움을 주기 위한 것입니다.

이 문서의 권장 사항은 Azure 보안 및 고객의 경험에서 제공 됩니다. 이러한 문서를 개발 프로젝트의 특정 단계 중에 고려해 야 할 사항에 대 한 참조로 사용할 수 있지만, 모든 문서를 한 번 이상 읽는 것이 좋습니다. 모든 문서를 읽으면 프로젝트의 이전 단계에서 누락 했을 수 있는 개념을 소개 합니다. 제품을 출시 하기 전에 이러한 개념을 구현 하면 보안 소프트웨어를 구축 하 고, 보안 규정 준수 요구 사항을 해결 하 고, 개발 비용을 절감할 수 있습니다.

이러한 문서는 안전한 Azure 응용 프로그램을 빌드 및 배포 하는 모든 수준의 소프트웨어 디자이너, 개발자 및 테스터를 위한 리소스입니다.

## <a name="overview"></a>개요

보안은 모든 응용 프로그램의 가장 중요 한 측면 중 하나 이며, 간단한 것은 아닙니다. 다행히 Azure는 클라우드에서 응용 프로그램을 보호 하는 데 도움이 될 수 있는 많은 서비스를 제공 합니다. 이러한 문서에서는 소프트웨어 개발 수명 주기의 각 단계에서 구현할 수 있는 활동 및 Azure 서비스를 다룹니다 .이를 통해 더 안전한 코드를 개발 하 고 클라우드에서 보다 안전한 응용 프로그램을 배포할 수 있습니다.

## <a name="security-development-lifecycle"></a>보안 개발 수명 주기

보안 소프트웨어 개발을 위한 최선의 구현 방법에 따라 프로젝트 방법론 ([폭포](https://en.wikipedia.org/wiki/Waterfall_model), [Agile](https://en.wikipedia.org/wiki/Agile_software_development)또는 [devops](https://en.wikipedia.org/wiki/DevOps))에 관계 없이 요구 사항 분석에서 유지 관리에 이르는 소프트웨어 개발 수명 주기의 각 단계에 보안을 통합 해야 합니다. 높은 프로필 데이터 위반 및 운영 보안 결함의 악용 시 개발자는 더 많은 개발자 들이 개발 프로세스 전체에서 보안을 해결 해야 한다는 점을 이해 하 고 있습니다.

나중에 개발 수명 주기에서 문제를 해결 하는 경우 더 많은 문제를 해결 하 게 됩니다. 보안 문제는 예외입니다. 소프트웨어 개발의 초기 단계에서 보안 문제를 무시 하는 경우 뒤에 나오는 각 단계는 이전 단계의 취약성을 상속할 수 있습니다. 최종 제품에는 여러 보안 문제 및 위반 가능성이 누적 됩니다. 개발 수명 주기의 각 단계에 대 한 보안 빌드를 통해 문제를 조기에 파악 하 고 개발 비용을 줄일 수 있습니다.

Microsoft [SDL (보안 개발 수명 주기)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) 의 단계를 수행 하 여 수명 주기의 각 단계에서 보안 소프트웨어 개발 사례를 수행 하는 데 사용할 수 있는 활동 및 Azure 서비스를 소개 합니다.

SDL 단계는 다음과 같습니다.

  - 학습
  - 요구 사항
  - 디자인
  - 구현
  - 확인
  - 해제
  - 응답

![보안 개발 수명 주기](./media/secure-dev-overview/01-sdl-phase.png)

이 문서에서는 SDL 단계를 디자인, 개발 및 배포로 그룹화 합니다.

## <a name="engage-your-organizations-security-team"></a>조직의 보안 팀 참여

조직에는 개발 수명 주기 동안 시작부터 끝까지 보안 작업을 지 원하는 공식적인 응용 프로그램 보안 프로그램이 있을 수 있습니다. 조직에 보안 및 규정 준수 팀이 있는 경우 응용 프로그램 개발을 시작 하기 전에 해당 팀에 참여 해야 합니다. 누락 된 태스크가 있는지 여부를 SDL의 각 단계에서 확인 합니다.

많은 독자가 보안 또는 규정 준수 팀에 참여 하지 못할 수 있습니다. 이러한 문서를 통해 SDL의 각 단계에서 고려해 야 하는 보안 질문과 결정을 안내할 수 있습니다.

## <a name="resources"></a>리소스

다음 리소스를 사용 하 여 보안 응용 프로그램을 개발 하 고 Azure에서 응용 프로그램을 보호 하는 방법에 대해 자세히 알아보세요.

[Microsoft SDL(Security Development Lifecycle)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) – SDL은 개발자가 보다 안전한 소프트웨어를 빌드하는 데 도움이 되는 Microsoft의 소프트웨어 개발 프로세스입니다. 개발 비용을 절감 하면서 보안 규정 준수 요구 사항을 해결 하는 데 도움이 됩니다.

[Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page) – OWASP은 웹 응용 프로그램 보안의 필드에서 무료로 사용할 수 있는 문서, 방법론, 설명서, 도구 및 기술을 생성 하는 온라인 커뮤니티입니다.

[왼쪽 (예: 상사](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca) )-개발자가 보다 안전한 코드를 만들기 위해 완료 해야 하는 다양 한 유형의 응용 프로그램 보안 활동을 설명 하는 일련의 온라인 문서입니다.

[Microsoft id 플랫폼](../../active-directory/develop/index.yml) – microsoft id 플랫폼은 Azure AD id 서비스와 개발자 플랫폼의 진화입니다. 인증 서비스, 오픈 소스 라이브러리, 응용 프로그램 등록 및 구성, 전체 개발자 설명서, 코드 샘플 및 기타 개발자 콘텐츠로 구성 된 완전 한 기능을 갖춘 플랫폼입니다. Microsoft id 플랫폼은 OAuth 2.0 및 Openid connect Connect와 같은 업계 표준 프로토콜을 지원 합니다.

Azure [솔루션에 대 한 보안 모범 사례](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/) -azure를 사용 하 여 클라우드 솔루션을 디자인, 배포 및 관리할 때 사용할 보안 모범 사례 모음입니다. 이 문서는 IT 전문가를 위한 리소스를 위한 것입니다. 여기에는 보안 Azure 솔루션을 빌드하고 배포하는 디자이너, 설계자, 개발자 및 테스터가 포함될 수 있습니다.

[Azure의 보안 및 규정 준수 청사진](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview) – azure 보안 및 규정 준수 청사진은 엄격한 규정 및 표준을 준수 하는 클라우드 기반 응용 프로그램을 빌드하고 시작 하는 데 도움이 되는 리소스입니다.

## <a name="next-steps"></a>다음 단계
다음 문서에서는 보안 응용 프로그램을 디자인, 개발 및 배포 하는 데 도움이 되는 보안 제어 및 작업을 권장 합니다.

- [보안 응용 프로그램 디자인](secure-design.md)
- [보안 애플리케이션 개발](secure-develop.md)
- [보안 응용 프로그램 배포](secure-deploy.md)
