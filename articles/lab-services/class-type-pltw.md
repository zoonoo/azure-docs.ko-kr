---
title: Project를 사용 하 여 labs를 설정 하는 방법 Azure Lab Services
description: 프로젝트 리드에 클래스를 소개 하도록 랩을 설정 하는 방법을 알아봅니다.
ms.topic: article
ms.date: 10/28/2020
ms.openlocfilehash: e3783ae4fa07bf783841022903c4bcf3ab6fbe23
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94648007"
---
# <a name="set-up-labs-for-project-lead-the-way-classes"></a>프로젝트 리드에 대 한 랩 설정 클래스

[프로젝트 리드 (PLTW)](https://www.pltw.org/) 는 컴퓨터 과학, 엔지니어링 및 생명 공학 과학의 미국에서 사전 k-12 교육 과정을 제공 하는 비영리 조직입니다.  각 PLTW 클래스에서 학생 들은 실습 교육 환경의 일부로 다양 한 소프트웨어 응용 프로그램을 사용 합니다.  많은 소프트웨어 응용 프로그램에는 고속 CPU가 필요 하거나 경우에 따라 GPU가 필요 합니다.  이 문서에서는 일반적으로 성적 9-12에서 학생 들에 게 제공 되는 다음과 같은 PLTW 클래스에 대해 랩을 설정 하는 방법을 보여 줍니다.

- **엔지니어링 디자인 소개**

    학생 들은 3D 모델링에 [Autodesk 'S 배우고의 CAD (computer 's 's design)](https://www.autodesk.com/products/inventor/new-features) 소프트웨어를 사용 하는 엔지니어링 디자인 프로세스에 도입 되었습니다.

- **엔지니어링 원리**
    
    학생 들은 엔지니어링 메커니즘과 structural\material 강도 및 자동화에 대해 알아봅니다.  이 클래스는 [MD 솔리드](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/MD+Solids/MD+Solids+Software+Installation+Guide.pdf), [서쪽 다리 디자이너](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/West+Point+Bridge+Builder/Installation+Guide+for+West+Point+Bridge+Designer.pdf)및 [아메리카 병력 시뮬레이션 등의](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/America's+Army/Installation+Guide+for+Americas+Army+Simulation+17-18.pdf)소프트웨어를 사용 합니다.

- **민사 엔지니어링 및 아키텍처**

    학생 들이 [Autodesk의 Revit](https://www.autodesk.com/products/revit/overview) 아키텍처 설계 소프트웨어를 사용 하 여 Model.bim (3d 빌드 정보 모델링)를 사용 하 여 빌드 및 사이트 디자인 및 개발을 학습 하 고 있습니다.

- **컴퓨터 통합 제조**

    학습자는 로봇 공학 및 자동화를 포함 하는 최신 제조 프로세스를 탐색 합니다.   이 클래스에서 학생은 [Autodesk 'S 배우고 CAD](https://www.autodesk.com/products/inventor/new-features) 및 [AUTODESK의 배우고 CAM (컴퓨터 사용 제조업)](https://www.autodesk.com/products/inventor-cam/overview) 소프트웨어를 사용 합니다. 

- **디지털 전자 제품**

    학생 들은 [국가별 계측의 다중 sim](https://www.ni.com/en-us/shop/electronic-test-instrumentation/application-software-for-electronic-test-and-instrumentation-category/what-is-multisim.html) 시뮬레이션 및 회로 디자인 소프트웨어를 사용 하 여 전자 논리 회로 및 장치를 연구 합니다.

- **엔지니어링 디자인 및 개발**

    학생 들이 엔지니어의 패널에 표시 되는 연구, 디자인 및 테스트를 결합 하는 종단 간 솔루션에 기여 합니다.  이 클래스에서 학생 [은 Autodesk 'S 배우고 CAD](https://www.autodesk.com/products/inventor/new-features) 소프트웨어를 사용 합니다.

- **컴퓨터 과학 Essentials**

    학생 들은 계산 개념 및 도구에 도입 되었습니다.  블록 기반 프로그래밍부터 시작한 다음, [Vexcode V5 블록과](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/VEXcode+V5+Blocks/VexCode+V5+Blocks+Installation+Guide.pdf)같은 코딩 환경을 사용 하 여 텍스트 기반 코딩을 사용 하도록 이동 합니다.

- **컴퓨터 과학 원칙**
    
    학생 들이 [Microsoft의 Visual Studio Code 개발 환경을](https://code.visualstudio.com/)사용 하 여 [Python](https://www.python.org/) 으로 프로그래밍 전문 지식을 성장 하 고 있습니다. 

- **컴퓨터 과학 A**

    학생 들은 모바일 앱 개발을 학습 하 여이 클래스에 대 한 프로그래밍 전문 지식을 성장 시킵니다.  이 클래스에서는 [Microsoft의 Visual Studio Code 개발 환경을](https://code.visualstudio.com/)사용 하 여 [Java](https://www.java.com/) 를 학습 합니다.  또한 학생은 모바일 앱 코드를 실행 하 고 테스트할 수 있는 에뮬레이터를 사용 합니다.  Azure 랩에서 에뮬레이터를 설정 하는 방법에 대 한 자세한 내용은에 문의 하세요 azlabspilot@microsoft.com .

각 클래스에 대 한 [소프트웨어의 전체 목록은](https://www.pltw.org/pltw-software) pltw 사이트를 참조 하세요.

## <a name="lab-configuration"></a>랩 구성
PLTW을 위한 랩을 설정 하려면 시작 하려면 Azure 구독 및 랩 계정이 필요 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Azure 구독을 받은 후 Azure Lab Services에서 새 랩 계정을 만들 수 있습니다. 새 랩 계정을 만드는 방법에 대 한 자세한 내용은 [랩 계정을 설정 하는 방법](./tutorial-setup-lab-account.md)에 대 한 자습서를 참조 하세요. 기존 랩 계정을 사용할 수도 있습니다.

랩 계정이 있으면 학교에서 제공 하는 PLTW 클래스의 각 세션에 대해 별도의 랩을 만들어야 합니다.  또한 각 PLTW 클래스 유형에 대해 별도의 이미지를 만드는 것이 좋습니다.  실습 및 이미지를 구성 하는 방법에 대 한 자세한 내용은 블로그 게시물 ( [물리적 랩에서 Azure Lab Services로 이동](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931))을 참조 하세요.

### <a name="lab-account-settings"></a>랩 계정 설정
랩 계정에 대해 아래 표에 설명 된 설정을 사용 하도록 설정 합니다. Marketplace 이미지를 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [랩 작성자가 사용할 수 있는 marketplace 이미지를 지정 하는 방법](./specify-marketplace-images.md)에 대 한 문서를 참조 하세요.

| 랩 계정 설정 | Instructions |
| -------------------- | ----- |
| Marketplace 이미지 | 랩 계정 내에서 사용할 Windows 10 Pro 이미지를 사용 하도록 설정 합니다. |

### <a name="lab-settings"></a>랩 설정
PLTW 클래스에 사용 하는 데 권장 되는 VM의 크기는 학생이 클래스에서 수행 하는 작업의 유형에 따라 달라 집니다.  위의 클래스에서는 크고 작은 GPU (시각화) VM 크기를 사용 하는 것이 좋습니다.  PLTW 클래스에 대 한 랩을 설정 하는 경우 아래 표에 설명 된 지침을 참조 하세요.

| 랩 설정 | 값/지침 |
| ------------ | ------------------ |
|Virtual Machine 크기| **작은 GPU (시각화)**.  이 VM은 OpenGL 및 DirectX와 같은 프레임 워크를 사용 하는 원격 시각화, 스트리밍, 게임, 인코딩에 가장 적합 합니다.  이 크기를 사용 하는 것이 좋습니다. 민사 공학 및 아키텍처, 디지털 전자 제품, 컴퓨터 통합 제조, 및 엔지니어링 디자인 및 개발
|Virtual Machine 크기| **큼**.  이 크기는 더 빠른 CPU, 향상된 로컬 디스크 성능, 큰 데이터베이스, 큰 메모리 캐시가 필요한 애플리케이션에 가장 적합합니다.  이 크기를 사용 하는 것이 좋습니다. 엔지니어링 디자인 소개, 엔지니어링 원칙, 컴퓨터 과학 Essentials, 컴퓨터 과학 원리 및 컴퓨터 과학 A.

### <a name="licensing-server"></a>라이선스 서버
위의 PLTW 클래스에서 사용 되는 대부분의 소프트웨어에는 라이선스 서버에 대 한 액세스 권한이 필요 *_하지_* 않습니다.  그러나 다음 소프트웨어에 대해 Autodesk의 네트워크 라이선스 모델을 사용 하려는 경우 라이선스 서버에 액세스 해야 합니다.
-   Revit
-   배우고
-   배우고 CAM

Autodesk의 소프트웨어에서 네트워크 라이선스를 사용 하려면 [Pltw](https://www.pltw.org/pltw-software) 에서 라이선스 서버에 Autodesk의 라이선스 관리자를 설치 하는 자세한 단계를 제공 합니다.  이 라이선스 서버는 일반적으로 온-프레미스 네트워크에 있거나 Azure VNet (가상 네트워크) 내에 있는 Azure VM (가상 머신)에서 호스팅됩니다.

라이선스 서버를 설정한 후에는 VNet을 [랩 계정](./tutorial-setup-lab-account.md)으로 [피어](./how-to-connect-peer-virtual-network.md) 링 해야 합니다. 랩 가상 머신에서 라이선스 서버에 액세스할 수 있도록 네트워크 피어 링을 _before * 랩을 만들고 그 밖의 다른 방법을 수행 해야 합니다.

Autodesk의 생성 된 라이선스 파일은 라이선스 서버의 MAC 주소를 포함 합니다.  Azure VM을 사용 하 여 라이선스 서버를 호스팅하려면 라이선스 서버의 MAC 주소가 변경 되지 않는지 확인 하는 것이 중요 합니다.   그렇지 않으면 MAC 주소가 변경 될 때 라이선스 파일을 다시 생성 해야 합니다.  MAC 주소가 변경 되는 것을 방지 하려면 다음 팁을 따르세요.

- 라이선스 서버를 호스트 하는 Azure VM에 대 한 [정적 개인 IP 및 MAC 주소를 설정](./how-to-create-a-lab-with-shared-resource.md#static-private-ip-and-mac-address) 합니다.
- 나중에 이러한 리소스를 새 지역으로 이동 하지 않아도 되도록 충분 한 VM 용량이 있는 지역에서 랩 계정 및 라이선스 서버 VNet을 모두 설정 했는지 확인 합니다.

또한 자세한 내용은 [공유 리소스로 라이선스 서버를 설정 하는 방법](./how-to-create-a-lab-with-shared-resource.md) 에 대 한 문서를 참조 하세요.

### <a name="template-machine"></a>템플릿 컴퓨터
PLTW에 필요한 설치 파일 중 일부는 크고 랩의 템플릿 컴퓨터에 다운로드 하는 경우 복사 하는 데 시간이 오래 걸립니다.

설치 파일을 템플릿 컴퓨터에 다운로드 하 고 모든 항목을 설치 하는 대신 물리적 환경에서 PLTW 이미지를 만드는 것이 좋습니다.  그런 다음 사용자 지정 이미지를 사용 하 여 랩을 만들 수 있도록 이미지를 공유 이미지 갤러리로 가져올 수 있습니다.  자세한 내용은 [사용자 지정 이미지를 공유 이미지 갤러리에 업로드](./upload-custom-image-shared-image-gallery.md)문서를 참조 하세요.

이 권장 사항을 따르면 랩을 설정 하는 주요 작업은 다음과 같습니다.

1. 실제 환경에서 클래스에 대 한 이미지를 만듭니다.

    a.  설치 파일을 다운로드 하 고 필요한 소프트웨어를 설치 하는 방법에 대 한 자세한 단계를 사용 합니다.

    > [!NOTE]    
    > Autodesk의 응용 프로그램을 설치 하는 경우 Autodesk on을 설치 하는 컴퓨터가 라이선스 서버와 통신할 수 있어야 합니다 (Autodesk의 설치 마법사는 라이선스 서버가 호스트 되는 컴퓨터의 컴퓨터 이름을 지정 하 라는 메시지를 표시 합니다).  Azure VM에서 라이선스 서버를 호스트 하는 경우 Autodesk의 설치 마법사가 라이선스 서버에 액세스할 수 있도록 랩의 템플릿 컴퓨터에 Autodesk를 설치 하기까지 기다려야 할 수 있습니다.

    b.  OneDrive (또는 학교에서 사용할 수 있는 다른 백업 옵션)를 [설치 하 고 구성](./how-to-prepare-windows-template.md#install-and-configure-onedrive) 합니다.
    
    다.  [Windows 업데이트를 설치 하 고 구성](./how-to-prepare-windows-template.md#install-and-configure-updates)합니다.

1.  [랩 계정에 연결 된 공유 이미지 갤러리](./how-to-attach-detach-shared-image-gallery.md)에 사용자 지정 이미지를 업로드 합니다.

1.  랩을 만들고 이전 단계에서 업로드 한 사용자 지정 이미지를 선택 합니다.

1.  랩을 만든 후를 시작 하 고 템플릿 컴퓨터에 연결 하 여 이미지가 예상 대로 작동 하는지 확인 합니다.

1.  마지막으로 템플릿 컴퓨터를 게시 하 여 학생의 Vm을 만듭니다.

## <a name="student-devices"></a>학생 장치
학생은 Windows\Mac 컴퓨터 및 Chromebooks에서 랩 Vm에 연결할 수 있습니다.  다음은 이러한 각 옵션에 대 한 지침에 대 한 링크입니다.

- [Windows에서 연결](./how-to-use-classroom-lab.md#connect-to-the-vm)
- [Mac에서 연결](./connect-virtual-machine-mac-remote-desktop.md)
- [Chromebook에서 연결](./connect-virtual-machine-chromebook-remote-desktop.md)

## <a name="cost"></a>비용
위의 PLTW 클래스에 대해 가능한 예상 비용을 살펴보겠습니다.  이러한 예상치는 라이선스 서버를 실행 하거나 공유 이미지 갤러리를 사용 하는 비용을 포함 하지 않습니다.  25 명의 학생 클래스를 사용 합니다.  예약 된 클래스 시간은 20 시간입니다.  또한 각 학생은 예약 된 클래스 시간 외에도 과제 또는 할당에 대해 10 시간 할당량을 얻습니다.  **크고** **작은 GPU (시각화)** 크기에 대 한 아래 비용 예상을 참조 하세요.

- **대량 VM**

    25 개 학생 x (20 개의 예약 된 시간 + 10 할당량 시간) x 70 랩 단위 x 0.01 USD/시간 = 525.00 USD

- **소형 GPU(시각화)**

    25 개 학생 x (20 개의 예약 된 시간 + 10 할당량 시간) x 160 랩 단위 x 0.01 USD/시간 = 1200.00 USD

> [!IMPORTANT] 
> 예상 비용은 예를 들어 목적 으로만 사용 됩니다.  가격 책정에 대 한 최신 정보는 [Azure Lab Services 가격 책정](https://azure.microsoft.com/pricing/details/lab-services/)을 참조 하세요.

> [!NOTE] 
> 많은 PLTW 클래스는 MIT App 배우고와 같은 브라우저를 통해 액세스 하는 응용 프로그램을 사용 합니다.  이러한 브라우저 기반 응용 프로그램에는 빠른 CPU 또는 GPU가 필요 하지 않으며 인터넷에 연결 된 모든 장치에서 액세스할 수 있습니다.  학생 들이 이러한 유형의 응용 프로그램을 사용 하는 경우 랩 Vm에서 브라우저를 사용 하는 대신 실제 장치에서 브라우저를 사용 하는 것이 좋습니다.  이렇게 하면 빠른 CPU 또는 GPU가 필요한 응용 프로그램에 대 한 랩 Vm만 사용 하 여 비용을 줄일 수 있습니다.

## <a name="next-steps"></a>다음 단계
다음 단계는 모든 랩 설정에 공통됩니다.

- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [이메일로 학생에게 등록 링크 보내기](how-to-configure-student-usage.md#send-invitations-to-users)