---
title: Custom Speech 용 CI/CD-음성 서비스
titleSuffix: Azure Cognitive Services
description: Custom Speech 및 CI/CD 워크플로를 사용 하 여 DevOps를 적용 합니다. 프로젝트에 대 한 기존 DevOps 솔루션을 구현 합니다.
services: cognitive-services
author: KatieProchilo
manager: cmayomsft
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/09/2020
ms.author: kaprochi
ms.openlocfilehash: 46bdc314e7aa0002937e808d7982f43c8e725d6f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91357474"
---
# <a name="cicd-for-custom-speech"></a>Custom Speech용 CI/CD

학습 및 테스트 데이터에 업데이트를 적용할 때 Custom Speech 모델을 지속적으로 향상 시킬 수 있도록 자동화 된 학습, 테스트 및 릴리스 관리를 구현 합니다. CI/CD 워크플로를 효과적으로 구현 하 여 가장 적합 한 Custom Speech 모델에 대 한 끝점을 항상 사용할 수 있는지 확인할 수 있습니다.

CI ( [지속적인 통합](https://docs.microsoft.com/azure/devops/learn/what-is-continuous-integration) )는 공유 리포지토리에서 업데이트를 자주 커밋하고이에 대 한 자동화 된 빌드를 수행 하는 엔지니어링 방법입니다. Custom Speech에 대 한 CI 워크플로는 데이터 원본에서 새 모델을 학습 하 고 새 모델에 대 한 자동화 된 테스트를 수행 하 여 이전 모델 보다 더 잘 수행 되도록 합니다.

CD ( [지속적인](https://docs.microsoft.com/azure/devops/learn/what-is-continuous-delivery) 업데이트)는 CI 프로세스에서 모델을 사용 하 고 향상 된 각 Custom Speech 모델에 대 한 끝점을 만듭니다. CD를 사용 하면 끝점을 쉽게 솔루션에 통합할 수 있습니다.

사용자 지정 CI/CD 솔루션은 가능 하지만 미리 작성 된 강력한 솔루션의 경우 GitHub 작업을 사용 하 여 CI/CD 워크플로를 실행 하는 [음성 DevOps 템플릿 리포지토리](https://github.com/Azure-Samples/Speech-Service-DevOps-Template)를 사용 합니다.

## <a name="cicd-workflows-for-custom-speech"></a>Custom Speech에 대 한 CI/CD 워크플로

이러한 워크플로의 목적은 각 Custom Speech 모델이 이전 빌드 보다 인식 정확도를 향상 하는지 확인 하는 것입니다. 테스트 및/또는 학습 데이터에 대 한 업데이트가 정확성을 향상 시키는 경우 이러한 워크플로는 새 Custom Speech 끝점을 만듭니다.

GitHub 및 Azure DevOps와 같은 git 서버는 병합, 끌어오기 요청 등의 특정 Git 이벤트가 발생 하는 경우 자동화 된 워크플로를 실행할 수 있습니다. 예를 들어 테스트 데이터에 대 한 업데이트가 *master* 분기로 푸시되는 경우 CI 워크플로를 트리거할 수 있습니다. 다른 Git 서버에는 서로 다른 도구가 있지만 빌드 서버에서 실행할 수 있도록 CLI (명령줄 인터페이스) 명령을 사용할 수 있습니다.

이 과정에서 워크플로는 데이터, 테스트, 테스트 파일, 모델 및 끝점의 이름을 지정 하 고 저장 해야 합니다. 또한 테스트 데이터와 학습 데이터를 업데이트 한 후에 생성 된 것을 쉽게 확인할 수 있도록 이러한 자산의 이름을 결정 하는 것이 유용 합니다.

### <a name="ci-workflow-for-testing-data-updates"></a>데이터 업데이트 테스트를 위한 CI 워크플로

CI/CD 워크플로의 주요 목적은 학습 데이터를 사용 하 여 새 모델을 작성 하 고 테스트 데이터를 사용 하 여 해당 모델을 테스트 하 여 이전 최적 모델 ("벤치 마크 모델")과 비교 하 여 WER ( [단어 오류 요금](how-to-custom-speech-evaluate-data.md#what-is-word-error-rate-wer) )가 향상 되었는지 여부를 설정 하는 것입니다. 새 모델을 더 잘 수행 하는 경우 향후 모델을 비교할 새 벤치 마크 모델이 됩니다.

데이터 업데이트 테스트를 위한 CI 워크플로는 수정 된 WER를 계산 하기 위해 업데이트 된 테스트 데이터로 현재 벤치 마크 모델을 다시 테스트 해야 합니다. 이렇게 하면 새 모델의 WER를 벤치 마크의 WER과 비교할 때 두 모델 모두 동일한 테스트 데이터에 대해 테스트 되 고 like와 like를 비교 하 게 됩니다.

이 워크플로는 테스트 데이터 및 다음에 대 한 업데이트를 트리거합니다.

- 업데이트 된 테스트 데이터에 대 한 벤치 마크 모델을 테스트 합니다.
- 업데이트 된 데이터를 사용 하 여 벤치 마크 모델의 WER를 포함 하는 테스트 출력을 저장 합니다.
- 이러한 테스트에서 WER은 향후 모델을 더 이상 이길 수 있는 새로운 벤치 마크 WER이 됩니다.
- CD 워크플로는 테스트 데이터에 대 한 업데이트를 실행 하지 않습니다.

### <a name="ci-workflow-for-training-data-updates"></a>데이터 업데이트 학습을 위한 CI 워크플로

학습 데이터에 대 한 업데이트는 사용자 지정 모델에 대 한 업데이트를 나타냅니다.

이 워크플로는 학습 데이터 및 다음에 대 한 업데이트를 트리거합니다.

- 업데이트 된 학습 데이터로 새 모델을 학습 합니다.
- 테스트 데이터에 대해 새 모델을 테스트 합니다.
- WER를 포함 하는 테스트 출력을 저장 합니다.
- 새 모델에서 wer를 벤치 마크 모델에서 WER와 비교 합니다.
- WER이 개선 되지 않으면 워크플로를 중지 합니다.
- WER이 개선 되 면 CD 워크플로를 실행 하 여 Custom Speech 끝점을 만듭니다.

### <a name="cd-workflow"></a>CD 워크플로

학습 데이터를 업데이트 한 후 모델의 인식을 향상 시키려면 CD 워크플로를 자동으로 실행 하 여 해당 모델에 대 한 새 끝점을 만들고 해당 끝점을 솔루션에서 사용할 수 있도록 설정 해야 합니다.

#### <a name="release-management"></a>릴리스 관리

대부분의 팀은 프로덕션 환경에 배포 하기 위한 수동 검토 및 승인 프로세스가 필요 합니다. 프로덕션 배포의 경우 개발 팀의 주요 사용자를 지원 하거나 트래픽이 낮은 기간에 사용할 때 이러한 상황이 발생 하는지 확인 하는 것이 좋습니다.

### <a name="tools-for-custom-speech-workflows"></a>Custom Speech 워크플로 용 도구

Custom Speech에 대 한 CI/CD 자동화 워크플로에 대 한 다음 도구를 사용 합니다.

- Azure 서비스 주체 인증을 만들고 azure 구독을 쿼리 하 고 테스트 결과를 Azure Blob에 저장 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 합니다.
- 명령줄 또는 자동화 된 워크플로에서 음성 서비스와 상호 작용 하는 [Azure SPEECH CLI](spx-overview.md) .

## <a name="devops-solution-for-custom-speech-using-github-actions"></a>GitHub 작업을 사용 하 Custom Speech에 대 한 DevOps 솔루션

Custom Speech에 대해 이미 구현 된 DevOps 솔루션의 경우 [음성 devops 템플릿](https://github.com/Azure-Samples/Speech-Service-DevOps-Template)리포지토리로 이동 합니다. 템플릿의 복사본을 만들고 GitHub 작업을 사용 하 여 테스트, 학습 및 버전 관리를 포함 하는 강력한 DevOps 시스템을 사용 하 여 사용자 지정 모델 개발을 시작 합니다. 리포지토리는 설정 및 워크플로를 설명 하는 데 도움이 되는 샘플 테스트 및 학습 데이터를 제공 합니다. 초기 설치 후 샘플 데이터를 프로젝트 데이터로 바꿉니다.

[Speech DevOps 템플릿 리포지토리](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) 는 다음에 대 한 인프라 및 세부 지침을 제공 합니다.

- 템플릿 리포지토리를 GitHub 계정에 복사한 다음, GitHub 작업 CI/CD 워크플로에 대 한 Azure 리소스 및 [서비스 주체](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) 를 만듭니다.
- "[Dev inner loop](https://mitchdenny.com/the-inner-loop/)"를 살펴봅니다. 기능 분기에서 학습 및 테스트 데이터를 업데이트 하 고, 임시 개발 모델을 사용 하 여 변경 내용을 테스트 하 고, 변경 내용을 제안 하 고 검토 하는 끌어오기 요청을 발생 시킵니다.
- *Master*에 대 한 끌어오기 요청에서 학습 데이터를 업데이트 하는 경우 GITHUB 작업 CI 워크플로를 사용 하 여 모델을 학습 합니다.
- 자동화 된 정확도 테스트를 수행 하 여 모델의 WER ( [오류 요금](how-to-custom-speech-evaluate-data.md#what-is-word-error-rate-wer) )를 설정 합니다. Azure Blob에 테스트 결과를 저장 합니다.
- WER이 개선 되 면 CD 워크플로를 실행 하 여 끝점을 만듭니다.

## <a name="next-steps"></a>다음 단계

다음과 같이 음성을 사용 하는 DevOps에 대해 자세히 알아보세요.

- [Speech devops 템플릿 리포지토리](https://github.com/Azure-Samples/Speech-Service-DevOps-Template) 를 사용 하 여 GitHub 작업으로 Custom Speech devops를 구현할 수 있습니다.
