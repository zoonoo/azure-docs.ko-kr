---
title: 'Azure AD Connect: 계정 및 사용 권한 | Microsoft Docs'
description: 이 항목에서는 사용 되는 계정과 필요한 사용 권한을 설명 합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: cychua
ms.assetid: b93e595b-354a-479d-85ec-a95553dd9cc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 10/03/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6071e6553fb1275fea63a37b4897aef2685bd509
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78376106"
---
# <a name="azure-ad-connect-accounts-and-permissions"></a>Azure AD Connect: 계정 및 사용 권한

## <a name="accounts-used-for-azure-ad-connect"></a>Azure AD Connect에 사용 되는 계정

![계정 개요](media/reference-connect-accounts-permissions/account5.png)

Azure AD Connect은 온-프레미스 또는 Windows Server Active Directory의 정보를 Azure Active Directory 동기화 하기 위해 3 개의 계정을 사용 합니다.  이러한 계정은 다음과 같습니다.

- **AD DS 커넥터 계정**: Windows Server Active Directory 정보를 읽고 쓰는 데 사용 됩니다.

- **Adsync 서비스 계정**: 동기화 서비스를 실행 하 고 SQL 데이터베이스에 액세스 하는 데 사용 됩니다.

- **AZURE Ad 커넥터 계정**: azure ad에 정보를 쓰는 데 사용 됩니다.

Azure AD Connect를 실행 하는 데 사용 되는 이러한 세 계정 외에도 Azure AD Connect를 설치 하려면 다음과 같은 추가 계정이 필요 합니다.  이러한 기능은 다음과 같습니다.

- **로컬 관리자 계정**: Azure AD Connect를 설치 하 고 컴퓨터에 대 한 로컬 관리자 권한이 있는 관리자입니다.

- **AD DS Enterprise Administrator 계정**: 필요에 따라 위의 "AD DS 커넥터 계정"을 만드는 데 사용 됩니다.

- Azure **Ad 전역 관리자 계정**: Azure ad 커넥터 계정을 만들고 azure ad를 구성 하는 데 사용 됩니다.

- **SQL SA 계정 (선택 사항)** : SQL Server의 전체 버전을 사용 하는 경우 adsync 데이터베이스를 만드는 데 사용 됩니다.  이 SQL Server는 Azure AD Connect 설치를 위한 로컬 또는 원격 일 수 있습니다.  이 계정은 엔터프라이즈 관리자와 동일한 계정일 수 있습니다.  이제 SQL 관리자가 대역 외에서 데이터베이스를 프로 비전 한 다음 데이터베이스 소유자 권한을 사용 하 여 Azure AD Connect 관리자가 설치할 수 있습니다.  이에 대 한 자세한 내용은 [SQL 위임 된 관리자 권한을 사용 하 여 Azure AD Connect 설치](how-to-connect-install-sql-delegation.md) 를 참조 하세요.


>[!IMPORTANT]
> 빌드 1.4. # # #. #은 더 이상 엔터프라이즈 관리자 또는 도메인 관리자 계정을 AD DS 커넥터 계정으로 사용할 수 없습니다.  **기존 계정 사용**을 지정 하는 경우 엔터프라이즈 관리자 또는 도메인 관리자 계정을 입력 하려고 하면 오류가 표시 됩니다.

> [!NOTE]
> ESAE 관리 포리스트에서 Azure AD Connect에 사용 되는 관리 계정을 관리할 수 있습니다 ("빨간색 포리스트" 라고도 하는).
> 전용 관리 포리스트를 사용 하면 조직에서 프로덕션 환경 보다 더 강력한 보안 제어를 사용 하는 환경에서 관리 계정, 워크스테이션 및 그룹을 호스트할 수 있습니다.
> 전용 관리 포리스트에 대 한 자세한 내용은 [Esae 관리 포리스트 디자인 방법](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#esae-administrative-forest-design-approach)을 참조 하세요.

> [!NOTE]
> 초기 설치 후에는 전역 관리자 역할이 필요 하지 않으며 유일 하 게 필요한 계정은 **디렉터리 동기화 계정** 역할 계정입니다. 이는 반드시 전역 관리자 역할이 있는 계정을 제거 하는 것을 의미 하는 것은 아닙니다. 마법사를 다시 실행 해야 하는 경우 계정을 완전히 제거 하면 문제가 발생할 수 있으므로 역할을 더 강력한 역할로 변경 하는 것이 좋습니다. Azure AD Connect 마법사를 다시 사용 해야 하는 경우 역할의 권한을 줄여 항상 권한을 다시 높일 수 있습니다. 

## <a name="installing-azure-ad-connect"></a>Azure AD Connect 설치
Azure AD Connect 설치 마법사는 두 가지 다른 경로를 제공 합니다.

* Express 설정에서 마법사에는 더 많은 권한이 필요 합니다.  사용자를 만들거나 사용 권한을 구성 하지 않고도 구성을 쉽게 설정할 수 있습니다.
* 사용자 지정 설정에서 마법사는 더 많은 선택과 옵션을 제공 합니다. 그러나 사용자에 게 올바른 사용 권한이 있는지 확인 해야 하는 경우도 있습니다.



## <a name="express-settings-installation"></a>Express 설정 설치
Express 설정에서 설치 마법사는 다음을 묻는 메시지를 표시 합니다.

  - 엔터프라이즈 관리자 자격 증명 AD DS
  - Azure AD 전역 관리자 자격 증명

### <a name="ad-ds-enterprise-admin-credentials"></a>엔터프라이즈 관리자 자격 증명 AD DS
AD DS Enterprise Admin 계정은 온-프레미스 Active Directory를 구성 하는 데 사용 됩니다. 이러한 자격 증명은 설치 중에만 사용 되며 설치가 완료 된 후에는 사용 되지 않습니다. 도메인 관리자가 아닌 엔터프라이즈 관리자는 모든 도메인에서 Active Directory의 사용 권한을 설정할 수 있는지 확인 해야 합니다.

DirSync에서 업그레이드 하는 경우 AD DS Enterprise Admins 자격 증명을 사용 하 여 DirSync에서 사용 하는 계정의 암호를 다시 설정 합니다. Azure AD 전역 관리자 자격 증명도 필요 합니다.

### <a name="azure-ad-global-admin-credentials"></a>Azure AD 전역 관리자 자격 증명
이러한 자격 증명은 설치 중에만 사용 되며 설치가 완료 된 후에는 사용 되지 않습니다. Azure AD에 변경 내용을 동기화 하는 데 사용 되는 Azure AD Connector 계정을 만드는 데 사용 됩니다. 또한이 계정은 Azure AD에서 기능으로 동기화를 사용 하도록 설정 합니다.

### <a name="ad-ds-connector-account-required-permissions-for-express-settings"></a>AD DS 커넥터 계정에 기본 설정에 대 한 권한이 필요 합니다.
AD DS 커넥터 계정은 Windows Server AD를 읽고 쓰기 위해 만들어지며 express 설정으로 만들 때 다음 권한이 있습니다.

| 권한이 | 사용 되는 |
| --- | --- |
| <li>디렉터리 변경 내용 복제</li><li>디렉터리 변경 내용 모두 복제 |암호 해시 동기화 |
| 모든 속성 사용자 읽기/쓰기 |가져오기 및 교환 하이브리드 |
| 모든 속성 iNetOrgPerson 읽기/쓰기 |가져오기 및 교환 하이브리드 |
| 모든 속성 그룹 읽기/쓰기 |가져오기 및 교환 하이브리드 |
| 모든 속성 연락처 읽기/쓰기 |가져오기 및 교환 하이브리드 |
| 암호 다시 설정 |비밀 번호 쓰기 저장 사용 준비 |

### <a name="express-installation-wizard-summary"></a>빠른 설치 마법사 요약

![빠른 설치](./media/reference-connect-accounts-permissions/express.png)

다음은 빠른 설치 마법사 페이지, 수집 된 자격 증명 및 사용 되는 항목에 대 한 요약입니다.

| 마법사 페이지 | 자격 증명 수집 됨 | 필요한 권한 | 사용 되는 |
| --- | --- | --- | --- |
| 해당 없음 |설치 마법사를 실행 하는 사용자 |로컬 서버의 관리자 |<li>동기화 서비스를 실행 하는 데 사용 되는 ADSync 서비스 계정을 만듭니다. |
| Azure AD에 연결 |Azure AD 디렉터리 자격 증명 |Azure AD의 전역 관리자 역할 |<li>Azure AD 디렉터리에서 동기화를 사용 하도록 설정 합니다.</li>  <li>Azure AD에서 진행 중인 동기화 작업에 사용 되는 Azure AD Connector 계정 만들기</li> |
| AD DS에 연결 |온-프레미스 Active Directory 자격 증명 |Active Directory에서 EA (Enterprise Admins) 그룹의 구성원 |<li>Active Directory에서 AD DS Connector 계정을 만들고 사용 권한을 부여 합니다. 이 생성 된 계정은 동기화 중에 디렉터리 정보를 읽고 쓰는 데 사용 됩니다.</li> |


## <a name="custom-installation-settings"></a>사용자 지정 설치 설정

사용자 지정 설정 설치를 사용 하 여 마법사는 더 많은 선택과 옵션을 제공 합니다. 

### <a name="custom-installation-wizard-summary"></a>사용자 지정 설치 마법사 요약

다음은 사용자 지정 설치 마법사 페이지, 수집 된 자격 증명 및 사용 되는 항목에 대 한 요약입니다.

![빠른 설치](./media/reference-connect-accounts-permissions/customize.png)

| 마법사 페이지 | 자격 증명 수집 됨 | 필요한 권한 | 사용 되는 |
| --- | --- | --- | --- |
| 해당 없음 |설치 마법사를 실행 하는 사용자 |<li>로컬 서버의 관리자</li><li>전체 SQL Server 사용 하는 경우 사용자는 SQL의 시스템 관리자 (SA) 여야 합니다.</li> |기본적으로는 동기화 엔진 서비스 계정으로 사용 되는 로컬 계정을 만듭니다. 계정은 관리자가 특정 계정을 지정 하지 않은 경우에만 생성 됩니다. |
| 동기화 서비스, 서비스 계정 옵션을 설치 합니다. |AD 또는 로컬 사용자 계정 자격 증명 |사용자, 설치 마법사에서 사용 권한을 부여 합니다. |관리자가 계정을 지정 하는 경우이 계정은 동기화 서비스에 대 한 서비스 계정으로 사용 됩니다. |
| Azure AD에 연결 |Azure AD 디렉터리 자격 증명 |Azure AD의 전역 관리자 역할 |<li>Azure AD 디렉터리에서 동기화를 사용 하도록 설정 합니다.</li>  <li>Azure AD에서 진행 중인 동기화 작업에 사용 되는 Azure AD Connector 계정 만들기</li> |
| 디렉터리 연결 |Azure AD에 연결 된 각 포리스트에 대 한 온-프레미스 Active Directory 자격 증명 |사용 권한은 사용 하도록 설정 하는 기능에 따라 달라 지 며 AD DS 커넥터 계정 만들기에서 찾을 수 있습니다. |이 계정은 동기화 중에 디렉터리 정보를 읽고 쓰는 데 사용 됩니다. |
| AD FS 서버 |마법사는 마법사를 실행 하는 사용자의 로그인 자격 증명으로 연결 하는 데 충분 하지 않은 경우 목록의 각 서버에 대해 자격 증명을 수집 합니다. |도메인 관리자 |AD FS 서버 역할의 설치 및 구성 |
| 웹 응용 프로그램 프록시 서버 |마법사는 마법사를 실행 하는 사용자의 로그인 자격 증명으로 연결 하는 데 충분 하지 않은 경우 목록의 각 서버에 대해 자격 증명을 수집 합니다. |대상 컴퓨터의 로컬 관리자 |WAP 서버 역할의 설치 및 구성 |
| 프록시 트러스트 자격 증명 |페더레이션 서비스 트러스트 자격 증명 (프록시가 신뢰 인증서를 등록 하는 데 사용 하는 자격 증명 (FS) |AD FS 서버의 로컬 관리자 인 도메인 계정 |FS-WAP 신뢰 인증서의 초기 등록 |
| AD FS 서비스 계정 페이지, "도메인 사용자 계정 옵션 사용" |AD 사용자 계정 자격 증명 |도메인 사용자 |자격 증명이 제공 되는 Azure AD 사용자 계정은 AD FS 서비스의 로그인 계정으로 사용 됩니다. |

### <a name="create-the-ad-ds-connector-account"></a>AD DS 커넥터 계정 만들기

>[!IMPORTANT]
>Azure AD DS 커넥터 계정에 대 한 올바른 Active Directory 권한을 구성 하는 데 도움이 되는 cmdlet 컬렉션을 포함 하는 build **1.1.880.0** (8 월 2018에 릴리스된)에는 adsyncconfig 라는 새로운 PowerShell 모듈이 도입 되었습니다.
>
>자세한 내용은 [Azure AD Connect: 구성 AD DS 커넥터 계정 권한](how-to-connect-configure-ad-ds-connector-account.md) 을 참조 하세요.

**디렉터리 연결** 페이지에서 지정 하는 계정은 설치 전에 Active Directory에 표시 되어야 합니다.  Azure AD Connect 버전 1.1.524.0 이상 이상에는 Azure AD Connect 마법사에서 Active Directory에 연결 하는 데 사용 되는 **AD DS 커넥터 계정을** 만들 수 있는 옵션이 있습니다.  

또한 필요한 권한이 부여 되어 있어야 합니다. 설치 마법사는 사용 권한을 확인 하지 않으며 문제는 동기화 중에만 발견 됩니다.

필요한 사용 권한은 사용 하도록 설정 하는 선택적 기능에 따라 달라 집니다. 여러 도메인이 있는 경우 포리스트의 모든 도메인에 대 한 권한을 부여 해야 합니다. 이러한 기능을 사용 하도록 설정 하지 않으면 기본 **도메인 사용자** 권한으로 충분 합니다.

| 기능과 | 권한에 |
| --- | --- |
| Msds-consistencyguid 기능 |디자인 개념에 설명 된 Msds-consistencyguid 특성에 대 한 쓰기 권한 ( [msds-consistencyguid As sourceAnchor 사용)](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) | 
| 암호 해시 동기화 |<li>디렉터리 변경 내용 복제</li>  <li>디렉터리 변경 내용 모두 복제 |
| Exchange 하이브리드 배포 |사용자, 그룹 및 연락처에 대 한 [Exchange 하이브리드 쓰기 저장](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) 에 설명 된 특성에 대 한 쓰기 권한 |
| Exchange 메일 공용 폴더 |공용 폴더에 대해 [Exchange 메일 공용 폴더](reference-connect-sync-attributes-synchronized.md#exchange-mail-public-folder) 에 설명 된 특성에 대 한 읽기 권한 | 
| 비밀 번호 쓰기 저장 |사용자에 대 한 [암호 관리 시작](../authentication/howto-sspr-writeback.md) 에 설명 된 특성에 대 한 쓰기 권한 |
| 장치 쓰기 저장 |[장치 쓰기 저장 (writeback](how-to-connect-device-writeback.md))에 설명 된 대로 PowerShell 스크립트를 사용 하 여 권한을 부여 합니다. |
| 그룹 쓰기 저장 |Exchange가 설치 된 포리스트에 **Office 365 그룹** 을 쓰기 저장 (writeback) 할 수 있습니다.  자세한 내용은 [그룹 쓰기 저장](how-to-connect-preview.md#group-writeback)을 참조 하세요.|

## <a name="upgrade"></a>업그레이드할
Azure AD Connect 한 버전에서 새 릴리스로 업그레이드 하는 경우 다음 권한이 필요 합니다.

>[!IMPORTANT]
>빌드 Azure AD Connect 1.1.484부터 SQL 데이터베이스를 업그레이드 하려면 sysadmin 권한이 필요한 회귀 버그가 도입 되었습니다.  이 버그는 빌드 1.1.647에서 수정 되었습니다.  이 빌드로 업그레이드 하는 경우에는 sysadmin 권한이 필요 합니다.  Dbo 권한이 충분 하지 않습니다.  Sysadmin 권한이 없어도 Azure AD Connect를 업그레이드 하려고 하면 업그레이드는 실패 하 고 나중에 Azure AD Connect 제대로 작동 하지 않습니다.  Microsoft는이 사실을 알고 있으며이를 해결 하기 위해 노력 하 고 있습니다.


| 원칙 | 필요한 권한 | 사용 되는 |
| --- | --- | --- |
| 설치 마법사를 실행 하는 사용자 |로컬 서버의 관리자 |이진 파일을 업데이트 합니다. |
| 설치 마법사를 실행 하는 사용자 |ADSyncAdmins의 구성원 |동기화 규칙 및 기타 구성을 변경 합니다. |
| 설치 마법사를 실행 하는 사용자 |전체 SQL server를 사용 하는 경우: DBO (또는 이와 유사한) 동기화 엔진 데이터베이스 |새 열을 사용 하 여 테이블을 업데이트 하는 등 데이터베이스 수준 변경을 수행 합니다. |

## <a name="more-about-the-created-accounts"></a>만든 계정에 대 한 자세한 정보
### <a name="ad-ds-connector-account"></a>AD DS 커넥터 계정
Express 설정을 사용 하는 경우 동기화에 사용 되는 Active Directory에 계정이 생성 됩니다. 만든 계정은 사용자 컨테이너의 포리스트 루트 도메인에 있으며 이름 앞에 **MSOL_** 있습니다. 계정이 만료 되지 않는 길고 복잡 한 암호를 사용 하 여 만들어집니다. 도메인에 암호 정책이 있는 경우이 계정에 대해 길고 복잡 한 암호가 허용 되는지 확인 합니다.

![AD 계정](./media/reference-connect-accounts-permissions/adsyncserviceaccount.png)

사용자 지정 설정을 사용 하는 경우 설치를 시작 하기 전에 계정을 만들 책임이 있습니다.  AD DS 커넥터 계정 만들기를 참조 하세요.

### <a name="adsync-service-account"></a>ADSync 서비스 계정
동기화 서비스는 다른 계정으로 실행할 수 있습니다. VSA ( **가상 서비스 계정** ), **그룹 관리 서비스 계정** (gMSA/smsa) 또는 일반 사용자 계정으로 실행할 수 있습니다. 지원 되는 옵션은 새로 설치 하는 경우 연결의 4 월 2017 버전에서 변경 되었습니다. Azure AD Connect의 이전 릴리스에서 업그레이드 하는 경우에는 이러한 추가 옵션을 사용할 수 없습니다.

| 계정 유형 | 설치 옵션 | 한 |
| --- | --- | --- |
| [가상 서비스 계정](#virtual-service-account) | Express 및 custom, 2017 4 월 이상 | 이 옵션은 도메인 컨트롤러에 설치 하는 경우를 제외 하 고 모든 express 설치에 사용 되는 옵션입니다. 사용자 지정의 경우 다른 옵션을 사용 하지 않는 한 기본 옵션입니다. |
| [그룹 관리 서비스 계정](#group-managed-service-account) | 사용자 지정, 2017 4 월 이상 | 원격 SQL server를 사용 하는 경우 그룹 관리 서비스 계정을 사용 하는 것이 좋습니다. |
| [사용자 계정](#user-account) | Express 및 custom, 2017 4 월 이상 | AAD_ 접두사가 접두사로 추가 된 사용자 계정은 설치 중에 Windows Server 2008에 설치 되 고 도메인 컨트롤러에 설치 된 경우에만 만들어집니다. |
| [사용자 계정](#user-account) | Express 및 사용자 지정, 2017 3 월 이전 | AAD_ 접두사가 붙은 로컬 계정은 설치 중에 생성 됩니다. 사용자 지정 설치를 사용 하는 경우 다른 계정을 지정할 수 있습니다. |

2017 년 3 월 이전 버전의 빌드와 연결을 사용 하는 경우 Windows에서 보안상의 이유로 암호화 키를 제거 하므로 서비스 계정에서 암호를 다시 설정 하면 안 됩니다. Azure AD Connect를 다시 설치 하지 않고 계정을 다른 계정으로 변경할 수 없습니다. 2017 4 월 이후의 빌드로 업그레이드 하는 경우 서비스 계정의 암호를 변경 하는 것은 지원 되지만 사용 된 계정은 변경할 수 없습니다.

> [!Important]
> 처음 설치 하는 경우에만 서비스 계정을 설정할 수 있습니다. 설치가 완료 된 후에는 서비스 계정을 변경할 수 없습니다.

다음은 동기화 서비스 계정에 대 한 기본, 권장 및 지원 옵션 표입니다.

표식의

- **굵은 글꼴** 은 기본 옵션을 나타내며 대부분의 경우 권장 되는 옵션입니다.
- *기울임꼴* 은 기본 옵션이 아닌 경우 권장 옵션을 나타냅니다.
- 2008-Windows Server 2008에 설치 된 경우 기본 옵션
- 굵게 지원 되지 않는 옵션
- 로컬 계정-서버의 로컬 사용자 계정
- 도메인 계정-도메인 사용자 계정
- sMSA- [독립 실행형 관리 서비스 계정](https://technet.microsoft.com/library/dd548356.aspx)
- gMSA- [그룹 관리 서비스 계정](https://technet.microsoft.com/library/hh831782.aspx)

| | 오류로</br>Express | LocalDB/LocalSQL</br>재구성 | 원격 SQL</br>재구성 |
| --- | --- | --- | --- |
| **독립 실행형/작업 그룹 컴퓨터** | 지원 되지 않음 | **VSA**</br>로컬 계정 (2008)</br>로컬 계정 |  지원 되지 않음 |
| **도메인에 가입 된 컴퓨터** | **VSA**</br>로컬 계정 (2008) | **VSA**</br>로컬 계정 (2008)</br>로컬 계정</br>도메인 계정</br>sMSA, gMSA | **gMSA**</br>도메인 계정 |
| **도메인 컨트롤러** | **도메인 계정** | *gMSA*</br>**도메인 계정**</br>sMSA| *gMSA*</br>**도메인 계정**|

#### <a name="virtual-service-account"></a>가상 서비스 계정
가상 서비스 계정은 암호가 없고 Windows에서 관리 되는 특별 한 유형의 계정입니다.

![VSA](./media/reference-connect-accounts-permissions/aadsyncvsa.png)

VSA는 동기화 엔진과 SQL이 동일한 서버에 있는 시나리오에서 사용 하기 위한 것입니다. 원격 SQL을 사용 하는 경우 그룹 관리 서비스 계정을 대신 사용 하는 것이 좋습니다.

이 기능에는 Windows Server 2008 R2 이상이 필요 합니다. Windows Server 2008에 Azure AD Connect를 설치 하는 경우 설치는 대신 [사용자 계정을](#user-account) 사용 하는 것으로 대체 됩니다.

#### <a name="group-managed-service-account"></a>그룹 관리 서비스 계정
원격 SQL server를 사용 하는 경우 **그룹 관리 서비스 계정을**사용 하는 것이 좋습니다. 그룹 관리 서비스 계정에 대 한 Active Directory을 준비 하는 방법에 대 한 자세한 내용은 [그룹 관리 서비스 계정 개요](https://technet.microsoft.com/library/hh831782.aspx)를 참조 하세요.

이 옵션을 사용 하려면 [필수 구성 요소 설치](how-to-connect-install-custom.md#install-required-components) 페이지에서 **기존 서비스 계정 사용**을 선택 하 고 **관리 서비스 계정**을 선택 합니다.  
![VSA](./media/reference-connect-accounts-permissions/serviceaccount.png)  
또한 [독립 실행형 관리 서비스 계정을](https://technet.microsoft.com/library/dd548356.aspx)사용 하도록 지원 됩니다. 그러나 이러한 컴퓨터는 로컬 컴퓨터 에서만 사용할 수 있으며 기본 가상 서비스 계정에는 사용할 수 없습니다.

이 기능을 사용 하려면 Windows Server 2012 이상이 필요 합니다. 이전 운영 체제를 사용 하 고 원격 SQL을 사용 해야 하는 경우에는 [사용자 계정을](#user-account)사용 해야 합니다.

#### <a name="user-account"></a>사용자 계정
사용자 지정 설정에서 사용할 계정을 지정 하지 않는 한 로컬 서비스 계정은 설치 마법사에 의해 만들어집니다. 계정에는 접두사가 **AAD_** 되며 실제 동기화 서비스를 실행 하는 데 사용 됩니다. 도메인 컨트롤러에 Azure AD Connect를 설치 하는 경우 계정은 도메인에 만들어집니다. 다음의 경우 **AAD_** 서비스 계정이 도메인에 있어야 합니다.
   - SQL server를 실행 하는 원격 서버를 사용 합니다.
   - 인증이 필요한 프록시를 사용 합니다.

![동기화 서비스 계정](./media/reference-connect-accounts-permissions/syncserviceaccount.png)

계정이 만료 되지 않는 길고 복잡 한 암호를 사용 하 여 만들어집니다.

이 계정은 다른 계정에 대 한 암호를 안전한 방식으로 저장 하는 데 사용 됩니다. 이러한 다른 계정 암호는 데이터베이스에 암호화 되어 저장 됩니다. 암호화 키에 대 한 개인 키는 Windows DPAPI (데이터 보호 API)를 사용 하 여 암호화 서비스 비밀 키 암호화로 보호 됩니다.

전체 SQL Server를 사용 하는 경우 서비스 계정은 동기화 엔진에 대해 만들어진 데이터베이스의 DBO입니다. 서비스는 다른 사용 권한으로 작동 하지 않습니다. SQL 로그인도 만들어집니다.

또한 계정에는 파일, 레지스트리 키 및 동기화 엔진과 관련 된 기타 개체에 대 한 사용 권한이 부여 됩니다.

### <a name="azure-ad-connector-account"></a>Azure AD 커넥터 계정
Azure AD의 계정이 동기화 서비스의 사용을 위해 생성 됩니다. 이 계정은 표시 이름으로 식별할 수 있습니다.

![AD 계정](./media/reference-connect-accounts-permissions/aadsyncserviceaccount2.png)

계정을 사용 하는 서버의 이름은 사용자 이름의 두 번째 부분에서 식별할 수 있습니다. 그림에서 서버 이름은 DC1입니다. 준비 서버가 있는 경우 각 서버에는 자체 계정이 있습니다.

계정이 만료 되지 않는 길고 복잡 한 암호를 사용 하 여 만들어집니다. 디렉터리 동기화 태스크를 수행할 수 있는 권한만 있는 특수 역할 **디렉터리 동기화 계정이** 부여 됩니다. 이 특수 기본 제공 역할은 Azure AD Connect 마법사 외부에서 부여할 수 없습니다. Azure Portal는 역할 **사용자**와이 계정을 표시 합니다.

Azure AD에서 동기화 서비스 계정은 20 개로 제한 됩니다. Azure AD에서 기존 Azure AD 서비스 계정 목록을 가져오려면 다음 Azure AD PowerShell cmdlet을 실행 합니다. `Get-AzureADDirectoryRole | where {$_.DisplayName -eq "Directory Synchronization Accounts"} | Get-AzureADDirectoryRoleMember`

사용 하지 않는 Azure AD 서비스 계정을 제거 하려면 다음 Azure AD PowerShell cmdlet을 실행 합니다. `Remove-AzureADUser -ObjectId <ObjectId-of-the-account-you-wish-to-remove>`

>[!NOTE]
>위의 PowerShell 명령을 사용 하려면 먼저 [Azure Active Directory powershell For Graph 모듈](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0#installing-the-azure-ad-module) 을 설치 하 고 [AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0) 를 사용 하 여 Azure AD의 인스턴스에 연결 해야 합니다.

Azure AD Connector 계정에 대 한 암호를 관리 하거나 다시 설정 하는 방법에 대 한 자세한 내용은 [Azure AD Connect 계정 관리](how-to-connect-azureadaccount.md) 를 참조 하세요.

## <a name="related-documentation"></a>관련 설명서
[온-프레미스 id를 Azure Active Directory 통합](whatis-hybrid-identity.md)하는 방법에 대 한 설명서를 읽지 않은 경우 다음 표에서 관련 항목에 대 한 링크를 제공 합니다.

|뒷부분 |링크나|  
| --- | --- |
|다운로드 Azure AD Connect | [다운로드 Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771)|
|Express 설정을 사용 하 여 설치 | [Azure AD Connect의 빠른 설치](how-to-connect-install-express.md)|
|사용자 지정 설정을 사용 하 여 설치 | [Azure AD Connect의 사용자 지정 설치](./how-to-connect-install-custom.md)|
|DirSync에서 업그레이드 | [Azure AD sync 도구에서 업그레이드 (DirSync)](how-to-dirsync-upgrade-get-started.md)|
|설치 후 | [설치 확인 및 라이선스 할당](how-to-connect-post-installation.md)|

## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 Id 통합](whatis-hybrid-identity.md)에 대해 자세히 알아보세요.
