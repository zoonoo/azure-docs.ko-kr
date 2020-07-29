---
title: Azure Pipelines 팀 데이터 과학 프로세스를 사용 하 여 CI/CD 파이프라인 만들기
description: Docker 및 Kubernetes를 사용 하 여 인공 지능 (AI) 응용 프로그램에 대 한 지속적인 통합 및 지속적인 업데이트 파이프라인을 만듭니다.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=jainr, previous-ms.author=jainr
ms.openlocfilehash: 42433ec419ac9e02077cd0359e18b5114206f27d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "76721832"
---
# <a name="create-cicd-pipelines-for-ai-apps-using-azure-pipelines-docker-and-kubernetes"></a>Azure Pipelines, Docker 및 Kubernetes를 사용 하 여 AI 앱에 대 한 CI/CD 파이프라인 만들기

AI (인공 지능) 응용 프로그램은 사전 학습 기계 학습 (ML) 모델과 함께 포함 된 응용 프로그램 코드입니다. AI 응용 프로그램에는 항상 두 가지 작업 스트림이 있습니다. Data 과학자는 ML 모델을 빌드하고 앱 개발자는 앱을 빌드하고 최종 사용자에 게 노출 하 여 사용할 수 있습니다. 이 문서에서는 ML 모델을 앱 소스 코드에 포함 하는 AI 응용 프로그램에 대 한 CI/CD (지속적인 통합 및 지속적인 업데이트) 파이프라인을 구현 하는 방법을 설명 합니다. 샘플 코드와 자습서에서는 Python Flask 웹 응용 프로그램을 사용 하 고 개인 Azure blob storage 계정에서 미리 학습 된 모델을 가져옵니다. AWS S3 저장소 계정을 사용할 수도 있습니다.

> [!NOTE]
> 다음 프로세스는 CI/CD를 수행 하는 여러 방법 중 하나입니다. 이 도구와 필수 구성 요소에 대 한 대안이 있습니다.

## <a name="source-code-tutorial-and-prerequisites"></a>소스 코드, 자습서 및 필수 구성 요소

GitHub에서 [소스 코드](https://github.com/Azure/DevOps-For-AI-Apps) 및 [자세한 자습서](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) 를 다운로드할 수 있습니다. 자습서의 단계에 따라 사용자 고유의 응용 프로그램에 대 한 CI/CD 파이프라인을 구현 합니다.

다운로드 한 소스 코드 및 자습서를 사용 하려면 다음 필수 구성 요소가 필요 합니다. 

- GitHub 계정에 분기 [소스 코드 리포지토리](https://github.com/Azure/DevOps-For-AI-Apps)
- [Azure DevOps 조직](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Azure CLI](/cli/azure/install-azure-cli)
- [AKS (Kubernetes) 클러스터에 대 한 Azure Container Service](/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) 명령을 실행 하 고 AKS 클러스터에서 구성을 가져옵니다. 
- [ACR (Azure Container Registry) 계정](/azure/container-registry/container-registry-get-started-portal)

## <a name="cicd-pipeline-summary"></a>CI/CD 파이프라인 요약

각각의 새 Git 커밋은 빌드 파이프라인을 시작 합니다. 빌드는 blob storage 계정에서 최신 ML 모델을 안전 하 게 끌어오고 단일 컨테이너에 앱 코드를 사용 하 여 패키지 합니다. 이러한 응용 프로그램 개발 및 데이터 과학 작업을 분리 하면 프로덕션 앱이 항상 최신 ML 모델을 사용 하 여 최신 코드를 실행 하 게 됩니다. 앱에서 테스트를 통과 하는 경우 파이프라인은 ACR의 Docker 컨테이너에 빌드 이미지를 안전 하 게 저장 합니다. 그런 다음 릴리스 파이프라인은 AKS를 사용 하 여 컨테이너를 배포 합니다. 

## <a name="cicd-pipeline-steps"></a>CI/CD 파이프라인 단계

다음 다이어그램 및 단계는 CI/CD 파이프라인 아키텍처를 설명 합니다.

![CI/CD 파이프라인 아키텍처](./media/ci-cd-flask/architecture.png)

1. 개발자는 선택한 IDE에서 응용 프로그램 코드를 사용 합니다.
2. 개발자는 코드를 Azure Repos, GitHub 또는 기타 Git 소스 제어 공급자에 커밋합니다. 
3. 데이터 과학자은 ML 모델을 개발 하는 데 사용 됩니다.
4. 데이터 과학자는 완성 된 모델을 모델 리포지토리 (이 경우 blob storage 계정)에 게시 합니다. 
5. Git 커밋을 기반으로 빌드를 시작 Azure Pipelines.
6. 빌드 파이프라인은 blob storage에서 최신 ML 모델을 가져오고 컨테이너를 만듭니다.
7. 파이프라인은 ACR의 개인 이미지 리포지토리에 빌드 이미지를 푸시합니다.
8. 릴리스 파이프라인은 성공적인 빌드에 따라 실행 됩니다.
9. 파이프라인은 ACR에서 최신 이미지를 가져와서 AKS의 Kubernetes 클러스터에 배포 합니다.
10. 앱에 대 한 사용자 요청은 DNS 서버를 통해 이동 합니다.
11. DNS 서버는 부하 분산 장치에 요청을 전달 하 고 응답을 다시 사용자에 게 보냅니다.

## <a name="see-also"></a>참조

- [TDSP(Team Data Science Process)](/azure/machine-learning/team-data-science-process/)
- [AML(Azure Machine Learning)](/azure/machine-learning/)
- [Azure DevOps](https://azure.microsoft.com/services/devops/)
- [AKS(Azure Kubernetes Service)](/azure/aks/intro-kubernetes)
