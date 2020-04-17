---
title: 마이크로소프트 보안 코드 분석 온보딩 가이드
description: 이 문서에서는 Microsoft 보안 코드 분석 확장 프로그램 설치에 대해 설명합니다.
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/14/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 3ef111817b6351277f975b9b7e454f9a89982451
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460208"
---
# <a name="onboarding-and-installing"></a>온보딩 및 설치

Microsoft 보안 코드 분석을 시작하기 위한 필수 구성 조건:

- 다음 섹션에서 자세히 설명한 대로 적격한 Microsoft 통합 지원 제품입니다.
- Azure DevOps 조직.
- Azure DevOps 조직에 확장을 설치할 수 있는 권한입니다.
- 클라우드 호스팅 Azure DevOps 파이프라인에 동기화할 수 있는 소스 코드입니다.

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>Microsoft 보안 코드 분석 확장 온보딩

### <a name="interested-in-purchasing-the-microsoft-security-code-analysis-extension"></a>Microsoft 보안 코드 분석 확장을 구입에 관심이 있으십니까?

다음 지원 제품 중 하나가 있는 경우 기술 계정 관리자에게 문의하여 기존 시간을 구입하거나 교체하여 확장에 액세스하십시오.

- 통합 지원 고급 계층
- 통합 지원 성능 계층
- 개발자를 위한 프리미어 지원
- 파트너를 위한 프리미어 지원
- 엔터프라이즈를 위한 프리미어 지원

위에서 언급한 지원 계약 중 하나가 없는 경우 파트너 중 하나에서 확장 프로그램을 구입할 수 있습니다.

**다음 단계:**

아래 목록에서 파트너에게 문의하여 Microsoft 보안 코드 분석 확장을 구입하도록 요청합니다.

>**파트너:**

- 영역 - 연락처 세부 정보:cloudsupport@zones.com
- Wortell – 연락처 정보:info@wortell.nl

### <a name="become-a-partner"></a>파트너 되기

Microsoft 보안 코드 분석 팀은 파트너를 위한 프리미어 지원 계약을 통해 파트너를 온보보드로 찾고 있습니다. 파트너는 Azure DevOps 고객이 확장을 구입하려는 고객에게 판매하여 보다 안전하게 개발할 수 있도록 지원하지만 Microsoft와 엔터프라이즈 지원 계약을 맺지 않은 고객에게는 지원이 되지 않습니다. 관심 있는 파트너는 [여기에서](http://www.microsoftpartnersupport.com/msrd/opin)등록할 수 있습니다.

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>Microsoft 보안 코드 분석 확장 프로그램 설치

1. 확장이 Azure DevOps 조직과 공유되면 Azure DevOps 조직 페이지로 이동합니다. 이러한 페이지의 예제 URL은 `https://dev.azure.com/contoso`.
1. 이름 옆 오른쪽 상단 모서리에 있는 쇼핑백 아이콘을 선택한 다음 **광고 확장 관리를 선택합니다.**
1. **공유**를 선택합니다.
1. Microsoft 보안 코드 분석 확장을 선택하고 **설치를**선택합니다.
1. 드롭다운 목록에서 Azure DevOps 조직을 선택하여 확장을 설치합니다.
1. **설치**를 선택합니다. 설치가 완료되면 확장을 사용할 수 있습니다.

>[!NOTE]
> 확장을 설치할 수 있는 권한이 없는 경우에도 설치 단계를 계속합니다. 설치 프로세스 중에 Azure DevOps 조직 관리자에게 액세스를 요청할 수 있습니다.

확장을 설치하면 보안 개발 빌드 작업이 표시되고 Azure 파이프라인에 추가할 수 있습니다.

## <a name="adding-specific-build-tasks-to-your-azure-devops-pipeline"></a>Azure DevOps 파이프라인에 특정 빌드 작업 추가

1. Azure DevOps 조직에서 팀 프로젝트를 엽니다.
1. **파이프라인** > **빌드를 선택합니다.**
1. 확장 빌드 작업을 추가할 파이프라인을 선택합니다.
   - 새 파이프라인: **새** 파이프라인을 선택하고 자세한 단계를 수행하여 새 파이프라인을 만듭니다.
   - 파이프라인 편집: 기존 파이프라인을 선택한 다음 **편집을** 선택하여 파이프라인 편집을 시작합니다.
1. 작업 **+** **추가** 창을 선택하고 이동합니다.
1. 목록에서 또는 검색 상자를 사용하여 추가할 빌드 작업을 찾습니다. **추가**를 선택합니다.
1. 작업에 필요한 매개 변수를 지정합니다.
1. 새 빌드를 큐에 넣습니다.
   >[!NOTE]
   >파일 및 폴더 경로는 원본 리포지토리의 루트를 기준으로 합니다. 출력 파일 및 폴더를 매개 변수로 지정하면 빌드 에이전트에서 정의한 공통 위치로 대체됩니다.

> [!TIP]
>
> - 빌드 후 분석을 실행하려면 빌드의 아티팩트 게시 단계 후에 Microsoft 보안 코드 분석 빌드 작업을 배치합니다. 이렇게 하면 정적 분석 도구를 실행하기 전에 빌드에서 결과를 완료하고 게시할 수 있습니다.
> - 보안 개발 빌드 작업을 위해 항상 **오류 계속을** 선택합니다. 한 도구가 실패하더라도 다른 도구는 실행할 수 있습니다. 도구 간에는 상호 종속성이 없습니다.
> - Microsoft 보안 코드 분석 빌드 작업은 도구가 성공적으로 실행되지 않는 경우에만 실패합니다. 그러나 도구가 코드의 문제를 식별하더라도 성공합니다. 분석 후 빌드 작업을 사용하여 도구가 코드의 문제를 식별할 때 실패하도록 빌드를 구성할 수 있습니다.
> - 일부 Azure DevOps 빌드 작업은 릴리스 파이프라인을 통해 실행할 때 지원되지 않습니다. 좀 더 구체적으로, Azure DevOps 릴리스 파이프라인 내에서 아티팩트를 게시 하는 작업을 지원 하지 않습니다.
> - 매개 변수로 지정할 수 있는 Azure DevOps 팀 빌드에서 미리 정의된 변수 목록은 [Azure DevOps 빌드 변수](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts)를 참조하십시오.

## <a name="next-steps"></a>다음 단계

빌드 작업 구성에 대한 자세한 내용은 [구성 가이드](security-code-analysis-customize.md) 또는 [YAML 구성 가이드를](yaml-configuration.md)참조하십시오.

확장 프로그램 및 제공되는 도구에 대해 더 궁금한 점이 있으면 [FAQ 페이지를](security-code-analysis-faq.md)확인하십시오.
