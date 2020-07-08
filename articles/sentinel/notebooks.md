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
ms.date: 11/25/2019
ms.openlocfilehash: bf63d5c8cb46fd791508af40dcefd7b39d4ba9de
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652018"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Jupyter Notebook을 사용하여 보안 위협 헌팅

Azure Sentinel의 기반은 데이터 저장소입니다. 데이터 저장소는 고성능 쿼리와 동적 스키마를 결합하고 대규모 데이터 볼륨으로 확장합니다. Azure Portal과 모든 Azure Sentinel 도구는 공통 API를 사용하여 데이터 저장소에 액세스합니다. 동일한 API를 [Jupyter](https://jupyter.org/) Notebook 및 Python과 같은 외부 도구에도 사용할 수 있습니다. 포털에서 다수의 일반적인 작업을 수행할 수 있지만 Jupyter는 데이터로 수행할 수 있는 작업의 범위를 확장합니다. 기계 학습, 시각화 및 데이터 분석을 위해 방대한 라이브러리 컬렉션과 전체 프로그래밍 기능을 결합합니다. 이러한 특성 덕분에 Jupyter는 보안 조사 및 헌팅에 맞는 강력한 도구가 될 수 있습니다.

![예제 Notebook](./media/notebooks/sentinel-notebooks-map.png)

Jupyter 환경이 Azure Portal에 통합되어 있어서 Notebook을 쉽게 만들고 실행하여 데이터를 분석할 수 있습니다. *Kqlmagic* 라이브러리는 Azure Sentinel에서 쿼리를 가져와서 Notebook 내에서 직접 실행할 수 있는 수단을 제공합니다. 쿼리는 [Kusto 쿼리 언어](https://kusto.azurewebsites.net/docs/query/index.html)를 사용합니다. 일부 Microsoft 보안 분석가가 개발한 몇 가지 Notebook이 Azure Sentinel에 패키징되어 있습니다. 이러한 Notebook 중 일부는 특정 시나리오에 맞게 빌드되어 있어서 그대로 사용할 수 있습니다. 그 외 Notebook은 기술과 기능을 설명하는 샘플로 제공되며, 사용자가 Notebook에서 사용하기 적합하게 수정하거나 복사할 수 있습니다. Azure Sentinel 커뮤니티 GitHub에서 다른 Notebook을 가져올 수도 있습니다.

통합 Jupyter 환경에서는 [Azure Notebooks](https://notebooks.azure.com/)를 사용하여 Notebook을 저장, 공유 및 실행합니다. 컴퓨터에 Python 환경과 Jupyter가 있거나 Azure Databricks와 같은 기타 JupterHub 환경인 경우에는 이러한 Notebook을 로컬에서 실행할 수도 있습니다.

Notebook의 두 가지 구성 요소는 다음과 같습니다.

- 쿼리와 코드를 입력 및 실행하고, 실행 결과가 표시되는 브라우저 기반 인터페이스
- 코드 자체의 구문 분석 및 실행을 담당하는 *커널* 

Azure Notebooks는 기본적으로 Azure *Free Cloud Compute and Storage*(무료 클라우드 컴퓨팅 및 스토리지)에서 커널이 실행됩니다. Notebook에 복잡한 기계 학습 모델이나 시각화가 포함된 경우에는 DSVM([Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/))과 같은 보다 강력한 전용 컴퓨팅 리소스를 사용하는 것이 좋습니다. 계정에서 Notebook을 공유하도록 선택하지 않는 한 비공개로 유지됩니다.

Azure Sentinel Notebook은 pandas, matplotlib, bokeh 등 널리 사용되는 다양한 Python 라이브러리를 사용합니다. 그 외 다음과 같은 영역을 지원하는 다양한 Python 패키지를 선택할 수 있습니다.

- 시각화 및 그래픽
- 데이터 처리 및 분석
- 통계 및 수치 계산
- 기계 학습 및 딥 러닝

[msticpy](https://github.com/Microsoft/msticpy/)라는 패키지에 오픈 소스 Jupyter 보안 도구도 릴리스되었습니다. 이 패키지는 포함된 많은 Notebook에서 사용됩니다. Msticpy 도구는 구체적으로 헌팅 및 조사를 위한 Notebook을 만드는 데 도움이 되도록 설계되었으며, 당사는 새로운 기능과 개선을 위해 적극적으로 노력하고 있습니다.

초기 Notebook에는 다음이 포함됩니다.

- **단계별 조사 - 프로세스 경고**: 영향을 받는 하나 이상 호스트의 활동을 분석하여 경고를 신속하게 심사할 수 있습니다.
- **단계별 헌팅 - Windows 호스트 탐색기**: 호스트의 계정 활동, 프로세스 실행, 네트워크 활동 및 기타 이벤트를 탐색할 수 있습니다.
- **단계별 헌팅 - Office365 탐색**: 여러 Office 365 데이터 세트에서 의심스러운 Office 365 활동을 헌팅합니다.

[Azure Sentinel Community GitHub 리포지토리](https://github.com/Azure/Azure-Sentinel)는 Microsoft가 구축하거나 커뮤니티가 제공하는 향후 Azure Sentinel Notebook을 위한 공간입니다.

Notebook을 사용하려면 Azure Notebooks 계정이 있어야 합니다. 자세한 내용은 [빠른 시작: 로그인 및 사용자 ID 설정](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks)을 Azure Notebooks 설명서에서 참조하세요. 이 계정을 만들려면 **Azure Sentinel - Notebook**의 명령 모음에서 **Azure Notebooks 가입** 옵션을 사용합니다.

> [!div class="mx-imgBorder"]
>![Azure Notebooks 가입 옵션](./media/notebooks/sentinel-azure-sign-up-azure-notebooks.png)

Azure Sentinel에서 Notebook을 직접 실행하거나 모든 Azure Sentinel Notebook을 새 Azure Notebooks 프로젝트로 복제할 수 있습니다.

## <a name="run-a-notebook-from-azure-sentinel"></a>Azure Sentinel에서 Notebook 실행
 
1. Azure Portal에서 **Azure Sentinel** > **위협 관리** > **Notebook**으로 이동하면 Azure Sentinel이 제공하는 Notebook을 볼 수 있습니다. 

2. 개별 Notebook을 선택하고 설명, 필수 데이터 형식, 데이터 원본을 확인합니다. 다음은 그 예입니다.
    
    > [!div class="mx-imgBorder"]
    > ![Notebook 시작](./media/notebooks/sentinel-azure-notebooks-launch.png)

3. 사용할 Notebook을 선택한 다음, **Notebook(미리 보기) 시작**을 선택하여 Azure Sentinel 작업 영역에 연결된 새 Azure Notebooks 프로젝트로 Notebook을 복제하여 구성합니다. 프로세스가 완료되면 실행을 위해 Azure Notebooks 내에서 Notebook이 열립니다.

## <a name="clone-azure-sentinel-notebooks-to-a-new-azure-notebooks-project"></a>Azure Sentinel Notebook을 새 Azure Notebooks 프로젝트에 복제

이 절차를 수행하면 Azure Sentinel Notebook이 포함된 Azure Notebooks 프로젝트가 생성됩니다. 그러면, Notebook을 그대로 실행하거나 변경한 다음, 실행할 수 있습니다.

1. Azure Portal에서 **Azure Sentinel** > **위협 관리** > **Notebook**으로 이동한 다음, 명령 모음에서 **Notebook 복제**를 선택합니다.
  
    > [!div class="mx-imgBorder"]
    >![Notebook 복제 옵션](./media/notebooks/sentinel-azure-clone-notebooks.png)

2. 다음 대화 상자가 나타나면 **가져오기**를 선택하여 GitHub 리포지토리를 Azure Notebooks 프로젝트에 복제합니다. 기존 Azure Notebooks 계정이 없으면 계정을 만들고 로그인하라는 메시지가 표시됩니다.

   ![Notebook 가져오기](./media/notebooks/sentinel-notebooks-clone.png)

3. **GitHub 리포지토리 업로드** 대화 상자에서 **재귀적으로 복제**를 선택하지 않습니다. 이 옵션은 연결된 GitHub 리포지토리를 참조하기 때문입니다. 프로젝트 이름으로 기본 이름을 사용하거나 새 이름을 입력합니다. 그런 다음, **가져오기**를 클릭하여 GitHub 콘텐츠 복제를 시작합니다. 완료하는 데 몇 분 정도 걸릴 수 있습니다.

   ![Notebook 가져오기](./media/notebooks/sentinel-create-project.png)

4. 방금 만든 프로젝트를 열고 **Notebook** 폴더를 열어서 Notebook을 확인합니다. 다음은 그 예입니다.

   ![리포지토리 가져오기](./media/notebooks/sentinel-open-notebook1.png)

그런 다음, Azure Notebooks에서 Notebook을 실행할 수 있습니다. Azure Sentinel에서 이러한 Notebook으로 돌아가려면 **Azure Sentinel - Notebook**의 명령 모음에서 **Notebook으로 이동**을 선택합니다.

> [!div class="mx-imgBorder"]
>![Notebook으로 이동 옵션](./media/notebooks/sentinel-azure-to-go-notebooks.png)


## <a name="using-notebooks-to-hunt"></a>Notebook을 사용하여 헌팅

각 Notebook은 헌팅 또는 조사를 수행하는 단계를 안내합니다. Notebook에 필요한 라이브러리 및 기타 종속성은 Notebook 자체에서 설치하거나 간단한 구성 절차를 통해 설치할 수 있습니다. Notebook 프로젝트를 Azure Sentinel 구독에 다시 연결하는 구성은 이전 단계에서 자동으로 프로비저닝됩니다.

1. 아직 Azure Notebooks로 이동하지 않았으면 **Azure Sentinel - Notebook**의 명령 모음에서 **Notebook으로 이동**을 사용합니다.
    
    > [!div class="mx-imgBorder"]
    >![Notebook으로 이동 옵션](./media/notebooks/sentinel-azure-to-go-notebooks.png)
    
    Azure Notebooks에서 **내 프로젝트**를 선택한 다음, Azure Sentinel Notebook이 포함된 프로젝트와 **Notebook** 폴더를 차례로 선택합니다.
    
2. Notebook을 열기 전에 기본적으로 무료 컴퓨팅에서 Notebook을 실행하도록 선택되어 있습니다.
    
   ![Notebook 선택](./media/notebooks/sentinel-open-notebook2.png)
    
    소개에서 설명한 대로 사용할 DSVM(Data Science Virtual Machine)을 구성한 경우 첫 번째 Notebook을 열기 전에 DSVM을 선택하고 인증합니다. 

3. Notebook을 선택하여 엽니다.
    
    Notebook을 처음 열면 커널 버전을 선택하라는 메시지가 표시될 수 있습니다. 이 메시지가 표시되지 않으면 **커널** >  **커널 변경**에서 커널 버전을 선택한 다음, 버전(3.6 이상)을 선택하면 됩니다. 선택한 커널 버전은 Notebook 창의 오른쪽 위에 표시됩니다.
    
   ![Notebook 선택](./media/notebooks/sentinel-select-kernel.png)

4. 다운로드한 Notebook을 변경하기 전에 원래 Notebook의 복사본을 만들고 복사본에서 작업을 수행하는 것이 좋습니다. 이렇게 하려면 **파일** > **복사본 만들기**를 선택합니다. 복사본에서 작업을 수행하면 데이터를 덮어쓰지 않고 새로운 Notebook 버전으로 안전하게 업데이트할 수 있습니다.
    
    이제 선택한 Notebook을 실행하거나 편집할 준비가 되었습니다.

권장 사항:

- Azure Sentinel에서 데이터를 쿼리하는 방법에 대한 간략한 소개는 기본 **Notebook** 폴더에서 [GetStarted](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/345cf9f7c8f6137f5af4593a3f9d7568acd6cbc2/DeprecatedNotebooks/Get%20Started.ipynb) Notebook을 참조하세요. 

- **Sample-Notebooks** 하위 폴더에서 추가 샘플 Notebook을 찾을 수 있습니다. 이러한 샘플 Notebook은 데이터와 함께 저장되어 있기 때문에 의도한 출력을 쉽게 볼 수 있습니다. 이러한 Notebook은 [nbviewer](https://nbviewer.jupyter.org/)에서 보는 것이 좋습니다. 

- **HowTos** 폴더에는 다양한 주제를 설명하는 Notebook이 포함되어 있습니다. 예를 들면, 기본 Python 버전 설정, DSVM 구성, Notebook에서 Azure Sentinel 책갈피 만들기 및 기타 주제를 설명하는 Notebook이 있습니다.

제공된 Notebook은 자체 Notebook을 개발할 때 사용할 수 있는 코드 샘플과 실례 및 유용한 도구로 사용할 수 있습니다.

여러분의 피드백을 환경합니다. 제안, 기능 요청, Notebook 기여, 버그 보고, 기존 Notebook에 대한 개선 및 추가 사항 등 무엇이든 괜찮습니다. [Azure Sentinel 커뮤니티 GitHub](https://github.com/Azure/Azure-Sentinel)로 이동하여 문제를 작성하거나, 포크하거나, 기여 자료를 업로드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Sentinel에서 Jupyter Notebook 사용을 시작하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [위협 요소를 사전에 헌팅하기](hunting.md)
- [헌팅 시 책갈피를 사용하여 흥미로운 정보 저장하기](bookmarks.md)
