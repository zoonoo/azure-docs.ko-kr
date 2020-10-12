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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "77471546"
---
액세스 권한을 부여 하기 전에 사용자에 게 두 번째 인증 단계를 묻는 메시지가 표시 되도록 하려면 MFA (Azure Multi-Factor Authentication)를 구성할 수 있습니다. 사용자 단위로 MFA를 구성 하거나 [조건부 액세스](../articles/active-directory/conditional-access/overview.md)를 통해 mfa를 활용할 수 있습니다.

* 사용자 당 MFA는 추가 비용 없이 사용 하도록 설정할 수 있습니다. 사용자 당 MFA를 사용 하도록 설정 하면 Azure AD 테 넌 트에 연결 된 모든 응용 프로그램에 대해 두 번째 단계 인증을 요구 하는 메시지가 사용자에 게 표시 됩니다. 단계는 [옵션 1](#peruser) 을 참조 하세요.
* 조건부 액세스를 사용 하면 두 번째 단계를 승격 하는 방법을 보다 세밀 하 게 제어할 수 있습니다. 이를 통해 VPN에 대 한 MFA 할당을 허용 하 고 Azure AD 테 넌 트에 연결 된 다른 응용 프로그램을 제외할 수 있습니다. 단계는 [옵션 2](#conditional) 를 참조 하세요.