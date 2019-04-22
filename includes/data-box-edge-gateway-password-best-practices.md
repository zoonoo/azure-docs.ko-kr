---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: a3b6616edbe1678cb18f78ec9025fc0286bf124f
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684478"
---
다음 모범 사례를 고려하세요.

- 관리 서비스는 기존 암호를 검색할 수 없습니다: Azure portal을 통해 재설정만 가능 합니다. 잊어버린 경우 암호를 재설정할 필요가 없도록 모든 암호를 안전한 장소에 저장하는 것이 좋습니다. 암호를 재설정 하면 수 다시 설정 하기 전에 모든 사용자에 게 알려야 합니다.
- HTTP를 통해 장치의 Windows PowerShell 인터페이스를 원격으로 액세스할 수 있습니다. 보안 모범 사례를 따라 신뢰할 수 있는 네트워크에만 HTTP를 사용 해야 합니다.
- 장치 암호 강력 하 고 잘 보호 되는지 확인 합니다. 수행 합니다 [암호에 대 한 유용한 정보](https://docs.microsoft.com/azure/security/azure-security-identity-management-best-practices#enable-password-management)합니다.