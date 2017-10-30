---
title: "Azure Machine Learning 설치 빠른 시작 서비스 | Microsoft Docs"
description: "이 빠른 시작은 Azure Machine Learning 리소스를 만드는 방법 및 Azure Machine Learning Workbench를 설치하는 방법을 보여 줍니다."
services: machine-learning
author: hning86
ms.author: haining, raymondl, chhavib
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: hero-article
ms.date: 10/13/2017
ms.openlocfilehash: 07d06e4de95fcc562bcc76ac5cc4f5cd3483ba6d
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2017
---
# <a name="create-azure-machine-learning-preview-accounts-and-install-azure-machine-learning-workbench"></a>Azure Machine Learning 미리 보기 계정 만들기 및 Azure Machine Learning Workbench 설치
Azure Machine Learning은 통합된 종단 간 데이터 과학 및 고급 분석 솔루션입니다. 이를 통해 전문 데이터 과학자들은 클라우드 규모로 데이터를 준비하고, 실험을 개발하며, 모델을 배포할 수 있습니다.

이 빠른 시작은 Azure Machine Learning 미리 보기에서 실험 및 모델 관리 계정을 만드는 방법을 보여줍니다. 또한 Azure Machine Learning Workbench 데스크톱 응용 프로그램 및 CLI 도구를 설치하는 방법을 보여 줍니다. 그런 다음 [아이리스 꽃 데이터 집합](https://en.wikipedia.org/wiki/iris_flower_data_set)을 사용하여 일부 물리적 특성을 기반으로 아이리스 종류를 예측하는 모델을 만드는 Azure Machine Learning 미리 보기 기능을 둘러봅니다.  

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="prerequisites"></a>필수 조건

현재 다음 운영 체제에만 Azure Machine Learning Workbench 데스크톱 앱을 설치할 수 있습니다. 
- Windows 10
- Windows Server 2016
- macOS Sierra
- macOS High Sierra

## <a name="sign-in-to-the-azure-portal"></a>Azure 포털에 로그인합니다.
[Azure 포털](https://portal.azure.com/)에 로그인합니다.

## <a name="create-azure-machine-learning-accounts"></a>Azure Machine Learning 계정 만들기
Azure Portal을 사용하여 Azure Machine Learning 계정을 프로비전합니다. 
1. 포털의 왼쪽 위 모서리에 있는 **새로 만들기** 단추(+)를 선택합니다.

2. 검색 창에 **Machine Learning**을 입력합니다. **Machine Learning 실험(미리 보기)**이라는 검색 결과를 선택합니다.  별 아이콘을 클릭하여 이 선택 영역을 Azure Portal의 즐겨찾기로 만듭니다.

   ![Azure Machine Learning 검색](media/quickstart-installation/portal-more-services.png)

3. **+ 추가**를 선택하여 새 Machine Learning 실험 계정을 구성합니다. 상세 양식이 열립니다.

   ![Machine Learning 실험 계정](media/quickstart-installation/portal-create-experimentation.png)

4. Machine Learning 실험 양식을 다음 정보로 채웁니다.

   설정|제안 값|설명
   ---|---|---
   실험 계정 이름 | _고유한 이름_ |계정을 식별하는 고유한 이름을 선택합니다. 본인의 이름 또는 실험을 가장 잘 식별하는 부서나 프로젝트 이름을 사용할 수 있습니다. 이름은 2~32자여야 합니다. 영숫자 문자 및 대시(-) 문자만 포함되어야 합니다. 
   구독 | _사용자의 구독_ |실험에 사용하려는 Azure 구독을 선택합니다. 구독이 여러 개인 경우 리소스의 요금이 청구될 적절한 구독을 선택합니다.
   리소스 그룹 | _리소스 그룹_ | 새 리소스 그룹 이름을 만들거나 구독에서 기존 이름을 사용할 수 있습니다.
   위치 | _사용자와 가장 가까운 지역_ | 사용자 및 데이터 리소스에 가장 가까운 위치를 선택합니다.
   사용자 수 | 2 | 사용자 수를 입력합니다. 이 선택 영역은 [가격 책정](https://azure.microsoft.com/pricing/details/machine-learning/)에 영향을 미칩니다. 처음 두 사용자는 무료입니다. 이 빠른 시작의 목적상 사용자 2명을 사용합니다. 나중에 Azure Portal에 필요한 대로 사용자 수를 업데이트할 수 있습니다.
   저장소 계정 | _고유한 이름_ | **새로 만들기**를 선택하고 이름을 입력하여 Azure Storage 계정을 만듭니다. 또는 **기존 항목 사용**을 선택하고 드롭다운 목록에서 기존 저장소 계정을 선택합니다. 저장소 계정은 필수이며 프로젝트 아티팩트를 보유하고 기록 데이터를 실행하는 데 사용됩니다. 
   실험 계정에 대한 작업 영역 | _고유한 이름_ | 새 작업 영역의 이름을 입력합니다. 이름은 2~32자여야 합니다. 영숫자 문자 및 대시(-) 문자만 포함되어야 합니다.
   작업 영역에 대한 소유자 할당 | _사용자 계정_ | 자신의 계정을 작업 영역 소유자로 선택합니다.
   모델 관리 계정 만들기 | *확인* | 실험 계정 만들기 경험의 일부로 Machine Learning 모델 관리 계정을 만드는 옵션도 있습니다. 이 리소스는 모델을 실시간 웹 서비스로 배포하고 관리할 준비가 될 때 사용됩니다. 모델 관리 계정을 실험 계정과 동시에 만드는 것이 좋습니다.
   계정 이름 | _고유한 이름_ | 모델 관리 계정을 식별하는 고유한 이름을 선택합니다. 본인의 이름 또는 실험을 가장 잘 식별하는 부서나 프로젝트 이름을 사용할 수 있습니다. 이름은 2~32자여야 합니다. 영숫자 문자 및 대시(-) 문자만 포함되어야 합니다. 
   모델 관리 가격 책정 계층 | **개발/테스트** | **선택한 가격 책정 계층 없음**을 선택하고 새 모델 관리 계정에 대한 가격 책정 계층을 지정합니다. 비용 절감을 위해 구독에서 사용할 수 있는 경우(제한된 가용성) **개발/테스트** 가격 책정 계층을 선택합니다. 그렇지 않으면 비용 절감을 위해 S1 가격 책정 계층을 선택합니다. **선택**을 클릭하여 가격 책정 계층 선택을 저장합니다. 
   대시보드에 고정 | _확인_ | **대시 보드에 고정** 옵션을 선택하여 Azure Portal의 전면 대시보드 페이지에서 Machine Learning 실험 계정을 간편하게 추적할 수 있습니다.

5. **만들기**를 선택하여 만들기 프로세스를 시작합니다.

6. Azure Portal 도구 모음에서 **알림**(벨 아이콘)을 클릭하여 배포 프로세스를 모니터링합니다. 

   알림에 **배포 진행 중**이 표시됩니다. 완료된 후 상태가 **배포 성공**으로 변경됩니다. 성공하면 Machine Learning 실험 계정 페이지가 열립니다.
   
   ![Azure Portal 알림](media/quickstart-installation/portal-notification.png)

이제 로컬 컴퓨터에서 사용하는 운영 체제에 따라 다음 두 섹션 중 하나에 따라 Azure Machine Learning Workbench를 설치합니다. 

## <a name="install-azure-machine-learning-workbench-on-windows"></a>Windows에서 Azure Machine Learning Workbench 설치
Windows 10, Windows Server 2016 또는 최신 버전을 실행하는 컴퓨터에 Azure Machine Learning Workbench를 설치합니다.

1. 최신 Azure Machine Learning Workbench 설치 관리자 [AmlWorkbenchSetup.msi](https://aka.ms/azureml-wb-msi)를 다운로드합니다.

2. 파일 탐색기에서 다운로드한 설치 관리자 **AmlWorkbenchSetup.msi**를 두 번 클릭합니다.

   >[!IMPORTANT]
   >설치 관리자를 디스크에 완전히 다운로드한 다음 거기에서 실행합니다. 브라우저의 다운로드 위젯에서 직접 실행하지 않습니다.

3. 화면의 지시에 따라 설치를 완료합니다.

   설치 관리자가 Python, Miniconda 및 기타 관련 라이브러리 등 모든 필요한 종속 구성 요소를 다운로드합니다. 모든 구성 요소의 설치를 완료하는 데 한 약 30분쯤 걸릴 수 있습니다. 

4. 이제 Azure Machine Learning Workbench가 다음 디렉터리에 설치되었습니다.
   
   `C:\Users\<user>\AppData\Local\AmlWorkbench`

## <a name="install-azure-machine-learning-workbench-on-macos"></a>macOS에서 Azure Machine Learning Workbench 설치
macOS Sierra를 실행하는 컴퓨터에 Azure Machine Learning Workbench를 설치합니다.

1. [Homebrew](http://brew.sh)를 사용하여 openssl 라이브러리를 설치합니다. 자세한 내용은 [Mac에서 .NET Core에 대한 필수 구성 요소](https://docs.microsoft.com/dotnet/core/macos-prerequisites)를 참조하세요.
   ```
   # install Homebrew first if you don't have it already
   /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

   # install latest openssl needed for .NET Core 1.x
   brew update
   brew install openssl
   mkdir -p /usr/local/lib
   ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
   ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
   ```

2. 최신 Azure Machine Learning Workbench 설치 관리자 [AmlWorkbench.dmg](https://aka.ms/azureml-wb-dmg)를 다운로드합니다.

   >[!IMPORTANT]
   >설치 관리자를 디스크에 완전히 다운로드한 다음 거기에서 실행합니다. 브라우저의 다운로드 위젯에서 직접 실행하지 않습니다.

3. 파인더에서 다운로드한 설치 관리자 **AmlWorkbench.dmg**를 두 번 클릭합니다.

4. 화면의 지시에 따라 설치를 완료합니다.

   설치 관리자가 Python, Miniconda 및 기타 관련 라이브러리 등 모든 필요한 종속 구성 요소를 다운로드합니다. 모든 구성 요소의 설치를 완료하는 데 한 약 30분쯤 걸릴 수 있습니다. 

5. 이제 Azure Machine Learning Workbench가 다음 디렉터리에 설치되었습니다. 

   `/Applications/AmlWorkbench.app`

## <a name="run-azure-machine-learning-workbench-to-sign-in-for-the-first-time"></a>Azure Machine Learning Workbench를 실행하여 처음으로 로그인
1. 설치 프로세스가 완료된 후에 설치 관리자의 마지막 화면에서 **Workbench 시작** 버튼을 선택합니다. 설치 관리자를 닫은 경우 화면에서 Machine Learning Workbench에 대한 바로 가기를 찾고 **Azure Machine Learning Workbench**라는 **시작** 메뉴에서 앱을 시작합니다.

2. 이전에 Azure 리소스를 프로비전하는 데 사용한 것과 같은 계정을 사용하여 Workbench에 로그인합니다. 

3. 로그인 프로세스가 성공하면 Workbench가 이전에 만든 Machine Learning 실험 계정을 찾으려고 합니다. 사용자의 자격 증명이 액세스 권한을 가진 모든 Azure 구독을 검색합니다. 적어도 한 개의 실험 계정을 찾으면 Workbench가 해당 계정을 사용하여 열립니다. 그런 다음 해당 계정에서 찾은 작업 영역 및 프로젝트를 나열합니다. 

   >[!TIP]
   > 두 개 이상의 실험 계정에 대한 액세스 권한이 있는 경우 Workbench 앱의 왼쪽 아래에 있는 아바타 아이콘을 선택하여 다른 계정으로 전환할 수 있습니다.

웹 서비스를 배포하기 위한 환경을 만드는 방법에 대해서는 [배포 환경 설정](deployment-setup-configuration.md)을 참조하세요.

## <a name="create-a-new-project"></a>새 프로젝트 만들기
1. Azure Machine Learning Workbench 앱을 시작하고 로그인합니다. 

2. **파일** > **새 프로젝트**를 선택합니다(또는 **프로젝트** 창에서 **+** 기호를 선택함). 

3. **프로젝트 이름** 및 **프로젝트 디렉터리** 상자를 채웁니다. **프로젝트 설명**은 옵션이지만 유용합니다. **Visualstudio.com GIT 리포지토리 URL** 상자는 지금은 비워 둡니다. 작업 영역을 선택하고 프로젝트 템플릿으로 **아이리스 분류**를 선택합니다.

   >[!TIP]
   >또는 Git 리포지토리 텍스트 상자를 [Visual Studio Team Service](https://www.visualstudio.com)에서 호스팅하는 Git 리포지토리의 URL로 채울 수 있습니다. 이 Git 리포지토리는 이미 존재해야 하며 마스터 분기가 없이 비어 있어야 합니다. 그리고 그에 대한 쓰기 액세스 권한이 있어야 합니다. Git 리포지토리를 지금 추가하면 나중에 시나리오를 로밍 및 공유할 수 있습니다. [자세히 알아보기](using-git-ml-project.md).

4. **만들기** 단추를 선택하여 프로젝트를 만듭니다. 새 프로젝트가 만들어지고 열립니다. 이 시점에서 프로젝트 홈페이지, 데이터 소스, 노트북 및 소스 코드 파일을 탐색할 수 있습니다. 

    >[!TIP]
    >또한 IDE(통합 개발 환경) 링크를 구성하고 그 안에서 프로젝트 디렉터리를 열어서 Visual Studio Code 또는 다른 편집기에서 프로젝트를 열 수 있습니다. [자세히 알아보기](how-to-configure-your-IDE.md). 

## <a name="run-a-python-script"></a>Python 스크립트 실행
로컬 컴퓨터에서 스크립트를 실행해 보겠습니다. 

1. 각 프로젝트는 자체의 **프로젝트 대시보드** 페이지에 열립니다. 응용 프로그램 상단 부근의 명령 모음에서 실행 대상으로 **로컬**을 선택하고 실행할 스크립트로 **iris_sklearn.py**를 선택합니다. 나중에 체크 아웃할 수 있는 샘플에 포함된 다른 파일이 있습니다. 

   ![명령 모음](media/quickstart-installation/run_control.png)

2. **인수** 텍스트 상자에 **0.01**을 입력합니다. 이 숫자는 정규화 속도를 설정하는 코드에 사용됩니다. 선형 회귀 모델을 학습하는 방법을 구성하는 데 사용되는 값입니다. 

3. **실행** 단추를 선택하여 컴퓨터에서 **iris_sklearn.py**를 실행하기 시작합니다. 

   이 코드는 유명한 Python [scikit-learn](http://scikit-learn.org/stable/index.html) 라이브러리의 [로지스틱 회귀](https://en.wikipedia.org/wiki/logistic_regression) 알고리즘을 사용하여 모델을 빌드합니다.

4. **작업** 패널이 아직 보이지 않는 경우 오른쪽에서 밀어 빼내면 **iris_sklearn** 작업이 패널에 추가됩니다. 그 상태는 작업이 실행하기 시작함에 따라 **제출 중**에서 **실행 중**으로 전환된 다음 몇 초 후 **완료됨**으로 전환됩니다. 

   축하합니다. Azure Machine Learning Workbench에서 Python 스크립트를 실행했습니다.

6. 2~4단계를 여러 번 반복합니다. 매번 **10**에서 **0.001**까지의 범위가 지정된 다른 인수 값을 사용합니다.

## <a name="view-run-history"></a>실행 기록 보기
1. **실행** 보기로 이동하고 실행 목록에서 **iris_sklearn.py**를 선택합니다. **iris_sklearn.py**에 대한 실행 기록 대시보드가 열립니다. **iris_sklearn.py**에 대해 실행된 모든 실행을 표시합니다. 

   ![실행 기록 대시보드](media/quickstart-installation/run_view.png)

2. 실행 기록 대시보드는 최상위 메트릭, 기본 그래프 집합 및 각 실행에 대한 메트릭 목록도 표시합니다. 구성을 정렬, 필터링 및 조정하여 이 보기를 사용자 지정할 수 있습니다. 구성 아이콘 또는 필터 아이콘을 선택합니다.

   ![메트릭 및 그래프](media/quickstart-installation/run_dashboard.png)

3. 완료된 실행을 선택하고 해당 특정 실행에 대한 자세한 보기를 확인할 수 있습니다. 세부 정보에는 추가 메트릭, 생성한 파일 및 잠재적으로 유용한 기타 로그가 포함됩니다.

## <a name="next-steps"></a>다음 단계
이제 Azure Machine Learning 실험 계정 및 Azure Machine Learning 모델 관리 계정을 만들었습니다. Azure Machine Learning Workbench 데스크톱 앱 및 명령줄 인터페이스를 설치했습니다. 새 프로젝트를 생성하고 스크립트를 실행하여 모델을 만들고 스크립트의 실행 기록을 탐색했습니다.

아이리스 모델을 웹 서비스로 배포하는 방법을 포함하여 이 워크플로를 더 심층적으로 경험하려면 전체 *아이리스 분류* 자습서를 따르세요. 이 자습서에는 [데이터 준비](tutorial-classifying-iris-part-1.md), [실험](tutorial-classifying-iris-part-2.md) 및 [모델 관리](tutorial-classifying-iris-part-3.md)에 대한 자세한 단계가 포함됩니다. 

> [!div class="nextstepaction"]
> [아이리스 분류 자습서](tutorial-classifying-iris-part-1.md)
