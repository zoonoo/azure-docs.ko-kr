---
title: 'Azure AD Connect: ADSync 서비스 계정 | Microsoft Docs'
description: 이 항목에서는 ADSync 서비스 계정에 설명 하 고 계정에 대 한 모범 사례를 제공 합니다.
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
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478720"
---
# <a name="adsync-service-account"></a>ADSync 서비스 계정
Azure AD Connect는 Active Directory와 Azure Active Directory 간의 동기화를 오케스트레이션 하는 온-프레미스 서비스를 설치 합니다.  Microsoft Azure AD Sync 동기화 서비스 (ADSync) 온-프레미스 환경의 서버에서 실행 됩니다.  서비스에 대 한 자격 증명 Express 설치에 기본적으로 설정 되었지만 조직의 보안 요구 사항에 맞게 사용자 지정할 수 있습니다.  이러한 자격 증명에 온-프레미스 포리스트 또는 Azure Active Directory에 연결 하는 사용 되지 않습니다.

ADSync 선택 서비스 계정이 Azure AD Connect를 설치 하기 전에 확인 중요 한 계획 결정 합니다.  동기화 데이터베이스에 대 한 액세스를 손실 및 연결 된 디렉터리 (Azure 및 AD DS)를 사용 하 여 인증에 실패 한 설치에서 서비스를 시작 하려면 실패 하면 후 자격 증명을 변경 하려고 합니다.  원래 자격 증명 복원 될 때까지 동기화가 발생 합니다.

## <a name="the-default-adsync-service-account"></a>기본 ADSync 서비스 계정

구성원 서버에서 실행 AdSync 서비스는 가상 서비스 계정 (VSA)의 컨텍스트에서 실행 됩니다.  제품 제한으로 인해 사용자 지정 서비스 계정은 도메인 컨트롤러에 설치 하면 만들어집니다.  Express 설정 서비스 계정에 조직의 보안 요구 사항에 맞지 않으면 사용자 지정 옵션을 선택 하 여 Azure AD Connect를 배포 합니다.  그런 다음 조직의 요구 사항을 충족 하는 서비스 계정 옵션을 선택 합니다.

>[!NOTE]
>도메인 컨트롤러에 설치 된 경우 기본 서비스 계정은 Domain\AAD_InstallationIdentifier 폼입니다.  이 계정의 암호를 임의로 생성 하 고 복구 하 고 암호 회전에 대 한 중요 한 과제를 제공 합니다.  독립 실행형을 사용 하 여 그룹 관리 서비스 계정에 도메인 컨트롤러에서 초기 설치 중 서비스 계정을 사용자 지정 하는 것이 좋습니다 (sMSA / gMSA)

|Azure AD Connect 위치|만든 서비스 계정|
|-----|-----|
|구성원 서버|NT SERVICE\ADSync|
|도메인 컨트롤러|Domain\AAD_74dc30c01e80 (참고 참조)|

## <a name="custom-adsync-service-accounts"></a>사용자 지정 ADSync 서비스 계정
ADSync를 실행 하는 Microsoft 권장 서비스는 가상 서비스 계정 또는 독립 실행형의 컨텍스트에서 또는 그룹 관리 서비스 계정입니다.  특정 조직의 보안 요구 사항에 맞게 프로 비전 서비스 계정을 만들려면 도메인 관리자를 선택할 수도 있습니다.   설치 하는 동안 사용 된 서비스 계정을 사용자 지정 하려면 아래 기본 설정 페이지의 사용자 지정 옵션을 선택 합니다.   사용할 수 있는 옵션은 다음과 같습니다.

- 기본 계정-Azure AD Connect가 계정을 프로 비전 서비스 위에 설명 된 대로
- 관리 서비스 계정-독립 실행형을 사용 하거나 그룹 MSA 관리자가 프로 비전
- 도메인 서비스 관리자에 게 문의 하 여 프로 비전 된 계정을 사용 하 여 도메인 계정

![](media/concept-adsync-service-account/adsync1.png)

![](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>ADSync 서비스 계정 변경 진단
동기화 데이터베이스에 대 한 액세스를 손실 및 연결 된 디렉터리 (Azure 및 AD DS)를 사용 하 여 인증에 실패 한 설치에서 서비스를 시작 하려면 실패 하면 후 ADSync 서비스에 대 한 자격 증명을 변경 합니다.  새 ADSync 서비스 계정에 데이터베이스 액세스를 부여 하는 것이 문제를 복구에 충분 하지 않습니다. 원래 자격 증명 복원 될 때까지 동기화가 발생 합니다.

ADSync 서비스를 시작할 수 없는 경우 이벤트 로그에 오류 수준 메시지가 발급 됩니다.  메시지의 콘텐츠 사용에서 기본 제공 데이터베이스 (localdb) 또는 전체 SQL 인지 여부에 따라 달라 집니다.  다음은 나타날 수 있는 이벤트 로그 항목의 예입니다.

### <a name="example-1"></a>예 1

AdSync 서비스 암호화 키를 찾을 수 없는 다시 만들었습니다.  이 문제를 해결할 때까지 동기화 발생 하지 않습니다.

이 문제는 Microsoft Azure AD Sync를 문제 해결 암호화 키 AdSync 서비스 로그온 자격 증명을 변경 하는 경우에 액세스할 수 없게 됩니다.  자격 증명을 변경 하는 경우 서비스 응용 프로그램을 사용 하 여 (예: 원래 구성 된 값으로 다시 로그온 계정을 변경 하려면 NT SERVICE\AdSync) 서비스를 다시 시작 합니다.  AdSync 서비스의 올바른 작업을 즉시 복원 됩니다.

다음을 참조 하세요 [문서](https://go.microsoft.com/fwlink/?linkid=2086764) 대 한 자세한 내용은 합니다.

### <a name="example-2"></a>예 2

서비스는 로컬 데이터베이스 (localdb)에 대 한 연결을 설정할 수 없어 시작할 수 없습니다.

이 문제는 Microsoft Azure AD Sync 서비스 문제 해결 AdSync 서비스 로그온 자격 증명을 변경 하는 경우 로컬 데이터베이스 공급자에 액세스 권한을 잃게 됩니다.  자격 증명 변경 된 경우 서비스 응용 프로그램을 사용 하 여 (예: 원래 구성 된 값으로 다시 로그온 계정을 변경 하려면 NT SERVICE\AdSync) 서비스를 다시 시작 합니다.  AdSync 서비스의 올바른 작업을 즉시 복원 됩니다.

다음을 참조 하세요 [문서](https://go.microsoft.com/fwlink/?linkid=2086764) 대 한 자세한 내용은 합니다.

다음 오류 정보를 공급자에 의해 반환 된 추가 정보:
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.