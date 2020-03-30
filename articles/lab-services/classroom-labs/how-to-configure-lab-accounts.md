---
title: Azure 랩 서비스에서 랩 계정 구성 | 마이크로 소프트 문서
description: 이 문서에서는 랩 계정을 만들고, 모든 랩 계정을 보거나, Azure Lab Services에서 랩 계정을 삭제하는 방법을 설명합니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284305"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Azure 랩 서비스에서 랩 계정 구성 
Azure Lab Services에서 랩 계정은 클래스룸 랩과 같은 관리되는 랩 유형에 대한 컨테이너입니다. 관리자는 Azure Lab Services를 사용하여 랩 계정을 설정하고 계정에 랩을 만들 수 있는 랩 소유자에게 액세스 권한을 제공합니다. 

이 문서에서는 다음 작업을 수행하는 방법을 보여 주며 다음과 같은 작업을 수행합니다. 

- 랩에서 VM에 대한 주소 범위 지정
- 연결 해제 시 VM 자동 종료 구성

## <a name="specify-an-address-range-for-vms-in-the-lab"></a>랩에서 VM에 대한 주소 범위 지정
다음 절차에는 랩의 VM에 대한 주소 범위를 지정하는 단계가 있습니다. 이전에 지정한 범위를 업데이트하는 경우 수정된 주소 범위는 변경 한 후 생성된 VM에만 적용됩니다. 

다음은 염두에 두어야 할 주소 범위를 지정할 때 몇 가지 제한 사항이 있습니다. 

- 접두사는 23보다 작거나 같아야 합니다. 
- 가상 네트워크가 랩 계정을 피어로 피어면 제공된 주소 범위가 피어있는 가상 네트워크의 주소 범위와 겹칠 수 없습니다.

1. 랩 **계정** 페이지에서 왼쪽 메뉴에서 **랩 설정을 선택합니다.**
2. 주소 **범위** 필드의 경우 랩에서 만들 VM의 주소 범위를 지정합니다. 주소 범위는 클래스리스 도메인 간 라우팅(CIDR) 표기형(예: 10.20.0.0/23)이어야 합니다. 랩의 가상 머신은 이 주소 범위에서 만들어집니다.
3. 도구 모음에서 **저장**을 선택합니다. 

    ![주소 범위 구성](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)


## <a name="automatic-shutdown-of-vms-on-disconnect"></a>연결 해제 시 VM 자동 종료
원격 데스크톱 연결이 끊어진 후 Windows 랩 VM(템플릿 또는 학생)의 자동 종료를 사용하거나 사용하지 않도록 설정할 수 있습니다. VM이 자동으로 종료되기 전에 사용자가 다시 연결될 때까지 기다려야 하는 시간도 지정할 수 있습니다.

![랩 계정의 자동 종료 설정](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

이 설정은 랩 계정에서 만든 모든 랩에 적용됩니다. 랩 소유자는 랩 수준에서 이 설정을 재정의할 수 있습니다. 랩 계정에서 이 설정을 변경하면 변경 한 후에 만들어진 랩에만 영향을 미칩니다.

랩 소유자가 랩 수준에서 이 설정을 구성하는 방법에 대해 알아보려면 [이 문서를](how-to-enable-shutdown-disconnect.md) 참조하세요.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [랩 작성자가 랩 위치를 선택하도록 허용](allow-lab-creator-pick-lab-location.md)
- [랩 네트워크를 피어 가상 네트워크로 연결](how-to-connect-peer-virtual-network.md)
- [랩에 공유 이미지 갤러리 첨부](how-to-attach-detach-shared-image-gallery.md)
- [사용자를 랩 소유자로 추가](how-to-add-user-lab-owner.md)
- [랩의 방화벽 설정 보기](how-to-configure-firewall-settings.md)
