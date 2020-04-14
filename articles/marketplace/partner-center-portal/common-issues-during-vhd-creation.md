---
title: 일반적인 VHD 만들기 문제(FAQ)
description: 가상 하드 디스크(VHD)를 만들 때 자주 묻는 일반적인 문제에 대해 질문합니다.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
ms.date: 04/09/2020
ms.openlocfilehash: a47bece4df7263dbc29ec19510c3ea422bb3f4cf
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266267"
---
# <a name="common-issues-during-vhd-creation"></a>VHD 생성 중 일반적인 문제

> [!NOTE]
> Azure VM 오퍼의 관리를 클라우드 파트너 포털에서 파트너 센터로 이전합니다. 오퍼가 마이그레이션될 때까지 클라우드 파트너 포털의 [FaQ(VHD 생성) 동안 일반적인 문제의](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues) 지침을 계속 따라 오퍼를 관리하십시오.

자주 묻는 질문(FAQ)은 Azure 가상 컴퓨터 오퍼에 대한 VHD(가상 하드 디스크)를 만들 때 발생할 수 있는 일반적인 문제를 다룹니다.

## <a name="how-do-i-create-a-vm-from-the-azure-portal-using-a-vhd-in-premium-storage"></a>프리미엄 저장소에서 VHD를 사용하여 Azure 포털에서 VM을 만들려면 어떻게 해야 합니까?

Azure Marketplace는 현재 관리되는 저장소 또는 Azure 프리미엄 저장소의 이미지에서 VM 오퍼 를 만드는 것을 지원하지 않습니다. 자세한 내용은 [Azure 관리 디스크 개요를](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)참조하십시오.

## <a name="can-i-use-generation-2-vms-for-offers"></a>오퍼에 2세대 VM을 사용할 수 있습니까?

아니요, 1세대 VHD만 지원됩니다. 그러나 현재 Microsoft Azure 플랫폼 팀과 협력하여 2세대 VM에 대한 지원을 조사하고 있습니다. 자세한 내용은 [Hyper-V에서 1세대 또는 2세대를 만들어야 합니까?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="how-do-i-change-the-name-of-the-host"></a>호스트의 이름을 변경하려면 어떻게 해야 하나요?

열리지 않습니다. VM을 만든 후에는 소유자를 포함한 사용자가 호스트 이름을 업데이트할 수 없습니다.

## <a name="how-do-i-reset-the-remote-desktop-service-or-its-sign-in-password"></a>원격 데스크톱 서비스 또는 로그인 암호를 재설정하려면 어떻게 해야 합니까?

이 문서에서는 Windows 및 Linux 기반 VM에 대한 RDS 재설정을 수행하는 방법을 설명합니다.

* [Windows VM에서 원격 데스크톱 서비스 또는 해당 로그인 암호를 다시 설정하는 방법](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
* [VMAccess 확장을 사용하여 Linux VM 암호 또는 SSH 키 재설정, SSH 구성 수정, 디스크 일관성 검사를 수행하는 방법](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)

## <a name="how-do-i-generate-new-ssh-certificates"></a>새 SSH 인증서를 생성하려면 어떻게 해야 합니까?

인증서 생성은 Azure [VM 이미지 인증에](https://aks.ms/CertifyVMimage)설명되어 있습니다.

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>VM과 함께 작동하도록 VPN(가상 사설망)을 구성하려면 어떻게 해야 합니까?

Azure 리소스 관리자 배포 모델을 사용하는 경우 세 가지 옵션이 있습니다.

* [Azure Portal을 사용하여 경로 기반 VPN 게이트웨이 만들기](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
* [Azure PowerShell을 사용하여 경로 기반 VPN 게이트웨이 만들기](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
* [CLI를 사용하여 경로 기반 VPN 게이트웨이 만들기](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Azure 기반 VM에서 Microsoft 서버 소프트웨어를 실행하기 위한 Microsoft 지원 정책은 무엇인가요?

Microsoft [Azure 가상 컴퓨터에 대한 Microsoft 서버 소프트웨어 지원에서](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)자세한 내용을 찾을 수 있습니다.

## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>가상 머신에 연결되는 고유 식별자가 있나요?

예, 가상 머신이 Azure에서 호스팅되는 경우에는 있습니다. Azure는 생성된 각 새 VM 리소스에 [Azure 가상 시스템 고유 ID라는](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/)고유 식별자를 할당합니다. 자세한 내용은 Azure 가상 시스템 고유 ID를 참조하십시오. [목록 API를](https://docs.microsoft.com/rest/api/compute/virtualmachines/list)통해 이 식별자를 얻을 수도 있습니다.

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>VM에서 시작 작업에서 사용자 지정 스크립트 확장을 관리하려면 어떻게 해야 합니까?

Azure PowerShell 모듈, Azure 리소스 관리자 템플릿 및 Windows 시스템의 문제 해결 단계를 사용하여 사용자 지정 스크립트 확장 사용에 대한 자세한 내용은 [Windows용 사용자 지정 스크립트 확장](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)을 참조하십시오.

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Azure 마켓플레이스에서 32비트 응용 프로그램 또는 서비스가 지원되나요?

일반적으로 그렇지 않습니다. Azure VM에 지원되는 운영 체제 및 표준 서비스는 모두 64비트입니다. 대부분의 64비트 운영 체제는 이전 버전의 호환성을 위해 32비트 버전의 응용 프로그램을 지원하지만 VM 솔루션의 일부로 32비트 응용 프로그램을 사용하는 것은 지원되지 않으며 매우 권장되지 않습니다. 응용 프로그램을 64비트 프로젝트로 다시 만듭니다.

자세한 내용은 다음 문서를 참조하세요.

* [32비트 애플리케이션 실행](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
* [Azure 가상 머신에 32비트 운영 체제 지원](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
* [Microsoft Azure 가상 머신에 대한 Microsoft 서버 소프트웨어 지원](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>오류: VHD가 이미 이미지 리포지토리에 리소스로 등록되어 있습니다.

VHD에서 이미지를 만들려고 할 때마다 Azure PowerShell에서 "VHD가 이미 리소스로 이미지 리포지토리에 등록되어 있습니다"라는 오류가 발생합니다. 이전에는 이미지를 만들지 않았고 Azure에서 이 이름의 이미지를 찾지 못했습니다. 이 문제를 해결하려면 어떻게 해야 하나요?

이 문제는 일반적으로 잠금이 있는 VHD에서 VM을 만든 경우에 나타납니다. 이 VHD에서 할당된 VM이 없는지 확인한 다음 작업을 다시 시도합니다. 이 문제가 계속되면 지원 티켓을 엽니다. [파트너 센터에 대한 지원을](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support)참조하십시오.
