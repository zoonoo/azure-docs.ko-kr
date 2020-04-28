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
ms.topic: reference
ms.date: 06/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f228da5afc5998d8fa59ce2d720cec4c9f955b67
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "67478720"
---
# <a name="adsync-service-account"></a>ADSync 서비스 계정
Azure AD Connect Active Directory와 Azure Active Directory 간의 동기화를 오케스트레이션 온-프레미스 서비스를 설치 합니다.  ADSync (Microsoft Azure AD Sync synchronization service)는 온-프레미스 환경의 서버에서 실행 됩니다.  서비스의 자격 증명은 기본적으로 Express 설치에서 설정 되지만 조직의 보안 요구 사항에 맞게 사용자 지정할 수 있습니다.  이러한 자격 증명은 온-프레미스 포리스트 또는 Azure Active Directory 연결 하는 데 사용 되지 않습니다.

ADSync 서비스 계정을 선택 하는 것은 Azure AD Connect를 설치 하기 전에 수행 해야 하는 중요 한 계획 결정입니다.  설치 후 자격 증명을 변경 하려고 하면 서비스가 시작 되지 않고, 동기화 데이터베이스에 대 한 액세스 권한이 손실 되며, 연결 된 디렉터리 (Azure 및 AD DS)를 사용 하 여 인증에 실패 하 게 됩니다.  원래 자격 증명이 복원 되기 전 까지는 동기화가 수행 되지 않습니다.

## <a name="the-default-adsync-service-account"></a>기본 ADSync 서비스 계정

구성원 서버에서 실행 될 때 AdSync 서비스는 VSA (가상 서비스 계정)의 컨텍스트에서 실행 됩니다.  제품 제한으로 인해 사용자 지정 서비스 계정은 도메인 컨트롤러에 설치 될 때 생성 됩니다.  Express 설정 서비스 계정이 조직의 보안 요구 사항을 충족 하지 않는 경우 사용자 지정 옵션을 선택 하 여 Azure AD Connect을 배포 합니다.  그런 다음 조직의 요구 사항을 충족 하는 서비스 계정 옵션을 선택 합니다.

>[!NOTE]
>도메인 컨트롤러에 설치 된 경우 기본 서비스 계정은 도메인 \ AAD_InstallationIdentifier 형식입니다.  이 계정에 대 한 암호는 임의로 생성 되며 복구 및 암호 순환에 상당한 문제를 제공 합니다.  독립 실행형 또는 그룹 관리 서비스 계정 (sMSA/gMSA)을 사용 하려면 도메인 컨트롤러에서 초기 설치 중에 서비스 계정을 사용자 지정 하는 것이 좋습니다.

|Azure AD Connect 위치|서비스 계정이 만들어짐|
|-----|-----|
|구성원 서버|NT SERVICE\ADSync|
|도메인 컨트롤러|도메인 \ AAD_74dc30c01e80 (참고 참조)|

## <a name="custom-adsync-service-accounts"></a>사용자 지정 ADSync 서비스 계정
Microsoft는 가상 서비스 계정 또는 독립 실행형 또는 그룹 관리 서비스 계정의 컨텍스트에서 ADSync 서비스를 실행 하는 것을 권장 합니다.  도메인 관리자는 특정 조직의 보안 요구 사항에 맞게 프로 비전 된 서비스 계정을 만들도록 선택할 수도 있습니다.   설치 중에 사용 되는 서비스 계정을 사용자 지정 하려면 아래의 Express 설정 페이지에서 사용자 지정 옵션을 선택 합니다.   다음 옵션을 사용할 수 있습니다.

- 기본 계정 – Azure AD Connect 위에서 설명한 대로 서비스 계정을 프로 비전 합니다.
- 관리 서비스 계정 – 관리자가 프로 비전 한 독립 실행형 또는 그룹 MSA를 사용 합니다.
- 도메인 계정 – 관리자가 프로 비전 한 도메인 서비스 계정을 사용 합니다.

![](media/concept-adsync-service-account/adsync1.png)

![](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>ADSync 서비스 계정 변경 진단
설치 후 ADSync 서비스에 대 한 자격 증명을 변경 하면 서비스가 시작 되지 않고, 동기화 데이터베이스에 대 한 액세스 권한이 손실 되며, 연결 된 디렉터리 (Azure 및 AD DS)를 사용 하 여 인증에 실패 합니다.  새 ADSync 서비스 계정에 대 한 데이터베이스 액세스 권한을 부여 하는 것 만으로는이 문제를 복구할 수 없습니다. 원래 자격 증명이 복원 되기 전 까지는 동기화가 수행 되지 않습니다.

ADSync 서비스는 시작할 수 없는 경우 이벤트 로그에 오류 수준 메시지를 발행 합니다.  메시지의 내용은 기본 제공 데이터베이스 (localdb) 또는 전체 SQL의 사용 여부에 따라 달라 집니다.  다음은 표시 될 수 있는 이벤트 로그 항목의 예입니다.

### <a name="example-1"></a>예 1

AdSync 서비스 암호화 키를 찾을 수 없어서 다시 만들었습니다.  이 문제가 해결 될 때까지 동기화가 수행 되지 않습니다.

이 문제를 해결 하는 경우 AdSync 서비스 로그온 자격 증명이 변경 되 면 Microsoft Azure AD 동기화 암호화 키에 액세스할 수 없게 됩니다.  자격 증명이 변경 된 경우 서비스 응용 프로그램을 사용 하 여 로그온 계정을 원래 구성 된 값으로 다시 변경 합니다 (예: NT SERVICE\AdSync)를 클릭 하 고 서비스를 다시 시작 합니다.  그러면 AdSync 서비스의 올바른 작업이 즉시 복원 됩니다.

자세한 내용은 다음 [문서](https://go.microsoft.com/fwlink/?linkid=2086764) 를 참조 하세요.

### <a name="example-2"></a>예제 2

로컬 데이터베이스 (localdb)에 대 한 연결을 설정할 수 없기 때문에 서비스를 시작할 수 없습니다.

이 문제를 해결 하는 경우 AdSync 서비스 로그온 자격 증명을 변경 하는 경우 동기화 서비스 Microsoft Azure AD는 로컬 데이터베이스 공급자에 대 한 액세스 권한을 잃게 됩니다.  자격 증명이 변경 된 경우 서비스 응용 프로그램을 사용 하 여 로그온 계정을 원래 구성 된 값으로 다시 변경 합니다 (예: NT SERVICE\AdSync)를 클릭 하 고 서비스를 다시 시작 합니다.  그러면 AdSync 서비스의 올바른 작업이 즉시 복원 됩니다.

자세한 내용은 다음 [문서](https://go.microsoft.com/fwlink/?linkid=2086764) 를 참조 하세요.

추가 정보 공급자에서 반환 된 오류 정보는 다음과 같습니다.
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.