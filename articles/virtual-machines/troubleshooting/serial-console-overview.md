---
title: Azure 직렬 콘솔 | 마이크로 소프트 문서
description: Azure 직렬 콘솔을 사용하면 SSH 또는 RDP를 사용할 수 없는 경우 VM에 연결할 수 있습니다.
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
ms.openlocfilehash: 779bb88d15ea6c52f4399f17223b89916e22653d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267015"
---
# <a name="azure-serial-console"></a>Azure 직렬 콘솔

Azure 포털의 직렬 콘솔은 Linux 또는 Windows에서 실행되는 가상 컴퓨터(VM) 및 가상 시스템 집합 인스턴스에 대한 텍스트 기반 콘솔에 대한 액세스를 제공합니다. 이 직렬 연결은 VM 또는 가상 시스템 스케일 세트 인스턴스의 ttyS0 또는 COM1 직렬 포트에 연결되어 네트워크 또는 운영 체제 상태와 관계없이 액세스를 제공합니다. 직렬 콘솔은 Azure 포털을 사용하여 액세스할 수 있으며 VM 또는 가상 시스템 규모 집합에 대한 기여자의 액세스 역할이 있거나 그 이상인 사용자만 허용됩니다.

직렬 콘솔은 VM 및 가상 시스템 스케일 세트 인스턴스와 동일한 방식으로 작동합니다. 이 문서에서 VM에 대한 모든 언급에는 달리 명시되지 않는 한 가상 시스템 크기 집합 인스턴스가 암시적으로 포함됩니다.

> [!NOTE]
> 직렬 콘솔은 일반적으로 글로벌 Azure 리전과 Azure 정부의 공개 미리 보기에서 사용할 수 있습니다. Azure 중국 클라우드에서는 아직 사용할 수 없습니다.

## <a name="prerequisites-to-access-the-azure-serial-console"></a>Azure 직렬 콘솔에 액세스하기 위한 필수 구성 조건
VM 또는 가상 시스템 규모 집합 인스턴스에서 직렬 콘솔에 액세스하려면 다음이 필요합니다.

- VM에 대해 부팅 진단을 사용하도록 설정해야 합니다.
- 암호 인증을 사용하는 사용자 계정은 VM 내에 있어야 합니다. VM 액세스 확장의 암호 [재설정](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) 기능을 사용하여 암호 기반 사용자를 만들 수 있습니다. **지원 + 문제 해결** 섹션에서 **암호 재설정**을 선택합니다.
- 직렬 콘솔에 액세스하는 Azure 계정에는 VM 및 부팅 진단 저장소 계정에 대해 [가상 시스템 기여자 역할이](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) 있어야 [합니다.](boot-diagnostics.md)

> [!NOTE]
> 클래식 배포는 지원되지 않습니다. VM 또는 가상 시스템 집합 인스턴스는 Azure 리소스 관리자 배포 모델을 사용해야 합니다.

## <a name="get-started-with-the-serial-console"></a>직렬 콘솔 로 시작하기
VM 및 가상 시스템 규모 집합에 대한 직렬 콘솔은 Azure 포털을 통해서만 액세스할 수 있습니다.

### <a name="serial-console-for-virtual-machines"></a>가상 머신용 직렬 콘솔
VM용 직렬 콘솔은 Azure 포털의 **지원 + 문제 해결** 섹션에서 **직렬 콘솔을** 클릭하는 것만큼 간단합니다.
  1. Azure [포털을](https://portal.azure.com)엽니다.

  1. 모든 **리소스로** 이동하여 가상 컴퓨터를 선택합니다. VM에 대한 개요 페이지가 열립니다.

  1. **지원 + 문제 해결** 섹션까지 아래로 스크롤하여 **직렬 콘솔**을 선택합니다. 직렬 콘솔이 있는 새 창이 열리고 연결이 시작됩니다.

     ![리눅스 시리얼 콘솔 창](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>가상 머신 스케일 세트를 위한 직렬 콘솔
직렬 콘솔은 확장 집합 내의 각 인스턴스에서 액세스할 수 있는 가상 시스템 스케일 집합에 사용할 수 있습니다. **직렬 콘솔** 단추를 보기 전에 가상 시스템 규모 집합의 개별 인스턴스로 이동해야 합니다. 가상 시스템 규모 집합에 부팅 진단이 활성화되어 있지 않은 경우 부팅 진단을 사용하도록 가상 시스템 크기 집합 모델을 업데이트한 다음 모든 인스턴스를 새 모델로 업그레이드하여 직렬 콘솔에 액세스합니다.
  1. Azure [포털을](https://portal.azure.com)엽니다.

  1. 모든 **리소스로** 이동하여 가상 시스템 배율 집합을 선택합니다. 가상 시스템 축척 집합에 대한 개요 페이지가 열립니다.

  1. **인스턴스로** 이동

  1. 가상 시스템 스케일 세트 인스턴스 선택

  1. 지원 **+ 문제 해결** 섹션에서 **직렬 콘솔을**선택합니다. 직렬 콘솔이 있는 새 창이 열리고 연결이 시작됩니다.

     ![리눅스 가상 머신 스케일 세트 직렬 콘솔](./media/virtual-machines-serial-console/vmss-start-console.gif)

## <a name="advanced-uses-for-serial-console"></a>직렬 콘솔에 대한 고급 사용
VM에 대한 콘솔 액세스 외에도 Azure 직렬 콘솔을 다음 용도로 사용할 수도 있습니다.
* 시스템 [요청 명령을 VM으로](./serial-console-nmi-sysrq.md) 보내기
* [마스크할 수 없는 인터럽트를 VM으로](./serial-console-nmi-sysrq.md) 보내기
* [VM을 정상적으로 재부팅하거나 강제로 전원 을 껐다.](./serial-console-power-options.md)


## <a name="next-steps"></a>다음 단계
사이드바에서 추가 직렬 콘솔 설명서를 사용할 수 있습니다.
- 자세한 내용은 [Linux VM용 직렬 콘솔에 사용할](./serial-console-linux.md)수 있습니다.
- 자세한 내용은 [Windows VM용 직렬 콘솔에 사용할](./serial-console-windows.md)수 있습니다.
