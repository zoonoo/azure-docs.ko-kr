---
title: Azure 파이프라인 - 팀 데이터 과학 프로세스를 사용하여 CI/CD 파이프라인 만들기
description: Docker 및 Kubernetes를 사용하여 인공 지능(AI) 애플리케이션을 위한 지속적인 통합 및 지속적인 전송 파이프라인을 만듭니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721832"
---
# <a name="create-cicd-pipelines-for-ai-apps-using-azure-pipelines-docker-and-kubernetes"></a>Azure 파이프라인, 도커 및 Kubernetes를 사용하여 AI 앱용 CI/CD 파이프라인 만들기

인공 지능(AI) 응용 프로그램은 미리 학습된 ML(기계 학습) 모델에 포함된 응용 프로그램 코드입니다. AI 응용 프로그램에는 항상 두 가지 작업 스트림이 있습니다: 데이터 과학자는 ML 모델을 빌드하고 앱 개발자는 앱을 빌드하고 최종 사용자에게 노출하여 소비합니다. 이 문서에서는 ML 모델을 앱 소스 코드에 포함하는 AI 응용 프로그램에 대한 지속적인 통합 및 연속 전달(CI/CD) 파이프라인을 구현하는 방법에 대해 설명합니다. 샘플 코드 및 자습서에서는 Python Flask 웹 응용 프로그램을 사용하고 개인 Azure Blob 저장소 계정에서 미리 학습된 모델을 가져옵니다. AWS S3 스토리지 계정을 사용할 수도 있습니다.

> [!NOTE]
> 다음 프로세스는 CI/CD를 수행하는 여러 가지 방법 중 하나입니다. 이 도구 및 필수 구성 도구에 대한 대안이 있습니다.

## <a name="source-code-tutorial-and-prerequisites"></a>소스 코드, 자습서 및 필수 구성 조건

GitHub에서 [소스 코드](https://github.com/Azure/DevOps-For-AI-Apps) 및 [자세한 자습서를](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) 다운로드할 수 있습니다. 자습서 단계를 수행하여 사용자 고유의 응용 프로그램에 대한 CI/CD 파이프라인을 구현합니다.

다운로드한 소스 코드 및 자습서를 사용하려면 다음과 같은 필수 구성 조건이 필요합니다. 

- GitHub 계정에 포크된 [소스 코드 리포지토리](https://github.com/Azure/DevOps-For-AI-Apps)
- [Azure DevOps 조직](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Azure CLI](/cli/azure/install-azure-cli)
- [Kubernetes(AKS) 클러스터를 위한 Azure 컨테이너 서비스](/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
- AKS 클러스터에서 명령을 실행하고 구성을 가져오는 [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) 
- [Azure 컨테이너 레지스트리(ACR) 계정](/azure/container-registry/container-registry-get-started-portal)

## <a name="cicd-pipeline-summary"></a>CI/CD 파이프라인 요약

각각의 새 Git 커밋은 빌드 파이프라인을 시작합니다. 빌드는 Blob 저장소 계정에서 최신 ML 모델을 안전하게 가져오고 단일 컨테이너에 앱 코드로 패키징합니다. 응용 프로그램 개발 및 데이터 과학 작업 흐름의 이러한 분리는 프로덕션 앱이 항상 최신 ML 모델로 최신 코드를 실행하고 있음을 보장합니다. 앱이 테스트를 통과하면 파이프라인은 ACR의 Docker 컨테이너에 빌드 이미지를 안전하게 저장합니다. 그런 다음 릴리스 파이프라인은 AKS를 사용하여 컨테이너를 배포합니다. 

## <a name="cicd-pipeline-steps"></a>CI/CD 파이프라인 단계

다음 다이어그램 및 단계는 CI/CD 파이프라인 아키텍처를 설명합니다.

![CI/CD 파이프라인 아키텍처](./media/ci-cd-flask/architecture.png)

1. 개발자는 선택한 IDE에서 응용 프로그램 코드를 작업합니다.
2. 개발자는 Azure Repos, GitHub 또는 기타 Git 소스 제어 공급자에 코드를 커밋합니다. 
3. 이와는 별도로 데이터 과학자들은 ML 모델을 개발하기 위해 노력하고 있습니다.
4. 데이터 과학자는 완성된 모델을 모델 리포지토리에 게시합니다(이 경우 Blob 저장소 계정). 
5. Azure 파이프라인은 Git 커밋을 기반으로 빌드를 시작합니다.
6. 빌드 파이프라인은 Blob 저장소에서 최신 ML 모델을 가져오고 컨테이너를 만듭니다.
7. 파이프라인은 빌드 이미지를 ACR의 개인 이미지 리포지토리로 푸시합니다.
8. 릴리스 파이프라인은 성공적인 빌드를 기반으로 시작됩니다.
9. 파이프라인은 ACR에서 최신 이미지를 가져와 AKS의 Kubernetes 클러스터에 배포합니다.
10. 앱에 대한 사용자 요청은 DNS 서버를 통해 이동합니다.
11. DNS 서버는 요청을 로드 밸러블러에 전달하고 사용자에게 응답을 다시 보냅니다.

## <a name="see-also"></a>참조

- [TDSP(Team Data Science Process)](/azure/machine-learning/team-data-science-process/)
- [AML(Azure Machine Learning)](/azure/machine-learning/)
- [Azure 데브옵스](https://azure.microsoft.com/services/devops/)
- [AKS(Azure Kubernetes Service)](/azure/aks/intro-kubernetes)
