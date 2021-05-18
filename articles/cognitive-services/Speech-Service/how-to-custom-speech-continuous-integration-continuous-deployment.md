---
title: Custom Speech용 CI/CD - Speech Service
titleSuffix: Azure Cognitive Services
description: Custom Speech 및 CI/CD 워크플로를 사용하여 DevOps를 적용합니다. 자체 프로젝트를 위해 기존 DevOps 솔루션을 구현합니다.
services: cognitive-services
author: KatieProchilo
manager: cmayomsft
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/09/2020
ms.author: kaprochi
ms.openlocfilehash: f7f11ef097d3abee2b4c18c32a1cb215d9fe8ce8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98939888"
---
# <a name="cicd-for-custom-speech"></a>Custom Speech용 CI/CD

학습 및 테스트 데이터에 업데이트를 적용할 때 Custom Speech 모델을 지속적으로 개선할 수 있도록 자동화된 학습, 테스트 및 릴리스 관리를 구현합니다. CI/CD 워크플로의 효과적인 구현을 통해 최상의 성능을 제공하는 Custom Speech 모델에 대한 엔드포인트를 항상 사용할 수 있습니다.

[CI](/azure/devops/learn/what-is-continuous-integration)(연속 통합)는 공유 리포지토리에서 자주 업데이트를 커밋하고 자동 빌드를 수행하는 엔지니어링 방식입니다. Custom Speech를 위한 CI 워크플로는 이전 모델보다 더 나은 성능을 발휘하도록 데이터 원본에서 새 모델을 학습시키고 새 모델에 대한 자동화된 테스트를 수행합니다.

[CD](/azure/devops/learn/what-is-continuous-delivery)(지속적인 업데이트)는 CI 프로세스에서 모델을 가져와 개선된 각 Custom Speech 모델에 대한 엔드포인트를 만듭니다. CD를 사용하면 엔드포인트를 솔루션에 쉽게 통합할 수 있습니다.

사용자 지정 CI/CD 솔루션이 가능하지만 사전 구축된 강력한 솔루션의 경우 GitHub 작업을 사용하여 CI/CD 워크플로를 실행하는 [Speech DevOps 템플릿 리포지토리](https://github.com/Azure-Samples/Speech-Service-DevOps-Template)를 사용합니다.

## <a name="cicd-workflows-for-custom-speech"></a>Custom Speech용 CI/CD 워크플로

이러한 워크플로의 목적은 각 Custom Speech 모델이 이전 빌드보다 더 나은 인식 정확도를 갖도록 하는 것입니다. 테스트 및/또는 학습 데이터에 대한 업데이트로 정확도가 향상되면 이러한 워크플로는 새로운 Custom Speech 엔드포인트를 만듭니다.

GitHub 및 Azure DevOps와 같은 Git 서버는 병합 또는 끌어오기 요청과 같은 특정 Git 이벤트가 발생할 때 자동화된 워크플로를 실행할 수 있습니다. 예를 들어 테스트 데이터에 대한 업데이트가 *기본* 분기로 푸시될 때 CI 워크플로가 트리거될 수 있습니다. Git 서버마다 도구가 다르지만 빌드 서버에서 실행할 수 있도록 CLI(명령줄 인터페이스) 스크립트 명령을 허용합니다.

그 과정에서 워크플로는 데이터, 테스트, 테스트 파일, 모델 및 엔드포인트를 원래 커밋 또는 버전으로 추적할 수 있도록 이름을 지정하고 저장해야 합니다. 또한 테스트 데이터와 학습 데이터를 업데이트한 후 생성된 자산을 쉽게 확인할 수 있도록 자산의 이름을 지정하는 것이 좋습니다.

### <a name="ci-workflow-for-testing-data-updates"></a>데이터 업데이트 테스트를 위한 CI 워크플로

CI/CD 워크플로의 주요 목적은 학습 데이터를 사용하여 새 모델을 구축하고 테스트 데이터를 사용해 해당 모델을 테스트하여 [WER](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy)(단어 오류율)이 이전 최고 성능 모델("벤치마크 모델")에 비해 향상되었는지 여부를 확인하는 것입니다. 새 모델의 성능이 더 향상된 것으로 나타나면 향후 모델을 비교하는 새로운 벤치마크 모델이 됩니다.

데이터 업데이트 테스트를 위한 CI 워크플로는 수정된 WER을 계산하기 위해 업데이트된 테스트 데이터로 현재 벤치마크 모델을 다시 테스트해야 합니다. 이렇게 하면 새 모델의 WER을 벤치마크의 WER과 비교할 때 두 모델이 동일한 테스트 데이터에 대해 테스트되고 비슷한 항목과 비교할 수 있습니다.

이 워크플로는 테스트 데이터 업데이트 시 트리거되어야 하며 다음을 수행합니다.

- 업데이트된 테스트 데이터에 대해 벤치마크 모델을 테스트합니다.
- 업데이트된 데이터를 사용하여 벤치마크 모델의 WER이 포함된 테스트 출력을 저장합니다.
- 이러한 테스트의 WER은 미래 모델이 반드시 능가해야 할 새로운 벤치마크 WER이 됩니다.
- 테스트 데이터에 대한 업데이트에는 CD 워크플로가 실행되지 않습니다.

### <a name="ci-workflow-for-training-data-updates"></a>학습 데이터 업데이트를 위한 CI 워크플로

학습 데이터 업데이트는 사용자 지정 모델 업데이트를 의미합니다.

이 워크플로는 학습 데이터 업데이트 시 트리거되어야 하며 다음을 수행합니다.

- 업데이트된 학습 데이터로 새 모델을 학습시킵니다.
- 테스트 데이터에 대해 새 모델을 테스트합니다.
- WER이 포함된 테스트 출력을 저장합니다.
- 새 모델의 WER을 벤치마크 모델의 WER과 비교합니다.
- WER이 개선되지 않으면 워크플로를 중지합니다.
- WER이 개선되면 CD 워크플로를 실행하여 Custom Speech 엔드포인트를 만듭니다.

### <a name="cd-workflow"></a>CD 워크플로

학습 데이터에 대한 업데이트로 모델의 인식이 개선되면 CD 워크플로가 자동으로 실행되어 해당 모델에 대한 새 엔드포인트를 만들고 해당 엔드포인트를 솔루션에서 사용할 수 있도록 제공해야 합니다.

#### <a name="release-management"></a>릴리스 관리

대부분의 팀은 프로덕션 환경에 배포하기 위한 수동 검토 및 승인 프로세스가 필요합니다. 프로덕션 배포의 경우 개발 팀의 주요 사용자를 지원할 때 또는 트래픽이 낮은 기간에 수행하도록 할 수 있습니다.

### <a name="tools-for-custom-speech-workflows"></a>Custom Speech 워크플로용 도구

Custom Speech용 CI/CD 자동화 워크플로에는 다음 도구를 사용합니다.

- [Azure CLI](/cli/azure/)를 사용하여 Azure 서비스 주체 인증을 만들고, Azure 구독을 쿼리하고, 테스트 결과를 Azure Blob에 저장합니다.
- [Azure Speech CLI](spx-overview.md)는 명령줄 또는 자동화된 워크플로에서 Speech Service와 상호 작용합니다.

## <a name="devops-solution-for-custom-speech-using-github-actions"></a>GitHub 작업을 사용하는 Custom Speech를 위한 DevOps 솔루션

Custom Speech를 위해 이미 구현된 DevOps 솔루션을 다운로드하려면 [Speech DevOps 템플릿 리포지토리](https://github.com/Azure-Samples/Speech-Service-DevOps-Template)로 이동합니다. 템플릿의 사본을 만들고 GitHub 작업을 사용하여 테스트, 학습 및 버전 관리를 포함하는 강력한 DevOps 시스템으로 사용자 지정 모델 개발을 시작합니다. 리포지토리는 설정을 지원하고 워크플로를 설명하는 샘플 테스트 및 학습 데이터를 제공합니다. 초기 설정 후 샘플 데이터를 프로젝트 데이터로 바꿉니다.

[Speech DevOps 템플릿 리포지토리](https://github.com/Azure-Samples/Speech-Service-DevOps-Template)는 다음에 대한 인프라와 자세한 지침을 제공합니다.

- 템플릿 리포지토리를 GitHub 계정에 복사한 다음 GitHub 작업 CI/CD 워크플로에 대한 Azure 리소스 및 [서비스 주체](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)를 만듭니다.
- "[dev inner loop](/dotnet/architecture/containerized-lifecycle/design-develop-containerized-apps/docker-apps-inner-loop-workflow)"를 살펴봅니다. 기능 분기에서 학습 및 테스트 데이터를 업데이트하고, 임시 개발 모델을 사용하여 변경 사항을 테스트하고, 변경 사항을 제안하고 검토하기 위한 끌어오기 요청을 제기합니다.
- 끌어오기 요청에서 학습 데이터가 *기본* 으로 업데이트되면 GitHub 작업 CI 워크플로를 사용하여 모델을 학습시킵니다.
- 자동 정확도 테스트를 수행하여 모델의 [WER](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy)(단어 오류율)을 설정합니다. Azure Blob에 테스트 결과를 저장합니다.
- WER이 개선되면 CD 워크플로를 실행하여 엔드포인트를 만듭니다.

## <a name="next-steps"></a>다음 단계

DevOps와 Speech에 대해 자세히 알아봅니다.

- [Speech DevOps 템플릿 리포지토리](https://github.com/Azure-Samples/Speech-Service-DevOps-Template)를 사용하여 GitHub 작업으로 Custom Speech용 DevOps를 구현합니다.
