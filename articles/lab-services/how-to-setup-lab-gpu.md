---
title: Azure Lab Services에서 Gpu를 사용 하 여 랩 설정 Microsoft Docs
description: GPU (그래픽 처리 장치) 가상 머신으로 랩을 설정 하는 방법에 대해 알아봅니다.
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: abd182339719f19a521feed95f7cfbed6942b3e8
ms.sourcegitcommit: ada9a4a0f9d5dbb71fc397b60dc66c22cf94a08d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2020
ms.locfileid: "91404785"
---
# <a name="set-up-a-lab-with-gpu-virtual-machines"></a>GPU virtual machines를 사용 하 여 랩 설정

이 문서에서는 다음 작업을 수행하는 방법을 보여 줍니다.

- *시각화* 및 *계산* gpu (그래픽 처리 장치) 중에서 선택 합니다.
- 적절 한 GPU 드라이버가 설치 되어 있는지 확인 합니다.

## <a name="choose-between-visualization-and-compute-gpu-sizes"></a>시각화 및 계산 GPU 크기 중에서 선택
랩 만들기 마법사의 첫 번째 페이지에서 **가상 컴퓨터 크기** 를 선택 하십시오. 드롭다운 목록에서 클래스에 필요한 vm의 크기를 선택 합니다.  

![VM 크기를 선택 하기 위한 "새 랩" 창의 스크린샷](./media/how-to-setup-gpu/lab-gpu-selection.png)

이 프로세스에는 **시각화** 또는 **계산** gpu를 선택할 수 있는 옵션이 있습니다.  학생 들이 사용 하는 소프트웨어를 기반으로 하는 GPU 유형을 선택 하는 것이 중요 합니다.  

다음 표에서 설명 하는 것 처럼 *계산* GPU 크기는 계산 집약적인 응용 프로그램을 위한 것입니다.  예를 들어 [자연어 처리 클래스 형식의 심층 학습은](./class-type-deep-learning-natural-language-processing.md) **작은 GPU (계산)** 크기를 사용 합니다.  학생 들은 [Data Science Virtual Machine 이미지](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) 에서 제공 하는 심층 학습 프레임 워크 및 도구를 사용 하 여 방대한 데이터 집합을 사용 하 여 심층 학습 모델을 학습 하기 때문에 계산 GPU는 이러한 종류의 클래스에 적합 합니다.

| 크기 | 코어 수 | RAM | Description | 
| ---- | ----- | --- | ----------- | 
| 소형 GPU(컴퓨팅) | -&nbsp;6 &nbsp; 코어<br>-&nbsp;56 &nbsp; GB &nbsp; RAM  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |이 크기는 AI (인공 지능) 및 심층 학습 같은 계산 집약적인 응용 프로그램에 가장 적합 합니다. |

*시각화* GPU 크기는 그래픽을 많이 사용 하는 응용 프로그램을 위한 것입니다.  예를 들어 [SOLIDWORKS 엔지니어링 클래스 형식은](./class-type-solidworks.md) **작은 GPU (시각화)** 크기를 사용 하 여 표시 합니다.  이러한 종류의 클래스에는 학생 들이 사용 하기에 적합 합니다 .이는 학생 들이 solid 개체를 모델링 하 고 시각화할 수 있도록 CAD (SOLIDWORKS 3D 컴퓨터 사용 디자인) 환경과 상호 작용 하기 때문입니다.

| 크기 | 코어 수 | RAM | Description | 
| ---- | ----- | --- | ----------- | 
| 소형 GPU(시각화) | -&nbsp;6 &nbsp; 코어<br>-&nbsp;56 &nbsp; GB &nbsp; RAM  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | 이 크기는 OpenGL 및 DirectX와 같은 프레임 워크를 사용 하는 원격 시각화, 스트리밍, 게임 및 인코딩에 적합 합니다. |
| 중간 GPU(시각화) | -&nbsp;12 &nbsp; 코어<br>-&nbsp;112 &nbsp; GB &nbsp; RAM  | [Standard_NV12](https://docs.microsoft.com/azure/virtual-machines/nv-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | 이 크기는 OpenGL 및 DirectX와 같은 프레임 워크를 사용 하는 원격 시각화, 스트리밍, 게임 및 인코딩에 적합 합니다. |

> [!NOTE]
> 클래스룸 랩을 만들 때 이러한 VM 크기 중 일부가 목록에 표시되지 않을 수 있습니다. 이 목록은 랩 위치의 현재 용량을 기준으로 채워집니다. 랩 계정 작성자가 [랩 작성자가 랩의 위치를 선택하도록 허용](allow-lab-creator-pick-lab-location.md)할 경우 랩에 다른 위치를 선택하고 VM 크기를 사용할 수 있는지 확인할 수 있습니다. Vm의 가용성에 대해서는 [지역별 사용 가능한 제품](https://azure.microsoft.com/regions/services/?products=virtual-machines)을 참조 하세요.

## <a name="ensure-that-the-appropriate-gpu-drivers-are-installed"></a>적절 한 GPU 드라이버가 설치 되어 있는지 확인 합니다.
랩 Vm의 GPU 기능을 활용 하려면 적절 한 GPU 드라이버가 설치 되어 있는지 확인 합니다.  Lab 만들기 마법사에서 GPU VM 크기를 선택할 때 **gpu 드라이버 설치** 옵션을 선택할 수 있습니다.  

!["GPU 드라이버 설치" 옵션을 보여 주는 "새 랩"의 스크린샷](./media/how-to-setup-gpu/lab-gpu-drivers.png)

위의 그림에 표시 된 것 처럼이 옵션은 기본적으로 사용 하도록 설정 되어 있으며,이 옵션은 선택한 GPU 및 이미지 유형에 대해 *최신* 드라이버가 설치 되어 있는지 확인 합니다.
- *계산* GPU 크기를 선택 하는 경우 랩 Vm은 [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) GPU에 의해 구동 됩니다.  이 경우 고성능 컴퓨팅을 가능 하 게 하 [는 최신 드라이버가](http://developer.download.nvidia.com/compute/cuda/2_0/docs/CudaReferenceManual_2.0.pdf) 설치 되어 있습니다.
- *시각화* GPU 크기를 선택 하는 경우 랩 Vm은 [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU 및 [그리드 기술로](https://www.nvidia.com/content/dam/en-zz/Solutions/design-visualization/solutions/resources/documents1/NVIDIA_GRID_vPC_Solution_Overview.pdf)구동 됩니다.  이 경우 최신 그리드 드라이버가 설치 되어 그래픽을 많이 사용 하는 응용 프로그램을 사용할 수 있습니다.

### <a name="install-the-drivers-manually"></a>수동으로 드라이버 설치
최신 버전이 아닌 드라이버 버전을 설치 해야 할 수 있습니다.  이 섹션에서는 *계산* gpu를 사용 하는지 *시각화* gpu를 사용 하는지에 따라 적절 한 드라이버를 수동으로 설치 하는 방법을 보여 줍니다.

#### <a name="install-the-compute-gpu-drivers"></a>계산 GPU 드라이버 설치

계산 GPU 크기에 대 한 드라이버를 수동으로 설치 하려면 다음을 수행 합니다.

1. 랩 만들기 마법사에서 [랩을 만들](./how-to-manage-classroom-labs.md)때 **GPU 드라이버 설치** 설정을 사용 하지 않도록 설정 합니다.

1. 랩을 만든 후 템플릿 VM에 연결 하 여 적절 한 드라이버를 설치 합니다.

   ![NVIDIA 드라이버 다운로드 페이지의 스크린샷](./media/how-to-setup-gpu/nvidia-driver-download.png) 

   a. 브라우저에서 [NVIDIA 드라이버 다운로드 페이지로](https://www.nvidia.com/Download/index.aspx)이동 합니다.  
   b. **제품 유형을** **Tesla**로 설정 합니다.  
   c. **제품 시리즈** 를 **K 시리즈**로 설정 합니다.  
   d. 랩을 만들 때 선택한 기본 이미지 유형에 따라 **운영 체제** 를 설정 합니다.  
   e. 필요한 verda 드라이버의 버전으로 **Verda Toolkit** 을 설정 합니다.  
   f. **검색** 을 선택 하 여 드라이버를 찾습니다.  
   g. **다운로드** 를 선택 하 여 설치 관리자를 다운로드 합니다.  
   h. 드라이버를 템플릿 VM에 설치 하도록 설치 관리자를 실행 합니다.  
1. [설치 된 드라이버 유효성 검사](how-to-setup-lab-gpu.md#validate-the-installed-drivers) 섹션의 지침에 따라 드라이버가 올바르게 설치 되었는지 확인 합니다. 
1. 클래스에 필요한 드라이버 및 기타 소프트웨어를 설치한 후 **게시** 를 선택 하 여 학생의 vm을 만듭니다.

> [!NOTE]
> Linux 이미지를 사용 하는 경우 설치 관리자를 다운로드 한 후 [linux에서 VERDA 드라이버 설치](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup?toc=/azure/virtual-machines/linux/toc.json#install-cuda-drivers-on-n-series-vms)의 지침에 따라 드라이버를 설치 합니다.

#### <a name="install-the-visualization-gpu-drivers"></a>시각화 GPU 드라이버 설치

시각화 GPU 크기에 대 한 드라이버를 수동으로 설치 하려면 다음을 수행 합니다.

1. 랩 만들기 마법사에서 [랩을 만들](./how-to-manage-classroom-labs.md)때 **GPU 드라이버 설치** 설정을 사용 하지 않도록 설정 합니다.
1. 랩을 만든 후 템플릿 VM에 연결 하 여 적절 한 드라이버를 설치 합니다.
1. 운영 체제에 대 한 지침에 따라 템플릿 VM에 Microsoft에서 제공 하는 그리드 드라이버를 설치 합니다.
   -  [Windows NVIDIA GRID 드라이버](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers)
   -  [Linux NVIDIA 그리드 드라이버](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup?toc=/azure/virtual-machines/linux/toc.json#nvidia-grid-drivers)
  
1. 템플릿 VM을 다시 시작 합니다.
1. [설치 된 드라이버 유효성 검사](how-to-setup-lab-gpu.md#validate-the-installed-drivers) 섹션의 지침에 따라 드라이버가 올바르게 설치 되었는지 확인 합니다.
1. 클래스에 필요한 드라이버 및 기타 소프트웨어를 설치한 후 **게시** 를 선택 하 여 학생의 vm을 만듭니다.

### <a name="validate-the-installed-drivers"></a>설치 된 드라이버의 유효성 검사
이 섹션에서는 GPU 드라이버가 제대로 설치 되었는지 확인 하는 방법을 설명 합니다.

#### <a name="windows-images"></a>Windows 이미지
1.  Windows를 실행 하는 [N 시리즈 vm에 NVIDIA GPU 드라이버 설치](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation)의 "드라이버 설치 확인" 섹션의 지침을 따르세요.
1.  *시각화* GPU를 사용 하는 경우 다음을 수행할 수도 있습니다.
    - NVIDIA 제어판에서 GPU 설정을 확인 하 고 조정 합니다. 이렇게 하려면 **Windows 제어판**에서 **하드웨어**를 선택한 다음 **NVIDIA 제어판**을 선택 합니다.

      ![NVIDIA 제어판 링크를 보여 주는 Windows 제어판의 스크린샷](./media/how-to-setup-gpu/control-panel-nvidia-settings.png) 

     - **작업 관리자**를 사용 하 여 GPU 성능을 확인 합니다.  이렇게 하려면 **성능** 탭을 선택한 다음 **GPU** 옵션을 선택 합니다.

       ![작업 관리자 GPU 성능 탭을 보여 주는 스크린샷](./media/how-to-setup-gpu/task-manager-gpu.png) 

      > [!IMPORTANT]
      > NVIDIA 제어판 설정은 *시각화* gpu에만 액세스할 수 있습니다.  계산 GPU에 대 한 NVIDIA 제어판을 열려고 하면 다음과 같은 오류 메시지가 표시 됩니다. "NVIDIA 표시 설정을 사용할 수 없습니다.  현재 NVIDIA GPU에 연결 된 디스플레이를 사용 하 고 있지 않습니다. "  마찬가지로, 작업 관리자의 GPU 성능 정보는 시각화 Gpu에 대해서만 제공 됩니다.

#### <a name="linux-images"></a>Linux 이미지
Linux를 실행 하는 [N 시리즈 vm에 NVIDIA GPU 드라이버 설치](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup#verify-driver-installation)의 "드라이버 설치 확인" 섹션의 지침을 따르세요.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [클래스룸 랩 만들기 및 관리](how-to-manage-classroom-labs.md)
- [SOLIDWORKS 컴퓨터-CAD (design) 클래스 형식](class-type-solidworks.md)
- [MATLAB (matrix 실습) 클래스 형식](class-type-matlab.md)



