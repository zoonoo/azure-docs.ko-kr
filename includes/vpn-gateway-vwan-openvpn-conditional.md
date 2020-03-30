---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 410570302eec418f1e4bcb75d6413936a96b5171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471549"
---
조건부 액세스를 사용하면 응용 프로그램별로 세분화된 액세스 제어를 수행할 수 있습니다. 조건부 액세스를 사용하려면 조건부 액세스 규칙의 적용을 받는 사용자에게 Azure AD Premium 1 또는 그 이상 라이선스가 적용되어야 합니다.

1. 엔터프라이즈 응용 프로그램으로 이동 - 모든 응용 프로그램 페이지를 클릭하고 **Azure VPN을** **클릭합니다.**

   - **조건부 액세스**를 클릭합니다.
   - **새 정책**을 클릭하여 **새로 만들기** 창을 엽니다.
2. **새** 창에서 **할당 -> 사용자 및 그룹으로**이동합니다. 사용자 **및 그룹 ->** **포함** 탭:

   - **사용자 및 그룹 선택**을 클릭합니다.
   - **사용자 및 그룹**확인 .
   - 선택을 **클릭하여** MFA의 영향을 받는 사용자 그룹 또는 집합을 선택합니다.
   - **완료**를 클릭합니다.

   ![할당](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. **새** 창에서 액세스 컨트롤 **-> 권한 부여** 창으로 이동합니다.

   - 권한 부여 액세스 권한을 **클릭합니다.**
   - **다단계 인증 필요를**클릭합니다.
   - **선택한 모든 컨트롤 필요를**클릭합니다.
   - **선택**을 클릭합니다.
   
   ![권한 부여 액세스 - MFA](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. 정책 사용 섹션에서 다음을 **수행합니다.**

   - **을 선택합니다.**
   - **만들기**를 클릭합니다.

   ![정책 사용](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)