---
title: Python 및 Jupyter 노트북을 사용 하 여 데이터 과학을 교육 하는 랩 설정 | Microsoft Docs
description: Python 및 Jupyter 노트북을 사용 하 여 데이터 과학을 학습 하도록 랩을 설정 하는 방법을 알아봅니다.
author: emaher
ms.topic: article
ms.date: 09/29/2020
ms.author: enewman
ms.openlocfilehash: 4bbf4c9d4bc83b48b8ecc62946fa9bffa8af50bc
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91533523"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>Python 및 Jupyter 노트북을 사용 하 여 데이터 과학을 교육 하는 랩 설정
이 문서에서는 학생 들에 게 [Jupyter 노트북](http://jupyter-notebook.readthedocs.io/)을 사용 하는 방법을 학습 하는 데 필요한 도구를 사용 하 여 랩 서비스에서 템플릿 vm (가상 머신)을 설정 하는 방법 및 학생이 vm (가상 머신)에서 노트북에 연결 하는 방법을 설명 합니다.

Jupyter Notebook은 서식 있는 텍스트와 실행 가능한 Python 원본 코드를 노트북이라는 단일 캔버스에 쉽게 결합할 수 있는 오픈 소스 프로젝트입니다. 노트북을 실행하면 입력 및 출력의 선형 레코드가 생성됩니다. 이러한 출력에는 텍스트, 정보 테이블, 분산형 플롯 등이 포함될 수 있습니다.

## <a name="set-up-the-lab"></a>랩 설정

### <a name="lab-configuration"></a>랩 구성
이 랩을 설정 하려면 Azure 구독 및 랩 계정에 액세스 해야 합니다. 조직의 관리자와 논의 하 여 기존 Azure 구독에 대 한 액세스 권한을 얻을 수 있는지 확인 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

Azure 구독이 있으면 자습서: [랩 계정 설정](tutorial-setup-lab-account.md)의 지침에 따라 Azure Lab Services에서 새 랩 계정을 만듭니다. 기존 랩 계정을 사용할 수도 있습니다.

### <a name="lab-account-settings"></a>랩 계정 설정
랩 계정에 대해 아래 표에 설명 된 설정을 사용 하도록 설정 합니다. Marketplace 이미지를 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [랩 작성자가 사용할 수 있는 marketplace 이미지 지정](specify-marketplace-images.md)을 참조 하세요.

| 랩 계정 설정 | Instructions |
| ------------------- | ------------ |
| Marketplace 이미지 | 랩 계정 내에서 운영 체제 요구 사항에 따라 Azure Marketplace 이미지 중 하나를 사용 하도록 설정 합니다. <br/><ul><li>Data Science Virtual Machine – Windows Server 2019</li><li>Data Science Virtual Machine – Ubuntu 18.04</li></ul> |

> [!NOTE]
> 이 문서에서는 Azure marketplace에서 사용할 수 있는 데이터 과학 가상 머신 이미지를 Jupyter Notebook으로 미리 구성 되어 사용 합니다. 그러나 이러한 이미지에는 데이터 과학을 위한 다양 한 개발 및 모델링 도구도 포함 되어 있습니다. 이러한 추가 도구를 원하지 않고 Jupyter 노트북을 사용 하는 경량 설치를 원하는 경우 사용자 지정 VM 이미지를 만듭니다. 예를 들어 [Azure에 JupyperHub를 설치](http://tljh.jupyter.org/en/latest/install/azure.html)합니다. 사용자 지정 이미지를 만든 후에는 공유 이미지 갤러리에 업로드 하 여 Azure Lab Services 내에서 이미지를 사용할 수 있습니다. [Azure Lab Services에서 공유 이미지 갤러리 사용](how-to-attach-detach-shared-image-gallery.md)에 대해 자세히 알아보세요. 

### <a name="lab-settings"></a>랩 설정
클래스 룸 랩을 설정할 때 다음 표와 같이 **가상 컴퓨터 크기** 및 **가상 컴퓨터 이미지** 설정을 구성 합니다. 클래스 룸 랩을 만드는 방법에 대 한 지침은 [교실 랩 설정](tutorial-setup-classroom-lab.md)을 참조 하세요.

| 랩 설정 | 값/지침 |
| ------------ | ------------------ | 
| 가상 머신 크기 | <p>여기에서 선택 하는 크기는 실행 하려는 워크 로드에 따라 달라 집니다.</p><ul><li>Small 또는 Medium – Jupyter 노트북에 대 한 기본 설정에 적합</li><li>소형 GPU (계산) – 인공 지능 및 심층 학습 같은 계산 집약적 및 네트워크 집약적 응용 프로그램에 가장 적합 합니다.</li></ul> | 
| 가상 컴퓨터 이미지 | <p>운영 체제 요구 사항에 따라 다음 이미지 중 하나를 선택 합니다.</p><ul><li>[Data Science Virtual Machine – Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019)</li><li>[Data Science Virtual Machine – Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview)</li></ul> |


### <a name="template-virtual-machine"></a>템플릿 가상 머신
랩을 만든 후에는 선택한 가상 머신 크기 및 이미지를 기준으로 템플릿 VM이 생성 됩니다. 이 클래스에 대해 학생 들에 게 제공 하려는 모든 항목을 사용 하 여 템플릿 VM을 구성 합니다. 자세히 알아보려면 [템플릿 가상 머신을 관리 하는 방법](how-to-create-manage-template.md)을 참조 하세요. 

기본적으로 Data Science VM 이미지는 이러한 종류의 클래스에 필요한 많은 데이터 과학 프레임 워크 및 도구와 함께 제공 됩니다. 예를 들어 이미지는 다음과 같습니다.

- [Jupyter 노트북](http://jupyter-notebook.readthedocs.io/): 데이터 과학자에서 원시 데이터를 가져오고, 계산을 실행 하 고, 동일한 환경에서 결과를 모두 볼 수 있도록 하는 웹 응용 프로그램입니다. 템플릿 VM에서 로컬로 실행 됩니다.  
- [Visual Studio Code](https://code.visualstudio.com/): 노트북을 작성 하 고 테스트할 때 풍부한 대화형 환경을 제공 하는 IDE (통합 개발 환경)입니다. 자세한 내용은 [Visual Studio Code에서 Jupyter 노트북 작업](https://code.visualstudio.com/docs/python/jupyter-support)을 참조 하세요.

### <a name="provide-notebooks-for-the-class"></a>클래스에 대 한 노트북 제공
다음 작업은 학생에 게 사용할 노트북을 제공 하는 것입니다. 자신의 노트북을 제공 하기 위해 템플릿 VM에서 로컬로 노트북을 저장할 수 있습니다. 

Azure Machine Learning에서 샘플 노트북을 사용 하려는 경우 [Jupyter 노트북을 사용 하 여 환경을 구성 하는 방법](../machine-learning/how-to-configure-environment.md#jupyter)을 참조 하세요. 

### <a name="optional-enable-graphical-desktop-for-linux"></a>선택 사항: Linux 용 그래픽 데스크톱 사용 
**Data Science Virtual Machine – Ubuntu** 이미지가 이미 X2GO 서버와 함께 프로 비전 되었으며 클라이언트 연결을 수락할 준비가 되었습니다. 템플릿 VM을 설정할 때는 추가 단계가 필요 하지 않습니다. 

### <a name="publish-the-template-machine"></a>템플릿 컴퓨터 게시
템플릿을 게시할 때 랩에 등록 된 각 학생은 설정 된 모든 로컬 도구 및 노트북을 사용 하 여 템플릿 VM의 복사본을 가져옵니다.

## <a name="how-students-connect-to-jupyter-notebooks"></a>학생이 Jupyter 노트북에 연결 하는 방법
템플릿을 게시 하면 각 학생이 Jupyter 노트북을 비롯 하 여 클래스에 대해 미리 구성한 모든 것과 함께 제공 되는 VM에 액세스할 수 있습니다. 다음 섹션에서는 학생이 Jupyter 노트북에 연결 하는 다양 한 방법을 보여 줍니다. 

### <a name="for-windows-vms"></a>Windows VM의 경우
Windows Vm에서 학생을 제공한 경우 Vm에 연결 하 고 로컬에서 사용할 수 있는 Jupyter 노트북을 사용 해야 합니다. 

Windows VM에 연결 하기 위해 학생이 RDP (원격 데스크톱 연결)를 사용할 수 있습니다. 자세한 단계는 [교실 랩에 액세스 하는 방법](how-to-use-classroom-lab.md)을 참조 하세요. 

Mac 또는 Chromebook를 사용 하는 학생은 다음 문서의 지침에 따라 데이터 과학 Windows VM에 연결할 수 있습니다. 

- [Mac에서 RDP를 사용하여 VM에 연결](connect-virtual-machine-mac-remote-desktop.md)
- [에서 RDP를 사용하여 VM에 연결](connect-virtual-machine-chromebook-remote-desktop.md)

### <a name="for-linux-vms"></a>Linux VM의 경우
학생에 게 Linux Vm을 제공한 경우 학생 들이 Vm에서 Jupyter 노트북에 연결 하는 데 사용할 수 있는 몇 가지 옵션이 있습니다.

- VM에 연결한 후 로컬에서 Jupyter 노트북에 액세스
    - 터미널 세션에 대 한 VM에 대 한 SSH
     - 그래픽 세션용 VM에 대 한 X2Go 연결
- SSH 터널링을 사용 하 여 학생의 로컬 컴퓨터에서 VM의 Jupyter 서버에 직접 연결 합니다. 

다음 섹션에서는 Jupyter 노트북에 연결 하는 이러한 방법에 대 한 세부 정보를 제공 합니다. 

#### <a name="ssh-to-virtual-machine"></a>가상 컴퓨터에 대 한 SSH
학생은 터미널 세션에서 SSH를 통해 Linux Vm에 연결할 수 있습니다. 자세한 단계는 [교실 랩에 액세스 하는 방법](how-to-use-classroom-lab.md)을 참조 하세요. Windows 클라이언트 컴퓨터를 사용 하는 경우 [PuTTY](https://www.putty.org/) 를 다운로드 하거나 명령 프롬프트에서 [windows의 OpenSSH](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse) 를 ssh로 사용 하도록 설정 하 여 ssh 클라이언트를 사용 하도록 설정 해야 합니다. 

1.  VM을 시작합니다.
2.  VM이 실행 되 면 **연결**을 클릭 합니다. 그러면 SSH 명령 문자열을 제공 하는 대화 상자가 표시 되 고 다음 샘플과 같이 표시 됩니다.
    
     ```shell
    ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```
3.  명령 프롬프트 또는 터미널로 이동 하 여이 명령에 붙여넣은 다음 **enter**키를 누릅니다.
4.  VM에 로그인 하려면 암호를 입력 하세요. 

학생이 Vm에 연결 되 면 로컬에서 Jupyter 노트북에 액세스 하 여 실행할 수 있습니다.

#### <a name="x2go-to-virtual-machine"></a>가상 머신으로 X2Go
**Data Science Virtual Machine – Ubuntu** 이미지가 이미 X2GO 서버와 함께 프로 비전 되었으며 클라이언트 연결을 수락할 준비가 되었습니다. Linux 컴퓨터의 그래픽 데스크톱에 연결 하려면 학습자는 이러한 일회성 단계를 수행 하 여 클라이언트 컴퓨터에 X2Go를 설정 해야 합니다.

1.  클라이언트 플랫폼용 [X2Go 클라이언트](https://wiki.x2go.org/doku.php/doc:installation:x2goclient) 를 다운로드 하 여 설치 합니다.
2.  [Azure Lab Services 포털](https://labs.azure.com)에서 연결 하려는 Linux VM이 시작 되었는지 확인 합니다.
3.  VM이 실행 되 면 **연결**을 클릭 합니다. 그러면 SSH 명령 문자열을 제공 하는 대화 상자가 표시 됩니다 .이 대화 상자에는 다음 샘플과 같이 표시 됩니다.

    ```
     ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```    
4. 이 정보가 있으면 X2Go client 앱을 열고 새 세션을 만듭니다. 
5.  **세션 기본 설정** 창에서 다음 값을 입력 합니다.
    - **세션 이름**: 원하는 대로 지정할 수 있지만 랩 VM의 이름을 사용 하는 것이 좋습니다.
     - **호스트**: `ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com`
     - **로그인**: 학생
     - **SSH 포트**: 12345
     - **세션 유형**: xfce
6. **확인**을 선택합니다. 

    > [!NOTE]
     > 새 X2Go 세션을 만들 때 RDP 포트가 **아닌** SSH 포트를 사용 해야 합니다.

이제 VM에 연결 하려면 다음 단계를 수행 합니다.    

1.  X2Go client에서 연결 하려는 VM을 두 번 클릭 합니다. 

    ![X2Go 클라이언트](./media/class-type-jupyter-notebook/x2go-client.png)
2. VM에 연결 하기 위한 암호를 입력 합니다. (연결을 완료 하려면 방화벽을 우회 하려면 X2Go 권한을 부여 해야 할 수도 있습니다.)
3.  이제 Ubuntu Data Science VM에 대 한 그래픽 인터페이스가 표시 됩니다.


#### <a name="ssh-tunnel-to-jupyter-server-on-the-vm"></a>VM의 Jupyter 서버에 대 한 SSH 터널
일부 학생이 자신의 로컬 컴퓨터에서 Vm 내부의 Jupyter 서버에 직접 연결 하는 것이 좋습니다. SSH 프로토콜을 사용 하면 서버의 특정 포트에서 실행 되는 응용 프로그램이 로컬 컴퓨터의 매핑 포트로 **터널링** 되도록 로컬 컴퓨터와 원격 서버 (이 경우에는 학생의 랩 VM) 사이에서 포트를 전달할 수 있습니다. 학생은 다음 단계를 수행 하 여 랩 Vm에서 Jupyter 서버에 대 한 SSH 터널을 수행 해야 합니다.

1.  [Azure Lab Services 포털](https://labs.azure.com)에서 연결 하려는 Linux VM이 시작 되었는지 확인 합니다.
2.  VM이 실행 되 면 **연결**을 클릭 합니다. 그러면 SSH 명령 문자열을 제공 하는 대화 상자가 표시 됩니다 .이 대화 상자에는 다음 문자열과 같이 표시 됩니다.

    ```bash
     ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```
3. 로컬 컴퓨터에서 터미널 또는 명령 프롬프트를 시작 하 고 SSH 연결 문자열을 여기에 복사 합니다. 그런 다음를 `-L 8888:localhost:8888` 명령 문자열에 추가 합니다. 그러면 포트 간에 **터널이** 생성 됩니다. 최종 문자열은 다음과 같습니다.

    ```bash
     ssh –L 8888:localhost:8888 -p 12345 student@ml-lab-b720853e-570f-49ac-9cb2-bd0bd2aeec35.eastus.cloudapp.azure.com
     ```
4. **Enter** 키를 눌러 명령을 실행 합니다. 
5.  메시지가 표시 되 면 랩 VM에 연결 하기 위한 암호를 제공 합니다. 
6.  VM에 연결 되 면 다음 명령을 사용 하 여 Jupyter 서버를 시작 합니다. 

    ```bash
     jupyter notebook
     ```
7. 명령을 실행 하면 터미널 또는 명령 프롬프트에 URL이 제공 됩니다. URL은 다음과 같습니다.

    ```bash
     http://localhost:8888/?token=8c09ecfc93e6a8cbedf9c66dffdae19670a64acc1d37
     ```
8. Jupyter Notebook에 연결 하 여 작업할 수 있도록이 URL을 로컬 컴퓨터의 브라우저에 붙여 넣습니다. 

    > [!NOTE]
    > Visual Studio Code를 사용 하면 뛰어난 [Jupyter Notebook 편집 환경을](https://code.visualstudio.com/docs/python/jupyter-support)사용할 수도 있습니다. [원격 Jupyter 서버에 연결 하는 방법](https://code.visualstudio.com/docs/python/jupyter-support#_connect-to-a-remote-jupyter-server) 에 대 한 지침을 따르고 이전 단계와 동일한 URL을 사용 하 여 브라우저 대신 VS Code에서 연결할 수 있습니다. 


## <a name="cost-estimate"></a>예상 비용
이 클래스에 대해 가능한 예상 비용을 살펴보겠습니다. 25 명의 학생 클래스를 사용 합니다. 예약 된 클래스 시간은 20 시간입니다. 또한 각 학생은 예약 된 클래스 시간 외에도 과제 또는 배정에 대해 10 시간 할당량을 얻습니다. 선택한 VM 크기는 작은 GPU (계산) 이며 랩 단위는 139입니다. 작은 (랩 단위 20) 또는 중간 크기 (42 랩 단위)를 사용 하려는 경우 아래 수식의 랩 단위 부분을 올바른 숫자로 바꿀 수 있습니다.  

이 클래스에 대 한 가능한 비용 예상 예는 다음과 같습니다. 25 학생 * (20 개의 예약 된 시간 + 10 할당량 시간) * 139 랩 단위 * 0.01 USD/시간 = 1042.5 USD

가격 책정에 대한 자세한 내용은 [Azure Lab Services 가격 책정](https://azure.microsoft.com/pricing/details/lab-services/)을 참조 하세요.

## <a name="conclusion"></a>결론
이 문서에서는 Jupyter 노트북 클래스의 랩을 만드는 단계를 살펴보았습니다. 다른 기계 학습 클래스에 대해 유사한 설정을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 단계는 랩을 설정 하는 데 일반적입니다.

- [템플릿 만들기 및 관리](how-to-create-manage-template.md)
- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [학생에 대 한 전자 메일 등록 링크](how-to-configure-student-usage.md#send-invitations-to-users)
