---
title: LUIS 앱에 대한 연속 통합 및 지속적인 전달
description: LUIS(Language Understanding)의 DevOps에 대한 CI/CD 워크플로를 구현하는 방법입니다.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/01/2021
ms.author: aahi
author: aahill
ms.manager: nitinme
ms.openlocfilehash: 7079c1ee309db9563142c54eea88ccd4ba6f6e28
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112463642"
---
# <a name="continuous-integration-and-continuous-delivery-workflows-for-luis-devops"></a>LUIS DevOps에 대한 지속적인 통합 및 지속적인 전송

LUIS(Language Understanding) 앱을 개발하는 소프트웨어 엔지니어는 [소스 제어](luis-concept-devops-sourcecontrol.md), [자동화된 빌드](luis-concept-devops-automation.md), [테스트](luis-concept-devops-testing.md)및 [릴리스 관리](luis-concept-devops-automation.md#release-management)에 대한 DevOps 사례를 적용할 수 있습니다. 이 문서에서는 LUIS에 대해 자동화된 빌드를 구현하는 개념을 설명합니다.

## <a name="build-automation-workflows-for-luis"></a>LUIS에 대한 자동화 워크플로 빌드

![CI 워크플로](./media/luis-concept-devops-automation/luis-automation.png)

SCM(소스 코드 관리) 시스템에서 자동화된 빌드 파이프라인을 구성하여 다음 이벤트에서 실행되도록 합니다.

1. [PR(끌어오기 요청](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests))이 발생할 때 트리거되는 **PR 워크플로**. 이 워크플로는 업데이트를 기본 분기에 병합하기 *전에* PR 콘텐츠의 유효성을 검사합니다.
1. PR에서 변경 내용을 병합하는 경우 업데이트를 기본 분기로 푸시할 때 트리거되는 **CI/CD 워크플로**. 이 워크플로는 기본 분기에 대한 모든 업데이트의 품질을 보장합니다.

**CI/CD 워크플로** 는 다음과 같은 두 가지 보완 개발 프로세스를 결합합니다.

* [CI(연속 통합](/devops/develop/what-is-continuous-integration))는 공유 리포지토리의 코드를 자주 커밋하고 이에 대해 자동화된 빌드를 수행하는 엔지니어링 방법입니다. 자동화된 [테스트](luis-concept-devops-testing.md) 접근 방식과 연결된 연속 통합을 사용하면 각 업데이트에 대해 LUDown 소스가 여전히 유효하고 LUIS 앱으로 가져올 수 있음을 확인할 수 있지만 학습된 앱이 솔루션에 필요한 의도 및 엔터티를 인식할 수 있는지 확인하는 테스트 그룹을 전달하는 것도 확인할 수 있습니다.

* [CD(지속적인 업데이트](/devops/deliver/what-is-continuous-delivery))는 더 자세한 테스트를 수행할 수 있는 환경에 애플리케이션을 자동으로 배포하기 위해 연속 통합 개념을 추가로 사용합니다. CD를 통해 가능한 한 빨리 변경에서 발생하는 예측 불가능한 문제를 조기에 확인하고, 테스트 검사의 간격에 대해 확인할 수 있습니다.

연속 통합 및 지속적인 업데이트의 목표는 "기본은 언제나 제공 가능함"을 보장하는 것입니다. LUIS 앱의 경우, 이는 필요한 경우 기본 분기 LUIS 앱에서 모든 버전을 가져와 프로덕션 환경에 제공하는 것을 의미합니다.

### <a name="tools-for-building-automation-workflows-for-luis"></a>LUIS에 대한 자동화 워크플로를 빌드하기 위한 도구

> [!TIP]
> [LUIS DevOps 템플릿 리포지토리](#apply-devops-to-luis-app-development-using-github-actions)에서 DevOps를 구현하기 위한 전체 솔루션을 찾을 수 있습니다.

빌드 자동화 워크플로를 만드는 데 사용할 수 있는 다양한 빌드 자동화 기술이 있습니다. 이러한 모든 기술을 사용하려면 빌드 서버에서 실행할 수 있도록 CLI(명령줄 인터페이스) 또는 REST 호출을 사용하여 단계를 스크립팅할 수 있어야 합니다.

LUIS에 대한 자동화 워크플로를 빌드하려면 다음 도구를 사용합니다.

* [Bot Framework 도구 LUIS CLI](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS) - LUIS 앱과 버전을 사용하고 LUIS 서비스 내에서 이를 학습, 테스트 및 게시합니다.

* [Azure CLI](/cli/azure/) - Azure 구독을 쿼리하고 LUIS 작성 및 예측 키를 인출하고 자동화 인증에 사용되는 Azure [서비스 주체](/cli/azure/ad/sp)를 만듭니다.

* [NLU.DevOps](https://github.com/microsoft/NLU.DevOps) 도구 - [LUIS 앱을 테스트](luis-concept-devops-testing.md)하고 테스트 결과를 분석합니다.

### <a name="the-pr-workflow"></a>PR 워크플로

앞서 언급했듯이, 개발자가 PR을 발생시켜 기능 분기에서 본 분기로 병합될 변경 내용을 제안하는 경우 이 워크플로를 실행하도록 구성합니다. 이는 기본 분기에 병합되기 전에 PR의 변경 내용 품질을 확인하기 위한 것입니다.

이 워크플로는 다음을 수행해야 합니다.

* PR에서 `.lu` 소스를 가져와서 임시 LUIS 앱을 만듭니다.
* LUIS 앱 버전을 학습하고 게시합니다.
* 해당 버전에 대해 모든 [단위 테스트](luis-concept-devops-testing.md)를 실행합니다.
* 모든 테스트가 통과되면 워크플로를 완료하고, 그렇지 않으면 실패합니다.
* 임시 앱을 정리하고 삭제합니다.

SCM에서 지원하는 경우 이 워크플로를 성공적으로 완료해야 PR을 완료할 수 있도록 분기 보호 규칙을 구성합니다.

### <a name="the-main-branch-cicd-workflow"></a>기본 분기 CI/CD 워크플로

PR의 업데이트가 기본 분기에 병합된 후 실행할 이 워크플로를 구성합니다. 업데이트를 테스트하여 기본 분기의 품질 수준을 높게 유지하는 것이 목적입니다. 업데이트가 품질 수준을 충족하는 경우 이 워크플로는 추가 심층 테스트를 수행할 수 있는 환경에 새 LUIS 앱 버전을 배포합니다.

이 워크플로는 다음을 수행해야 합니다.

* 업데이트된 소스 코드를 사용하여 기본 LUIS 앱(기본 분기에 대해 유지 관리하는 앱)에서 새 버전을 빌드합니다.

* LUIS 앱 버전을 학습하고 게시합니다.

  > [!NOTE]
  > [자동화된 빌드 워크플로에서 테스트를 실행](luis-concept-devops-testing.md#running-tests-in-an-automated-build-workflow)하는 방법에 설명된 대로 NLU.DevOps와 같은 도구에서 액세스할 수 있도록 테스트 중인 LUIS 앱 버전을 게시해야 합니다. LUIS는 LUIS 앱에 대해 두 개의 명명된 게시 슬롯 *스테이징* 과 *프로덕션* 만 지원하지만 [버전을 직접 게시](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish)하고 [버전을 기준으로 쿼리](./luis-migration-api-v3.md#changes-by-slot-name-and-version-name)할 수도 있습니다. 자동화 워크플로의 직접 버전 게시를 사용하여 명명된 게시 슬롯 사용으로 제한되지 않도록 합니다.

* 모든 [단위 테스트](luis-concept-devops-testing.md)를 실행합니다.

* 필요에 따라 [일괄 테스트](luis-concept-devops-testing.md#how-to-do-unit-testing-and-batch-testing)를 실행하여 LUIS 앱 버전의 품질 및 정확도를 측정하고 이를 일부 기준과 비교합니다.

* 테스트가 성공적으로 완료되면 다음을 수행합니다.
  * 리포지토리의 소스에 태그를 지정합니다.
  * 추가 테스트를 위해 CD(지속적인 업데이트) 작업을 실행하여 LUIS 앱 버전을 환경에 배포합니다.

### <a name="continuous-delivery-cd"></a>CD(지속적인 업데이트)

CI/CD 워크플로의 CD 작업은 빌드 및 자동화된 단위 테스트 성공 시 조건에 따라 실행됩니다. 해당 작업은 더 많은 테스트를 수행할 수 있는 환경에 LUIS 애플리케이션을 자동으로 배포하는 것입니다.

LUIS 앱을 배포하는 최상의 방법에 대한 권장 솔루션은 없으며, 프로젝트에 적합한 프로세스를 구현해야 합니다. [LUIS DevOps 템플릿](https://github.com/Azure-Samples/LUIS-DevOps-Template) 리포지토리는 이를 위한 간단한 솔루션을 구현합니다. 이 솔루션은 [새 LUIS 앱 버전](./luis-how-to-publish-app.md)을 *프로덕션* 게시 슬롯에 게시하는 것입니다. 이 방법은 간단한 설정에 적합합니다. 그러나 *개발*, *스테이징* 및 *UAT* 와 같이 여러 다른 프로덕션 환경을 동시에 지원해야 하는 경우에는 앱당 두 개의 명명된 게시 슬롯 제한이 충분하지 않습니다.

앱 버전을 배포하는 다른 옵션은 다음과 같습니다.

* 직접 버전 엔드포인트에 게시된 앱 버전을 그대로 유지하고 필요에 따라 직접 버전 엔드포인트를 사용하여 다운스트림 프로덕션 환경을 구성하는 프로세스를 구현합니다.
* 각 프로덕션 환경의 경우 서로 다른 LUIS 앱을 유지 관리하고 대상 프로덕션 환경의 경우 `.lu`를 LUIS 앱의 새 버전으로 가져와서 학습하고 게시하는 자동화 단계를 작성합니다.
* 테스트된 LUIS 앱 버전을 [LUIS Docker 컨테이너](./luis-container-howto.md?tabs=v3)로 내보내고 LUIS 컨테이너를 Azure [Container Instances](../../container-instances/index.yml)에 배포합니다.

## <a name="release-management"></a>릴리스 관리

일반적으로 개발 및 스테이징 등의 비프로덕션 환경에 대해서만 지속적인 업데이트를 수행하는 것이 좋습니다. 대부분의 팀은 프로덕션 환경에 배포하기 위한 수동 검토 및 승인 프로세스가 필요합니다. 프로덕션 배포의 경우 개발 팀의 주요 사용자를 지원할 때 또는 트래픽이 낮은 기간에 수행하도록 할 수 있습니다.


## <a name="apply-devops-to-luis-app-development-using-github-actions"></a>GitHub Actions를 사용하여 LUIS 앱 개발에 DevOps 적용

LUIS에 대한 DevOps 및 소프트웨어 엔지니어링 모범 사례를 구현하는 전체 솔루션을 보려면 [LUIS DevOps 템플릿 리포지토리](https://github.com/Azure-Samples/LUIS-DevOps-Template)로 이동합니다. 이 템플릿 리포지토리를 사용하여 고유한 프로젝트에 대해 LUIS에서 [소스 제어](luis-concept-devops-sourcecontrol.md), 자동화된 빌드, [테스트](luis-concept-devops-testing.md) 및 릴리스 관리를 가능하게 하는 CI/CD 워크플로 및 방법에 대한 기본 제공 지원을 통해 고유한 리포지토리를 만들 수 있습니다.

[LUIS DevOps 템플릿 리포지토리](https://github.com/Azure-Samples/LUIS-DevOps-Template)는 다음을 수행하는 방법을 안내합니다.

* **템플릿 리포지토리 복제** - 사용자 고유의 GitHub 리포지토리에 템플릿을 복사합니다.
* **LUIS 리소스 구성** - Azure에서 연속 통합 워크플로에 사용되는 [LUIS 작성 및 예측 리소스](./luis-how-to-azure-subscription.md)를 만듭니다.
* **CI/CD 워크플로 구성** - CI/CD 워크플로에 대한 매개 변수를 구성하고 [GitHub 비밀](https://help.github.com/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets)에 저장합니다.
* **["dev inner loop"](/dotnet/architecture/containerized-lifecycle/design-develop-containerized-apps/docker-apps-inner-loop-workflow) 안내** - 개발자는 개발 분기에서 작업하는 동안 샘플 LUIS 앱을 업데이트하고, 업데이트를 테스트한 후 변경 내용을 제안하고 검토 승인을 검색하기 위해 끌어오기 요청을 발생시킵니다.
* **CI/CD 워크플로 실행** - GitHub Actions를 사용하여 [LUIS 앱을 빌드 및 테스트하는 연속 통합 워크플로](#build-automation-workflows-for-luis)를 실행합니다.
* **자동화 테스트 수행** - [LUIS 앱에 대한 자동화된 일괄 테스트](luis-concept-devops-testing.md)를 수행하여 앱의 품질을 평가합니다.
* **LUIS 앱 배포** - [지속적인 업데이트(CD) 작업](#continuous-delivery-cd)을 실행하여 LUIS 앱을 게시합니다.
* **자체 프로젝트에서 리포지토리 사용** - 사용자 고유의 LUIS 애플리케이션에서 리포지토리를 사용하는 방법을 설명합니다.

## <a name="next-steps"></a>다음 단계

* [NLU.DevOps를 사용하여 GitHub Actions 워크플로](https://github.com/Azure-Samples/LUIS-DevOps-Template/blob/master/docs/4-pipeline.md)를 작성하는 방법을 알아봅니다.

* [LUIS DevOps 템플릿 리포지토리](https://github.com/Azure-Samples/LUIS-DevOps-Template)를 사용하여 DevOps를 고유한 프로젝트에 적용합니다.
* [LUIS에 대한 소스 제어 및 분기 전략](luis-concept-devops-sourcecontrol.md)
* [LUIS DevOps에 대한 테스트](luis-concept-devops-testing.md)
