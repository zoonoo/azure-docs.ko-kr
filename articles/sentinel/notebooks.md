---
title: 보안 검색에 Azure Sentinel이 있는 노트북 사용
description: 이 문서에서는 Azure Sentinel 검색 기능을 사용하여 전자 필기장을 사용하는 방법에 대해 설명합니다.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/25/2019
ms.openlocfilehash: 84b72a71ed2de910bce44b0c3f3309782f096680
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77581840"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Jupyter 노트북을 사용하여 보안 위협 을 사냥

Azure Sentinel의 기초는 데이터 저장소입니다. 고성능 쿼리, 동적 스키마 및 대규모 데이터 볼륨으로 확장됩니다. Azure 포털 및 모든 Azure Sentinel 도구는 공통 API를 사용하여 이 데이터 저장소에 액세스합니다. [Jupyter](https://jupyter.org/) 노트북 및 파이썬과 같은 외부 도구에서도 동일한 API를 사용할 수 있습니다. 포털에서 많은 일반적인 작업을 수행할 수 있지만 Jupyter는 이 데이터로 수행할 수 있는 작업의 범위를 확장합니다. 전체 프로그래밍 기능과 기계 학습, 시각화 및 데이터 분석을 위한 방대한 라이브러리 컬렉션을 결합합니다. 이러한 특성을 통해 Jupyter는 보안 조사 및 사냥을 위한 강력한 도구가 됩니다.

![예제 노트북](./media/notebooks/sentinel-notebooks-map.png)

Jupyter 환경을 Azure 포털에 통합하여 전자 필기장을 쉽게 만들고 실행하여 데이터를 분석할 수 있습니다. *Kqlmagic* 라이브러리는 Azure Sentinel에서 쿼리를 받아 전자 필기장 내에서 직접 실행할 수 있는 접착제를 제공합니다. 쿼리는 [Kusto 쿼리 언어를](https://kusto.azurewebsites.net/docs/query/index.html)사용합니다. 일부 Microsoft 보안 분석가가 개발한 여러 노트북은 Azure Sentinel과 함께 패키지로 되어 있습니다. 이러한 전자 필기장 중 일부는 특정 시나리오에 맞게 만들어졌으며 있는 대로 사용할 수 있습니다. 다른 것들은 자신의 전자 필기장에 사용하기 위해 복사하거나 적응할 수 있는 기술과 기능을 설명하기 위한 샘플입니다. 다른 노트북은 Azure Sentinel 커뮤니티 GitHub에서 가져올 수도 있습니다.

통합된 Jupyter 환경은 [Azure 노트북을](https://notebooks.azure.com/) 사용하여 노트북을 저장, 공유 및 실행합니다. 컴퓨터에 Python 환경과 Jupyter가 있거나 Azure Databricks와 같은 다른 JupterHub 환경이 있는 경우 이러한 노트북을 로컬로 실행할 수도 있습니다.

노트북에는 다음 두 가지 구성 요소가 있습니다.

- 쿼리 및 코드를 입력하고 실행하고 실행 결과가 표시되는 브라우저 기반 인터페이스입니다.
- 코드 자체를 구문 분석하고 실행하는 *커널입니다.* 

Azure 전자 필기장에서 기본적으로 이 커널은 Azure *무료 클라우드 계산 및 저장소에서 실행됩니다.* 노트북에 복잡한 기계 학습 모델 이나 시각화가 포함되어 있는 경우 [DSVM(데이터 과학 가상 머신)과](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) 같은 보다 강력한 전용 컴퓨팅 리소스를 사용하는 것이 좋습니다. 공유하도록 선택하지 않는 한 계정의 전자 필기장은 비공개로 유지됩니다.

Azure Sentinel 노트북은 팬더, 매트플롯립, 보케 등과 같은 많은 인기 있는 파이썬 라이브러리를 사용합니다. 당신이 선택할 수있는 훌륭한 다른 파이썬 패키지가 있습니다, 같은 영역을 커버:

- 시각화 및 그래픽
- 데이터 처리 및 분석
- 통계 및 수치 컴퓨팅
- 머신 러닝 및 딥 러닝

우리는 또한 [msticpy라는](https://github.com/Microsoft/msticpy/)패키지에 몇 가지 오픈 소스 Jupyter 보안 도구를 발표했습니다. 이 패키지는 포함된 많은 노트북에 사용됩니다. Msticpy 도구는 사냥 및 조사를 위한 노트북을 만드는 데 도움이 되도록 특별히 설계되었으며 새로운 기능과 개선 사항에 대해 적극적으로 노력하고 있습니다.

초기 노트북에는 다음이 포함됩니다.

- **안내조사 - 프로세스 경고**: 영향을 받는 호스트 또는 호스트의 활동을 분석하여 경고를 신속하게 분류할 수 있습니다.
- **안내 검색 - Windows 호스트 탐색기**: 호스트에서 계정 활동, 프로세스 실행, 네트워크 활동 및 기타 이벤트를 탐색할 수 있습니다.
- **안내 검색 - Office365-탐색**: 여러 Office 365 데이터 세트에서 의심스러운 Office 365 활동을 검색합니다.

[Azure Sentinel 커뮤니티 GitHub 리포지토리는](https://github.com/Azure/Azure-Sentinel) Microsoft에서 빌드하거나 커뮤니티에서 제공한 향후 Azure Sentinel 전자 필기전자의 위치입니다.

전자 필기장을 사용하려면 Azure 전자 필기장 계정이 있어야 합니다. 자세한 내용은 [빠른 시작: 로그인 및](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks) Azure 노트북 설명서에서 사용자 ID 설정 참조. 이 계정을 만들려면 **Azure Sentinel - 전자 필기장**명령 모음에서 **Azure 전자 필기장에 대한 등록** 옵션을 사용할 수 있습니다.

> [!div class="mx-imgBorder"]
>![Azure 노트북 옵션 등록](./media/notebooks/sentinel-azure-sign-up-azure-notebooks.png)

Azure Sentinel에서 직접 전자 필기장을 실행하거나 모든 Azure Sentinel 전자 필기장을 새 Azure 전자 필기장으로 복제할 수 있습니다.

## <a name="run-a-notebook-from-azure-sentinel"></a>Azure 센티넬에서 전자 필기장 실행
 
1. Azure 포털에서 Azure **Sentinel이** > 제공하는 전자 필기장을 볼 수 있는 Azure Sentinel**위협 관리** > **전자 필기장으로**이동합니다. 

2. 개별 전자 필기장을 선택하여 설명, 필수 데이터 유형 및 데이터 원본을 읽습니다. 예를 들어:
    
    > [!div class="mx-imgBorder"]
    > ![노트북 을 실행](./media/notebooks/sentinel-azure-notebooks-launch.png)

3. 사용할 전자 필기장을 선택한 다음 **전자 필기장 실행(미리 보기)을** 선택하여 전자 필기장을 복제하고 Azure Sentinel 작업 영역에 연결하는 새 Azure 전자 필기장 프로젝트로 구성합니다. 프로세스가 완료되면 Azure 전자 필기장 내에서 전자 필기장이 열리므로 실행할 수 있습니다.

## <a name="clone-azure-sentinel-notebooks-to-a-new-azure-notebooks-project"></a>Azure Sentinel 전자 필기장을 새 Azure 전자 필기장으로 복제

이 프로시저는 Azure Sentinel 전자 필기장을 포함하는 Azure 전자 필기장 프로젝트를 만듭니다. 그런 다음 전자 필기장을 있는 것처럼 실행하거나 전자 필기장을 변경한 다음 실행할 수 있습니다.

1. Azure 포털에서 Azure **Sentinel** > **위협 관리** > **전자 필기로** 이동한 다음 명령 모음에서 **전자 필기장을 복제합니다.**
  
    > [!div class="mx-imgBorder"]
    >![노트북 복제 옵션](./media/notebooks/sentinel-azure-clone-notebooks.png)

2. 다음 대화 상자가 나타나면 **가져오기를** 선택하여 GitHub 리포지토리를 Azure 전자 필기장으로 복제합니다. 기존 Azure 전자 필기장이 없는 경우 계정을 만들고 로그인하라는 메시지가 표시됩니다.

   ![노트북 가져오기](./media/notebooks/sentinel-notebooks-clone.png)

3. **GitHub 리포지토리 업로드** 대화 상자에서 이 옵션은 연결된 GitHub 리포지토리를 참조하므로 **복제를 재귀적으로** 선택하지 마십시오. 프로젝트 이름에 대 한 기본 이름을 사용 하거나 새 이름을 입력 합니다. 그런 다음 **가져오기를** 클릭하여 GitHub 콘텐츠 복제를 시작했는데 완료하는 데 몇 분 정도 걸릴 수 있습니다.

   ![노트북 가져오기](./media/notebooks/sentinel-create-project.png)

4. 방금 만든 프로젝트를 연 다음 **전자 필기장 폴더를** 열어 전자 필기장을 봅니다. 예를 들어:

   ![리포지토리 가져오기](./media/notebooks/sentinel-open-notebook1.png)

그런 다음 Azure 전자 필기전자에서 전자 필기장을 실행할 수 있습니다. Azure Sentinel에서 이러한 전자 필기장으로 돌아가려면 **Azure Sentinel - 전자 필기장**명령 모음에서 전자 **필기장으로 이동을** 선택합니다.

> [!div class="mx-imgBorder"]
>![노트북 옵션으로 이동](./media/notebooks/sentinel-azure-to-go-notebooks.png)


## <a name="using-notebooks-to-hunt"></a>노트북을 사용하여 사냥

각 노트북은 사냥이나 조사를 수행하기위한 단계를 안내합니다. 노트북 자체에 또는 간단한 구성 절차를 통해 노트북에 필요한 라이브러리 및 기타 종속성을 설치할 수 있습니다. 전자 필기장 프로젝트를 Azure Sentinel 구독에 다시 연결하는 구성은 이전 단계에서 자동으로 프로비전됩니다.

1. Azure 전자 필기장에 아직 없는 경우 **Azure Sentinel - 전자 필기장**명령 모음에서 **전자 필기장으로 이동** 옵션을 사용할 수 있습니다.
    
    > [!div class="mx-imgBorder"]
    >![노트북 옵션으로 이동](./media/notebooks/sentinel-azure-to-go-notebooks.png)
    
    Azure 전자 필기전자에서 **내 프로젝트**, Azure Sentinel 전자 필기장이 포함된 프로젝트를 선택한 다음 마지막으로 **전자 필기장 폴더를 선택합니다.**
    
2. 전자 필기장을 열기 전에 기본적으로 무료 계산이 노트북을 실행하도록 선택된다는 점에 유의하십시오.
    
   ![노트북 선택](./media/notebooks/sentinel-open-notebook2.png)
    
    소개에서 설명한 대로 사용할 DSVM(데이터 과학 가상 머신)을 구성한 경우 첫 번째 전자 필기장을 열기 전에 DSVM을 선택하고 인증합니다. 

3. 전자 필기장을 선택하여 엽니다.
    
    전자 필기장을 처음 열 때 커널 버전을 선택하라는 메시지가 표시될 수 있습니다. 메시지가 표시되지 않으면 **커널** >  **변경 커널에서**커널 버전을 선택한 다음 3.6 이상인 버전을 선택할 수 있습니다. 선택한 커널 버전이 전자 필기장 창의 오른쪽 상단에 표시됩니다.
    
   ![노트북 선택](./media/notebooks/sentinel-select-kernel.png)

4. 다운로드한 전자 필기장을 변경하기 전에 원본 전자 필기장의 복사본을 만들고 복사본을 작업하는 것이 좋습니다. 이렇게 하려면 **파일** > **복사**를 선택합니다. 복사본을 작업하면 데이터를 덮어쓰지 않고도 향후 버전의 전자 필기장으로 안전하게 업데이트할 수 있습니다.
    
    이제 선택한 전자 필기장을 실행하거나 편집할 준비가 되었습니다.

권장 사항:

- Azure Sentinel에서 데이터를 쿼리하는 방법에 대한 간략한 소개는 기본 **전자 필기장** 폴더의 [GetStarted](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) 전자 필기장을 참조하십시오. 

- 샘플 전자 필기장 하위 폴더에서 추가 샘플 **전자 필기장을** 찾을 수 있습니다. 이러한 샘플 노트북은 데이터와 함께 저장되므로 의도한 출력을 더 쉽게 볼 수 있습니다. [nbviewer에서](https://nbviewer.jupyter.org/)이러한 전자 필기장을 보는 것이 좋습니다. 

- **HowTos** 폴더에는 기본 Python 버전 설정, DSVM 구성, 전자 필기장에서 Azure Sentinel 책갈피 만들기 및 기타 주제를 설명하는 노트북이 포함되어 있습니다.

제공된 전자 필기장은 유용한 도구이자 사용자 고유의 전자 필기장 개발에 사용할 수 있는 그림 및 코드 샘플로 사용됩니다.

제안, 기능 요청, 기여된 노트북, 버그 보고서 또는 기존 노트북에 대한 개선 사항 및 추가 여부에 관계없이 피드백을 환영합니다. [Azure Sentinel 커뮤니티 GitHub로](https://github.com/Azure/Azure-Sentinel) 이동하여 문제 또는 분기를 만들고 기여를 업로드합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Sentinel에서 Jupyter 전자 필기장을 사용하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [위협에 대한 사전 예방적 사냥](hunting.md)
- [북마크를 사용하여 사냥하는 동안 흥미로운 정보를 저장합니다.](bookmarks.md)
