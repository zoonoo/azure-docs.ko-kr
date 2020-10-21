---
title: Azure Marketplace VM에 대 한 일반적인 질문
description: Azure Marketplace에서 가상 머신을 만들 때 발생 하는 일반적인 질문입니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2020
ms.openlocfilehash: 131cd9b1de82213d89ee07f30aed25544f6fcb18
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284389"
---
# <a name="common-questions-about-vm-in-azure-marketplace"></a>Azure Marketplace VM에 대 한 일반적인 질문

FAQ (질문과 대답)는 Azure Marketplace에서 VM (가상 머신) 제품을 만들 때 발생할 수 있는 일반적인 문제를 다룹니다.

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>내 VM에서 작동하도록 VPN(가상 사설망)을 구성하려면 어떻게 해야 하나요?

Azure Resource Manager 배포 모델을 사용하는 경우 세 가지 옵션은 다음과 같습니다.

- [Azure Portal을 사용하여 경로 기반 VPN 게이트웨이 만들기](../vpn-gateway/create-routebased-vpn-gateway-portal.md)
- [Azure PowerShell을 사용하여 경로 기반 VPN 게이트웨이 만들기](../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
- [CLI를 사용하여 경로 기반 VPN 게이트웨이 만들기](../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Azure 기반 VM에서 Microsoft 서버 소프트웨어를 실행하기 위한 Microsoft 지원 정책은 무엇인가요?

[Microsoft Azure 가상 머신에 대한 Microsoft 서버 소프트웨어 지원](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)에서 자세한 내용을 확인할 수 있습니다.

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>VM에서 시작 태스크에 있는 사용자 지정 스크립트 확장을 어떻게 관리하나요?

Azure PowerShell 모듈, Azure Resource Manager 템플릿을 통해 사용자 지정 스크립트 확장을 사용하는 방법과 Windows 시스템의 문제 해결 단계에 대한 자세한 내용은 [Windows용 사용자 지정 스크립트 확장](/azure/virtual-machines/extensions/custom-script-windows)을 참조하세요.

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Azure Marketplace에서 32비트 애플리케이션 또는 서비스가 지원되나요?

아니요. Azure VM에 지원되는 운영 체제 및 표준 서비스는 모두 64비트입니다. 대부분의 64비트 운영 체제는 이전 버전과의 호환성을 위해 32비트 버전의 애플리케이션을 지원하지만, VM 솔루션의 일부로 32비트 애플리케이션을 사용하는 것은 지원되지 않으며 권장되지 않습니다. 애플리케이션을 64비트 프로젝트로 다시 만드세요.

자세한 내용은 다음 문서를 참조하세요.

- [32비트 애플리케이션 실행](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
- [Azure 가상 머신에 32비트 운영 체제 지원](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Microsoft Azure 가상 머신에 대한 Microsoft 서버 소프트웨어 지원](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>오류: VHD가 이미 리소스로 이미지 리포지토리에 등록됨

내 VHD에서 이미지를 만들려고 할 때마다 Azure PowerShell에서 "리소스인 이미지 리포지토리로 VHD를 이미 등록했습니다." 오류가 표시됩니다. 이전에 이미지를 만들지도 않았고, Azure에서 이 이름을 가진 이미지를 찾지도 못했습니다. 이 문제를 해결하려면 어떻게 해야 하나요?

이 문제는 일반적으로 사용자가 잠겨 있는 VHD에서 VM을 만든 경우에 발생합니다. 이 VHD에서 할당된 VM이 없는지 확인한 다음, 작업을 다시 시도하세요. 문제가 계속 발생하면 지원 티켓을 여세요. [파트너 센터 지원](support.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [VM 인증 문제 해결](azure-vm-create-certification-faq.md)
