---
title: Azure 랩 서비스를 통해 Linux 셸 스크립팅 랩 설정 | 마이크로 소프트 문서
description: Linux에서 셸 스크립팅을 가르치는 랩을 설정하는 방법에 대해 알아봅니다.
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
ms.date: 09/30/2019
ms.author: spelluru
ms.openlocfilehash: 100a485588c77f6977001dae984b30ebcb1de557
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443553"
---
# <a name="set-up-a-lab-to-teach-shell-scripting-on-linux"></a>리눅스에서 쉘 스크립팅을 가르치는 랩 을 설정
이 문서에서는 Linux에서 셸 스크립팅을 가르치는 랩을 설정하는 방법을 보여 주며 있습니다. 스크립팅은 관리자가 반복적인 작업을 방지할 수 있도록 하는 시스템 관리의 유용한 부분입니다. 이 샘플 시나리오에서 클래스는 기존 bash 스크립트와 향상된 스크립트를 포함합니다. 향상된 스크립트는 bash 명령과 Ruby를 결합하는 스크립트입니다. 이 접근 방식을 사용하면 Ruby에서 데이터를 전달하고, bash 명령이 셸과 상호 작용할 수 있습니다. 

이러한 스크립팅 클래스를 수강하는 학생은 Linux 가상 머신을 통해 Linux의 기본 사항을 알아보고 bash 셸 스크립팅에 익숙해질 수 있습니다. Linux 가상 머신은 원격 데스크톱 액세스를 사용하도록 설정되어 있고 [gedit](https://help.gnome.org/users/gedit/stable/) 및 [Visual Studio Code](https://code.visualstudio.com/) 텍스트 편집기가 설치 되어 있습니다.

## <a name="lab-configuration"></a>랩 구성
이 랩을 설정하려면 Azure 구독을 시작해야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Azure 구독이 있으면 Azure Lab Services에서 새 랩 계정을 만들거나 기존 랩 계정을 사용할 수 있습니다. 새 랩 계정을 만드는 다음 자습서를 참조하십시오: [랩 계정 설정 자습서](tutorial-setup-lab-account.md).

랩 계정을 만든 후 랩 계정에서 다음 설정을 사용하도록 설정합니다. 

| 랩 계정 설정 | Instructions |
| ----------- | ------------ |  
| 마켓플레이스 이미지 | [우분투 서버 18.04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) 이미지를 사용하여 랩 계정 내에서 사용할 수 있습니다. 자세한 내용은 [랩 작성자가 사용할 수 있는 마켓플레이스 이미지 지정을](specify-marketplace-images.md)참조하십시오. | 

[이 자습서를](tutorial-setup-classroom-lab.md) 수행하여 새 랩을 만들고 다음 설정을 적용합니다.

| 랩 설정 | 값/지침 | 
| ------------ | ------------------ |
| 가상 컴퓨터(VM) 크기 | 작음  |
| VM 이미지 | [우분투 서버 18.04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) |
| 원격 데스크톱 연결 사용 | 사용. <p>이 설정을 사용하면 교사와 학생이 RDP(원격 데스크톱)를 사용하여 VM에 연결할 수 있습니다. 자세한 내용은 [Azure Lab 서비스의 랩에서 Linux 가상 컴퓨터용 원격 데스크톱 활성화를](how-to-enable-remote-desktop-linux.md#connect-to-the-template-vm)참조하십시오. </p>|


## <a name="install-desktop-and-xrdp"></a>데스크톱 및 xrdp 설치
[우분투 서버 18.04 LTS](https://azuremarketplace.microsoft.com/marketplace/apps/canonical.0001-com-ubuntu-server-bionic) 이미지에는 기본적으로 원격 데스크톱 서버가 설치되어 있지 않습니다. 설치의 지침에 따라 원격 데스크톱을 구성하여 Azure 아티클의 [Linux VM에 연결하여](../../virtual-machines/linux/use-remote-desktop.md) 원격 데스크톱 프로토콜을 통해 연결하는 템플릿 컴퓨터에 필요한 패키지를 설치합니다.

## <a name="install-ruby"></a>Ruby 설치
루비는 bash 스크립트와 결합할 수 있는 오픈 소스 동적 언어입니다. 이 섹션에서는 최신 `apt-get` 버전의 Ruby 를 설치하는 방법을 보여 주시면 [됩니다.](https://www.ruby-lang.org/)

1. 다음 명령을 실행하여 업데이트를 설치합니다.

    ```bash
    sudo apt-get update 
    sudo apt-get upgrade 
    ```
2.  [루비를 설치합니다.](https://www.ruby-lang.org/)  루비는 bash 스크립트와 결합할 수 있는 오픈 소스 동적 언어입니다. 
    
    ```bash
    sudo apt-get install ruby-full
    ```

## <a name="install-development-tools"></a>개발 도구 설치
이 섹션에서는 몇 개의 텍스트 편집기를 설치하는 방법을 보여 주며, 이 섹션에서는 다음과 같은 방법을 보여 주실 수 있습니다. Gedit은 그놈 데스크톱 환경의 기본 텍스트 편집기입니다. 범용 텍스트 편집기로 설계되었습니다. Visual Studio 코드는 디버깅 및 소스 제어 통합에 대한 지원을 포함하는 텍스트 편집기입니다.

> [!NOTE]
> 여러 가지 텍스트 편집기도 사용할 수 있습니다. 비주얼 스튜디오 코드와 gedit은 두 가지 예에 불과합니다.

1. [gedit를](https://help.gnome.org/users/gedit/stable/)설치합니다.

    ```bash
    sudo apt-get install gedit
    ```
1. [비주얼 스튜디오 코드를 설치합니다.](https://code.visualstudio.com/)  비주얼 스튜디오 코드는 스냅 스토어를 사용하여 설치할 수 있습니다.  대체 설치 옵션은 [Visual Studio Code 대체 다운로드를](https://code.visualstudio.com/#alt-downloads)참조하십시오.

    ```bash
    sudo snap install vscode --classic 
    ```

    이제 템플릿이 업데이트되었으며 랩을 완료하는 데 필요한 프로그래밍 언어와 개발 도구가 모두 있습니다. 이제 템플릿 이미지를 랩에 게시할 수 있습니다. 템플릿 페이지에서 **게시** 단추를 선택하여 템플릿을 랩에 게시합니다.  

## <a name="cost"></a>비용 
이 랩의 비용을 추정하려면 다음 예제를 사용할 수 있습니다.
 
25명의 학생이 20시간의 수업 시간과 숙제 또는 과제에 대한 할당량 10시간의 수업의 경우 랩가격은 다음과 같은 것입니다. 

25명의 학생 * (20 + 10) 시간 * 20 랩 유닛 * 시간당 0.01 USD = 150 USD

가격 책정에 대한 자세한 내용은 다음 문서에서 확인할 수 [있습니다.](https://azure.microsoft.com/pricing/details/lab-services/)

## <a name="conclusion"></a>결론
이 문서에서는 스크립팅 클래스를 위한 랩을 만드는 단계를 안내했습니다. 이 문서는 리눅스 컴퓨터에서 루비 스크립팅 도구를 설정하는 데 중점을 두었지만 Linux의 Python과 같은 다른 스크립팅 클래스에도 동일한 설정을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
다음 단계는 랩을 설정하는 데 일반적입니다.

- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [학생에게 전자 메일 등록 링크](how-to-configure-student-usage.md#send-invitations-to-users). 





