---
title: GitHub를 사용하여 DevOps를 적용하는 방법 - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS(Language Understanding) 및 GitHub를 사용하여 DevOps를 적용합니다.
services: cognitive-services
author: andycw
manager: cmayo
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/5/2020
ms.author: anwigley
ms.openlocfilehash: 448b3d93ed58e4cfc73da576f0c5871600400ac6
ms.sourcegitcommit: 5c136a01bddfccb2cc9f7e7e7741e2cf2651ddbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/03/2021
ms.locfileid: "111352514"
---
# <a name="apply-devops-to-luis-app-development-using-github-actions"></a>GitHub Actions를 사용하여 LUIS 앱 개발에 DevOps 적용

LUIS에 대한 DevOps 및 소프트웨어 엔지니어링 모범 사례를 구현하는 전체 솔루션을 보려면 [LUIS DevOps 템플릿 리포지토리](https://github.com/Azure-Samples/LUIS-DevOps-Template)로 이동합니다. 이 템플릿 리포지토리를 사용하여 고유한 프로젝트에 대해 LUIS에서 [소스 제어](luis-concept-devops-sourcecontrol.md), [자동화된 빌드](luis-concept-devops-automation.md), [테스트](luis-concept-devops-testing.md) 및 [릴리스 관리](luis-concept-devops-automation.md#release-management)를 가능하게 하는 CI/CD 워크플로 및 방법에 대한 기본 제공 지원을 통해 고유한 리포지토리를 만들 수 있습니다.

## <a name="the-luis-devops-template-repo"></a>LUIS DevOps 템플릿 리포지토리

[LUIS DevOps 템플릿 리포지토리](https://github.com/Azure-Samples/LUIS-DevOps-Template)는 다음을 수행하는 방법을 안내합니다.

* **템플릿 리포지토리 복제** - 사용자 고유의 GitHub 리포지토리에 템플릿을 복사합니다.
* **LUIS 리소스 구성** - Azure에서 연속 통합 워크플로에 사용되는 [LUIS 작성 및 예측 리소스](./luis-how-to-azure-subscription.md)를 만듭니다.
* **CI/CD 워크플로 구성** - CI/CD 워크플로에 대한 매개 변수를 구성하고 [GitHub 비밀](https://help.github.com/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets)에 저장합니다.
* **["dev inner loop"](/dotnet/architecture/containerized-lifecycle/design-develop-containerized-apps/docker-apps-inner-loop-workflow) 안내** - 개발자는 개발 분기에서 작업하는 동안 샘플 LUIS 앱을 업데이트하고, 업데이트를 테스트한 후 변경 내용을 제안하고 검토 승인을 검색하기 위해 끌어오기 요청을 발생시킵니다.
* **CI/CD 워크플로 실행** - GitHub Actions를 사용하여 [LUIS 앱을 빌드 및 테스트하는 연속 통합 워크플로](luis-concept-devops-automation.md)를 실행합니다.
* **자동화 테스트 수행** - [LUIS 앱에 대한 자동화된 일괄 테스트](luis-concept-devops-testing.md)를 수행하여 앱의 품질을 평가합니다.
* **LUIS 앱 배포** - [지속적인 업데이트(CD) 작업](luis-concept-devops-automation.md#continuous-delivery-cd)을 실행하여 LUIS 앱을 게시합니다.
* **자체 프로젝트에서 리포지토리 사용** - 사용자 고유의 LUIS 애플리케이션에서 리포지토리를 사용하는 방법을 설명합니다.

## <a name="next-steps"></a>다음 단계

* [LUIS DevOps 템플릿 리포지토리](https://github.com/Azure-Samples/LUIS-DevOps-Template)를 사용하여 DevOps를 고유한 프로젝트에 적용합니다.
* [LUIS에 대한 소스 제어 및 분기 전략](luis-concept-devops-sourcecontrol.md)
* [LUIS DevOps에 대한 테스트](luis-concept-devops-testing.md)
* [LUIS DevOps에 대한 자동화 워크플로](luis-concept-devops-automation.md)
