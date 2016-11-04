---
title: Azure Active Directory ID 보호 플레이 북 | Microsoft Docs
description: Azure AD ID 보호를 사용하여 손상된 ID 및 장치를 악용하는 공격자의 능력을 제한하고 이전에 손상이 우려되거나 손상된 ID 또는 장치를 보호할 수 있는 방법을 알아봅니다.
services: active-directory
keywords: Azure Active Directory ID 보호, 클라우드 앱 검색, 응용 프로그램 관리, 보안, 위험, 위험 수준, 취약점, 보안 정책
documentationcenter: ''
author: MarkusVi
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2016
ms.author: markvi

---
# <a name="azure-active-directory-identity-protection-playbook"></a>Azure Active Directory ID 보호 플레이 북
이 플레이 북은 다음 작업을 수행하는 데 도움이 됩니다.

* 위험 이벤트 및 취약성을 시뮬레이트하여 ID 보호 환경에 데이터 채우기
* 위험 기반 조건부 액세스 정책을 설정하고 이러한 정책의 영향 테스트

## <a name="simulating-risk-events"></a>위험 이벤트 시뮬레이션
이 섹션에서는 다음 위험 이벤트 유형을 시뮬레이트하는 단계를 제공합니다.

* 익명 IP 주소에서 로그인(초급)
* 잘 모르는 위치에서 로그인(중급)
* 비정상적 위치로 불가능한 이동(고급)

안전한 방법으로 다른 위험 이벤트를 시뮬레이션할 수 없습니다.

### <a name="signins-from-anonymous-ip-addresses"></a>익명 IP 주소에서 로그인
이 위험 이벤트 유형은 익명 프록시 IP 주소로 식별된 IP 주소에서 시도하여 성공적으로 로그인한 사용자를 식별합니다. 이 프록시는 해당 장치의 IP 주소를 숨기려는 사용자가 사용하며 악의적인 의도로 사용될 수 있습니다.

**익명 IP에서 로그인을 시뮬레이트하려면 다음 단계를 수행합니다.**

1. [Tor 브라우저](https://www.torproject.org/projects/torbrowser.html.en)를 다운로드합니다.
2. Tor 브라우저를 사용하여 [https://myapps.microsoft.com](https://myapps.microsoft.com)으로 이동합니다.   
3. **익명 IP 주소에서 로그인** 보고서에 표시하려는 계정의 자격 증명을 입력합니다.

로그인이 5분 이내에 ID 보호 대시보드에 나타납니다. 

### <a name="signins-from-unfamiliar-locations"></a>알 수 없는 위치에서 로그인
알 수 없는 위치 위험은 새로운 위치/알 수 없는 위치를 확인하기 위해 과거 로그인 위치(IP, 위도/경도 및 ASN)를 고려하는 실시간 로그인 평가 메커니즘입니다. 시스템은 이전 IP, 위도/경도 및 사용자의 ASN을 저장하고 익숙한 위치인지 고려합니다. 로그인 위치가 기존의 익숙한 위치 중 하나와 일치하지 않는 경우 로그인 위치는 알 수 없는 위치로 간주됩니다.

Azure Active Directory ID 보호:  

* 새로운 위치를 알 수 없는 위치의 플래그로 지정하지 않는 14일의 초기 학습 기간이 있습니다.
* 익숙한 장치 및 기존의 익숙한 위치에 지리적으로 가까운 위치에서 시도한 로그인을 무시합니다.

알 수 없는 위치를 시뮬레이트하려면 계정이 이전에 로그인하지 않은 위치 및 장치에서 로그인해야 합니다. 

**익숙하지 않은 위치에서 로그인을 시뮬레이트하려면 다음 단계를 수행합니다.**

1. 적어도 14일의 로그인 기록이 있는 계정을 선택합니다. 
2. 다음 중 하나를 수행합니다.
   
   a. VPN을 사용하는 동안 [https://myapps.microsoft.com](https://myapps.microsoft.com) 로 이동하고 위험 이벤트를 시뮬레이트하려는 계정의 자격 증명을 입력합니다.
   
   b. 계정의 자격 증명을 사용하여 로그인하는 다른 위치에 연결을 요청합니다(권장하지 않음).

로그인이 5분 이내에 ID 보호 대시보드에 나타납니다.

### <a name="impossible-travel-to-atypical-location"></a>비정상적 위치로 불가능한 이동
알고리즘이 기계 학습을 사용하여 익숙한 장치에서 불가능한 이동 또는 디렉터리의 다른 사용자가 사용하는 VPN에서 시도한 로그인 등 가양성을 걸러내기 때문에 불가능한 이동 조건을 시뮬레이트하는 작업은 어렵습니다. 또한 알고리즘은 위험 이벤트의 생성을 시작하기 전에 사용자에 대한 3-14일 간의 로그인 기록이 필요합니다.

**비정상적 위치로 불가능한 이동을 시뮬레이트하려면 다음 단계를 수행합니다.**

1. 표준 브라우저를 사용하여 [https://myapps.microsoft.com](https://myapps.microsoft.com)으로 이동합니다.  
2. 불가능한 이동 위험 이벤트를 생성하려는 계정의 자격 증명을 입력합니다.
3. 사용자 에이전트를 변경합니다. 사용자 에이전트 전환기 추가 기능을 사용하여 개발자 도구에서 Internet Explorer의 사용자 에이전트를 변경하거나 Firefox 또는 Chrome에서 사용자 에이전트를 변경할 수 있습니다.
4. 사용자의 IP 주소를 변경합니다. VPN, Tor 추가 기능을 사용하거나 다른 데이터 센터의 Azure에서 새 컴퓨터를 스핀업하여 사용자의 IP 주소를 변경할 수 있습니다.
5. 이전과 동일한 자격 증명을 사용하여 이전에 로그인한 후 몇 분 내에 [https://myapps.microsoft.com](https://myapps.microsoft.com) 에 로그인합니다.

로그인이 2-4시간 이내에 ID 보호 대시보드에 나타납니다.<br>
포함된 복잡한 기계 학습 모델 때문에 선택되지 않을 수 있습니다.<br>  여러 Azure AD 계정에 이러한 단계를 복제할 수도 있습니다.

## <a name="simulating-vulnerabilities"></a>취약점 시뮬레이션
취약점은 잘못된 행위자에 의해 악용될 수 있는 Azure AD 환경의 단점입니다. 현재 3가지 유형의 취약점은 Azure AD의 다른 기능을 활용하는 Azure AD ID 보호에 표시됩니다. 이러한 기능이 설정되면 이러한 취약성이 ID 보호 대시보드에 자동으로 표시됩니다.

* Azure AD [Multi-Factor Authentication이란?](../multi-factor-authentication/multi-factor-authentication.md)
* Azure AD [Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md).
* Azure AD [Privileged Identity Management](active-directory-privileged-identity-management-configure.md). 

## <a name="user-compromise-risk"></a>사용자 손상 위험
**사용자 손상 위험을 테스트하려면 다음 단계를 수행합니다**.

1. 테넌트에 대한 전역 관리자 자격 증명을 사용하여 [https://portal.azure.com](https://portal.azure.com) 에 로그인합니다.
2. **ID 보호**로 이동합니다. 
3. 기본 **Azure AD ID 보호** 블레이드에서 **설정**을 클릭합니다. 
4. **포털 설정** 블레이드의 **보안 규칙**에서 **사용자 손상 위험**을 클릭합니다. 
5. **로그인 위험** 블레이드에서 **규칙 사용**을 끈 다음 **저장** 설정을 클릭합니다.
6. 지정된 사용자 계정의 경우 알 수 없는 위치 또는 익명 IP 위험 이벤트를 시뮬레이션합니다. 해당 사용자에 대한 사용자 위험 수준을 **보통**으로 상승합니다.
7. 몇 분 정도 기다린 후에 사용자에 대한 해당 사용자 수준이 **보통**인지 확인합니다.
8. **포털 설정** 블레이드로 이동합니다.
9. **사용자 손상 위험** 블레이드의 **규칙 사용**에서 **켜기**를 선택합니다. 
10. 다음 옵션 중 하나를 선택합니다.
    
    a. 차단하려면 **로그인 차단**에서 **보통**을 선택합니다.
    
    b. 보안 암호 변경을 적용하려면 **Multi-Factor Authentication 요구**에서 **보통**을 선택합니다.
11. **저장**을 클릭합니다.
12. 이제 위험 수준이 상승한 사용자로 로그인하여 위험 기반 조건부 액세스를 테스트할 수 있습니다. 사용자 위험이 보통인 경우 정책 구성에 따라 로그인이 차단되거나 암호를 변경하도록 강제됩니다. 
    <br><br>
    ![플레이 북](./media/active-directory-identityprotection-playbook/201.png "Playbook")
    <br>

## <a name="signin-risk"></a>로그인 위험
**로그인 위험을 테스트하려면 다음 단계를 수행합니다.**

1. 테넌트에 대한 전역 관리자 자격 증명을 사용하여 [https://portal.azure.com ](https://portal.azure.com) 에 로그인합니다.
2. **ID 보호**로 이동합니다.
3. 기본 **Azure AD ID 보호** 블레이드에서 **설정**을 클릭합니다. 
4. **포털 설정** 블레이드의 **보안 규칙**에서 **로그인 위험**을 클릭합니다.
5. **로그인 위험** 블레이드의 **규칙 사용**에서 **켜기**를 선택합니다. 
6. 다음 옵션 중 하나를 선택합니다.
   
   a. 차단하려면 **로그인 차단**에서 **보통**을 선택합니다.
   
   b. 보안 암호 변경을 적용하려면 **Multi-Factor Authentication 요구**에서 **보통**을 선택합니다.
7. 차단하려면 로그인 차단에서 보통을 선택합니다.
8. Multi-Factor Authentication을 적용하려면 **Multi-Factor Authentication 요구**에서 **보통**을 선택합니다.
9. **Save**를 클릭합니다.
10. 이제 둘 다 **보통** 위험 이벤트이기 때문에 알 수 없는 위치 또는 익명 IP 위험 이벤트를 시뮬레이트하여 위험 기반 조건부 액세스를 테스트할 수 있습니다.

<br>
![플레이 북](./media/active-directory-identityprotection-playbook/200.png "Playbook")
<br>

## <a name="see-also"></a>참고 항목
* [Azure Active Directory ID 보호](active-directory-identityprotection.md)

<!--HONumber=Oct16_HO2-->


