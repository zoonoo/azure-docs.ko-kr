---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/11/2020
ms.author: mimart
ms.openlocfilehash: 4e03d076fc9ed7d87326a4c8b4f8bf6c0b21c750
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "79126719"
---
## <a name="disable-email-verification"></a>메일 확인을 사용하지 않도록 설정

기본적으로 Azure Active Directory B2C (Azure AD B2C)는 로컬 계정 (메일 주소 또는 사용자 이름을 사용 하 여 등록 하는 사용자 계정)에 대해 고객의 전자 메일 주소를 확인 합니다. Azure AD B2C는 등록 프로세스 중에 고객이 해당 주소를 확인 하도록 요구 하 여 유효한 전자 메일 주소를 확인 합니다. 또한 악의적인 행위자가 자동화 된 프로세스를 사용 하 여 응용 프로그램에서 사기성 계정을 생성 하지 못하도록 방지 합니다.

일부 응용 프로그램 개발자는 등록 프로세스 중에 전자 메일 확인을 건너뛰고 나중에 고객이 전자 메일 주소를 확인 하는 것을 선호 합니다. 이를 지원하는 경우 전자 메일 확인을 사용하지 않도록 Azure AD B2C를 구성할 수 있습니다. 이렇게 하면 보다 원활한 등록 프로세스가 만들어지고 개발자에 게는 없는 고객의 전자 메일 주소를 확인 한 고객을 구분할 수 있는 유연성이 제공 됩니다.

> [!WARNING]
> 등록 프로세스에서 전자 메일 확인을 사용하지 않도록 설정하면 스팸 메일이 수신될 수 있습니다. 기본 Azure AD B2C 제공 전자 메일 확인을 사용 하지 않도록 설정 하는 경우 대체 확인 시스템을 구현 하는 것이 좋습니다.
