---
title: Azure Active Directory Connect FAQ | Microsoft Docs
description: 이 문서에는 Azure AD Connect에 대해 자주 묻는 질문과 대답이 나와 있습니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 4e47a087-ebcd-4b63-9574-0c31907a39a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 4f3ffb3c769adeabd1edc10ce064653f7b336330
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2018
ms.locfileid: "42142993"
---
# <a name="azure-active-directory-connect-faq"></a>Azure Active Directory Connect FAQ

## <a name="general-installation"></a>일반 설치
**Q: Azure AD(Azure Active Directory) 전역 관리자가 2FA(2단계 인증)를 사용하도록 설정하는 경우 설치가 작동하나요?**  
이 시나리오는 2016년 2월 빌드부터 지원됩니다.

**Q: Azure AD Connect를 무인 설치하는 방법이 있나요?**  
Azure AD Connect 설치는 설치 마법사를 사용하는 경우에만 지원됩니다. 무인 자동 설치는 지원되지 않습니다.

**Q: 하나의 도메인에 연결할 수 없는 포리스트가 있습니다. Azure AD Connect를 설치하려면 어떻게 하나요?**  
이 시나리오는 2016년 2월 빌드부터 지원됩니다.

**Q: Azure AD DS(Azure Active Directory Domain Services) 상태 에이전트는 서버 코어에서 작동하나요?**  
예. 에이전트가 설치되면 다음 PowerShell cmdlet을 사용하여 등록 프로세스를 완료할 수 있습니다. 

`Register-AzureADConnectHealthADDSAgent -Credentials $cred`

**Q: Azure AD Connect는 두 도메인에서 Azure AD로의 동기화를 지원하나요?**  
예, 이 시나리오는 지원됩니다. [여러 도메인](active-directory-aadconnect-multiple-domains.md)을 참조하세요.
 
**Q: Azure AD Connect에서 동일한 Active Directory 도메인에 여러 커넥터를 사용할 수 있나요?**  
아니요, 동일한 AD 도메인에 여러 커넥터를 사용할 수 없습니다. 

**Q: Azure AD Connect 데이터베이스를 로컬 데이터베이스에서 원격 SQL Server 인스턴스로 이동할 수 있나요?**   
예, 이 작업을 수행하는 방법에 대한 일반적인 지침은 다음과 같습니다. 현재 문서를 더 자세히 준비하고 있습니다.
1. LocalDB ADSync 데이터베이스를 백업합니다.
이 작업을 수행하는 가장 간단한 방법은 Azure AD Connect와 동일한 컴퓨터에 설치된 SQL Server Management Studio를 사용하는 것입니다. *(localdb)\.\ADSync*에 연결한 다음, ADSync 데이터베이스를 백업합니다.

2. ADSync 데이터베이스를 원격 SQL Server 인스턴스에 복원합니다.

3. 기존 [원격 SQL 데이터베이스](active-directory-aadconnect-existing-database.md)에 대해 Azure AD Connect를 설치합니다.
   문서에서는 로컬 SQL 데이터베이스를 사용하여 마이그레이션하는 방법을 보여 줍니다. 원격 SQL 데이터베이스를 사용하여 마이그레이션하는 경우, 이 프로세스의 5단계에서 Windows 동기화 서비스가 실행될 기존 서비스 계정도 입력해야 합니다. 다음은 이 동기화 엔진 서비스 계정에 대한 설명입니다.
   
      **기존 서비스 계정 사용**: 기본적으로 Azure AD Connect는 사용할 동기화 서비스에 대한 가상 서비스 계정을 사용합니다. 원격 SQL Server 인스턴스를 사용하거나 인증이 필요한 프록시를 사용하는 경우, 도메인의 관리 서비스 계정 또는 서비스 계정을 사용하고 암호를 알고 있어야 합니다. 이러한 경우에 사용할 계정을 입력합니다. 설치를 실행하는 사용자가 서비스 계정에 대한 로그인 자격 증명을 만들 수 있도록 SQL의 시스템 관리자인지 확인합니다. 자세한 내용은 [Azure AD Connect 계정 및 권한](active-directory-aadconnect-accounts-permissions.md#adsync-service-account)을 참조하세요. 
   
      최신 빌드를 사용하면 이제는 SQL 관리자가 대역 외에서 데이터베이스를 프로비전한 후 데이터베이스 소유권이 있는 Azure AD Connect 관리자가 설치할 수 있습니다. 자세한 내용은 [SQL 위임된 관리자 권한을 사용하여 Azure AD Connect 설치](active-directory-aadconnect-sql-delegation.md)를 참조하세요.

간단히 하려면 SQL의 시스템 관리자로 Azure AD Connect를 설치하는 것이 좋습니다. 그러나 최신 빌드를 사용하면 이제 [SQL 위임된 관리자 권한을 사용하여 Azure AD Connect 설치](active-directory-aadconnect-sql-delegation.md)에서 설명한 대로 SQL 위임된 관리자를 사용할 수 있습니다.

## <a name="network"></a>네트워크
**Q: 방화벽, 네트워크 장치 또는 네트워크에서 열려 있는 상태로 유지할 수 있는 연결 시간을 제한하는 다른 요소가 있습니다. Azure AD Connect를 사용하는 경우 클라이언트 쪽 시간 제한 임계값은 어떻게 되나요?**  
모든 네트워킹 소프트웨어, 물리적 장치 또는 열린 상태로 유지할 수 있는 최대 연결 시간을 제한하는 다른 요소는 Azure AD Connect 클라이언트를 설치한 서버와 Azure Active Directory 간의 연결에 대해 최소 5분(300초)의 임계값을 사용해야 합니다. 이 권장 사항은 이전에 릴리스된 모든 Microsoft ID 동기화 도구에도 적용됩니다.

**Q: SLD(단일 레이블 도메인)가 지원되나요?**  
이 네트워크 구성에 대해 적극 권장하며([문서 참조](https://support.microsoft.com/help/2269810/microsoft-support-for-single-label-domains)) 단일 수준 도메인에 대한 네트워크 구성이 올바르게 작동하는 한 단일 레이블 도메인으로 Azure AD Connect 동기화 사용이 지원됩니다.

**Q: 비연속 AD 도메인을 갖는 포리스트가 지원되나요?**  
아니요. Azure AD Connect는 비연속 네임스페이스가 있는 온-프레미스 포리스트를 지원하지 않습니다.

**Q: "점"으로 구분된 NetBIOS 이름이 지원되나요?**  
아니요, Azure AD Connect는 NetBIOS 이름에 점(.)이 있는 온-프레미스 포리스트 또는 도메인을 지원하지 않습니다.

**Q: 순수한 IPv6 환경이 지원되나요?**  
아니요. Azure AD Connect는 순수한 IPv6 환경을 지원하지 않습니다.

## <a name="federation"></a>페더레이션
**Q: 내 Office 365 인증서를 갱신하도록 요청하는 메일을 받으면 어떻게 해야 하나요?**  
인증서 갱신에 대한 지침은 [인증서 갱신](active-directory-aadconnect-o365-certs.md)을 참조하세요.

**Q: Office 365 신뢰 당사자에 대해 "신뢰 당사자 자동 업데이트"를 설정했습니다. 토큰 서명 인증서가 자동으로 롤오버될 때 어떤 조치를 취해야 하나요?**  
[인증서 갱신](active-directory-aadconnect-o365-certs.md) 문서에 설명된 지침을 사용하세요.

## <a name="environment"></a>Environment
**Q: Azure AD Connect를 설치한 후에 서버 이름을 변경하는 것이 지원되나요?**  
아니요. 서버 이름을 변경하면 동기화 엔진이 SQL 데이터베이스 인스턴스에 연결할 수 없으므로 서비스를 시작할 수 없습니다.

## <a name="identity-data"></a>ID 데이터
**Q: Azure AD의 userPrincipalName(UPN) 특성이 온-프레미스 UPN과 일치하지 않는 이유는 무엇인가요?**  
자세한 내용은 다음 문서를 참조하세요.

* [Office 365, Azure 또는 Intune의 사용자 이름이 온-프레미스 UPN 또는 대체 로그인 ID와 일치하지 않습니다.](https://support.microsoft.com/kb/2523192)
* [다른 페더레이션된 도메인을 사용하기 위해 사용자 계정의 UPN을 변경한 후에 Azure Active Directory 동기화 도구에서 변경 내용이 동기화되지 않습니다.](https://support.microsoft.com/kb/2669550)

[Azure AD Connect 동기화 서비스 기능](active-directory-aadconnectsyncservice-features.md)에서 설명한 대로 동기화 엔진에서 UPN을 업그레이드할 수 있도록 Azure AD를 구성할 수도 있습니다.

**Q: 온-프레미스 AD 그룹 또는 연락처 개체를 기존 Azure AD 그룹 또는 연락처 개체와 소프트 일치하도록 지원되나요?**  
예. 이 소프트 일치는 proxyAddress에 따라 다릅니다. 소프트 매칭은 메일 사용이 가능하지 않은 그룹에서 지원되지 않습니다.

**Q: 온-프레미스 Azure AD 그룹 또는 연락처 개체와 하드 일치하도록 기존 Azure AD 그룹 또는 연락처 개체의 ImmutableId 특성을 수동으로 설정하는 것이 지원되나요?**  
아니요, 기존 Azure AD 그룹 또는 연락처 개체의 ImmutableId 특성을 하드 일치하도록 수동으로 설정하는 기능은 현재 지원되지 않습니다.

## <a name="custom-configuration"></a>사용자 지정 구성
**Q: Azure AD Connect에 대한 PowerShell cmdlet 설명서는 어디에 있나요?**  
이 사이트에서 설명하는 cmdlet을 제외하고 Azure AD Connect에 있는 다른 PowerShell cmdlet은 고객이 사용할 수 없습니다.

**Q: Synchronization Service Manager에 있는 "서버 내보내기/서버 가져오기" 옵션을 사용하여 서버 간에 구성을 이동할 수 있나요?**  
아니요. 이 옵션은 모든 구성 설정을 검색하는 것이 아니므로 사용하면 안됩니다. 그 대신 마법사를 사용하여 두 번째 서버에서 기본 구성을 만들고, 동기화 규칙 편집기를 사용하여 서버 간에 사용자 지정 규칙을 이동하도록 PowerShell 스크립트를 생성합니다. 자세한 내용은 [스윙 마이그레이션](active-directory-aadconnect-upgrade-previous-version.md#swing-migration)을 참조하세요.

**Q: Azure 로그인 페이지에 대해 암호를 캐시할 수 있지만 *autocomplete = "false"* 특성이 있는 암호 입력 요소를 포함하고 있어 이 캐싱을 방지할 수 있나요?**  
현재 autocomplete 태그를 포함하여 **암호** 필드의 HTML 특성을 수정하는 기능은 지원되지 않습니다. 현재 **암호** 필드에 특성을 추가할 수 있는 사용자 지정 JavaScript를 허용하는 기능을 개발하고 있습니다.

**Q: Azure 로그인 페이지에는 이전에 성공적으로 로그인한 사용자의 사용자 이름이 표시됩니다. 이 동작을 끌 수 있나요?**  
현재 autocomplete 태그를 포함하여 **암호** 입력 필드의 HTML 특성을 수정하는 기능은 지원되지 않습니다. 현재 **암호** 필드에 특성을 추가할 수 있는 사용자 지정 JavaScript를 허용하는 기능을 개발하고 있습니다.

**Q: 동시 세션을 방지하는 방법이 있나요?**  
아니요.

## <a name="auto-upgrade"></a>자동 업그레이드

**Q: 자동 업그레이드를 사용하는 이점 및 결과는 무엇인가요?**  
모든 고객은 해당 Azure AD Connect 설치에 대한 자동 업그레이드를 사용하는 것이 좋습니다. Azure AD Connect에서 확인된 취약성에 대한 보안 업데이트를 포함하여 항상 최신 패치를 받는 것이 이점입니다. 업그레이드 프로세스는 간편하고 새 버전이 지원되는 즉시 자동으로 수행됩니다. 수천 명의 Azure AD Connect 고객이 모든 새 릴리스에서 자동 업그레이드를 사용합니다.

자동 업그레이드 프로세스는 항상 설치가 자동 업그레이드에 적합한지 여부를 먼저 설정합니다. 적합한 경우 업그레이드가 수행되고 테스트됩니다. 또한 이 프로세스에는 규칙 및 특정 환경 요인에 대한 사용자 지정 변경 내용을 찾는 작업도 포함됩니다. 테스트에서 업그레이드가 성공하지 못한 것으로 표시되면 이전 버전이 자동으로 복원됩니다.

환경의 크기에 따라 프로세스를 수행하는 데 몇 시간이 걸릴 수 있습니다. 업그레이드가 진행되는 동안 Windows Server Active Directory와 Azure AD 간의 동기화는 수행되지 않습니다.

**Q: 내 자동 업그레이드가 더 이상 작동하지 않으며, 새 버전을 설치해야 한다는 이메일을 받았습니다. 이렇게 하는 것이 왜 필요한가요?**  
작년에 특정 상황에서 서버의 자동 업그레이드 기능을 사용하지 않도록 설정했을 수 있는 Azure AD Connect 버전을 릴리스했습니다. 이 문제는 Azure AD Connect 버전 1.1.750.0에서 해결되었습니다. 문제의 영향을 받은 경우 PowerShell 스크립트를 실행하여 복구하거나 수동으로 Azure AD Connect의 최신 버전으로 업그레이드하면 문제를 완화할 수 있습니다. 

PowerShell 스크립트를 실행하려면 [스크립트를 다운로드](https://aka.ms/repairaadconnect)하고 관리 PowerShell 창의 Azure AD Connect 서버에서 이 스크립트를 실행합니다. 스크립트 실행하는 방법을 알아보려면 [이 짧은 비디오를 보세요](https://aka.ms/repairaadcau).

수동으로 업그레이드하려면 AADConnect.msi 파일의 최신 버전을 다운로드하고 실행해야 합니다.
 
-  현재 버전이 1.1.750.0 이전인 경우 [최신 버전을 다운로드하여 업그레이드](https://www.microsoft.com/download/details.aspx?id=47594)합니다.
- Azure AD Connect 버전이 1.1.750.0 이상이면 추가 작업이 필요하지 않습니다. 이미 자동 업그레이드 수정이 포함된 버전을 사용하고 있습니다. 

**Q: 자동 업그레이드를 다시 활성화하기 위해 최신 버전으로 업그레이드하라는 이메일을 받았습니다. 1.1.654.0 버전을 사용하고 있습니다. 업그레이드해야 하나요?**  
예, 1.1.750.0 버전 이상으로 업그레이드하거나 최신 자동 업그레이드를 사용하도록 다시 설정해야 합니다. [최신 버전을 다운로드하여 업그레이드하세요](https://www.microsoft.com/download/details.aspx?id=47594).

**Q: 자동 업그레이드를 다시 활성화하기 위해 최신 버전으로 업그레이드하라는 이메일을 받았습니다. PowerShell을 사용하여 자동 업그레이드를 사용하도록 설정한 경우에도 최신 버전을 설치해야 하나요?**  
예, 1.1.750.0 버전 이상으로 업그레이드해야 합니다. PowerShell을 사용하여 자동 업그레이드 서비스를 사용하도록 설정하더라도 1.1.750.0 이전 버전에서 발견된 자동 업그레이드 문제가 완화되지 않습니다.

**Q: 최신 버전으로 업그레이드하려고 하지만 Azure AD Connect를 설치한 사용자가 확실하지 않고 사용자 이름과 암호가 없습니다. 필요한가요?**
초기에 Azure AD Connect를 업그레이드하는 데 사용된 사용자 이름과 암호를 알 필요가 없습니다. 전역 관리자 역할이 있는 Azure AD 계정을 사용하세요.

**Q: 사용 중인 Azure AD Connect 버전은 어떻게 확인할 수 있나요?**  
서버에 설치된 Azure AD Connect의 버전을 확인하려면 제어판으로 이동하고, 다음과 같이 **프로그램** > **프로그램 및 기능**에서 설치된 Microsoft Azure AD Connect 버전을 찾으세요.

![제어판에 표시된 Azure AD Connect 버전](media/active-directory-aadconnect-faq/faq1.png)

**Q: Azure AD Connect의 최신 버전으로 업그레이드하려면 어떻게 할까요?**  
최신 버전으로 업그레이드하는 방법을 알아보려면 [Azure AD Connect: 이전 버전에서 최신 버전으로 업그레이드](active-directory-aadconnect-upgrade-previous-version.md)를 참조하세요. 

**Q: 작년에 Azure AD Connect의 최신 버전으로 이미 업그레이드했습니다. 다시 업그레이드해야 하나요?**  
Azure AD Connect 팀은 서비스를 자주 업데이트합니다. 새 기능뿐만 아니라 버그 수정 및 보안 업데이트도 활용하려면 서버를 최신 버전으로 유지해야 합니다. 자동 업그레이드를 사용하도록 설정하면 소프트웨어 버전이 자동으로 업데이트됩니다. Azure AD Connect의 버전 릴리스 기록을 확인하려면 [Azure AD Connect: 버전 릴리스 내역](active-directory-aadconnect-version-history.md)을 참조하세요.

**Q: 업그레이드하는 데 걸리는 시간과 내 사용자에게 미치는 영향은 어떻게 되나요?**  
업그레이드에 필요한 시간은 테넌트 크기에 따라 다릅니다. 대규모 조직의 경우 저녁 또는 주말에 업그레이드를 수행하는 것이 가장 좋습니다. 업그레이드하는 동안 동기화 작업은 수행되지 않습니다.

**Q: Azure AD Connect로 업그레이드했지만 Office 포털에서 여전히 DirSync를 언급합니다. 그 이유는 무엇입니까?**  
Office 팀은 Office 포털 업데이트에서 현재 제품 이름을 반영하도록 노력하고 있습니다. 사용 중인 동기화 도구는 반영되지 않습니다.

**Q: 내 자동 업그레이드 상태가 "일시 중단됨"으로 표시됩니다. 일시 중지된 이유가 무엇인가요? 활성화해야 하나요?**  
특정 상황에서 자동 업그레이드 상태를 "일시 중단됨"으로 설정되는 버그가 이전 버전에서 소개되었습니다. 수동으로 사용하도록 설정하는 것은 기술적으로 가능하지만 몇 가지 복잡한 단계가 필요합니다. 최신 버전의 Azure AD Connect를 설치하는 것이 가장 좋습니다.

**Q: 회사에는 엄격한 변경 관리 요구 사항이 있으며 변경 내용이 푸시되는 시기를 제어하려고 합니다. 자동 업그레이드가 시작되는 시기를 제어할 수 있나요?**  
아니요, 현재 이러한 기능은 없습니다. 이 기능은 이후 릴리스에서 평가되고 있습니다.

**Q: 자동 업그레이드에 실패한 경우 이메일을 받게 되나요? 성공했는지 어떻게 알 수 있나요?**  
업그레이드 결과는 알려주지 않습니다. 이 기능은 이후 릴리스에서 평가되고 있습니다.

**Q: 자동 업그레이드를 푸시하려는 경우에 대한 타임라인을 게시하나요?**  
자동 업그레이드는 최신 버전의 릴리스 프로세스에서 첫 번째 단계입니다. 새 릴리스가 있을 때마다 업그레이드가 자동으로 푸시됩니다. Azure AD Connect의 최신 버전은 [Azure AD 로드맵](../fundamentals/whats-new.md)에서 사전에 발표됩니다.

**Q: 자동 업그레이드는 Azure AD Connect Health도 업그레이드하나요?**  
예, 자동 업그레이드는 Azure AD Connect Health도 업그레이드합니다.

**Q: 준비 모드에서 Azure AD Connect 서버도 자동으로 업그레이드하나요?**  
예, 준비 모드인 Azure AD Connect 서버를 자동 업그레이드할 수 있습니다.

**Q: 자동 업그레이드가 실패하고 Azure AD Connect 서버가 시작되지 않으면 어떻게 해야 하나요?**  
드문 경우이지만 업그레이드를 수행한 후에 Azure AD Connect 서비스가 시작되지 않습니다. 이러한 경우 일반적으로 서버를 다시 부팅하면 문제가 해결됩니다. 그래도 Azure AD Connect 서비스가 시작되지 않으면 지원 티켓을 여세요. 자세한 내용은 [Office 365 고객 지원팀에 문의할 서비스 요청 만들기](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/)를 참조하세요. 

**Q: 최신 버전의 Azure AD Connect로 업그레이드하는 경우 어떤 위험이 있는지 확신할 수 없습니다. 업그레이드에서 도움을 받기 위해 통화할 수 있나요?**  
최신 버전의 Azure AD Connect로 업그레이드하는 데 도움이 필요한 경우 [Office 365 고객 지원팀에 문의할 서비스 요청 만들기](https://blogs.technet.microsoft.com/praveenkumar/2013/07/17/how-to-create-service-requests-to-contact-office-365-support/)에서 지원 티켓을 여세요.

## <a name="troubleshooting"></a>문제 해결
**Q: Azure AD Connect에 대한 도움을 받으려면 어떻게 합니까?**

[Microsoft KB(기술 자료) 검색](https://www.microsoft.com/Search/result.aspx?q=azure%20active%20directory%20connect&form=mssupport)

* KB(기술 자료)에서 Azure AD Connect 지원에 대한 일반적인 고장 수리 문제에 대한 기술 솔루션을 검색하세요.

[Azure Active Directory 포럼](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)

* [Azure AD 커뮤니티](https://social.msdn.microsoft.com/Forums/azure/en-US/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)로 이동하여 기술 관련 질문 및 대답을 검색하거나 직접 질문하세요.

[Azure AD에 대한 지원 받기](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)
