---
title: Azure Lab Services 및 GNS3를 사용 하 여 네트워킹 랩 설정 | Microsoft Docs
description: GNS3를 사용 하 여 네트워킹을 배우는 Azure Lab Services을 사용 하 여 랩을 설정 하는 방법을 알아봅니다.
ms.topic: article
ms.date: 01/19/2021
ms.openlocfilehash: dec5dea13d5a89536a06da45fc57d33881a9b3ad
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99500514"
---
# <a name="set-up-a-lab-to-teach-a-networking-class"></a>네트워킹 클래스를 학습 하기 위한 랩 설정 
이 문서에서는 학생이 [GNS3](https://www.gns3.com/) 소프트웨어를 사용 하 여 가상 및 실제 네트워크를 에뮬레이트, 구성, 테스트 및 문제 해결할 수 있도록 하는 데 중점을 둔 클래스를 설정 하는 방법을 보여 줍니다. 

이 문서에는 두 가지 주요 섹션이 있습니다. 첫 번째 섹션에서는 교실 랩을 만드는 방법을 설명 합니다. 두 번째 섹션에서는 중첩 된 가상화를 사용 하도록 설정 하 고 GNS3가 설치 및 구성 된 템플릿 컴퓨터를 만드는 방법을 설명 합니다.

## <a name="lab-configuration"></a>랩 구성
이 랩을 설정하려면 Azure 구독을 시작해야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Azure 구독을 받은 후 Azure Lab Services에서 새 랩 계정을 만들거나 기존 계정을 사용할 수 있습니다. 새 랩 계정 만들기에 대 한 자습서: [랩 계정 설정 자습서](tutorial-setup-lab-account.md)를 참조 하세요.

[이 자습서](tutorial-setup-classroom-lab.md) 에 따라 새 랩을 만들고 다음 설정을 적용 합니다.

| 가상 머신 크기 | 이미지 |
| -------------------- | ----- | 
| 대형(중첩된 가상화) | Windows 10 Pro, 버전 1909 |

## <a name="template-machine"></a>템플릿 컴퓨터 

템플릿 컴퓨터를 만든 후 컴퓨터를 시작 하 고 연결 하 여 다음 세 가지 주요 작업을 완료 합니다. 
 
1. 중첩 된 가상화를 위해 템플릿 시스템을 준비 합니다.
2. GNS3를 설치 합니다.
3. Hyper-v에서 중첩 된 GNS3 VM을 만듭니다.
4. Windows Hyper-v VM을 사용 하도록 GNS3를 구성 합니다.
5. 적절 한 어플라이언스를 추가 합니다.
6. 템플릿을 게시 합니다.


### <a name="prepare-template-machine-for-nested-virtualization"></a>중첩 된 가상화를 위한 템플릿 시스템 준비
- 중첩 된 가상화를 위해 템플릿 가상 머신을 준비 하려면 [이 문서의](how-to-enable-nested-virtualization-template-vm.md) 지침을 따르세요. 

### <a name="install-gns3"></a>GNS3 설치
- [Windows에서 GNS3를 설치](https://docs.gns3.com/docs/getting-started/installation/windows)하는 방법에 대 한 지침을 따르세요.  구성 요소 대화 상자에 **GNS3 VM** 설치를 포함 해야 합니다. 아래를 참조 하십시오.

![SelectGNS3vm](./media/class-type-networking-gns3/gns3-select-vm.png)

결국 GNS3 VM 선택에 도달 하 게 됩니다. **Hyper-v** 옵션을 선택 했는지 확인 합니다.

![SelectHyperV](./media/class-type-networking-gns3/gns3-vm-hyper-v.png)

  이 옵션은 Hyper-v 관리자에서 PowerShell 스크립트 및 VHD 파일을 다운로드 하 여 GNS3 VM을 만듭니다. 기본값을 사용 하 여 설치를 계속 합니다. **설치가 완료 되 면 GNS3를 시작 하지 않습니다**.

### <a name="create-gns3-vm"></a>GNS3 VM 만들기
설치가 완료 되 면 zip 파일 **"GNS3.VM.Hyper-V.2.2.17.zip"** 이 설치 파일과 같은 폴더에 다운로드 됩니다. 여기에는 드라이브와 hyper-v vm을 만들기 위한 PowerShell 스크립트가 포함 되어 있습니다.
- GNS3.VM.Hyper-V.2.2.17.zip에서 **모두 추출** 합니다.  이 작업을 수행 하면 드라이브와 PowerShell 스크립트를 추출 하 여 VM을 만듭니다.
- "create-vm.ps1" PowerShell 스크립트에서 파일을 마우스 오른쪽 단추로 클릭 하 여 **powershell을 사용** 하 여 실행 합니다.
- 실행 정책 변경 요청이 표시 될 수 있습니다. "Y"를 입력 하 여 스크립트를 실행 합니다.

![PSExecutionPolicy](./media/class-type-networking-gns3/powershell-execution-policy-change.png)

- 스크립트가 완료 되 면 Hyper-v 관리자에서 VM "GNS3 VM"이 만들어졌는지 확인할 수 있습니다.

### <a name="configure-gns3-to-use-hyper-v-vm"></a>Hyper-v VM을 사용 하도록 GNS3 구성
이제 GNS3가 설치 되 고 GNS3 VM이 추가 되었습니다. GNS3를 시작 하 여 두 개의 연결을 모두 연결 합니다.  [GNS3 설치 마법사가 자동으로 시작 됩니다](https://docs.gns3.com/docs/getting-started/setup-wizard-gns3-vm#local-gns3-vm-setup-wizard).  
- **가상 머신에서 어플라이언스 실행을 사용 합니다.** 사용합니다.  **VMware vmrun 도구를 찾을 수 없을** 때까지 마법사의 나머지 부분에 대 한 기본값을 사용 합니다. error.

![VMWareError](./media/class-type-networking-gns3/gns3-vmware-vmrun-tool-not-found.png)

- **확인** 을 선택 하 고 마법사를 **취소** 합니다.
- Hyper-v vm에 대 한 연결을 완료 하려면   ->  **기본 설정**  ->  **GNS3 vm** 을 열고 **GNS3 vm 사용** 을 선택 하 고 **hyper-v** 옵션을 선택 합니다.
 
![EnableGNS3VMs](./media/class-type-networking-gns3/gns3-preference-vm.png)

### <a name="add-appropriate-appliances"></a>적절 한 어플라이언스 추가

이제 [클래스에 대 한](https://docs.gns3.com/docs/using-gns3/beginners/install-from-marketplace) 적절 한 어플라이언스를 추가 하려고 합니다.

### <a name="publish-template"></a>템플릿 게시

이제 템플릿 VM이 올바르게 설정 되 고 게시할 준비가 완료 되 면 몇 가지 주요 사항을 확인 해야 합니다.
- GNS3 VM이 종료 되거나 꺼져 있는지 확인 합니다.  VM이 계속 실행 되는 동안 게시 하면 VM이 손상 됩니다.
- GNS3을 닫고 실행 하는 동안 게시 하면 의도 하지 않은 부작용이 발생할 수 있습니다.
- 설치 파일 또는 기타 불필요 한 파일을 정리 합니다.

## <a name="cost"></a>비용  

이 랩의 비용을 예측하려는 경우 다음 예제를 사용할 수 있습니다. 
 
학생이 25명이며 예약된 클래스 시간이 20시간이고, 숙제 또는 할당이 10시간인 경우 랩 가격은 

25 개 학생 * (20 + 10) 시간 * 84 랩 단위 * 0.01 USD/시간 = 630 USD 

**중요:** 비용 예측은 예를 들어 목적 으로만 사용 됩니다.  가격 책정에 대 한 최신 정보는 [Azure Lab Services 가격 책정](https://azure.microsoft.com/pricing/details/lab-services/)을 참조 하세요.

## <a name="conclusion"></a>결론
이 문서에서는 GNS3를 사용 하 여 네트워크 구성에 대 한 랩을 만드는 단계를 안내 합니다.

## <a name="next-steps"></a>다음 단계
다음 단계는 모든 랩 설정에 공통됩니다.

- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [이메일로 학생에게 등록 링크 보내기](how-to-configure-student-usage.md#send-invitations-to-users)