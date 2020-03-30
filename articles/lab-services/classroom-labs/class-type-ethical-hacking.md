---
title: Azure Lab 서비스를 통해 윤리적 해킹 연구소 구축 | 마이크로 소프트 문서
description: Azure Lab 서비스를 사용하여 윤리적 해킹을 가르치는 랩을 설정하는 방법을 알아봅니다.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 2b600edc4c360a2b2990be34e44bb8fbd1c8f721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133183"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class"></a>윤리적 해킹 수업을 가르치기 위한 실험실 설치 
이 문서에서는 윤리적 해킹의 법의학 측면에 중점을 둔 클래스를 설정하는 방법을 보여 주며 있습니다. 윤리적 해킹 커뮤니티에서 사용하는 방법인 침투 테스트는 누군가가 악의적인 공격자가 악용할 수 있는 취약성을 입증하기 위해 시스템 또는 네트워크에 대한 액세스 권한을 얻으려고 할 때 발생합니다. 

윤리적 해킹 클래스에서 학생은 취약점을 방어하기 위한 최신 기술을 배울 수 있습니다. 각 학생에게는 두 개의 중첩된 가상 머신이 있는 Windows Server 호스트 가상 머신이 있습니다. 즉 [Metasploitable3](https://github.com/rapid7/metasploitable3) 이미지가 있는 가상 머신과 [Kali Linux](https://www.kali.org/) 이미지가 있는 가상 머신이 있습니다. Metasploitable 가상 머신은 악용 목적을 위해 사용되며 Kali 가상 머신은 법정 작업을 실행하는 데 필요한 도구에 대한 액세스 권한을 제공합니다.

이 문서에는 두 가지 주요 섹션이 있습니다. 첫 번째 섹션에서는 강의실 랩을 만드는 방법을 설명합니다. 두 번째 섹션에서는 중첩된 가상화를 사용하도록 설정하고 필요한 도구와 이미지를 사용하여 템플릿 컴퓨터를 만드는 방법을 설명합니다. 이 경우 하이퍼-V가 활성화된 컴퓨터에서 메타스플로이트 가능한 이미지와 칼리 리눅스 이미지가 이미지를 호스트할 수 있습니다.

## <a name="lab-configuration"></a>랩 구성
이 랩을 설정하려면 Azure 구독을 시작해야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Azure 구독을 받으면 Azure Lab 서비스에서 새 랩 계정을 만들거나 기존 계정을 사용할 수 있습니다. 새 랩 계정을 만들기 위한 다음 자습서를 참조하십시오: [랩 계정을 설정하는 자습서.](tutorial-setup-lab-account.md)

[이 자습서를](tutorial-setup-classroom-lab.md) 수행하여 새 랩을 만든 다음 다음 설정을 적용합니다.

| 가상 머신 크기 | 이미지 |
| -------------------- | ----- | 
| 중간(중첩 가상화) | Windows Server 2019 Datacenter |

## <a name="template-machine"></a>템플릿 기계 

템플릿 컴퓨터를 만든 후 컴퓨터를 시작하고 다음에 세 가지 주요 작업을 완료하기 위해 컴퓨터에 연결합니다. 
 
1. 중첩된 가상화를 위해 컴퓨터를 설정합니다. 하이퍼-V와 같은 모든 적절한 창 기능을 활성화하고 하이퍼-V 이미지가 서로 및 인터넷과 통신할 수 있도록 네트워킹을 설정합니다.
2. [칼리](https://www.kali.org/) 리눅스 이미지를 설정합니다. Kali는 침투 테스트 및 보안 감사를 위한 도구를 포함하는 Linux 배포판입니다.
3. 메타스플로이트 가능한 이미지를 설정합니다. 이 예제에서는 [메타스플로이트3](https://github.com/rapid7/metasploitable3) 이미지가 사용됩니다. 이 이미지는 의도적으로 보안 취약점을 갖도록 만들어집니다.

위에서 설명한 작업을 자동화하는 스크립트는 [Lab Services 윤리적 해킹 스크립트에서](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/EthicalHacking)확인할 수 있습니다.

### <a name="prepare-template-machine-for-nested-virtualization"></a>중첩 가상화를 위한 템플릿 컴퓨터 준비
[이 문서의](how-to-enable-nested-virtualization-template-vm.md) 지침에 따라 중첩가상화를 위해 템플릿 가상 컴퓨터를 준비합니다. 

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>칼리 리눅스 이미지와 중첩 된 가상 머신을 설정
Kali는 침투 테스트 및 보안 감사를 위한 도구를 포함하는 Linux 배포판입니다.

1. 에서 [https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/)이미지를 다운로드합니다.  
    1. 하이퍼 **V에 대한 칼리 리눅스 하이퍼 V 64 비트를** 다운로드합니다.
    1. .7z 파일을 추출합니다.  아직 7 zip이 없는 경우 에서 [https://www.7-zip.org/download.html](https://www.7-zip.org/download.html)다운로드합니다. 나중에 필요할 때 추출된 폴더의 위치를 기억하십시오.
2. 관리 도구에서 **하이퍼 V 관리자를 엽니다.**
1. **작업**을 선택한 다음 **가상 컴퓨터 가져오기를**선택합니다. 
1. **가상 컴퓨터 가져오기** 마법사의 폴더 **찾기** 페이지에서 칼리 Linux 이미지를 보유하는 추출된 폴더의 위치를 선택합니다.

    ![폴더 대화 상자 찾기](../media/class-type-ethical-hacking/locate-folder.png)
1. 가상 **컴퓨터 선택** 페이지에서 칼리 리눅스 이미지를 선택합니다.  이 경우, 이미지는 **칼리 리눅스 -2019.3-hyperv입니다.**

    ![칼리 이미지 선택](../media/class-type-ethical-hacking/select-kali-image.png)
1.  가져오기 **유형 선택** 페이지에서 **가상 컴퓨터 복사(새 고유 ID 만들기)를**선택합니다.

    ![가져오기 유형 선택](../media/class-type-ethical-hacking/choose-import-type.png)
1. **가상 컴퓨터 파일에 대한 폴더 선택에서** 기본 설정을 수락하고 **폴더를 선택하여 가상 하드 디스크** 페이지를 저장한 다음 **다음을 선택합니다.**
1. 네트워크 **연결** 페이지에서 이 문서의 **중첩 가상화 에 대한 템플릿 준비** 섹션의 앞에서 만든 **LabServicesSwitch를** 선택한 다음 다음 을 **선택합니다.**

    ![네트워크 페이지 연결](../media/class-type-ethical-hacking/connect-network.png)
1. 요약 페이지에서 **완료를** **선택합니다.** 복사 및 가져오기 작업이 완료될 때까지 기다립니다. 칼리 리눅스 가상 머신은 이제 하이퍼 V에서 사용할 수 있습니다.
1. **Hyper-V 관리자에서** **작업** -> **시작을**선택한 다음 **작업** -> **연결을** 선택하여 가상 컴퓨터에 연결합니다.  
12. 기본 사용자 이름은 `root`이며 암호는 `toor`입니다. 

    > [!NOTE]
    > 이미지의 잠금을 해제해야 하는 경우 CTRL 키를 누르고 마우스를 위쪽으로 드래그합니다.

## <a name="set-up-a-nested-vm-with-metasploitable-image"></a>메타스플로이트 이미지로 중첩된 VM 설정  
Rapid7 메타스플로이트 가능한 이미지는 보안 취약점으로 의도적으로 구성된 이미지입니다. 이 이미지를 사용하여 문제를 테스트하고 찾을 수 있습니다. 다음 지침은 미리 생성된 Metasploitable 이미지를 사용하는 방법을 보여 주며, 미리 만들어진 Metasploitable 이미지를 사용하는 방법을 보여 준다. 그러나 최신 버전의 메타스플로이터 이미지가 필요한 경우 [https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3)를 참조하십시오.

1. 로 [https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html)이동합니다. 양식을 작성하여 이미지를 다운로드하고 **제출** 단추를 선택합니다.
1. **메타스플로이트 다운로드 지금** 버튼을 선택합니다.
1. zip 파일을 다운로드하면 zip 파일을 추출하고 위치를 기억합니다.
1. Hyper-V와 함께 사용할 수 있도록 추출된 vmdk 파일을 vhdx 파일로 변환합니다. 이렇게 하려면 관리 권한이 있는 PowerShell을 열고 vmdk 파일이 있는 폴더로 이동한 다음 다음 지침을 따릅니다.
    1. Microsoft [가상 컴퓨터 변환기를](https://www.microsoft.com/download/details.aspx?id=42497)다운로드하고 메시지가 표시되면 mvmc_setup.msi 파일을 실행합니다.
    1. PowerShell 모듈을 가져옵니다.  모듈이 설치된 기본 위치는 C:\프로그램 파일\Microsoft 가상 컴퓨터 변환기\입니다.

        ```powershell
        Import-Module 'C:\Program Files\Microsoft Virtual Machine Converter\MvmcCmdlet.psd1'
        ```
    1. vmdk를 Hyper-V에서 사용할 수 있는 vhd 파일로 변환합니다. 이 작업에는 몇 분 정도 걸릴 수 있습니다.
    
        ```powershell
        ConvertTo-MvmcVirtualHardDisk -SourceLiteralPath .\Metasploitable.vmdk -DestinationLiteralPath .\Metasploitable.vhdx -VhdType DynamicHardDisk -VhdFormat vhdx
        ```
    1. 새로 만든 메타스플로이트.vhdx를 C:\사용자\공개\문서\하이퍼-V\가상 하드 디스크\로 복사합니다. 
1. 새 Hyper-V 가상 컴퓨터를 만듭니다.
    1. **열기 하이퍼 V 관리자**.
    1. **작업** -> **새** -> 가상**컴퓨터를**선택합니다.
    1. **새 가상 컴퓨터 마법사의** **시작하기 전** 페이지에서 **다음을 클릭합니다.**
    1. 이름 **및 위치 지정** 페이지에서 **이름에**대해 **메타스플로이터(Metasploitable)를** 입력하고 **다음**을 선택합니다.

        ![새 VM 이미지 마법사](../media/class-type-ethical-hacking/new-vm-wizard-1.png)
    1. 생성 **지정** 페이지에서 기본값을 수락하고 **다음**을 선택합니다.
    1. 메모리 **할당** 페이지에서 **시작 메모리에**대해 **512MB를** 입력하고 **다음을**선택합니다. 

        ![메모리 할당 페이지](../media/class-type-ethical-hacking/assign-memory-page.png)
    1. 네트워킹 **구성** 페이지에서 연결을 **연결되지 않음으로**둡니다. 나중에 네트워크 어댑터를 설정합니다.
    1. 가상 **하드 디스크 연결** 페이지에서 **기존 가상 하드 디스크 사용을**선택합니다. 이전 단계에서 만든 **metasploitable.vhdx** 파일의 위치를 찾아다음을 **Next**선택합니다. 

        ![가상 네트워크 디스크 페이지 연결](../media/class-type-ethical-hacking/connect-virtual-network-disk.png)
    1. 새 **가상 컴퓨터 마법사 완료** 페이지에서 **완료를**선택합니다.
    1. 가상 시스템이 만들어지면 Hyper-V Manager에서 가상 컴퓨터를 선택합니다. 아직 기기를 켜지 마십시오.  
    1. **작업** -> **설정을**선택합니다.
    1. **메타스플로이가능** 대화 상자에 대한 설정에서 **하드웨어 추가를**선택합니다. 
    1. **레거시 네트워크 어댑터를**선택하고 **추가**를 선택합니다.

        ![네트워크 어댑터 페이지](../media/class-type-ethical-hacking/network-adapter-page.png)
    1. 레거시 **네트워크 어댑터** 페이지에서 **가상 스위치에** 대한 **LabServicesSwitch를** 선택하고 **확인을**선택합니다. LabServicesSwitch는 **중첩된 가상화에** 대한 템플릿 준비 섹션에서 Hyper-V용 템플릿 컴퓨터를 준비할 때 만들어졌습니다.

        ![레거시 네트워크 어댑터 페이지](../media/class-type-ethical-hacking/legacy-network-adapter-page.png)
    1. 이제 메타스플로이트 이미지를 사용할 준비가 되었습니다. **Hyper-V 관리자에서** **작업** -> **시작을**선택한 다음 **작업** -> **연결을** 선택하여 가상 컴퓨터에 연결합니다.  기본 사용자 이름은 **msfadmin이고** 암호는 **msfadmin입니다.** 


템플릿은 이제 업데이트되고 윤리적 해킹 침투 테스트 클래스에 필요한 이미지, 침투 테스트를 수행하는 도구가있는 이미지 및 보안 취약점이있는 다른 이미지가 있습니다. 이제 템플릿 이미지를 클래스에 게시할 수 있습니다. 템플릿 페이지에서 **게시** 단추를 선택하여 템플릿을 랩에 게시합니다.
  

## <a name="cost"></a>비용  
이 랩의 비용을 추정하려면 다음 예제를 사용할 수 있습니다. 
 
25명의 학생이 20시간의 수업 시간과 숙제 또는 과제에 대한 할당량 10시간의 수업의 경우 랩가격은 다음과 같은 것입니다. 

25명의 학생 * (20 + 10) 시간 * 55 랩 단위 * 시간당 0.01 USD = 412.50 USD. 

가격 책정에 대한 자세한 내용은 [Azure 랩 서비스 가격 책정](https://azure.microsoft.com/pricing/details/lab-services/)을 참조하십시오.

## <a name="conclusion"></a>결론
이 문서에서는 윤리적 해킹 클래스에 대한 실험실을 만드는 단계를 안내했습니다. 여기에는 관통 테스트를 위해 호스트 가상 시스템 내에 두 개의 가상 컴퓨터를 만들기 위한 중첩 가상화를 설정하는 단계가 포함되어 있습니다.

## <a name="next-steps"></a>다음 단계
다음 단계는 랩을 설정하는 데 일반적입니다.

- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [학생에게 전자 메일 등록 링크](how-to-configure-student-usage.md#send-invitations-to-users). 

