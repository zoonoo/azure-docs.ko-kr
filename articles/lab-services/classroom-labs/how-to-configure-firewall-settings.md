---
title: Azure Lab Services에 대한 방화벽 설정
description: 방화벽 규칙에 정보를 추가할 수 있도록 랩에서 가상 시스템의 공용 IP 주소 및 포트 번호 범위를 확인하는 방법을 알아봅니다.
author: emaher
ms.author: enewman
ms.date: 02/14/2020
ms.topic: article
ms.service: lab-services
ms.openlocfilehash: fbd45af0c9b94f04fdaad9d9b5c8214a91a8db91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443461"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Azure Lab Services에 대한 방화벽 설정

각 조직이나 학교는 자신의 요구에 가장 적합한 방식으로 자체 네트워크를 설정합니다.  때로는 자신의 네트워크 외부의 컴퓨터에 대한 원격 데스크톱 프로토콜 (RDP) 또는 보안 셸 (ssh) 연결을 차단하는 방화벽 규칙을 설정하는 것이 포함됩니다.  Azure Lab 서비스는 퍼블릭 클라우드에서 실행되므로 캠퍼스 네트워크에서 연결할 때 학생이 VM에 액세스할 수 있도록 하기 위해 몇 가지 추가 구성이 필요할 수 있습니다.

각 랩은 단일 공용 IP 주소와 여러 포트를 사용합니다.  템플릿 VM과 학생 VM 모두의 모든 VM은 이 공용 IP 주소를 사용합니다.  공용 IP 주소는 랩 수명 동안 변경되지 않습니다.  그러나 각 VM에는 다른 포트 번호가 있습니다.  포트 번호는 49152에서 65535까지 다양합니다.  공용 IP 주소와 포트 번호의 조합은 강사와 학생을 올바른 VM에 연결하는 데 사용됩니다.  이 문서에서는 랩에서 사용하는 특정 공용 IP 주소를 찾는 방법을 설명합니다.  이 정보는 학생이 VM에 액세스할 수 있도록 인바운드 및 아웃바운드 방화벽 규칙을 업데이트하는 데 사용할 수 있습니다.

>[!IMPORTANT]
>각 랩에는 공용 IP 주소가 다릅니다.

## <a name="find-public-ip-for-a-lab"></a>랩용 공용 IP 찾기

각 랩의 공용 IP 주소는 랩 서비스 랩 계정의 모든 랩 페이지에 **나열됩니다.**  **모든 랩** 페이지를 찾는 방법에 대한 자세한 내용은 랩 [계정에서 랩을 관리하는 방법을 참조하세요.](how-to-manage-lab-accounts.md#view-and-manage-labs-in-the-lab-account)  

> [!div class="mx-imgBorder"]
> ![모든 랩 페이지](../media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>랩의 템플릿 컴퓨터가 아직 게시되지 않은 경우 공용 IP 주소가 표시되지 않습니다.

## <a name="conclusion"></a>결론

이제 랩의 공용 IP 주소를 알 수 있습니다.  공용 IP 주소 및 포트 범위 49152-65535에 대한 조직의 방화벽에 대해 인바운드 및 아웃바운드 규칙을 만들 수 있습니다.  규칙이 업데이트되면 학생은 네트워크 방화벽 액세스를 차단하지 않고 VM에 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [랩 작성자가 랩 위치를 선택하도록 허용](allow-lab-creator-pick-lab-location.md)
- [랩 네트워크를 피어 가상 네트워크로 연결](how-to-connect-peer-virtual-network.md)
- [랩에 공유 이미지 갤러리 첨부](how-to-attach-detach-shared-image-gallery.md)
- [사용자를 랩 소유자로 추가](how-to-add-user-lab-owner.md)
- [랩의 방화벽 설정 보기](how-to-configure-firewall-settings.md)
- [랩에 대한 다른 설정 구성](how-to-configure-lab-accounts.md)
