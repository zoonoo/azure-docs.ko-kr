---
title: Microsoft 보안 코드 분석 온 보 딩 가이드
description: 이 문서에서는 Microsoft 보안 코드 분석 확장을 설치 하는 방법을 설명 합니다.
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/24/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: fd0057c5f5e365d6c91ae221a7add813855bb1ec
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323590"
---
# <a name="onboarding-and-installing"></a>온보딩 및 설치

Microsoft 보안 코드 분석을 시작 하기 위한 필수 조건:

- 다음 섹션에 설명 된 대로 적합 한 Microsoft 통합 지원 제품입니다.
- Azure DevOps 조직.
- Azure DevOps 조직에 확장을 설치할 수 있는 권한.
- 클라우드 호스팅 Azure DevOps 파이프라인에 동기화 할 수 있는 소스 코드입니다.

## <a name="onboarding-the-microsoft-security-code-analysis-extension"></a>Microsoft 보안 코드 분석 확장을 온 보 딩

### <a name="interested-in-purchasing-the-microsoft-security-code-analysis-extension"></a>Microsoft 보안 코드 분석 확장을 구매 하는 데 관심이 있나요?

다음 지원 서비스 중 하나를 사용 하는 경우 기술 계정 관리자에 게 문의 하 여 기존 시간을 구입 하거나 교환 하 여 확장에 대 한 액세스 권한을 얻으십시오.

- 통합 지원 고급 계층
- 통합 지원 성능 계층
- 개발자를 위한 프리미어 지원
- 파트너에 대 한 프리미어 지원
- Enterprise에 대 한 프리미어 지원

위에서 언급 한 지원 계약 중 하나가 없으면 파트너 중 하나에서 확장을 구입할 수 있습니다.

**다음 단계:**

위의 자격을 충족 하는 경우 아래 목록에서 파트너에 게 문의 하 여 Microsoft 보안 코드 분석 확장을 구매 하세요. 그렇지 않으면 [Microsoft 보안 코드 분석 지원](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request)에 문의 하십시오.

>**귀사**

- 영역 – 연락처 세부 정보:cloudsupport@zones.com
- Wortell – 연락처 세부 정보:info@wortell.nl
- Logicalis – 연락처 세부 정보:logicalisleads@us.logicalis.com

### <a name="become-a-partner"></a>파트너 되기

Microsoft 보안 코드 분석 팀은 파트너 규약에 대 한 프리미어 지원 파트너를 등록 합니다. 파트너는 Azure DevOps 고객이 확장을 구매 하려는 고객에 게 확장을 판매 하 여 보다 안전 하 게 개발할 수 있도록 지원 하지만 Microsoft와의 기업 지원 계약은 없습니다. 관심이 있는 파트너는 [여기](http://www.microsoftpartnersupport.com/msrd/opin)에서 등록할 수 있습니다.

## <a name="installing-the-microsoft-security-code-analysis-extension"></a>Microsoft 보안 코드 분석 확장 프로그램 설치

1. 확장이 Azure DevOps 조직과 공유 되 면 Azure DevOps 조직 페이지로 이동 합니다. 이러한 페이지에 대 한 URL의 예는 `https://dev.azure.com/contoso` 입니다.
1. 이름 옆의 오른쪽 위 모서리에서 쇼핑 모음 아이콘을 선택 하 고 **확장 관리**를 선택 합니다.
1. **공유**를 선택 합니다.
1. Microsoft 보안 코드 분석 확장을 선택 하 고 **설치**를 선택 합니다.
1. 드롭다운 목록에서 확장을 설치할 Azure DevOps 조직을 선택 합니다.
1. **설치**를 선택합니다. 설치가 완료 된 후에는 확장을 사용 하 여 시작할 수 있습니다.

>[!NOTE]
> 확장을 설치 하기 위한 액세스 권한이 없더라도 설치 단계를 계속 진행 합니다. 설치 과정에서 Azure DevOps 조직 관리자에 대 한 액세스를 요청할 수 있습니다.

확장을 설치한 후에는 보안 개발 빌드 작업이 표시 되 고 Azure Pipelines에 추가할 수 있습니다.

## <a name="adding-specific-build-tasks-to-your-azure-devops-pipeline"></a>Azure DevOps 파이프라인에 특정 빌드 작업 추가

1. Azure DevOps 조직에서 팀 프로젝트를 엽니다.
1. **파이프라인**  >  **빌드**를 선택 합니다.
1. 확장 빌드 작업을 추가 하려는 파이프라인을 선택 합니다.
   - 새 파이프라인 **: 새로 만들기를 선택 하** 고 자세히 설명 된 단계에 따라 새 파이프라인을 만듭니다.
   - 파이프라인 편집: 기존 파이프라인을 선택 하 고 **편집** 을 선택 하 여 파이프라인 편집을 시작 합니다.
1. 을 선택 **+** 하 고 **작업 추가** 창으로 이동 합니다.
1. 목록에서 또는 검색 상자를 사용 하 여 추가 하려는 빌드 작업을 찾습니다. **추가**를 선택합니다.
1. 작업에 필요한 매개 변수를 지정 합니다.
1. 새 빌드를 큐에 넣습니다.
   >[!NOTE]
   >파일 및 폴더 경로는 원본 리포지토리의 루트를 기준으로 합니다. 출력 파일과 폴더를 매개 변수로 지정 하는 경우 빌드 에이전트에서 정의한 공통 위치로 대체 됩니다.

> [!TIP]
>
> - 빌드 후 분석을 실행 하려면 빌드의 빌드 아티팩트 게시 단계 후에 Microsoft 보안 코드 분석 빌드 작업을 저장 합니다. 이렇게 하면 빌드를 완료 하 고 정적 분석 도구를 실행 하기 전에 결과를 게시할 수 있습니다.
> - 보안 개발 빌드 작업의 경우 항상 **오류 발생 시 계속** 을 선택 합니다. 한 도구가 실패 하더라도 다른 도구를 실행할 수 있습니다. 도구 간에는 상호 종속성이 없습니다.
> - Microsoft 보안 코드 분석 빌드 작업은 도구를 성공적으로 실행 하지 못한 경우에만 실패 합니다. 그러나 도구는 코드에서 문제를 식별 하는 경우에도 성공 합니다. 사후 분석 빌드 작업을 사용 하 여 도구에서 코드의 문제를 식별 하는 경우 빌드가 실패 하도록 구성할 수 있습니다.
> - 일부 Azure DevOps 빌드 작업은 릴리스 파이프라인을 통해 실행할 때 지원 되지 않습니다. 구체적으로 말하자면 Azure DevOps는 릴리스 파이프라인 내에서 아티팩트를 게시 하는 작업을 지원 하지 않습니다.
> - 매개 변수로 지정할 수 있는 Azure DevOps 팀 빌드에서 미리 정의 된 변수 목록은 [Azure Devops 빌드 변수](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

빌드 작업을 구성 하는 방법에 대 한 자세한 내용은 [구성 가이드](security-code-analysis-customize.md) 또는 [yaml 구성 가이드](yaml-configuration.md)를 참조 하세요.

확장 및 제공 된 도구에 대 한 추가 질문이 있는 경우 [FAQ 페이지](security-code-analysis-faq.md)를 확인 하세요.
