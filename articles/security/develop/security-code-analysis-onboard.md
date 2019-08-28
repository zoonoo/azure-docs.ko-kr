---
title: Microsoft Azure 보안 코드 분석 온 보 딩 가이드
description: 이 문서에서는 보안 코드 분석 확장을 설치 하는 방법에 대해 설명 합니다.
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 8a8eca73205d4f8f33d4d069fda72638af61d355
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718302"
---
# <a name="how-to-onboarding-and-installing"></a>방법: 온 보 딩 및 설치

Microsoft 보안 코드 분석을 시작 하기 위한 필수 구성 요소
  - 적격 Microsoft 통합 지원 서비스 제공 (아래 세부 정보)
  - Azure DevOps 조직
  - Azure DevOps 조직에 확장을 설치할 수 있는 권한
  - 클라우드 호스팅 Azure DevOps 파이프라인과 동기화 할 수 있는 소스 코드


## <a name="onboarding-microsoft-security-code-analysis-extension"></a>Microsoft 보안 코드 분석 확장 온 보 딩

- 다음 지원 제품 중 하나가 이미 있는 경우 기술 계정 관리자에 게 문의 하 고 기존 시간을 purchase\swap 확장에 대 한 액세스 권한을 얻으십시오.
   - 통합 지원 – 개발자, 프리미어 지원 파트너 또는 프리미어 지원에 대 한 고급 및 성능 계층, 프리미어 지원입니다.
- 다음 지원 서비스 중 하나를 사용 하거나 Microsoft에 대 한 지원 계획이 없는 경우 적격 지원 제공으로 업그레이드 해야 합니다.
   - 파트너, Azure Basic, Azure Developer, Azure Standard, Azure 전문가 지원 또는 통합 지원 – 코어 계층에 대 한 Azure 지원
- 적격 지원 제공 서비스를 구입 하려면 [지원 서비스 홈 페이지](https://www.microsoft.com/enterprise/services/support) 를 방문 하세요.
- 지원 계약이 준비 되 면 시작 하는 데 도움이 되는 기술 계정 관리자에 게 문의 하 여 필요한 모든 세부 정보를 수집 합니다.
 
>[!NOTE]
> Microsoft 파트너 네트워크에 등록 된 파트너만 파트너에 대 한 프리미어 지원를 구매할 수 있으며, 그렇지 않은 경우 앞에서 언급 한 적격 지원 서비스 중 하나를 구매 하세요.

## <a name="installing-microsoft-security-code-analysis-extension"></a>Microsoft 보안 코드 분석 확장을 설치 하는 중

1. 확장이 Azure DevOps 조직과 공유 되 면 Azure DevOps 조직 페이지로 이동 합니다 (예:). http://dev.azure.com/contoso)
2. 이름 옆의 오른쪽 위 모서리에서 쇼핑 모음 아이콘을 클릭 하 고 확장 관리를 클릭 합니다. 
3. Microsoft 보안 코드 분석 확장을 클릭 하 고 Azure DevOps UI 마법사를 시작 하 여 설치를 시작 합니다.
4. 드롭다운에서 확장을 설치할 Azure DevOps 조직을 선택 합니다.
5. 설치를 클릭 합니다. 완료 되 면 확장을 사용 하 여 계속 진행할 수 있습니다.

>[!NOTE]
> 사용자가 액세스할 수 없는 경우에도 설치 단계를 계속 진행 하 고이 과정에서 Azure DevOps 조직 관리자의 액세스를 요청할 수 있습니다.
>
확장이 설치 되 면 보안 개발 빌드 작업이 표시 되 고 Azure Pipelines에 추가할 수 있습니다.

## <a name="adding-specific-build-tasks-to-your-devops-pipeline"></a>DevOps 파이프라인에 특정 빌드 작업 추가

1. Azure DevOps 조직에서 팀 프로젝트를 엽니다.
2. **파이프라인** 아래의 **빌드** 탭으로 이동 합니다. 
3. 확장 빌드 작업을 추가할 파이프라인을 선택 합니다. 
   - 새로 만들기- **새로** 만들기를 클릭 하 고 자세히 설명 된 단계에 따라 새 파이프라인을 만듭니다.
   - 편집-파이프라인을 선택 하 고 **편집** 을 클릭 하 여 기존 파이프라인 편집을 시작 합니다.
4. +를 클릭 하 여 **작업 추가** 창으로 이동 합니다.
5. 목록에서 추가 하거나 검색 상자를 사용 하 여 추가 하려는 빌드 작업을 찾은 다음 **추가**를 클릭 합니다. 
6. 이제 태스크에 필요한 매개 변수를 계속 지정할 수 있습니다.
>[!NOTE]
>파일 또는 디렉터리 경로는 원본 리포지토리의 루트를 기준으로 하며, 출력 폴더/파일을 지정 하는 매개 변수는 빌드 에이전트에서 정의한 공통 위치로 바뀝니다.

7. 새 빌드를 큐에 넣습니다.
> [!TIP]
>  - 빌드 후 분석을 실행 하려면 빌드의 빌드 아티팩트 게시 단계 후에 Microsoft 보안 코드 분석 빌드 작업을 저장 합니다. 이렇게 하면 빌드를 완료 하 고 정적 분석 도구를 실행 하기 전에 결과를 게시할 수 있습니다.
>  - 보안 개발 빌드 작업의 **' 오류 발생 시 계속 '** 옵션을 항상 확인 하세요. 도구 중 하나가 실패 하더라도 다른 도구를 실행할 수 있습니다. 상호 종속성이 없습니다.
>  - Microsoft 보안 코드 분석 빌드 작업은 도구가 성공적으로 실행 되지 않는 경우에만 실패 하지만 도구에서 코드의 문제를 식별 하는 경우에는 실패 하지 않습니다. **분석 후** 빌드 작업을 사용 하 여 도구에서 코드의 문제를 식별할 때 중단 되도록 빌드를 구성할 수 있습니다.
>  - 일부 Azure DevOps 빌드 작업은 "릴리스" 파이프라인을 통해 실행할 때 지원 되지 않습니다. Azure DevOps의 제한 사항입니다. 릴리스 파이프라인 내에서 아티팩트를 게시 하는 작업은 지원 하지 않습니다.
>  - Azure DevOps 팀 빌드에서 매개 변수로 지정할 수 있는 미리 정의 된 변수 목록은 [Azure Devops 빌드 변수](https://docs.microsoft.com/azure/devops/pipelines/build/variables?tabs=batch&view=vsts) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

빌드 작업을 구성 하는 방법에 대 한 자세한 내용은 [구성 가이드](security-code-analysis-customize.md) 를 참조 하세요.

확장 및 제공 된 도구에 대 한 추가 질문이 있는 경우 [faq 페이지](security-code-analysis-faq.md) 를 참조 하세요.


