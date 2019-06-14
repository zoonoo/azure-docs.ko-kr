---
title: 'Azure AD Connect: ADConnectivityTool PowerShell 모듈이란? | Microsoft Docs'
description: 이 문서는 새로운 ADConnectivity PowerShell 모듈 및 어떻게 문제를 해결 하는 데 사용할 수 있습니다를 소개 합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 4/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd5340cd8c802df4ffbe0207b5401d2fee4e207e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64571125"
---
# <a name="troubleshoot-azure-ad-connectivity-with-the-adconnectivitytool-powershell-module"></a>ADConnectivityTool PowerShell 모듈을 사용 하 여 Azure AD 연결 문제 해결

ADConnectivity 도구는 다음 중 하나에서 사용되는 PowerShell 모듈입니다.

- 설치 하는 동안 네트워크 연결에 문제가 있는의 성공적인 유효성 검사를 하지 못하는 경우 Active Directory 자격 증명을 마법사에서 제공 하는 사용자입니다.
- PowerShell 세션에서 함수를 호출한 사용자가 사후 설치합니다.

도구는 **C:\Program Files\Microsoft Azure Active Directory Connect\Tools\ ADConnectivityTool.psm1**에 있습니다. 

## <a name="adconnectivitytool-during-installation"></a>설치 중의 ADConnectivityTool 작업

Azure AD Connect 마법사의 **디렉터리 연결** 페이지에서 네트워크 문제가 발생하면 ADConnectivityTool에서 자동으로 해당 함수 중 하나를 사용하여 진행 상황을 확인합니다.  다음 중 하나가 네트워크 문제로 간주될 수 있습니다.

- 사용자가 제공한 포리스트의 이름이 잘못 입력되었거나 포리스트가 없다고 했습니다. 
- 사용자가 제공한 포리스트와 연결된 도메인 컨트롤러에서 389 UDP 포트가 닫힙니다.
- 'AD 포리스트 계정' 창에 제공된 자격 증명에 대상 포리스트와 연결된 도메인 컨트롤러를 검색할 수 있는 권한이 없습니다.
- 사용자가 제공한 포리스트와 연결된 도메인 컨트롤러에서 53, 88 또는 389 TCP 포트 중 하나가 닫힙니다. 
- 389 UDP 포트와 TCP 포트(또는 포트)가 모두 닫힙니다.
- 제공된 포리스트 및/또는 연결된 해당 도메인 컨트롤러에 대한 DNS를 확인할 수 없습니다.

이러한 문제가 발견될 때마다 관련 오류 메시지가 AADConnect 마법사에 표시됩니다.


![오류](media/how-to-connect-adconnectivitytools/error1.png)

예를 들어 **디렉터리 연결** 화면에 디렉터리를 추가하려고 하면 Azure AD Connect에서 이를 확인하고 389 포트를 통해 도메인 컨트롤러와 통신할 수 있어야 합니다.  그렇지 않으면 위의 스크린샷에서 보여 주는 오류가 표시됩니다.  

실제로 내부적으로는 Azure AD Connect에서 `Start-NetworkConnectivityDiagnosisTools` 함수를 호출하고 있습니다.  이 함수는 네트워크 연결 문제로 인해 자격 증명의 유효성 검사가 실패할 때 호출됩니다.

마지막으로, 마법사에서 도구를 호출할 때마다 자세한 로그 파일이 생성됩니다. 로그에 위치한 **C:\ProgramData\AADConnect\ADConnectivityTool-\<날짜 >-\<시간 >.log**

## <a name="adconnectivitytools-post-installation"></a>설치 후의 ADConnectivityTools 작업
Azure AD Connect가 설치되면 ADConnectivityTools PowerShell 모듈의 함수를 사용할 수 있습니다.  

함수에 대한 참조 정보는 [ADConnectivityTools 참조](reference-connect-adconnectivitytools.md)에서 찾을 수 있습니다.

### <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

ADConnectivityTool.psm1을 PowerShell로 가져온 후에 **수동으로만** 호출할 수 있으므로 이제 이 함수를 호출하려고 합니다. 

이 함수는 제공된 AD 자격 증명의 유효성을 검사하기 위해 Azure AD Connect 마법사에서 실행하는 것과 동일한 논리를 실행합니다.  그러나 문제와 제안된 솔루션에 대해 훨씬 더 자세한 설명을 제공합니다. 

연결 유효성 검사를 구성하는 단계는 다음과 같습니다.
-   도메인 FQDN(정규화된 도메인 이름) 개체를 가져옵니다.
-   사용자가 '새 AD 계정 만들기'를 선택한 경우 이러한 자격 증명이 엔터프라이즈 관리자 그룹에 속하는지 확인합니다.
-   포리스트 FQDN 개체를 가져옵니다.
-   이전에 가져온 포리스트 FQDN 개체와 연결된 하나 이상의 도메인에 연결할 수 있는지 확인합니다.
-   포리스트의 기능 수준이 Windows Server 2003 이상인지 확인합니다.

이러한 모든 작업이 성공적으로 실행되면 사용자가 디렉터리를 추가할 수 있습니다.

문제가 해결된 후(또는 문제가 전혀 없는 경우) 사용자가 이 함수를 실행하면 사용자는 Azure AD Connect 마법사로 돌아가서 자격 증명을 다시 삽입하려고 시도합니다.



## <a name="next-steps"></a>다음 단계
- [Azure AD Connect: 계정 및 사용 권한](reference-connect-accounts-permissions.md)
- [기본 설치](how-to-connect-install-express.md)
- [사용자 지정 설치](how-to-connect-install-custom.md)
- [ADConnectivityTools 참조](reference-connect-adconnectivitytools.md)

