---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 711ec280a7836a78b46443c331391a7ce94095df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98901059"
---
다음 모범 사례를 고려하세요.

- 암호를 잊어버린 경우 암호를 재설정할 필요가 없도록 모든 암호를 안전한 장소에 저장하는 것이 좋습니다. 관리 서비스에서 기존 암호를 검색할 수 없습니다. Azure Portal을 통해서만 재설정할 수 있습니다. 암호를 재설정하는 경우 재설정하기 전에 모든 사용자에게 알려야 합니다.
- HTTP를 통해 디바이스의 Windows PowerShell 인터페이스에 원격으로 액세스할 수 있습니다. 보안 모범 사례에 따라 신뢰할 수 있는 네트워크에서만 HTTP를 사용해야 합니다.
- 디바이스 암호가 강력하고 잘 보호되는지 확인합니다. [암호 모범 사례](../articles/security/fundamentals/identity-management-best-practices.md#enable-password-management)를 따릅니다.