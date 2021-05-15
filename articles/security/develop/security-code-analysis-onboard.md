---
title: Microsoft Security Code Analysis 온보딩 가이드
description: Microsoft Security Code Analysis 확장을 온보딩하고 설치하는 방법을 알아봅니다. 사전 요구 사항을 참조하시고 추가 리소스를 확인해주세요.
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 03/22/2021
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: c827dc81c1ef1ab03dd6c9178a609b512e59ef15
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801217"
---
# <a name="onboarding-and-installing"></a>온보딩 및 설치

> [!Note]
> 2022년 3월 1일부터 MSCA(Microsoft Security Code Analysis) 확장이 사용 중지됩니다. 기존 MSCA 고객은 2022년 3월 1일까지 MSCA에 대한 액세스가 유지됩니다. Azure DevOps의 대체 옵션은 [OWASP 소스 코드 분석 도구](https://owasp.org/www-community/Source_Code_Analysis_Tools)를 참조하세요. GitHub로 마이그레이션을 계획하는 고객의 경우 [GitHub 고급 보안](https://docs.github.com/github/getting-started-with-github/about-github-advanced-security)을 확인할 수 있습니다.

Microsoft Security Code Analysis을 시작하기 위한 사전 요구 사항.

- 다음 섹션에 설명된 대로 적합한 Microsoft 통합 지원 제품입니다.
- Azure DevOps 조직.
- Azure DevOps 조직에 확장을 설치할 수 있는 권한.
- 클라우드 호스팅된 Azure DevOps 파이프라인에 동기화 할 수 있는 소스 코드입니다.

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>Microsoft Security Code Analysis 온보딩

### <a name="interested-in-purchasing-the-microsoft-security-code-analysis-extension"></a>Microsoft Security Code Analysis 확장을 구매하는 데 관심이 있나요?

다음 지원 서비스 중 하나를 사용하는 경우 기술 계정 관리자에게 문의하여 기존 시간을 구입하거나 교환하여 확장에 대한 액세스 권한을 얻으십시오.

- 통합 지원 고급 계층
- 통합 지원 성능 계층
- 개발자를 위한 프리미어 지원
- 파트너를 위한 프리미어 지원
- Enterprise를 위한 프리미어 지원

위에서 언급한 지원 계약 중 하나가 없으면 파트너 중 하나에서 확장을 구입할 수 있습니다.

**다음 단계:**

위의 자격을 충족하는 경우 아래 목록에서 파트너에게 문의하여 Microsoft 보안 코드 분석 확장을 구매하세요. 그렇지 않은 경우 [Microsoft 보안 코드 분석 지원](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request)에 문의해주세요.

>**파트너:**

- 영역 – 연락처 세부 정보: cloudsupport@zones.com
- Wortell – 연락처 세부 정보: info@wortell.nl
- Logicalis – 연락처 세부 정보: logicalisleads@us.logicalis.com

### <a name="become-a-partner"></a>파트너 되기

Microsoft 보안 코드 분석 팀은 파트너 규약에 대한 프리미어 지원 파트너를 등록합니다. 파트너는 Azure DevOps 고객이 확장을 구매하려는 고객에게 확장을 판매하여 보다 안전하게 개발할 수 있도록 지원하지만 Microsoft와의 기업 지원 계약은 없습니다. 관심이 있는 파트너는 [여기](http://www.microsoftpartnersupport.com/msrd/opin)에서 등록할 수 있습니다.

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>Microsoft Security Code Analysis 설치

1. 확장이 Azure DevOps 조직과 공유되면 Azure DevOps 조직 페이지로 이동합니다. 이러한 페이지에 대한 URL의 예는 `https://dev.azure.com/contoso`입니다.
1. 이름 옆의 오른쪽 위 모서리에서 쇼핑백 아이콘을 선택하고 **확장 관리** 를 선택합니다.
1. **공유됨** 을 선택합니다.
1. Microsoft 보안 코드 분석 확장을 선택하고 **설치** 를 선택합니다.
1. 드롭다운 목록에서 확장을 설치할 Azure DevOps 조직을 선택합니다.
1. **설치** 를 선택합니다. 설치가 완료된 후에는 확장을 사용하여 시작할 수 있습니다.

>[!NOTE]
> 확장을 설치하기 위한 액세스 권한이 없더라도 설치 단계를 계속 진행합니다. 설치 과정에서 Azure DevOps 조직 관리자에 대한 액세스를 요청할 수 있습니다.

확장을 설치한 후에는 보안 개발 빌드 작업이 표시되고 Azure Pipelines에 추가할 수 있습니다.

## <a name="adding-specific-build-tasks-to-your-azure-devops-pipeline"></a>Azure DevOps 파이프라인에 특정 빌드 작업 추가

1. Azure DevOps 조직에서 팀 프로젝트를 엽니다.
1. **파이프라인** > **빌드** 를 선택합니다.
1. 확장 빌드 작업을 추가하려는 파이프라인을 선택합니다.
   - 새 파이프라인: **새로 만들기** 를 선택하고 자세히 설명된 단계에 따라 새 파이프라인을 만듭니다.
   - 파이프라인 편집: 기존 파이프라인을 선택하고 **편집** 을 선택하여 파이프라인 편집을 시작합니다.
1. **+** 을 선택하고 **작업 추가** 창으로 이동합니다.
1. 목록에서 또는 검색 상자를 사용하여 추가하려는 빌드 작업을 찾습니다. **추가** 를 선택합니다.
1. 작업에 필요한 매개 변수를 지정합니다.
1. 새 빌드를 큐에 넣습니다.
   >[!NOTE]
   >파일 및 폴더 경로는 원본 리포지토리의 루트를 기준으로 합니다. 출력 파일과 폴더를 매개 변수로 지정하는 경우 빌드 에이전트에서 정의한 공통 위치로 대체됩니다.

> [!TIP]
>
> - 빌드 후 분석을 실행하려면 빌드의 빌드 아티팩트 게시 단계 후에 Microsoft 보안 코드 분석 빌드 작업을 저장합니다. 이렇게 하면 빌드를 완료하고 정적 분석 도구를 실행하기 전에 결과를 게시할 수 있습니다.
> - 보안 개발 빌드 작업의 경우 항상 **오류 발생 시 계속** 을 선택합니다. 한 도구가 실패하더라도 다른 도구를 실행할 수 있습니다. 도구 간에는 상호 종속성이 없습니다.
> - Microsoft 보안 코드 분석 빌드 작업은 도구를 성공적으로 실행하지 못한 경우에만 실패합니다. 그러나 도구는 코드에서 문제를 식별하는 경우에도 성공합니다. 사후 분석 빌드 작업을 사용하여 도구에서 코드의 문제를 식별하는 경우 빌드가 실패하도록 구성할 수 있습니다.
> - 일부 Azure DevOps 빌드 작업은 릴리스 파이프라인을 통해 실행할 때 지원되지 않습니다. 구체적으로 말하자면 Azure DevOps는 릴리스 파이프라인 내에서 아티팩트를 게시하는 작업을 지원하지 않습니다.
> - 매개 변수로 지정할 수 있는 Azure DevOps 팀 빌드에서 미리 정의된 변수 목록은 [Azure Devops 빌드 변수](/azure/devops/pipelines/build/variables?tabs=batch)를 참조하세요.

## <a name="next-steps"></a>다음 단계

빌드 작업을 구성하는 방법에 대한 자세한 내용은 [구성 가이드](security-code-analysis-customize.md) 또는 [YAML 구성 가이드](yaml-configuration.md)를 참조하세요.

확장 및 제공된 도구에 대한 추가 질문이 있는 경우 [FAQ 페이지](security-code-analysis-faq.md)를 확인하세요.