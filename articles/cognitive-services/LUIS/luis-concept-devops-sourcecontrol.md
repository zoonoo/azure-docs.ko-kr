---
title: 소스 제어 및 개발 분기 - LUIS
description: 소스 제어에서 LUIS(Language Understanding) 앱을 유지 관리하는 방법입니다. 개발 분기에서 작업하는 동안 LUIS 앱에 업데이트를 적용하는 방법입니다.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 68d88ef667da9f22d3e3a17f10036693fcca0c3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98932520"
---
# <a name="devops-practices-for-luis"></a>LUIS에 대한 DevOps 사례

LUIS(Language Understanding) 앱을 개발하는 소프트웨어 엔지니어는 다음 지침을 수행하여 [소스 제어](luis-concept-devops-sourcecontrol.md), [자동화된 빌드](luis-concept-devops-automation.md), [테스트](luis-concept-devops-testing.md) 및 [릴리스 관리](luis-concept-devops-automation.md#release-management)에 대한 DevOps 사례를 적용할 수 있습니다.

## <a name="source-control-and-branch-strategies-for-luis"></a>LUIS에 대한 소스 제어 및 분기 전략

DevOps의 성공에 따라 달라지는 주요 요소 중 하나는 [소스 제어](/azure/devops/user-guide/source-control)입니다. 개발자는 소스 제어 시스템을 사용하여 코드를 공동 작업하고 변경 내용을 추적할 수 있습니다. 분기를 사용하면 개발자가 코드 기준의 다른 버전 간에 전환하고 팀의 다른 멤버와 독립적으로 작업할 수 있습니다. 개발자가 한 분기에서 다른 분기로 업데이트를 제안하기 위해 PR([끌어오기 요청](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests))을 발생시키는 경우이거나 변경 내용이 병합된 경우 [자동화된 빌드](luis-concept-devops-automation.md)에서 코드를 작성하고 지속적으로 테스트하는 트리거가 될 수 있습니다.

이 문서에 설명된 개념과 지침을 사용 하여 소스 제어 시스템의 변경 내용을 추적하면서 LUIS 앱을 개발하고 다음과 같은 소프트웨어 엔지니어링 모범 사례를 따를 수 있습니다.

- **소스 제어**
  - LUIS 앱에 대한 소스 코드는 사람이 읽을 수 있는 형식입니다.
  - 반복 가능한 방식으로 소스에서 모델을 빌드할 수 있습니다.
  - 소스 코드 리포지토리를 통해 소스 코드를 관리할 수 있습니다.
  - 작성 및 구독 키와 같은 자격 증명 및 비밀은 소스 코드에 저장되지 않습니다.

- **분기 및 병합**
  - 개발자는 독립 분기에서 작업할 수 있습니다.
  - 개발자는 여러 분기에서 동시에 작업할 수 있습니다.
  - 다시 지정 또는 병합을 통해 LUIS 앱에 대한 변경 내용을 한 분기에서 다른 분기로 통합할 수 있습니다.
  - 개발자는 PR을 부모 분기에 병합할 수 있습니다.

- **버전 관리**
  - 큰 애플리케이션의 각 구성 요소는 독립적으로 버전 관리되므로 개발자는 버전 번호를 확인하여 주요 변경 내용 또는 업데이트를 검색할 수 있습니다.

- **코드 검토**
  - PR의 변경 내용은 PR을 수락하기 전에 검토할 수 있도록 사람이 읽을 수 있는 소스 코드로 표시됩니다.

## <a name="source-control"></a>원본 제어

소스 코드 관리 시스템에서 LUIS 앱의 [앱 스키마 정의](./app-schema-definition.md)를 유지 관리하려면 앱의 [LUDown 형식(`.lu`)](/azure/bot-service/file-format/bot-builder-lu-file-format)을 사용합니다. `.lu` 형식은 사람이 읽을 수 있으므로 `.json` 형식보다 선호되며 보다 쉽게 PR을 변경하거나 변경 내용을 검토할 수 있습니다.

### <a name="save-a-luis-app-using-the-ludown-format"></a>LUDown 형식을 사용하여 LUIS 앱 저장

LUIS 앱을 `.lu` 형식으로 저장하고 소스 제어에 배치하려면 다음 중 하나를 수행합니다.

- [LUIS 포털](https://www.luis.ai/)에서 `.lu`로 [앱 버전을 내보내고](./luis-how-to-manage-versions.md#other-actions) 소스 제어 리포지토리에 추가합니다.

- 또는 텍스트 편집기를 사용하여 LUIS 앱에 대한 `.lu` 파일을 만들고 소스 제어 리포지토리에 추가합니다.

> [!TIP]
> LUIS 앱의 JSON 내보내기를 사용하는 경우 이를 [LUDown으로 변환](https://github.com/microsoft/botframework-cli/tree/master/packages/luis#bf-luisconvert)할 수 있습니다.  `--sort` 옵션을 사용하여 의도 및 발화가 사전순으로 정렬되도록 합니다.  
> LUIS 포털에 기본 제공되는 **.LU** 내보내기 기능을 통해 이미 출력이 정렬되어 있습니다.

### <a name="build-the-luis-app-from-source"></a>소스에서 LUIS 앱 빌드

LUIS 앱에서 *소스에서 빌드* 는 [`.lu` 소스를 가져와 새 LUIS 앱 버전을 만들어](./luis-how-to-manage-versions.md#import-version) [버전을 학습](./luis-how-to-train.md)하고 [게시](./luis-how-to-publish-app.md)하는 것을 의미합니다. LUIS 포털 또는 명령줄에서 이 작업을 수행할 수 있습니다.

- LUIS 포털을 사용하여 소스 제어에서 앱의 [`.lu` 버전을 가져오고](./luis-how-to-manage-versions.md#import-version) 앱을 [학습](./luis-how-to-train.md)하고 [게시](./luis-how-to-publish-app.md)합니다.

- 명령줄 또는 CI/CD 워크플로에서 [LUIS용 Bot Framework 명령줄 인터페이스](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS)를 사용하여 소스 제어에서 애플리케이션으로 `.lu` 버전의 앱을 [가져오고](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisversionimport) 앱을 [학습](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luistrainrun)하고 [게시](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish)합니다.

### <a name="files-to-maintain-under-source-control"></a>소스 제어에서 유지 관리할 파일

LUIS 애플리케이션의 다음 파일 유형은 소스 제어에서 유지 관리해야 합니다.

- LUIS 애플리케이션의 `.lu` 파일

- [단위 테스트 정의 파일](luis-concept-devops-testing.md#writing-tests)(발화 및 예상 결과)

- 성능 테스트에 사용되는 [일괄 테스트 파일](./luis-how-to-batch-test.md#batch-test-file)(발화 및 예상 결과)

### <a name="credentials-and-keys-are-not-checked-in"></a>자격 증명 및 키가 체크 인되지 않음

권한 없는 사용자에게 표시될 수 있는 리포지토리에 체크 인하는 파일에는 구독 키 또는 유사한 기밀 값이 포함되지 않습니다. 체크 인을 방지해야 하는 키와 기타 값은 다음과 같습니다.

- LUIS 작성 및 예측 키
- LUIS 작성 및 예측 엔드포인트
- Azure 구독 키
- 액세스 토큰(예: 자동화 인증에 사용되는 Azure [서비스 주체](/cli/azure/ad/sp)에 대한 토큰)

#### <a name="strategies-for-securely-managing-secrets"></a>비밀을 안전하게 관리하기 위한 전략

비밀을 안전하게 관리하는 전략은 다음과 같습니다.

- Git 버전 제어를 사용하는 경우 로컬 파일에 런타임 비밀을 저장하고 [.gitignore](https://git-scm.com/docs/gitignore) 파일에 파일 이름과 일치하는 패턴을 추가하여 파일의 체크 인을 방지할 수 있습니다.
- 자동화 워크플로에서는 해당 자동화 기술에서 제공하는 매개 변수 구성에 안전하게 비밀을 저장할 수 있습니다. 예를 들어 [GitHub Actions](https://github.com/features/actions)를 사용하는 경우 [GitHub 비밀](https://help.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets)에 비밀을 안전하게 저장할 수 있습니다.

## <a name="branching-and-merging"></a>분기 및 병합

Git와 같은 분산 버전 제어 시스템은 팀 멤버가 다른 사용자와 공유하는 개발 분기를 통해 코드 변경 내용을 게시, 공유, 검토 및 반복하는 방법에 유연성을 제공합니다. 팀에 적합한 [Git 분기 전략](/azure/devops/repos/git/git-branching-guidance)을 채택합니다.

어떤 분기 전략을 채택하든, 모든 전략의 주요 원칙은 팀 멤버가 다른 분기에서 진행되는 작업과 별개로 *기능 분기* 내에서 솔루션에 대해 작업을 수행할 수 있다는 것입니다.

LUIS 프로젝트를 사용하여 분기에서 독립적인 작업을 지원하려면:

- **기본 분기에는 자체 LUIS 앱이 있습니다.** 이 앱은 프로젝트에 대한 솔루션의 현재 상태를 나타내며, 현재 활성 버전은 항상 기본 분기에 있는 `.lu` 소스에 매핑되어야 합니다. 이 앱의 `.lu` 소스에 대한 모든 업데이트를 검토하고 테스트하여 언제든지 프로덕션과 같은 빌드 환경에 이 앱을 배포할 수 있도록 해야 합니다. `.lu`에 대한 업데이트가 기능 분기에서 기본 분기로 병합된 경우 LUIS 앱에서 새 버전을 만들고 [버전 번호를 지정](#versioning)해야 합니다.

- **각 기능 분기는 LUIS 앱의 고유한 인스턴스를 사용해야 합니다**. 개발자는 다른 분기에서 작업하는 개발자에게 영향을 주는 위험 없이 기능 분기에서 이 앱을 사용합니다. 이 'dev branch' 앱은 기능 분기가 삭제될 때 삭제해야 하는 작업 복사본입니다.

![Git 기능 분기](./media/luis-concept-devops-sourcecontrol/feature-branch.png)

### <a name="developers-can-work-from-independent-branches"></a>개발자는 독립 분기에서 작업을 수행할 수 있습니다.

개발자는 다음을 수행하여 다른 분기와 독립적으로 LUIS 앱의 업데이트 작업을 수행할 수 있습니다.

1. 기본 분기에서 일반적으로 기본. 또는 개발 분기 전략에 따라 기능 분기를 만듭니다.

1. 기능 분기의 작업을 지원하기 위해서만 [LUIS 포털에서 새 LUIS 앱을 만듭니다](./luis-how-to-start-new-app.md)("*dev branch 앱*").

   * 솔루션의 `.lu` 소스가 이전에 프로젝트의 다른 분기에서 작업을 수행한 후에 저장되어 분기에 이미 있는 경우 `.lu` 파일을 가져와서 dev branch LUIS 앱을 만듭니다.

   * 새 프로젝트에 대한 작업을 시작하는 경우 리포지토리에 기본 LUIS 앱에 대한 `.lu` 소스가 아직 없습니다. 기능 분기 작업을 완료하면 포털에서 dev branch 앱을 내보내 `.lu` 파일을 만들고 PR의 일부로 제출합니다.

1. dev branch 앱의 활성 버전에서 작업하여 필요한 변경 내용을 구현합니다. 모든 기능 분기 작업에 대해 단일 버전의 dev branch 앱에서만 작업하는 것이 좋습니다. Dev branch 앱에서 두 개 이상의 버전을 만드는 경우 PR을 발생시킬 때 체크 인할 변경 내용이 포함된 버전을 추적해야 합니다.

1. 업데이트 테스트 - dev branch 앱 테스트에 대한 자세한 내용은 [LUIS DevOps 테스트](luis-concept-devops-testing.md)를 참조하세요.

1. [버전 목록](./luis-how-to-manage-versions.md)에서 dev branch 앱의 활성 버전을 `.lu`로 내보냅니다.

1. 업데이트를 체크 인하고 업데이트에 대한 동료 평가를 초대합니다. GitHub를 사용하는 경우 [끌어오기 요청](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests)을 발생시킵니다.

1. 변경 내용이 승인되면 기본 분기에 업데이트를 병합합니다. 이때 기본 분기에서 업데이트된 `.lu`를 사용하여 *기본* LUIS 앱의 새 [버전](./luis-how-to-manage-versions.md)을 만듭니다. 버전 설정 시 고려 사항은 [버전 관리](#versioning)를 참조하세요.

1. 기능 분기가 삭제되면 기능 분기 작업을 위해 만든 dev branch LUIS 앱을 삭제하는 것이 좋습니다.

### <a name="developers-can-work-in-multiple-branches-concurrently"></a>개발자는 여러 분기에서 동시에 작업할 수 있습니다.

위에서 설명한 패턴을 따르는 경우 [개발자는 독립 분기에서 작업을 수행](#developers-can-work-from-independent-branches)할 수 있습니다. 각 기능 분기에서 고유한 LUIS 애플리케이션을 사용합니다. 단일 개발자는 현재 작업 중인 분기에 대해 올바른 dev branch LUIS 앱으로 전환하는 한 여러 분기에서 동시에 작업할 수 있습니다.

기능 분기 작업을 위해 만든 dev branch LUIS 앱과 기능 분기에 동일한 이름을 사용하는 것이 좋습니다. 이렇게 하면 잘못된 앱에서 실수로 작업할 가능성이 줄어듭니다.

위에서 언급한 것처럼 간단하게 하기 위해 각 dev branch 앱에서 단일 버전으로 작업하는 것이 좋습니다. 여러 버전을 사용하는 경우 dev branch 앱 간에 전환할 때 올바른 버전을 활성화해야 합니다.

### <a name="multiple-developers-can-work-on-the-same-branch-concurrently"></a>여러 개발자가 동시에 동일한 분기에서 작업할 수 있습니다.

동시에 동일한 기능 분기에서 작업하는 여러 개발자를 지원할 수 있습니다.

- 개발자는 일반적인 방식으로 작업을 진행하는 동안 같은 기능 분기를 체크 아웃하고 자신과 다른 개발자가 제출한 변경 내용을 푸시 및 풀합니다.

- 위에서 설명한 패턴을 따르는 경우 [개발자는 독립 분기에서 작업을 수행](#developers-can-work-from-independent-branches)할 수 있습니다. 이 분기는 고유한 LUIS 애플리케이션을 사용하여 개발을 지원합니다. 'dev branch' LUIS 앱은 기능 분기에서 작업을 시작하는 개발 팀의 첫 번째 멤버에 의해 만들어집니다.

- dev branch LUIS 앱에 [팀 멤버를 기여자로 추가](./luis-how-to-collaborate.md)합니다.

- 기능 분기 작업이 완료되면 [버전 목록](./luis-how-to-manage-versions.md)에서 활성 버전의 dev branch LUIS 앱을 `.lu`로 내보내고 업데이트된 `.lu` 파일을 리포지토리에 저장하고, 변경 내용을 체크 인하고 끌어오기 요청(PR)을 수행합니다.

### <a name="incorporating-changes-from-one-branch-to-another-with-rebase-or-merge"></a>다시 지정하거나 병합을 사용하여 한 분기에서 다른 분기로 변경 내용 통합

다른 분기에서 작업하는 팀의 다른 개발자가 `.lu` 소스를 업데이트하고 기능 분기를 만든 후 기본 분기에 병합할 수 있습니다. 기능 분기 내에서 변경을 계속 수행하기 전에 변경 내용을 작업 버전에 통합할 수 있습니다. 이 작업을 수행하려면 다른 코드 자산과 동일한 방식으로 [기본 분기로 다시 지정하거나 병합](https://git-scm.com/book/en/v2/Git-Branching-Rebasing)합니다. LUDown 형식의 LUIS 앱은 사람이 읽을 수 있으므로 표준 병합 도구를 사용한 병합을 지원합니다.

기능 분기에서 LUIS 앱을 다시 지정하는 경우 다음 팁을 따르세요.

- 다시 지정하거나 병합하기 전에 먼저 포털에서 앱을 다시 내보내 앱에 대한 `.lu` 소스의 로컬 복사본에 LUIS 포털을 사용하여 적용한 모든 최신 변경 내용이 있는지 확인합니다. 이런 방식으로 포털에서 변경하고 아직 내보내지 않은 변경 내용이 손실되지 않도록 할 수 있습니다.

- 병합 중에는 표준 도구를 사용하여 병합 충돌을 해결할 수 있습니다.

- 다시 지정 또는 병합이 완료된 후 앱을 포털에 다시 가져오는 작업을 완료해야 하므로 업데이트된 앱으로 작업하는 동안에는 사용자의 변경 내용을 계속 적용할 수 있습니다.

### <a name="merge-prs"></a>PR 병합

PR이 승인되면 변경 내용을 기본 분기에 병합할 수 있습니다. LUIS 앱의 LUDown 소스에는 특별한 고려 사항이 적용되지 않습니다. 사람이 읽을 수 있으므로 표준 병합 도구를 사용하여 병합을 지원합니다. 모든 병합 충돌은 다른 소스 파일과 동일한 방법으로 해결할 수 있습니다.

PR을 병합한 후에는 정리하는 것이 좋습니다.

- 리포지토리의 분기 삭제

- 기능 분기 작업을 위해 만든 'dev branch' LUIS 앱을 삭제합니다.

애플리케이션 코드 자산을 사용하는 것과 동일한 방식으로 LUIS 앱 업데이트와 함께 제공되는 단위 테스트를 작성해야 합니다. 연속 통합 워크플로를 사용하여 다음을 테스트해야 합니다.

- PR이 병합되기 전 PR의 업데이트
- PR이 승인되고 변경 내용이 기본에 병합된 후의 기본 분기 LUIS 앱

LUIS DevOps 테스트에 대한 자세한 내용은 [LUIS에 대한 DevOps 테스트](luis-concept-devops-testing.md)를 참조하세요. 워크플로 구현에 대한 자세한 내용은 [LUIS DevOps에 대한 자동화 워크플로](luis-concept-devops-automation.md)를 참조하세요.

## <a name="code-reviews"></a>코드 검토

LUDown 형식의 LUIS 앱은 사람이 읽을 수 있으며, 이는 검토에 적합한 PR의 변경 내용에 대한 의사 소통을 지원합니다. 단위 테스트 파일은 LUDown 형식으로도 작성되며 PR에서도 쉽게 검토할 수 있습니다.

## <a name="versioning"></a>버전 관리

애플리케이션은 [Azure Bot Service](/azure/bot-service/bot-service-overview-introduction), [QnA Maker](https://www.qnamaker.ai/), [Azure Speech Service](../speech-service/overview.md) 등에서 실행되는 봇과 같은 항목을 포함할 수 있는 여러 구성 요소로 구성됩니다. 느슨하게 연결된 애플리케이션의 목표를 달성하려면 [버전 제어](/azure/devops/learn/git/what-is-version-control)를 사용하여 각 애플리케이션 구성 요소의 버전을 독립적으로 관리함으로써 개발자가 버전 번호를 확인하여 주요 변경 내용 또는 업데이트를 검색할 수 있도록 합니다. 자체 리포지토리에 유지 관리하는 경우 다른 구성 요소와 독립적으로 LUIS 앱의 버전을 지정하는 것이 더 쉽습니다.

기본 분기에 대한 LUIS 앱에는 버전 관리 체계가 적용되어 있어야 합니다. LUIS 앱에 대한 업데이트를 `.lu`에 병합하는 경우 해당 업데이트된 소스를 기본 분기에 대한 LUIS 앱의 새 버전으로 가져옵니다.

기본 LUIS 앱 버전에 대해 숫자 버전 관리 체계를 사용하는 것이 좋습니다. 예를 들면 다음과 같습니다.

`major.minor[.build[.revision]]`

각 업데이트는 마지막 숫자에서 버전 번호가 증가합니다.

주/부 버전을 사용하여 LUIS 앱 기능의 변경 범위를 나타낼 수 있습니다.

* 주 버전: 새 [의도](./luis-concept-intent.md) 또는 [엔터티](./luis-concept-entity-types.md)에 대한 지원과 같은 중요한 변경
* 부 버전: 중요한 새 학습 후와 같이 이전 버전과 호환되는 사소한 변경
* 빌드: 기능을 변경하지 않고 다른 빌드만 수행

기본 LUIS 앱의 최신 수정 버전에 대한 버전 번호를 확인한 후 새 앱 버전을 빌드 및 테스트하고, 품질 보증 또는 프로덕션과 같은 다양한 빌드 환경에서 사용할 수 있는 엔드포인트에 게시해야 합니다. CI(연속 통합) 워크플로에서 이러한 모든 단계를 자동화하는 것이 좋습니다.

참조
- LUIS 앱을 테스트하고 릴리스하는 CI 워크플로를 구현하는 방법에 대한 자세한 내용은 [자동화 워크플로](luis-concept-devops-automation.md)를 참조하세요.
- LUIS 앱을 배포하는 방법에 대한 자세한 내용은 [릴리스 관리](luis-concept-devops-automation.md#release-management)를 참조하세요.

### <a name="versioning-the-feature-branch-luis-app"></a>'feature branch' LUIS 앱 버전 관리

기능 분기에서 작업을 지원하기 위해 만든 'dev branch' LUIS 앱을 사용하는 경우 작업이 완료되면 앱을 내보내고 PR에 업데이트된 `'lu`를 포함합니다. 리포지토리의 분기 및 'dev branch' LUIS 앱은 PR이 기본으로 병합된 후 삭제해야 합니다. 이 앱은 기능 분기의 작업을 지원하기 위해서만 존재하므로 이 앱 내에서 적용해야 하는 특정 버전 관리 체계가 없습니다.

PR의 변경 내용이 기본에 병합되면(버전 관리를 적용해야 하는 경우) 기본에 대한 모든 업데이트의 버전이 독립적으로 관리됩니다.

## <a name="next-steps"></a>다음 단계

* [LUIS DevOps 테스트](luis-concept-devops-testing.md)에 대해 자세히 알아봅니다.
* [GitHub를 사용하여 LUIS에 대한 DevOps를 구현](luis-how-to-devops-with-github.md)하는 방법을 알아봅니다.
