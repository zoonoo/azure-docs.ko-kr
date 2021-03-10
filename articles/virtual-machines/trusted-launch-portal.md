---
title: '미리 보기: 신뢰할 수 있는 시작 VM 배포'
description: 신뢰할 수 있는 시작을 사용 하는 VM을 배포 합니다.
author: khyewei
ms.author: khwei
ms.reviewer: cynthn
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: template-how-to
ms.openlocfilehash: f5e361d32cf2ab436f92ce2ca86a054a6dd3337e
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553751"
---
# <a name="deploy-a-vm-with-trusted-launch-enabled-preview"></a>신뢰할 수 있는 시작을 사용 하도록 설정 된 VM 배포 (미리 보기)

[신뢰할 수 있는 시작](trusted-launch.md) 은 [2 세대](generation-2.md) vm의 보안을 개선 하는 방법입니다. 신뢰할 수 있는 시작은 vTPM 및 보안 부팅 등의 인프라 기술을 결합 하 여 고급 및 지속적인 공격 기법을 방지 합니다.

> [!IMPORTANT]
> 신뢰할 수 있는 시작은 현재 공개 미리 보기로 제공 됩니다.
> 
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
>
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="deploy-using-the-portal"></a>Portal을 사용하여 배포

트러스트 된 실행을 사용 하도록 설정 된 가상 컴퓨터를 만듭니다.

1. Azure [Portal](https://aka.ms/TL_preview)에 로그인합니다.
1. **Virtual Machines** 를 검색 합니다.
1. **서비스** 에서 **가상 머신** 을 선택합니다.
1. **가상 컴퓨터** 페이지에서 **추가** 를 선택한 다음 **가상 컴퓨터** 를 선택 합니다.
1. **프로젝트 세부 정보** 아래에서 올바른 구독을 선택 했는지 확인 합니다.
1. **리소스 그룹** 에서 **새로 만들기** 를 선택 하 고 리소스 그룹의 이름을 입력 하거나 드롭다운 목록에서 기존 리소스 그룹을 선택 합니다.
1. **인스턴스 정보** 에서 가상 컴퓨터 이름에 대 한 이름을 입력 하 고 [신뢰할 수 있는 시작](trusted-launch.md#public-preview-limitations)을 지 원하는 지역을 선택 합니다.
1. **이미지** 에서 [신뢰할 수 있는 시작을 지 원하는 이미지](trusted-launch.md#public-preview-limitations)를 선택 합니다. 이미지의 Gen 1 버전만 표시 될 수 있습니다. 즉, 다음 단계로 이동 합니다.
1. 페이지 위쪽에서 선택 하 여 **고급** 탭으로 전환 합니다.
1. **VM 생성** 섹션으로 스크롤한 다음 **Gen 2** 를 선택 합니다.
1. 계속 **고급** 탭에서 **신뢰할 수 있는 시작** 으로 스크롤한 다음 **신뢰할 수 있는 실행** 확인란을 선택 합니다. 그러면 보안 부팅 및 vTPM 이라는 두 가지 추가 옵션이 표시 됩니다. 배포에 대 한 적절 한 옵션을 선택 합니다.

    :::image type="content" source="media/trusted-launch/trusted-launch-portal.png" alt-text="신뢰할 수 있는 시작 옵션을 보여 주는 스크린샷":::

1. **기본** 탭으로 돌아가서 **이미지** 를 확인 하 고 다음 메시지가 표시 되는지 확인 **합니다 .이 이미지는 신뢰할 수 있는 실행 미리 보기를 지원 합니다. 고급 탭에서를 구성** 합니다. 이제 gen 2 이미지를 선택 해야 합니다.

    :::image type="content" source="media/trusted-launch/gen-2-image.png" alt-text="신뢰할 수 있는 시작을 지 원하는 gen2 이미지 임을 확인 하는 메시지를 보여 주는 스크린샷":::

1.  신뢰할 수 있는 시작을 지 원하는 VM 크기를 선택 합니다. [지원 되는 크기](trusted-launch.md#public-preview-limitations)목록을 참조 하세요.
1.  **관리자 계정** 정보를 입력 한 다음 **인바운드 포트 규칙** 을 입력 합니다.
1.  페이지 맨 아래에서 **검토 + 만들기** 를 선택 합니다.
1.  **가상 컴퓨터 만들기** 페이지에서 배포 하려는 VM에 대 한 세부 정보를 볼 수 있습니다. 준비가 되면 **만들기** 를 선택합니다.

    :::image type="content" source="media/trusted-launch/validation.png" alt-text="신뢰할 수 있는 시작 옵션이 포함 되어 있는 유효성 검사 페이지의 sceenshot.":::


VM 배포에는 몇 분 정도 걸립니다. 

## <a name="deploy-using-a-template"></a>템플릿을 사용하여 배포

빠른 시작 템플릿을 사용 하 여 신뢰할 수 있는 시작 Vm을 배포할 수 있습니다.

**Linux**:    
[![Azure에 배포](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2FcreateUiDefinition.json)

**Windows**:    
[![Azure에 배포](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2FcreateUiDefinition.json)

## <a name="view-and-update"></a>보기 및 업데이트

포털에서 VM에 대 한 **개요** 페이지를 방문 하 여 기존 vm에 대 한 신뢰할 수 있는 시작 구성을 볼 수 있습니다.

신뢰할 수 있는 시작 구성을 변경 하려면 왼쪽 메뉴의 **설정** 섹션에서 **구성** 을 선택 합니다. 신뢰할 수 있는 **시작** 섹션에서 보안 부팅 및 vtpm을 사용 하거나 사용 하지 않도록 설정할 수 있습니다. 완료 되 면 페이지 맨 위에 있는 **저장** 을 선택 합니다. 

:::image type="content" source="media/trusted-launch/configuration.png" alt-text="신뢰할 수 있는 시작 구성을 변경 하는 방법의 스크린샷":::

VM이 실행 중인 경우 수정 된 신뢰할 수 있는 시작 구성을 적용 하기 위해 VM을 다시 시작 한다는 메시지가 표시 됩니다. 변경 내용을 적용 하려면 **예** 를 선택 하 고 VM이 다시 시작 될 때까지 기다립니다.


## <a name="verify-secure-boot-and-vtpm"></a>보안 부팅 및 vTPM 확인

가상 컴퓨터에서 보안 부팅 및 vTPM이 사용 되도록 설정 되었는지 확인할 수 있습니다.
    
### <a name="linux-validate-if-secure-boot-is-running"></a>Linux: 보안 부팅이 실행 중인지 확인

VM에 대 한 SSH를 수행 하 고 다음 명령을 실행 합니다. 

```bash
mokutil --sb-state
```

보안 부팅을 사용 하는 경우 명령은 다음을 반환 합니다.
 
```bash
SecureBoot enabled 
```

### <a name="linux-validate-if-vtpm-is-enabled"></a>Linux: vTPM이 사용 되는 경우 유효성 검사

VM에 SSH를 수행합니다 Tpm0 장치가 있는지 확인 합니다. 

```bash
ls /dev/tpm0
```

VTPM을 사용 하는 경우 명령은 다음을 반환 합니다.

```output
/dev/tpm0
```

VTPM이 사용 하지 않도록 설정 된 경우 명령은 다음을 반환 합니다.

```output
ls: cannot access '/dev/tpm0': No such file or directory
```

### <a name="windows-validate-that-secure-boot-is-running"></a>Windows: 보안 부팅이 실행 중인지 확인

원격 데스크톱을 사용 하 여 VM에 연결한 다음를 실행 `msinfo32.exe` 합니다.

오른쪽 창에서 보안 부팅 상태가 **켜져** 있는지 확인 합니다.

## <a name="enable-the-azure-security-center-experience"></a>Azure Security Center 환경 사용

Azure Security Center를 사용 하 여 신뢰할 수 있는 시작 Vm에 대 한 정보를 표시 하려면 여러 정책을 사용 하도록 설정 해야 합니다. 정책을 사용 하도록 설정 하는 가장 쉬운 방법은이 [리소스 관리자 템플릿을](https://github.com/prash200/azure-quickstart-templates/tree/master/101-asc-trustedlaunch-policies) 구독에 배포 하는 것입니다. 

아래 단추를 선택 하 여 구독에 정책을 배포 합니다.

[![Azure에 배포](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-asc-trustedlaunch-policies%2Fazuredeploy.json)

템플릿은 구독 당 한 번만 배포 해야 합니다. `GuestAttestation` `AzureSecurity` 지원 되는 모든 vm에 자동으로 설치 되 고 확장 됩니다. 오류가 발생 하는 경우 템플릿을 다시 배포 해 보세요.

신뢰할 수 있는 시작 Vm에 대 한 vTPM 및 보안 부팅 권장 사항을 얻으려면 [구독에 사용자 지정 이니셔티브 추가](https://docs.microsoft.com/azure/security-center/custom-security-policies#to-add-a-custom-initiative-to-your-subscription)를 참조 하세요.
 
## <a name="sign-things-for-secure-boot-on-linux"></a>Linux에서 보안 부팅에 대 한 서명

UEFI 보안 부팅을 위해 서명 해야 하는 경우도 있습니다.  예를 들어 Ubuntu 용 [보안 부팅에 대 한 항목을 서명 하는 방법을](https://ubuntu.com/blog/how-to-sign-things-for-secure-boot) 살펴보겠습니다. 이러한 경우, VM에 대해 MOK 유틸리티를 등록 하는 키를 입력 해야 합니다. 이렇게 하려면 Azure 직렬 콘솔을 사용 하 여 MOK 유틸리티에 액세스 해야 합니다.

1. Linux 용 Azure 직렬 콘솔을 사용 하도록 설정 합니다. 자세한 내용은 [Linux 용 직렬 콘솔](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/serial-console-linux)을 참조 하세요.
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **가상 컴퓨터** 를 검색 하 고 목록에서 VM을 선택 합니다.
1. 왼쪽 메뉴의 **지원 + 문제 해결** 에서 **직렬 콘솔** 를 선택 합니다. 페이지는 직렬 콘솔을 사용 하 여 오른쪽으로 열립니다.
1. Azure 직렬 콘솔을 사용 하 여 VM에 로그온 합니다. **로그인** 의 경우 VM을 만들 때 사용한 사용자 이름을 입력 합니다. 예: *azureuser*. 메시지가 표시 되 면 사용자 이름과 연결 된 암호를 입력 합니다.
1. 로그인 한 후에는를 사용 `mokutil` 하 여 공개 키 파일을 가져옵니다 `.der` .

    ```bash
    sudo mokutil –import <path to public key.der> 
    ```
1. 을 입력 하 여 Azure 직렬 콘솔에서 컴퓨터를 다시 부팅 `sudo reboot` 합니다. 10 초 카운트다운이 시작 됩니다.
1. 위로 또는 아래로 키를 눌러 카운트다운을 중단 하 고 UEFI 콘솔 모드에서 대기 합니다. 타이머가 중단 되지 않으면 부팅 프로세스가 계속 되 고 모든 MOK 변경 내용이 손실 됩니다.
1. MOK 유틸리티 메뉴에서 적절 한 작업을 선택 합니다.

    :::image type="content" source="media/trusted-launch/mok-mangement.png" alt-text="직렬 콘솔에서 MOK 관리 메뉴에 사용할 수 있는 옵션을 보여 주는 스크린샷":::


## <a name="next-steps"></a>다음 단계

[신뢰할 수 있는 시작](trusted-launch.md) 및 [2 세대](generation-2.md) vm에 대해 자세히 알아보세요.
