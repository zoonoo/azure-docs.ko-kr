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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "77471549"
---
조건부 액세스를 통해 응용 프로그램 별로 세분화 된 액세스 제어를 사용할 수 있습니다. 조건부 액세스를 사용 하려면 조건부 액세스 규칙이 적용 되는 사용자에 게 하나 이상의 라이선스가 적용 Azure AD Premium 해야 합니다.

1. **엔터프라이즈 응용 프로그램-모든 응용 프로그램** 페이지로 이동 하 고 **Azure VPN**을 클릭 합니다.

   - **조건부 액세스**를 클릭 합니다.
   - **새 정책**을 클릭하여 **새로 만들기** 창을 엽니다.
2. **새** 창에서 **할당-> 사용자 및 그룹**으로 이동 합니다. **사용자 및 그룹->** **포함** 탭:

   - **사용자 및 그룹 선택**을 클릭합니다.
   - **사용자 및 그룹**을 확인 합니다.
   - **선택** 을 클릭 하 여 MFA의 영향을 받을 그룹 또는 사용자 집합을 선택 합니다.
   - **완료**를 클릭합니다.

   ![할당](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. **새** 창에서 **액세스 제어-> 부여** 창으로 이동 합니다.

   - **액세스 권한 부여**를 클릭 합니다.
   - **Multi-factor Authentication 필요**를 클릭 합니다.
   - **선택한 모든 컨트롤 필요를**클릭 합니다.
   - **선택**을 클릭합니다.
   
   ![액세스 권한 부여-MFA](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. **정책 사용** 섹션에서 다음을 수행 합니다.

   - **켜기**를 선택합니다.
   - **만들기**를 클릭합니다.

   ![정책 사용](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)