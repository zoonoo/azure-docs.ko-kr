---
title: Azure AD Connect 제거
description: 이 문서에서는 Azure AD Connect를 제거하는 방법을 설명합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5eb537a70c69745c8067ffb71cfb895a0875945
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96934186"
---
# <a name="uninstall-azure-ad-connect"></a>Azure AD Connect 제거

이 문서에서는 Azure AD Connect를 올바르게 제거하는 방법을 설명합니다.

## <a name="uninstall-azure-ad-connect-from-the-server"></a>서버에서 Azure AD Connect 제거
가장 먼저 해야 할 일은 실행 중인 서버에서 Azure AD Connect를 제거하는 것입니다.  다음 단계를 사용합니다.

 1. Azure AD Connect를 실행 중인 서버에서 **제어판** 으로 이동합니다.
 2. **프로그램 제거**
 ![프로그램 제거](media/how-to-connect-uninstall/uninstall-1.png)를 클릭합니다.</br>
 
 3. **Azure AD Connect** 를 선택합니다.
 ![Azure AD Connect를 선택합니다.](media/how-to-connect-uninstall/uninstall-2.png)</br>
 
 4. 메시지가 표시되면 **예** 를 클릭하여 확인합니다.
 5. 이 확인 작업을 거치면 Azure AD Connect 화면이 나타납니다.  **제거** 를 클릭합니다.
 ![제거](media/how-to-connect-uninstall/uninstall-3.png)</br>
 
 6. 이 작업이 완료되면 **끝내기** 를 클릭합니다.
 7. ![종료](media/how-to-connect-uninstall/uninstall-4.png)</br>
 
 8. 다시 **제어판** 으로 돌아가서 **새로 고침** 을 누르고 모든 구성 요소가 제거되었는지 확인합니다.


## <a name="next-steps"></a>다음 단계

- [Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.
- [기존 ADSync 데이터베이스를 사용하여 Azure AD Connect 설치](how-to-connect-install-existing-database.md)
- [ISQL 위임된 관리자 권한을 사용하여 Azure AD Connect 설치](how-to-connect-install-sql-delegation.md)

