---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: b5f642eafa7792872bdd6f49141d2b6b1412cb78
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96583056"
---
이러한 모범 사례를 염두에 두십시오.

- 암호를 잊어버린 경우 암호를 재설정할 필요가 없도록 모든 암호를 안전한 장소에 저장 하는 것이 좋습니다. 관리 서비스에서 기존 암호를 검색할 수 없습니다. Azure Portal 통해서만 다시 설정할 수 있습니다. 암호를 다시 설정 하는 경우 다시 설정 하기 전에 모든 사용자에 게 알려야 합니다.
- HTTP를 통해 장치의 Windows PowerShell 인터페이스에 원격으로 액세스할 수 있습니다. 보안 모범 사례에 따라 신뢰할 수 있는 네트워크 에서만 HTTP를 사용 해야 합니다.
- 장치 암호가 강력 하 고 잘 보호 되는지 확인 합니다. [암호 모범 사례](https://docs.microsoft.com/azure/security/fundamentals/identity-management-best-practices#enable-password-management)를 따릅니다.