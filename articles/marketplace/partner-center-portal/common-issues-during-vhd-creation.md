---
title: 일반적인 VHD 만들기 문제(FAQ)
description: VHD (가상 하드 디스크)를 만들 때 발생 하는 일반적인 문제에 대 한 질문과 대답입니다.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
ms.date: 04/09/2020
ms.openlocfilehash: a47bece4df7263dbc29ec19510c3ea422bb3f4cf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81266267"
---
# <a name="common-issues-during-vhd-creation"></a>VHD를 만드는 동안 발생 하는 일반적인 문제

> [!NOTE]
> Azure VM 제품의 관리를 Cloud 파트너 포털에서 파트너 센터로 전환 하 고 있습니다. 제품이 마이그레이션될 때까지 Cloud 파트너 포털에서 제공 하는 작업을 관리 하기 위해 [VHD를 만드는 동안 발생 하는 일반적인 문제 (FAQ)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues) 의 지침을 계속 따르세요.

FAQ (질문과 대답)는 Azure 가상 머신 제품에 대 한 VHD (가상 하드 디스크)를 만들 때 발생할 수 있는 일반적인 문제를 다룹니다.

## <a name="how-do-i-create-a-vm-from-the-azure-portal-using-a-vhd-in-premium-storage"></a>Premium storage에서 VHD를 사용 하 여 Azure Portal에서 VM을 만들 어떻게 할까요? 있나요?

Azure Marketplace는 현재 관리 되는 저장소 또는 Azure Premium Storage에서 이미지의 VM 제품 만들기를 지원 하지 않습니다. 자세한 내용은 [Azure Managed Disks 개요](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)를 참조 하세요.

## <a name="can-i-use-generation-2-vms-for-offers"></a>제품에 2 세대 Vm을 사용할 수 있나요?

아니요. 1 세대 Vhd만 지원 됩니다. 그러나 현재는 Microsoft Azure 플랫폼 팀과 협력 하 여 2 세대 Vm에 대 한 지원을 조사 하 고 있습니다. 자세한 내용은 [hyper-v에서 1 세대 또는 2 세대 가상 머신을 만들어야 하나요?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) 를 참조 하세요.

## <a name="how-do-i-change-the-name-of-the-host"></a>호스트의 이름을 변경하려면 어떻게 해야 하나요?

열리지 않습니다. VM을 만든 후에는 소유자를 포함 하 여 사용자가 호스트 이름을 업데이트할 수 없습니다.

## <a name="how-do-i-reset-the-remote-desktop-service-or-its-sign-in-password"></a>원격 데스크톱 서비스 또는 해당 로그인 암호를 다시 설정 어떻게 할까요??

다음 문서에서는 Windows 및 Linux 기반 Vm에 대해 RDS 다시 설정을 수행 하는 방법을 설명 합니다.

* [Windows VM에서 원격 데스크톱 서비스 또는 해당 로그인 암호를 다시 설정하는 방법](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
* [VMAccess 확장을 사용하여 Linux VM 암호 또는 SSH 키 재설정, SSH 구성 수정, 디스크 일관성 검사를 수행하는 방법](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)

## <a name="how-do-i-generate-new-ssh-certificates"></a>새 SSH 인증서를 생성할 어떻게 할까요? 있나요?

인증서 생성은 [AZURE VM 이미지 인증](https://aks.ms/CertifyVMimage)에 설명 되어 있습니다.

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Vm과 함께 작동 하도록 VPN (가상 사설망)을 구성 어떻게 할까요??

Azure Resource Manager 배포 모델을 사용 하는 경우 다음 세 가지 옵션을 사용할 수 있습니다.

* [Azure Portal을 사용하여 경로 기반 VPN 게이트웨이 만들기](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
* [Azure PowerShell를 사용 하 여 경로 기반 VPN 게이트웨이 만들기](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
* [CLI를 사용하여 경로 기반 VPN 게이트웨이 만들기](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Azure 기반 VM에서 Microsoft 서버 소프트웨어를 실행하기 위한 Microsoft 지원 정책은 무엇인가요?

[Microsoft Azure 가상 컴퓨터에 대 한 Microsoft 서버 소프트웨어 지원](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)에서 세부 정보를 찾을 수 있습니다.

## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>가상 머신에 연결되는 고유 식별자가 있나요?

예, 가상 머신이 Azure에서 호스팅되는 경우에는 있습니다. Azure는 생성 된 각 새 VM 리소스에 [Azure 가상 머신 고유 ID](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/)라고 하는 고유 식별자를 할당 합니다. 자세한 내용은 Azure 가상 컴퓨터의 고유 ID를 참조 하세요. [LIST API](https://docs.microsoft.com/rest/api/compute/virtualmachines/list)를 통해이 식별자를 가져올 수도 있습니다.

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>VM에서 시작 작업의 사용자 지정 스크립트 확장을 어떻게 관리 하나요?

Windows 시스템에서 Azure PowerShell 모듈, Azure Resource Manager 템플릿 및 문제 해결 단계를 사용 하 여 사용자 지정 스크립트 확장을 사용 하는 방법에 대 한 자세한 내용은 [windows 용 사용자 지정 스크립트 확장](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)을 참조 하세요.

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Azure Marketplace에서 32 비트 응용 프로그램 또는 서비스가 지원 되나요?

일반적으로 그렇지 않습니다. Azure VM에 지원되는 운영 체제 및 표준 서비스는 모두 64비트입니다. 대부분의 64 비트 운영 체제는 이전 버전과의 호환성을 위해 32 비트 버전의 응용 프로그램을 지원 하지만, VM 솔루션의 일부로 32 비트 응용 프로그램을 사용 하는 것은 지원 되지 않으며 권장 되지 않습니다. 64 비트 프로젝트로 응용 프로그램을 다시 만듭니다.

자세한 내용은 다음 문서를 참조하세요.

* [32비트 애플리케이션 실행](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
* [Azure 가상 머신에 32비트 운영 체제 지원](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
* [Microsoft Azure 가상 머신에 대한 Microsoft 서버 소프트웨어 지원](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>오류: VHD가 이미 리소스로 이미지 리포지토리에 등록 되어 있습니다.

내 Vhd에서 이미지를 만들 때마다 Azure PowerShell의 "VHD가 리소스로 이미지 리포지토리에 이미 등록 되었습니다." 라는 오류가 발생 합니다. 이전에 이미지를 만들지 않았으며 Azure에서이 이름을 가진 이미지를 찾지 못했습니다. 이 문제를 해결하려면 어떻게 해야 하나요?

이 문제는 일반적으로 해당 VM에 대 한 잠금이 설정 된 VHD에서 VM을 만든 경우에 나타납니다. 이 VHD에서 할당 된 VM이 없는지 확인 한 후 작업을 다시 시도 하세요. 이 문제가 계속 되 면 지원 티켓을 엽니다. [파트너 센터 지원을](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support)참조 하세요.
