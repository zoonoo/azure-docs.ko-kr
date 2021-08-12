---
title: Azure Pipelines를 사용하여 CI/CD 파이프라인 만들기 - Team Data Science Process
description: Docker와 Kubernetes를 사용하여 AI(인공 지능) 애플리케이션용 연속 통합 및 지속적인 업데이트 파이프라인을 만듭니다.
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
ms.openlocfilehash: af7900faff18d526686b80f23b9de3b3bce5ec5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93309602"
---
# <a name="create-cicd-pipelines-for-ai-apps-using-azure-pipelines-docker-and-kubernetes"></a>Azure Pipelines, Docker, Kubernetes를 사용하여 AI 앱용 CI/CD 파이프라인 만들기

AI(인공 지능) 애플리케이션은 미리 학습된 ML(기계 학습) 모델과 함께 포함된 애플리케이션 코드입니다. AI 애플리케이션에는 항상 두 가지 작업 스트림이 있습니다. 데이터 과학자는 ML 모델을 빌드하고, 앱 개발자는 앱을 빌드하여 최종 사용자가 사용할 수 있도록 공개합니다. 이 문서에서는 앱 소스 코드에 ML 모델을 포함하는 AI 애플리케이션용 CI/CD(연속 통합 및 지속적인 업데이트) 파이프라인을 구현하는 방법을 설명합니다. 샘플 코드와 자습서에서는 Python Flask 웹 애플리케이션을 사용하고 프라이빗 Azure Blob Storage 계정에서 미리 학습된 모델을 가져옵니다. AWS S3 스토리지 계정을 사용할 수도 있습니다.

> [!NOTE]
> 다음 프로세스는 CI/CD를 수행하는 여러 방법 중 하나입니다. 이 도구와 필수 조건에 대한 대안이 있습니다.

## <a name="source-code-tutorial-and-prerequisites"></a>소스 코드, 자습서, 필수 조건

GitHub에서 [소스 코드](https://github.com/Azure/DevOps-For-AI-Apps)와 [자세한 자습서](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md)를 다운로드할 수 있습니다. 자습서 단계에 따라 사용자 고유의 애플리케이션용 CI/CD 파이프라인을 구현합니다.

다운로드한 소스 코드와 자습서를 사용하려면 다음 필수 조건을 충족해야 합니다. 

- GitHub 계정에 포크된 [소스 코드 리포지토리](https://github.com/Azure/DevOps-For-AI-Apps)
- [Azure DevOps 조직](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Azure CLI](/cli/azure/install-azure-cli)
- [Azure Container Service for Kubernetes(AKS) 클러스터](/previous-versions/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
- AKS 클러스터에서 명령을 실행하고 구성을 가져오는 [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) 
- [ACR(Azure Container Registry) 계정](../../container-registry/container-registry-get-started-portal.md)

## <a name="cicd-pipeline-summary"></a>CI/CD 파이프라인 요약

Git commit을 실행할 때마다 빌드 파이프라인이 시작됩니다. 빌드는 Blob Storage 계정에서 최신 ML 모델을 안전하게 풀하고 앱 코드와 함께 단일 컨테이너에 패키지합니다. 애플리케이션 개발 및 데이터 과학 작업 스트림을 이렇게 분리하면 프로덕션 앱이 항상 최신 ML 모델을 사용하여 최신 코드를 실행하게 됩니다. 앱이 테스트를 통과하면 파이프라인은 ACR의 Docker 컨테이너에 빌드 이미지를 안전하게 저장합니다. 그런 다음, 릴리스 파이프라인이 AKS를 사용하여 컨테이너를 배포합니다. 

## <a name="cicd-pipeline-steps"></a>CI/CD 파이프라인 단계

다음 다이어그램과 단계는 CI/CD 파이프라인 아키텍처를 설명합니다.

![CI/CD 파이프라인 아키텍처](./media/ci-cd-flask/architecture.png)

1. 개발자는 선택한 IDE에서 애플리케이션 코드 작업을 수행합니다.
2. 개발자가 Azure Repos, GitHub 또는 기타 Git 소스 제어 공급자에게 코드를 커밋합니다. 
3. 이와 별도로, 데이터 과학자는 고유한 ML 모델을 개발합니다.
4. 데이터 과학자가 완성된 모델을 모델 리포지토리(이 예제에서는 Blob Storage 계정)에 게시합니다. 
5. Azure Pipelines에서 Git commit에 따라 빌드를 시작합니다.
6. 빌드 파이프라인은 Blob Storage에서 최신 ML 모델을 풀하고 컨테이너를 만듭니다.
7. 파이프라인이 ACR의 프라이빗 이미지 리포지토리에 빌드 이미지를 푸시합니다.
8. 빌드에 성공하면 릴리스 파이프라인이 시작됩니다.
9. 이 파이프라인은 ACR에서 최신 이미지를 풀하여 AKS의 Kubernetes 클러스터에 배포합니다.
10. 앱에 대한 사용자 요청이 DNS 서버를 통해 전달됩니다.
11. DNS 서버가 부하 분산 장치에 요청을 전달하고 응답을 다시 사용자에게 보냅니다.

## <a name="see-also"></a>참고 항목

- [TDSP(Team Data Science Process)](./index.yml)
- [AML(Azure Machine Learning)](../index.yml)
- [Azure DevOps](https://azure.microsoft.com/services/devops/)
- [AKS(Azure Kubernetes Service)](../../aks/intro-kubernetes.md)