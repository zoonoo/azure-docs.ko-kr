---
title: Azure Sentinel에서 Notebook을 사용한 보안 헌팅
description: 이 문서에서는 Azure Sentinel 헌팅 기능과 Notebook을 사용하는 방법을 설명합니다.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/06/2020
ms.openlocfilehash: ded332813a840892f640aa6f6e48debbfe381b4b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90889238"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Jupyter Notebook을 사용하여 보안 위협 헌팅

Azure 센티널의 기반은 데이터 저장소입니다. 고성능 쿼리, 동적 스키마 및 크기 조정 기능을 대규모 데이터 볼륨으로 결합 합니다. Azure Portal과 모든 Azure Sentinel 도구는 공통 API를 사용하여 데이터 저장소에 액세스합니다. 동일한 API를 [Jupyter](https://jupyter.org/) Notebook 및 Python과 같은 외부 도구에도 사용할 수 있습니다. 포털에서 다수의 일반적인 작업을 수행할 수 있지만 Jupyter는 데이터로 수행할 수 있는 작업의 범위를 확장합니다. 기계 학습, 시각화 및 데이터 분석을 위해 방대한 라이브러리 컬렉션과 전체 프로그래밍 기능을 결합합니다. 이러한 특성 덕분에 Jupyter는 보안 조사 및 헌팅에 맞는 강력한 도구가 될 수 있습니다.

![예제 Notebook](./media/notebooks/sentinel-notebooks-map.png)

Jupyter 환경이 Azure Portal에 통합되어 있어서 Notebook을 쉽게 만들고 실행하여 데이터를 분석할 수 있습니다. *Kqlmagic* 라이브러리는 Azure Sentinel에서 쿼리를 가져와서 Notebook 내에서 직접 실행할 수 있는 수단을 제공합니다. 쿼리는 [Kusto 쿼리 언어](https://kusto.azurewebsites.net/docs/query/index.html)를 사용합니다. 일부 Microsoft 보안 분석가가 개발한 몇 가지 Notebook이 Azure Sentinel에 패키징되어 있습니다. 이러한 Notebook 중 일부는 특정 시나리오에 맞게 빌드되어 있어서 그대로 사용할 수 있습니다. 그 외 Notebook은 기술과 기능을 설명하는 샘플로 제공되며, 사용자가 Notebook에서 사용하기 적합하게 수정하거나 복사할 수 있습니다. 다른 전자 필기장은 Azure 센티널 커뮤니티 GitHub 에서도 가져올 수 있습니다.

통합 Jupyter 환경에서는 [Azure Notebooks](https://notebooks.azure.com/)를 사용하여 Notebook을 저장, 공유 및 실행합니다. 컴퓨터에 Python 환경과 Jupyter가 있거나 Azure Databricks와 같은 기타 JupterHub 환경인 경우에는 이러한 Notebook을 로컬에서 실행할 수도 있습니다.

Notebook의 두 가지 구성 요소는 다음과 같습니다.

- 쿼리와 코드를 입력 및 실행하고, 실행 결과가 표시되는 브라우저 기반 인터페이스
- 코드 자체의 구문 분석 및 실행을 담당하는 *커널*

Azure 센티널 노트북의 커널은 Azure VM (가상 머신)에서 실행 됩니다. 노트북에 복잡 한 기계 학습 모델이 포함 되어 있는 경우 더 강력한 가상 머신을 활용할 수 있는 몇 가지 라이선스 옵션이 있습니다.

Azure Sentinel Notebook은 pandas, matplotlib, bokeh 등 널리 사용되는 다양한 Python 라이브러리를 사용합니다. 그 외 다음과 같은 영역을 지원하는 다양한 Python 패키지를 선택할 수 있습니다.

- 시각화 및 그래픽
- 데이터 처리 및 분석
- 통계 및 수치 계산
- 기계 학습 및 딥 러닝

[msticpy](https://github.com/Microsoft/msticpy/)라는 패키지에 오픈 소스 Jupyter 보안 도구도 릴리스되었습니다. 이 패키지는 포함된 많은 Notebook에서 사용됩니다. Msticpy 도구는 구체적으로 헌팅 및 조사를 위한 Notebook을 만드는 데 도움이 되도록 설계되었으며, 당사는 새로운 기능과 개선을 위해 적극적으로 노력하고 있습니다.

[Azure Sentinel Community GitHub 리포지토리](https://github.com/Azure/Azure-Sentinel)는 Microsoft가 구축하거나 커뮤니티가 제공하는 향후 Azure Sentinel Notebook을 위한 공간입니다.

노트북을 사용 하려면 먼저 Azure Machine Learning (ML) 작업 영역을 만들어야 합니다.

## <a name="create-an-azure-ml-workspace"></a>Azure ML 작업 영역 만들기

1. Azure Portal에서 **Azure 센티널**  >  **Threat management**  >  **노트북** 으로 이동한 다음, **노트북 시작**을 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![노트북을 시작 하 여 azure ml 작업 영역 시작](./media/notebooks/sentinel-notebooks-launch.png)

1. **AzureML 작업 영역**에서 **새로 만들기**를 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![작업 영역 만들기](./media/notebooks/sentinel-notebooks-azureml-create.png)

1. **Machine Learning** 페이지에서 다음 정보를 입력 한 다음 **검토 + 만들기**를 선택 합니다.

    |필드|설명|
    |--|--|
    |Subscription|사용할 Azure 구독을 선택합니다.|
    |Resource group|구독의 기존 리소스 그룹을 사용하거나 이름을 입력하여 새 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 솔루션에 관련된 리소스를 보유합니다. 이 예제에서는 **AzureMLRG**를 사용 합니다.|
    |작업 영역 이름|작업 영역을 식별하는 고유한 이름을 입력합니다. 이 예제에서는 **testworkspace1**를 사용 합니다. 이름은 리소스 그룹 전체에서 고유해야 합니다. 다른 사용자가 만든 작업 영역과 구별되고 기억하기 쉬운 이름을 사용하세요.|
    |지역|사용자 및 데이터 리소스와 가장 가까운 위치를 선택하여 작업 영역을 만듭니다.|
    |Workspace Edition|이 예제에서 작업 영역 형식으로 **기본** 을 선택 합니다. 작업 영역 유형 (Basic & Enterprise)에는 액세스 및 가격 책정을 사용할 수 있는 기능이 결정 됩니다.|

    > [!div class="mx-imgBorder"]
    > ![작업 영역 세부 정보 제공](./media/notebooks/sentinel-notebooks-azureml-basics.png)

1. 정보를 검토 하 고 올바른지 확인 한 다음 **만들기** 를 선택 하 여 작업 영역의 배포를 시작 합니다.

    > [!div class="mx-imgBorder"]
    > ![작업 영역 정보 검토](./media/notebooks/sentinel-notebooks-azureml-review.png)

    작업 영역을 만드는 데 몇 분 정도 걸릴 수 있습니다 .이 시간 동안에는 **개요** 페이지에 현재 배포 상태가 표시 됩니다.

    > [!div class="mx-imgBorder"]
    > ![작업 영역 배포](./media/notebooks/sentinel-notebooks-azureml-deploy.png)

배포가 완료 되 면 새 Azure ML 작업 영역에서 노트북을 시작할 수 있습니다.

> [!div class="mx-imgBorder"]
> ![작업 영역 배포 성공](./media/notebooks/sentinel-notebooks-azureml-complete.png)

## <a name="launch-a-notebook-using-your-azure-ml-workspace"></a>Azure ML 작업 영역을 사용 하 여 노트북 시작

1. Azure Portal에서 **Azure Sentinel** > **위협 관리** > **Notebook**으로 이동하면 Azure Sentinel이 제공하는 Notebook을 볼 수 있습니다.

    > [!TIP]
    > **설명서 & 피드백** 을 선택 하 여 전자 필기장에 대 한 추가 도움말과 지침이 포함 된 창을 엽니다.
    > ![노트북 가이드 보기](./media/notebooks/sentinel-azure-notebooks-guides.png)

1. 개별 노트북을 선택 하 여 설명, 필수 데이터 형식 및 데이터 원본을 볼 수 있습니다.

    > [!div class="mx-imgBorder"]
    > ![노트북 세부 정보 보기](./media/notebooks/sentinel-azure-notebooks-view.png)

1. 사용할 노트북을 선택 하 고 **노트북 시작** 을 선택 하 여 Azure 센티널 작업 영역에 연결 하는 새 Azure Notebooks 프로젝트로 노트북을 복제 하 고 구성 합니다. 프로세스가 완료되면 실행을 위해 Azure Notebooks 내에서 Notebook이 열립니다.

    > [!div class="mx-imgBorder"]
    > ![노트북 선택](./media/notebooks/sentinel-azure-notebooks-select.png)

1. AzureML 작업 영역에서 Azure ML 작업 영역을 선택 하 고 **시작**을 선택 합니다.

    > [!div class="mx-imgBorder"]
    > ![Notebook 시작](./media/notebooks/sentinel-azure-notebooks-launch.png)

1. 계산 인스턴스를 선택 합니다. 계산 인스턴스가 없으면 다음을 수행 합니다.
    1. 더하기 기호 (+)를 선택 하 여 **새 계산 인스턴스** 마법사를 시작 합니다.

        > [!div class="mx-imgBorder"]
        > ![계산 인스턴스 시작 마법사](./media/notebooks/sentinel-azure-notebooks-compute-wizard.png)

    1. **새 계산 인스턴스** 페이지에서 필요한 정보를 입력 한 다음 **만들기**를 선택 합니다.

        > [!div class="mx-imgBorder"]
        > ![계산 인스턴스 만들기](./media/notebooks/sentinel-azure-notebooks-compute-create.png)

1. 노트북 서버를 만든 후 각 셀 내에서 실행 아이콘을 선택 하 여 전자 필기장에서 코드를 실행 합니다.

    > [!div class="mx-imgBorder"]
    > ![노트북 실행](./media/notebooks/sentinel-azure-notebooks-run.png)

권장 사항:

- Azure 센티널에서 데이터를 쿼리 하는 방법에 대 한 간략 한 소개는 [AZURE ML 노트북 및 Azure 센티널 시작](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/A%20Getting%20Started%20Guide%20For%20Azure%20Sentinel%20ML%20Notebooks.ipynb) 가이드를 참조 하세요.

- [**샘플-노트북**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/Sample-Notebooks) GitHub 하위 폴더에서 추가 샘플 노트북을 찾을 수 있습니다. 이러한 샘플 Notebook은 데이터와 함께 저장되어 있기 때문에 의도한 출력을 쉽게 볼 수 있습니다. 이러한 Notebook은 [nbviewer](https://nbviewer.jupyter.org/)에서 보는 것이 좋습니다.

- [**영문**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/HowTos) GitHub 하위 폴더에는 기본 Python 버전 설정, dsvm 구성, 노트북에서 Azure 센티널 책갈피 만들기 등을 설명 하는 노트북이 포함 되어 있습니다.

제공된 Notebook은 자체 Notebook을 개발할 때 사용할 수 있는 코드 샘플과 실례 및 유용한 도구로 사용할 수 있습니다.

여러분의 피드백을 환경합니다. 제안, 기능 요청, Notebook 기여, 버그 보고, 기존 Notebook에 대한 개선 및 추가 사항 등 무엇이든 괜찮습니다. [Azure Sentinel 커뮤니티 GitHub](https://github.com/Azure/Azure-Sentinel)로 이동하여 문제를 작성하거나, 포크하거나, 기여 자료를 업로드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Sentinel에서 Jupyter Notebook 사용을 시작하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [위협 요소를 사전에 헌팅하기](hunting.md)
- [헌팅 시 책갈피를 사용하여 흥미로운 정보 저장하기](bookmarks.md)
