---
title: Azure VMware Solution 점프 상자 만들기
description: Azure VMware Solution 점프 상자를 만드는 단계입니다.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 5d5a5d82cf6e70e7ad55bcfa10c7f85372131c87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578423"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-access-private-cloud.md -->

1. 리소스 그룹에서 **+ 추가**를 선택하고, **Microsoft Windows 10**을 검색하여 선택한 다음, **만들기**를 선택합니다.

   :::image type="content" source="../media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="점프 상자에 대한 새 Windows 10 VM을 추가합니다." border="true":::

1. 필드에서 필요한 정보를 입력한 다음, **검토 + 만들기**를 선택합니다. 

   필드에 대한 자세한 내용은 다음 표를 참조하세요.

   | 필드 | 값 |
   | --- | --- |
   | **구독** | 값은 리소스 그룹에 속하는 구독으로 미리 채워져 있습니다. |
   | **리소스 그룹** | 값은 이전 자습서에서 만든 현재 리소스 그룹에 대해 미리 채워져 있습니다.  |
   | **가상 머신 이름** | VM에 대한 고유한 이름을 입력합니다. |
   | **지역** | VM의 지리적 위치를 선택합니다. |
   | **가용성 옵션** | 선택된 기본값을 그대로 둡니다. |
   | **이미지** | VM 이미지를 선택합니다. |
   | **크기** | 기본 크기 값을 그대로 둡니다. |
   | **인증 유형**  | **암호**를 선택합니다. |
   | **사용자 이름** | VM에 로그온하기 위한 사용자 이름을 입력합니다. |
   | **암호** | VM에 로그온하기 위한 암호를 입력합니다. |
   | **암호 확인** | VM에 로그온하기 위한 암호를 입력합니다. |
   | **퍼블릭 인바운드 포트** | **없음**을 선택합니다. [없음]이 선택되면 [JIT 액세스](../../security-center/security-center-just-in-time.md#jit-configure)를 사용하여 VM에 액세스하려는 경우에만 VM에 대한 액세스를 제어할 수 있습니다.  |


1. 유효성 검사가 통과되면 **만들기**를 선택하여 가상 머신 만들기 프로세스를 시작합니다.

   :::image type="content" source="../media/tutorial-access-private-cloud/ss11-review-create-wjb01.png" alt-text="점프 상자에 대한 새 Windows 10 VM을 추가합니다." border="true":::