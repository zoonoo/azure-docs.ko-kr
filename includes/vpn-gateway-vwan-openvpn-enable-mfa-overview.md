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
ms.openlocfilehash: 8fba8aa577bcb3b5ef44d57c388a1f1de7494782
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95995621"
---
액세스 권한을 부여하기 전에 사용자에게 두 번째 인증 단계를 묻는 메시지가 표시되도록 하려면 Azure AD MFA(Multi-Factor Authentication)를 구성하면 됩니다. 사용자별로 MFA를 구성하거나 [조건부 액세스](../articles/active-directory/conditional-access/overview.md)를 통해 MFA를 활용할 수 있습니다.

* 사용자별 MFA는 추가 비용 없이 사용하도록 설정할 수 있습니다. 사용자별 MFA를 사용하도록 설정하면 Azure AD 테넌트에 연결된 모든 애플리케이션에 대해 2단계 인증을 요구하는 메시지가 사용자에게 표시됩니다. 단계는 [옵션 1](#peruser)을 참조하세요.
* 조건부 액세스를 사용하면 두 번째 요소의 승격 방법을 보다 세밀하게 제어할 수 있습니다. VPN에만 MFA를 할당하고 Azure AD 테넌트에 연결된 다른 애플리케이션을 제외할 수 있습니다. 단계는 [옵션 2](#conditional)를 참조하세요.