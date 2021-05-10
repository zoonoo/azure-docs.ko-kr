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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "77471549"
---
조건부 액세스를 통해 애플리케이션별로 세분화된 액세스 제어가 가능합니다. 조건부 액세스를 사용하려면 조건부 액세스 규칙이 적용되는 사용자에게 Azure AD Premium 1 이상의 라이선스를 적용해야 합니다.

1. **엔터프라이즈 애플리케이션 - 모든 애플리케이션** 페이지로 이동하여 **Azure VPN** 을 클릭합니다.

   - **조건부 액세스** 를 클릭합니다.
   - **새 정책** 을 클릭하여 **새로 만들기** 창을 엽니다.
2. **새로 만들기** 창에서 **할당 -> 사용자 및 그룹** 으로 이동합니다. **사용자 및 그룹 ->** **포함** 탭에서:

   - **사용자 및 그룹 선택** 을 클릭합니다.
   - **사용자 및 그룹** 을 선택합니다.
   - **선택** 을 클릭하여 MFA의 영향을 받는 그룹 또는 사용자 세트를 선택합니다.
   - **Done** 을 클릭합니다.

   ![할당](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. **새로 만들기** 창에서 **액세스 제어 -> 권한 부여** 창으로 이동합니다.

   - **액세스 권한 부여** 를 클릭합니다.
   - **다단계 인증 필요** 를 클릭합니다.
   - **선택한 모든 컨트롤 필요** 를 클릭합니다.
   - **선택** 을 클릭합니다.
   
   ![액세스 권한 부여 - MFA](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. **정책 사용** 섹션에서:

   - **켜기** 를 선택합니다.
   - **만들기** 를 클릭합니다.

   ![정책 사용](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)