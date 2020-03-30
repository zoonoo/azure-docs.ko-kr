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
ms.openlocfilehash: 3417bf0bd4ae1e0aa670f9fbfcc1fbbfeb372972
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471546"
---
액세스 권한을 부여하기 전에 사용자에게 두 번째 인증 요소에 대한 메시지가 표시되도록 하려면 MFA(Azure 다단계 인증)를 구성할 수 있습니다. 사용자별로 MFA를 구성하거나 [조건부 액세스를](../articles/active-directory/conditional-access/overview.md)통해 MFA를 활용할 수 있습니다.

* 사용자당 MFA는 추가 비용 없이 활성화할 수 있습니다. 사용자당 MFA를 사용하도록 설정하면 Azure AD 테넌트에 연결된 모든 응용 프로그램에 대해 두 번째 요소 인증을 묻는 메시지가 표시됩니다. 단계는 [옵션 1을](#peruser) 참조하십시오.
* 조건부 액세스를 사용하면 두 번째 요소를 승격하는 방법을 보다 세밀하게 제어할 수 있습니다. MFA를 VPN에만 할당할 수 있으며 Azure AD 테넌트에 연결된 다른 응용 프로그램을 제외할 수 있습니다. 단계는 [옵션 2를](#conditional) 참조하십시오.