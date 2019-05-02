---
title: 'Azure AD Connect: 다음 단계 및 Azure AD Connect 관리 방법 | Microsoft Docs'
description: Azure AD Connect에 대한 기본 구성 및 운영 작업을 확장하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: c18bee36-aebf-4281-b8fc-3fe14116f1a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c204029557a73dc3f02015afb92c0fdbf0d4d50e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64571338"
---
# <a name="next-steps-and-how-to-manage-azure-ad-connect"></a>다음 단계 및 Azure AD Connect 관리 방법
이 문서의 운영 절차를 사용하여 조직 요구 사항 및 요건에 부합하도록 Azure Active Directory(Azure AD) Connect를 사용자 지정합니다.  

## <a name="add-additional-sync-admins"></a>추가 동기화 관리자 추가
기본적으로 설치를 수행한 사용자와 로컬 관리자만 설치된 동기화 엔진을 관리할 수 있습니다. 추가 사용자가 동기화 엔진에 액세스하여 관리할 수 있도록 로컬 서버에서 그룹 이름으로 ADSyncAdmins를 찾아 이 그룹에 추가합니다.

## <a name="assign-licenses-to-azure-ad-premium-and-enterprise-mobility-suite-users"></a>Azure AD Premium 및 Enterprise Mobility Suite 사용자에게 라이선스 할당
이제 사용자가 클라우드로 동기화되었으므로, Office 365와 같은 클라우드 앱으로 작업할 수 있도록 라이선스를 할당해야 합니다.

### <a name="to-assign-an-azure-ad-premium-or-enterprise-mobility-suite-license"></a>Azure AD Premium 또는 엔터프라이즈 이동성 제품군 라이선스를 할당하려면

1. 관리자 권한으로 Azure Portal에 로그인합니다.
2. 왼쪽 창에서 **Active Directory**를 선택합니다.
3. **Active Directory** 페이지에서 설정하려는 사용자가 있는 디렉토리를 두 번 클릭합니다.
4. 디렉토리 페이지의 맨 위에서 **라이센스**를 선택합니다.
5. **라이선스** 페이지에서 **Active Directory Premium** 또는 **Enterprise Mobility Suite**를 선택한 후 **할당**을 클릭합니다.
6. 대화 상자에서 라이선스를 할당하려는 사용자를 선택 하고 확인 표시 아이콘을 클릭하여 변경 내용을 저장합니다.

## <a name="verify-the-scheduled-synchronization-task"></a>예약된 동기화 작업 확인
Azure Portal을 사용하여 동기화 상태를 확인합니다.

### <a name="to-verify-the-scheduled-synchronization-task"></a>예약된 동기화 작업을 확인하려면
1. 관리자 권한으로 Azure Portal에 로그인합니다.
2. 왼쪽 창에서 **Active Directory**를 선택합니다.
3. 왼쪽에서 선택 **Azure AD Connect**
4. 페이지의 맨 마지막 동기화를 note 합니다.

![디렉터리 동기화 시간](./media/how-to-connect-post-installation/verify2.png)

## <a name="start-a-scheduled-synchronization-task"></a>예약된 동기화 작업 시작
동기화 작업을 실행 해야 할 경우이 수행할 수 있습니다.

1. 마법사를 시작 하려면 Azure AD Connect 바탕 화면 바로 가기를 두 번 클릭 합니다.
2. **Configure**를 클릭합니다.
3. 작업 화면에서 선택 합니다 **동기화 옵션 사용자 지정** 를 클릭 하 고 **다음**
4. Azure AD 자격 증명 입력
5. **다음**을 클릭합니다. **다음**을 클릭합니다.  **다음**을 클릭합니다.
5.  에 **구성할 준비가** 화면에서 **구성이 완료 되 면 동기화 프로세스 시작** 확인란을 선택 합니다.
6.  **Configure**를 클릭합니다.

Azure AD Connect 동기화 Scheduler에 대한 자세한 내용은 [Azure AD Connect Scheduler](how-to-connect-sync-feature-scheduler.md)를 참조하세요.

## <a name="additional-tasks-available-in-azure-ad-connect"></a>Azure AD Connect에서 사용할 수 있는 추가 작업
Azure AD Connect의 초기 설치 후, Azure AD Connect 시작 페이지 또는 바탕 화면 바로 가기에서 항상 마법사를 다시 시작할 수 있습니다.  마법사를 다시 실행하면 몇가지 새로운 옵션이 추가 작업의 형태로 제공됩니다.  

다음 테이블에서는 작업에 대한 요약과 각 작업의 간략한 설명을 제공합니다.

![추가 작업 목록](./media/how-to-connect-post-installation/addtasks2.png)

| 추가 작업 | 설명 |
| --- | --- |
|**개인 정보 설정**|Microsoft와 공유 되는 원격 분석 데이터를 봅니다.|
|**현재 구성 보기**|현재 Azure AD Connect 솔루션을 봅니다.  일반 설정, 동기화된 디렉토리 및 동기화 설정을 포함합니다. |
| **동기화 옵션 사용자 지정** |추가 Active Directory 포리스트를 구성에 추가 또는 사용자, 그룹, 디바이스나 암호 쓰기 저장과 같은 동기화 옵션 사용 등 현재 구성을 변경합니다. |
|**디바이스 옵션 구성**|동기화에 사용 가능한 장치 옵션|
|**디렉터리 스키마 새로 고침**|동기화에 대 한 새 온-프레미스 디렉터리 개체를 추가할 수 있습니다.|
|**준비 모드 구성** |즉시 동기화되지 않거나 Azure AD 또는 온-프레미스 Active Directory로 내보내지 않은 스테이지 정보입니다.  이 기능을 통해 동기화가 되기 전에 동기화를 미리볼 수 있습니다. |
|**사용자 로그인 변경**|사용자 로그인을 사용 하는 인증 방법 변경|
|**페더레이션 관리**|AD FS 인프라를 관리, 인증서 갱신 및 AD FS 서버 추가|
|**문제 해결**|Azure AD Connect 문제 해결에 도움|

## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.
