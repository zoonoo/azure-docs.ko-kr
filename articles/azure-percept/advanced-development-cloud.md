---
title: 클라우드에서 Azure Percept 고급 개발 시작
description: Jupyter Notebook 및 Azure Machine Learning을 통해 클라우드에서 고급 개발 시작
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 408040ea68273a29ed9be87b60bd0cc6da736a05
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101658900"
---
# <a name="getting-started-with-advanced-development-in-the-cloud-via-jupyter-notebooks-and-azure-machine-learning"></a>Jupyter Notebook 및 Azure Machine Learning을 통해 클라우드에서 고급 개발 시작

이 문서에서는 Azure Machine Learning 작업 영역을 설정하고, 미리 구성된 샘플 Jupyter Notebook을 작업 영역에 업로드하고, 컴퓨팅 인스턴스를 만들고, 작업 영역 내에서 Notebook의 셀을 실행하는 과정을 안내합니다.

[Notebook](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb)은 bowl을 검색하기 위한 사용자 지정 데이터 세트를 사용하여 Python 환경의 AzureML에 미리 학습된 TensorFlow 모델(MobileNetSSDV2Lite)을 사용하여 전이 학습을 수행합니다.

이 Notebook에서는 [COCO](https://cocodataset.org/#home)에서 시작하여 원하는 클래스(bowls)만 필터링한 후 이를 적절한 형식으로 변환하는 방법을 보여줍니다. 또는 오픈 소스 [VoTT 2](https://github.com/microsoft/VoTT) 레이블 도구를 사용하여 PASCAL VOC 형식으로 경계 상자를 만들고 레이블을 지정할 수 있습니다.

모델을 사용자 지정 데이터 세트로 다시 학습시킨 후 모듈 쌍 업데이트 메서드를 사용하여 모델을 Azure Percept DK에 배포할 수 있습니다.

그런 다음, Azure Percept Vision SoM에서 라이브 RTSP 스트림을 확인하여 모델 추론을 확인할 수 있습니다. 모델 재 학습 및 배포는 모두 클라우드의 Notebook 내에서 수행됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Azure Machine Learning 구독](https://azure.microsoft.com/free/services/machine-learning/)
- [Azure Percept Vision SoM이 연결된 Azure Percept DK](./overview-azure-percept-dk.md)
- [Azure Percept DK 온보딩 경험](./quickstart-percept-dk-set-up.md) 완료

## <a name="download-azure-percept-github-repository"></a>Azure Percept GitHub 리포지토리 다운로드

1. [Azure Percept GitHub 리포지토리](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview)로 이동합니다.

1. 리포지토리를 복제하거나 ZIP 파일을 다운로드합니다. 화면 상단에서 **코드** -> **ZIP 다운로드** 를 클릭합니다.

    :::image type="content" source="./media/advanced-development-cloud/download-zip.png" alt-text="GitHub 다운로드 화면":::

## <a name="set-up-azure-machine-learning-portal-and-notebook"></a>Azure Machine Learning 포털 및 Notebook 설정

1. [Azure Machine Learning 포털](https://ml.azure.com)로 이동합니다.

1. 드롭다운 메뉴에서 디렉터리, Azure 구독 및 Machine Learning 작업 영역을 선택하고 **시작** 을 클릭합니다.

    :::image type="content" source="./media/advanced-development-cloud/machine-learning-portal-get-started.png" alt-text="Azure ML 시작 화면":::

    Azure Machine Learning 작업 영역이 아직 없는 경우 **새 작업 영역 만들기** 를 클릭합니다. 새 브라우저 탭에서 다음을 수행합니다.

    1. Azure 구독을 선택합니다.
    1. 리소스 그룹을 선택합니다.
    1. 작업 영역의 이름을 입력합니다.
    1. 지역을 선택합니다.
    1. 작업 영역 버전을 선택합니다.
    1. **검토 + 만들기** 를 클릭합니다.
    1. 다음 페이지에서 선택 내용을 검토하고 **만들기** 를 클릭합니다.

        :::image type="content" source="./media/advanced-development-cloud/workspace-review-and-create.png" alt-text="Azure ML의 작업 영역 만들기 화면":::

    작업 영역을 만드는 데 몇 분 정도 걸릴 수 있습니다. 작업 영역 만들기가 완료되면 리소스 옆에 녹색 확인 표시가 나타나고 Machine Learning Services 개요 페이지 맨 위에 **배포가 완료됨** 이 표시됩니다.

    :::image type="content" source="./media/advanced-development-cloud/workspace-creation-complete-inline.png" alt-text="작업 영역 만들기 확인" lightbox= "./media/advanced-development-cloud/workspace-creation-complete.png":::

    작업 영역 만들기가 완료되면 Machine Learning 포털 탭으로 돌아가서 **시작** 을 클릭합니다.

1. Machine Learning 작업 영역 홈페이지의 왼쪽 창에서 **Notebooks** 를 클릭합니다.

    :::image type="content" source="./media/advanced-development-cloud/notebook.png" alt-text="Azure ML 홈페이지":::

1. **내 파일** 탭에서 세로 화살표를 클릭하여 .ipynb 파일을 업로드합니다.

    :::image type="content" source="./media/advanced-development-cloud/upload-files.png" alt-text="파일 업로드 아이콘이 강조 표시된 홈페이지입니다.":::

1. Azure Percept GitHub 리포지토리의 로컬 복사본에서 [Transferlearningusing_SSDLiteV2 Model.ipynb 파일](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb)로 이동하고 선택합니다. **열기** 를 클릭합니다. **파일 업로드** 창에서 **이 파일의 콘텐츠 신뢰** 옆에 있는 상자를 선택하고 **업로드** 를 클릭합니다.

    :::image type="content" source="./media/advanced-development-cloud/select-file.png" alt-text="파일 선택 화면":::

1. 오른쪽 위에 있는 메뉴 모음에서 **컴퓨팅** 상태를 확인합니다. 컴퓨팅을 찾을 수 없으면 **+** 아이콘을 클릭하여 새 컴퓨팅을 만듭니다.

    :::image type="content" source="./media/advanced-development-cloud/no-computes-found-inline.png" alt-text="+ 아이콘이 강조 표시된 컴퓨팅 상태입니다." lightbox= "./media/advanced-development-cloud/no-computes-found.png":::

1. **새 컴퓨팅 인스턴스** 창에서 **컴퓨팅 이름** 을 입력하고, **가상 머신 유형** 을 선택하고, **가상 머신 크기** 를 선택합니다. **만들기** 를 클릭합니다.

    :::image type="content" source="./media/advanced-development-cloud/new-compute-instance.png" alt-text="새 컴퓨팅 인스턴스 만들기 화면입니다.":::

    **만들기** 를 클릭한 후 **컴퓨팅** 상태에 파란색 원 및 **\<your compute name> - 만드는 중** 이 표시됩니다.

    :::image type="content" source="./media/advanced-development-cloud/compute-creating.png" alt-text="컴퓨팅 만들기가 아직 진행 중일 때의 컴퓨팅 상태입니다.":::

    컴퓨팅 만들기가 완료된 다음에는 **컴퓨팅** 상태에 녹색 원 및 **\<your compute name> - 실행 중** 이 표시됩니다.

    :::image type="content" source="./media/advanced-development-cloud/compute-running.png" alt-text="컴퓨팅 만들기가 완료되었음을 나타내는 컴퓨팅 상태입니다.":::

1. 컴퓨팅이 실행 중이면 **+** 아이콘 옆의 드롭다운 메뉴에서 **Python 3.6 - AzureML** 커널을 선택합니다.

## <a name="working-with-the-notebook"></a>Notebook 작업

이제 Notebook을 실행하여 사용자 지정 bowl 감지기를 학습시키고 이를 개발 키트에 배포합니다. 일부 셀은 스크립트 실행 전 입력 매개변수가 필요하기 때문에 Notebook의 각 셀을 개별적으로 실행해야 합니다. 입력 매개 변수가 필요한 셀은 Notebook에서 직접 편집할 수 있습니다. 셀을 실행하려면 셀 왼쪽에 있는 재생 아이콘을 클릭합니다.

:::image type="content" source="./media/advanced-development-cloud/run-cell-inline.png" alt-text="셀 아이콘이 강조 표시된 Notebook" lightbox= "./media/advanced-development-cloud/run-cell.png":::

## <a name="next-steps"></a>다음 단계

추가 Azure Machine Learning Service 예제 Notebook을 보려면 이 [리포지토리](https://github.com/Azure/MachineLearningNotebooks/tree/2aa7c53b0ce84e67565d77e484987714fdaed36e/how-to-use-azureml)를 참조하세요.
