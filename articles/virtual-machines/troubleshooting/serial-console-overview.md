---
title: Azure 직렬 콘솔 | Microsoft Docs
description: Azure 직렬 콘솔을 사용하면 SSH 또는 RDP를 사용할 수 없을 때 VM에 연결할 수 있습니다.
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 28c5a3085d84b25deb7c5ee09a9c9cc4d7a06819
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374068"
---
# <a name="azure-serial-console"></a>Azure 직렬 콘솔

Azure Portal의 직렬 콘솔은 Linux 또는 Windows를 실행하는 VM(가상 머신) 및 가상 머신 확장 집합 인스턴스의 텍스트 기반 콘솔에 대한 액세스를 제공합니다. 이 직렬 연결은 VM 또는 가상 머신 확장 집합 인스턴스의 ttyS0 또는 COM1 직렬 포트에 연결되며, 네트워크 또는 운영 체제 상태와 독립적으로 액세스를 제공합니다. 직렬 콘솔은 Azure Portal을 통해서만 액세스할 수 있으며 VM 또는 가상 머신 확장 집합에 대한 기여자 이상의 액세스 역할을 가진 사용자에게만 허용됩니다.

직렬 콘솔은 VM 및 가상 머신 확장 집합 인스턴스에 대해 동일한 방식으로 작동합니다. 이 문서에서는 VM에 대한 모든 언급에는 달리 명시되지 않는 한 가상 머신 확장 집합 인스턴스가 암시적으로 포함됩니다.

직렬 콘솔은 일반적으로 글로벌 Azure 지역 및 Azure Government의 공개 미리 보기에서 사용할 수 있습니다. Azure 중국 클라우드에서는 아직 사용할 수 없습니다.

## <a name="prerequisites-to-access-the-azure-serial-console"></a>Azure 직렬 콘솔에 액세스하기 위한 필수 구성 요소
VM 또는 가상 머신 확장 집합 인스턴스에서 직렬 콘솔에 액세스하려면 다음이 필요합니다.

- VM에 대해 부트 진단을 사용하도록 설정해야 합니다.
- 암호 인증을 사용하는 사용자 계정은 VM 내에 존재해야 합니다. VM 액세스 확장의 [암호 재설정](../extensions/vmaccess.md#reset-password) 함수를 사용하여 암호 기반 사용자를 만들 수 있습니다. **지원 + 문제 해결** 섹션에서 **암호 재설정**을 선택합니다.
- 직렬 콘솔에 액세스하는 Azure 계정에는 VM 및 [부트 진단](boot-diagnostics.md) 스토리지 계정 모두에 대해 [Virtual Machine Contributor 역할](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)이 있어야 합니다.
- 클래식 배포는 지원되지 않습니다. VM 또는 가상 머신 확장 집합 인스턴스는 Azure Resource Manager 배포 모델을 사용해야 합니다.

> [!NOTE]
> 직렬 콘솔은 현재 관리 되는 부트 진단 저장소 계정과 호환 되지 않습니다. 직렬 콘솔을 사용 하려면 사용자 지정 저장소 계정을 사용 하 고 있는지 확인 합니다.

## <a name="get-started-with-the-serial-console"></a>직렬 콘솔 시작
VM 및 가상 머신 확장 집합용 직렬 콘솔은 Azure Portal을 통해서만 액세스할 수 있습니다.

### <a name="serial-console-for-virtual-machines"></a>Virtual Machines용 직렬 콘솔
VM용 직렬 콘솔은 Azure Portal의 **지원 + 문제 해결** 섹션 내에서 **직렬 콘솔**을 클릭하는 것만큼 간단합니다.
  1. [Azure Portal](https://portal.azure.com)을 엽니다.

  1. **모든 리소스**로 이동하여 Virtual Machine을 선택합니다. VM에 대한 개요 페이지가 열립니다.

  1. **지원 + 문제 해결** 섹션까지 아래로 스크롤하여 **직렬 콘솔**을 선택합니다. 직렬 콘솔이 있는 새 창이 열리고 연결이 시작됩니다.

     ![Linux 직렬 콘솔 창](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets용 직렬 콘솔
직렬 콘솔은 가상 머신 확장 집합에 사용할 수 있으며, 확장 집합 내의 각 인스턴스에서 액세스할 수 있습니다. **직렬 콘솔** 단추를 보기 전에 가상 머신 확장 집합의 개별 인스턴스로 이동해야 합니다. 가상 머신 확장 집합에 부트 진단이 활성화되어 있지 않은 경우 가상 머신 확장 집합 모델을 업데이트하여 부트 진단을 활성화한 다음, 직렬 콘솔에 액세스하기 위해 모든 인스턴스를 새 모델로 업그레이드해야 합니다.
  1. [Azure Portal](https://portal.azure.com)을 엽니다.

  1. **모든 리소스**로 이동하여 Virtual Machine Scale Set을 선택합니다. 가상 머신 확장 집합의 개요 페이지가 열립니다.

  1. **인스턴스**로 이동

  1. 가상 머신 확장 집합 인스턴스 선택

  1. **지원 + 문제 해결** 섹션에서 **직렬 콘솔**을 선택합니다. 직렬 콘솔이 있는 새 창이 열리고 연결이 시작됩니다.

     ![Linux 가상 머신 확장 집합 직렬 콘솔](./media/virtual-machines-serial-console/vmss-start-console.gif)


### <a name="tls-12-in-serial-console"></a>직렬 콘솔의 TLS 1.2
직렬 콘솔은 TLS 1.2 엔드투엔드를 사용하여 서비스 내의 모든 통신을 보호합니다. 직렬 콘솔은 사용자 관리 부트 진단 스토리지 계정에 종속되며, TLS 1.2는 스토리지 계정에 대해 별도로 구성해야 합니다. 이를 수행하기 위한 지침은 [여기](../../storage/common/transport-layer-security-configure-minimum-version.md)에 나와 있습니다.

## <a name="advanced-uses-for-serial-console"></a>직렬 콘솔의 고급 용도
VM에 대한 콘솔 액세스 외에도 다음 용도로 Azure 직렬 콘솔을 사용할 수 있습니다.
* [VM에 시스템 요청 명령](./serial-console-nmi-sysrq.md) 보내기
* [VM에 마스크 불가능 인터럽트](./serial-console-nmi-sysrq.md) 보내기
* 정상적으로 [VM을 다시 부팅하거나 강제로 전원을 껐다가 다시 켜기](./serial-console-power-options.md)


## <a name="next-steps"></a>다음 단계
추가 직렬 콘솔 설명서는 사이드바에서 확인할 수 있습니다.
- [Linux VM용 직렬 콘솔](./serial-console-linux.md)에 대한 자세한 정보를 확인할 수 있습니다.
- [Windows VM용 직렬 콘솔](./serial-console-windows.md)에 대한 자세한 정보를 확인할 수 있습니다.
