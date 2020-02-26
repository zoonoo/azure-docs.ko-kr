---
title: 보안 구하기를 위해 Azure 센티널에서 노트북 사용
description: 이 문서에서는 Azure 센티널 구하기 기능에서 노트북을 사용 하는 방법을 설명 합니다.
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
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77581840"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Jupyter 노트북을 사용 하 여 보안 위협 구하기

Azure 센티널의 기반은 데이터 저장소입니다. 고성능 쿼리, 동적 스키마 및 확장을 대규모 데이터 볼륨으로 결합 합니다. Azure Portal 및 모든 Azure 센티널 도구는 공통 API를 사용 하 여이 데이터 저장소에 액세스 합니다. [Jupyter](https://jupyter.org/) 노트북 및 Python과 같은 외부 도구에도 동일한 API를 사용할 수 있습니다. 포털에서 많은 일반 작업을 수행할 수 있지만 Jupyter는이 데이터로 수행할 수 있는 작업의 범위를 확장 합니다. 기계 학습, 시각화 및 데이터 분석을 위해 방대한 라이브러리 컬렉션과 함께 전체 프로그래밍 기능을 결합 합니다. 이러한 특성은 Jupyter를 보안 조사 및 사냥을 위한 매력적인 도구로 만듭니다.

![예제 노트북](./media/notebooks/sentinel-notebooks-map.png)

Jupyter 환경을 Azure Portal에 통합 하 여 데이터를 분석 하는 전자 필기장을 쉽게 만들고 실행할 수 있습니다. *Kqlmagic* 라이브러리는 Azure 센티널에서 쿼리를 가져와서 노트북 내에서 직접 실행할 수 있는 붙이기를 제공 합니다. 쿼리에서는 [Kusto 쿼리 언어](https://kusto.azurewebsites.net/docs/query/index.html)를 사용 합니다. 일부 Microsoft 보안 분석가에서 개발한 여러 노트북은 Azure 센티널로 패키지 됩니다. 이러한 노트북 중 일부는 특정 시나리오를 위해 빌드되고 그대로 사용할 수 있습니다. 다른 항목은 자신의 노트북에서 사용 하기 위해 복사 하거나 적용할 수 있는 기술 및 기능을 설명 하기 위한 샘플로 제공 됩니다. 다른 전자 필기장은 Azure 센티널 커뮤니티 GitHub 에서도 가져올 수 있습니다.

통합 Jupyter 환경에서는 [Azure Notebooks](https://notebooks.azure.com/) 를 사용 하 여 노트북을 저장, 공유 및 실행 합니다. 컴퓨터에 Python 환경 및 Jupyter가 있거나 Azure Databricks와 같은 기타 Jupyter 환경에 있는 경우 이러한 노트북을 로컬로 실행할 수도 있습니다.

노트북에는 다음과 같은 두 가지 구성 요소가 있습니다.

- 쿼리 및 코드를 입력 하 고 실행 하 고 실행 결과를 표시 하는 브라우저 기반 인터페이스입니다.
- 코드 자체의 구문 분석 및 실행을 담당 하는 *커널* 입니다. 

Azure Notebooks 기본적으로이 커널은 Azure *무료 클라우드 계산 및 저장소*에서 실행 됩니다. 노트북에 복잡 한 기계 학습 모델 또는 시각화가 포함 된 경우 dsvm ( [데이터 과학 Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) )과 같은 보다 강력한 전용 계산 리소스를 사용 하는 것이 좋습니다. 계정에 있는 노트북은 공유 하도록 선택 하지 않는 한 비공개로 유지 됩니다.

Azure 센티널 노트북은 pandas, matplotlib, bokeh 등 널리 사용 되는 다양 한 Python 라이브러리를 사용 합니다. 다음과 같은 영역을 포함 하 여 선택할 수 있는 다양 한 Python 패키지를 선택할 수 있습니다.

- 시각화 및 그래픽
- 데이터 처리 및 분석
- 통계 및 수치 계산
- 기계 학습 및 심층 학습

또한 [msticpy](https://github.com/Microsoft/msticpy/)라는 패키지에 몇 가지 오픈 소스 Jupyter 보안 도구를 릴리스 했습니다. 이 패키지는 포함 된 많은 노트북에서 사용 됩니다. Msticpy 도구는 새로운 기능 및 개선 사항에 대 한 노트를 만드는 데 도움이 되도록 특별히 설계 되었습니다.

초기 노트북에는 다음이 포함 됩니다.

- **단계별 조사-경고 처리**: 영향을 받는 호스트 또는 호스트에서 작업을 분석 하 여 신속 하 게 경고를 심사 할 수 있습니다.
- **기반 구하기-Windows 호스트 탐색기**: 호스트의 계정 활동, 프로세스 실행, 네트워크 활동 및 기타 이벤트를 탐색할 수 있습니다.
- **Office365-탐색**: 여러 office 365 데이터 집합에서 의심 스러운 Office 365 활동을 찾아봅니다.

[Azure 센티널 Community GitHub 리포지토리](https://github.com/Azure/Azure-Sentinel) 는 Microsoft에서 빌드하고 커뮤니티에서 제공 하는 향후 azure 센티널 노트북의 위치입니다.

노트북을 사용 하려면 Azure Notebooks 계정이 있어야 합니다. 자세한 내용은 [빠른 시작: 로그인 및 Azure Notebooks 설명서에서 사용자 ID 설정](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks) 을 참조 하세요. 이 계정을 만들려면 **Azure 센티널-노트북**의 명령 모음에서 **Azure Notebooks에 등록** 옵션을 사용할 수 있습니다.

> [!div class="mx-imgBorder"]
>Azure Notebooks 옵션에 등록 ![](./media/notebooks/sentinel-azure-sign-up-azure-notebooks.png)

Azure 센티널에서 노트북 다이렉트를 실행 하거나 모든 Azure 센티널 노트북을 새 Azure Notebooks 프로젝트로 복제할 수 있습니다.

## <a name="run-a-notebook-from-azure-sentinel"></a>Azure 센티널에서 노트북 실행
 
1. Azure Portal에서 azure 센티널이 제공 하는 노트북을 볼 수 있는 **Azure 센티널** > **Threat management** > **노트북**으로 이동 합니다. 

2. 개별 노트북을 선택 하 여 설명, 필수 데이터 형식 및 데이터 원본을 읽습니다. 다음은 그 예입니다.
    
    > [!div class="mx-imgBorder"]
    > 노트북](./media/notebooks/sentinel-azure-notebooks-launch.png) 시작 ![

3. 사용할 노트북을 선택 하 고 **노트북 시작 (미리 보기)** 을 선택 하 여 Azure 센티널 작업 영역에 연결 하는 새 Azure Notebooks 프로젝트로 노트북을 복제 하 고 구성 합니다. 프로세스가 완료 되 면 실행을 위해 Azure Notebooks 내에서 노트북이 열립니다.

## <a name="clone-azure-sentinel-notebooks-to-a-new-azure-notebooks-project"></a>새 Azure Notebooks 프로젝트에 Azure 센티널 노트북 복제

이 절차는 Azure 센티널 노트북을 포함 하는 Azure Notebooks 프로젝트를 만듭니다. 그런 다음 노트북을 있는 그대로 실행 하거나 변경한 다음 실행할 수 있습니다.

1. Azure Portal에서 **Azure 센티널** > **Threat management** > **노트북** 으로 이동한 다음 명령 모음에서 **노트북 복제** 를 선택 합니다.
  
    > [!div class="mx-imgBorder"]
    >![전자 필기장 복제 옵션](./media/notebooks/sentinel-azure-clone-notebooks.png)

2. 다음 대화 상자가 나타나면 **가져오기** 를 선택 하 여 Azure Notebooks 프로젝트에 GitHub 리포지토리를 복제 합니다. 기존 Azure Notebooks 계정이 없으면 계정을 만들고 로그인 하 라는 메시지가 표시 됩니다.

   ![노트북 가져오기](./media/notebooks/sentinel-notebooks-clone.png)

3. **Github 리포지토리 업로드** 대화 상자에서이 옵션은 연결 된 github 리포지토리를 참조 하므로 **재귀적으로 복제** 를 선택 하지 않습니다. 프로젝트 이름에 대해 기본 이름을 사용 하거나 새 이름을 입력 합니다. 그런 다음 **가져오기** 를 클릭 하 여 GitHub 콘텐츠 복제를 시작 합니다 .이를 완료 하는 데 몇 분 정도 걸릴 수 있습니다.

   ![노트북 가져오기](./media/notebooks/sentinel-create-project.png)

4. 방금 만든 프로젝트를 열고 **노트북 폴더를 열어 전자 필기장** 을 확인 합니다. 다음은 그 예입니다.

   ![리포지토리 가져오기](./media/notebooks/sentinel-open-notebook1.png)

그런 다음 Azure Notebooks에서 노트북을 실행할 수 있습니다. Azure 센티널에서 이러한 노트북으로 돌아가려면 **Azure 센티널-노트북**의 명령 모음에서 **노트북으로 이동** 을 선택 합니다.

> [!div class="mx-imgBorder"]
>![노트북 옵션으로 이동](./media/notebooks/sentinel-azure-to-go-notebooks.png)


## <a name="using-notebooks-to-hunt"></a>전자 필기장을 사용 하 여 헌트

각 노트북은 헌트 또는 조사를 수행 하는 단계를 안내 합니다. 노트북 자체에서 또는 간단한 구성 절차를 통해 노트북에 필요한 라이브러리 및 기타 종속성을 설치할 수 있습니다. 노트북 프로젝트를 Azure 센티널 구독에 다시 연결 하는 구성은 앞의 단계에서 자동으로 프로 비전 됩니다.

1. 아직 Azure Notebooks 하지 않은 경우 **Azure 센티널-노트북**의 명령 모음에서 **노트북으로 이동** 옵션을 사용할 수 있습니다.
    
    > [!div class="mx-imgBorder"]
    >![노트북 옵션으로 이동](./media/notebooks/sentinel-azure-to-go-notebooks.png)
    
    Azure Notebooks에서 **내 프로젝트**를 선택 하 고, Azure 센티널 노트북을 포함 하는 프로젝트를 선택 하 고, 마지막으로 **노트북** 폴더를 선택 합니다.
    
2. 노트북을 열기 전에 기본적으로 무료 계산을 선택 하 여 노트북을 실행 합니다.
    
   ![노트북 선택](./media/notebooks/sentinel-open-notebook2.png)
    
    소개에서 설명한 대로 사용할 DSVM (데이터 과학 Virtual Machines)을 구성한 경우 첫 번째 노트북을 열기 전에 DSVM을 선택 하 고 인증 합니다. 

3. 노트북을 선택 하 여 엽니다.
    
    처음으로 노트북을 열 때 커널 버전을 선택 하 라는 메시지가 표시 될 수 있습니다. 메시지가 표시 되지 않는 경우 커널 >  **변경 커널** **에서 커널 버전** 을 선택한 다음 최소 3.6 버전을 선택할 수 있습니다. 선택한 커널 버전이 노트북 창의 오른쪽 위에 표시 됩니다.
    
   ![노트북 선택](./media/notebooks/sentinel-select-kernel.png)

4. 다운로드 한 노트북을 변경 하기 전에 원래 노트북의 복사본을 만들고 복사본에 대해 작업을 수행 하는 것이 좋습니다. 이렇게 하려면 **파일** 을 선택 하 > **복사본을 만듭니다**. 복사본에 대 한 작업을 수행 하면 데이터를 덮어쓰지 않고 나중 버전의 전자 필기장으로 안전 하 게 업데이트할 수 있습니다.
    
    이제 선택한 노트북을 실행 하거나 편집할 준비가 되었습니다.

권장 사항:

- Azure 센티널에서 데이터를 쿼리 하는 방법에 대 한 간략 한 소개는 주 **전자** 필기장 폴더의 [getstarted](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) 노트북을 참조 하세요. 

- **샘플-노트북** 하위 폴더에서 추가 샘플 노트북을 찾을 수 있습니다. 이러한 샘플 노트북은 데이터와 함께 저장 되므로 원하는 출력을 쉽게 볼 수 있습니다. [Nbviewer](https://nbviewer.jupyter.org/)에서 이러한 노트북을 보는 것이 좋습니다. 

- **영문** 폴더에는 기본 Python 버전 설정, dsvm 구성, 노트북에서 Azure 센티널 책갈피 만들기 등을 설명 하는 노트북이 포함 되어 있습니다.

제공 된 노트북은 사용자 고유의 노트북 개발에 사용할 수 있는 유용한 도구와 그림 및 코드 샘플로 제공 됩니다.

의견, 제안, 기능 요청, 전자 필기장 제공, 버그 보고서, 기존 전자 필기장에 대 한 개선 사항 및 추가 사항이 있는지 여부를 환영 합니다. [Azure 센티널 Community GitHub](https://github.com/Azure/Azure-Sentinel) 로 이동 하 여 문제를 만들거나 참여를 업로드 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure 센티널에서 Jupyter 노트북을 사용 하 여 시작 하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [위협에 대 한 사전 구하기](hunting.md)
- [책갈피를 사용 하 여 검색할 때 관심 있는 정보 저장](bookmarks.md)
