---
title: Azure Sentinel 미리 보기에서 notebook을 사용 하 여 찾거나 기능 | Microsoft Docs
description: 이 문서에서는 Azure Sentinel hunting 기능을 사용 하 여 notebook을 사용 하는 방법을 설명 합니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: ffe3ae5b6aa26d154928a74e51864a0574b82c68
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65228626"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Jupyter Notebook을 사용 하 여 보안 위협에 대 한 사냥 하

> [!IMPORTANT]
> Azure Sentinel은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Sentinel의 기초는 데이터 저장소입니다. 고성능 쿼리, 동적 스키마와 눈금 엄청난 데이터 볼륨에 결합 합니다. Sentinel Azure 포털과 Azure Sentinel 도구는 모두이 데이터 저장소에 액세스 하는 공용 API를 사용 합니다. 동일한 API는와 같은 외부 도구에 대 한 사용 가능한 수도 [Jupyter](https://jupyter.org/) notebook 및 Python입니다. 많은 일반 태스크는 포털에서 수행할 수 있습니다, 있지만 Jupyter이이 데이터를 사용 하 여 수행할 수 있는 작업의 범위를 확장 합니다. Machine learning, 시각화 및 데이터 분석을 위해 라이브러리의 방대한 컬렉션 전체 프로그래밍을 결합합니다. 이러한 특성 Jupyter hunting 보안 조사에 매우 유용한 도구를 확인합니다.

![예제 노트북](./media/notebooks/sentinel-nb-mapandtimeline.png)

통합 했습니다 Jupyter 환경을 Sentinel Azure 포털에 쉽게 만들고 데이터를 분석 하는 notebook을 실행 합니다. 합니다 *Kqlmagic* 라이브러리는 Azure Sentinel에서 쿼리를 가져와서 전자 필기장 내 보에서 직접 실행할 수 있는 기능을 제공 합니다. 쿼리에서 사용 하 여 [Kusto 쿼리 언어](https://kusto.azurewebsites.net/docs/query/index.html)합니다. Microsoft의 보안 분석가 중 일부를 개발한 여러 노트북 Azure Sentinel 패키징 됩니다. 이러한 notebook의 일부 특정 시나리오에 대해 작성 된 및로 사용할 수 있습니다-됩니다. 다른 사용자는 샘플으로 기술과 복사 하거나 고유한 전자 필기장에서 사용 하기 위해 조정할 수 있는 기능을 설명 하기 위해서입니다. 또한 GitHub의 Azure Sentinel 커뮤니티에서 다른 notebook은 가져올 수 있습니다.

통합 된 Jupyter 환경을 사용 하 여 [Azure Notebooks](https://notebooks.azure.com/) 를 저장 하기 위해 공유 하 고 notebook을 실행 합니다. 로컬로 (있는 경우 Python 환경 및 Jupyter를 컴퓨터에)에 이러한 notebook을 실행할 수 있습니다 또는 Azure Databricks 등의 다른 JupterHub 환경입니다.

Notebook에 두 구성 요소

- 입력 하 고 쿼리 및 코드를 실행할 수 있는 브라우저 기반 인터페이스는 실행 결과에 표시 되는 및입니다.
- *커널* 담당 하는 구문 분석 하 고 자체 코드를 실행 합니다. 

Azure Notebooks이이 커널은 Azure에서 실행 *무료 클라우드 계산 및 저장소* 기본적으로 합니다. 전자 필기장 복잡 한 기계 학습 모델을 포함 하거나 시각화 사용을 고려해 야 하는 경우 더 강력 하 고 전용 계산 리소스와 같은 [데이터 과학 Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM). 계정의 전자 필기장 공유 하도록 선택 하지 않으면 개인 유지 됩니다.

Azure Sentinel notebook pandas, matplotlib, bokeh 등과 같은 여러 인기 있는 Python 라이브러리를 사용합니다. 상당히 많은 같은 영역을 다루는를 선택 하기 위한 다른 Python 패키지 있습니다.

- 시각화 및 그래픽
- 데이터 처리 및 분석
- 통계 및 숫자 컴퓨팅
- 기계 학습 및 딥 러닝

몇 가지 오픈 소스 Jupyter 보안 도구 라는 패키지도 릴리스 했습니다 [msticpy](https://github.com/Microsoft/msticpy/)합니다. 이 패키지는 다양 한 포함 된 notebook에 사용 됩니다. 조사를 적극적으로 작업 중인 새로운 기능 및 향상 된 기능 및 Msticpy 도구는 hunting 용 notebook 만들기에 도움이 되도록 특별히 디자인 되었습니다.

초기 notebook에는 다음과 같습니다.

- **조사-프로세스 경고 단계별**: 신속 하 게 영향을 받는 호스트에서 활동을 분석 하 여 경고를 심사 하 수 있습니다.
- **단계별 hunting-Windows 호스트 탐색기**: 호스트에서 계정 활동, 프로세스 실행, 네트워크 작업 및 기타 이벤트를 탐색할 수 있습니다.  
- **단계별 hunting-office 365 탐색**: 여러 O365 데이터 집합에서 Office 365 활동을 의심 스러운 끊임없이 연구 하 고 있습니다.

합니다 [Azure Sentinel 커뮤니티 GitHub 리포지토리](https://github.com/Azure/Azure-Sentinel) 를 모든 향후 Azure Sentinel notebook의 위치는 Microsoft에서 작성 하거나 커뮤니티에서 제공 합니다.

## <a name="run-a-notebook"></a>Notebook 실행

다음 예제에서에서는 프로젝트를 만들 Azure Notebooks Sentinel Azure portal에서 notebook 사용 하 여 프로젝트를 채우는. 이러한 notebook을 사용 하기 전에 것이 노트북의 복사본을 만들고 복사본에서 작동 하는 것이 좋습니다. 작업 복사본을 안전 하 게 기존 데이터를 덮어쓰지 않고 notebook의 향후 버전을 업데이트할 수 있습니다.

1. Sentinel Azure 포털에서 클릭 **Notebook** 탐색 메뉴에서. 새 Azure Notebooks 프로젝트를 만들려면 **복제본 Azure Notebooks Sentinel** 기존 전자 필기장을 열려면 클릭 프로젝트 또는 **Notebook으로 이동**합니다.
  
   ![notebook을 선택 합니다.](./media/notebooks/sentinel-az-notebooks-home.png)

2. 했다면 **복제본 Azure Notebooks Sentinel** 이전 단계에서 다음과 같은 대화 상자가 표시 됩니다. 클릭 **가져오기** Azure Notebooks 프로젝트로 GitHub 리포지토리를 복제 합니다. 기존 Azure Notebooks 계정이 없으면 하 고 로그인을 묻는 메시지가 나타납니다.

   ![Notebook 가져오기](./media/notebooks/sentinel-nb-signin-and-clone.png)

3. 새 프로젝트를 만들 때 프로젝트 이름을-새에서 기본 이름이 나 형식을 사용 해야 합니다. 확인 안 함 합니다 **재귀적으로 복제** 연결 된 GitHub 리포지토리 옵션-이 옵션은 참조 합니다. 클릭할 **가져오기** 완료 하려면 몇 분 정도 걸릴 수 있습니다 GitHub 콘텐츠 복제를 시작 합니다.

   ![Notebook 가져오기](./media/notebooks/sentinel-create-nb-project.png)

4. 엽니다는 **Notebook** notebook을 보려는 폴더입니다. 각 노트북을 이용 하면 헌트 또는 조사를 수행 하기 위한 단계를 안내 합니다. Notebook 자체에서 또는 간단한 구성 프로시저를 통해 라이브러리 및 전자 필기장에 필요한 기타 종속성을 설치할 수 있습니다. Notebook 프로젝트가 Sentinel Azure 구독에 다시 연결 하는 구성은 이전 단계에서 자동으로 프로 비전 합니다. 전자 필기장 Azure Sentinel Log Analytics 작업 영역을 실행할 준비가 되었습니다.

   ![리포지토리 가져오기](./media/notebooks/sentinel-open-notebook1.png)

5. Notebook을 엽니다. 사용 가능한 계산 (강조 표시) notebook을 실행 하려면 기본적으로 선택 됩니다. (위 참조)를 사용 하는 DSVM를 구성한 경우에 DSVM를 선택 하 고 첫 번째 노트북을 열기 전에 인증 합니다. 열려는 notebook을 클릭 합니다.

   ![notebook을 선택 합니다.](./media/notebooks/sentinel-open-notebook2.png)

6. Python 버전을 선택합니다. Notebook을 처음 열면 라는 메시지가 나타납니다는 커널 버전을 선택할 수 있습니다. 그렇지 않은 경우에 다음과 같이 사용 하는 커널을 선택 합니다. Python 3.6 이상 선택한 커널 이어야 합니다 (위에서 노트북 창의 오른쪽).

   ![notebook을 선택 합니다.](./media/notebooks/sentinel-select-kernel.png)

Azure Sentinel에서 데이터를 쿼리하여 간단한 소개를 참조 합니다 [GetStarted](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) 주 노트북 폴더에서 notebook입니다. 추가 샘플 노트북을 찾을 수 있습니다 합니다 **샘플 Notebook** 하위 폴더입니다. 쉽게 원하는 출력을 볼 수 있도록 데이터를 사용 하 여 저장 된 샘플 노트북이 (에서 보는 것이 좋습니다 [nbviewer](https://nbviewer.jupyter.org/)). 합니다 **HowTos** 설명 하는, 예를 들어 notebook을 포함 하는 폴더: Python 버전을 기본 설정, 노트북 및 다른 주체에서 DSVM를 구성 하는 경우, Azure Sentinel 만들기 책갈피를 설정 합니다.

이러한 notebook은 모두 유용한 도구와 그림 및 코드 샘플 고유한 전자 필기장의 개발에 사용할 수 있는 것입니다.

Notebook, 버그 보고서 또는 향상 된 기능 및 기존 노트북에 대 한 추가 제안 기능에 대 한 요청 제공 하는지 여부를 피드백을 환영 합니다. 로 이동 합니다 [Azure Sentinel 커뮤니티 GitHub](https://github.com/Azure/Azure-Sentinel) 문제 또는 분기를 만들고 작성 글 업로드 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Sentinel에서 Jupyter notebook을 사용 하 여 시작 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [사전에 위협에 대 한 hunt](hunting.md)
- [책갈피를 사용 하 여 사냥 하는 동안 흥미로운 정보를 저장 합니다.](bookmarks.md)