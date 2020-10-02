---
title: Azure Lab Services를 사용 하 여 윤리적 해킹 랩 설정 Microsoft Docs
description: Azure Lab Services를 사용 하 여 랩을 설정 하는 방법에 대해 알아봅니다. 윤리적 해킹을 학습 합니다.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: ae0d57223edb68d1bed4ad64a005dd33da019dd0
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631684"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class"></a>윤리적 해킹 클래스를 학습 하기 위한 랩 설정 
이 문서에서는 윤리적 해킹의 법적 고 지에 초점을 맞춘 클래스를 설정 하는 방법을 보여 줍니다. 윤리적 해킹 커뮤니티에서 사용하는 방법인 침투 테스트는 누군가가 악의적인 공격자가 악용할 수 있는 취약성을 입증하기 위해 시스템 또는 네트워크에 대한 액세스 권한을 얻으려고 할 때 발생합니다. 

윤리적 해킹 클래스에서 학생은 취약점을 방어하기 위한 최신 기술을 배울 수 있습니다. 각 학생에게는 두 개의 중첩된 가상 머신이 있는 Windows Server 호스트 가상 머신이 있습니다. 즉 [Metasploitable3](https://github.com/rapid7/metasploitable3) 이미지가 있는 가상 머신과 [Kali Linux](https://www.kali.org/) 이미지가 있는 가상 머신이 있습니다. Metasploitable 가상 머신은 악용 목적을 위해 사용되며 Kali 가상 머신은 법정 작업을 실행하는 데 필요한 도구에 대한 액세스 권한을 제공합니다.

이 문서에는 두 가지 주요 섹션이 있습니다. 첫 번째 섹션에서는 교실 랩을 만드는 방법을 설명 합니다. 두 번째 섹션에서는 중첩 된 가상화를 사용 하도록 설정 하 고 필요한 도구 및 이미지를 사용 하 여 템플릿 컴퓨터를 만드는 방법을 설명 합니다. 이 경우 Hyper-v를 사용 하 여 이미지를 호스트 하는 컴퓨터의 Metasploitable 이미지와 Kali Linux 이미지가 있습니다.

## <a name="lab-configuration"></a>랩 구성
이 랩을 설정하려면 Azure 구독을 시작해야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Azure 구독을 받은 후 Azure Lab Services에서 새 랩 계정을 만들거나 기존 계정을 사용할 수 있습니다. 새 랩 계정 만들기에 대 한 자습서: [랩 계정 설정 자습서](tutorial-setup-lab-account.md)를 참조 하세요.

[이 자습서](tutorial-setup-classroom-lab.md) 에 따라 새 랩을 만들고 다음 설정을 적용 합니다.

| 가상 머신 크기 | 이미지 |
| -------------------- | ----- | 
| 중간 (중첩 된 가상화) | Windows Server 2019 Datacenter |

## <a name="template-machine"></a>템플릿 컴퓨터 

템플릿 컴퓨터를 만든 후 컴퓨터를 시작 하 고 연결 하 여 다음 세 가지 주요 작업을 완료 합니다. 
 
1. 중첩 된 가상화를 위해 컴퓨터를 설정 합니다. Hyper-v와 같은 적절 한 windows 기능을 모두 사용 하도록 설정 하 고, 서로 통신할 수 있도록 Hyper-v 이미지에 대 한 네트워킹을 설정 합니다.
2. [Kali](https://www.kali.org/) Linux 이미지를 설정 합니다. Kali는 침투 테스트 및 보안 감사에 대 한 도구를 포함 하는 Linux 배포판입니다.
3. Metasploitable 이미지를 설정 합니다. 이 예에서는 [Metasploitable3](https://github.com/rapid7/metasploitable3) 이미지가 사용 됩니다. 이 이미지는 의도적으로 보안 취약성을 갖도록 생성 됩니다.

위에 설명 된 작업을 자동화 하는 스크립트는 [Lab Services 윤리적 해킹 스크립트](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/EthicalHacking)에서 사용할 수 있습니다.

### <a name="prepare-template-machine-for-nested-virtualization"></a>중첩 된 가상화를 위한 템플릿 시스템 준비
중첩 된 가상화를 위해 템플릿 가상 머신을 준비 하려면 [이 문서의](how-to-enable-nested-virtualization-template-vm.md) 지침을 따르세요. 

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>Kali Linux 이미지를 사용 하 여 중첩 된 가상 머신 설정
Kali는 침투 테스트 및 보안 감사에 대 한 도구를 포함 하는 Linux 배포판입니다.

1. 에서 이미지를 다운로드 [https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/) 합니다.  
    1. Hyper-v 용 **Kali Linux hyper-v 64 비트** 를 다운로드 합니다.
    1. 7z 파일의 압축을 풉니다.  아직 7 개의 zip이 없으면에서 다운로드 [https://www.7-zip.org/download.html](https://www.7-zip.org/download.html) 합니다. 압축을 푼 폴더의 위치는 나중에 필요 합니다.
2. 관리 도구에서 **Hyper-v 관리자** 를 엽니다.
1. **작업**을 선택한 다음, **가상 컴퓨터 가져오기**를 선택 합니다. 
1. **가상 컴퓨터 가져오기** 마법사의 **폴더 찾기** 페이지에서 Kali Linux 이미지를 포함 하는 추출 된 폴더의 위치를 선택 합니다.

    ![폴더 찾기 대화 상자](./media/class-type-ethical-hacking/locate-folder.png)
1. **가상 컴퓨터 선택** 페이지에서 Kali Linux 이미지를 선택 합니다.  이 경우 이미지는 **kali-2019.3-hyperv**입니다.

    ![Kali 이미지 선택](./media/class-type-ethical-hacking/select-kali-image.png)
1.  **가져오기 유형 선택** 페이지에서 **가상 머신 복사 (새로운 고유 ID 만들기)** 를 선택 합니다.

    ![가져오기 유형 선택](./media/class-type-ethical-hacking/choose-import-type.png)
1. **가상 컴퓨터 파일용 폴더 선택** 및 **가상 하드 디스크를 저장할 폴더 선택** 페이지에서 기본 설정을 적용 하 고 **다음**을 선택 합니다.
1. **네트워크 연결** 페이지에서이 문서의 **중첩 된 가상화를 위한 템플릿 준비** 섹션에서 이전에 만든 **LabServicesSwitch** 를 선택한 후 **다음**을 선택 합니다.

    ![네트워크 연결 페이지](./media/class-type-ethical-hacking/connect-network.png)
1. **요약** 페이지에서 **마침** 을 선택 합니다. 복사 및 가져오기 작업이 완료 될 때까지 기다립니다. 이제 Kali Linux 가상 머신이 Hyper-v에서 제공 될 예정입니다.
1. **Hyper-v 관리자**에서 **작업**  ->  **시작**을 선택한 다음 **작업**  ->  **연결** 을 선택 하 여 가상 컴퓨터에 연결 합니다.  
12. 기본 사용자 이름은 `root`이며 암호는 `toor`입니다. 

    > [!NOTE]
    > 이미지 잠금을 해제 해야 하는 경우 CTRL 키를 누르고 마우스를 위로 끕니다.

## <a name="set-up-a-nested-vm-with-metasploitable-image"></a>Metasploitable 이미지를 사용 하 여 중첩 된 VM 설정  
Rapid7 Metasploitable 이미지는 보안 취약성으로 의도적으로 구성 된 이미지입니다. 이 이미지를 사용 하 여 문제를 테스트 하 고 확인 합니다. 다음 지침에서는 미리 만든 Metasploitable 이미지를 사용 하는 방법을 보여 줍니다. 그러나 최신 버전의 Metasploitable 이미지가 필요한 경우를 참조 [https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3) 하세요.

1. Metasploitable 이미지를 다운로드 합니다.
    1. [https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html)으로 이동합니다. 양식을 작성 하 여 이미지를 다운로드 하 고 **제출** 단추를 선택 합니다.
    2. **Metasploitable 지금 다운로드** 단추를 선택 합니다.
    3. Zip 파일이 다운로드 되 면 zip 파일의 압축을 풀고 Metasploitable. .vmdk 파일의 위치를 저장 합니다.
1. Hyper-v에서 vhdx 파일을 사용할 수 있도록 추출 된 .vmdk 파일을 vhdx 파일로 변환 합니다. VMware 이미지를 Hyper-v 이미지로 변환 하거나 그 반대로 변환 하는 데 사용할 수 있는 몇 가지 도구가 있습니다.  [Starwind V2V 변환기](https://www.starwindsoftware.com/starwind-v2v-converter)를 사용 합니다.  다운로드 하려면 [Starwind V2V 변환기 다운로드 페이지](https://www.starwindsoftware.com/starwind-v2v-converter#download)를 참조 하세요.
    1. **Starwind V2V 변환기**를 시작 합니다.
    1. **변환할 이미지의 위치 선택** 페이지에서 **로컬 파일**을 선택 합니다.  **다음**을 선택합니다.
    1. **원본 이미지** 페이지에서로 이동 하 고 **파일 이름** 설정에 대해 이전 단계에서 추출한 .vmdk Metasploitable를 선택 합니다.  **다음**을 선택합니다.
    1. **대상 이미지 위치 선택**에서 **로컬 파일**을 선택 합니다.  **다음**을 선택합니다.
    1. **대상 이미지 형식 선택** 페이지에서 **VHD/VHDX**를 선택 합니다.  **다음**을 선택합니다.
    1. **VHD/vhdx 이미지 형식에 대 한 옵션 선택** 페이지에서 **VHDX growable 이미지**를 선택 합니다.  **다음**을 선택합니다.
    1. **대상 파일 이름 선택** 페이지에서 기본 파일 이름을 그대로 적용 합니다.  **변환**을 선택합니다.
    1. **변환** 페이지에서 이미지가 변환 될 때까지 기다립니다.  이 작업은 몇 분 정도 걸릴 수 있습니다.  변환이 완료 되 면 **마침** 을 선택 합니다.
1. 새 Hyper-v 가상 컴퓨터를 만듭니다.
    1. **Hyper-v 관리자**를 엽니다.
    1. **작업**  ->  **새**  ->  **가상 컴퓨터**를 선택 합니다.
    1. **새 가상 컴퓨터 마법사**의 **시작 하기 전** 페이지에서 **다음**을 선택 합니다.
    1. **이름 및 위치 지정** 페이지에서 **이름**에 **Metasploitable** 를 입력 하 고 **다음**을 선택 합니다.

        ![새 VM 이미지 마법사](./media/class-type-ethical-hacking/new-vm-wizard-1.png)
    1. **세대 지정** 페이지에서 기본값을 그대로 적용 하 고 **다음**을 선택 합니다.
    1. **메모리 할당** 페이지에서 **시작 메모리**에 대해 **512** 을 입력 하 고 **다음**을 선택 합니다. 

        ![메모리 할당 페이지](./media/class-type-ethical-hacking/assign-memory-page.png)
    1. **네트워킹 구성** 페이지에서 연결을 **연결 되지 않음**으로 유지 합니다. 네트워크 어댑터는 나중에 설정 합니다.
    1. **가상 하드 디스크 연결** 페이지에서 **기존 가상 하드 디스크 사용**을 선택 합니다. 이전 단계에서 만든 **metasploitable** 파일의 위치로 이동 하 고 **다음**을 선택 합니다. 

        ![가상 네트워크 디스크 연결 페이지](./media/class-type-ethical-hacking/connect-virtual-network-disk.png)
    1. **새 가상 컴퓨터 마법사 완료** 페이지에서 **마침**을 선택 합니다.
    1. 가상 머신을 만든 후에는 Hyper-v 관리자에서 가상 머신을 선택 합니다. 아직 컴퓨터를 켜지 마세요.  
    1. **작업**  ->  **설정**을 선택 합니다.
    1. **Metasploitable에** 대 한 설정 대화 상자에서 **하드웨어 추가**를 선택 합니다. 
    1. **레거시 네트워크 어댑터**를 선택 하 고 **추가**를 선택 합니다.

        ![네트워크 어댑터 페이지](./media/class-type-ethical-hacking/network-adapter-page.png)
    1. **레거시 네트워크 어댑터** 페이지에서 **가상 스위치** 설정에 대해 **LabServicesSwitch** 를 선택 하 고 **확인**을 선택 합니다. LabServicesSwitch는 **중첩 된 가상화를 위한 템플릿 준비** 섹션에서 hyper-v에 대 한 템플릿 컴퓨터를 준비할 때 생성 되었습니다.

        ![레거시 네트워크 어댑터 페이지](./media/class-type-ethical-hacking/legacy-network-adapter-page.png)
    1. 이제 Metasploitable 이미지를 사용할 준비가 되었습니다. **Hyper-v 관리자**에서 **작업**  ->  **시작**을 선택한 다음 **작업**  ->  **연결** 을 선택 하 여 가상 컴퓨터에 연결 합니다.  기본 사용자 이름은 **msfadmin** 이 고 암호는 **msfadmin**입니다. 


이제 템플릿이 업데이트 되며, 침투 테스트를 수행 하는 도구를 포함 하는 이미지 및 검색을 위한 보안 취약성이 있는 다른 이미지를 포함 하는 윤리적 해킹 침투 테스트 클래스에 필요한 이미지가 있습니다. 이제 템플릿 이미지를 클래스에 게시할 수 있습니다. 템플릿 페이지에서 **게시** 단추를 선택하여 랩에 템플릿을 게시합니다.
  

## <a name="cost"></a>비용  
이 랩의 비용을 예측하려는 경우 다음 예제를 사용할 수 있습니다. 
 
학생이 25명이며 예약된 클래스 시간이 20시간이고, 숙제 또는 할당이 10시간인 경우 랩 가격은 

25 개 학생 * (20 + 10) 시간 * 55 랩 단위 * 0.01 USD/시간 = 412.50 USD 

가격 책정에 대 한 자세한 내용은 [Azure Lab Services 가격 책정](https://azure.microsoft.com/pricing/details/lab-services/)을 참조 하세요.

## <a name="conclusion"></a>결론
이 문서에서는 윤리적 해킹 클래스의 랩을 만드는 단계를 안내 합니다. 통과 테스트를 위해 호스트 가상 컴퓨터 내에 두 개의 가상 컴퓨터를 만들기 위한 중첩 된 가상화를 설정 하는 단계가 포함 되어 있습니다.

## <a name="next-steps"></a>다음 단계
다음 단계는 모든 랩 설정에 공통됩니다.

- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [이메일로 학생에게 등록 링크 보내기](how-to-configure-student-usage.md#send-invitations-to-users) 

