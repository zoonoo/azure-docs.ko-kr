---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 05/04/2020
ms.author: mimart
ms.openlocfilehash: de1b67265c5c3b3f7247b7f21506eed88abfd550
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "82900351"
---
1. Amazon 계정 자격 증명을 사용 하 여 [Amazon Developer 콘솔](https://developer.amazon.com/dashboard) 에 로그인 합니다.
1. 이미 수행한 경우 **등록**을 클릭하고 개발자 등록 단계를 수행하며 정책에 동의합니다.
1. 대시보드에서 **Amazon로 로그인**을 선택 합니다.
1. **Create a New Security Profile**(새 보안 프로필 만들기)을 선택합니다.
1. **보안 프로필 이름**, **보안 프로필 설명**및 **동의 개인 정보 알림 url**을 입력 합니다. 예를 들어 `https://www.contoso.com/privacy` 개인 정보 알림 url은 사용자에 게 개인 정보 보호 정보를 제공 하는 관리 페이지입니다. 그런 다음 **Save**를 클릭합니다.
1. **Amazon 구성으로 로그인** 섹션에서 사용자가 만든 **보안 프로필 이름을** 선택 하 고 **관리** 아이콘을 클릭 한 다음 **웹 설정**을 선택 합니다.
1. **웹 설정** 섹션에서 **클라이언트 ID** 값을 복사합니다. **비밀 표시**를 선택하여 클라이언트 암호를 표시한 후 복사합니다. 테넌트에서 Amazon 계정을 ID 공급자로 구성하려면 둘 모두가 필요합니다. **클라이언트 보안 비밀**은 중요한 보안 자격 증명이므로
1. **웹 설정** 섹션에서 **편집**을 선택 합니다. **허용 되는 원본** 및 **허용 된 반환 url**에서 적절 한 url (위에 설명 되어 있음)을 입력 합니다. 
1. **저장**을 클릭합니다.
