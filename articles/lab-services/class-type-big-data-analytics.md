---
title: Azure Lab Services를 사용 하 여 빅 데이터 분석을 학습할 랩 설정 Microsoft Docs
description: HDP (Hortonworks Data Platform)의 Docker 배포를 사용 하 여 빅 데이터 분석을 학습할 수 있도록 랩을 설정 하는 방법에 대해 알아봅니다.
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 6ae4e658985a3974b311171e83e6243dfc4a1ae9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85444032"
---
# <a name="set-up-a-lab-for-big-data-analytics-using-docker-deployment-of-hortonworks-data-platform"></a>HortonWorks Data Platform의 Docker 배포를 사용 하 여 빅 데이터 분석에 대 한 랩 설정

이 문서에서는 빅 데이터 분석 클래스를 학습 하도록 랩을 설정 하는 방법을 보여 줍니다.  이러한 종류의 클래스를 사용 하 여 많은 양의 데이터를 처리 하 고 컴퓨터 및 통계 학습 알고리즘을 적용 하 여 데이터 정보를 도출 하는 방법을 배웁니다.  학생의 핵심 목표는 빅 데이터를 저장, 관리 및 처리 하기 위한 도구를 제공 하는 [Apache Hadoop의 오픈 소스 소프트웨어 패키지](https://hadoop.apache.org/) 와 같은 데이터 분석 도구를 사용 하는 방법을 배우는 것입니다.

이 랩에서는 학생 들이 [HDP (Hortonworks Data Platform)](https://www.cloudera.com/products/hdp.html)라고 하는 [Cloudera](https://www.cloudera.com/)에서 제공 하는 인기 있는 상업용 버전의 Hadoop을 사용 합니다.  구체적으로 말하면 학생은 무료로 제공 되 고 학습 및 실험을 위해 제공 되는 간단 하 고 사용 하기 쉬운 버전의 플랫폼인 [HDP Sandbox 3.0.1](https://www.cloudera.com/tutorials/getting-started-with-hdp-sandbox/1.html) 를 사용 합니다.  이 클래스는 HDP 샌드박스를 배포한 Windows 또는 Linux VM (가상 머신)을 사용할 수 있지만이 문서에서는 Windows를 사용 하는 방법을 보여 줍니다.

이 랩에서 또 다른 흥미로운 측면은 [Docker](https://www.docker.com/) 컨테이너를 사용 하 여 랩 VM에 HDP 샌드박스를 배포할 것입니다.  각 Docker 컨테이너는 소프트웨어 응용 프로그램을 내에서 실행할 수 있는 자체 격리 된 환경을 제공 합니다.  개념적으로 Docker 컨테이너는 중첩 된 Vm과 비슷하며 [Docker 허브](https://www.docker.com/products/docker-hub)에서 제공 하는 컨테이너 이미지에 따라 다양 한 소프트웨어 응용 프로그램을 쉽게 배포 하 고 실행 하는 데 사용할 수 있습니다.  HDP Sandbox에 대 한 Cloudera의 배포 스크립트는 Docker 허브에서 [HDP Sandbox 3.0.1 docker 이미지](https://hub.docker.com/r/hortonworks/sandbox-hdp) 를 자동으로 가져오고 두 개의 docker 컨테이너를 실행 합니다.
  - sandbox-hdp
  - sandbox-프록시

## <a name="lab-configuration"></a>랩 구성

이 랩을 설정 하려면 시작 하려면 Azure 구독 및 랩 계정이 필요 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Azure 구독을 받은 후 Azure Lab Services에서 새 랩 계정을 만들 수 있습니다. 새 랩 계정을 만드는 방법에 대 한 자세한 내용은 자습서를 참조 [하 여 랩 계정 설정](tutorial-setup-lab-account.md)을 참조 하세요.  기존 랩 계정을 사용할 수도 있습니다.

### <a name="lab-account-settings"></a>랩 계정 설정

랩 계정에 대해 아래 표에 설명 된 설정을 사용 하도록 설정 합니다. Marketplace 이미지를 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [랩 작성자에 게 제공 되는 marketplace 이미지 지정](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)을 참조 하세요.

| 랩 계정 설정 | Instructions |
| ------------------- | ------------ |
|Marketplace 이미지| 랩 계정 내에서 사용할 Windows 10 Pro 이미지를 사용 하도록 설정 합니다.|

### <a name="lab-settings"></a>랩 설정

클래스 룸 랩을 설정 하는 경우 아래 표의 설정을 사용 합니다.  교실 랩을 만드는 방법에 대 한 자세한 내용은 [교실 랩 설정 자습서](tutorial-setup-classroom-lab.md)를 참조 하세요.

| 랩 설정 | 값/지침 |
| ------------ | ------------------ |
|Virtual Machine 크기| 중간 (중첩 된 가상화). 이 VM 크기는 관계형 데이터베이스, 메모리 내 캐싱 및 분석에 가장 적합 합니다.  이 크기는 중첩된 가상화도 지원합니다.|  
|가상 머신 이미지| Windows 10 Pro|

> [!NOTE] 
> Docker를 사용 하 여 HDP 샌드박스를 배포 하려면 다음이 필요 합니다.
>   - 중첩 된 가상화를 사용 하는 Windows Hyper-v
>   - 최소 10gb의 RAM

## <a name="template-machine-configuration"></a>템플릿 컴퓨터 구성

템플릿 컴퓨터를 설정 하려면 다음을 수행 합니다.
- Docker 설치
- HDP 샌드박스 배포
- PowerShell 및 Windows 작업 스케줄러를 사용 하 여 Docker 컨테이너를 자동으로 시작

### <a name="install-docker"></a>Docker 설치

이 섹션의 단계는 [Docker 컨테이너를 사용 하 여 배포 하기 위한 Cloudera의 지침](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)을 기반으로 합니다. 

Docker 컨테이너를 사용 하려면 먼저 Docker Desktop을 템플릿 VM에 설치 해야 합니다.

1. [필수 구성 요소 섹션](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) 의 단계에 따라 [Windows용 Docker](https://docs.docker.com/docker-for-windows/install/)를 설치 합니다. 

    > [!IMPORTANT] 
    > **Linux 컨테이너 대신 Windows 컨테이너 사용** 구성 옵션을 선택 하지 않은 상태로 유지 합니다.

1. **Windows 컨테이너 및 hyper-v 기능이** 설정 되어 있는지 확인 합니다.
   ![Windows 기능 켜기 또는 끄기](./media/class-type-big-data-analytics/windows-hyperv-features.png)

1. [Windows 용 메모리](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#memory-for-windows) 섹션의 단계에 따라 Docker의 메모리 구성을 구성 합니다.

    > [!WARNING]
    > Docker를 설치할 때 **Linux 컨테이너 대신 Windows 컨테이너 사용** 옵션을 실수로 확인 하는 경우 메모리 구성 설정이 표시 되지 않습니다.  이 문제를 해결 하려면 [Windows 시스템 트레이에서 Docker 아이콘을 클릭](https://docs.docker.com/docker-for-windows/#docker-settings-dialog)하 여 Linux 컨테이너를 사용 하도록 전환할 수 있습니다. Docker 바탕 화면 메뉴가 열리면 **Linux 컨테이너로 전환을**선택 합니다.
 
### <a name="deploy-hdp-sandbox"></a>HDP 샌드박스 배포

이 섹션에서는 HDP 샌드박스를 배포한 다음 브라우저를 사용 하 여 HDP 샌드박스에서 액세스 합니다.

1. 다음 단계를 완료 하는 데 권장 되므로 가이드의 [전제 조건 섹션](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) 에 나열 된 대로 [Git Bash](https://gitforwindows.org/) 를 설치 했는지 확인 합니다.

1. [Cloudera의 배포 및 Docker에 대 한 설치 가이드](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)를 사용 하 여 다음 섹션의 단계를 완료 합니다.
   
   -    HDP 샌드박스 배포
   -    HDP 샌드박스 확인

    > [!WARNING] 
    > HDP에 대 한 최신 .zip 파일을 다운로드 하는 경우 공백이 포함 된 디렉터리 경로에 .zip 파일을 저장 *하지* 않도록 합니다.

    > [!NOTE] 
    > 배포 하는 동안 **드라이브가 공유 되지**않았음을 나타내는 예외가 발생 하는 경우 HDP의 Linux 컨테이너에서 로컬 Windows 파일에 액세스할 수 있도록 Docker를 사용 하 여 C 드라이브를 공유 해야 합니다.  이 문제를 해결 하려면 [Windows 시스템 트레이에서 docker 아이콘을 클릭](https://docs.docker.com/docker-for-windows/#docker-settings-dialog) 하 여 Docker 바탕 화면 메뉴를 열고 **설정**을 선택 합니다.  **Docker의 설정** 대화 상자가 열리면 **리소스 > 파일 공유** 를 선택 하 고 **C** 드라이브를 확인 합니다.  그런 다음 HDP 샌드박스를 배포 하는 단계를 반복할 수 있습니다.

1. HDP 샌드박스에서 Docker 컨테이너를 배포 하 고 실행 한 후에는 브라우저를 시작 하 여 환경에 액세스 하 고, [샌드박스 시작 페이지](https://www.cloudera.com/tutorials/learning-the-ropes-of-the-hdp-sandbox.html#welcome-page) 를 열고 HDP 대시보드를 시작 하는 Cloudera의 지침을 따릅니다.

    > [!NOTE] 
    > 이 지침에서는 sandbox 환경의 로컬 IP 주소를 템플릿 VM의 호스트 파일에 있는 sandbox-hdp.hortonworks.com에 먼저 매핑한 것으로 가정 합니다.  이 매핑을 수행 **하지** 않으면로 이동 하 여 샌드박스 시작 페이지에 액세스할 수 있습니다 `http://localhost:8080` .

### <a name="automatically-start-docker-containers-when-students-log-in"></a>학생이 로그인 할 때 Docker 컨테이너를 자동으로 시작

학생 들에 게 편리한 사용 환경을 제공 하기 위해 다음을 자동으로 수행 하는 PowerShell 스크립트를 사용 합니다.
  - 학생이 시작 되 고 랩 VM에 연결 될 때 HDP 샌드박스 Docker 컨테이너를 시작 합니다.
  - 브라우저를 시작 하 고 샌드박스 시작 페이지로 이동 합니다.
또한 학생이 VM에 로그인 할 때 Windows 작업 스케줄러를 사용 하 여이 스크립트를 자동으로 실행 합니다.
이를 설정 하려면 [빅 데이터 분석 스크립팅](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BigDataAnalytics/)단계를 따르세요.

## <a name="cost-estimate"></a>예상 비용

이 랩의 비용을 예측 하려는 경우 다음 예제를 사용할 수 있습니다.

학생이 25명이며 예약된 클래스 시간이 20시간이고, 숙제 또는 할당이 10시간인 경우 랩 가격은
  - 25 개 학생 * (20 + 10) 시간 * 55 랩 단위 * 0.01 USD/시간당 = 412.50 USD

가격 책정에 대한 자세한 내용은 [Azure Lab Services 가격 책정](https://azure.microsoft.com/pricing/details/lab-services/)을 참조 하세요.

## <a name="conclusion"></a>결론

이 문서에서는 Docker를 사용 하 여 배포 된 Hortonworks Data Platform을 사용 하는 빅 데이터 분석 클래스의 랩을 만드는 데 필요한 단계를 안내 합니다.  이 클래스 형식의 설치는 비슷한 데이터 분석 클래스에 사용할 수 있습니다.  이 설정은 배포에 Docker를 사용 하는 다른 종류의 클래스에도 적용 될 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 단계는 랩을 설정 하는 데 일반적입니다.

- [템플릿 만들기 및 관리](how-to-create-manage-template.md)
- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [학생에 대 한 전자 메일 등록 링크](how-to-configure-student-usage.md#send-invitations-to-users)
