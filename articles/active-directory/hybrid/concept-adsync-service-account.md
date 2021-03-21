---
title: 'Azure AD Connect: ADSync 서비스 계정 | Microsoft Docs'
description: 이 항목에서는 ADSync 서비스 계정에 대해 설명 하 고 계정에 대 한 모범 사례를 제공 합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/17/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb23d79caa6964c3f61fbb84c8b8f229f475b8ab
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722160"
---
# <a name="adsync-service-account"></a>ADSync 서비스 계정
Azure AD Connect Active Directory와 Azure Active Directory 간의 동기화를 오케스트레이션 온-프레미스 서비스를 설치 합니다.  ADSync (Microsoft Azure AD Sync synchronization service)는 온-프레미스 환경의 서버에서 실행 됩니다.  서비스의 자격 증명은 기본적으로 Express 설치에서 설정 되지만 조직의 보안 요구 사항에 맞게 사용자 지정할 수 있습니다.  이러한 자격 증명은 온-프레미스 포리스트 또는 Azure Active Directory 연결 하는 데 사용 되지 않습니다.

ADSync 서비스 계정을 선택 하는 것은 Azure AD Connect를 설치 하기 전에 수행 해야 하는 중요 한 계획 결정입니다.  설치 후 자격 증명을 변경 하려고 하면 서비스가 시작 되지 않고, 동기화 데이터베이스에 대 한 액세스 권한이 손실 되며, 연결 된 디렉터리 (Azure 및 AD DS)를 사용 하 여 인증에 실패 하 게 됩니다.  원래 자격 증명이 복원 되기 전 까지는 동기화가 수행 되지 않습니다.

동기화 서비스는 다양한 계정으로 실행할 수 있습니다. VSA (가상 서비스 계정), 관리 서비스 계정 (gMSA/sMSA) 또는 일반 사용자 계정으로 실행할 수 있습니다. 지원 되는 옵션은 새로 설치 하는 경우 Azure AD Connect의 2017 년 4 월 릴리스 및 2021 년 3 월 릴리스를 사용 하 여 변경 되었습니다. Azure AD Connect의 이전 버전을 업그레이드하는 경우 이러한 추가 옵션을 사용할 수 없습니다. 


|계정 유형|설치 옵션|Description| 
|-----|------|-----|
|가상 서비스 계정|빠른 및 사용자 지정, 2017년 4월 이후| 가상 서비스 계정은 도메인 컨트롤러에 설치 하는 경우를 제외 하 고 모든 빠른 설치에 사용 됩니다. 사용자 지정 설치를 사용 하는 경우 다른 옵션을 사용 하지 않는 한 기본 옵션입니다.| 
|관리 서비스 계정|사용자 지정, 2017년 4월 이후|원격 SQL Server를 사용 하는 경우 그룹 관리 서비스 계정을 사용 하는 것이 좋습니다. |
|관리 서비스 계정|Express 및 custom, 2021 년 3 월 이상|ADSyncMSA_ 접두사가 있는 독립 실행형 관리 서비스 계정은 도메인 컨트롤러에 설치 된 경우 설치 하는 동안 만들어집니다. 사용자 지정 설치를 사용 하는 경우 다른 옵션을 사용 하지 않는 한 기본 옵션입니다.|
|사용자 계정|Express 및 사용자 지정, 2017 년 4 월 ~ 2021 년 3 월|AAD_ 접두사가 추가 된 사용자 계정은 도메인 컨트롤러에 설치 될 때 express 설치를 설치 하는 동안 만들어집니다. 사용자 지정 설치를 사용 하는 경우 다른 옵션을 사용 하지 않는 한 기본 옵션입니다.|
|사용자 계정|빠른 및 사용자 지정, 2017 3월 이전|AAD_ 접두사가 추가 된 사용자 계정은 express 설치를 설치 하는 동안 만들어집니다. 사용자 지정 설치를 사용하는 경우 다른 계정을 지정할 수 있습니다.| 

>[!IMPORTANT]
> Connect의 2017년 3월 이전 빌드를 사용하는 경우에는 서비스 계정의 암호를 재설정하지 말아야 합니다. 보안상의 이유로 Windows에서 암호화 키를 제거하기 때문입니다. Azure AD Connect를 다시 설치하지 않고는 계정을 다른 계정으로 변경할 수 없습니다. 2017 4 월 이후의 빌드로 업그레이드 하는 경우 서비스 계정에서 암호를 변경 하는 것이 지원 되지만 사용 된 계정은 변경할 수 없습니다. 

> [!IMPORTANT]
> 서비스 계정은 처음 설치할 때만 설정할 수 있습니다. 설치가 완료 된 후에는 서비스 계정을 변경할 수 없습니다. 서비스 계정 암호를 변경 해야 하는 경우이는 지원 되며 [여기](how-to-connect-sync-change-serviceacct-pass.md)에서 지침을 찾을 수 있습니다.

다음은 동기화 서비스 계정에 대 한 기본, 권장 및 지원 옵션 표입니다. 

범례: 

- **굵은 글꼴** 은 기본 옵션을 나타내며 대부분의 경우 권장 되는 옵션입니다. 
- *기울임꼴* 은 기본 옵션이 아닌 경우 권장 옵션을 나타냅니다. 
- 굵지 않은 글꼴 - 지원되는 옵션 
- 로컬 계정 - 서버의 로컬 사용자 계정 
- 도메인 계정 - 도메인 사용자 계정 
- sMSA - [독립 실행형 관리 서비스 계정](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10))
- gMSA - [그룹 관리 서비스 계정](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)) 

 ||**LocalDB </br> Express**|**LocalDB/LocalSQL </br> 사용자 지정**|**원격 SQL </br> 사용자 지정**|
|-----|-----|-----|-----|
|**도메인에 가입된 컴퓨터**|**VSA**|**VSA**</br> *sMSA*</br> *gMSA*</br> 로컬 계정</br> 도메인 계정| *gMSA* </br>도메인 계정|
|도메인 컨트롤러| **sMSA**|**sMSA** </br>*gMSA*</br> 도메인 계정|*gMSA*</br>도메인 계정| 

## <a name="virtual-service-account"></a>가상 서비스 계정 

가상 서비스 계정은 암호가 없고 Windows에서 자동으로 관리 되는 특별 한 유형의 관리 되는 로컬 계정입니다. 

 ![가상 서비스 계정](media/concept-adsync-service-account/account-1.png)

가상 서비스 계정은 동기화 엔진과 SQL이 동일한 서버에 있는 시나리오에서 사용 하기 위한 것입니다. 원격 SQL을 사용 하는 경우 그룹 관리 서비스 계정을 대신 사용 하는 것이 좋습니다. 

[WINDOWS DPAPI (데이터 보호 API)](https://msdn.microsoft.com/library/ms995355.aspx) 문제로 인해 도메인 컨트롤러에서 가상 서비스 계정을 사용할 수 없습니다. 

## <a name="managed-service-account"></a>관리 서비스 계정 

원격 SQL Server를 사용 하는 경우 그룹 관리 서비스 계정을 사용 하는 것이 좋습니다. 그룹 관리 서비스 계정에 대 한 Active Directory을 준비 하는 방법에 대 한 자세한 내용은 [그룹 관리 서비스 계정 개요](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11))를 참조 하세요. 

이 옵션을 사용하려면 [필수 구성 요소 설치](how-to-connect-install-custom.md#install-required-components) 페이지에서 **기존 서비스 계정 사용** 을 선택하고 **관리 서비스 계정** 을 선택합니다. 

 ![관리 서비스 계정](media/concept-adsync-service-account/account-2.png)

독립 실행형 관리 서비스 계정을 사용하는 것도 지원됩니다. 그러나 이러한 항목은 로컬 컴퓨터 에서만 사용할 수 있으며 기본 가상 서비스 계정에서 사용 하는 경우에는 도움이 되지 않습니다. 

### <a name="auto-generated-standalone-managed-service-account"></a>자동 생성 된 독립 실행형 관리 서비스 계정 

도메인 컨트롤러에 Azure AD Connect를 설치 하는 경우 사용자 지정 설정에 사용할 계정을 지정 하지 않으면 설치 마법사에서 독립 실행형 관리 서비스 계정이 만들어집니다. 계정에는 접두사가 **ADSyncMSA_** 되며 실제 동기화 서비스를 실행 하는 데 사용 됩니다. 

이 계정은 암호가 없고 Windows에서 자동으로 관리 되는 관리 되는 도메인 계정입니다. 

이 계정은 동기화 엔진과 SQL이 도메인 컨트롤러에 있는 시나리오에서 사용 하기 위한 것입니다. 

## <a name="user-account"></a>사용자 계정 

로컬 서비스 계정은 설치 마법사에서 만듭니다.(사용자 지정 설정에 사용할 계정을 지정하지 않으면) 계정은 AAD_ 를 접두사로 하며 실행할 실제 동기화 서비스에 사용됩니다. 도메인 컨트롤러에 Azure AD Connect를 설치하는 경우 계정은 도메인에 만들어집니다. 다음과 같은 경우 AAD_ 서비스 계정이 도메인에 있어야 합니다. 
- SQL Server를 실행 하는 원격 서버를 사용 합니다. 
- 인증이 필요한 프록시를 사용합니다. 

 ![사용자 계정(user account)](media/concept-adsync-service-account/account-3.png)

계정은 만료되지 않은 길고 복잡한 암호를 사용하여 만들어집니다. 

이 계정은 다른 계정에 대 한 암호를 안전한 방식으로 저장 하는 데 사용 됩니다. 이러한 다른 계정 암호는 데이터베이스에 암호화된 상태로 저장됩니다. 암호화 키의 프라이빗 키는 Windows DPAPI(데이터 보호 API)를 사용하여 암호화 서비스 비밀 키 암호화로 보호됩니다. 

전체 SQL Server를 사용하는 경우 서비스 계정은 동기화 엔진에 대해 만든 데이터베이스의 DBO입니다. 서비스는 다른 사용 권한으로 작동 하지 않습니다. SQL 로그인도 생성됩니다. 

또한 계정에는 파일, 레지스트리 키 및 동기화 엔진과 관련 된 기타 개체에 대 한 사용 권한도 부여 됩니다. 


## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.
