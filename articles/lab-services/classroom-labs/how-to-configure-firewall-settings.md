---
title: Azure Lab Services에 대한 방화벽 설정
description: 정보를 방화벽 규칙에 추가할 수 있도록 랩의 가상 컴퓨터에 대 한 공용 IP 주소와 포트 번호 범위를 확인 하는 방법에 대해 알아봅니다.
author: emaher
ms.author: enewman
ms.date: 02/14/2020
ms.topic: article
ms.service: lab-services
ms.openlocfilehash: fbd45af0c9b94f04fdaad9d9b5c8214a91a8db91
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77443461"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Azure Lab Services에 대한 방화벽 설정

각 조직 또는 학교는 자신의 요구에 가장 적합 한 방식으로 자체 네트워크를 설정 합니다.  경우에 따라 자체 네트워크 외부의 컴퓨터에 대 한 원격 데스크톱 프로토콜 (rdp) 또는 Secure Shell (ssh) 연결을 차단 하는 방화벽 규칙을 설정 하는 경우가 있습니다.  Azure Lab Services 공용 클라우드에서 실행 되기 때문에, 학생이 본사 네트워크에서 연결할 때 해당 VM에 액세스할 수 있도록 몇 가지 추가 구성이 필요할 수 있습니다.

각 랩에서는 단일 공용 IP 주소와 여러 포트를 사용 합니다.  템플릿 VM 및 학생 Vm 모두에 대 한 모든 Vm은이 공용 IP 주소를 사용 합니다.  공용 IP 주소는 랩의 수명 동안 변경 되지 않습니다.  그러나 각 VM은 서로 다른 포트 번호를 갖게 됩니다.  포트 번호의 범위는 49152에서 65535 까지입니다.  공용 IP 주소와 포트 번호의 조합은 강사와 학생을 올바른 VM에 연결 하는 데 사용 됩니다.  이 문서에서는 랩에서 사용 되는 특정 공용 IP 주소를 찾는 방법을 설명 합니다.  이러한 정보는 학생 들이 Vm에 액세스할 수 있도록 인바운드 및 아웃 바운드 방화벽 규칙을 업데이트 하는 데 사용할 수 있습니다.

>[!IMPORTANT]
>각 랩에서는 서로 다른 공용 IP 주소가 있습니다.

## <a name="find-public-ip-for-a-lab"></a>랩에 대 한 공용 IP 찾기

각 랩에 대 한 공용 IP 주소는 랩 서비스 랩 계정의 **모든** 랩 페이지에 나열 됩니다.  **모든 실습** 페이지를 찾는 방법에 대 한 지침은 [랩 계정에서 랩을 관리](how-to-manage-lab-accounts.md#view-and-manage-labs-in-the-lab-account)하는 방법을 참조 하세요.  

> [!div class="mx-imgBorder"]
> ![모든 랩 페이지](../media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>랩의 템플릿 컴퓨터가 아직 게시 되지 않은 경우 공용 IP 주소가 표시 되지 않습니다.

## <a name="conclusion"></a>결론

이제 랩에 대 한 공용 IP 주소를 알고 있습니다.  공용 ip 주소 및 포트 범위 49152-65535에 대 한 조직의 방화벽에 대해 인바운드 및 아웃 바운드 규칙을 만들 수 있습니다.  규칙을 업데이트 한 후에는 학생 들이 네트워크 방화벽 액세스를 차단 하지 않고 해당 Vm에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [랩 작성자가 랩 위치를 선택하도록 허용](allow-lab-creator-pick-lab-location.md)
- [랩의 네트워크를 피어 가상 네트워크에 연결](how-to-connect-peer-virtual-network.md)
- [공유 이미지 갤러리를 랩에 연결](how-to-attach-detach-shared-image-gallery.md)
- [사용자를 랩 소유자로 추가](how-to-add-user-lab-owner.md)
- [랩에 대 한 방화벽 설정 보기](how-to-configure-firewall-settings.md)
- [랩에 대 한 기타 설정 구성](how-to-configure-lab-accounts.md)
