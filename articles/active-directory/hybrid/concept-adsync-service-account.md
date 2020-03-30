---
title: 'Azure AD 연결: ADSync 서비스 계정 | 마이크로 소프트 문서'
description: 이 항목에서는 ADSync 서비스 계정에 대해 설명하고 계정에 대한 모범 사례를 제공합니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67478720"
---
# <a name="adsync-service-account"></a>ADSync 서비스 계정
Azure AD Connect는 Active Directory와 Azure Active Directory 간의 동기화를 오케스트레이션하는 온-프레미스 서비스를 설치합니다.  Microsoft Azure AD 동기화 동기화 서비스(ADSync)는 온-프레미스 환경의 서버에서 실행됩니다.  서비스에 대한 자격 증명은 Express 설치에서 기본적으로 설정되지만 조직 보안 요구 사항을 충족하도록 사용자 지정될 수 있습니다.  이러한 자격 증명은 온-프레미스 포리스트 또는 Azure Active Directory에 연결하는 데 사용되지 않습니다.

ADSync 서비스 계정을 선택하는 것은 Azure AD Connect를 설치하기 전에 결정해야 하는 중요한 계획 결정입니다.  설치 후 자격 증명을 변경하면 서비스가 시작되지 못하고 동기화 데이터베이스에 대한 액세스가 손실되고 연결된 디렉터리(Azure 및 AD DS)로 인증하지 못하는 문제가 발생합니다.  원래 자격 증명이 복원될 때까지 동기화가 수행되지 않습니다.

## <a name="the-default-adsync-service-account"></a>기본 ADSync 서비스 계정

구성원 서버에서 실행될 때 AdSync 서비스는 VSA(가상 서비스 계정)의 컨텍스트에서 실행됩니다.  제품 제한으로 인해 도메인 컨트롤러에 설치할 때 사용자 지정 서비스 계정이 만들어집니다.  Express 설정 서비스 계정이 조직 보안 요구 사항을 충족하지 않는 경우 사용자 지정 옵션을 선택하여 Azure AD Connect를 배포합니다.  그런 다음 조직의 요구 사항을 충족하는 서비스 계정 옵션을 선택합니다.

>[!NOTE]
>도메인 컨트롤러에 설치할 때 기본 서비스 계정은 Domain\AAD_InstallationIdentifier 양식입니다.  이 계정의 암호는 임의로 생성되며 복구 및 암호 회전에 중요한 문제가 발생합니다.  독립 실행형 또는 그룹 관리 서비스 계정(sMSA / gMSA)을 사용하려면 도메인 컨트롤러에 처음 설치하는 동안 서비스 계정을 사용자 지정하는 것이 좋습니다.

|Azure AD 연결 위치|생성된 서비스 계정|
|-----|-----|
|멤버 서버|NT 서비스\애드싱크|
|도메인 컨트롤러|도메인\AAD_74dc30c01e80(참고 참조)|

## <a name="custom-adsync-service-accounts"></a>사용자 지정 애드싱크 서비스 계정
가상 서비스 계정 또는 독립 실행형 또는 그룹 관리 서비스 계정의 컨텍스트에서 ADSync 서비스를 실행하는 것이 좋습니다.  도메인 관리자는 특정 조직 보안 요구 사항을 충족하도록 프로비저닝된 서비스 계정을 만들 수도 있습니다.   설치 하는 동안 사용 하는 서비스 계정을 사용자 지정 하려면 아래 익스프레스 설정 페이지에서 사용자 지정 옵션을 선택 합니다.   다음 옵션을 사용할 수 있습니다.

- 기본 계정 - Azure AD Connect는 위에서 설명한 대로 서비스 계정을 프로비전합니다.
- 관리되는 서비스 계정 - 관리자가 프로비전한 독립 실행형 또는 그룹 MSA 사용
- 도메인 계정 - 관리자가 프로비전한 도메인 서비스 계정 사용

![](media/concept-adsync-service-account/adsync1.png)

![](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>ADSync 서비스 계정 변경 진단
설치 후 ADSync 서비스에 대한 자격 증명을 변경하면 서비스가 시작되지 못하고 동기화 데이터베이스에 대한 액세스가 손실되고 연결된 디렉터리(Azure 및 AD DS)로 인증하지 못하는 문제가 발생합니다.  새 ADSync 서비스 계정에 대한 데이터베이스 액세스 권한을 부여하면 이 문제를 복구하기에 충분하지 않습니다. 원래 자격 증명이 복원될 때까지 동기화가 수행되지 않습니다.

ADSync 서비스는 시작할 수 없을 때 이벤트 로그에 오류 수준 메시지를 발행합니다.  메시지의 내용은 기본 제공 데이터베이스(localdb) 또는 전체 SQL이 사용 중인지 여부에 따라 달라집니다.  다음은 존재할 수 있는 이벤트 로그 항목의 예입니다.

### <a name="example-1"></a>예 1

AdSync 서비스 암호화 키를 찾을 수 없고 다시 만들었습니다.  이 문제가 해결될 때까지 동기화가 수행되지 않습니다.

이 문제 해결 AdSync 서비스 로그온 자격 증명이 변경되면 Microsoft Azure AD 동기화 암호화 키에 액세스할 수 없게 됩니다.  자격 증명이 변경된 경우 서비스 응용 프로그램을 사용하여 Log On 계정을 원래 구성된 값(예: NT 서비스\AdSync)를 하고 서비스를 다시 시작합니다.  그러면 AdSync 서비스의 올바른 작업이 즉시 복원됩니다.

자세한 내용은 다음 [문서를](https://go.microsoft.com/fwlink/?linkid=2086764) 참조하십시오.

### <a name="example-2"></a>예제 2

로컬 데이터베이스(localdb)에 대한 연결을 설정하지 못했기 때문에 서비스를 시작할 수 없습니다.

이 문제 해결 Microsoft Azure AD Sync 서비스는 AdSync 서비스 로그온 자격 증명이 변경된 경우 로컬 데이터베이스 공급자에 액세스할 수 있는 권한을 잃게 됩니다.  자격 증명이 변경된 경우 서비스 응용 프로그램을 사용하여 Log On 계정을 원래 구성된 값(예: NT 서비스\AdSync)를 하고 서비스를 다시 시작합니다.  그러면 AdSync 서비스의 올바른 작업이 즉시 복원됩니다.

자세한 내용은 다음 [문서를](https://go.microsoft.com/fwlink/?linkid=2086764) 참조하십시오.

추가 세부 정보 공급자가 다음 오류 정보를 반환했습니다.
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.