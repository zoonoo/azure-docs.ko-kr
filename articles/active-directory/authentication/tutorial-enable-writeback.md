---
title: Azure AD 비밀번호 쓰기 저장 사용
description: 이 자습서에서는 Azure AD Connect의 일부로 클라우드에서 실행된 암호 변경 내용을 온-프레미스 AD로 가져오도록 비밀번호 쓰기 저장을 사용하도록 설정할 것입니다.
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.openlocfilehash: 6cbc4fa071a45e670dae3cc6be66272bc67767f7
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54437184"
---
# <a name="tutorial-enabling-password-writeback"></a>자습서: 암호 쓰기 저장을 사용하도록 설정

이 자습서에서는 하이브리드 환경에 비밀번호 쓰기 저장을 사용하도록 설정할 것입니다. 비밀번호 쓰기 저장은 Azure AD(Azure Active Directory)의 암호 변경 내용을 온-프레미스 AD DS(Active Directory Domain Services) 환경에 다시 동기화하는 데 사용됩니다. 암호 변경 내용을 Azure AD의 기존 온-프레미스 디렉터리로 다시 전송하는 안전한 메커니즘을 제공하기 위해 Azure AD Connect의 일부로 비밀번호 쓰기 저장이 설정됩니다. 비밀번호 쓰기 저장의 내부 작업에 대한 자세한 내용은 [비밀번호 쓰기 저장이란](concept-sspr-writeback.md) 문서에서 찾을 수 있습니다.

> [!div class="checklist"]
> * Azure AD Connect에서 비밀번호 쓰기 저장 옵션 사용
> * SSPR(셀프 서비스 암호 재설정)에서 비밀번호 쓰기 저장 옵션 사용

## <a name="prerequisites"></a>필수 조건

* 적어도 평가판 라이선스가 할당된 정상 작동 중인 Azure AD 테넌트에 대한 액세스 권한.
* Azure AD 테넌트의 전역 관리자 권한이 있는 계정.
* [Azure AD Connect](../hybrid/how-to-connect-install-express.md) 최신 버전을 실행하도록 구성된 기존 서버.
* 이전 SSPR(셀프 서비스 암호 재설정) 자습서 완료.

## <a name="enable-password-writeback-option-in-azure-ad-connect"></a>Azure AD Connect에서 비밀번호 쓰기 저장 옵션 사용

비밀번호 쓰기 저장을 사용하려면 먼저 Azure AD Connect를 설치한 서버에서 이 기능을 사용하도록 설정해야 합니다.

1. 비밀번호 쓰기 저장을 구성하고 사용하도록 설정하려면 Azure AD Connect 서버에 로그인하고 **Azure AD Connect** 구성 마법사를 시작합니다.
2. **시작** 페이지에서 **구성**을 선택합니다.
3. **추가 작업** 페이지에서 **동기화 옵션 사용자 지정**을 선택하고 **다음**을 선택합니다.
4. **Azure AD에 연결** 페이지에서 전역 관리자 자격 증명을 입력하고 **다음**을 선택합니다.
5. **디렉터리 연결** 및 **도메인/OU** 필터링 페이지에서 **다음**을 선택합니다.
6. **선택적 기능** 페이지에서 **비밀번호 쓰기 저장** 옆의 확인란을 선택한 후 **다음**을 선택합니다.
7. **구성 준비 완료** 페이지에서 **구성**을 선택하고 프로세스가 완료될 때까지 기다립니다.
8. 구성이 완료된 것이 확인되면 **마침**을 선택합니다.

## <a name="enable-password-writeback-option-in-sspr"></a>SSPR에서 비밀번호 쓰기 저장 옵션 사용

Azure AD Connect에서 비밀번호 쓰기 저장 기능을 사용하도록 설정했으면 중간까지 온 것입니다. SSPR에서 비밀번호 쓰기 저장을 사용하도록 허용하면 암호를 변경 또는 재설정하는 사용자가 온-프레미스에서도 해당 암호를 설정하도록 허용하는 루프가 완료됩니다.

1. 전역 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory**로 이동하여 **암호 재설정**을 클릭한 다음, **온-프레미스 통합**을 선택합니다.
3. **온-프레미스 디렉터리에 비밀번호 쓰기 저장** 옵션을 **예**로 설정합니다.
4. **사용자가 해당 암호를 재설정하지 않고 계정의 잠금을 해제할 수 있음** 옵션을 **예**로 설정합니다.
5. 페이지 맨 아래에 있는 **저장**

## <a name="next-steps"></a>다음 단계

이 자습서에서는 셀프 서비스 암호 재설정에 비밀번호 쓰기 저장을 사용하도록 설정했습니다. Azure Portal 창을 열어 놓은 상태로 그 다음 자습서로 넘어가서 셀프 서비스 암호 재설정과 관련된 추가 설정을 구성한 후 솔루션을 파일럿에 배포하세요.

> [!div class="nextstepaction"]
> [Windows 로그온 화면에서 SSPR을 사용하도록 설정](tutorial-sspr-windows.md)
