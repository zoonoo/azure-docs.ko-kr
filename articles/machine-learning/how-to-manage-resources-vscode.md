---
title: VS Code 확장 리소스 만들기 및 관리(미리 보기)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Visual Studio Code 확장을 사용하여 Azure Machine Learning 리소스를 만들고 관리하는 방법을 알아봅니다.
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 05/25/2021
ms.openlocfilehash: 4ebdb5d092a64213727bd35d923c335c8e82210b
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110479920"
---
# <a name="manage-azure-machine-learning-resources-with-the-vs-code-extension-preview"></a>VS Code 확장을 사용하여 Azure Machine Learning 리소스 관리(미리 보기)

VS Code 확장을 사용하여 Azure Machine Learning 리소스를 관리하는 방법을 알아봅니다.

![Azure Machine Learning VS Code 확장](media/how-to-manage-resources-vscode/azure-machine-learning-vscode-extension.png)

## <a name="prerequisites"></a>사전 요구 사항

- 동작합니다. 구독이 없는 경우 [Azure Machine Learning의 평가판 또는 유료 버전](https://aka.ms/AMLFree)에 가입하여 사용해 보세요.
- Visual Studio Code. 없는 경우 [설치](https://code.visualstudio.com/docs/setup/setup-overview)합니다.
- Azure Machine Learning 확장. [Azure Machine Learning VS Code 확장 설치 가이드](how-to-setup-vs-code.md)에 따라 확장을 설정합니다.

## <a name="create-resources"></a>리소스 만들기

리소스를 만드는 가장 빠른 방법은 확장의 도구 모음을 사용하는 것입니다.

1. Azure Machine Learning 보기를 엽니다.
1. 작업 표시줄에서 **+** 를 선택합니다.
1. 드롭다운 목록에서 리소스를 선택합니다.
1. 사양 파일을 구성합니다. 필요한 정보는 만들려는 리소스의 종류에 따라 달라집니다.
1. 사양 파일을 마우스 오른쪽 단추로 클릭하고 **Azure ML: 리소스 만들기** 를 선택합니다.

또는 명령 팔레트를 사용하여 리소스를 만들 수 있습니다.

1. 명령 팔레트를 엽니다(**보기 > 명령 팔레트**).
1. 텍스트 상자에 `> Azure ML: Create <RESOURCE-TYPE>`을 입력합니다. `RESOURCE-TYPE`을 만들려는 리소스 종류로 대체합니다.
1. 사양 파일을 구성합니다.
1. 명령 팔레트를 엽니다(**보기 > 명령 팔레트**).
1. 텍스트 상자에 `> Azure ML: Create Resource`를 입력합니다.

## <a name="version-resources"></a>리소스 버전 관리

환경, 데이터 세트 및 모델과 같은 일부 리소스를 사용하면 리소스를 변경하고 다른 버전을 저장할 수 있습니다.

리소스 버전을 관리하려면 다음을 수행합니다.

1. 리소스를 만든 기존 사양 파일을 사용하거나 리소스 만들기 프로세스에 따라 새 사양 파일을 만듭니다.
1. 템플릿에서 버전 번호를 늘립니다.
1. 사양 파일을 마우스 오른쪽 단추로 클릭하고 **Azure ML: 리소스 만들기** 를 선택합니다.

업데이트된 리소스의 이름이 이전 버전과 동일하면 Azure Machine Learning은 변경 내용을 선택하여 새 버전을 만듭니다.

## <a name="workspaces"></a>작업 영역

자세한 정보는 [작업 영역](concept-workspace.md)을 참조하세요.

### <a name="create-a-workspace"></a>작업 영역 만들기

1. Azure Machine Learning 보기에서 구독 노드를 마우스 오른쪽 단추로 클릭하고 **작업 영역 만들기** 를 선택합니다.
1. 사양 파일이 나타납니다. 사양 파일을 구성합니다.
1. 사양 파일을 마우스 오른쪽 단추로 클릭하고 **Azure ML: 리소스 만들기** 를 선택합니다.

또는 명령 팔레트에서 `> Azure ML: Create Workspace` 명령을 사용합니다.

### <a name="remove-workspace"></a>작업 영역 제거

1. 작업 영역을 포함하는 구독 노드를 확장합니다.
1. 제거하려는 작업 영역에서 마우스 오른쪽 단추를 클릭합니다.
1. 제거할 것인지 여부를 선택합니다.
    - *작업 영역만*: 이 옵션은 작업 영역 Azure 리소스 **만** 삭제합니다. 리소스 그룹, 스토리지 계정 및 작업 영역이 연결된 기타 모든 리소스는 여전히 Azure에 있습니다.
    - *연결된 리소스*: 이 옵션은 작업 영역 **및** 관련된 모든 리소스를 삭제합니다.

또는 명령 팔레트에서 `> Azure ML: Remove Workspace` 명령을 사용합니다.

## <a name="datastores"></a>데이터 저장소

이 확장은 현재 다음 유형의 데이터 저장소를 지원합니다.

- Azure Blob
- Azure Data Lake Gen 1
- Azure Data Lake Gen 2
- Azure 파일

자세한 정보는 [데이터 저장소](concept-data.md#datastores)를 참조하세요.

### <a name="create-a-datastore"></a>데이터 저장소 만들기

1. 작업 영역을 포함하는 구독 노드를 확장합니다.
1. 데이터 저장소를 만들려는 작업 영역 노드를 확장합니다.
1. **데이터 저장소** 노드를 마우스 오른쪽 단추로 클릭하고 **데이터 저장소 만들기** 를 선택합니다.
1. 데이터 저장소 유형을 선택합니다.
1. 사양 파일이 나타납니다. 사양 파일을 구성합니다.
1. 사양 파일을 마우스 오른쪽 단추로 클릭하고 **Azure ML: 리소스 만들기** 를 선택합니다.

또는 명령 팔레트에서 `> Azure ML: Create Datastore` 명령을 사용합니다.

### <a name="manage-a-datastore"></a>데이터 저장소 관리

1. 작업 영역을 포함하는 구독 노드를 확장합니다.
1. 작업 영역 노드를 확장합니다.
1. 작업 영역 내에서 **데이터 저장소** 노드를 확장합니다.
1. 다음을 수행할 데이터 저장소를 마우스 오른쪽 단추로 클릭합니다.
    - *데이터 저장소 등록 취소*. 작업 영역에서 데이터 저장소를 제거합니다.
    - *데이터 저장소 보기*. 읽기 전용 데이터 저장소 설정 표시

또는 명령 팔레트에서 `> Azure ML: Unregister Datastore` 및 `> Azure ML: View Datastore` 명령을 각각 사용합니다.

## <a name="datasets"></a>데이터 세트

확장은 현재 다음 데이터 세트 형식을 지원합니다.

- *테이블 형식*: DataFrame으로 데이터를 구체화할 수 있습니다.
- *파일*: 파일 또는 파일 컬렉션입니다. 컴퓨팅에 파일을 다운로드하거나 탑재하는 기능을 제공합니다.

자세한 정보는 [데이터 세트](concept-data.md#datasets)를 참조하세요.

### <a name="create-dataset"></a>데이터 세트 만들기

1. 작업 영역을 포함하는 구독 노드를 확장합니다.
1. 데이터 세트를 만들려는 작업 영역 노드를 확장합니다.
1. **데이터 세트** 노드를 마우스 오른쪽 단추로 클릭하고 **데이터 세트 만들기** 를 선택합니다.
1. 사양 파일이 나타납니다. 사양 파일을 구성합니다.
1. 사양 파일을 마우스 오른쪽 단추로 클릭하고 **Azure ML: 리소스 만들기** 를 선택합니다.

또는 명령 팔레트에서 `> Azure ML: Create Dataset` 명령을 사용합니다.

### <a name="manage-a-dataset"></a>데이터 세트 관리

1. 작업 영역을 포함하는 구독 노드를 확장합니다.
1. 작업 영역 노드를 확장합니다.
1. **데이터 세트** 노드를 확장합니다.
1. 다음을 수행할 데이터 세트를 마우스 오른쪽 단추로 클릭합니다.
    - **데이터 세트 속성 보기**. 특정 데이터 세트와 연결된 메타데이터를 볼 수 있습니다. 여러 버전의 데이터 세트가 있는 경우 데이터 세트 노드를 확장하고 대상 버전에 대해 이 섹션에서 설명한 것과 동일한 단계를 수행하여 특정 버전의 데이터 세트 속성만 볼 수 있습니다.
    - **데이터 세트 미리 보기**. VS Code 데이터 뷰어에서 직접 데이터 세트를 봅니다. 이 옵션은 테이블 형식 데이터 세트에만 사용할 수 있습니다.
    - **데이터 세트 등록 취소**. 작업 영역에서 데이터 세트와 모든 버전을 제거합니다.

또는 명령 팔레트에서 `> Azure ML: View Dataset Properties` 및 `> Azure ML: Unregister Dataset` 명령을 각각 사용합니다.

## <a name="environments"></a>환경

자세한 정보는 [환경](concept-environments.md)을 참조하세요.

### <a name="create-environment"></a>환경 만들기

1. 작업 영역을 포함하는 구독 노드를 확장합니다.
1. 데이터 저장소를 만들려는 작업 영역 노드를 확장합니다.
1. **환경** 노드에서 마우스 오른쪽 단추를 클릭하고 **환경 만들기** 를 선택합니다.
1. 사양 파일이 나타납니다. 사양 파일을 구성합니다.
1. 사양 파일을 마우스 오른쪽 단추로 클릭하고 **Azure ML: 리소스 만들기** 를 선택합니다.

또는 명령 팔레트에서 `> Azure ML: Create Environment` 명령을 사용합니다.

### <a name="view-environment-configurations"></a>환경 구성 보기

확장에서 특정 환경의 종속성 및 구성을 보려면 다음을 수행합니다.

1. 작업 영역을 포함하는 구독 노드를 확장합니다.
1. 작업 영역 노드를 확장합니다.
1. **환경** 노드를 확장합니다.
1. 보려는 환경에서 마우스 오른쪽 단추를 클릭하고 **환경 보기** 를 선택합니다.

또는 명령 팔레트에서 `> Azure ML: View Environment` 명령을 사용합니다.

## <a name="experiments"></a>실험

자세한 정보는 [실험](concept-azure-machine-learning-architecture.md#experiments)을 참조하세요.

### <a name="create-job"></a>작업 만들기

작업을 만드는 가장 빠른 방법은 확장의 작업 표시줄에서 **작업 만들기** 아이콘을 클릭하는 것입니다.

Azure Machine Learning 보기에서 리소스 노드 사용:

1. 작업 영역을 포함하는 구독 노드를 확장합니다.
1. 작업 영역 노드를 확장합니다.
1. 작업 영역의 **실험** 노드를 마우스 오른쪽 단추로 클릭하고 **작업 만들기** 를 선택합니다.
1. 직업 유형을 선택합니다.
1. 사양 파일이 나타납니다. 사양 파일을 구성합니다.
1. 사양 파일을 마우스 오른쪽 단추로 클릭하고 **Azure ML: 리소스 만들기** 를 선택합니다.

또는 명령 팔레트에서 `> Azure ML: Create Job` 명령을 사용합니다.

### <a name="view-job"></a>작업 보기

Azure Machine Learning Studio에서 작업을 보려면 다음을 수행합니다.

1. 작업 영역을 포함하는 구독 노드를 확장합니다.
1. 작업 영역 내에서 **실험** 노드를 확장합니다.
1. 보려는 실험을 마우스 오른쪽 단추로 클릭하고 **View Experiment in Studio**(Studio에서 실험 보기)를 선택합니다.
1. Azure Machine Learning 스튜디오에서 실험 URL을 열도록 요청하는 프롬프트가 표시됩니다. **열기** 를 선택합니다.

또는 명령 팔레트에서 각각 `> Azure ML: View Experiment in Studio` 명령을 사용합니다.

### <a name="track-run-progress"></a>실행 진행률 추적

작업을 실행하는 동안 진행률을 확인할 수 있습니다. 확장에서 Azure Machine Learning 스튜디오의 실행 진행률을 추적하려면 다음을 수행합니다.

1. 작업 영역을 포함하는 구독 노드를 확장합니다.
1. 작업 영역 내에서 **실험** 노드를 확장합니다.
1. 진행률을 추적하려는 작업 노드를 확장합니다.
1. 실행을 마우스 오른쪽 단추로 클릭하고 **View Run in Studio**(Studio에서 실행 보기)를 선택합니다.
1. Azure Machine Learning 스튜디오에서 실행 URL을 열도록 요청하는 프롬프트가 표시됩니다. **열기** 를 선택합니다.

### <a name="download-run-logs--outputs"></a>실행 로그 및 출력 다운로드

실행이 완료되면 실행의 일부로 생성된 모델 등의 로그 및 자산을 다운로드할 수 있습니다.

1. 작업 영역을 포함하는 구독 노드를 확장합니다.
1. 작업 영역 내에서 **실험** 노드를 확장합니다.
1. 로그 및 출력을 다운로드하려는 작업 노드를 확장합니다.
1. 실행에서 마우스 오른쪽 단추를 클릭합니다.
    - 출력을 다운로드하려면 **출력 다운로드** 를 선택합니다.
    - 로그를 다운로드하려면 **로그 다운로드** 를 선택합니다.

또는 명령 팔레트에서 `> Azure ML: Download Outputs` 및 `> Azure ML: Download Logs` 명령을 각각 사용합니다.

## <a name="compute-instances"></a>컴퓨팅 인스턴스

자세한 정보는 [컴퓨팅 인스턴스](concept-compute-instance.md)를 참조하세요.

### <a name="create-compute-instance"></a>컴퓨팅 인스턴스 만들기

1. 작업 영역을 포함하는 구독 노드를 확장합니다.
1. 작업 영역 노드를 확장합니다.
1. **컴퓨팅** 노드를 확장합니다.
1. 작업 영역에서 **컴퓨팅 인스턴스** 노드를 마우스 오른쪽 단추로 클릭하고 **컴퓨팅 만들기** 를 선택합니다.
1. 사양 파일이 나타납니다. 사양 파일을 구성합니다.
1. 사양 파일을 마우스 오른쪽 단추로 클릭하고 **Azure ML: 리소스 만들기** 를 선택합니다.

또는 명령 팔레트에서 `> Azure ML: Create Compute` 명령을 사용합니다.

### <a name="connect-to-compute-instance"></a>컴퓨팅 인스턴스에 연결

컴퓨팅 인스턴스를 개발 환경 또는 원격 Jupyter 서버로 사용하려면 [컴퓨팅 인스턴스에 연결](how-to-set-up-vs-code-remote.md?tabs=extension)을 참조하세요.

### <a name="stop-or-restart-compute-instance"></a>컴퓨팅 인스턴스 중지 또는 다시 시작

1. 작업 영역을 포함하는 구독 노드를 확장합니다.
1. 작업 영역 노드를 확장합니다.
1. **컴퓨팅** 노드 내에서 **컴퓨팅 인스턴스** 노드를 확장합니다.
1. 중지하거나 다시 시작하려는 컴퓨팅 인스턴스를 마우스 오른쪽 단추로 클릭하고 **컴퓨팅 인스턴스 중지** 또는 **컴퓨팅 인스턴스 다시 시작** 을 각각 선택합니다.

또는 명령 팔레트에서 `> Azure ML: Stop Compute instance` 및 `Restart Compute instance` 명령을 각각 사용합니다.

### <a name="view-compute-instance-configuration"></a>컴퓨팅 인스턴스 구성 보기

1. 작업 영역을 포함하는 구독 노드를 확장합니다.
1. 작업 영역 노드를 확장합니다.
1. **컴퓨팅** 노드 내에서 **컴퓨팅 인스턴스** 노드를 확장합니다.
1. 검사하려는 컴퓨팅 인스턴스에서 마우스 오른쪽 단추를 클릭하고 **컴퓨팅 인스턴스 속성 보기** 를 선택합니다.

또는 명령 팔레트에서 `Azure ML: View Compute instance Properties` 명령을 사용합니다.

### <a name="delete-compute-instance"></a>컴퓨팅 인스턴스 삭제

1. 작업 영역을 포함하는 구독 노드를 확장합니다.
1. 작업 영역 노드를 확장합니다.
1. **컴퓨팅** 노드 내에서 **컴퓨팅 인스턴스** 노드를 확장합니다.
1. 삭제하려는 컴퓨팅 인스턴스를 마우스 오른쪽 단추로 클릭하고 **컴퓨팅 인스턴스 삭제** 를 선택합니다.

또는 명령 팔레트에서 `Azure ML: Delete Compute instance` 명령을 사용합니다.

## <a name="compute-clusters"></a>컴퓨팅 클러스터

자세한 정보는 [컴퓨팅 대상 학습](concept-compute-target.md#train)을 참조하세요.

### <a name="create-compute-cluster"></a>컴퓨팅 클러스터 만들기

1. 작업 영역을 포함하는 구독 노드를 확장합니다.
1. 작업 영역 노드를 확장합니다.
1. **컴퓨팅** 노드를 확장합니다.
1. 작업 영역에서 **컴퓨팅 클러스터** 노드를 마우스 오른쪽 단추로 클릭하고 **컴퓨팅 만들기** 를 선택합니다.
1. 사양 파일이 나타납니다. 사양 파일을 구성합니다.
1. 사양 파일을 마우스 오른쪽 단추로 클릭하고 **Azure ML: 리소스 만들기** 를 선택합니다.

또는 명령 팔레트에서 `> Azure ML: Create Compute` 명령을 사용합니다.

### <a name="view-compute-configuration"></a>컴퓨팅 구성 보기

1. 작업 영역을 포함하는 구독 노드를 확장합니다.
1. 작업 영역 노드를 확장합니다.
1. **컴퓨팅** 노드 내에서 **컴퓨팅 클러스터** 노드를 확장합니다.
1. 보려는 컴퓨팅에서 마우스 오른쪽 단추를 클릭하고 **컴퓨팅 속성 보기** 를 선택합니다.

또는 명령 팔레트에서 `> Azure ML: View Compute Properties` 명령을 사용합니다.

### <a name="delete-compute-cluster"></a>컴퓨팅 클러스터 삭제

1. 작업 영역을 포함하는 구독 노드를 확장합니다.
1. 작업 영역 노드를 확장합니다.
1. **컴퓨팅** 노드 내에서 **컴퓨팅 클러스터** 노드를 확장합니다.
1. 삭제할 컴퓨팅을 마우스 오른쪽 단추로 클릭하고 **컴퓨팅 제거** 를 선택합니다.

또는 명령 팔레트에서 `> Azure ML: Remove Compute` 명령을 사용합니다.

## <a name="inference-clusters"></a>유추 클러스터

자세한 정보는 [ 유추에 사용되는 컴퓨팅 대상](concept-compute-target.md#deploy)을 참조하세요.

### <a name="manage-inference-clusters"></a>유추 클러스터 관리

1. 작업 영역을 포함하는 구독 노드를 확장합니다.
1. 작업 영역 노드를 확장합니다.
1. **컴퓨팅** 노드 내에서 **유추 클러스터** 노드를 확장합니다.
1. 다음을 수행할 컴퓨팅을 마우스 오른쪽 단추로 클릭합니다.
    - **컴퓨팅 속성 보기**. 연결된 컴퓨팅에 대한 읽기 전용 구성 데이터를 표시합니다.
    - **컴퓨팅 분리**. 작업 영역에서 컴퓨팅을 분리합니다.

또는 명령 팔레트에서 `> Azure ML: View Compute Properties` 및 `> Azure ML: Detach Compute` 명령을 각각 사용합니다.

### <a name="delete-inference-clusters"></a>유추 클러스터 삭제

1. 작업 영역을 포함하는 구독 노드를 확장합니다.
1. 작업 영역 노드를 확장합니다.
1. **컴퓨팅** 노드 내에서 **연결된 컴퓨팅** 노드를 확장합니다.
1. 삭제할 컴퓨팅을 마우스 오른쪽 단추로 클릭하고 **컴퓨팅 제거** 를 선택합니다.

또는 명령 팔레트에서 `> Azure ML: Remove Compute` 명령을 사용합니다.

## <a name="attached-compute"></a>연결된 컴퓨팅

자세한 내용은 [관리되지 않는 컴퓨팅](concept-compute-target.md#unmanaged-compute)을 참조하세요.

### <a name="manage-attached-compute"></a>연결된 컴퓨팅 관리

1. 작업 영역을 포함하는 구독 노드를 확장합니다.
1. 작업 영역 노드를 확장합니다.
1. **컴퓨팅** 노드 내에서 **연결된 컴퓨팅** 노드를 확장합니다.
1. 다음을 수행할 컴퓨팅을 마우스 오른쪽 단추로 클릭합니다.
    - **컴퓨팅 속성 보기**. 연결된 컴퓨팅에 대한 읽기 전용 구성 데이터를 표시합니다.
    - **컴퓨팅 분리**. 작업 영역에서 컴퓨팅을 분리합니다.

또는 명령 팔레트에서 `> Azure ML: View Compute Properties` 및 `> Azure ML: Detach Compute` 명령을 각각 사용합니다.

## <a name="models"></a>모델

자세한 정보는 [모델](concept-azure-machine-learning-architecture.md#models)을 참조하세요.

### <a name="create-model"></a>모델 만들기

1. 작업 영역을 포함하는 구독 노드를 확장합니다.
1. 작업 영역 노드를 확장합니다.
1. 작업 영역의 **모델** 노드를 마우스 오른쪽 단추로 클릭하고 **모델 만들기** 를 선택합니다.
1. 사양 파일이 나타납니다. 사양 파일을 구성합니다.
1. 사양 파일을 마우스 오른쪽 단추로 클릭하고 **Azure ML: 리소스 만들기** 를 선택합니다.

또는 명령 팔레트에서 `> Azure ML: Create Model` 명령을 사용합니다.

### <a name="view-model-properties"></a>모델 속성 보기

1. 작업 영역을 포함하는 구독 노드를 확장합니다.
1. 작업 영역 내에서 **모델** 노드를 확장합니다.
1. 보려는 속성이 지정된 모델에서 마우스 오른쪽 단추를 클릭하고 **모델 속성 보기** 를 선택합니다. 편집기에서 모델 속성을 포함하는 파일이 열립니다.

또는 명령 팔레트에서 `> Azure ML: View Model Properties` 명령을 사용합니다.

### <a name="download-model"></a>모델 다운로드

1. 작업 영역을 포함하는 구독 노드를 확장합니다.
1. 작업 영역 내에서 **모델** 노드를 확장합니다.
1. 다운로드할 모델에서 마우스 오른쪽 단추를 클릭하고 **모델 파일 다운로드** 를 선택합니다.

또는 명령 팔레트에서 `> Azure ML: Download Model File` 명령을 사용합니다.

### <a name="delete-a-model"></a>모델 삭제

1. 작업 영역을 포함하는 구독 노드를 확장합니다.
1. 작업 영역 내에서 **모델** 노드를 확장합니다.
1. 삭제할 모델에서 마우스 오른쪽 단추를 클릭하고 **모델 제거** 를 선택합니다.
1. 모델을 제거할지 확인하는 메시지가 표시됩니다. **확인** 을 선택합니다.

또는 명령 팔레트에서 `> Azure ML: Remove Model` 명령을 사용합니다.

## <a name="endpoints"></a>엔드포인트

자세한 내용은 [엔드포인트](concept-azure-machine-learning-architecture.md#endpoints)를 참조하세요.

### <a name="create-endpoint"></a>엔드포인트 만들기

1. 작업 영역을 포함하는 구독 노드를 확장합니다.
1. 작업 영역 노드를 확장합니다.
1. 작업 영역의 **모델** 노드를 마우스 오른쪽 단추로 클릭하고 **엔드포인트 만들기** 를 선택합니다.
1. 엔드포인트 유형을 선택합니다.
1. 사양 파일이 나타납니다. 사양 파일을 구성합니다.
1. 사양 파일을 마우스 오른쪽 단추로 클릭하고 **Azure ML: 리소스 만들기** 를 선택합니다.

또는 명령 팔레트에서 `> Azure ML: Create Endpoint` 명령을 사용합니다.

### <a name="delete-endpoint"></a>엔드포인트 삭제

1. 작업 영역을 포함하는 구독 노드를 확장합니다.
1. 작업 영역 내에서 **엔드포인트** 노드를 확장합니다.
1. 제거하려는 배포를 마우스 오른쪽 단추로 클릭하고 **서비스 제거** 를 선택합니다.
1. 서비스를 제거할지 확인하는 메시지가 표시됩니다. **확인** 을 선택합니다.

또는 명령 팔레트에서 `> Azure ML: Remove Service` 명령을 사용합니다.

### <a name="view-service-properties"></a>서비스 속성 보기

배포를 만들고 삭제하는 것 외에도 배포와 관련된 설정을 보고 편집할 수 있습니다.

1. 작업 영역을 포함하는 구독 노드를 확장합니다.
1. 작업 영역 내에서 **엔드포인트** 노드를 확장합니다.
1. 관리하려는 배포에서 마우스 오른쪽 단추를 클릭합니다.
    - 배포 구성 설정을 보려면 **서비스 속성 보기** 를 선택합니다.

또는 명령 팔레트에서 `> Azure ML: View Service Properties` 명령을 사용합니다.

## <a name="next-steps"></a>다음 단계

VS Code 확장을 사용하여 [이미지 분류 모델을 학습](tutorial-train-deploy-image-classification-model-vscode.md)합니다.