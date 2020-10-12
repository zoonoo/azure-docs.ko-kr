---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 86b18c2a6fc3fbf342f5b7a4b6b563432c605f73
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "68781473"
---
이러한 모범 사례를 염두에 두십시오.

- 암호를 잊어버린 경우 암호를 재설정할 필요가 없도록 모든 암호를 안전한 장소에 저장 하는 것이 좋습니다. 관리 서비스에서 기존 암호를 검색할 수 없습니다. Azure Portal 통해서만 다시 설정할 수 있습니다. 암호를 다시 설정 하는 경우 다시 설정 하기 전에 모든 사용자에 게 알려야 합니다.
- HTTP를 통해 장치의 Windows PowerShell 인터페이스에 원격으로 액세스할 수 있습니다. 보안 모범 사례에 따라 신뢰할 수 있는 네트워크 에서만 HTTP를 사용 해야 합니다.
- 장치 암호가 강력 하 고 잘 보호 되는지 확인 합니다. [암호 모범 사례](https://docs.microsoft.com/azure/security/fundamentals/identity-management-best-practices#enable-password-management)를 따릅니다.