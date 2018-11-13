---
title: Azure 및 Azure Stack을 사용 하 여 솔루션을 학습 하는 edge 컴퓨터를 만들 | Microsoft Docs
description: Azure 및 Azure Stack을 사용 하 여 Python을 사용 하 여 edge machine learning 솔루션을 만드는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/26/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 59696245dc33302c65aee5a39dc856926347b8fb
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51569117"
---
# <a name="tutorial-create-an-edge-machine-learning-solution-with-azure-and-azure-stack"></a>자습서: Azure 및 Azure Stack을 사용 하 여 솔루션을 학습 하는 edge 컴퓨터 만들기

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure 및 Azure Stack을 사용 하 여 솔루션을 학습 하는 지 컴퓨터를 만드는 방법에 알아봅니다.

이 자습서에서는 샘플 환경을 빌드합니다.

> [!div class="checklist"]
> - 저장소 계정 및 상주할 데이터 정리에 대 한 컨테이너를 만듭니다.
> - Azure portal에서 Ubuntu 데이터 과학 Virtual Machine (DSVM)을 만듭니다.
> - 빌드 및 모델을 학습 하기 위해 Azure에서 Azure Machine Learning 서비스를 배포 합니다.
> - Azure Machine Learning 서비스 계정을 만듭니다.
> - 배포 하 고 Azure Container Registry를 사용 합니다.
> - Azure Stack에 Kubernetes 클러스터를 배포 합니다.
> - 데이터에 대 한 Azure Stack 저장소 계정 및 저장소 큐를 만듭니다.
> - Azure Stack에서 데이터 정리를 Azure로 이동 하는 새 Azure Stack 함수를 만듭니다.

**이 솔루션을 사용 하는 경우**

 -  조직에서 사용 하는 DevOps 접근 방식 또는 하나에 대 한 계획에 가까운 미래입니다.
 -  Azure Stack 구현 및 공용 클라우드 전반 CI/CD 사례를 구현 해야 합니다.
 -  클라우드 및 온-프레미스 환경에서 CI/CD 파이프라인 통합 해야 합니다.
 -  클라우드 또는 온-프레미스 서비스를 사용 하 여 응용 프로그램을 개발 하는 기능을 해야 합니다.
 -  클라우드 및 온-프레미스 응용 프로그램에서 일관성 있는 개발자 기술을 이용 하려고 합니다.

> [!Tip]  
> ![하이브리드 pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack은 Azure의 확장입니다. Azure Stack은 유연성 및 혁신을 온-프레미스 환경에 컴퓨팅 및 하이브리드 앱을 어디서 나 빌드 및 배포 할 수 있는 유일한 하이브리드 클라우드를 사용 하도록 설정 하는 클라우드를 제공 합니다.  
> 
> 백서 [하이브리드 응용 프로그램에 대 한 디자인 고려 사항](https://aka.ms/hybrid-cloud-applications-pillars) (배치, 확장성, 가용성, 복원 력, 관리 효율성 및 보안) 소프트웨어 품질 핵심 요소를 디자인, 배포 및 운영에 대 한 검토 하이브리드 응용 프로그램입니다. 디자인 고려 사항을 프로덕션 환경에서 문제를 최소화 되는 하이브리드 응용 프로그램 디자인을 최적화 하는 데 도움이 됩니다.

## <a name="prerequisites"></a>필수 조건

몇 가지 구성 요소가 사용 사례를 빌드하는 데 필요한 및 준비 하려면 다소 시간이 걸릴 수 있습니다.

 -  Azure OEM/하드웨어 파트너는 Azure Stack 프로덕션에 배포할 수 있습니다. 및 모든 사용자는 ASDK를 배포할 수 있습니다.

 -  Azure Stack 운영자 해야도 App Service 배포, 계획 및 제품은 테 넌 트 구독을 만들고 Windows Server 2016 이미지를 추가 합니다.

 -  하이브리드 네트워크 및 App Service 설정 필요 (에 대해 자세히 알아보세요 [Vnet와 앱 통합.](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet))

 -  Private [빌드 및 릴리스 에이전트](https://docs.microsoft.com/vsts/pipelines/agents/agents?view=vsts) (사용 하는 모든 기존 구성 요소 확인 시작 하기 전에 요구 사항을 충족 합니다.) 배포 하기 전에 VSTS 통합을 설정 해야 합니다에 대 한

Azure 및 Azure Stack에 대 한 사전 지식이 필요합니다. 계속 하기 전에 자세한 내용은 다음이 항목을 사용 하 여 시작:

 -  [Azure 소개](https://azure.microsoft.com/overview/what-is-azure/)

 -  [Azure Stack의 주요 개념](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

 -  [Azure Stack 하이브리드 CI/CD 솔루션 가이드](/azure/azure-stack/user/azure-stack-solution-pipeline)

**Azure**

 -  Azure 구독 (만들기는 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))

 -  새 웹 앱 URL을 만든 [웹 앱](https://docs.microsoft.com/azure/app-service/app-service-web-overview) Azure에서

 -  배포 [Azure 컨테이너 서비스 (ACS) Kubernetes Azure에서](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)

 -  Azure Machine Learning 서비스 (미리 보기)의 배포 [4 부분으로 이루어진 자습서](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/tutorial-classifying-iris-part-1)

 -  Azure Machine Learning 실험 [계정](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/experimentation-service-template)

**Azure Stack**

 -  Azure Stack 통합 시스템 또는 Azure Stack 개발 키트 배포 합니다.

    - Azure Stack에서 설치 지침을 찾을 [Azure Stack Development Kit 설치](/articles/azure-stack/asdk/asdk-install)합니다.
     - [https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1) 이 설치를 완료 하려면 몇 시간 필요할 수 있습니다.

 -  배포 [App Service](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) Azure Stack에 PaaS 서비스

 -  A [계획/제품](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) Azure Stack 환경 내에서

 -  A [테 넌 트 구독](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) Azure Stack 환경 내에서

 -  Ubuntu Server VM 이미지 (사용할 수 있습니다 [Azure Stack Marketplace](https://buildazure.com/2016/05/04/azure-marketplace-ubuntu-server-16-04-lts/))이 VM을 개인 빌드 에이전트 뿐만 아니라 Kubernetes Vm으로 Azure Stack에서 테 넌 트 구독에 빌드됩니다. 이 이미지를 사용할 수 없는 경우 Azure Stack 운영자 환경에 추가이 되도록 지원할 수 있습니다. (사용 하지 마십시오의 ubuntu에 18.04 빌드가 현재 지원 되지 않습니다.)

 -  (참고 나중에 사용할 새 웹 앱 URL) 테 넌 트 구독 내에서 웹 앱

 -  VSTS Linux 기반 개인 빌드 에이전트 가상 머신의 테 넌 트 구독 내에서 배포

 -  배포는 [Azure 컨테이너 서비스 (ACS) Kubernetes Azure Stack에서](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)

**개발자 도구**

 -  [VSTS 작업 영역](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services) (등록 프로세스는 "MyFirstProject" 라는 프로젝트를 생성 하는 데 사용)

 -  [Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) 설치 하 고 [VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services) 로그온

 -  [로컬 클론](https://www.visualstudio.com/docs/git/gitquickstart) 프로젝트의

 -  [Windows 10 용 Linux 하위 시스템](https://docs.microsoft.com/windows/wsl/install-win10) 설치 (BASH 및 SSH)

 -  [Windows 용 docker](https://download.docker.com/win/stable/Docker%20for%20Windows%20Installer.exe) 설치

 -  [Azure Machine Learning Workbench (미리 보기)](https://aka.ms/azureml-wb-msi) 설치

 -  [Python](https://www.python.org/ftp/python/3.7.0/python-3.7.0rc1-amd64.exe) 환경 설치

**VSTS**

 -  **VSTS 계정입니다.** 신속 하 게 빌드, 테스트 및 배포에 대 한 연속 통합을 설정 합니다. VSTS 계정에 대 한 자세한 내용은 참조 하십시오 [VSTS 계정을 만들](https://docs.microsoft.com/vsts/accounts/create-account-msa-or-work-student?view=vsts)합니다.

 -  **코드 리포지토리입니다.** GitHub, BitBucket, DropBox, Onedrive, 및 TFS의 기존 코드 리포지토리를 사용 하는 VSTS 플랫폼 개발 파이프라인을 간소화 하기 위해 여러 코드 리포지토리를 활용할 수 있습니다. 코드에 대 한 자세한 내용은 저장소 참조 [Git 및 VSTS 시작](https://docs.microsoft.com/vsts/git/gitquickstart?view=vsts&tabs=visual-studio) 자습서입니다.

 -  **서비스 연결 합니다.** 테스트, 빌드 또는 배포에 대 한 작업을 실행 하는 외부 및 원격 서비스에 연결 합니다. VSTS에 대 한 자세한 내용은 서비스 연결 참조 [빌드 및 릴리스 서비스 끝점](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints?view=vsts)합니다.

 -  **빌드 정의 합니다.** 작업 카탈로그를 통해 작업 집합을 작성 및 자동화 된 빌드 프로세스를 정의 합니다. 빌드에 대 한 자세한 내용은 정의 참조 [빌드 정의 만들기](https://docs.microsoft.com/vsts/build-release/actions/ci-cd-part-1?view=vsts) 설명서.

 -  **릴리스 정의 합니다.** 응용 프로그램 아티팩트 배포 되는 환경의 컬렉션에 대 한 배포 프로세스를 정의 합니다. 릴리스에 대 한 자세한 내용은 정의 참조 [릴리스 정의 만들기](https://docs.microsoft.com/vsts/build-release/actions/ci-cd-part-1?view=vsts) 설명서.

 -  **호스 티 드 VSTS Linux 빌드 에이전트 풀입니다.** 신속 하 게 빌드, 테스트 및 응용 프로그램을 배포할 Microsoft 관리 및 유지 관리를 사용 하 여 호스트 된 에이전트. VSTS 빌드 호스트에 대 한 자세한 내용은 에이전트 참조 [호스트 에이전트](https://docs.microsoft.com/vsts/build-release/concepts/agents/hosted?view=vsts) 설명서.

## <a name="step-1-create-a-storage-account"></a>1 단계: 저장소 계정 만들기

저장소 계정 및 상주할 데이터 정리에 대 한 컨테이너를 만듭니다.

1.  에 로그인 합니다 [ *Azure portal*](https://portal.azure.com/)합니다.

2.  Azure portal에서 서비스의 메뉴를 열고 왼쪽의 메뉴 확장 **모든 서비스**합니다. 아래로 스크롤하여 **저장소** 선택한 **저장소 계정**합니다. 에 * * 저장소 계정 * * 창 **추가**합니다.

3.  저장소 계정의 이름을 입력합니다.

    > [!Note]  
    > Storage 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 포함할 수 있습니다. 저장소 계정 이름은 Azure 내에서 고유 해야 합니다. Azure portal는 선택한 저장소 계정 이름은 이미 사용 중인지 여부를 나타냅니다.

4.  사용할 배포 모델을 지정 합니다. **Resource Manager**합니다.

5.  저장소 계정 유형을 선택 합니다. **범용 V1**, 성능 계층을 지정 합니다: **표준**합니다.

6.  저장소 계정의 복제 옵션을 선택 합니다. **GRS**합니다.

7.  새 저장소 계정 구독을 선택 합니다.

8.  새 리소스 그룹을 지정하거나 기존 리소스 그룹을 선택합니다.

9.  저장소 계정의 지리적 위치를 선택 합니다.

10. **만들기**를 선택하여 저장소 계정을 만듭니다.

    ![대체 텍스트](\media\azure-stack-solution-machine-learning\image1.png)

11.  최근에 만든 저장소 계정을 선택 합니다.

12.  선택 **Blob**합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image2.png)

13.  선택 **+ 컨테이너** 에서 선택한 **컨테이너**합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image3.png)

14.  컨테이너 이름을 **uploadeddata** 하 고 액세스 유형을 선택 **컨테이너**합니다.

15.  선택 **만들**합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image4.png)

## <a name="step-2-create-a-data-science-virtual-machine"></a>2 단계: 데이터 과학 Virtual Machine 만들기

Azure portal에서 Ubuntu 데이터 과학 Virtual Machine (DSVM)을 만듭니다.

1.  Azure portal에 로그온 [https://portal.azure.com](https://portal.azure.com/)

2.  선택 된 **+ 새로 만들기** 링크 및 CSP에 대 한"데이터 과학 Virtual Machine에 대 한 Linux Ubuntu 검색

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image5.png)

1.  선택 **Linux (Ubuntu) 용 데이터 과학 Virtual Machine** 목록에 따라는 화면의 지침 DSVM을 만듭니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image6.png)

> ! [중요]  
> **선택할** 암호 * *으로*인증 유형*합니다.

새로 만든된 저장소 계정으로 동일한 리소스 그룹에 새 DSVM을 배치 합니다. 모든 가장자리 ML 개체는이 리소스 그룹 내에서 Azure에 배포 됩니다.

1.  선택적 기능 설정 구성

    a.  선택 된 **저장소 계정** 앞에서 만든 합니다.

    b.  새 **가상 네트워크**를 **서브넷**, 및 **공용 IP** 만들도록 리소스 그룹 이름에 따라 이름을 기본적으로 합니다.

    다.  새 **NSG** 자동으로 만들도록이 이미 적용 되어 올바른 규칙입니다.

    d.  에 대 한 합니다 **진단 저장소 계정이**, 이전에 만든 저장소 계정을 선택 합니다.

    e.  참고:을 사용 하도록 설정 하 고 Azure 구독에 대해 구성 된 AAD에 Azure 리소스에 대 한 관리 되는 id 수도 함께 활성화 합니다.

2.  **확인**을 선택합니다.

### <a name="connect-to-the-dsvm"></a>DSVM에 연결

DSVM에서 만들어지면 연결 VM에 Windows 하위 시스템에서 Linux에 대 한 합니다.

```Bash  
    ssh <user>@<DSVM Public IP>
```

1.  VM 연결을 확인 하 라는 메시지가 나타나면 예를 입력 합니다.

2.  DSVM에 만든 암호를 입력 합니다.

### <a name="update-the-dsvm"></a>DSVM를 업데이트 합니다. 

```Bash  
sudo su 
apt-get update 
apt-get -y upgrade 
apt-get -y dist-upgrade 
apt-get -y autoremove
```

## <a name="step-3-deploy-azure-machine-learning-services"></a>3 단계: Azure Machine Learning 서비스 배포

Azure Machine Learning에서 Azure 서비스를 배포 합니다.

Azure Machine Learning 서비스(미리 보기)는 통합된 종단 간 데이터 과학 및 고급 분석 솔루션입니다. 이를 통해 전문 데이터 과학자들은 클라우드 규모로 데이터를 준비하고, 실험을 개발하며, 모델을 배포할 수 있습니다.

이 섹션에 설명합니다.

> [!div class="checklist"]
> - Azure Machine Learning 서비스에 대한 서비스 계정 만들기
> - Azure Machine Learning Workbench를 설치하고 로그인합니다.
> - Workbench에서 프로젝트 만들기
> - 해당 프로젝트에서 스크립트 실행
> - CLI(명령줄 인터페이스)에 액세스

Microsoft Azure 포트폴리오의 일부인 Azure Machine Learning 서비스에는 Azure 구독이 필요합니다. Azure 구독을 만들려면을 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

리소스 그룹, Virtual Machines 및 기타 자산 등의 자산을 만들려면 적절 한 권한이 필요 합니다.

Azure Machine Learning Workbench 응용 프로그램은 다음 운영 체제에 설치할 수 있습니다.

 -  Windows 10 또는 Windows Server 2016
 -  macOS Sierra 또는 High Sierra

## <a name="step-4-create-azure-machine-learning-services"></a>4 단계: Azure Machine Learning 서비스 만들기

Azure Machine Learning 서비스 계정을 만듭니다.

Azure Machine Learning 계정을 프로 비전 하려면 Azure portal을 사용 합니다.

1.  에 로그인 합니다 [Azure portal](https://portal.azure.com/) 사용할 Azure 구독에 대 한 자격 증명을 사용 합니다. Azure 구독을 만들려면을 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image7.png)

1.  포털의 왼쪽 상단 모서리에서 **리소스 만들기** 단추(+)를 선택합니다.

    ![Azure Portal에서 리소스 만들기](media\azure-stack-solution-machine-learning\image8.png)

1.  검색 창에 **Machine Learning**을 입력합니다. **Machine Learning 실험(미리 보기)** 이라는 검색 결과를 선택합니다.

    ![Azure Machine Learning 검색](media\azure-stack-solution-machine-learning\image9.png)

1.  에 **Machine Learning 실험** 창에 선택한 아래쪽으로 스크롤하여 **만들기** 실험 계정을 정의 하려면.

    ![Azure Machine Learning - 실험 계정 만들기](media\azure-stack-solution-machine-learning\image10.png)

1.  에 **ML 실험** 창 Machine Learning 실험 계정을 구성 합니다.

    | 설정 | 자습서에 제안된 값 | 설명 |
    |---------------------------------------|----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | 실험 계정 이름 | 고유 이름 | 계정을 식별 하는 고유한 이름을 입력 합니다. 실험을 가장 잘 식별 하는 부서나 프로젝트 이름을 사용 합니다. 이름은 2~32자여야 합니다. 영숫자 문자 및 대시(-) 문자만 포함되어야 합니다. |
    | 구독 | 구독 | 실험에 사용할 Azure 구독을 선택 합니다. 여러 구독이 있는 경우 리소스가 과금 될 적절 한 구독을 선택 합니다. |
    | 리소스 그룹 | 리소스 그룹 | 구독에서 기존 리소스 그룹을 사용 하거나이 실험 계정에 대 한 새 리소스 그룹을 만들려면 이름을 입력 합니다. |
    | 위치 | 사용자에 게 가장 가까운 지역 | 사용자 및 데이터 리소스에 가장 가까운 위치를 선택 합니다. |
    | 사용자 수 | 2 | 사용자 수를 입력합니다. [사용자 수가 가격에 미치는 영향](https://azure.microsoft.com/pricing/details/machine-learning/)을 알아 보세요.<br><br>이 빠른 시작에서는 두 명의 사용자가 필요 합니다. 사용자는 필요에 따라 Azure Portal에서 추가하거나 삭제할 수 있습니다. |
    | Storage 계정 | 고유 이름 | **새로 만들기**를 선택하고 이름을 입력하여 [Azure 저장소 계정](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=portal)을 만듭니다. 이름은 3~24자여야 하며, 영숫자만 포함해야 합니다. 또는 선택할 **기존 항목 사용** 목록에서 기존 저장소 계정을 선택 합니다. 저장소 계정은 필수이며 프로젝트 아티팩트를 보유하고 기록 데이터를 실행하는 데 사용됩니다. |
    | 실험 계정에 대한 작업 영역 | IrisGarden<br>(자습서에서 사용되는 이름) | 이 계정의 작업 영역에 대한 이름을 입력합니다. 이름은 2~32자여야 합니다. 영숫자 문자 및 대시(-) 문자만 포함되어야 합니다. 이 작업 영역 만들기, 관리 및 실험을 게시 하는 데 필요한 도구를 포함 합니다. |
    | 작업 영역에 대한 소유자 할당 | 계정 | 자체 계정을 작업 영역 소유자로 선택 합니다. |
    | 모델 관리 계정 만들기 | **확인** | 모델 관리 계정을 만듭니다. 이 배포 및 실시간 웹 서비스로 모델 관리에 사용 됩니다. <br><br>선택 사항 이지만, 모델 관리 계정을 실험 계정과 동시에 만드는 것이 좋습니다. |
    | 계정 이름 | 고유 이름 | 모델 관리 계정을 식별 하는 고유한 이름을 선택 합니다. 부서를 사용 하 여 또는 프로젝트 실험을 가장 잘 식별 하는 이름입니다. 이름은 2~32자여야 합니다. 영숫자 문자 및 대시(-) 문자만 포함되어야 합니다. |
    | 모델 관리 가격 책정 계층 | **개발/테스트** | 선택 **가격 책정 계층이 선택 되지 않음** 새 모델 관리 계정에 대 한 가격 책정 계층을 지정 합니다. 비용 절감에 대 한 구독 (제한적으로 사용 가능)에서 사용 가능한 경우 DEVTEST 가격 책정 계층을 선택 합니다. 그렇지 않으면 S1 가격 책정 계층을 선택합니다. 가격 책정 계층 선택을 저장 하려면 선택 합니다. |
    | 대시보드에 고정 | 선택 | 선택 된 **대시보드에 고정** Azure portal의 전면 대시보드 페이지에서 Machine Learning 실험 계정 쉽게 추적할 수 있도록 하는 옵션입니다. |

    ![Machine Learning 실험 계정 구성](media\azure-stack-solution-machine-learning\image11.png)

1.  **만들기**를 클릭하여 모델 관리 계정과 함께 실험 계정을 만드는 프로세스를 시작합니다.

    ![Machine Learning 실험 계정 구성](media\azure-stack-solution-machine-learning\image12.png)

    계정을 만들려면 몇 분 정도 걸릴 수 있습니다. Azure portal 도구 모음에서 알림 아이콘 (벨)을 선택 하 여 배포 프로세스의 상태를 확인 합니다.

    ![Azure Portal 알림](media\azure-stack-solution-machine-learning\image13.png)

### <a name="install-and-log-in-to-workbench"></a>설치 하 고 workbench에 로그인 

Azure Machine Learning Workbench를 이제 Windows나 macOS에서 사용할 수 있습니다. [지원되는 플랫폼](https://docs.microsoft.com/azure/machine-learning/service/quickstart-installation) 목록을 참조하세요.

**경고:** 설치를 완료 하는 데 약 1 걸릴 수 있습니다.

1.  최신 Workbench 설치 관리자를 다운로드하여 시작합니다.

    > [!Important]  
    > 설치 관리자를 디스크에 완전히 다운로드한 다음, 거기에서 실행합니다. 브라우저의 다운로드 위젯에서 직접 실행 하지 마십시오.<br>**On Windows:<br>**  를 합니다. [AmlWorkbenchSetup.msi](https://aka.ms/azureml-wb-msi)를 다운로드합니다.<br>b. 다운로드한 설치 관리자를 파일 탐색기에서 두 번 클릭합니다.

1.  에 따라는 화면의 완료 될 때까지 설치 관리자의 지침입니다.

    * * 설치 만큼 30 분 정도 걸릴 수 있습니다. **
    
    `Windows: C:\\Users\\<user>\\AppData\\Local\\AmlWorkbench`
    
    설치 관리자를 다운로드 하 고 Python, Miniconda 및 기타 관련 라이브러리 등 모든 필요한 종속성을 설정 합니다. 이 설치에는 Azure 플랫폼 간 명령줄 도구 또는 Azure CLI도 포함 됩니다.

1.  설치 관리자의 마지막 화면에서 **Launch Workbench**(Workbench 시작) 단추를 선택하여 Workbench를 시작합니다.

    설치 관리자를 닫으면를 사용 하 여 시작 합니다 **Machine Learning Workbench** 바탕 화면 바로 가기에서 합니다.

1.  선택 **Microsoft로 로그인** Azure Machine Learning Workbench를 사용 하 여 인증 합니다. 실험 및 모델 관리 계정을 만드는 데 Azure portal에서 동일한 자격 증명을 사용 합니다.

    로그인 되 면 Workbench에서 Azure 구독을 찾아 모든 작업 영역 및 해당 계정과 연결 된 프로젝트를 표시 하는 첫 번째 실험 계정을 사용 합니다.

    > [!Tip]  
    > Workbench 응용 프로그램 창의 왼쪽 아래 모서리에 있는 아이콘을 사용 하 여 다른 실험 계정으로 전환 합니다.

### <a name="create-a-new-project-in-workbench"></a>Workbench에서 새 프로젝트 만들기

1.  Azure Machine Learning Workbench 앱을 열고 필요한 경우 로그인 합니다.

    - Windows를 사용 하 여를 시작 합니다 **Machine Learning Workbench** 바탕 화면 바로 가기에서 합니다.

    - macOS에서는 실행 패드에서 **Azure ML Workbench**를 선택합니다.

1.  **프로젝트** 창에서 더하기 기호(+)를 선택하고 **새 프로젝트**를 선택합니다.

    ![새 작업 영역](media\azure-stack-solution-machine-learning\image14.png)

1.  양식 필드에 정보를 채우고 **만들기** 단추를 선택하여 Workbench에 새 프로젝트를 만듭니다.

    | 필드 | 자습서에 제안된 값 | 설명 |
    |-------------------------------------|------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | 프로젝트 이름 | myIris | 계정을 식별 하는 고유한 이름을 입력 합니다. 부서를 사용 하 여 또는 프로젝트 실험을 가장 잘 식별 하는 이름입니다. 이름은 2~32자여야 합니다. 영숫자 문자 및 대시(-) 문자만 포함되어야 합니다. |
    | 프로젝트 디렉터리 | c:\Temp\ | 프로젝트가 만들어지는 디렉터리를 지정합니다. |
    | 프로젝트 설명 | 비워 둡니다. | 프로젝트를 설명하기에 유용한 선택적 필드입니다. |
    | Visualstudio.com GIT 리포지토리 URL | 비워 둡니다. | 선택적 필드입니다. 소스 제어 및 공동 작업에 대 한 Visual Studio Team Services에서 Git 리포지토리를 사용 하 여 프로젝트를 연결 합니다. [리포지토리를 설정 하는 방법을 알아봅니다](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/using-git-ml-project)합니다. |
    | 선택한 작업 영역 | IrisGarden(있는 경우) | Azure portal에서 실험 계정에 대해 만든 작업 영역을 선택 합니다. <br>빠른 시작을 사용 하 여, IrisGarden의 이름으로 작업 영역에 나열 됩니다. 실험 계정 이름 또는 기본 계정 이름을 사용 하 여 작업 영역을 사용 하십시오. |
    | 프로젝트 템플릿 | 아이리스 분류 | 템플릿에 제품을 탐색 하는 데 사용 되는 데이터 및 스크립트에 포함 됩니다. 이 템플릿은 스크립트 및이 설명서 사이트에서이 빠른 시작 및 기타 자습서에 필요한 데이터를 포함 합니다. |

    ![새 프로젝트](media\azure-stack-solution-machine-learning\image15.png)

1.  새 프로젝트가 만들어지고 프로젝트 대시보드에서 해당 프로젝트가 열립니다. 프로젝트 홈페이지, 데이터 소스, 노트북 및 소스 코드 파일을 탐색 합니다.

    ![프로젝트 열기](media\azure-stack-solution-machine-learning\image16.png)

### <a name="attach-a-dsvm-compute-target"></a>DSVM 계산 대상 연결

DSVM이 만들어지면 Azure ML 프로젝트에 연결 합니다.

1.  Azure ML Workbench 앱 내에서 Azure ML Workbench CLI를 선택 하 여 시작 **파일**->**PowerShell 열기**

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image17.png)

1.  프롬프트를 열었습니다. PowerShell 명령을 사용 하 여:

    ```PowerShell  
        az login
    ```

1.  다음과 같은 메시지가 나타납니다.

     ![대체 텍스트](media\azure-stack-solution-machine-learning\image18.png)

1.  프롬프트에 설명 된 대로 사이트를 찾아 제공 되는 코드를 입력 합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image19.png)

1.  메시지가 표시 되 면 계속 선택한 Azure ML 실험 계정에 연결 된 Azure 계정을 선택 합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image20.png)

1.  Azure ML Workbench CLI는 다음 프롬프트를 보냅니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image21.png)

1.  ML 계정 및 작업 영역 로그인 성공으로 표시 되 면 DSVM을 연결 합니다.

    ```PowerShell  
        az ml computetarget attach remotedocker --name <compute target name> --address <ip address or FQDN> --username <admin username> --password <admin password>
    ```

    다음 알림이 표시 됩니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image22.png)

    ```PowerShell  
        # prepare the Docker image on the DSVM 
        az ml experiment prepare -c <compute target name>
    ```

이 프로세스는 시간이 다소 소요 될 하 고 다양 한 docker 이미지를 끌어옵니다를 등록 하 고 필요한 코드 및 응용 프로그램에 적용 하는 상당한 양의 텍스트 생성 됩니다.

이제이 DSVM에서 실험을 실행할 수 있습니다.

### <a name="create-a-data-preparation-package"></a>데이터 준비 패키지 만들기

다음으로, Azure Machine Learning Workbench에서 데이터 준비를 시작 합니다. Workbench에서 수행 하는 각 변환은 JSON 형식으로 로컬 데이터 준비 패키지에 저장 됩니다 (\*.dprep 파일). 이 데이터 준비 패키지는 Workbench에서 데이터 준비 작업에 대 한 기본 컨테이너입니다.

이 데이터 준비 패키지 있습니다 수 넘겨 나중와 같은 로컬 C 런타임\#/CoreCLR, Scala/Spark 또는 Scala/HDI 합니다.

1.  폴더 아이콘을 선택하여 [파일] 보기를 연 다음, **iris.csv**를 선택하여 이 파일을 엽니다.

    이 파일에는 5개 열, 50개 행이 있는 테이블이 포함되어 있습니다. 네 개의 열은 숫자 기능 열입니다. 다섯 번째 열은 문자열 대상 열. 열에는 헤더 이름이 없습니다.

    ![iris.csv](media\azure-stack-solution-machine-learning\image23.png)

1.  **데이터 뷰**에서 더하기 기호(**+**)를 선택하여 새 데이터 원본을 추가합니다. **데이터 원본 추가** 페이지가 열립니다.

    ![Azure Machine Learning Workbench의 데이터 뷰](media\azure-stack-solution-machine-learning\image24.png)

1.  선택 **텍스트 파일 (\*.csv \*.json, \*.txt.,...)** .

    ![Azure Machine Learning Workbench에서 데이터 원본](media\azure-stack-solution-machine-learning\image25.png)

1.  **다음**을 선택합니다.

2.  파일을 찾습니다 **iris.csv**, 선택한 **마침**합니다. 이렇게 하면 구분 기호 및 데이터 형식과 같은 매개 변수에 대한 기본값이 사용됩니다.

    > [!Important]  
    > 선택 된 **iris.csv** 이 연습에 대 한 현재 프로젝트 디렉터리에서 파일입니다. 그렇지 않으면 이후 단계가 실패할 수 있습니다.

    ![아이리스 선택](media\azure-stack-solution-machine-learning\image26.png)

1.  라는 새 파일 `*iris-1.dsource` 만들어집니다. 파일을 사용 하 여 고유 하 게 라고 `-1` 샘플 프로젝트는 번호가 이미 제공 하므로 **iris.dsource** 파일입니다.

    파일이 열리고 데이터가 표시됩니다. 일련의 열 머리글에서 **Column1** 하 **Column5**,이 데이터 집합에 자동으로 추가 됩니다. 아래쪽으로 스크롤하여 데이터 집합의 마지막 행이 비어 있는지 확인 합니다. CSV 파일에 여분의 줄 바꿈이 인해 행이 비어 있습니다.

    ![아이리스 데이터 뷰](media\azure-stack-solution-machine-learning\image27.png)

1.  **메트릭** 단추를 선택합니다. 히스토그램이 생성되어 표시됩니다.

    선택 하 여 데이터 뷰로 다시 전환 합니다 **데이터** 단추입니다.

    ![아이리스 데이터 뷰](media\azure-stack-solution-machine-learning\image28.png)

1.  히스토그램을 관찰합니다. 각 열에 대한 통계의 전체 집합이 계산됩니다.

    ![아이리스 데이터 뷰](media\azure-stack-solution-machine-learning\image29.png)

1.  **준비** 단추를 선택하여 데이터 준비 패키지를 만들기 시작합니다. **준비** 대화 상자가 열립니다.

    샘플 프로젝트에 포함 되어는 **iris.dprep** 데이터 준비 파일이 기본적으로 선택 됩니다.

    ![아이리스 데이터 뷰](media\azure-stack-solution-machine-learning\image30.png)

1.  선택 하 여 새 데이터 준비 패키지를 만들 **+ 새 데이터 준비 패키지** 합니다.

    ![아이리스 데이터 뷰](media\azure-stack-solution-machine-learning\image31.png)

1.  패키지 이름에 대한 새 값(**iris-1** 사용)을 입력한 다음, **확인**을 선택합니다.

    새 데이터 준비 패키지를 명명 된 **iris-1.dprep** 만들어지고 데이터 준비 편집기에서 열립니다.

    ![아이리스 데이터 뷰](media\azure-stack-solution-machine-learning\image32.png)

    다음으로, 데이터 준비는 필요 합니다.

1.  각 열 머리글을 선택하여 헤더 텍스트를 편집할 수 있도록 합니다. 그런 다음, 각 열의 이름을 다음과 같이 바꿉니다.

    순서 대로 입력 **꽃 받침 길이**를 **꽃 받침 너비**를 **꽃잎 길이**를 **꽃잎 너비**, 및 **종류** 5 개 열에 대해 각각.

    ![열 이름 바꾸기](media\azure-stack-solution-machine-learning\image33.png)

1.  고유 값을 계산합니다.

    1.  **종류** 열을 선택합니다.

    2.  마우스 오른쪽 단추를 클릭하여 선택합니다.

    3.  선택 **값 개수** 합니다.

        데이터 아래에 **검사기** 창이 열립니다. 네 개의 막대가 있는 히스토그램에 표시 됩니다. 대상 열에는 4 개의 고유 값: **iris_virginica**를 **iris_versicolor**합니다**iris-setosa**, 및 **(null)** 값입니다.

    ![값 개수 선택](media\azure-stack-solution-machine-learning\image34.png)

    ![값 개수 히스토그램](media\azure-stack-solution-machine-learning\image35.png)

1.  null 값을 필터링하려면 "(null)" 상자를 선택한 다음, 빼기 기호(**-**)를 선택합니다.

    그러면 (null) 행이 회색으로 바뀌어 필터링되었음을 나타냅니다.

    ![null 필터링](media\azure-stack-solution-machine-learning\image36.png)

1.  개별 데이터 준비 단계는 **단계** 창에서 자세히 설명합니다. 열 이름이 고 null 값 행 필터링 된 경우 각 작업은 데이터 준비 단계로 기록 됩니다. 해당 설정을 조정 하 고, 단계 순서, 단계를 제거 하는 개별 단계를 편집 합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image37.png)

1.  데이터 준비 편집기를 닫습니다. 그래프 아이콘이 있는 **iris-1** 탭의 **x** 아이콘을 선택하여 탭을 닫습니다. 작업에 자동으로 저장 됩니다는 **iris-1.dprep** 아래에 표시 된 파일을 **데이터 준비** 제목입니다.

    ![닫습니다](media\azure-stack-solution-machine-learning\image38.png)

### <a name="generate-python-code-to-invoke-a-data-preparation-package"></a>데이터 준비 패키지를 호출 하는 Python 코드를 생성 합니다.

데이터 준비 패키지의 출력은 Python 또는 Jupyter Notebook에서 직접 탐색할 수 있습니다. 패키지는 로컬 Python, Spark(Docker에 포함) 및 HDInsight를 포함한 여러 런타임에서 실행될 수 있습니다.

1.  데이터 준비 탭에서 **iris-1.dprep** 파일을 찾습니다.

2.  **iris-1.dprep** 파일을 마우스 오른쪽 단추로 클릭하고 바로 가기 메뉴에서 **데이터 액세스 코드 파일 생성**을 선택합니다.

    ![코드 생성](media\azure-stack-solution-machine-learning\image39.png)

    라는 새 파일 **iris-1.py** 데이터 준비 패키지로 만든 논리를 호출 하는 코드의 다음 줄으로 열립니다.

    ```Python
    # Use the Azure Machine Learning data preparation package
    from azureml.dataprep import package

    # Use the Azure Machine Learning data collector to log various metrics
    from azureml.logging import get_azureml_logger
    logger = get_azureml_logger()

    # This call will load the referenced package and return a DataFrame.
    # If run in a PySpark environment, this call returns a
    # Spark DataFrame. If not, it will return a Pandas DataFrame.
    df = package.run('iris-1.dprep', dataflow_idx=0)
    # Remove this line and add code that uses the DataFrame
    df.head(10)
    ```

    이 코드가 실행 되는 컨텍스트에 따라 drep 다른 종류를의 데이터 프레임을 나타냅니다.

    -  Python 런타임에서 실행되는 경우 [pandas 데이터 프레임](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html)이 사용됩니다.

    -  Spark 컨텍스트에서 실행되면 [Spark 데이터 프레임](https://spark.apache.org/docs/latest/sql-programming-guide.html)이 사용됩니다.

### <a name="review-irissklearnpy-and-the-configuration-files"></a>iris_sklearn.py 및 구성 파일 검토

1.  열려 있는 프로젝트를 선택 합니다 **파일** 프로젝트 폴더의 파일 목록을 엽니다 가장 왼쪽 창에서 단추 (폴더 아이콘).

    ![Azure Machine Learning Workbench에서 프로젝트 열기](media\azure-stack-solution-machine-learning\image40.png)

1.  **iris_sklearn.py** Python 스크립트 파일을 선택합니다.

    ![스크립트 선택](media\azure-stack-solution-machine-learning\image41.png)

    Workbench의 새 텍스트 편집기 탭에 코드가 표시됩니다.

    > [!Note]  
    > 표시 된 코드를 하지 때문일 수 있습니다 정확 하 게 앞의 코드와 동일한이 샘플 프로젝트는 자주 업데이트 합니다.

    ![파일 열기](media\azure-stack-solution-machine-learning\image42.png)

1.  Python 스크립트 코드를 확인하여 코딩 스타일을 파악합니다.

    **iris_sklearn.py** 스크립트에서 수행하는 작업은 다음과 같습니다.  

    -  **iris.dprep**이라는 기본 데이터 준비 패키지를 로드하여 [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html)을 만듭니다.

    -   임의의 기능을 추가하여 문제를 해결하기 어렵게 만듭니다. 아이리스는 거의 100% 정확도로 쉽게 분류할 수 있는 작은 데이터 집합이므로 임의성이 필요합니다.

    -  Thescikit learnmachine 학습 라이브러리를 사용 하 여 로지스틱 회귀 모델을 빌드합니다. 이 라이브러리는 Azure Machine Learning Workbench와 함께 기본 제공됩니다.

    -  [pickle](https://docs.python.org/3/library/pickle.html) 라이브러리를 사용하여 모델을 `outputs` 폴더의 파일에 직렬화합니다.

    -  직렬화된 모델을 로드하고 이 모델을 메모리에 다시 역직렬화합니다.

    -  역직렬화된 모델을 사용하여 새 레코드에 대해 예측을 수행합니다.

    -  두 그래프, 혼동 행렬 및 다중 클래스 수신기 작동 특성 (ROC) 곡선을 사용 하 여 합니다 [matplotlib](https://matplotlib.org/) 라이브러리 theoutputsfolder에 저장 합니다. 구분 되지 경우 환경에서이 라이브러리를 설치 합니다.

    -  자동으로 실행된 기록에 정규화 속도 및 모델 정확도를 그립니다. `run_logger` 개체는 정규화 속도 및 모델 정확도를 처음부터 끝까지 로그에 기록하는 데 사용됩니다.

### <a name="run-irissklearnpy-in-the-local-environment"></a>로컬 환경에서 iris_sklearn.py를 실행 합니다.

1.  Azure Machine Learning CLI(명령줄 인터페이스)를 시작합니다.

    1.  Azure Machine Learning Workbench를 시작합니다.

    2.  Workbench 메뉴에서 **파일**> **명령 프롬프트 열기**를 선택합니다.

    프로젝트 폴더에서 Azure Machine Learning CLI (명령줄 인터페이스) 창이 시작 `C:\Temp\\myIris\` Windows에서. 이 프로젝트는 자습서의 1 부에서 만든와 동일 합니다.

    > [!Important]  
    > 이 CLI 창을 사용 하 여 다음 단계를 수행 합니다.

1.  CLI 창에서 Python 그리기 라이브러리를 설치 **matplotlib**아직 설치 되지 않은 경우.

    **iris_sklearn.py** 스크립트는 두 개의 Python 패키지 **scikit-learn** 및 **matplotlib**에 의존합니다. 합니다 **scikit-알아봅니다** 편의 위해 Azure Machine Learning Workbench가 설치 패키지입니다. 그러나 설치 **matplotlib** 필요할 수 있습니다.

    설치 하지 않고 계속 하는 경우 **matplotlib**,이 자습서의 코드 성공적으로 계속 실행할 수 있습니다. 그러나 코드는 기록 시각화에 표시된 대로 혼동 행렬 출력 및 다중 클래스 ROC 곡선 그래프를 생성할 수 없습니다.

    ```CLI
    pip install matplotlib
    python -m pip install --upgrade pip
    ```

    이 설치는 약 1분이 걸립니다.

1.  Workbench 응용 프로그램으로 돌아갑니다.

2.  **iris_sklearn.py**라는 탭을 찾습니다.

    ![스크립트가 있는 탭 찾기](media\azure-stack-solution-machine-learning\image43.png)

1.  해당 탭의 도구 모음에서 선택 **로컬** 을 실행 환경으로 andiris_sklearn.pyas 스크립트를 실행 합니다. 이 옵션들은 이미 선택되어 있을 수 있습니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image44.png)

1.  도구 모음 및 enter0.01in 왼쪽에서 오른쪽으로 이동 합니다 **인수** 필드입니다.

    이 값은 로지스틱 회귀 모델의 정규화 속도에 해당 합니다.

    ![로컬 및 스크립트 선택](media\azure-stack-solution-machine-learning\image45.png)

1.  **실행** 단추를 선택합니다. 작업이 바로 예약됩니다. 이 작업은 Workbench 창의 오른쪽에 있는 **작업** 창에 나열됩니다.

    ![로컬 및 스크립트 선택](media\azure-stack-solution-machine-learning\image46.png)

    잠시 후 작업의 상태에서 전환 **제출 중**를 **실행**, 마지막에 **완료**합니다.

1.  **작업** 창의 작업 상태 텍스트에서 **완료됨**을 선택합니다.

    ![sklearn 실행](media\azure-stack-solution-machine-learning\image47.png)

    팝업 창이 열리고 실행에 대 한 표준 출력 (stdout) 텍스트가 표시 됩니다. 표준 출력 텍스트를 닫으려면 선택 합니다 **닫습니다** (**x**)의 오른쪽 위에 있는 팝업 창에는 단추입니다.

    ![표준 출력](media\azure-stack-solution-machine-learning\image48.png)

1.  동일한 작업 상태에서를 **작업** 창, 파란색 텍스트 **iris_sklearn.py 선택 \[n\] **(* n *은 실행된 횟수) 바로 위에  **완료** 상태 및 시작 시간입니다. **실행 속성** 창이 열리고 해당되는 특정 실행에 대한 다음 정보가 표시됩니다.

    -  **실행 속성** 정보

    -  **Outputs**

    -  **Metrics**(메트릭)

    -  **시각화**(있는 경우)

    -  **로그**

    실행이 완료되면 팝업 창에서 다음과 같은 결과를 표시합니다.

    > [!Note]  
    > 이 자습서 학습 이전 집합에 불규칙화를 도입 하기 때문에 정확한 결과 여기에 표시 된 결과에서 달라질 수 있습니다.

    ```Python  
        Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  5 2016, 11:41:13) [MSC v.1900 64 bit (AMD64)]

        Iris dataset shape: (150, 5)
        Regularization rate is 0.01
        LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
            intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
            penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
            verbose=0, warm_start=False)
        Accuracy is 0.6792452830188679

        ==========================================
        Serialize and deserialize using the outputs folder.

        Export the model to model.pkl
        Import the model from model.pkl
        New sample: [[3.0, 3.6, 1.3, 0.25]]
        Predicted class is ['Iris-setosa']
        Plotting confusion matrix...
        Confusion matrix in text:
        [[50  0  0]
        [ 1 37 12]
        [ 0  4 46]]
        Confusion matrix plotted.
        Plotting ROC curve....

        ROC curve plotted.
        Confusion matrix and ROC curve plotted. See them in Run History details pane.

    ```

1.  **실행 속성** 탭을 닫은 다음, **iris_sklearn.py** 탭으로 돌아갑니다.

1.  추가 실행에 대해 반복합니다.

**인수** 필드에서 `0.001`에서 `10`까지 일련의 값을 입력합니다. **실행**을 선택하여 코드를 몇 번 더 실행합니다. 변경 될 때마다 인수 값은 될 때마다 다른 결과에서 생성 코드에서 로지스틱 회귀 모델에 공급 됩니다.

### <a name="review-the-run-history-in-detail"></a>실행 기록 자세히 보기

Azure Machine Learning Workbench에서 스크립트를 실행할 때마다 실행된 기록 레코드로 캡처됩니다. 열어 특정 스크립트의 실행된 기록을 확인 합니다 **실행** 보기.

1.  **실행** 목록을 열려면 왼쪽 도구 모음에서 **실행** 단추(시계 아이콘)를 선택합니다. 선택한 **iris_sklearn.py** 표시할 합니다 **실행 대시보드** ofiris_sklearn.py 합니다.

    ![실행 보기](media\azure-stack-solution-machine-learning\image49.png)

1.  **실행 대시보드** 탭이 열립니다.

    여러 번 실행에 걸쳐 캡처된 통계 정보를 검토합니다. 그래프 탭의 위쪽에 렌더링합니다. 각 실행은 연속 번호가 있으며 실행된 세부 정보 화면 맨 아래에 있는 테이블에 나열 됩니다.

    ![실행 대시보드](media\azure-stack-solution-machine-learning\image50.png)

1.  테이블을 필터링한 다음, 원하는 그래프를 선택하면 각 실행의 상태, 기간, 정확도 및 정규화 속도를 볼 수 있습니다.

2.  **실행** 테이블에서 두 개 이상의 실행 옆에 있는 확인 상자를 선택합니다. **비교** 단추를 선택해 자세한 비교 창을 엽니다. 병렬 비교를 검토합니다.

3.  **실행 대시보드**로 돌아가려면 **비교** 창의 왼쪽 위에서 **실행 목록** 뒤로 단추를 선택합니다.

    ![실행 목록에 반환](media\azure-stack-solution-machine-learning\image51.png)

1.  개별 실행을 선택하여 실행 세부 정보 보기를 표시합니다. 선택한 실행에 대한 통계는 **실행 속성** 섹션에 나열됩니다. 출력 폴더에 기록 된 파일에 나열 됩니다는 **출력** 섹션 및 여기에서 파일을 다운로드 합니다.

    ![실행 세부 정보](media\azure-stack-solution-machine-learning\image52.png)

두 개의 도면, 즉 혼돈 행렬 및 다중 클래스 ROC 곡선은 **시각화** 섹션에서 렌더링됩니다. 또한 모든 로그 파일은 **로그** 섹션에서 찾을 수 있습니다.

### <a name="run-scripts-in-the-azure-machine-learning-ml-workbench-cli-window"></a>Azure ML (Machine Learning) Workbench CLI 창에서 스크립트를 실행 합니다.

1.  Azure Machine Learning CLI(명령줄 인터페이스)를 시작합니다.

    1.  Azure Machine Learning Workbench를 시작합니다.

    2.  Workbench 메뉴에서 **파일** > **명령 프롬프트 열기**를 선택합니다.

    프로젝트 폴더에서 CLI 프롬프트가 시작 `C:\\Temp\\myIris` Windows에서. 자습서의 1 부에서 만든 프로젝트입니다.

    > [!Important]  
    > 이 CLI 창을 사용 하 여 다음 단계를 수행 합니다.

1.  CLI 창에서 Azure에 로그인합니다. [az 로그인에 대한 자세한 내용은 여기를 참조합니다](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    이미 로그인 되어 있는 경우이 단계를 건너뜁니다.

1.  명령 프롬프트에 다음을 입력합니다.

    ```CLI
        az login
    ```

    이 명령은 브라우저에서 사용할 코드를 반환 합니다 [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin)합니다.

1.  로 이동 [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin) 브라우저에서 합니다. CLI에서 받은에서 받은 코드를 입력 합니다.

    Workbench 응용 프로그램과 CLI는 Azure 리소스에 대해 인증할 때 독립적인 자격 증명 캐시를 사용 합니다. 인증이 필요 없는 다시 캐시 된 토큰이 만료 될 때까지 합니다.

1.  조직에 여러 Azure 구독 (엔터프라이즈 환경) 하는 경우 사용할 구독을 설정 합니다. 구독을 찾아 구독 ID를 사용 하 여 설정할 이것을 테스트 합니다.

1.  이 명령을 사용 하 여 액세스 모든 Azure 구독을 나열 합니다.

    ```CLI
        az account list -o table 
    ```

    합니다 **az 계정 목록** 명령은 로그인에 사용 가능한 구독 목록을 반환 합니다. 가 둘 이상의 경우에 사용 된 구독에 대 한 구독 ID 값을 식별 합니다.

1.  기본 계정으로 사용 되는 Azure 구독을 설정 합니다.

    ```CLI
        az account set -s <the-subscription-id
    ```

    여기서 < the--i d >는 사용 된 구독에 대 한 ID 값입니다. 대괄호를 포함하지 마십시오.

1.  현재 구독에 대한 세부 정보를 요청하여 새 구독 설정을 확인합니다.

    ```CLI
        az account show
    ```

1.  CLI 창에서 **iris_sklearn.py** 스크립트를 시험 삼아 제출합니다.

    Iris_sklearn.py 실행은 로컬 계산 컨텍스트에 대해 실행 됩니다.

1.  Windows에서:

    ```CLI
        az ml experiment submit -c local .\\iris_sklearn.py
    ```

1.  macOS에서:

    ```CLI
        az ml experiment submit -c local iris_sklearn.py
    ```

    출력에 비슷해야 합니다. 

    ```
        RunId: myIris_1521077190506

    Executing user inputs .....
    ===========================

    Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  2 2016, 17:52:12) 
    [GCC 4.2.1 Compatible Apple LLVM 4.2 (clang-425.0.28)]

    Iris dataset shape: (150, 5)
    Regularization rate is 0.01
    LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
            intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
            penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
            verbose=0, warm_start=False)
    Accuracy is 0.6792452830188679

    ==========================================
    Serialize and deserialize using the outputs folder.

    Export the model to model.pkl
    Import the model from model.pkl
    New sample: [[3.0, 3.6, 1.3, 0.25]]
    Predicted class is ['Iris-setosa']
    Plotting confusion matrix...
    Confusion matrix in text:
    [[50  0  0]
    [ 1 37 12]
    [ 0  4 46]]
    Confusion matrix plotted.
    Plotting ROC curve....
    ROC curve plotted.
    Confusion matrix and ROC curve plotted. See them in Run History details page.

    Execution Details
    =================
    RunId: myIris_1521077190506

    ```

6.  출력을 검토합니다. Workbench는 스크립트를 실행 하는 경우 동일한 출력 및 결과 수 해야 모든.

7.  Workbench로 돌아가서 다음을 수행합니다.

    프로젝트 파일을 나열하려면 왼쪽 창에서 폴더 아이콘을 선택합니다.  **run.py**이라는 Python 스크립트를 엽니다. 이 스크립트는 다양 한 정규화 속도로 루프에 유용 합니다. 

    ![실행 목록에 반환](media\azure-stack-solution-machine-learning\image53.png)

1.  다양한 정규화 속도로 실험을 여러 번 실행합니다.

    이 스크립트는 시작` aniris_sklearn.pyjob` 정규화 속도 o와 `10.0` (터무니 없이 큰 숫자)입니다. 스크립트 비율 보다 작으면 안 될 때까지 다음 실행 및 등과 절반 속도 다음 잘라냅니다 `0.005`합니다. 스크립트에는 다음 코드가 포함되어 있습니다.

    ![실행 목록으로 돌아가기](media\azure-stack-solution-machine-learning\image54.png)

1.  다음과 같은 명령줄에서 **run.py** 스크립트를 실행합니다.

    ```CLI
        python run.py
    ```
이 명령은 제출 `iris_sklearn.py` 여러 번 사용 하 여 다른 정규화 속도로

때 `run.py` 완료 되 면 다른 메트릭 그래프 워크 벤치의 실행된 기록 목록 보기에 표시 됩니다.

### <a name="run-scripts-in-an-ubuntu-based-data-science-virtual-machine-dsvm-on-azure"></a>Azure에서 스크립트에는 Ubuntu 기반 가상 머신 DSVM (데이터 과학)을 실행 합니다.

원격 Linux 컴퓨터에서 Docker 컨테이너에서 스크립트를 실행 하려면 SSH 액세스 (사용자 이름 및 암호)는 원격 컴퓨터를 실행 하려면 필요 합니다. 또한 머신에는 Docker 엔진이 설치되어 실행 중이어야 합니다.

1.  생성 된 편집<your DSVM Name>.runconfigfile underaml_configand 기본 valuePySparktoPython에서 프레임 워크를 변경 합니다.

    ```yaml  
    Framework: Python
    ```
1.  대상을 사용 하 여 CLI 창에서 이전과 동일한 명령을 실행할*<DSVM>* 원격 Docker 컨테이너에서 iris_sklearn.py를 실행 하려면이 이번: (대체는 <DSVM> 이름의 데이터 과학 VM은 대괄호가 없어도).

    ```CLI
        az ml experiment submit -c <DSVM> iris_sklearn.py
    ```

원격 Linux VM에서 실행 발생 점을 제외 하 고 adocker-pythonenvironment에서 작업 하는 경우 명령을 실행 합니다. CLI 창에서 동일한 출력 정보가 표시됩니다.

### <a name="download-the-model-pickle-file"></a>모델 pickle 파일 다운로드

이 자습서의 이전 부분에서는 **iris_sklearn.py** 스크립트가 Machine Learning Workbench에서 로컬로 실행되었습니다. 이 작업은 인기 있는 [pickle](https://docs.python.org/3/library/pickle.html) Python 개체 직렬화 패키지를 사용하여 로지스틱 회귀 모델을 직렬화했습니다.

1.  Machine Learning Workbench 응용 프로그램을 엽니다. 엽니다는 **myIris** 자습서 시리즈의 이전 부분에서 만든 프로젝트입니다.

2.  프로젝트를 연 후 선택 합니다 **파일** 프로젝트 폴더의 파일 목록을 열려면 왼쪽된 창에서 단추 (폴더 아이콘).

3.  **iris_sklearn.py** 파일을 선택합니다. Python 코드가 Workbench의 새 텍스트 편집기 탭에서 열립니다.

4.  **iris_sklearn.py** 파일을 검토하여 pickle 파일이 생성된 위치를 확인합니다. Ctrl+F를 선택하여 **찾기** 대화 상자를 연 다음 Python 코드에서 **pickle**이라는 단어를 찾습니다.

이 코드 조각에서는 pickle 처리된 출력 파일이 생성된 방법을 보여 줍니다. 디스크에서 출력 pickle 파일의 이름은 **model.pkl**입니다.

    ```Python
        print("Export the model to model.pkl")
        f = open('./outputs/model.pkl', 'wb')
        pickle.dump(clf1, f)
        f.close()

    ```

1.  이전 실행의 출력 파일에서 모델 pickle 파일을 찾습니다.

    경우는 **iris_sklearn.py** 스크립트가 실행 되는 모델 파일에 기록 됩니다 합니다 **출력** 이름의 폴더 **model.pkl**합니다. 이 폴더는 로컬 프로젝트 폴더가 아니라 스크립트를 실행 하도록 선택한 실행 환경에 있습니다. 

    1. 선택 파일을 찾으려고 합니다 **실행** 단추 (시계 아이콘) 목록을 열려면 왼쪽된 창에서 **모두 실행**합니다.  

    2. **모든 실행** 탭이 열립니다. 실행 테이블에서 대상이 최근 실행 중 하나를 선택 **로컬** 고 스크립트 이름이 **iris_sklearn.py**합니다.  

    3. **실행 속성** 창이 열립니다. 창의 오른쪽 위 섹션을 확인 합니다 **출력** 섹션입니다. d\ 합니다. Pickle 파일을 다운로드 하려면 확인란을 옆에 선택 합니다 **model.pkl** 파일을 선택한 후 **다운로드**합니다. 프로젝트 폴더의 루트에 파일을 저장 합니다. 이후 단계에서 파일이 필요 합니다.  

    ![pickle 파일 다운로드](media\azure-stack-solution-machine-learning\image55.png)

### <a name="get-scoring-script-and-schema-files"></a>스크립트 및 스키마 파일을 점수 매기기 가져오기

모델 파일과 함께 웹 서비스를 배포 하려면 점수 매기기 스크립트는 필요 합니다. 필요에 따라 웹 서비스 입력된 데이터에 대 한 스키마가 필요 합니다. 점수 매기기 스크립트는 현재 폴더에서 **model.pkl** 파일을 로드하고, 이를 사용하여 새 예측을 생성합니다.

1.  Machine Learning Workbench 응용 프로그램을 엽니다. 엽니다는 **myIris** 자습서 시리즈의 이전 부분에서 만든 프로젝트입니다.

2.  프로젝트를 연 후 선택 합니다 **파일** 프로젝트 폴더의 파일 목록을 열려면 왼쪽된 창에서 단추 (폴더 아이콘).

3.  **score_iris.py** 파일을 선택합니다. Python 스크립트가 열립니다. 이 파일은 점수 매기기 파일로 사용됩니다.

    ![점수 매기기 파일](media\azure-stack-solution-machine-learning\image56.png)

1.  스키마 파일을 가져오려면 스크립트를 실행합니다. 명령 모음에서 **로컬** 환경과 **score_iris.py** 스크립트를 선택한 다음, **실행**을 선택합니다.

    이 스크립트에서 JSON 파일을 생성 합니다 **출력** 모델에 필요한 입력된 데이터 스키마를 캡처하는 섹션입니다.

1.  **프로젝트 대시보드** 창의 오른쪽에서 **작업** 창을 확인합니다. 녹색 표시 하려면 최신 * * score_iris.py** 작업에 대 한 대기 **Completed** 상태입니다. 그런 다음, 최신 작업 실행에 대한 **score_iris.py** 하이퍼링크를 선택하여 실행 세부 정보를 확인합니다.

2.  **실행 속성** 창의 **출력** 섹션에서 새로 만든 **service_schema.json** 파일을 선택합니다. 파일 이름 옆의 확인란을 선택한 다음 **다운로드**를 선택합니다. 프로젝트 루트 폴더에 파일을 저장 합니다.

3.  이전 탭으로 반환 하며 **score_iris.py** 스크립트입니다. 데이터 수집을 사용 하 여 모델 입력 및 예측 웹 서비스에서 캡처할 수 있습니다. 다음 단계에서는 데이터 컬렉션에 대 한 관심 사용 합니다.

4.  클래스를 가져오는 파일의 맨 위에 있는 코드를 검토 **ModelDataCollector**합니다. 모델 데이터 컬렉션 기능을 포함합니다.

    ```Python  
        from azureml.datacollector import ModelDataCollector
    ```

1.  **init()** 함수에서 **ModelDataCollector**를 인스턴스화하는 다음 코드 줄을 검토합니다.

    ```Python  
        global inputs_dc, prediction_dc
        inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
        prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

1.  **run(input_df)** 함수에서 입력 및 예측 데이터를 수집하는 다음 코드 줄을 검토합니다.

    ```Python  
        inputs_dc.collect(input_df)
        prediction_dc.collect(pred)
    ```

이제 모델 운영 화 하도록 환경을 준비 합니다.

## <a name="step-5-deploy-and-use-azure-container-registry"></a>5 단계: 배포 하 고 Azure Container Registry를 사용 합니다.

배포 하 고 Azure Container Registry를 사용 합니다.

**az acr create** 명령으로 Azure Container Registry를 만듭니다. 레지스트리 이름은 Azure 내에서 고유해야 하며, 5-50자의 영숫자만 포함해야 합니다. 리소스 그룹은 동일 합니다.

    ```CLI
        az acr create --resource-group <ResourceGroup> --name  <acrName> --sku Basic
    ```

### <a name="container-registry-login"></a>컨테이너 레지스트리 로그인

**az acr login** 명령을 사용하여 ACR 인스턴스에 로그인합니다. 컨테이너 레지스트리가 생성될 때 지정된 고유한 이름을 입력합니다.

    ```CLI
        az acr login --name <acrName>
    ```

명령에서 반환 된 ' 완료 되 면 로그인 했습니다. 메시지입니다.

### <a name="prepare-to-operationalize-locally-for-development-and-testing-the-service"></a>개발 및 서비스 테스트에 대 한 로컬에서 조작 준비

사용 하 여 *로컬 모드* 개발 및 테스트에 대 한 로컬 컴퓨터에 Docker 컨테이너에서 실행 하도록 배포 합니다.

다음 단계를 수행하여 모델을 조작하려면 Docker 엔진이 로컬로 실행되어야 합니다. 사용 하 여는 `-h` 해당 도움말 메시지를 표시 하는 각 명령 후에는 플래그입니다.

    > [!Note]  
    > If Docker engine is not locally available, proceed by creating a cluster in Azure for deployment and keep the cluster for re-use, or delete it after the tutorial to avoid ongoing charges.

    > [!Note]  
    > Web services deployed locally do not appear in Azure Portal's list of services. They will be running in Docker on the local machine.

1.  CLI(명령줄 인터페이스)를 엽니다. Machine Learning Workbench 응용 프로그램의 **파일** 메뉴에서 **명령 프롬프트 열기**를 선택합니다.

    명령줄 프롬프트가 현재 프로젝트 폴더 위치인에서 엽니다 **c:\\temp\\myIris**합니다.

1.  Azure 리소스 공급자 했는지 **Microsoft.ContainerRegistry** 구독에 등록 됩니다. 3 단계에서 환경을 만들기 전에이 리소스 공급자를 등록 합니다. 다음 명령을 사용 하 여 이미 등록 되어 있는지 확인 합니다.

    ```CLI
        az provider list --query "\[\].{Provider:namespace, Status:registrationState}" --out table
    ```

    이 출력을 보려면:

    ```CLI
        Provider                                    Status 
        --------                                    --------
        Microsoft.Authorization                     Registered 
        Microsoft.ContainerRegistry                 Registered 
        microsoft.insights                          Registered 
        Microsoft.MachineLearningExperimentation    Registered 
    ```

    하는 경우 **Microsoft.ContainerRegistry** 명령이 등록된 되지 않은 사용 됩니다.

    ```CLI
    az provider register --namespace Microsoft.ContainerRegistry
    ```

    등록은 몇 분 정도 걸릴 수 있습니다. 이전을 사용 하 여 등록 상태를 확인할 **az provider 목록** 명령 또는 다음 명령을:

    ```CLI
    az provider show -n Microsoft.ContainerRegistry
    ```

    출력의 세 번째 줄에 **“registrationState”: “Registering”** 이 표시됩니다. 몇 분 정도 대기 하 고 반복 합니다 **표시** 명령 출력에 표시 될 때까지 **"registrationState": "등록 합니다.**

1.  환경을 만듭니다. 환경당 한 번씩이 단계를 실행 합니다.

    다음 설치 명령에서는 구독에 대 한 참가자 액세스. 리소스 그룹에 배포에 대 한 참가자 액세스 필요 합니다. Gflag the를 사용 하 여 설치 명령의 일부로 리소스 그룹 이름을 지정 합니다.

    ```CLI
    az ml env setup -n <new deployment environment name> --location <e.g. eastus2> -g <existing resource group name>
    ```

    화면의 지침에 따라 Docker 이미지 저장을 위한 저장소 계정, Docker 이미지 나열을 위한 Azure 컨테이너 레지스트리, 원격 분석 수집을 위한 Azure Application Insights 계정을 프로비전합니다. **이 명령은 또한 Container Service 클러스터를 만듭니다 cswitch the를 사용 하는 경우**합니다.

    클러스터 이름은 환경을 식별 하는 방법입니다. 위치는 Azure portal에서 만든 모델 관리 계정의 위치와 동일 해야 합니다.

    환경이 성공적으로 설정되었는지 확인하려면 다음 명령을 사용하여 상태를 확인합니다.

    ```CLI
    az ml env show -n <deployment environment name> -g <existing resource group name>
    ```

    가지도록 "프로 비전 상태" 값 "성공"와 같이 5 단계에서 환경을 설정 하기 전에:

    ![프로비전 상태](media\azure-stack-solution-machine-learning\image57.png)

1.  환경을 설정합니다.

    설정이 완료되면 다음 명령을 사용하여 환경 조작에 필요한 환경 변수를 설정합니다. 3 단계에서 이전에 사용한 동일한 환경 이름을 사용 합니다. 설정 프로세스가 완료될 때 명령 창에 출력된 것과 동일한 리소스 그룹 이름을 사용합니다.

    ```CLI
        az ml env set -n <deployment environment name> -g <existing resource group name>
    ```

1.  로컬 웹 서비스 배포를 위한 조작 가능한 환경의 적절 한 구성을 확인 하려면 다음 명령을 입력 합니다.

    ```CLI
    az ml env show
    ```

    이제 실시간 웹 서비스를 만듭니다.

    > [!Note]  
    > 모델 관리 계정 및 후속 웹 서비스 배포를 위한 환경을 다시 사용 합니다. 각 웹 서비스에 대해 만들려는 않아도가 됩니다. 계정 또는 환경은 연결된 여러 웹 서비스를 가질 수 있습니다.

### <a name="create-a-real-time-web-service-by-using-separate-commands"></a>별도의 명령을 사용하여 실시간 웹 서비스 만들기

대 안으로 합니다 **az ml service 만들기 실시간** 명령 이전에 표시 된이 단계 별도로 수행할 수도 있습니다.

먼저 모델을 등록합니다. 그런 다음 매니페스트를 생성하고 Docker 이미지를 빌드하며 웹 서비스를 만듭니다. 이 단계별 접근 방식을 각 단계 마다 더 많은 유연성을 제공 합니다. 또한 이전 단계에서 생성 한 엔터티를 다시 사용할 수 있습니다.

1. pickle 파일 이름을 제공하여 모델을 등록합니다.

    ```CLI
    az ml model register --model model.pkl --name model.pkl
    ```

    이 명령은 모델 ID를 생성합니다.

2.  매니페스트를 만듭니다.

    매니페스트를 만들려면 다음 명령을 사용 하 고 이전 단계의 모델 ID 출력을 제공 합니다. 
    
    ```CLI
    az ml manifest create --manifest-name <new manifest name> -f score_iris.py -r python -i <model ID> -s service_schema.json
    ```

    이 명령은 매니페스트 ID를 생성합니다.

3.  Docker 이미지를 만듭니다.

    Docker 이미지를 만들려면 다음 명령을 사용 하 고 이전 단계의 매니페스트 ID 값 출력을 제공 합니다. Conda 종속성을 통해 사용할 수도 있습니다는 `-c` 전환 합니다. 
    
    ```CLI
    az ml image create -n irisimage --manifest-id <manifest ID> -c aml_config\conda_dependencies.yml
    ```
    
    이 명령은 Docker 이미지 ID를 생성합니다.

2.  서비스를 만듭니다.

    서비스를 만들려면 다음 명령을 사용 하 고 이전 단계의 이미지 ID 출력을 제공 합니다. 
    
    ```CLI
    az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
    ```
    
    이 명령은 웹 서비스 ID를 생성합니다.
    
    다음으로, 웹 서비스를 실행 합니다.

## <a name="step-6-deploy-a-kubernetes-cluster-to-azure-stack"></a>6 단계: Azure Stack에 Kubernetes 클러스터 배포

Azure Stack에 Kubernetes 클러스터를 배포 합니다.

Azure Stack에서 Azure 컨테이너 서비스 (ACS) 엔진에서 생성 하는 Azure Resource Manager 템플릿을 사용 하 여 Kubernetes는 설치할 수 있습니다. [*Kubernetes* ](https://kubernetes.io/) 는 배포를 자동화 하기 위한 오픈 소스 시스템 크기 조정 및 컨테이너의 응용 프로그램 관리. A [ *컨테이너* ](https://www.docker.com/what-container) VM에는 이미지에 포함 되어 있습니다. 컨테이너 이미지를 VM과 달리 코드, 특정 라이브러리 및 설정을 실행 하기 위해 런타임 코드와 같은 응용 프로그램을 실행 하는 데 필요한 리소스를 포함 합니다.

Kubernetes를 사용 합니다.

 -  초 후에 배포할 수 있는 확장성이 매우 뛰어난, 업그레이드, 응용 프로그램을 개발 합니다.

 -  응용 프로그램의 설계를 간소화 하 고 다른 Helm 응용 프로그램에서 안정성을 개선 합니다. [*Helm* ](https://github.com/kubernetes/helm) 은 설치 및 Kubernetes 응용 프로그램의 수명 주기를 관리 하는 데 도움이 되는 오픈 소스 패키징 도구입니다.

 -  모니터링 및 확장을 사용 하 여 응용 프로그램의 상태를 진단 하 고 업그레이드 기능을 쉽게 합니다.

> [!Note]  
> 설치 클러스터의에 걸리는 시간에 대 한 하세요 계획 적절 하 게 합니다.

### <a name="prerequisites-for-kubernetes-cluster-deployment"></a>Kubernetes 클러스터 배포에 대 한 필수 구성 요소

시작 하려면 사용 권한 및 Azure Stack 준비 상태를 확인 합니다.

1.  있는지 확인 합니다 **Kubernetes 클러스터 만들기 (미리 보기)** 항목은 Azure Stack Marketplace에서 사용할 수 있습니다. 이 항목을 사용할 수 없는 경우 Azure Stack 환경에이 항목을 추가 하는 Azure Stack 운영자를 사용 하 여 작동 합니다.

2.  Azure Active Directory (Azure AD) 테 넌 트에서 응용 프로그램을 만들 수 있는지 확인 합니다. 권한은 Kubernetes 배포에 필요 합니다.

    권한을 검사에 대 한 지침을 참조 하세요 [ *Azure Active Directory 확인 사용 권한*](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)합니다.

3.  SSH 공용 및 개인 키 쌍을 Azure Stack에서 Linux VM에 로그인을 생성 합니다. 클러스터를 만들 때 공개 키 필요 합니다. 명령 참조 [SSH에 대 한 인증 키를 생성](#generate-an-authenticatio-key-for-ssh)합니다.

4.  확인 하는 Azure Stack 테 넌 트 포털에서 구독이 유효 하 고 충분 한 공용 IP는 새 응용 프로그램을 추가할 수 있는 해결 합니다.

    Azure Stack에 클러스터를 배포할 수 없습니다 **관리자** 구독 합니다. 사용 된 **사용자** 구독 합니다.

###  <a name="generate-an-authentication-key-for-ssh"></a>SSH에 대 한 인증 키를 생성 합니다.

Linux 세션에 대 한 Windows 하위 시스템 내에서 명령을 사용 하 여 다음 인증 키를 생성 합니다. 

1. 형식:

    ```Bash  
    ssh-keygen -t rsa
    ```
    
2. 선택 `id_rsa` 메시지가 표시 되 면 합니다. 
3. 메시지가 표시 되 면 암호를 만듭니다. 나중에 사용할이 암호를 확인 하는 것이 반드시 합니다. 
    출력은 아래와 유사한 표시 됩니다. 
    
    ```Bash  
    Your identification has been saved in `id_rsa`.
    Your public key has been saved in `id_rsa.pub`. 
    The key fingerprint is: SHA256:lUtUUjzaqWqGeolEPKeBmsnrhcNGM9Dn2OxYatt05SE  <user>@<machine-name>
    The key's randomart image is:  
    ```
    ![대체 텍스트](media\azure-stack-solution-machine-learning\image58.png)

4. 키를 생성 한 후 다음 명령을 사용 하 여 키 정보를 붙여 넣습니다. 
    ```Bash
    cat id_rsa.pub
    ```
    출력은 아래와 유사한 표시 됩니다. 
    ```Bash
    ssh-rsa  AAAAB3NzaC1yc2EAAAADAQABAAABAQDHaWrAktR3BlQ356T8Qvv8O2Q/U/hsXQwS9xJbuduuc9lkJwddzgmNCyM9PooZWYtGVXyHU6SC3YH1XkwqGtKhtPb03d24hmhX1euAaqIqHHSp4WgUS5s1gNsp37L8QCSGNCnF31FWavI8bnjOjccUkMowKl8iyGN++5UyQgNuynEVUbFJjrntoDL66HUu88xDxTcVB7rqDr2QKFwYJkg4HSoHYpezJd7W8kcmv33eh0xs8nlDA7Dzu7zXpyVc54bH9XtPR6EUXaQa+UqKaNlQNiJqEs+1X/zNuK9V6NLVNiO0h3jCHI3K8o4VnZyRKHCQProasiiPLUUJ6SF/RTLN  <user>@<machine-name>
    ```
    
5. SSH 공용 키 필드에 생성 된 키를 복사 합니다.

### <a name="create-a-service-principal-in-azure-ad"></a>Azure AD에서 서비스 주체 만들기

1.  전역에 로그인 [ *Azure portal*](http://portal.azure.com/)합니다.

2.  Azure Stack 인스턴스와 연결 된 Azure AD 테 넌 트를 사용 하 여 로그인 합니다.

3.  Azure AD 응용 프로그램을 만듭니다.

    1. 선택 **Azure Active Directory** > **+ 앱 등록**> **새 응용 프로그램 등록**합니다.
    2. 입력을 **이름** 응용 프로그램입니다. 
    3. 선택 **웹 앱 / API**합니다. 
    4. 입력 `http://localhost` 에 대 한 합니다 **로그온 URL**합니다. 
    5. **만들기**를 선택합니다.

1.  **응용 프로그램 ID**를 적어 둡니다. 클러스터를 만들 때이 ID가 필요 합니다. 참조 하 고 **서비스 주체 클라이언트 ID**합니다.

2.  선택 **설정을** > **키**합니다.

    1. 입력 된 **설명을**합니다. 
    2. 선택 **무기한** 에 대 한 **Expires**합니다. 
    3. **저장**을 선택합니다. 키 문자열을 기록 하십시오. 클러스터를 만들 때 키 문자열이 필요한으로 참조 되는 **서비스 주체 클라이언트 비밀**합니다.

### <a name="give-the-service-principal-access"></a>서비스 주체 액세스를 제공 합니다.

리소스를 만들 수 있도록 구독에 대 한 서비스 주체 액세스를 제공 합니다.

1.  에 로그인 합니다 [관리 포털](https://adminportal.local.azurestack.external/)합니다.

2.  선택 **더 많은 서비스** > * * 사용자 구독 * * > **+ 추가**합니다.

3.  만들어진 구독을 선택 합니다.

4.  선택 **액세스 제어 (IAM)** > 선택 **+ 추가**합니다.

5.  선택 된 **소유자** 역할입니다.

6.  주 서비스에 대해 만든 응용 프로그램 이름을 선택 합니다. 필요한 경우 검색 상자에 이름을 입력 합니다.

7.  **저장**을 선택합니다.

8.  엽니다는 [Azure Stack 포털](https://portal.local.azurestack.external)합니다.

9.  선택 **+ 새로 만들기** > **계산** > **Kubernetes 클러스터**합니다. **만들기**를 선택합니다.

    ![솔루션 템플릿 배포](media\azure-stack-solution-machine-learning\image59.png)

10\ 합니다. 선택 **기본 사항** 에 Kubernetes 클러스터를 만듭니다.

    ![Deploy Solution Template](media\azure-stack-solution-machine-learning\image60.png)

11. 입력 된 **Linux VM Admin Username**합니다. Kubernetes 클러스터의 일부인 Linux Virtual Machines 및 dvm이 대 한 사용자 이름입니다.

12. 입력 된 **SSH 공개 키** dvm이 고 Kubernetes 클러스터의 일부로 생성 하는 모든 Linux 컴퓨터에 대 한 인증에 사용 합니다.

13. 입력 된 **마스터 프로필 DNS 접두사** 지역에 고유한 합니다. 지역 고유 해야이 같은 이름을 `ask8s-12345`입니다. 선택 하려고 리소스 그룹과 동일한 이름을 모범 사례로 좋습니다.

    > [!Note]  
    > 각 클러스터에 대 한 마스터 프로필 새롭고 고유한 DNS 접두사를 사용 합니다.

14. 입력 된 **에이전트 풀 프로필 수**입니다. 수는 클러스터의 에이전트 수를 포함합니다. 있을 수 있습니다 1에서 4.

15. 입력 된 **서비스 주체 ClientId** Kubernetes Azure 클라우드 공급자가 사용 됩니다.

16. 입력 된 **서비스 주체 클라이언트 비밀** 서비스 주체 응용 프로그램을 만들 때 만들어집니다.

17. 입력 된 **Kubernetes Azure Cloud Provider 버전이**합니다. Kubernetes Azure 공급자에 대 한 버전입니다. Azure Stack에는 각 Azure Stack 버전에 대 한 사용자 지정 Kubernetes 빌드를 해제합니다.

18. 선택 된 **구독** id입니다.

19. 새 리소스 그룹의 이름을 입력 하거나 기존 리소스 그룹을 선택 합니다. 리소스 이름은 영숫자 및 소문자 있어야 합니다.

20. 선택 합니다 **위치** 리소스 그룹입니다. Azure Stack 설치에 대해 선택한 지역입니다.

### <a name="specify-the-azure-stack-settings"></a>Azure Stack 설정 지정

1.  선택 된 **Azure Stack 스탬프 설정**합니다.

    ![솔루션 템플릿 배포](media\azure-stack-solution-machine-learning\image61.png)

2.  입력 된 **Azure Resource Manager 끝점을 테 넌 트**합니다. Kubernetes 클러스터에 대 한 리소스 그룹을 만들려면 연결 하는 Azure Resource Manager 끝점입니다. Azure Stack 연산자를 사용 하 여 끝점에 통합된 시스템에 대 한 필요 합니다. 에 Azure Stack 개발 키트 ASDK ()를 사용 하 여 `https://management.local.azurestack.external`입니다.

3.  입력 된 **테 넌 트 ID** 테 넌 트에 대 한 합니다. 참조 [ *테 넌 트 ID 가져오기* ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) 테 넌 트 ID 값을 찾을 수 있습니다.

### <a name="install-kubectl-on-windows-powershell-environment"></a>Windows PowerShell 환경에서 kubectl 설치

WSL 환경 내에서 WSL 환경에 kubectl을 설치 하려면 다음 명령을 실행 합니다.

```PowerShell  
Install-script -name install-kubectl -scope CurrentUser -force
Install-kubectl.ps1 -downloadlocation “C:\Users\<Current User>\Documents\Kube
```

### <a name="install-kubectl-on-the-windows-subsystem-for-linux-environment"></a>Linux 환경에 대 한 Windows 하위 시스템에 kubectl 설치

WSL 환경 내에서 WSL 환경에 kubectl을 설치 하려면 다음 명령을 실행 합니다.

```Bash  
    apt-get update && apt-get install -y apt-transport-https
    curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
    cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
    deb http://apt.kubernetes.io/ kubernetes-xenial main
    EOF
    apt-get update
    apt-get install -y kubectl
```

### <a name="configure-kubectl"></a>kubectl 구성

Kubectl 찾고 액세스 하는 Kubernetes 클러스터에 대 한 순서에는*kubeconfig 파일* 필요 합니다. 이 자동으로 만들어집니다 Minikube 클러스터를 배포 또는 kube-up.sh를 사용 하 여 클러스터를 만들 때. 참조를 [ *시작 가이드* ](https://kubernetes.io/docs/setup/) 클러스터 만들기에 대 한 자세한 내용은 합니다. 다른 사용자가 만든 클러스터에 대 한 액세스에 대 한 참조를 [ *클러스터 액세스 공유 문서*](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)합니다. 기본적으로 kubectl 구성에 위치한 **~.kube/config**합니다.

### <a name="check-the-kubectl-configuration"></a>Kubectl 구성 확인

해당 kubectl 클러스터 상태를 가져와서 제대로 구성 되었는지 확인 합니다.

```Bash  
kubectl cluster-info
```

kubectl은 url 응답 표시 되 면 클러스터에 액세스 하려면 올바르게 구성 됩니다.

kubectl 없거나 올바르게 구성 된 다음 메시지가 표시 되 면 Kubernetes 클러스터에 연결할 수 없습니다.

```Bash  
The connection to the server <server-name:port> was refused -  did you specify the right host or port?
```

예를 들어에 로컬 랩톱에서 Kubernetes 클러스터를 실행 하는 경우 도구 필요할 수 있습니다 (minikube 또는 유사한) 위에 명시 된 명령을 다시 실행 합니다.

Kubectl 클러스터 정보를 반환 하는 경우 url 응답 하지만 클러스터가 여전히 확인 되지 않습니다에 액세스할 수 적절 한 구성을 사용 하 여:

```Bash  
> kubectl cluster-info dump
```

### <a name="enable-shell-autocompletion"></a>셸 자동 완성을 사용 하도록 설정

kubectl 자동 완성 지원을, 쉽고 빠르게 셸을 사용 하는 포함 되어 있습니다.

완료 스크립트 자체 kubectl에서 생성 되 고 프로필에서 액세스할 수 있습니다.

### <a name="connect-to-the-kubernetes-cluster"></a>Kubernetes 클러스터에 연결

Kubernetes 명령줄 클라이언트인 클러스터에 연결 (**kubectl**)가 필요 합니다. 연결 하 고 클러스터 관리에 대 한 지시 사항은 [Azure Container Service 설명서.](https://docs.microsoft.com/azure/container-service/dcos-swarm/)

Kubernetes 클러스터를 연결 하는 것에 대 한 모든 SSH 클라이언트를 사용할 수 있습니다. Windows 하위 시스템에 대 한 WSL (Linux)을 사용 하는 것이 좋습니다. Kubernetes 클러스터에 연결 하는 컴퓨터 클러스터에 대해 만든 리소스 그룹에 이며 vmd edge-ml 스택의 접두사를 사용 하 여 시작 됩니다.

```Bash  
ssh <user>@vmd-edge-ml-stack.<FQDN of Kubernetes Machine in  Azure Stack>
```

Kubernetes 컴퓨터에 연결 되 면 Kubernetes 구성 파일을 가져올 다음 컴퓨터를 실행 합니다.

```Bash  
sudo find / -name \*kubeconfig\* -type f
```

출력은이 같습니다.

```Bash  
/var/lib/waagent/custom-script/download/0/acs-engine/_output/edgemlstack/kubeconfig/kubeconfig.<regionname>.json
```

이 파일 경로 정보를 복사 하 고 위에서 복사한 kubeconfig 파일 경로 붙여 다음 명령을 실행 합니다.

```Bash  
sudo cat  /var/lib/waagent/custom-script/download/0/acs-engine/_output/edgemlstack/kubeconfig/kubeconfig.<regionname>.json
```

출력에는 원시 JSON 콘텐츠는 텍스트의 큰 블록이 됩니다. 이 출력 텍스트 복사한 다음이 코드를 Visual Studio 파일에 JSON 파일로 저장 합니다. 이 인해 로컬에 저장 된 kubeconfig.json 파일입니다. (저장/mntc/사용자/<current user>kubeconfig.json으로 / 문서/Kube 디렉터리)

### <a name="configure-the-kubernetes-cluster"></a>Kubernetes 클러스터를 구성 합니다.

로컬 JSON 파일을 가져오면 새 WSL 세션에서 다음 명령을 사용 하 여 클러스터를 구성 합니다.

```Bash  
    cd /mnt/c/users/<current user>/documents/Kube
    kubectl proxy
    kubectl create -f https://raw.githubusercontent.com/kubernetes/dashboard/master/src/deploy/recommended/kubernetes-dashboard.yaml
    kubectl proxy
    set KUBECONFIG=”/mnt/c/users/<current user>/documents/Kube/kubeconfig.json”
    kubectl.exe config view
```

(아래 출력 참조) Kubernetes 구성 설정은 정의 합니다.

![대체 텍스트](media\azure-stack-solution-machine-learning\image62.png)

로컬 프록시 서비스를 시작 합니다.

```Bash  
kubectl proxy
```

다음 주소에서 kubernetes 클러스터 UI로 이동: `https://localhost:8001`합니다.

![대체 텍스트](media\azure-stack-solution-machine-learning\image63.png)

이제 컨테이너 및 온-프레미스에서 볼 수 있는 클라우드에 상주 하는 컨테이너를 배포 하는 위치를 해야 합니다.

![대체 텍스트](media\azure-stack-solution-machine-learning\image64.png)

사용자 지정 된 **iris_deployment.yaml** 파일 (에 /*mnt/c/사용자/<current user>/문서/Kube 디렉터리*) 하므로 **webservicename** 및 컨테이너  **이미지** 하 고 **이름을** 원하는 어떤 코드 편집기를 사용 하 여 배포와 일치 합니다.

![대체 텍스트](media\azure-stack-solution-machine-learning\image65.png)

컨테이너 포트를 설정 **5001입니다.**

![대체 텍스트](media\azure-stack-solution-machine-learning\image66.png)

만든 다음 합니다 **imagePullSecret**:

### <a name="create-a-secret-in-the-cluster-that-holds-the-authorization-token"></a>권한 부여 토큰을 보유 하는 클러스터의 비밀 만들기

Kubernetes 클러스터의 암호를 사용 하 여 **docker 레지스트리** 개인 이미지를 끌어올 컨테이너 레지스트리로 인증 하는 형식입니다.

이름을 지정 하는이 암호를 만듭니다 **azuremlcr**:

```Bash  
kubectl create secret docker-registry azuremlcr --docker-server=<your-registry-server> --docker-username=<your-name> --docker-password= "<your-pword>" --docker-email=<your-email>
```

찾기:

- **<-레지스트리-서버 >** 는 Azure Container Registry **Login Server**합니다.
- **< 사용자 이름 >** 는 Azure Container Registry **Username**합니다.
- **< 사용자 pword >** 는 Azure Container Registry **암호**합니다. 암호는 따옴표를 확인 하세요.
- **<-전자 메일 >** 가 컨테이너에 대 한 읽기/쓰기 권한이 있는 사용자입니다.
- 이 정보를 확인할 합니다 **Azure Container** **레지스트리** 아래의 **액세스 키**합니다.
- 암호를 호출할 때 클러스터에서 이제 docker 자격 증명이 설정 **azuremlcr**합니다.

저장 된 **iris_deployment.yaml** 파일 (에 /*mnt/c/사용자/<current user>/문서/Kube 디렉터리*).

### <a name="create-the-kubernetes-container"></a>Kubernetes 컨테이너 만들기

```Bash  
kubectl.exe create -f /mnt/c/users/<current  user>/documents/Kube/iris_deployment.yaml
```

    ![Alt text](media\azure-stack-solution-machine-learning\image67.png)

배포의 상태를 확인 합니다.

```Bash  
Kubectl get deployments
```

    ![Alt text](media\azure-stack-solution-machine-learning\image68.png)

배포에는 약간의 시간이 걸릴 수 있습니다.

### <a name="configure-visual-studio-team-services-to-deploy-automatically"></a>자동으로 배포 하려면 Visual Studio Team Services를 구성 합니다.

#### <a name="create-a-team-project"></a>팀 프로젝트 만들기

1.  확인 [Project Collection Administrators 그룹 멤버 자격.](https://docs.microsoft.com/vsts/organizations/security/set-project-collection-level-permissions?view=vsts) 팀 프로젝트를 만들 **새 프로젝트를 만들** 권한으로 설정 되어 있어야 **허용**합니다.

2.  프로젝트 페이지에서 선택 **새 프로젝트**합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image69.png)

1.  프로젝트 이름을 **HybridMLIris**합니다.

2.  초기 원본 제어 유형으로 **Git**

3.  선택한 프로세스 선택 **만들기**합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image70.png)

### <a name="import-some-code--create-repository"></a>가져오기 코드 리포지토리 만들기

YAML 코드에 대 한 Git 리포지토리는 필요 합니다.

#### <a name="add-user-to-the-git-repo"></a>GIT 리포지토리로 사용자 추가

1.  기본 프로젝트 대시보드에서 생성 Git 자격 증명을 선택 합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image71.png)

1.  필수 및 Git 자격 증명을 저장 하는 경우 암호를 입력 합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image72.png)

1.  선택 하 여 리포지토리를 초기화 합니다 **초기화** 단추를 만들기는 **추가 정보** 파일입니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image73.png)

#### <a name="clone-the-git-repository-locally-and-upload-the-code"></a>Git 리포지토리를 로컬로 복제 하 고 코드를 업로드 합니다. 

1.  컴퓨터의 디렉터리에 있도록 `c:\\users\\<User>\\source\\repos\\hybridMLIris`, 리포지토리를 복제 하 고

    ```Bash  
    sudo mkdir /mnt/c/users/<User>/source sudo mkdir /mnt/c/users/<User>/source/repos sudo mkdir /mnt/c/users/<User>/source/repos/hybridMLIris cd /mnt/c/users/<User>/source/repos/hybridMLIris sudo git clone  https://<yourvstssite>.visualstudio.com/HybridMLIris/_git/HybridMLIris
    ```

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image74.png)

1.  새로 복제 된 리포지토리로 이동 합니다.

    ```Bash  
    ls
    cd ./HybridMLIris
    ```
    
    ![대체 텍스트](media\azure-stack-solution-machine-learning\image75.png)

1.  복사 합니다 **iris_deployment.yaml** 리포지토리로 파일입니다.

    ```Bash  
    cp /mnt/c/users/<User>/documents/Kube/iris_deployment.yaml  /mnt/c/users/<User>/source/repos/HybridMLIris/HybridMLIris/iris_deployment.yaml
    ``` 

1.  GIT에서 변경 내용 커밋

    ```Bash  
    git add . git commit -m Added Deployment YAML git push
    ```

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image76.png)

### <a name="prepare-the-private-build-and-release-agent-for-vsts-integration"></a>개인 빌드 및 릴리스 에이전트를 VSTS 통합을 위한 준비

#### <a name="prerequisites"></a>필수 조건

VSTS에 대 한 Azure Resource Manager는 서비스 주체를 사용 하 여 인증 합니다. Azure Stack 구독에 리소스를 프로 비전 할 수는 VSTS 필요한 참가자 상태입니다. \ **다음은 이러한 인증을 사용 하도록 구성 해야 할 고급 단계:**

1.  서비스 주체를 만들지 또는 기존 항목을 사용할 수 있습니다.

2.  인증 키를 서비스 사용자가 작성 해야 합니다.

3.  Azure Stack 구독 참가자 역할의 일부가 될 SPN을 허용 하도록 역할 기반 Access Control을 통해 유효성을 검사 해야 합니다.

4.  SPN 정보 뿐만 아니라 Azure Stack 끝점을 사용 하 여 VSTS에서 새 서비스 정의 만들어야 합니다.

#### <a name="service-principal-creation"></a>서비스 주체 만들기

참조를 [서비스 주체 만들기 지침](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) 서비스 주체를 만들고 응용 프로그램 형식에 대 한 웹 앱/a p I를 선택 합니다.

**액세스 키 만들기**

서비스 주체 인증에 대 한 키가 필요 하 고 키를 생성 하려면이 섹션의 단계를 수행 합니다.

1.  **앱 등록** Azure Active Directory에서 응용 프로그램을 선택 합니다.

    ![응용 프로그램 선택](media\azure-stack-solution-machine-learning\image77.png)

1.  값을 기록해 **응용 프로그램 id입니다. 값에는 VSTS에서 서비스 끝점을 구성할 때 사용 됩니다.**

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image78.png)

1.  인증 키를 생성하려면 **설정**을 선택합니다.

    ![설정 선택](media\azure-stack-solution-machine-learning\image79.png)

1.  **키**를 선택합니다.

    ![키 선택](media\azure-stack-solution-machine-learning\image80.png)

1.  키에 대한 설명 및 키의 기간을 제공합니다. 완료되면 **저장**을 선택합니다.

    ![키 저장](media\azure-stack-solution-machine-learning\image81.png)

키를 저장하면 키 값이 표시됩니다. 나중에 필요할 때이 값을 복사 합니다. 합니다 **키 값** 응용 프로그램 ID는 응용 프로그램으로 로그인 해야 합니다. 응용 프로그램 검색할 수 있는 키 값을 저장 합니다.

![대체 텍스트](media\azure-stack-solution-machine-learning\image82.png)

#### <a name="get-tenant-id"></a>테 넌 트 ID 가져오기

VSTS 서비스 끝점 구성의 일부로 필요 합니다 **테 넌 트 ID** Azure Stack 스탬프에 배포 된 AAD 디렉터리에 해당 하는 합니다. 테 넌 트 id를 수집 하려면이 섹션의 단계에 따라

1.  **Azure Active Directory**를 선택합니다.

    ![Azure Active Directory 선택](media\azure-stack-solution-machine-learning\image83.png)

1.  선택 된 테 넌 트 ID를 가져오려면 **속성** Azure AD 테 넌 트에 대 한 합니다.

    ![Azure AD 속성 선택](media\azure-stack-solution-machine-learning\image84.png)

1.  **디렉터리 ID**를 복사합니다. 이 값은 테 넌 트 id입니다.

    ![테넌트 ID](media\azure-stack-solution-machine-learning\image85.png)

Azure Stack 구독에 리소스를 배포 하려면 서비스 주체 권한 부여

구독에서 리소스에 액세스 하려면 응용 프로그램 역할을 할당 합니다. 응용 프로그램에 적합한 사용 권한을 나타내는 역할을 결정합니다. 사용 가능한 역할에 대해 알아보려면 [RBAC: 기본 제공 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)을 참조하세요.

구독, 리소스 그룹 또는 리소스 수준에서 범위를 설정 합니다. 권한은 하위 수준의 범위로 상속됩니다. 예를 들어, 리소스 그룹에 대 한 읽기 권한자 역할에 응용 프로그램을 추가할 수 있도록 리소스 그룹 및 포함 된 모든 리소스 읽기.

1.  응용 프로그램을 할당할 범위 원하는 수준으로 이동 합니다. 예를 들어 구독 범위에서 역할을 할당하려면 **구독**을 선택합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image86.jpeg)

1.  선택 합니다 **구독** (리소스 그룹 또는 리소스) 응용 프로그램을 할당 합니다.

    ![할당을 위한 구독 선택](media\azure-stack-solution-machine-learning\image87.png)

1.  **Access Control(IAM)** 을 선택합니다.

    ![액세스 선택](media\azure-stack-solution-machine-learning\image88.png)

1.  **추가**를 선택합니다.

    ![추가 선택](media\azure-stack-solution-machine-learning\image89.png)

1.  응용 프로그램을 할당할 역할을 선택 합니다. 다음 이미지는 **소유자** 역할입니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image90.png)

1.  기본적으로 Azure Active Directory 응용 프로그램이 사용 가능한 옵션에 표시되지 않습니다. 응용 프로그램을 찾으려면 **이름을** 검색에 필드를 선택 합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image91.png)

1.  **저장**을 선택하여 역할 할당을 완료합니다. 응용 프로그램은 해당 범위에 대 한 역할에 할당 된 사용자 목록에 표시 됩니다.

### <a name="role-based-access-control"></a>역할 기반 Access Control

Azure 역할 기반 Access Control (RBAC)에 Azure 및 Azure Stack에 대 한 세분화 된 액세스 관리할을 수 있습니다. RBAC를 사용 하 여 액세스 사용자가 해당 작업을 수행 해야 하는 만큼만 부여할 수 있습니다. 역할 기반 Access Control에 대 한 자세한 내용은 참조 하세요 [Azure 구독 리소스에 대 한 액세스 관리](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json)합니다.

**VSTS 에이전트 풀**

에이전트는 구조로 각 에이전트를 개별적으로 관리 하는 대신 **에이전트 풀**합니다. 해당 풀의 모든 에이전트에 대 한 공유 경계를 정의 하는 에이전트 풀입니다. VSTS에서 에이전트 풀을 VSTS 계정에 팀 프로젝트에서 공유할 수 있도록 범위가 지정 됩니다. 자세한 내용 및 VSTS를 만드는 방법에 대 한 자습서에 대 한 에이전트 풀 참조 [에이전트 풀 만들기 및 큐.](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts)

**Azure Stack 용 aPersonal 액세스 토큰 (PAT) 추가**

 -  선택한 VSTS 계정에 로그온 **계정 프로필** 이름입니다.

 -  선택 **보안 관리** 액세스 토큰 생성 페이지에 있습니다.

![대체 텍스트](media\azure-stack-solution-machine-learning\image92.png)

![대체 텍스트](media\azure-stack-solution-machine-learning\image93.jpeg)

![대체 텍스트](media\azure-stack-solution-machine-learning\image94.jpeg)

> [!Note]  
> 토큰 정보를 가져옵니다. 이 화면을 종료 한 후에 다시 표시 되지 않습니다.

1.  복사 합니다 **토큰**합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image95.png)

#### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>Azure Stack에는 VSTS 빌드 에이전트 설치 호스팅된 빌드 서버

1.  에 연결 합니다 **빌드 서버** Azure Stack 호스트에 배포 합니다.

    > [!Note]  
    > Azure Stack 호스트 된 빌드 서버는 공용 인터넷에서 액세스할 수를 확인 하세요. 아니면 Azure Stack 연산자를 사용 하 여 액세스를 사용 하 여 작동 합니다.

    ```Bash  
    ssh <user>@<buildserver.publicip>
    ```

2.  최신 버전 (18.04) Ubuntu 빌드 서버를 업그레이드 합니다.

    ```Bash  
    sudo su
    apt-get update
    apt-get upgrade
    apt-get dist-upgrade
    apt-get autoremove
    do-release-upgrade -d
    ```

    > [!Note]  
    > 이 작업에 다소 시간이 걸립니다.

2.  빌드 서버에 대 한 필수 조건 응용 프로그램을 설치 합니다. Bash에서 Ubuntu의 셸에서 다음 명령을 빌드 서버 실행을 따라:

    ```Bash  
    wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.asc.gpg
    sudo mv microsoft.asc.gpg /etc/apt/trusted.gpg.d/
    wget -q https://packages.microsoft.com/config/ubuntu/18.04/prod.list 
    sudo mv prod.list /etc/apt/sources.list.d/microsoft-prod.list
    sudo chown root:root /etc/apt/trusted.gpg.d/microsoft.asc.gpg
    sudo chown root:root /etc/apt/sources.list.d/microsoft-prod.list
    sudo apt-get install apt-transport-https
    sudo apt-get update
    sudo apt-get install liblttng-ust0 libcurl3 libssl1.0.0 curl lsb-release libkrb5-3 zlib1g libicu60 aspnetcore-runtime-2.1 dotnet-sdk-2.1
    wget https://github.com/PowerShell/PowerShell/releases/download/v6.1.0-preview.3/powershell-preview_6.1.0-preview.3-1.ubuntu.18.04_amd64.deb
    sudo dpkg -i powershell-preview_6.1.0-preview.3-1.ubuntu.18.04_amd64.deb
    sudo apt-get install -f
    AZ_REPO=$(lsb_release -cs)
    echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ $AZ_REPO main" | \
        sudo tee /etc/apt/sources.list.d/azure-cli.list
    curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add –
    sudo apt-get update && sudo apt-get install azure-cli
    ```

2.  다운로드 및 빌드 에이전트를 사용 하 여 서비스 배포를 **개인용 액세스 토큰 (PAT)** VM 관리자 계정으로 실행 합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image96.png)

    ```Bash  
        cd \home\<user>
        sudo mkdir myagent && cd myagent
        wget https://vstsagentpackage.azureedge.net/agent/2.134.2/vsts-agent-linux-x64-2.134.2.tar.gz
        sudo tar zxvf vsts-agent-linux-x64-2.134.2.tar.gz
    ```

2.  추출 된 빌드 에이전트 폴더로 이동 합니다. 다음 코드를 실행 합니다.

    ```Bash  
        cd ..
        sudo chmod -R 777 myagent
        cd myagent
        ./config.sh
    ```

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image97.png)

2.  이후에 **./config.sh**다음 서버 부팅 시 서비스를 사용 하도록 코드 및 서비스를 시작 완성을 실행 합니다.

    ```Bash  
    sudo ./svc.sh install
    sudo ./svc.sh start
    ```

이제 VSTS 폴더에서 에이전트를 표시 합니다.

#### <a name="endpoint-creation-permissions"></a>끝점 만들기 권한

사용자는 VSTO 빌드 스택에 Azure 서비스 앱을 배포할 수 있도록 끝점을 만들 수 있습니다. VSTS는 Azure Stack을 사용 하 여 연결 하는 빌드 에이전트에 연결 합니다.

![대체 텍스트](media\azure-stack-solution-machine-learning\image98.png)

1.  에 **설정을** 메뉴에서 **보안**합니다.

2.  에 **VSTS 그룹** 왼쪽에서 선택 목록 **의해 끝점 작성자**합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image99.png)

3.  에 **구성원 탭** 선택 합니다 **+ 추가**합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image100.png)

1.  형식 **username** 사용자 이름 목록에서 선택 합니다.

2.  **변경 내용 저장**을 선택합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image101.png)

3.  에 **VSTS 그룹** 왼쪽에서 선택 목록 **끝점 관리자**합니다.

4.  에 **구성원 탭** 선택 합니다 **+ 추가**합니다.

5.  입력 **사용자 이름** 목록에서 해당 사용자를 선택 합니다.

6.  선택 **변경 내용을 저장 합니다.**

    ![buchatech](media\azure-stack-solution-machine-learning\image102.jpeg)

    Azure Stack에서 빌드 에이전트는 다음 Azure Stack을 사용 하 여 통신에 대 한 끝점 정보를 전달 하는 VSTS에서 지침을 얻습니다.

    Azure Stack 연결에는 VSTS 준비가 되었습니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image103.png)

### <a name="configure-build-and-release-definitions"></a>빌드 및 릴리스 정의 구성 합니다.

이제는 연결이 설정 된 수동으로 만든된 AKS 및 Azure Container Registry 빌드에 Azure 끝점을 매핑할를 릴리스 정의 합니다.

#### <a name="create-the-build-definition-for-the-yaml-code"></a>The YAML 코드에 대 한 빌드 정의 만들기

1.  빌드 및 릴리스 허브에 있는 빌드 섹션을 선택 하 고 새 정의 만듭니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image104.png)

1.  VSTS Git 선택 하 고 이전에 만든 리포지토리를 선택 합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image105.png)

1.  빈 파이프라인 템플릿 선택

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image106.png)

1.  빌드 이름을 **복사 아티팩트** 에이전트 큐에 대 한 Azure Stack 빌드 서버를 선택 합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image107.png)

1.  1 단계에서 프로세스를 선택 하 고 이름을 **복사 아티팩트**, 한 다음 **태스크를 추가** 단계:

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image108.png)

1.  선택 **빌드 아티팩트 게시** 에서 합니다 **유틸리티** 나열 하 고 선택 **추가**합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image109.png)

1.  선택 합니다 **게시할 경로** 선택한 합니다 **iris_deployment.yaml** 파일입니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image110.png)

1.  아티팩트 이름 지정 **iris_deployment** 되도록 게시 위치를 선택 하 고 **Visual Studio Team Services/TFS**합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image111.png)

1.  선택 **저장 및 큐**합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image112.png)

1.  빌드 ID를 선택 하 여 빌드의 상태를 확인 합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image113.png)

성공 다음과 유사 하 게 표시 됩니다.

![대체 텍스트](media\azure-stack-solution-machine-learning\image114.png)

#### <a name="create-the-release-definition-for-the-yaml-code"></a>YAML 코드에 대 한 릴리스 정의 만들기

1.  빌드 및 릴리스 허브에 새 정의 릴리스 섹션을 선택 합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image115.png)

1.  템플릿으로 빈 파이프라인을 선택 합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image106.png)

1.  스택 환경 Azure 이름입니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image116.png)

1.  새 아티팩트를 선택 하 여 추가할 **아티팩트** 고 **+ 추가**

2.  원본 유형으로 빌드를 선택 하 고 **HybridMLIris** 프로젝트입니다.

3.  원본에 대해 이전에 만든 빌드 정의 선택 합니다.

4.  선택한 **추가**합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image117.png)

1.  Azure Stack 환경에서 선택한 Azure Stack에 새 작업 추가

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image118.png)


1.  에이전트 단계에서는 Azure Stack 호스트 된 빌드 서버에 에이전트 큐를 설정 합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image119.png)

1.  이 단계에 새 태스크를 추가 하 고 배포 내에서 Kubernetes 작업으로 배포 선택한 추가 선택 합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image120.png)


1.  이름을 **Kubectl Apply** (기본 이름)을 적용 명령을 선택 합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image121.png)

    이제 새 Kubernetes 서비스 연결을 만듭니다.

#### <a name="create-kubernetes-service-endpoint"></a>Kubernetes 서비스 끝점 만들기

1.  Kubernetes 서비스 연결을 선택 합니다 **+ 새로 만들기** 단추를 선택한**Kubernetes**목록에서. 이 끝점을 사용 하 여 연결할 수는**VSTS**하며**Azure Container Service (AKS)** 합니다.

2.  **연결 이름**: 연결 이름을 제공 합니다.

3.  **서버 URL**:는 formathttp에서 컨테이너 서비스 주소를 제공 합니다. / / {API 서버 주소}

4.  **Kubeconfig**: Kubeconfig 값을 가져오려면 관리자 권한으로 시작 하는 명령 프롬프트에서 다음 Azure 명령을 실행 합니다.

    > [!Important]  
    > 이 CLI 창을 사용 하 여 다음 단계를 수행 합니다.

6.  CLI 창에서 Azure에 로그인합니다. [az 로그인에 대한 자세한 내용은 여기를 참조합니다](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

7.  명령 프롬프트에 다음을 입력합니다.

    ```CLI
        az login
    ```

10. 이 명령은 브라우저에서 사용 하는 코드를 반환 합니다. <https://aka.ms/devicelogin>합니다.

11. 로 <https://aka.ms/devicelogin> 브라우저에서 합니다. 메시지가 표시 되 면 브라우저에 CLI에서 받은 코드를 입력 합니다.

    ![Kubernetes 서비스 끝점](media\azure-stack-solution-machine-learning\image122.png)

1.  Kubernetes 클러스터에 대 한 액세스 자격 증명을 가져오려면 명령 프롬프트에서 다음 명령을 입력 합니다.

### <a name="azure-ml-workbench-cli"></a>Azure ML Workbench CLI

az aks 자격 증명 가져오기 리소스 그룹 <yourResourceGroup> 이름 <yourazurecontainerservice>

![Kubernetes 서비스 끝점](media\azure-stack-solution-machine-learning\image123.png)

1.  로 이동 합니다 **.kube**홈 디렉터리에 있는 폴더 (예: c:\\사용자가\\<user>\\문서\\Kube)

2.  내용을 복사 합니다**config**파일 및 Kubernetes 연결 창에 붙여 넣습니다. 선택 된**확인**단추입니다.

    ![Kubernetes 서비스 끝점](media\azure-stack-solution-machine-learning\image124.png)
    

3.  Kubernetes 끝점 만들어지고 선택 되 면 구성 파일을 추가 하려면 사용 하 여 구성 파일 확인란을 선택 합니다. 다음 연결 된 아티팩트에 iris_deployment.yaml 파일로 이동 합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image125.png)

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image126.png)

4.  릴리스 정의를 저장합니다.

#### <a name="check-the-status-of-the-release-definition"></a>릴리스 정의의 상태를 확인 합니다. 

저장 한 후 VSTS 릴리스 정의 자동으로 릴리스를 시작 합니다.

WSL 명령 프롬프트에서 빠른 명령을 실행 한 다음 Kubernetes UI를 검사 하 여 배포의 상태를 확인 합니다.

```Bash  
kubectl get deployments
```

출력 배포 프로세스에서 작업 하는 동안 이와 비슷하게 같아야 합니다.

![대체 텍스트](media\azure-stack-solution-machine-learning\image127.png)

```Bash  
kubectl proxy
```

Kubernetes UI 실행 되 면 배포 이동할 [ **https://localhost:8001/** ](https://localhost:8001/) 으로 이동 **워크 로드에는 복제본 집합->** 합니다.

![대체 텍스트](media\azure-stack-solution-machine-learning\image128.png)

### <a name="deploy-the-yaml-service"></a>YAML 서비스 배포

#### <a name="upload-the-irisserviceyaml-to-the-repository-and-sync-changes"></a>iris_service.yaml 리포지토리와 동기화 변경 내용 업로드

1.  새로 복제 된 리포지토리로 이동 합니다.

    ```Bash  
    cd /mnt/c/users/<User>/source/repos/HybridMLIris/HybridMLIris/
    ```

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image75.png)

1.  복사 합니다 **iris_service.yaml** 리포지토리로 파일입니다.

    ```Bash  
    cp /mnt/c/users/<User>/documents/Kube/iris_service.yaml  /mnt/c/users/<User>/source/repos/HybridMLIris/HybridMLIris/iris_service.yaml
    ```

1.  GIT에서 변경 내용 커밋

    ```Bash  
    git add .
    git commit -m “Added Service YAML” 
    git push
    ```

![대체 텍스트](media\azure-stack-solution-machine-learning\image129.png)

#### <a name="update-the-build-definition-for-the-yaml-code"></a>The YAML 코드에 대 한 빌드 정의 업데이트 합니다.

1.  빌드 및 릴리스 허브에 있는 빌드 섹션을 선택 하 고 앞에서 만든 정의 선택 합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image130.png)

2.  정의 편집 하려면 편집 단추를 선택 합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image131.png)

3.  **태스크를 추가** 단계입니다. 선택 **빌드 아티팩트 게시** 에서 합니다 **유틸리티** 나열 하 고 선택 **추가**합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image108.png)

4.  이름을 **Kubectl Apply** (기본 이름)을 적용 명령을 선택 합니다.



#### <a name="update-the-release-definition-for-the-yaml-code"></a>YAML 코드에 대 한 릴리스 정의 업데이트 합니다.

1.  빌드 및 릴리스 허브에서 theReleases 섹션을 선택 하 고 이전에 만든 릴리스 정의 선택 합니다. 편집 링크를 선택 합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image132.png)

1.  환경을 선택 **Azure Stack** 다음 Azure Stack에 새 태스크를 추가 합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image133.png)

1.  추가 **새 작업** 이 단계를 선택 합니다 **Kubernetes에 배포** 아래에서 작업 **배포** 선택한 **추가**합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image134.png)

1.  이름을 **Kubectl Apply** (기본 이름)을 적용 명령을 선택 합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image109.png)

1.  앞에서 만든 Azure Stack 연결 Kubernates 서비스 연결을 설정 하 고 다음을 선택 합니다 **구성 파일을 사용 하 여** 구성 파일을 추가 하려면 확인란을 선택 합니다. 연결 된 아티팩트에 iris_service.yaml 파일을 찾습니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image135.png)


    ![대체 텍스트](media\azure-stack-solution-machine-learning\image136.png)

1.  릴리스 정의를 저장합니다.

#### <a name="check-the-status-of-the-release-definition"></a>릴리스 정의의 상태를 확인 합니다.

저장 한 후 VSTS 릴리스 정의 자동으로 릴리스를 시작 합니다.

WSL 명령 프롬프트에서 빠른 명령을 실행 한 다음 Kubernetes UI를 검사 하 여 배포의 상태를 확인 합니다.

```Bash  
kubectl get deployments
```

출력 배포 프로세스에서 작업 하는 동안 이와 비슷하게 같아야 합니다.

![대체 텍스트](media\azure-stack-solution-machine-learning\image127.png)


```Bash  
kubectl proxy
```

Kubernetes UI 실행 되 면 배포 이동할 [ **https://localhost:8001/** ](https://localhost:8001/) 으로 이동 **워크 로드에는 복제본 집합->** 합니다.

![대체 텍스트](media\azure-stack-solution-machine-learning\image137.png)


### <a name="kubernetes-scoring-and-validation"></a>Kubernetes 점수 매기기 및 유효성 검사

Kubernetes UI를 시작 합니다.

```Bash  
kubectl proxy
```

Kubernetes UI를 찾은 다음로 이동 **배포** -> **아이리스 배포** -> **새 복제본 세트**  ->  **Iris-배포-xxxxxxxxx** (xs는 배포 ID)입니다.

![대체 텍스트](media\azure-stack-solution-machine-learning\image138.png)

으로 이동 **서비스** 선택한 합니다 **외부 끝점** 유효성을 검사할 서비스의 작동 합니다.

![대체 텍스트](media\azure-stack-solution-machine-learning\image139.png)

아래와 비슷한 유효성 검사 메시지를 표시 합니다.

![대체 텍스트](media\azure-stack-solution-machine-learning\image140.png)

#### <a name="create-azure-stack-scoring-function-app-in-the-azure-stack-portal"></a>Azure Stack 포털에 함수 앱을 점수 매기기 Azure 스택 만들기

함수 앱은 각 함수의 실행을 호스트 해야 합니다. 함수 앱을 쉽게 관리, 배포 및 공유 리소스를 논리 단위로 그룹화 하는 함수를 허용 합니다.

1.  Azure Stack 사용자 포털에서 선택 합니다 **+ 새로 만들기** 단추 왼쪽 위에 있는, 선택한**웹 + 모바일** >**함수 앱**합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image141.png)

1.  함수 이름을 **데이터 함수** 콘텐츠 나머지 Machine Learning을 사용 하 여 동일한 리소스 그룹에 배치 합니다. 도구에서 자동 생성 소비에 대 한 새 앱 서비스 계획 및 앱 저장소에 대해 이전에 만든 저장소 계정을 사용 하도록 합니다.

    ![새 함수 앱 설정 정의](media\azure-stack-solution-machine-learning\image142.png)

1.  선택**만들기**프로 비전 하 고 함수 앱을 배포 합니다.

2.  포털의 오른쪽 위 모서리에 있는 알림 아이콘을 선택 하 고 확인 합니다**배포 성공** 메시지입니다.

    ![새 함수 앱 설정 정의](media\azure-stack-solution-machine-learning\image143.png)

1.  선택**리소스로 이동** 새 함수 앱을 볼 수 있습니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image144.png)

1.  선택 하 여 새 함수를 만듭니다 **함수**, 해당 **+ 새 함수** 단추입니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image145.png)

1.  HTTP 트리거를 선택 합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image146.png)

1.  선택 **C\#**  언어 및 함수 이름: **정리-데이터 점수 매기기**, 권한 부여 수준을 설정 하 고 **익명**합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image147.png)

1.  예제 내용의 함수로 정리-점수-데이터에 대 한 코드를 복사-붙여넣기.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image148.png)

#### <a name="use-postman-to-validate-functions"></a>Postman을 사용 하 여 유효성을 검사할 함수

Kbernetes 및 함수를 설정한 후 확인 올바르게 사용할 수 있습니다 Postman 무료 앱을 테스트 하 고 함수 및 스키마 유효성 검사. 이 프로세스를 시작 하려면 먼저 Kubernetes 인스턴스에서 일부 정보를 수집 해야 합니다.

1.  Kubernetes UI를 찾은 다음로 이동 **배포** -> **아이리스 배포** -> **새 복제본 세트**  ->  **Iris-배포-xxxxxxxxx** (xs는 배포 ID)

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image138.png)

1.  으로 이동 **Services** 복사 합니다 **외부 끝점**합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image149.png)

1.  다운로드 하 고 Postman 앱을 설치 [여기](https://www.getpostman.com/apps) 필요한 경우.

2.  Postman 앱에 로그인 하 고 새 파일 대화 상자를 닫습니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image150.png)

1.  postman 앱 내에서 게시물을 선택...

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image151.png)

1.  붙여넣기를 **외부 끝점** URL에서 postman 앱에는 **요청 URL** 추가  **\\점수** 아래 표시 된 대로 URL의 끝에 합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image152.png)

1.  선택 합니다 **본문** 탭 한 다음 데이터 형식으로 **원시**, 한 다음 **JSON**합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image153.png)

1.  웹 브라우저에서로 이동 **외부 끝점**합니다. 다음 URL을 추가 **/swagger.json** 서비스 Swagger 파일 설치를 테스트 하는 데이 인해 발생 합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image154.png)

1.  에 나열 된 예제를 복사 합니다 **Swagger.JSON** 파일입니다.

2.  Postman 앱에서이 예제에서는 게시물의 본문에 붙여넣고 선택 **보낼**합니다. 아래에 표시 된 것과 비슷합니다는 값을 반환 해야 합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image155.png)

## <a name="step-7-create-an-azure-stack-storage-account-and-storage-queue"></a>7 단계: Azure Stack 저장소 계정 및 저장소 큐 만들기

데이터에 대 한 Azure Stack 저장소 계정 및 저장소 큐를 만듭니다.

1.  Azure Stack 사용자 포털에 로그인 합니다. (각 Azure Stack에 고유 포털이 URL)

2.  Azure Stack 사용자 포털에서 서비스의 메뉴를 열고 왼쪽의 메뉴 확장 **모든 서비스**합니다. 아래로 스크롤하여 **저장소** 선택한 **저장소 계정**합니다. 에 **Storage 계정** 창 **추가**합니다.

3.  저장소 계정의 이름을 입력합니다.

4.  저장소 계정의 복제 옵션을 선택 합니다. **LRS**합니다.

5.  새 리소스 그룹을 지정하거나 기존 리소스 그룹을 선택합니다.

6.  선택 **로컬** 저장소 계정의 위치에 대 한 합니다.

7.  선택**만들기**저장소 계정을 만들어야 합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image156.png)

1.  최근에 만든 저장소 계정을 선택 합니다.

2.  선택**큐**합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image157.png)

1.  선택 **+ 큐** 선택한 큐 이름을 **확인 합니다.**

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image158.png)

1.  가져오기는 **연결 문자열** 저장소 큐에 복사 합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image159.png)

1.  Azure 함수 앱으로 이동 하 고 선택한 **응용 프로그램 설정**합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image160.png)

1.  함수 앱의 응용 프로그램 설정 내에서 응용 프로그램 설정, 아래로 스크롤하여 선택한 **+ 새 설정 추가**합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image161.png)

1.  저장소 계정 이름을 입력 합니다 **이름을** 끝에 추가 필드에; (_s)

이렇게 하면 저장소 계정 끝점을 이해 하려면 응용 프로그램.

1.  연결 문자열을 붙여 합니다 **값** 필드입니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image162.png)

1.  선택한 응용 프로그램 설정의 최상위까지 스크롤하여 **저장할**합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image163.png)

### <a name="update-the-scoring-function-to-use-storage-queue"></a>저장소 큐를 사용 하는 점수 매기기 함수를 업데이트 합니다.

1.  선택 **통합** 함수에서 선택 취소 하 고는 **가져오기** 옵션입니다.

2.  **저장**을 선택합니다.

3.  선택한 **+ 새 출력** 출력에서 합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image164.png)

1.  선택한 **Azure Queue Storage** 선택한 **선택**합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image165.png)

1.  업데이트를 **큐 이름** 설정한 후 앞에서 만든 저장소 큐에는 **저장소 계정 연결** 을 선택 하 고 이전 만든 저장소 계정 연결 **저장 합니다.**

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image166.png)

## <a name="step-8-create-a-function-to-handle-clean-data"></a>8 단계: 데이터 정리를 처리 하는 함수 만들기

Azure Stack에서 데이터 정리를 Azure로 이동 하는 새 Azure Stack 함수를 만듭니다.

1.  선택 하 여 새 함수를 만듭니다 **함수**, 해당 **+ 새 함수** 단추입니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image167.png)

1.  선택 **타이머 트리거**합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image168.png)

1.  선택 **C\#**  언어 및 함수 이름: **azure에 업로드** 일정을 설정 하 고 **0 0 \*/1 \* \* \***  는 CRON 표기법은 시간에 한 번입니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image169.png)

### <a name="get-the-connection-string-to-the-azure-hosted-storage-account"></a>Azure 호스 티 드 저장소 계정에 연결 문자열 가져오기

1.  이동할 <https://portal.azure.com> 를 선택한는 **Azure Storage 계정** 앞에서 만든 합니다.

2.  선택 **액세스 키**를 복사 합니다 **연결 문자열** 저장소 계정에 대 한 합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image170.png)

### <a name="update-the-upload-to-azure-function-to-use-the-azure-hosted-storage"></a>업데이트 업로드-에-azure 호스트 된 Azure Storage를 사용 하는 함수

1.  Azure 함수 앱으로 이동 하 고 선택한 **응용 프로그램 설정**합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image171.png)

1.  함수 앱의 응용 프로그램 설정 내에서 응용 프로그램 설정, 아래로 스크롤하여 선택한 **+ 새 설정 추가**합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image172.png)

1.  저장소 계정 이름을 입력 합니다 **이름을** 끝에 추가 필드에; (_s)

이렇게 하면 저장소 계정 끝점을 이해 하려면 응용 프로그램.

1.  Azure 호스트 된 저장소 계정 연결 문자열에 붙여 합니다 **값** 필드입니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image173.png)

1.  선택한 응용 프로그램 설정의 최상위까지 스크롤하여 **저장할**합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image174.png)

1.  로 다시 이동 합니다 **azure에 업로드** 함수입니다.

2.  선택 **통합** 함수입니다.

3.  선택한 **+ 새 출력** 출력에서 합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image175.png)

1.  선택한 **Azure Blob Storage** 선택한 **선택**합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image176.png)

1.  업데이트를 **경로** 형식은 앞부분에서 만든 저장소 컨테이너에: **uploadeddata / {rand-guid}.txt**를 설정한 후는 **저장소 계정 연결** 에 Azure storage 계정 연결 이전 만들고 선택 **저장 합니다.**

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image177.png)

1.  복사-붙여넣기에 대 한 코드 예제에서는 내용의 **azure에 업로드** 함수로 합니다.

2.  저장소 계정 연결 문자열을 가리키도록 필요에 따라 수정 합니다.

3.  저장 하 고 코드를 실행 합니다.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image178.png)

1.  Azure에서 클라우드까지 구문 분석 데이터를 보려면 Azure 호스트 된 저장소 계정에 확인: 성공 비슷합니다는 아래.

    ![대체 텍스트](media\azure-stack-solution-machine-learning\image179.png)

데이터를 Azure Stack 호스트 된 Kubernetes 기계 학습에서 중요 한 데이터의 삭제 되었으며 Azure Stack 호스팅된 함수 앱을 통해 온-프레미스 Azure Stack에서 Azure 공용 클라우드로 업로드는 edge/연결 끊김의 업로드에 대 한 데이터를 준비할 수 및 시나리오입니다.

## <a name="next-steps"></a>다음 단계

 - Azure 클라우드 패턴에 대 한 자세한 내용은 참조 하세요 [클라우드 디자인 패턴](https://docs.microsoft.com/azure/architecture/patterns)합니다.
