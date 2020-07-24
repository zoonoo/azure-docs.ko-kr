---
title: LUIS apps에 대 한 연속 워크플로
description: Language Understanding 용 DevOps에 대 한 CI/CD 워크플로를 구현 하는 방법 (LUIS)
ms.topic: conceptual
ms.date: 06/5/2020
ms.openlocfilehash: 4cedf0cbe3f9ab675c191f8e4639688bb7916ee0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87072988"
---
# <a name="continuous-integration-and-continuous-delivery-workflows-for-luis-devops"></a>LUIS DevOps에 대 한 지속적인 통합 및 지속적인 업데이트 워크플로

LUIS (Language Understanding) 앱을 개발 하는 소프트웨어 엔지니어는 [소스 제어](luis-concept-devops-sourcecontrol.md), [자동화 된 빌드](luis-concept-devops-automation.md), [테스트](luis-concept-devops-testing.md)및 [릴리스 관리](luis-concept-devops-automation.md#release-management)에 대 한 devops 사례를 적용할 수 있습니다. 이 문서에서는 LUIS에 대해 자동화 된 빌드를 구현 하는 개념을 설명 합니다.

## <a name="build-automation-workflows-for-luis"></a>LUIS에 대 한 자동화 워크플로 빌드

![CI 워크플로](./media/luis-concept-devops-automation/luis-automation.png)

SCM (소스 코드 관리) 시스템에서 자동화 된 빌드 파이프라인이 다음 이벤트에서 실행 되도록 구성 합니다.

1. Pr **워크플로** 는 [끌어오기 요청](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests) (pr)이 발생할 때 트리거됩니다. 이 워크플로는 업데이트가 마스터 분기로 병합 *되기 전에* PR 콘텐츠의 유효성을 검사 합니다.
1. PR에서 변경 내용을 병합 하는 등의 방법으로 업데이트가 마스터 분기로 푸시되는 경우 **CI/CD 워크플로가** 트리거됩니다. 이 워크플로는 마스터 분기에 대 한 모든 업데이트의 품질을 보장 합니다.

**CI/CD 워크플로** 는 다음과 같은 두 가지 보완 개발 프로세스를 결합 합니다.

* CI ( [지속적인 통합](https://docs.microsoft.com/azure/devops/learn/what-is-continuous-integration) )는 공유 리포지토리의 코드를 자주 커밋하고 자동으로 빌드를 수행 하는 엔지니어링 방법입니다. 자동 [테스트](luis-concept-devops-testing.md) 접근 방식과 연결 된 연속 통합을 사용 하면 각 업데이트에 대해 LUIS 앱으로 가져올 수 있으며, 학습 된 앱이 솔루션에 필요한 의도 및 엔터티를 인식할 수 있는지 확인 하는 테스트 그룹을 전달 하는 것을 확인할 수 있습니다.

* CD ( [지속적인](https://docs.microsoft.com/azure/devops/learn/what-is-continuous-delivery) 업데이트)는 더 자세한 테스트를 수행할 수 있는 환경에 응용 프로그램을 자동으로 배포 하기 위해 연속 통합 개념을 추가로 사용 합니다. CD를 사용 하면 가능한 한 빨리 변경에서 발생 하는 예측할 수 없는 문제를 조기에 알아보고, 테스트 검사의 간격에 대해 알아볼 수 있습니다.

연속 통합 및 지속적인 업데이트의 목표는 "master is always 제공 가능한" 인지 확인 하는 것입니다. LUIS 앱의 경우,이는 필요한 경우 master branch LUIS 앱에서 모든 버전을 가져와 프로덕션 환경에 제공 하는 것을 의미 합니다.

### <a name="tools-for-building-automation-workflows-for-luis"></a>LUIS에 대 한 자동화 워크플로를 작성 하기 위한 도구

빌드 자동화 워크플로를 만드는 데 사용할 수 있는 다양 한 빌드 자동화 기술이 있습니다. 이러한 모든 작업에는 빌드 서버에서 실행할 수 있도록 CLI (명령줄 인터페이스) 또는 REST 호출을 사용 하 여 단계를 스크립팅할 수 있어야 합니다.

LUIS에 대 한 자동화 워크플로를 작성 하려면 다음 도구를 사용 합니다.

* [Bot Framework TOOLS LUIS는 CLI](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS) 를 사용 하 여 LUIS 앱과 버전에서 작업 하 고, LUIS 서비스 내에서이를 학습, 테스트 및 게시 합니다.

* Azure 구독을 쿼리하고 LUIS 작성 및 예측 키를 인출 하 고 automation 인증에 사용 되는 Azure [서비스 주체](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest) 를 만드는 것을 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 합니다.

* [Nlu. ](https://github.com/microsoft/NLU.DevOps) [LUIS 앱을 테스트](luis-concept-devops-testing.md) 하 고 테스트 결과를 분석 하기 위한 devops 도구입니다.

### <a name="the-pr-workflow"></a>PR 워크플로

앞서 언급 했 듯이, 개발자가 PR을 발생 시켜 기능 분기에서 마스터 분기로 병합 될 변경 내용을 제안 하는 경우이 워크플로를 실행 하도록 구성 합니다. 이는 PR의 변경 내용 품질이 마스터 분기로 병합 되기 전에 확인 하는 것입니다.

이 워크플로는 다음을 수행 해야 합니다.

* PR에서 소스를 가져와서 임시 LUIS 앱을 만듭니다 `.lu` .
* LUIS app 버전을 학습 하 고 게시 합니다.
* 모든 [단위 테스트](luis-concept-devops-testing.md) 를 실행 합니다.
* 모든 테스트가 통과 하면 워크플로를 전달 하 고, 그렇지 않으면 실패 합니다.
* 임시 앱을 정리 하 고 삭제 합니다.

SCM에서 지 원하는 경우 PR을 완료 하기 전에이 워크플로를 성공적으로 완료 해야 하도록 분기 보호 규칙을 구성 합니다.

### <a name="the-master-branch-cicd-workflow"></a>Master branch CI/CD 워크플로

PR의 업데이트를 마스터 분기로 병합 한 후이 워크플로를 실행 하도록 구성 합니다. 이는 업데이트를 테스트 하 여 마스터 분기의 품질 막대를 높게 유지 하는 것입니다. 업데이트가 품질 막대를 충족 하는 경우이 워크플로는 추가 심층 테스트를 수행할 수 있는 환경에 새 LUIS app 버전을 배포 합니다.

이 워크플로는 다음을 수행 해야 합니다.

* 업데이트 된 소스 코드를 사용 하 여 기본 LUIS 앱 (master 분기에 대해 유지 관리 하는 앱)에서 새 버전을 빌드합니다.

* LUIS app 버전을 학습 하 고 게시 합니다.

  > [!NOTE]
  > [자동화 된 빌드 워크플로에서 테스트를 실행](luis-concept-devops-testing.md#running-tests-in-an-automated-build-workflow) 하는 방법에 설명 된 대로 LUIS 앱 버전을 테스트 하 여 nlu와 같은 도구를 게시 해야 합니다. DevOps에서 액세스할 수 있습니다. LUIS는 LUIS 앱에 대해 두 개의 명명 된 게시 슬롯, *스테이징* 및 *프로덕션* 만 지원 하지만 [버전을 직접 게시](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish) 하 고 [버전을 기준으로 쿼리할](https://docs.microsoft.com/azure/cognitive-services/luis/luis-migration-api-v3#changes-by-slot-name-and-version-name)수도 있습니다. 자동화 워크플로의 직접 버전 게시를 사용 하 여 명명 된 게시 슬롯 사용으로 제한 되지 않도록 합니다.

* 모든 [단위 테스트](luis-concept-devops-testing.md)를 실행 합니다.

* 필요에 따라 [배치 테스트](luis-concept-devops-testing.md#how-to-do-unit-testing-and-batch-testing) 를 실행 하 여 LUIS app 버전의 품질 및 정확도를 측정 하 고이를 일부 기준선과 비교 합니다.

* 테스트가 성공적으로 완료 되 면 다음을 수행 합니다.
  * 리포지토리의 원본에 태그를 합니다.
  * 추가 테스트를 위해 CD (지속적인 업데이트) 작업을 실행 하 여 LUIS app 버전을 환경에 배포 합니다.

### <a name="continuous-delivery-cd"></a>지속적인 업데이트 (CD)

CI/CD 워크플로의 CD 작업은 빌드 및 자동화 된 단위 테스트 성공 시 조건에 따라 실행 됩니다. 작업은 더 많은 테스트를 수행할 수 있는 환경에 LUIS 응용 프로그램을 자동으로 배포 하는 것입니다.

LUIS 앱을 배포 하는 최상의 방법에 대 한 권장 솔루션은 없으며, 프로젝트에 적절 한 프로세스를 구현 해야 합니다. [LUIS DevOps 템플릿](https://github.com/Azure-Samples/LUIS-DevOps-Template) 리포지토리는이를 위한 간단한 솔루션을 구현 합니다 .이 솔루션은 [새 LUIS app 버전](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-publish-app) 을 *프로덕션* 게시 슬롯에 게시 합니다. 이 방법은 간단한 설정에 적합 합니다. 그러나 *개발*, *스테이징* 및 *UAT*같이 여러 다른 프로덕션 환경을 동시에 지원 해야 하는 경우에는 앱 당 두 개의 명명 된 게시 슬롯의 한도가 충분 하지 않은 것으로 입증 됩니다.

앱 버전을 배포 하는 다른 옵션은 다음과 같습니다.

* 직접 버전 끝점에 게시 된 앱 버전을 그대로 유지 하 고 필요에 따라 직접 버전 끝점을 사용 하 여 다운스트림 프로덕션 환경을 구성 하는 프로세스를 구현 합니다.
* 각 프로덕션 환경에 대해 서로 다른 LUIS 앱을 유지 관리 하 고 `.lu` , 대상 프로덕션 환경에 대 한 LUIS 앱에서 새 버전으로을 가져와서 학습 하 고 게시 하는 자동화 단계를 작성 합니다.
* 테스트 된 LUIS app 버전을 [LUIS docker 컨테이너로](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto?tabs=v3) 내보내고 LUIS 컨테이너를 Azure [container instances](https://docs.microsoft.com/azure/container-instances/)에 배포 합니다.

## <a name="release-management"></a>릴리스 관리

일반적으로 개발 및 스테이징 등의 비프로덕션 환경 으로만 지속적으로 배달 하는 것이 좋습니다. 대부분의 팀은 프로덕션 환경에 배포 하기 위한 수동 검토 및 승인 프로세스가 필요 합니다. 프로덕션 배포의 경우 개발 팀의 주요 사용자를 지원 하거나 트래픽이 낮은 기간에 사용할 때 이러한 상황이 발생 하는지 확인 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

* [GitHub를 사용 하 여 LUIS에 대 한 DevOps를 구현](luis-how-to-devops-with-github.md) 하는 방법 알아보기
* [NLU를 사용 하 여 GitHub 작업 워크플로를 작성 하는 방법을 알아봅니다. DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template/blob/master/docs/4-pipeline.md)
