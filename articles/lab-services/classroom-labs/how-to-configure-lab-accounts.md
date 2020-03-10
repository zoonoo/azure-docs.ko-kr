---
title: Azure Lab Services에서 랩 계정 구성 Microsoft Docs
description: 이 문서에서는 Azure Lab Services에서 랩 계정을 만들거나 모든 랩 계정을 보거나 랩 계정을 삭제 하는 방법을 설명 합니다.
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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: fa9dba62b3b58687ec6a2bfc29e8722f7016b679
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361004"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Azure Lab Services에서 랩 계정 구성 
Azure Lab Services에서 랩 계정은 교실 labs와 같은 관리 되는 랩 형식에 대 한 컨테이너입니다. 관리자는 Azure Lab Services를 사용하여 랩 계정을 설정하고 계정에 랩을 만들 수 있는 랩 소유자에게 액세스 권한을 제공합니다. 

이 문서에서는 다음 작업을 수행 하는 방법을 보여 줍니다. 

- 랩에서 Vm의 주소 범위 지정
- 연결을 끊을 때 Vm의 자동 종료 구성

## <a name="specify-an-address-range-for-vms-in-the-lab"></a>랩에서 Vm의 주소 범위 지정
다음 절차에는 랩에서 Vm에 대 한 주소 범위를 지정 하는 단계가 있습니다. 이전에 지정한 범위를 업데이트 하는 경우 수정 된 주소 범위는 변경 후에 생성 된 Vm에만 적용 됩니다. 

유의 해야 하는 주소 범위를 지정할 때 몇 가지 제한 사항이 있습니다. 

- 접두사는 23 보다 작거나 같아야 합니다. 
- 가상 네트워크가 랩 계정으로 피어 링 되는 경우 제공 된 주소 범위는 피어 링 가상 네트워크의 주소 범위와 겹칠 수 없습니다.

1. **랩 계정** 페이지의 왼쪽 메뉴에서 **랩 설정** 을 선택 합니다.
2. **주소 범위** 필드에서 랩에서 생성 될 vm의 주소 범위를 지정 합니다. 주소 범위는 클래스 없는 CIDR (도메인 간 라우팅) 표기법 (예: 10.20.0.0/23)에 있어야 합니다. 랩의 가상 머신은 이 주소 범위에서 만들어집니다.
3. 도구 모음에서 **저장**을 선택합니다. 

    ![주소 범위 구성](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)


## <a name="automatic-shutdown-of-vms-on-disconnect"></a>연결을 끊을 때 Vm 자동 종료
원격 데스크톱 연결을 끊은 후 Windows 랩 Vm (템플릿 또는 학생)의 자동 종료를 사용 하거나 사용 하지 않도록 설정할 수 있습니다. 또한 사용자가 자동으로 종료 되기 전에 사용자가 다시 연결 될 때까지 Vm에서 대기할 기간을 지정할 수 있습니다.

![랩 계정에서 자동 종료 설정](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

이 설정은 랩 계정에 생성 된 모든 랩에 적용 됩니다. 랩 소유자는 랩 수준에서이 설정을 재정의할 수 있습니다. 랩 계정에서이 설정을 변경 하면 변경 후에 생성 된 랩에서만 영향을 받습니다.

랩 소유자가 랩 수준에서이 설정을 구성할 수 있는 방법에 대해 알아보려면 [이 문서](how-to-enable-shutdown-disconnect.md) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [랩 작성자가 랩 위치를 선택 하도록 허용](allow-lab-creator-pick-lab-location.md)
- [랩의 네트워크를 피어 가상 네트워크에 연결](how-to-connect-peer-virtual-network.md)
- [공유 이미지 갤러리를 랩에 연결](how-to-attach-detach-shared-image-gallery.md)
- [사용자를 랩 소유자로 추가](how-to-add-user-lab-owner.md)
- [랩에 대 한 방화벽 설정 보기](how-to-configure-firewall-settings.md)
