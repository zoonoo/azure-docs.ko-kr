---
title: Azure Active Directory B2C에서 소비자 등록 중에 이메일 확인 사용 안 함 | Microsoft Docs
description: Azure Active Directory B2C의 소비자 등록 중에 이메일 확인을 사용하지 않도록 설정하는 방법을 보여주는 토픽입니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8b50b59b6a1f99787b842923cd6ec77ee6500f0a
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66509527"
---
# <a name="disable-email-verification-during-consumer-sign-up-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 소비자 등록 중 이메일 확인 사용 안 함 
Azure AD(Azure Active Directory) B2C를 사용하도록 설정하면 소비자는 전자 메일 주소를 제공하고 로컬 계정을 만들어 애플리케이션에 등록할 수 있게 됩니다. Azure AD B2C는 등록 프로세스 중에 소비자에게 전자 메일 주소를 확인하도록 요구하여 전자 메일 주소의 유효성을 보장합니다. 또한 악의적인 자동화 프로세스를 통해 애플리케이션에 대한 가짜 계정이 생성되지 못하게 합니다.

일부 애플리케이션 개발자는 등록 프로세스 동안 전자 메일 확인을 건너뛰고 소비자에게 나중에 전자 메일 주소를 확인하도록 합니다. 이를 지원하는 경우 전자 메일 확인을 사용하지 않도록 Azure AD B2C를 구성할 수 있습니다. 이를 통해 등록 프로세스가 좀 더 원활해지고 개발자는 전자 메일 주소를 확인한 소비자와 아직 전자 메일 주소를 확인하지 않은 소비자를 구분할 수 있습니다.

기본적으로 가입 사용자 흐름에는 이메일 확인 기능이 켜져 있습니다. 해제하려면 다음 단계를 사용합니다.

1. **사용자 흐름**을 클릭합니다.
2. 사용자 흐름(예: "B2C_1_SiUp")을 클릭하여 엽니다. 
3. **페이지 레이아웃**을 클릭합니다.
4. **로컬 계정 등록 페이지**를 클릭합니다.
5. **사용자 특성** 섹션의 **이름** 열에 있는 **이메일 주소**를 클릭합니다.
6. **확인 필요** 아래에서 **아니요**를 선택합니다.
7. 블레이드 위쪽에서 **저장**을 클릭합니다. 완료되었습니다!

> [!NOTE]
> 등록 프로세스에서 전자 메일 확인을 사용하지 않도록 설정하면 스팸 메일이 수신될 수 있습니다. 기본 기능을 사용하지 않도록 설정하는 경우에는 자체 확인 시스템을 추가하는 것이 좋습니다.
> 
>
