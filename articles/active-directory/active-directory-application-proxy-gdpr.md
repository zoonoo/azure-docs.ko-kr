---
title: Azure Active Directory 응용 프로그램 프록시의 GDPR | Microsoft Docs
description: Azure Active Directory 응용 프로그램 프록시의 GDPR에 대해 자세히 알아봅니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: c7186f98-dd80-4910-92a4-a7b8ff6272b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: a3df15743b4918d72fac5f8769a2d3ee721a452f
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2018
---
# <a name="gdpr-in-the-azure-active-directory-application-proxy"></a>Azure Active Directory 응용 프로그램 프록시의 GDPR  

Azure AD(Azure Active Directory) 응용 프로그램 프록시는 다른 모든 Microsoft 서비스 및 기능과 함께 GDPR 규정을 준수합니다. Microsoft의 GDPR 지원에 대한 자세한 정보는 [사용 조건 및 설명서](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)를 참조하세요.

이 기능에는 컴퓨터의 커넥터가 포함되어 있기 때문에 GDPR 규정 준수를 유지하기 위해 모니터링해야 하는 이벤트가 몇 가지 있습니다. 응용 프로그램 프록시는 EUII를 포함할 수 있는 다음 로그 형식을 만듭니다.

- 커넥터 이벤트 로그
- Windows 이벤트 로그

다음 두 가지 방법으로 GDPR 규격 상태를 유지할 수 있습니다.

- 요청이 발생하면 삭제하고 내보내기

- 로깅 해제

관련 작업:

- Windows 이벤트 로그에 대한 데이터 보존을 구성하는 방법은 [이벤트 로그에 대한 설정](https://technet.microsoft.com/library/cc952132.aspx)을 참조하세요. 
- Windows 이벤트 로그에 대한 일반적인 정보는 [Windows 이벤트 로그 사용](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx)을 참조하세요.


연결 이벤트 로그는 `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace`에서 찾을 수 있습니다. 다음 섹션에는 커넥터 이벤트 로그에 관련된 단계가 제공됩니다. 이 단계는 모든 커넥터 컴퓨터에서 완료해야 합니다.
 

## <a name="processing-requests"></a>요청 처리

처리해야 할 요청 유형은 세 가지입니다. 

- 삭제
- 보기
- 내보내기
 
보기/내보내기 요청을 처리하려면 각 로그 파일을 검토하여 관련 항목을 검색해야 합니다. 

로그는 텍스트 파일이기 때문에 예를 들어 사용자와 관련된 항목을 찾으려면 `findstr` 명령을 사용하여 검색할 수 있습니다. 다음 필드가 로그에 있을 수 있으므로 검색합니다. 

- UserId
- Kerberos 제한 위임을 사용하는 모든 응용 프로그램에 대해 구성된 사용자 이름 유형:
    - 사용자 계정 이름
    - 온-프레미스 사용자 계정 이름
    - UPN(사용자 계정 이름)의 사용자 이름 부분
    - 온-프레미스 사용자 계정 이름의 사용자 이름 부분
    - 온-프레미스 SAM 계정 이름 

 
그런 다음, 이 필드를 수집하여 사용자와 공유할 수 있습니다.
삭제 요청을 처리하려면 관련 로그를 삭제해야 합니다. 새 로그 파일을 생성하려면 커넥터 서비스(Microsoft Azure AD 응용 프로그램 프록시 커넥터)를 다시 시작합니다. 새 로그 파일을 사용하면 이전 로그 파일을 삭제할 수 있습니다. 보기/내보내기 프로세스를 수행하여 관련 로그를 모두 찾은 다음, 해당 필드나 파일을 선별적으로 삭제할 수 있습니다. 또한, 이전 로그 파일이 더 이상 필요하지 않으면 언제나 삭제할 수 있습니다.


## <a name="turn-off-connector-logs"></a>커넥터 로그 해제

커넥터 로그를 해제하려면 강조 표시된 줄을 제거하여 `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config`를 조정해야 합니다. 


![구성](./media/active-directory-application-proxy-gdpr/01.png)


## <a name="next-steps"></a>다음 단계

Azure AD 응용 프로그램 프록시에 대한 개요는 [온-프레미스 응용 프로그램에 보안 원격 액세스를 제공하는 방법](manage-apps/application-proxy.md)을 참조하세요.

