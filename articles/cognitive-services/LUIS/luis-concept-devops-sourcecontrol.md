---
title: 소스 제어 및 개발 분기-LUIS
description: 원본 제어에서 Language Understanding (LUIS) 앱을 유지 관리 하는 방법입니다. 개발 분기에서 작업 하는 동안 LUIS 앱에 업데이트를 적용 하는 방법입니다.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/28/2020
ms.openlocfilehash: 25f2c4f4698785326f80c24d3749e7585e85d5bb
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91309509"
---
# <a name="devops-practices-for-luis"></a>LUIS에 대 한 DevOps 방법

LUIS (Language Understanding) 앱을 개발 하는 소프트웨어 엔지니어는 다음 지침에 따라 [소스 제어](luis-concept-devops-sourcecontrol.md), [자동화 된 빌드](luis-concept-devops-automation.md), [테스트](luis-concept-devops-testing.md)및 [릴리스 관리](luis-concept-devops-automation.md#release-management) 에 대 한 devops 사례를 적용할 수 있습니다.

## <a name="source-control-and-branch-strategies-for-luis"></a>LUIS에 대한 소스 제어 및 분기 전략

DevOps의 성공에 따라 달라 지는 주요 요소 중 하나는 [원본 제어](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)에 있습니다. 개발자는 소스 제어 시스템을 사용 하 여 코드를 공동 작업 하 고 변경 내용을 추적할 수 있습니다. 분기를 사용 하면 개발자가 코드 베이스의 다른 버전 간에 전환 하 고 팀의 다른 멤버와 독립적으로 작업할 수 있습니다. 개발자가 한 분기에서 다른 분기의 업데이트를 제안 하기 위해 PR ( [끌어오기 요청](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests) )을 발생 시킬 때 또는 변경 내용이 병합 된 경우 [자동](luis-concept-devops-automation.md) 빌드에서 코드를 작성 하 고 지속적으로 테스트 하는 트리거가 될 수 있습니다.

이 문서에서 설명 하는 개념과 지침을 사용 하 여 소스 제어 시스템의 변경 내용을 추적 하는 동안 LUIS 앱을 개발 하 고 다음과 같은 소프트웨어 엔지니어링 모범 사례를 따를 수 있습니다.

- **소스 제어**
  - LUIS 앱에 대 한 소스 코드는 사람이 읽을 수 있는 형식입니다.
  - 반복 가능한 방식으로 원본에서 모델을 빌드할 수 있습니다.
  - 소스 코드 리포지토리를 통해 소스 코드를 관리할 수 있습니다.
  - 작성 및 구독 키와 같은 자격 증명 및 암호는 소스 코드에 저장 되지 않습니다.

- **분기 및 병합**
  - 개발자는 독립 분기에서 작업을 수행할 수 있습니다.
  - 개발자는 여러 분기에서 동시에 작업할 수 있습니다.
  - 기준 주소 다시 지정 또는 병합을 통해 LUIS 앱에 대 한 변경 내용을 한 분기에서 다른 분기로 통합할 수 있습니다.
  - 개발자는 PR을 부모 분기에 병합할 수 있습니다.

- **버전 관리**
  - 큰 응용 프로그램의 각 구성 요소는 독립적으로 버전 관리 되므로 개발자는 버전 번호를 확인 하 여 주요 변경 내용 또는 업데이트를 검색할 수 있습니다.

- **코드 검토**
  - PR의 변경 내용은 PR을 수락 하기 전에 검토할 수 있는 사람이 읽을 수 있는 소스 코드로 표시 됩니다.

## <a name="source-control"></a>원본 제어

소스 코드 관리 시스템에서 LUIS 앱의 [앱 스키마 정의](https://docs.microsoft.com/azure/cognitive-services/luis/app-schema-definition) 를 유지 하려면 앱의 [ludown 형식 ( `.lu` )](https://docs.microsoft.com/azure/bot-service/file-format/bot-builder-lu-file-format?view=azure-bot-service-4.0)  을 사용 합니다. `.lu` 형식에는 사람이 읽을 수 있으므로 형식을 지정 하는 것이 좋습니다 `.json` . 이렇게 하면 pr의 변경 내용을 보다 쉽게 만들고 검토할 수 있습니다.

### <a name="save-a-luis-app-using-the-ludown-format"></a>LUDown 형식을 사용 하 여 LUIS 앱 저장

LUIS 앱을 형식으로 저장 하 `.lu` 고 소스 제어에 추가 하려면 다음을 수행 합니다.

- 둘 중 하나: LUIS 포털에서 [앱 버전을 내보내고](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions#other-actions) `.lu` 소스 [LUIS portal](https://www.luis.ai/) 제어 리포지토리에 추가 합니다.

- 또는 텍스트 편집기를 사용 하 여 `.lu` LUIS 앱에 대 한 파일을 만들고 소스 제어 리포지토리에 추가 합니다.

> [!TIP]
> LUIS 앱의 JSON 내보내기로 작업 하는 경우 [BOTBUILDER LUIS CLI](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS)를 사용 하 여 [이를 ludown으로 변환할](https://github.com/microsoft/botframework-cli/tree/master/packages/luis#bf-luisconvert) 수 있습니다. 옵션을 사용 하 여 `--sort` 의도 및 길이 발언 사전순으로 정렬 되도록 합니다.  
> 에 유의 **하십시오. ** LUIS 포털에 기본 제공 되는 LU 내보내기 기능은 이미 출력을 정렬 합니다.

### <a name="build-the-luis-app-from-source"></a>원본에서 LUIS 앱 빌드

LUIS 앱의 경우 *원본에서 빌드하려면* [ `.lu` 원본을 가져와 새 LUIS app 버전을 만들어](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions#import-version) [버전을 학습](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) 하 고[게시](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-publish-app)합니다. LUIS 포털에서 또는 명령줄에서이 작업을 수행할 수 있습니다.

- LUIS 포털을 사용 하 여 소스 제어에서 앱 [ `.lu` 버전을 가져오고](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions#import-version) 앱을 [학습](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) 하 고 [게시할](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-publish-app) 수 있습니다.

- 명령줄 또는 CI/CD 워크플로에서 [LUIS 용 Bot Framework 명령줄 인터페이스](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS) 를 사용 하 여 [import](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisversionimport) `.lu` 소스 제어에서 앱 버전을 LUIS 응용 프로그램으로 가져오고 앱을 [학습](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luistrainrun) 하 고 [게시할](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish) 수 있습니다.

### <a name="files-to-maintain-under-source-control"></a>원본 제어에서 유지할 파일

LUIS 응용 프로그램에 대 한 다음 형식의 파일은 소스 제어에서 유지 관리 해야 합니다.

- `.lu` LUIS 응용 프로그램의 파일입니다.

- [단위 테스트 정의 파일](luis-concept-devops-testing.md#writing-tests) (길이 발언 및 예상 결과)

- 성능 테스트에 사용 되는 [배치 테스트 파일](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-batch-test#batch-file-format) (길이 발언 및 예상 결과)

### <a name="credentialsand-keys-are-not-checked-in"></a>자격 증명 및 키가 체크 인 되지 않음

권한 없는 사용자에 게 표시 될 수 있는 리포지토리에 체크 인하는 파일에 구독 키 또는 유사한 기밀 값을 포함 하지 마세요. 체크 인을 방지 해야 하는 키와 기타 값은 다음과 같습니다.

- LUIS 작성 및 예측 키
- LUIS 작성 및 예측 끝점
- Azure 구독 키
- 액세스 토큰 (예: automation 인증에 사용 되는 Azure [서비스 사용자](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest) 에 대 한 토큰)

#### <a name="strategies-for-securely-managing-secrets"></a>비밀을 안전 하 게 관리 하기 위한 전략

비밀을 안전 하 게 관리 하는 전략은 다음과 같습니다.

- Git 버전 제어를 사용 하는 경우 로컬 파일에 런타임 암호를 저장 하 고 .gitignore 파일에 대 한 파일 이름과 일치 하는 패턴을 추가 하 여 파일의 체크 인을 방지할 수 있습니다 [.](https://git-scm.com/docs/gitignore)
- 자동화 워크플로에서는 해당 자동화 기술에서 제공 하는 매개 변수 구성에 안전 하 게 암호를 저장할 수 있습니다. 예를 들어 [Github 작업](https://github.com/features/actions)을 사용 하는 경우 [github 비밀](https://help.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets)에 비밀을 안전 하 게 저장할 수 있습니다.

## <a name="branching-and-merging"></a>분기 및 병합

Git과 같은 분산 버전 제어 시스템은 팀 멤버가 다른 사용자와 공유 하는 개발 분기를 통해 코드 변경 내용을 게시, 공유, 검토 및 반복 하는 방법에 유연성을 제공 합니다. 팀에 적합 한 [Git 분기 전략](https://docs.microsoft.com/azure/devops/repos/git/git-branching-guidance) 을 채택 합니다.

모든 분기 전략을 채택 하는 것은 무엇이 든, 팀 멤버는 다른 분기에서 진행 되는 작업과 별개로 *기능 분기* 내에서 솔루션에 대해 작업을 수행할 수 있다는 것입니다.

LUIS 프로젝트를 사용 하 여 분기에서 독립적인 작업을 지원 하려면 다음을 수행 합니다.

- **마스터 분기에는 자체 LUIS 앱이 있습니다.** 이 앱은 프로젝트에 대 한 솔루션의 현재 상태를 나타내며, 현재 활성 버전은 항상 `.lu` 마스터 분기에 있는 원본에 매핑되어야 합니다. 이 앱 `.lu` 의 원본에 대 한 모든 업데이트를 검토 하 고 테스트 하 여 언제 든 지 프로덕션과 같은 빌드 환경에이 앱을 배포할 수 있도록 해야 합니다. 에 대 한 업데이트가 `.lu` 기능 분기의 마스터로 병합 되는 경우 LUIS 앱에서 새 버전을 만들고 [버전 번호](#versioning)를 지정 해야 합니다.

- **각 기능 분기는 LUIS 앱의 고유한 인스턴스를 사용 해야 합니다**. 개발자는 다른 분기에서 작업 하는 개발자에 게 영향을 주는 위험 없이 기능 분기에서이 앱을 사용 합니다. 이 ' dev branch ' 앱은 기능 분기가 삭제 될 때 삭제 해야 하는 작업 복사본입니다.

![Git 기능 분기](./media/luis-concept-devops-sourcecontrol/feature-branch.png)

### <a name="developers-can-work-from-independent-branches"></a>개발자는 독립 분기에서 작업을 수행할 수 있습니다.

개발자는 다음을 수행 하 여 다른 분기와 독립적으로 LUIS 앱의 업데이트 작업을 수행할 수 있습니다.

1. 주 분기에서 기능 분기 만들기 (일반적으로 마스터 또는 개발에 따라 분기 전략에 따라)

1. 기능 분기의 작업을 지원 하기 위해서만 LUIS 포털 ("*dev branch 앱*") [에서 새 LUIS 앱을 만듭니다](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-start-new-app) .

   * 솔루션의 `.lu` 소스가 이전에 프로젝트의 다른 분기에서 작업을 수행한 후에 저장 되었기 때문에 분기에 이미 있는 경우 해당 파일을 가져와서 dev BRANCH LUIS 앱을 만듭니다 `.lu` .

   * 새 프로젝트에 대 한 작업을 시작 하는 경우 리포지토리에 `.lu` MASTER LUIS 앱의 원본이 아직 없는 것입니다. `.lu`기능 분기 작업을 완료 했을 때 포털에서 dev branch 앱을 내보내 PR의 일부로 제출 하 여 파일을 만듭니다.

1. 개발 분기 앱의 활성 버전에서 작업 하 여 필요한 변경 내용을 구현 합니다. 모든 기능 분기 작업에 대해 단일 버전의 dev branch 앱 에서만 작업 하는 것이 좋습니다. Dev branch 앱에서 두 개 이상의 버전을 만드는 경우 PR을 발생 시킬 때 체크 인할 변경 내용이 포함 된 버전을 추적 해야 합니다.

1. 업데이트 테스트-dev branch 앱 테스트에 대 한 자세한 내용은 [LUIS DevOps 테스트](luis-concept-devops-testing.md) 를 참조 하세요.

1. `.lu` [버전 목록](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions)에서와 같이 개발 분기 앱의 활성 버전을 내보냅니다.

1. 업데이트를 체크 인하고 업데이트에 대 한 피어 검토를 초대 합니다. GitHub를 사용 하는 경우 [끌어오기 요청](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests)을 발생 시킵니다.

1. 변경 내용이 승인 되 면 업데이트를 마스터 분기로 병합 합니다. 이때 master에서 업데이트 된를 사용 하 여 *마스터* LUIS 앱의 새 [버전](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions) 을 만듭니다 `.lu` . 버전 이름 설정 시 고려 사항은 버전 [관리](#versioning) 를 참조 하세요.

1. 기능 분기가 삭제 되 면 기능 분기 작업을 위해 만든 dev branch LUIS 앱을 삭제 하는 것이 좋습니다.

### <a name="developers-can-work-in-multiple-branches-concurrently"></a>개발자는 여러 분기에서 동시에 작업할 수 있습니다.

개발자가 위에서 설명한 패턴을 따르는 경우 [독립 분기에서 작업을 수행할 수](#developers-can-work-from-independent-branches)있습니다. 각 기능 분기에서 고유한 LUIS 응용 프로그램을 사용 합니다. 단일 개발자가 현재 작업 중인 분기에 대 한 올바른 dev branch LUIS 앱으로 전환 하는 한 여러 분기에서 동시에 작업할 수 있습니다.

기능 분기 작업을 위해 만든 dev branch LUIS 앱과 기능 분기에 동일한 이름을 사용 하는 것이 좋습니다. 이렇게 하면 잘못 된 앱에서 실수로 작업할 가능성이 줄어듭니다.

위에서 언급 한 것 처럼 간단 하 게 하기 위해 각 개발 분기 앱에서 단일 버전으로 작업 하는 것이 좋습니다. 여러 버전을 사용 하는 경우 개발 분기 앱 간을 전환할 때 올바른 버전을 활성화 해야 합니다.

### <a name="multiple-developers-can-work-on-the-same-branch-concurrently"></a>여러 개발자가 동시에 동일한 분기에서 작업할 수 있습니다.

동시에 동일한 기능 분기에서 작업 하는 여러 개발자를 지원할 수 있습니다.

- 개발자는 일반적인 방식으로 작업을 진행 하는 동안 같은 기능 분기를 확인 하 고 자신 및 다른 개발자가 제출한 변경 내용을 푸시 및 풀 합니다.

- 위에서 설명한 패턴을 따를 경우 개발자는 [독립적인 분기에서 작업을 수행할 수 있습니다](#developers-can-work-from-independent-branches). 그러면이 분기에서 고유한 LUIS 응용 프로그램을 사용 하 여 개발을 지원 합니다. ' Dev branch ' LUIS 앱은 기능 분기에서 작업을 시작 하는 개발 팀의 첫 번째 멤버에 의해 만들어집니다.

- Dev branch LUIS 앱에 [참가자로 팀 멤버를 추가](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-collaborate) 합니다.

- 기능 분기 작업이 완료 되 면 버전 목록에서와 같이 dev branch LUIS 앱의 활성 버전을 내보내고, 업데이트 된 파일을 리포지토리에 `.lu` 저장 하 고, [versions list](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions) `.lu` 변경 내용을 체크 인하고 PR 합니다.

### <a name="incorporating-changes-from-one-branch-to-another-with-rebase-or-merge"></a>기준 주소 다시 지정 또는 병합을 사용 하 여 한 분기에서 다른 분기로 변경 내용 통합

다른 분기에서 작업 하는 팀의 다른 개발자가 소스를 업데이트 `.lu` 하 고 기능 분기를 만든 후 마스터 분기에 병합할 수 있습니다. 기능 분기 내에서 변경을 계속 하기 전에 변경 내용을 작업 버전에 통합 해야 할 수 있습니다. 이렇게 하려면 다른 코드 자산과 동일한 방법으로 [마스터에 다시 기준으로 병합 하거나 병합](https://git-scm.com/book/en/v2/Git-Branching-Rebasing) 합니다. LUDown 형식의 LUIS 앱은 사람이 읽을 수 있으므로 표준 병합 도구를 사용한 병합을 지원 합니다.

기능 분기에서 LUIS 앱을의 기준 주소 하는 경우 다음 팁을 따르세요.

- 기준 주소를 다시 지정 하거나 병합 하기 전에 `.lu` 먼저 포털에서 앱을 다시 내보내 앱에 대 한 원본의 로컬 복사본에 LUIS 포털을 사용 하 여 적용 한 모든 최신 변경 내용이 있는지 확인 합니다. 이런 방식으로 포털에서 변경한 내용이 손실 되지 않도록 할 수 있습니다.

- 병합 중에는 표준 도구를 사용 하 여 병합 충돌을 해결할 수 있습니다.

- 다시 기준 주소 다시 지정 후에는 병합이 완료 된 후 앱을 다시 포털에 다시 가져오는 작업을 완료 해야 하므로 업데이트 된 앱으로 작업 하는 동안에는 사용자의 변경 내용을 계속 적용할 수 있습니다.

### <a name="merge-prs"></a>Merge Pr

PR이 승인 되 면 변경 내용을 마스터 분기에 병합할 수 있습니다. LUIS 앱의 LUDown 원본에는 특별 한 고려 사항이 적용 되지 않습니다 .이는 사람이 읽을 수 있으므로 표준 병합 도구를 사용 하 여 병합을 지원 합니다. 모든 병합 충돌은 다른 원본 파일과 동일한 방법으로 해결할 수 있습니다.

PR을 병합 한 후에는 정리 하는 것이 좋습니다.

- 리포지토리의 분기 삭제

- 기능 분기 작업을 위해 만든 ' dev branch ' LUIS 앱을 삭제 합니다.

응용 프로그램 코드 자산을 사용 하는 것과 동일한 방식으로 LUIS 앱 업데이트와 함께 제공 되는 단위 테스트를 작성 해야 합니다. 연속 통합 워크플로를 사용 하 여 테스트 해야 합니다.

- PR이 병합 되기 전에 PR의 업데이트
- PR이 승인 되 고 변경 내용이 마스터에 병합 된 후의 master branch LUIS 앱입니다.

LUIS DevOps 테스트에 대 한 자세한 내용은 [LUIS에 대 한 DevOps 테스트](luis-concept-devops-testing.md)를 참조 하십시오. 워크플로 구현에 대 한 자세한 내용은 [LUIS DevOps에 대 한 자동화 워크플로](luis-concept-devops-automation.md)를 참조 하세요.

## <a name="code-reviews"></a>코드 검토

LUDown 형식의 LUIS 앱은 사람이 읽을 수 있으며,이는 검토에 적합 한 PR의 변경 내용에 대 한 통신을 지원 합니다. 단위 테스트 파일은 LUDown 형식으로도 작성 되며 PR 에서도 쉽게 볼 수 있습니다.

## <a name="versioning"></a>버전 관리

응용 프로그램은 [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0), [QnA Maker](https://www.qnamaker.ai/), [Azure Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview)등에서 실행 되는 봇과 같은 항목을 포함할 수 있는 여러 구성 요소로 구성 됩니다. 느슨하게 연결 된 응용 프로그램의 목표를 달성 하려면 [버전 제어](https://docs.microsoft.com/azure/devops/learn/git/what-is-version-control) 를 사용 하 여 응용 프로그램의 각 구성 요소를 독립적으로 버전 관리 함으로써 개발자가 버전 번호를 확인 하 여 주요 변경 내용 또는 업데이트를 검색할 수 있도록 합니다. 자체 리포지토리에 유지 관리 하는 경우 다른 구성 요소와 독립적으로 LUIS 앱의 버전을 지정 하는 것이 더 쉽습니다.

Master 분기에 대 한 LUIS 앱에 버전 관리 체계가 적용 되어 있어야 합니다. LUIS 앱에 대 한 업데이트를 마스터에 병합 하는 경우 `.lu` 업데이트 된 원본을 master 분기에 대 한 LUIS 앱의 새 버전으로 가져옵니다.

Master LUIS app 버전에 대해 숫자 버전 관리 체계를 사용 하는 것이 좋습니다. 예를 들면 다음과 같습니다.

`major.minor[.build[.revision]]`

각 업데이트는 마지막 숫자에서 버전 번호가 증가 합니다.

주/부 버전은 LUIS app 기능의 변경 범위를 나타내는 데 사용할 수 있습니다.

* 주 버전: 새 [의도](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent) 나 [엔터티에](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-entity-types) 대 한 지원과 같은 중요 한 변경 내용
* 부 버전: 중요 한 새 교육 후와 같은 이전 버전과 호환 되는 사소한 변경 내용
* 빌드: 기능을 변경 하지 않고 다른 빌드만 수행 합니다.

Master LUIS 앱의 최신 수정 버전에 대 한 버전 번호를 확인 한 후 새 앱 버전을 빌드 및 테스트 하 고, 품질 보증 또는 프로덕션과 같은 다양 한 빌드 환경에서 사용할 수 있는 끝점에 게시 해야 합니다. CI (연속 통합) 워크플로에서 이러한 모든 단계를 자동화 하는 것이 좋습니다.

다음을 참조하세요.
- LUIS 앱을 테스트 하 고 릴리스 하는 CI 워크플로를 구현 하는 방법에 대 한 자세한 내용은 [자동화 워크플로](luis-concept-devops-automation.md) 를 참조 하세요.
- LUIS 앱을 배포 하는 방법에 대 한 자세한 내용은을 [Release Management](luis-concept-devops-automation.md#release-management) .

### <a name="versioning-the-feature-branch-luis-app"></a>' Feature branch ' LUIS 앱 버전 관리

기능 분기에서 작업을 지원 하기 위해 만든 ' dev branch ' LUIS 앱을 사용 하는 경우 작업이 완료 될 때 앱을 내보내고 PR에 업데이트를 포함 합니다 `'lu` . 리포지토리의 분기 및 ' dev branch ' LUIS 앱은 PR을 master에 병합 한 후 삭제 해야 합니다. 이 앱은 기능 분기의 작업을 지원 하기 위해서만 존재 하므로이 앱 내에서 적용 해야 하는 특정 버전 지정 체계가 없습니다.

PR의 변경 내용이 마스터에 병합 되 면 (버전 관리를 적용 해야 하는 경우) master에 대 한 모든 업데이트의 버전이 독립적으로 관리 됩니다.

## <a name="next-steps"></a>다음 단계

* [LUIS DevOps 테스트](luis-concept-devops-testing.md) 에 대 한 자세한 정보
* [GitHub를 사용 하 여 LUIS에 대 한 DevOps를 구현](luis-how-to-devops-with-github.md) 하는 방법 알아보기
