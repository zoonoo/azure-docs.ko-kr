---
title: Azure Stream Analytics에 대 한 지속적인 통합 및 배포
description: 이 문서에서는 Azure Stream Analytics에 대 한 CI/CD (지속적인 통합 및 배포) 파이프라인의 개요를 제공 합니다.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: b5057eb8c84e839f504060228986ea759c8bdc3d
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93123170"
---
# <a name="continuous-integration-and-deployment-cicd-for-azure-stream-analytics"></a>Azure Stream Analytics 용 CI/CD (지속적인 통합 및 배포)

원본 제어 통합을 사용 하 여 Azure Stream Analytics 작업을 지속적으로 배포할 수 있습니다. 원본 제어 통합을 사용 하면 코드 업데이트가 Azure에 리소스 배포를 트리거하는 워크플로를 사용할 수 있습니다. 이 문서에서는 CI/CD (연속 통합 및 배포) 파이프라인을 만들기 위한 기본 단계에 대해 간략하게 설명 합니다.

Azure Stream Analytics를 처음 접하는 경우 [Azure Stream Analytics 빠른](stream-analytics-quick-create-portal.md)시작을 시작 하세요.

## <a name="create-a-cicd-pipeline"></a>CI/CD 파이프라인 만들기

이 가이드의 단계를 수행 하 여 Stream Analytics에 대 한 CI/CD 파이프라인을 만듭니다.

1. Azure Stream Analytics 쿼리를 개발 합니다.

   [Visual Studio Code](./quick-create-visual-studio-code.md) 또는 [Visual Studio](stream-analytics-quick-create-vs.md) 용 Azure Stream Analytics 도구를 사용 하 여 [쿼리를 로컬로 개발 하 고 테스트할](develop-locally.md)수 있습니다. [기존 작업](visual-studio-code-explore-jobs.md#export-a-job-to-a-local-project) 을 로컬 프로젝트로 내보낼 수도 있습니다.

2. Git 리포지토리와 같은 소스 제어 시스템에 Azure Stream Analytics 프로젝트를 커밋합니다.

3. [AZURE STREAM ANALYTICS CI/CD 도구](cicd-tools.md) 를 사용 하 여 프로젝트를 빌드하고 배포를 위한 Azure 리소스 관리 템플릿을 생성 합니다.

4. 품질 회귀에 대 한 [자동화 된 스크립트 테스트](cicd-tools.md#automated-test) 를 실행 합니다.

5. Azure에 작업을 자동으로 [배포](cicd-tools.md#deploy-to-azure) 합니다.

## <a name="auto-build-test-and-deploy"></a>자동 빌드, 테스트 및 배포

명령줄을 사용 하 고 [CI/CD 도구를 Azure Stream Analytics](cicd-tools.md) 하 여 자동으로 빌드, 테스트 및 배포할 수 있습니다. [Azure Pipelines](set-up-cicd-pipeline.md)에서 CI/CD 파이프라인을 설정할 수도 있습니다. 파이프라인 관리, 시각화 및 트리거와 같은 고급 기능을 사용 하도록 Azure Pipelines.

## <a name="next-steps"></a>다음 단계

* [CI/CD 도구를 사용 하 여 Azure Stream Analytics 작업의 빌드, 테스트 및 배포 자동화](cicd-tools.md)
* [Azure Pipelines를 사용 하 여 Stream Analytics 작업에 대 한 CI/CD 파이프라인 설정](set-up-cicd-pipeline.md)