---
title: Azure Data Science Virtual Machine이란?
description: Windows 및 Linux 데이터 과학 Virtual Machines에 대한 주요 분석 시나리오 및 구성 요소를 간략히 설명합니다.
keywords: 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 12/31/2019
ms.openlocfilehash: a63087620d50336c67472348da3b7f37fb380635
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/02/2020
ms.locfileid: "75611971"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Linux 및 Windows용 Azure Data Science Virtual Machine이란?

DSVM(Data Science Virtual Machine)은 데이터 과학 수행을 위해 특별히 구축된 Azure 클라우드 플랫폼의 사용자 지정 VM 이미지입니다. 여기에는 고급 분석을 위한 인텔리전트 애플리케이션 빌드를 바로 수행할 수 있도록 다수의 유명한 데이터 과학 도구가 미리 설치 및 구성되어 있습니다. 

DSVM은 다음에서 사용할 수 있습니다.
+ **Windows Server 2019(미리 보기)**
+ **Ubuntu 18.04 LTS(미리 보기)**
+ Windows Server 2016
+ Ubuntu 16.04 LTS 및 CentOS 7.4


> [!NOTE]
> 딥 러닝을 위한 모든 VM 도구는 Data Science Virtual Machine에 통합되었습니다. 


## <a name="why-choose-the-dsvm"></a>DSVM을 선택하는 이유
Data Science Virtual Machine의 목표는 산업 전반에 걸쳐 모든 기술 수준의 데이터 전문가에게 효율적이고 사전 구성된 데이터 과학 환경을 제공하는 것입니다. 비슷한 작업 영역을 직접 롤아웃하는 대신 DSVM을 프로비저닝할 수 있습니다. 이러한 선택은 설치, 구성 및 패키지 관리 프로세스에서 며칠 또는 몇 _주_를 절약할 수 있습니다. DSVM이 할당된 후 데이터 과학 프로젝트에 대한 작업을 즉시 시작할 수 있습니다.

## <a name="sample-use-cases"></a>샘플 사용 사례

다음은 DSVM 고객을 위한 몇 가지 일반적인 사용 사례를 보여줍니다.

### <a name="moving-data-science-workloads-to-the-cloud"></a>데이터 과학 워크로드를 클라우드로 이동

DSVM은 로컬 데스크톱을 관리형 클라우드 데스크톱으로 대체하려는 데이터 과학 팀을 위한 기본 구성을 제공하여 팀의 모든 데이터 과학자가 실험을 확인하고 협업을 촉진하는 일관된 설정을 사용할 수 있도록 합니다. 또한 시스템 관리 부담을 줄여 비용을 절감합니다. 시스템 관리 업무가 감소하기 때문에 고급 분석을 위해 소프트웨어 패키지를 평가, 설치 및 유지 관리하는 데 필요한 시간이 절약됩니다.

### <a name="data-science-training-and-education"></a>데이터 과학 학습 및 교육
데이터 과학 수업을 가르치는 엔터프라이즈 트레이너 및 강사는 대개 가상 머신 이미지를 제공합니다. 이미지를 통해 수강생에게 일관된 설정을 제공하여 샘플이 예상대로 작동하도록 할 수 있습니다. 

DSVM은 지원 및 비호환 문제를 덜어주는 일관된 설정을 사용하여 주문형 환경을 생성합니다. 이러한 환경을 빈번하게 구축해야 하는 경우, 특히 짧은 수업의 경우, 실질적인 이점이 있습니다.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>대규모 프로젝트를 위한 주문형 탄력적 용량
데이터 과학 해카톤/시합 또는 대규모 데이터 모델링 탐사에는, 일반적으로 짧은 기간 동안 확장된 하드웨어 용량이 필요합니다. DSVM은 요구에 따라 고성능 컴퓨팅 리소스를 실행하는 실험이 가능하도록 확장된 서버에 신속하게 데이터 과학 환경을 복제할 수 있습니다.

### <a name="custom-compute-power-for-azure-notebooks"></a>Azure Notebooks의 사용자 지정 컴퓨팅 능력
[Azure Notebooks](../../notebooks/azure-notebooks-overview.md)는 무료 호스트형 서비스이며 설치 없이 클라우드에서 Jupyter Notebook을 개발, 실행 및 공유합니다. 평가판 서비스 계층은 메모리 4GB, 데이터 1GB로 제한됩니다. 

모든 한도를 해제하려면 DSVM 또는 Jupyter 서버에서 실행되는 다른 모든 VM에 Notebook 프로젝트를 연결하면 됩니다. Azure Active Directory를 사용하는 계정(예: 회사 계정)으로 Azure Notebooks에 로그인하면 Notebooks가 해당 계정과 연결된 모든 구독의 DSVM을 자동으로 보여줍니다. [DSVM을 Azure Notebooks에 연결](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier)하여 사용 가능한 컴퓨팅 성능을 확장할 수 있습니다.

### <a name="short-term-experimentation-and-evaluation"></a>단기 실험 및 평가
DSVM을 사용하면, 특별히 게시된 [샘플 및 연습](./dsvm-samples-and-walkthroughs.md) 중 일부를 진행하여 새로운 데이터 과학 [도구](./tools-included.md)를 평가하거나 학습할 수 있습니다.


### <a name="deep-learning-with-gpus"></a>GPU를 사용한 딥 러닝
DSVM에서 학습 모델은 GPU(그래픽 처리 장치)를 기반으로 하는 하드웨어에서 딥 러닝 알고리즘을 사용할 수 있습니다. DSVM을 사용하면 Azure 플랫폼의 VM 확장 기능을 활용하여 필요에 따라 클라우드에서 GPU 기반 하드웨어를 사용할 수 있습니다. 큰 모델을 학습할 때 또는 동일한 OS 디스크를 유지하면서 고속 계산이 필요한 경우 GPU 기반 VM으로 전환할 수 있습니다. DSVM에서 N 시리즈 GPU 사용 가상 머신 SKU를 선택할 수 있습니다. Azure 체험 계정은 GPU 사용 가상 머신 SKU를 지원하지 않습니다.

Windows Server 2016 버전 DSVM에는 GPU 드라이버, 프레임워크 및 GPU 버전 딥 러닝 프레임워크가 미리 설치되어 있습니다. Linux 버전의 경우 CentOS 및 Ubuntu DSVM 모두에서 GPU에 대한 딥 러닝이 활성화됩니다. 

비 GPU 기반 Azure 가상 머신에 DSVM의 Ubuntu, CentOS 또는 Windows 2016 버전을 배포할 수도 있습니다. 이 경우 모든 딥 러닝 프레임워크는 CPU 모드로 다시 대체됩니다.
 
[사용 가능한 딥 러닝 및 AI 프레임워크에 대해 자세히 알아보세요.](dsvm-tools-deep-learning-frameworks.md)

<a name="included"></a>

## <a name="whats-included-on-the-dsvm"></a>DSVM에는 무엇이 포함되나요?

[여기](tools-included.md)에서 Windows 및 Linux DSVM의 전체 도구 목록을 참조하세요.

## <a name="next-steps"></a>다음 단계

다음 문서에서 자세한 내용을 알아보세요.

+ Windows:
  + [Windows DSVM 설정](provision-vm.md)
  + [Windows DSVM에서 수행할 수 있는 10 가지 작업](vm-do-ten-things.md)

+ Linux:
  + [Linux DSVM(Ubuntu) 설정](dsvm-ubuntu-intro.md)
  + [Linux DSVM(CentOS) 설정](linux-dsvm-intro.md)
  + [Linux DSVM의 데이터 과학](linux-dsvm-walkthrough.md)
