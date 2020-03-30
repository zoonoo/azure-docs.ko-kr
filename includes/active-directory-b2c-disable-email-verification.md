---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/11/2020
ms.author: mimart
ms.openlocfilehash: 4e03d076fc9ed7d87326a4c8b4f8bf6c0b21c750
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126719"
---
## <a name="disable-email-verification"></a>메일 검증을 사용하지 않도록 설정

기본적으로 Azure Active Directory B2C(Azure AD B2C)는 로컬 계정(전자 메일 주소 또는 사용자 이름으로 등록한 사용자의 계정)에 대해 고객의 전자 메일 주소를 확인합니다. Azure AD B2C는 고객이 등록 프로세스 중에 확인하도록 요구하여 유효한 전자 메일 주소를 보장합니다. 또한 악의적인 행위자가 자동화된 프로세스를 사용하여 응용 프로그램에서 사기성 계정을 생성하는 것을 방지합니다.

일부 응용 프로그램 개발자는 등록 프로세스 중에 전자 메일 확인을 건너뛰고 대신 고객이 나중에 전자 메일 주소를 확인하도록 하는 것을 선호합니다. 이를 지원하는 경우 전자 메일 확인을 사용하지 않도록 Azure AD B2C를 구성할 수 있습니다. 이렇게 하면 보다 원활한 가입 프로세스가 생성되고 개발자는 이메일 주소를 확인하지 않은 고객으로부터 이메일 주소를 확인한 고객을 유연하게 차별화할 수 있습니다.

> [!WARNING]
> 등록 프로세스에서 전자 메일 확인을 사용하지 않도록 설정하면 스팸 메일이 수신될 수 있습니다. Azure AD B2C가 제공한 기본 전자 메일 확인을 사용하지 않도록 설정하면 대체 확인 시스템을 구현하는 것이 좋습니다.
