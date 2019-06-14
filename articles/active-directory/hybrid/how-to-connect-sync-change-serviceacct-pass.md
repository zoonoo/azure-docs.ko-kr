---
title: 'Azure AD Connect 동기화:  ADSync 서비스 계정 변경 | Microsoft Docs'
description: 이 항목 문서는 암호화 키 및 암호가 변경된 후 암호화 키를 제거하는 방법을 설명합니다.
services: active-directory
keywords: Azure AD 동기화 서비스 계정, 암호
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 077671ab4e964d7641aa3a0f0b435b39117eb6aa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65139386"
---
# <a name="changing-the-adsync-service-account-password"></a>ADSync 서비스 계정 암호 변경
ADSync 서비스 계정 암호를 변경 하면 동기화 서비스 됩니다 시작할 수 올바르게 암호화 키를 제거 하 고 ADSync 서비스 계정 암호를 다시 초기화 될 때까지. 

Azure AD Connect 동기화 서비스의 일부로 사용 하 여 암호화 키를 AD DS 커넥터 계정 및 ADSync 서비스 계정의 암호를 저장.  이러한 계정은 데이터베이스에 저장되기 전에 암호화됩니다. 

사용된 암호화 키는 [Windows 데이터 보호(DPAPI)](https://msdn.microsoft.com/library/ms995355.aspx)를 사용하여 보호됩니다. DPAPI를 사용 하 여 암호화 키 보호 합니다 **ADSync 서비스 계정**합니다. 

서비스 계정 암호를 변경 해야 하는 경우의 절차를 사용할 수 있습니다 [ADSync 서비스 계정 암호화 키 제거](#abandoning-the-adsync-service-account-encryption-key) 에이 작업을 수행 합니다.  이러한 절차는 어떤 이유로든 암호화 키를 제거해야 하는 경우에 사용되어야 합니다.

## <a name="issues-that-arise-from-changing-the-password"></a>암호 변경으로 인해 발생하는 문제
서비스 계정 암호를 변경하는 경우 두 가지 작업을 수행해야 합니다.

첫째, Windows 서비스 제어 관리자에서 암호를 변경해야 합니다.  이 문제가 해결될 때까지 다음 오류가 표시됩니다.


- Windows 서비스 제어 관리자에서 동기화 서비스를 시작하려고 하면 "**로컬 컴퓨터에서 Microsoft Azure AD 동기화 서비스를 시작하지 못했습니다.** "라는 오류 메시지가 표시됩니다. **오류 1069: 서비스가 로그온 실패로 인해 시작되지 않았습니다.** "
- Windows 이벤트 뷰어의 시스템 이벤트 로그에는 **이벤트 ID 7038** “**다음 오류 때문에 현재 구성된 암호를 사용하여 ADSync 서비스에서 (으)로 로그온할 수 없습니다: 사용자 이름 또는 암호가 잘못되었습니다.** "라는 오류가 메시지가 포함됩니다.

둘째, 특정 조건 하에서 암호가 업데이트되는 경우 동기화 서비스가 DPAPI를 통해 암호화 키를 더 이상 검색할 수 없습니다. 암호화 키가 없으면 동기화 서비스는 온-프레미스 AD 및 Azure AD 사이에서 동기화하는 데 필요한 암호를 해독할 수 없습니다.
다음과 같은 오류가 표시됩니다.

- Windows 서비스 제어 관리자에서 동기화 서비스를 시작하려고 하면 암호화 키를 검색할 수 없는 경우 다음과 같은 오류로 인해 실패합니다. “<strong>Windows에서 로컬 컴퓨터의 Microsoft Azure AD 동기화를 시작하지 못했습니다. 자세한 정보는 시스템 이벤트 로그를 참조하십시오. Microsoft 서비스가 아닌 경우, 서비스 공급업체에 문의하는 경우 -21451857952 서비스 특정 오류를 참조하십시오</strong>.”
- Windows 이벤트 뷰어의 응용 프로그램 이벤트 로그에 사용 하 여 오류가 **이벤트 ID 6028** 오류 메시지 및 *"서버 암호화 키를 액세스할 수 없습니다."*

이러한 오류를 수신 하지 않습니다을 보장 하려면의 절차에 따라 [ADSync 서비스 계정 암호화 키 중단](#abandoning-the-adsync-service-account-encryption-key) 암호를 변경 하는 경우.
 
## <a name="abandoning-the-adsync-service-account-encryption-key"></a>ADSync 서비스 계정 암호화 키 제거
>[!IMPORTANT]
>다음 절차를 Azure AD Connect 빌드 1.1.443.0 이전 빌드에만 적용됩니다.

다음 절차를 사용하여 암호화 키를 제거하십시오.

### <a name="what-to-do-if-you-need-to-abandon-the-encryption-key"></a>암호화 키를 제거해야 하는 경우 수행할 작업

암호화 키를 제거해야 하는 경우 다음 절차를 사용하여 작업을 수행하십시오.

1. [동기화 서비스 중지](#stop-the-synchronization-service)

1. [기존 암호화 키 제거](#abandon-the-existing-encryption-key)

2. [AD DS 커넥터 계정 암호를 제공 합니다.](#provide-the-password-of-the-ad-ds-connector-account)

3. [ADSync 서비스 계정의 암호를 다시 초기화](#reinitialize-the-password-of-the-adsync-service-account)

4. [동기화 서비스 시작](#start-the-synchronization-service)

#### <a name="stop-the-synchronization-service"></a>동기화 서비스 중지
먼저 Windows 서비스 제어 관리자에서 서비스를 중지할 수 있습니다.  서비스 중지를 시도할 때 서비스가 실행 중이지 않은지 확인합니다.  실행 중인 경우 완료될 때까지 기다렸다가 중지합니다.


1. Windows 서비스 제어 관리자로 이동합니다(시작 → 서비스).
2. **Microsoft Azure AD Sync**를 선택하고 [중지]를 클릭합니다.

#### <a name="abandon-the-existing-encryption-key"></a>기존 암호화 키 제거
새 암호화 키를 만들 수 있도록 기존 암호화 키를 제거합니다.

1. Azure AD Connect 서버에 관리자로 로그인 합니다.

2. 새 PowerShell 세션을 시작합니다.

3. `$env:Program Files\Microsoft Azure AD Sync\bin\` 폴더로 이동합니다.

4. `./miiskmu.exe /a` 명령을 실행합니다.

![Azure AD Connect 동기화 암호화 키 유틸리티](./media/how-to-connect-sync-change-serviceacct-pass/key5.png)

#### <a name="provide-the-password-of-the-ad-ds-connector-account"></a>AD DS 커넥터 계정 암호를 제공 합니다.
데이터베이스 내에 저장 하는 기존 암호를 더 이상 해독할 수 AD DS 커넥터 계정 암호를 사용 하 여 동기화 서비스를 제공 해야 합니다. 동기화 서비스는 새 암호화 키를 사용하여 암호를 암호화합니다.

1. Synchronization Service Manager를 시작합니다(시작 → 동기화 서비스).
</br>![Sync Service Manager](./media/how-to-connect-sync-change-serviceacct-pass/startmenu.png)  
2. **커넥터** 탭으로 이동합니다.
3. 온-프레미스 AD에 해당하는 **AD 커넥터**를 선택합니다. AD 커넥터가 둘 이상이면 각각에 대해 다음 단계를 반복합니다.
4. **작업** 아래에서 **속성**을 선택합니다.
5. 팝업 대화 상자에서 **Active Directory 포리스트에 연결**을 선택합니다.
6. **암호** 텍스트 상자에 AD DS 계정의 암호를 입력합니다. 암호를 모르는 경우 이 단계를 수행하기 전에 알려진 값으로 설정해야 합니다.
7. **확인**을 클릭하여 새 암호를 저장하고 팝업 대화 상자를 닫습니다.
![Azure AD Connect 동기화 암호화 키 유틸리티](./media/how-to-connect-sync-change-serviceacct-pass/key6.png)

#### <a name="reinitialize-the-password-of-the-adsync-service-account"></a>ADSync 서비스 계정의 암호를 다시 초기화
Azure AD 서비스 계정의 암호를 동기화 서비스에 직접 제공할 수 없습니다. 대신 cmdlet **Add-ADSyncAADServiceAccount**을 사용하여 Azure AD 서비스 계정을 다시 초기화해야 합니다. cmdlet은 계정 암호를 다시 설정하여 동기화 서비스에 사용할 수 있게 합니다.

1. Azure AD Connect 서버에서 새 PowerShell 세션을 시작합니다.
2. cmdlet `Add-ADSyncAADServiceAccount`를 실행합니다.
3. 팝업 대화 상자에 Azure AD 테넌트에 대한 Azure AD 전역 관리자 자격 증명을 제공합니다.
![Azure AD Connect 동기화 암호화 키 유틸리티](./media/how-to-connect-sync-change-serviceacct-pass/key7.png)
4. 성공한 경우 PowerShell 명령 프롬프트가 표시됩니다.

#### <a name="start-the-synchronization-service"></a>동기화 서비스 시작
이제 동기화 서비스가 암호화 키 및 필요한 모든 암호에 액세스할 수 있으므로 Windows 서비스 제어 관리자에서 서비스를 다시 시작할 수 있습니다.


1. Windows 서비스 제어 관리자로 이동합니다(시작 → 서비스).
2. **Microsoft Azure AD 동기화**를 선택하고 다시 시작을 클릭합니다.

## <a name="next-steps"></a>다음 단계
**개요 항목**

* [Azure AD Connect 동기화: 동기화의 이해 및 사용자 지정](how-to-connect-sync-whatis.md)

* [Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)
