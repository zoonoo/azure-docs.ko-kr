---
title: Azure Lab 서비스를 사용하여 빅 데이터 분석을 가르치는 랩 설정 | 마이크로 소프트 문서
description: HORtonworks 데이터 플랫폼(HDP)의 Docker 배포를 사용하여 빅 데이터 분석을 가르치는 랩을 설정하는 방법을 알아봅니다.
services: lab-services
documentationcenter: na
author: nicolela
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/15/2020
ms.author: nicolela
ms.openlocfilehash: c499b7a0f1cd16bf57fef21742b01bda71249916
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538779"
---
# <a name="set-up-a-lab-to-big-data-analytics-using-docker-deployment-of-hortonworks-data-platform"></a>HortonWorks 데이터 플랫폼의 Docker 배포를 사용하여 빅 데이터 분석에 실험실 설정

이 문서에서는 빅 데이터 분석 클래스를 가르치기 위해 랩을 설정하는 방법을 보여 주었습니다.  이러한 유형의 수업을 통해 학생들은 대량의 데이터를 처리하고 기계 및 통계 학습 알고리즘을 적용하여 데이터 통찰력을 도출하는 방법을 배웁니다.  학생들의 주요 목표는 빅 데이터를 저장, 관리 및 처리하는 도구를 제공하는 [Apache Hadoop의 오픈 소스 소프트웨어 패키지와](https://hadoop.apache.org/) 같은 데이터 분석 도구를 사용하는 방법을 배우는 것입니다.

이 실습에서는 학생들은 [호튼웍스 데이터 플랫폼(HDP)이라고](https://www.cloudera.com/products/hdp.html)불리는 [Cloudera에서](https://www.cloudera.com/)제공하는 인기 있는 상용 버전의 Hadoop을 사용합니다.  특히, 학생들은 [HDP 샌드박스 3.0.1을](https://www.cloudera.com/tutorials/getting-started-with-hdp-sandbox/1.html) 사용할 것이며, 이는 비용 부담이 없고 학습 및 실험을 위한 간편하고 사용하기 쉬운 버전입니다.  이 클래스는 HDP 샌드박스가 배포된 Windows 또는 Linux 가상 머신(VM)을 사용할 수 있지만 이 문서에서는 Windows를 사용하는 방법을 보여 줍니다.

이 랩의 또 다른 흥미로운 측면은 [Docker](https://www.docker.com/) 컨테이너를 사용하여 랩 VM에 HDP 샌드박스를 배포한다는 것입니다.  각 Docker 컨테이너는 소프트웨어 응용 프로그램이 내부에서 실행되도록 자체 격리된 환경을 제공합니다.  개념적으로 Docker 컨테이너는 중첩된 VM과 유사하며 [Docker Hub에서](https://www.docker.com/products/docker-hub)제공되는 컨테이너 이미지를 기반으로 다양한 소프트웨어 응용 프로그램을 쉽게 배포하고 실행하는 데 사용할 수 있습니다.  HDP 샌드박스에 대한 Cloudera의 배포 스크립트는 Docker Hub에서 [HDP 샌드박스 3.0.1 도커 이미지를](https://hub.docker.com/r/hortonworks/sandbox-hdp) 자동으로 가져오고 두 개의 Docker 컨테이너를 실행합니다.
  - 샌드 박스 HDP
  - 샌드 박스 프록시

## <a name="lab-configuration"></a>랩 구성

이 랩을 설정하려면 Azure 구독 및 랩 계정을 시작해야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Azure 구독을 받으면 Azure Lab 서비스에서 새 랩 계정을 만들 수 있습니다. 새 랩 계정 만들기에 대한 자세한 내용은 [랩 계정 설정 자습서를](tutorial-setup-lab-account.md)참조하십시오.  기존 랩 계정을 사용할 수도 있습니다.

### <a name="lab-account-settings"></a>랩 계정 설정

랩 계정에 대해 아래 표에 설명된 설정을 사용하도록 설정합니다. 마켓플레이스 이미지를 사용하도록 설정하는 방법에 대한 자세한 내용은 [랩 작성자가 사용할 수 있는 마켓플레이스 이미지 지정을](https://docs.microsoft.com/azure/lab-services/classroom-labs/specify-marketplace-images)참조하십시오.

| 랩 계정 설정 | Instructions |
| ------------------- | ------------ |
|마켓플레이스 이미지| 랩 계정 내에서 사용할 Windows 10 Pro 이미지를 사용하도록 설정합니다.|

### <a name="lab-settings"></a>랩 설정

교실 실습을 설정할 때 아래 표의 설정을 사용합니다.  강의실 랩을 만드는 방법에 대한 자세한 내용은 [강의실 랩 자습서 설정을](tutorial-setup-classroom-lab.md)참조하십시오.

| 랩 설정 | 값/지침 |
| ------------ | ------------------ |
|Virtual Machine 크기| 중간(중첩된 가상화). 이 VM 크기는 관계형 데이터베이스, 메모리 내 캐싱 및 분석에 가장 적합합니다.  이 크기는 중첩된 가상화도 지원합니다.|  
|가상 머신 이미지| Windows 10 Pro|

> [!NOTE] 
> Docker를 사용하여 HDP 샌드박스를 배포하려면 다음을 수행해야 하므로 중간(중첩 가상화)을 사용해야 합니다.
>   - 중첩된 가상화를 갖춘 Windows 하이퍼-V
>   - 최소 10GB의 RAM

## <a name="template-machine-configuration"></a>템플릿 기계 구성

템플릿 컴퓨터를 설정하려면 다음을 수행합니다.
- Docker 설치
- HDP 샌드박스 배포
- PowerShell 및 Windows 작업 스케줄러를 사용하여 Docker 컨테이너를 자동으로 시작합니다.

### <a name="install-docker"></a>Docker 설치

이 섹션의 단계는 [Docker 컨테이너를 사용 하 고 배포에 대 한 Cloudera의 지침을](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)기반으로 합니다. 

Docker 컨테이너를 사용하려면 먼저 템플릿 VM에 Docker 데스크톱을 설치해야 합니다.

1. [Windows용 Docker를](https://docs.docker.com/docker-for-windows/install/)설치하려면 [필수 구성 조건 섹션의](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) 단계를 따릅니다. 

    > [!IMPORTANT] 
    > Linux 컨테이너 구성 **옵션 대신 Windows 컨테이너 사용** 옵션을 선택하지 않은 상태로 두는 것이 좋습니다.

1. Windows **컨테이너 및 하이퍼-V 기능이** 켜져 있는지 확인합니다.
   ![Windows 기능 켜기 또는 끄기](../media/class-type-big-data-analytics/windows-hyperv-features.png)

1. [Windows용 메모리](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#memory-for-windows) 섹션의 단계를 수행하여 Docker의 메모리 구성을 구성합니다.

    > [!WARNING]
    > Docker를 설치할 때 **Linux 컨테이너 대신 Windows 컨테이너 사용** 옵션을 실수로 확인하면 메모리 구성 설정이 표시되지 않습니다.  이 문제를 해결 하려면 Windows 시스템 [트레이에서 Docker 아이콘을 클릭](https://docs.docker.com/docker-for-windows/#docker-settings-dialog)하 여 리눅스 컨테이너를 사용 하 여 전환할 수 있습니다. Docker 데스크톱 메뉴가 열리면 **Linux 컨테이너로 전환이**선택됩니다.
 
### <a name="deploy-hdp-sandbox"></a>HDP 샌드박스 배포

이 섹션에서는 HDP 샌드박스를 배포한 다음 브라우저를 사용하여 HDP 샌드박스에 액세스합니다.

1. 다음 단계를 완료하는 것이 좋습니다 때문에 가이드의 [필수 구성 조건 섹션에](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html#prerequisites) 나열된 [Git Bash를](https://gitforwindows.org/) 설치한 것이 있는지 확인합니다.

1. [Docker용 Cloudera의 배포 및 설치 가이드를](https://www.cloudera.com/tutorials/sandbox-deployment-and-install-guide/3.html)사용하여 다음 섹션의 단계를 완료합니다.
   
   -    HDP 샌드박스 배포
   -    HDP 샌드박스 확인

    > [!WARNING] 
    > HDP용 최신 .zip 파일을 다운로드할 때 공백이 포함된 디렉터리 경로에 .zip 파일을 *저장하지* 않도록 합니다.

    > [!NOTE] 
    > 배포 중에 **드라이브가 공유되지 않았다는**예외가 있는 경우 HDP의 Linux 컨테이너가 로컬 Windows 파일에 액세스할 수 있도록 C 드라이브를 Docker와 공유해야 합니다.  이 문제를 해결하려면 [Windows 시스템 트레이의 Docker 아이콘을 클릭하여](https://docs.docker.com/docker-for-windows/#docker-settings-dialog) Docker 데스크톱 메뉴를 열고 **설정을**선택합니다.  **Docker의 설정** 대화 상자가 열리면 **리소스 > 파일 공유를** 선택하고 **C** 드라이브를 확인합니다.  그런 다음 단계를 반복하여 HDP 샌드박스를 배포할 수 있습니다.

1. HDP 샌드박스용 Docker 컨테이너가 배포되고 실행되면 브라우저를 실행하고 [샌드박스 웰컴 페이지를](https://www.cloudera.com/tutorials/learning-the-ropes-of-the-hdp-sandbox.html#welcome-page) 열고 HDP 대시보드를 실행하기 위한 Cloudera의 지침에 따라 환경에 액세스할 수 있습니다.

    > [!NOTE] 
    > 이 지침은 먼저 샌드박스 환경의 로컬 IP 주소를 템플릿 VM의 호스트 파일의 sandbox-hdp.hortonworks.com 매핑한 것으로 가정합니다.  이 매핑을 **수행하지 않으면** 로 이동하여 샌드박스 시작 페이지에 [http://localhost:8080](http://localhost:8080)액세스할 수 있습니다.

### <a name="automatically-start-docker-containers-when-students-log-in"></a>학생이 로그인할 때 Docker 컨테이너가 자동으로 시작됩니다.

학생에게 사용하기 쉬운 환경을 제공하기 위해 자동으로 PowerShell 스크립트를 사용합니다.
  - 학생이 실험실 VM을 시작하고 연결하면 HDP 샌드박스 Docker 컨테이너를 시작합니다.
  - 브라우저를 시작하고 샌드박스 시작 페이지로 이동합니다.
또한 학생이 VM에 로그인할 때 Windows 작업 스케줄러를 사용하여 이 스크립트를 자동으로 실행합니다.
이를 설정하려면 다음 단계인 [빅 데이터 분석 스크립팅을](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BigDataAnalytics/)수행합니다.

## <a name="cost-estimate"></a>비용 추정

이 랩의 비용을 추정하려면 다음 예제를 사용할 수 있습니다.

25명의 학생이 20시간의 수업 시간과 숙제 또는 과제에 대한 할당량 10시간의 수업의 경우 랩가격은 다음과 같은 것입니다.
  - 25명의 학생 * (20 + 10) 시간 * 55 랩 유닛 * 시간당 0.01 USD = 412.50 USD

가격에 대한 자세한 내용은 [Azure 랩 서비스 가격 을](https://azure.microsoft.com/pricing/details/lab-services/)참조하십시오.

## <a name="conclusion"></a>결론

이 문서에서는 Docker와 함께 배포된 Hortonworks 데이터 플랫폼을 사용하는 빅 데이터 분석 클래스에 대한 랩을 만드는 데 필요한 단계를 안내했습니다.  이 클래스 유형에 대한 설정은 유사한 데이터 분석 클래스에 사용할 수 있습니다.  이 설정은 배포에 Docker를 사용하는 다른 유형의 클래스에도 적용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 단계는 랩을 설정하는 데 일반적입니다.

- [템플릿 만들기 및 관리](how-to-create-manage-template.md)
- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [학생에게 이메일 등록 링크](how-to-configure-student-usage.md#send-invitations-to-users)
