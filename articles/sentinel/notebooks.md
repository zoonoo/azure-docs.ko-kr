---
title: Azure 센티널에서 노트북을 사용 하 여 기능 구하기 | Microsoft Docs
description: 이 문서에서는 Azure 센티널 구하기 기능에서 노트북을 사용 하는 방법을 설명 합니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 5b90ecc1db686b698668b07bd839304b425445ca
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240520"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Jupyter 노트북을 사용 하 여 보안 위협 구하기

Azure 센티널의 기반은 데이터 저장소입니다. 고성능 쿼리, 동적 스키마 및 확장을 대규모 데이터 볼륨으로 결합 합니다. Azure 센티널 포털 및 모든 Azure 센티널 도구는 공통 API를 사용 하 여이 데이터 저장소에 액세스 합니다. [Jupyter](https://jupyter.org/) 노트북 및 Python과 같은 외부 도구에도 동일한 API를 사용할 수 있습니다. 포털에서 많은 일반 작업을 수행할 수 있지만 Jupyter는이 데이터로 수행할 수 있는 작업의 범위를 확장 합니다. 기계 학습, 시각화 및 데이터 분석을 위해 방대한 라이브러리 컬렉션과 함께 전체 프로그래밍 기능을 결합 합니다. 이러한 특성은 Jupyter를 보안 조사 및 사냥을 위한 매력적인 도구로 만듭니다.

![예제 노트북](./media/notebooks/sentinel-notebooks-map.png)

Azure 센티널 포털에 Jupyter 환경을 통합 하 여 데이터를 분석 하는 전자 필기장을 쉽게 만들고 실행할 수 있습니다. *Kqlmagic* 라이브러리는 Azure 센티널에서 쿼리를 가져와서 노트북 내에서 직접 실행할 수 있는 붙이기를 제공 합니다. 쿼리에서는 [Kusto 쿼리 언어](https://kusto.azurewebsites.net/docs/query/index.html)를 사용 합니다. 일부 Microsoft 보안 분석가에서 개발한 여러 노트북은 Azure 센티널로 패키지 됩니다. 이러한 노트북 중 일부는 특정 시나리오를 위해 빌드되고 그대로 사용할 수 있습니다. 다른 항목은 자신의 노트북에서 사용 하기 위해 복사 하거나 적용할 수 있는 기술 및 기능을 설명 하기 위한 샘플로 제공 됩니다. 다른 전자 필기장은 Azure 센티널 커뮤니티 GitHub 에서도 가져올 수 있습니다.

통합 Jupyter 환경에서는 [Azure Notebooks](https://notebooks.azure.com/) 를 사용 하 여 노트북을 저장, 공유 및 실행 합니다. 이러한 노트북은 로컬에서 (Python 환경 및 컴퓨터에 Jupyter가 있는 경우) 또는 Azure Databricks 같은 다른 Jupyter 환경에서 실행할 수도 있습니다.

노트북에는 다음과 같은 두 가지 구성 요소가 있습니다.

- 쿼리 및 코드를 입력 하 고 실행 하 고 실행 결과를 표시 하는 브라우저 기반 인터페이스입니다.
- 코드 자체의 구문 분석 및 실행을 담당 하는 *커널* 입니다. 

Azure Notebooks에서이 커널은 기본적으로 Azure *무료 클라우드 계산 및 저장소* 에서 실행 됩니다. 노트북에 복잡 한 기계 학습 모델 또는 시각화가 포함 된 경우 dsvm ( [데이터 과학 Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) )과 같은 보다 강력한 전용 계산 리소스를 사용 하는 것이 좋습니다. 계정에 있는 노트북은 공유 하도록 선택 하지 않는 한 비공개로 유지 됩니다.

Azure 센티널 노트북은 pandas, matplotlib, bokeh 등 널리 사용 되는 다양 한 Python 라이브러리를 사용 합니다. 다음과 같은 영역을 포함 하 여 선택할 수 있는 다양 한 Python 패키지를 선택할 수 있습니다.

- 시각화 및 그래픽
- 데이터 처리 및 분석
- 통계 및 수치 계산
- 기계 학습 및 심층 학습

또한 [msticpy](https://github.com/Microsoft/msticpy/)라는 패키지에 몇 가지 오픈 소스 Jupyter 보안 도구를 릴리스 했습니다. 이 패키지는 포함 된 많은 노트북에서 사용 됩니다. Msticpy 도구는 새로운 기능 및 개선 사항에 대 한 노트를 만드는 데 도움이 되도록 특별히 설계 되었습니다.

초기 노트북에는 다음이 포함 됩니다.

- **단계별 조사-경고 처리**: 영향을 받는 호스트에서 작업을 분석 하 여 신속 하 게 경고를 심사 할 수 있습니다.
- **단계별 구하기-Windows 호스트 탐색기**: 호스트의 계정 활동, 프로세스 실행, 네트워크 활동 및 기타 이벤트를 탐색할 수 있습니다.  
- 단계별 **탐색-Office365**: 여러 O365 데이터 집합에서 의심 스러운 Office 365 활동을 사냥 합니다.

[Azure 센티널 Community GitHub 리포지토리](https://github.com/Azure/Azure-Sentinel) 는 Microsoft에서 빌드하고 커뮤니티에서 제공 하는 향후 azure 센티널 노트북의 위치입니다.

## <a name="run-a-notebook"></a>노트북 실행

다음 예제에서는 Azure 센티널 포털에서 프로젝트를 전자 필기장으로 채워서 Azure Notebooks 프로젝트를 만듭니다. 이러한 노트북을 사용 하기 전에 노트북의 복사본을 만들고 복사본에 대해 작업을 수행 하는 것이 좋습니다. 복사본에 대 한 작업을 수행 하면 데이터를 덮어쓰지 않고 나중 버전의 전자 필기장으로 안전 하 게 업데이트할 수 있습니다.

1. Azure 센티널 포털의 탐색 메뉴에서 **노트북** 을 클릭 합니다. 새 Azure Notebooks 프로젝트를 만들려면 **Azure 센티널 노트북 복제** 를 클릭 하거나 기존 전자 필기장 프로젝트를 열려면 노트북으로 **이동**을 클릭 합니다.
  
   ![노트북 선택](./media/notebooks/sentinel-azure-notebooks-home.png)

2. 이전 단계에서 **Azure 센티널 노트북 복제** 를 선택한 경우 다음 대화 상자가 표시 됩니다. **가져오기** 를 클릭 하 여 Azure Notebooks 프로젝트에 GitHub 리포지토리를 복제 합니다. 기존 Azure Notebooks 계정이 없으면 계정을 만들고 로그인 하 라는 메시지가 표시 됩니다.

   ![노트북 가져오기](./media/notebooks/sentinel-notebooks-clone.png)

3. 새 프로젝트를 만들 때 프로젝트의 이름을로 기본 이름을 사용 하거나 새 이름을 입력 해야 합니다. **재귀적으로 복제** 옵션을 선택 하지 않습니다 .이 옵션은 연결 된 GitHub 리포지토리를 참조 합니다. **가져오기를** 클릭 하면 GitHub 콘텐츠 복제가 시작 되며이를 완료 하는 데 몇 분 정도 걸릴 수 있습니다.

   ![노트북 가져오기](./media/notebooks/sentinel-create-project.png)

4. **노트북 폴더를** 열어 노트북을 확인 합니다. 각 노트북은 헌트 또는 조사를 수행 하는 단계를 안내 합니다. 노트북 자체에서 또는 간단한 구성 절차를 통해 노트북에 필요한 라이브러리 및 기타 종속성을 설치할 수 있습니다. 노트북 프로젝트를 Azure 센티널 구독에 다시 연결 하는 구성은 앞의 단계에서 자동으로 프로 비전 됩니다. 노트북은 Azure 센티널 Log Analytics 작업 영역에 대해 실행할 준비가 되었습니다.

   ![리포지토리 가져오기](./media/notebooks/sentinel-open-notebook1.png)

5. 노트북을 엽니다. 무료 계산은 기본적으로 노트북을 실행 하기 위해 선택 됩니다 (강조 표시 됨). 사용할 DSVM을 구성한 경우 (위 참조) 첫 번째 노트북을 열기 전에 DSVM을 선택 하 고 인증 합니다. 노트북을 클릭 하 여 엽니다.

   ![노트북 선택](./media/notebooks/sentinel-open-notebook2.png)

6. Python 버전을 선택 합니다. 노트북을 처음 열면 커널 버전을 선택 하 라는 메시지가 표시 될 수 있습니다. 그렇지 않은 경우 다음과 같이 사용할 커널을 선택 합니다. Python 3.6 이상이 선택 된 커널 (노트북 창의 오른쪽 상단)에 있어야 합니다.

   ![노트북 선택](./media/notebooks/sentinel-select-kernel.png)

Azure 센티널에서 데이터를 쿼리 하는 방법에 대 한 간략 한 소개는 주 전자 필기장 폴더의 [Getstarted](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) 노트북을 참조 하세요. **샘플** 노트북 하위 폴더에서 추가 샘플 노트북을 찾을 수 있습니다. 샘플 노트북은 데이터와 함께 저장 되어 의도 된 출력을 쉽게 볼 수 있습니다 ( [nbviewer](https://nbviewer.jupyter.org/)에서 보는 것이 좋습니다). **영문** 폴더에는 기본 Python 버전 설정, dsvm 구성, 노트북에서 Azure 센티널 책갈피 만들기 등을 설명 하는 노트북이 포함 되어 있습니다.

이러한 노트북은 사용자 고유의 노트북 개발에 사용할 수 있는 유용한 도구와 그림 및 코드 샘플로 제공 됩니다.

의견, 제안, 기능 요청, 전자 필기장 제공, 버그 보고서, 기존 전자 필기장에 대 한 개선 사항 및 추가 사항이 있는지 여부를 환영 합니다. [Azure 센티널 Community GitHub](https://github.com/Azure/Azure-Sentinel) 로 이동 하 여 문제를 만들거나 참여를 업로드 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure 센티널에서 Jupyter 노트북을 사용 하 여 시작 하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [위협에 대 한 사전 구하기](hunting.md)
- [책갈피를 사용 하 여 검색할 때 관심 있는 정보 저장](bookmarks.md)
