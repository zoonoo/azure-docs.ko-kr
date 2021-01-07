---
title: GitHub를 사용 하 여 DevOps를 적용 하는 방법 LUIS
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) 및 GitHub를 사용 하 여 DevOps를 적용 합니다.
services: cognitive-services
author: andycw
manager: cmayo
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/5/2020
ms.author: anwigley
ms.openlocfilehash: b733e90b69be4e2bd458be5486564747ed4cca78
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018874"
---
# <a name="apply-devops-to-luis-app-development-using-github-actions"></a>GitHub Actions를 사용하여 LUIS 앱 개발에 DevOps 적용

LUIS에 대 한 DevOps 및 소프트웨어 엔지니어링 모범 사례를 구현 하는 전체 솔루션에 대 한 [LUIS devops 템플릿](https://github.com/Azure-Samples/LUIS-DevOps-Template) 리포지토리로 이동 합니다. 이 템플릿 리포지토리를 사용 하 여 고유한 프로젝트에 대해 [소스 제어](luis-concept-devops-sourcecontrol.md), [자동화 된 빌드](luis-concept-devops-automation.md), [테스트](luis-concept-devops-testing.md)및 [릴리스 관리](luis-concept-devops-automation.md#release-management) 를 가능 하 게 하는 CI/CD 워크플로 및 방법에 대 한 기본 제공 지원을 통해 고유한 리포지토리를 만들 수 있습니다.

## <a name="the-luis-devops-template-repo"></a>LUIS DevOps 템플릿 리포지토리

[LUIS DevOps 템플릿 리포지토리](https://github.com/Azure-Samples/LUIS-DevOps-Template) 는 다음을 수행 하는 방법을 안내 합니다.

* **템플릿 리포지토리를 복제** 합니다. 사용자 고유의 GitHub 리포지토리에 템플릿을 복사 합니다.
* **LUIS 리소스 구성** -Azure에서 연속 통합 워크플로에 사용 되는 [LUIS 작성 및 예측 리소스](./luis-how-to-azure-subscription.md) 를 만듭니다.
* **Ci/cd 워크플로 구성** -CI/cd 워크플로에 대 한 매개 변수를 구성 하 고 [GitHub 비밀](https://help.github.com/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets)에 저장 합니다.
* **["Dev inner loop"](https://mitchdenny.com/the-inner-loop/) 를 단계별로 안내 합니다** . 개발자는 개발 분기에서 작업 하는 동안 샘플 LUIS 앱을 업데이트 하 고, 업데이트를 테스트 한 후 변경 내용을 제안 하 고 검토 승인을 검색 하기 위해 끌어오기 요청을 발생 시킵니다.
* **CI/CD 워크플로 실행** -GitHub 작업을 사용 하 여 [LUIS 앱을 빌드 및 테스트 하는 연속 통합 워크플로를](luis-concept-devops-automation.md) 실행 합니다.
* **자동화 된 테스트 수행** - [LUIS 앱에 대 한 자동화 된 일괄 처리 테스트](luis-concept-devops-testing.md) 를 수행 하 여 앱의 품질을 평가 합니다.
* **LUIS app** -LUIS 앱을 게시 하는 [지속적인 업데이트 (CD) 작업](luis-concept-devops-automation.md#continuous-delivery-cd) 을 배포 합니다.
* **자체 프로젝트에서 리포지토리 사용** -사용자 고유의 LUIS 응용 프로그램으로 리포지토리를 사용 하는 방법을 설명 합니다.

## <a name="next-steps"></a>다음 단계

* [LUIS devops 템플릿 리포지토리](https://github.com/Azure-Samples/LUIS-DevOps-Template) 를 사용 하 여 devops를 고유한 프로젝트에 적용할 수 있습니다.
* [LUIS에 대한 소스 제어 및 분기 전략](luis-concept-devops-sourcecontrol.md)
* [LUIS DevOps에 대한 테스트](luis-concept-devops-testing.md)
* [LUIS DevOps에 대 한 자동화 워크플로](luis-concept-devops-automation.md)
