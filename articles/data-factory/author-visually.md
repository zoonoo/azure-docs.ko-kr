---
title: "Azure Data Factory 시각적 작성 | Microsoft Docs"
description: "Azure Data Factory를 시각적으로 작성하는 방법을 알아봅니다"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/9/2018
ms.author: shlo
ms.openlocfilehash: 3e67665facba78c4ca8e2317f0323b4c5c02a49c
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2018
---
# <a name="visually-author-data-factories"></a>데이터 팩터리 시각화
Azure Data Factory UX 환경을 사용하면 코드 한 줄 작성하지 않고도, 데이터 팩터리에서 리소스를 시각적으로 작성하고 배포할 수 있습니다. 이 코드 없는 인터페이스를 사용하여 파이프라인 캔버스에서 작업을 끌어다 놓고, 테스트 실행을 수행하고, 반복적으로 디버그하고, 파이프라인 실행을 배포 및 모니터링할 수 있습니다. ADF UX 도구는 다음 두 가지 방법으로 사용하도록 선택할 수 있습니다.

1. Data Factory 서비스에 직접 작동
2. 공동 작업, 소스 제어 또는 버전 관리를 위해 한 VSTS Git 통합 구성

## <a name="authoring-with-data-factory"></a>Data Factory를 사용하여 작성
첫 번째 옵션은 Data Factory 모드에서 직접 작성하는 것입니다. 이 방법은 변경된 JSON 엔터티를 저장하는 리포지토리도 없고, 공동 작업 또는 버전 제어에 대해 최적화되지도 않는다는 점에서 VSTS 코드 리포지토리를 통한 작성 방식과 다릅니다.

![데이터 팩터리 구성](media/author-visually/configure-data-factory.png)

Data Factory 모드에는 '게시' 모드만 있습니다. 변경한 내용은 Data Factory 서비스에 직접 게시됩니다.

![Data Factory 게시](media/author-visually/data-factory-publish.png)

## <a name="authoring-with-vsts-git-integration"></a>VSTS Git 통합을 사용하여 작성
VSTS Git 통합을 사용한 작성에서는 Data Factory 파이프라인을 작성하면서 소스 제어 및 공동 작업을 수행할 수 있습니다. 사용자는 소스 제어, 공동 작업 및 버전 관리 등을 위해 VSTS Git 계정 리포지토리에 데이터 팩터리를 연결할 수 있습니다. 단일 VSTS GIT 계정에 여러 리포지토리가 있을 수 있습니다. 그러나 하나의 VSTS Git 리포지토리만 단일 데이터 팩터리에 연결할 수 있습니다. VSTS 계정 및 리포지토리가 아직 없으면 [여기에서](https://docs.microsoft.com/en-us/vsts/accounts/create-account-msa-or-work-student) 하나 만듭니다.

### <a name="configure-vsts-git-repo-with-azure-data-factory"></a>Azure Data Factory를 사용하여 VSTS Git 리포지토리 구성
두 가지 방법으로 데이터 팩터리를 사용해서 VSTS GIT 리포지토리를 구성할 수 있습니다.

#### <a name="method-1-lets-get-started-page"></a>방법 1: '시작하기' 페이지

'시작하기' 페이지로 가서 ‘코드 리포지토리 구성'을 클릭합니다.

![코드 리포지토리 구성](media/author-visually/configure-repo.png)

여기에서 리포지토리 설정을 구성하기 위한 측면 패널이 나타납니다.

![리포지토리 설정 구성](media/author-visually/repo-settings.png)
* **리포지토리 유형**: Visual Studio Team Services Git(현재, Github는 지원되지 않음)
* **Visual Studio Team Services 계정**: 계정 이름은 https://{account name}.visualstudio.com에서 찾을 수 있습니다. [여기에서](https://www.visualstudio.com/team-services/git/) VSTS 계정으로 로그인하고 Visual Studio 프로필에 액세스하여 리포지토리 및 프로젝트를 확인합니다.
* **ProjectName:** 프로젝트 이름은 https://{account name}.visualstudio.com/{project name}에서 찾을 수 있습니다.
* **RepositoryName:** 리포지토리 이름입니다. VSTS 프로젝트는 프로젝트 확장에 따라 소스 코드를 관리하기 위한 Git 리포지토리를 포함합니다. 새 리포지토리를 만들거나 프로젝트에 이미 있는 기존 리포지토리를 사용합니다.
* **리포지토리에 기존 Data Factory 리소스 가져오기**: 이 확인란을 선택하여 UX 캔버스에서 작성된 현재 데이터 팩터리 리소스를 JSON 형식의 연결된 VSTS GIT 리포지토리로 가져올 수 있습니다. 이 작업의 각 리소스를 개별적으로 내보냅니다(즉, 연결된 서비스 및 데이터 집합을 별도 Json으로 내보냄).    이 확인란의 선택을 취소하면 기존 리소스를 Git 리포지토리로 가져오지 않습니다.

#### <a name="method-2-from-authoring-canvas"></a>방법 2: 작성 캔버스에서

'작성 캔버스'에서 데이터 팩터리 이름 아래의 'Data Factory' 드롭다운 메뉴를 클릭합니다. 그런 다음 '코드 리포지토리 구성'을 클릭합니다. **방법 1**과 마찬가지로, 리포지토리 설정을 구성하기 위한 측면 패널이 나타납니다. 설정에 대한 내용은 이전 섹션을 참조하세요.

![코드 리포지토리 구성 2](media/author-visually/configure-repo-2.png)

### <a name="version-control"></a>버전 제어
소스 제어라고도 하는 버전 제어 시스템을 통해 개발자는 코드에 대해 공동 작업을 수행하고, 코드 베이스의 변경 내용을 추적할 수 있습니다. 소스 제어는 개발자가 여러 명인 프로젝트에 반드시 필요한 도구입니다.

데이터 팩터리와 연결된 각 VSTS Git 리포지토리에는 마스터 분기가 생성됩니다. 여기에서, VSTS Git 리포지토리에 대한 액세스 권한이 있는 모든 사용자가 변경을 수행할 때 동기화 및 게시의 두 옵션이 제공됩니다.

![동기화 및 게시](media/author-visually/sync-publish.png)

#### <a name="sync"></a>동기화

'동기화'를 클릭하여 마스터 분기에서 로컬 분기로 변경 내용을 끌어오거나, 로컬 분기에서 마스터 분기로 변경 내용을 밀어 넣을 수 있습니다.

![변경 내용 동기화](media/author-visually/sync-change.png)

#### <a name="publish"></a>게시
 마스터 분기의 변경 내용을 Data Factory 서비스에 게시합니다.

> [!NOTE]
> **마스터 분기는 Data Factory 서비스에 배포된 내용을 반영하지 않습니다.** 따라서 마스터 분기를 *반드시* Data Factory 서비스에 수동으로 게시해야 합니다.




## <a name="expression-language"></a>식 언어

Azure Data Factory에서 지원하는 식 언어를 사용하여 속성 값을 정의할 때 식을 지정할 수 있습니다. 지원되는 식에 대한 자세한 내용은 [Azure Data Factory의 식 및 함수](control-flow-expression-language-functions.md)를 참조하세요.

UX에서도 속성 값에 식을 지정합니다.

![식 언어](media/author-visually/expression-language.png)

## <a name="parameters"></a>매개 변수
'매개 변수' 탭에서 파이프라인 및 데이터 집합에 대한 매개 변수를 지정할 수 있습니다. 또한 "동적 콘텐츠 추가"를 눌러 속성의 매개 변수를 쉽게 사용할 수 있습니다.

![동적 콘텐츠](media/author-visually/dynamic-content.png)

여기에서, 기존 매개 변수를 사용하거나 속성 값에 새 매개 변수를 지정할 수 있습니다.

![매개 변수](media/author-visually/parameters.png)

## <a name="feedback"></a>사용자 의견
다양한 기능 또는 경험 중인 문제에 대한 의견을 Microsoft로 보내려면 ‘사용자 의견’ 아이콘을 클릭합니다.

![사용자 의견](media/monitor-visually/feedback.png)

## <a name="next-steps"></a>다음 단계

파이프라인 모니터링 및 관리에 대한 자세한 내용은 [프로그래밍 방식으로 파이프라인 모니터링 및 관리](monitor-programmatically.md) 문서를 참조하세요.
