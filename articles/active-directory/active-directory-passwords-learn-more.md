<properties 
	pageTitle="자세한 정보: Azure AD 암호 관리 | Microsoft Azure" 
	description="암호 쓰기 저장의 작동 원리, 암호 쓰기 저장 보안, 암호 재설정 포털의 작동 원리, 암호 재설정에서 사용되는 데이터를 포함하여 Azure AD 암호 관리에 대한 고급 항목을 제공합니다." 
	services="active-directory" 
	documentationCenter="" 
	authors="asteen" 
	manager="kbrint" 
	editor="billmath"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/08/2015" 
	ms.author="asteen"/>

# 암호 관리에 대한 자세한 정보
암호 관리를 이미 배포했거나 배포하기 전에 작동 원리에 대한 기술적 핵심 사항을 자세히 알아보려는 경우, 이 섹션에서 해당 서비스의 기술적 개념에 대한 훌륭한 개요를 제공합니다. 여기서 다루는 내용은 다음과 같습니다.

* [**암호 쓰기 저장 개요**](#password-writeback-overview)
  - [암호 쓰기 저장의 작동 원리](#how-password-writeback-works)
  - [암호 쓰기 저장에 지원되는 시나리오](#scenarios-supported-for-password-writeback)
  - [암호 쓰기 저장 보안 모델](#password-writeback-security-model)
* [**암호 재설정 포털의 작동 원리**](#how-does-the-password-reset-portal-work)
  - [암호 재설정에서 사용되는 데이터](#what-data-is-used-by-password-reset)

## 암호 쓰기 저장 개요
암호 쓰기 저장은 Azure Active Directory Premium의 현재 구독자가 설정하여 사용할 수 있는 [Azure Active Directory Connect](active-directory-aadconnect) 구성 요소입니다. 자세한 내용은 [Azure Active Directory 버전](active-directory-editions.md)을 참조하세요.

암호 쓰기 저장을 사용하면 암호를 온-프레미스 Active Directory에 쓸 수 있도록 클라우드 테넌트를 구성할 수 있습니다. 복잡한 온-프레미스 셀프 서비스 암호 재설정 솔루션을 설정 및 관리할 필요가 없으며 사용자가 어디에 있든 상관 없이 온-프레미스 암호를 재설정할 수 있는 편리한 클라우드 기반 방법을 제공합니다. 암호 쓰기 저장의 주요 기능 중 몇 가지가 나와 있으므로 잘 읽어보시기 바랍니다.

- **지연 없는 피드백.** 암호 쓰기 저장은 동기식 작업입니다. 사용자의 암호가 정책에 맞지 않거나 어떤 이유로든 재설정 또는 변경할 수 없는 경우 즉시 사용자에게 알려줍니다.
- **AD FS 또는 기타 페더레이션 기술을 사용하여 사용자의 암호 재설정 지원.** 페더레이션된 사용자 계정이 Azure AD 테넌트로 동기화되는 동안 암호 쓰기 저장을 사용하여 온-프레미스 AD 암호를 클라우드에서 관리할 수 있습니다.
- **암호 해시 동기화를 사용하여 사용자의 암호 재설정 지원.** 암호 재설정 서비스에서 동기화된 사용자 계정을 암호 해시 동기화에 사용할 수 있음을 감지하면 이 계정의 온-프레미스 및 클라우드 암호를 동시에 재설정합니다.
- **액세스 패널 및 Office 365에서 암호 변경 지원.** 페더레이션 또는 암호가 동기화된 사용자가 만료되었거나 만료되지 않은 암호를 변경하면 해당 암호를 로컬 AD 환경에 다시 씁니다.
- **관리자가 [Azure 관리 포털](https://manage.windowsazure.com)에서** **암호를 재설정할 때 암호 쓰기 저장 지원.** 사용자가 페더레이션 또는 암호가 동기화된 경우 관리자가 [Azure 관리 포털](https://manage.windowsazure.com)에서 해당 사용자의 암호를 재설정할 때마다 로컬 AD에서 관리자가 선택하는 암호도 설정합니다. 이 기능은 현재 Office 관리자 포털에서 지원되지 않습니다.
- **온-프레미스 AD 암호 정책 적용.** 사용자가 자신의 암호를 재설정하는 경우 해당 디렉터리에 커밋하기 전에 온-프레미스 AD 정책에 맞는지 확인합니다. 여기에는 기록, 복잡성, 나이, 암호 필터 및 로컬 AD에서 사용자가 정의한 기타 암호 제한 사항이 포함됩니다.
- **인바운드 방화벽 규칙 불필요.** 암호 쓰기 저장 기능은 기본 통신 채널로 Azure 서비스 버스 릴레이를 사용합니다. 따라서 이 기능이 작동하기 위해 방화벽에서 인바운드 포트를 열 필요가 없습니다.
- **온-프레미스 Active Directory의 보호된 그룹 내에 존재하는 사용자 계정에 대해서는 지원되지 않음.** 보호된 그룹에 대한 자세한 내용은 [Active Directory의 보호된 계정 및 그룹](https://technet.microsoft.com/library/dn535499.aspx)을 참조하세요.

### 암호 쓰기 저장의 작동 원리
암호 쓰기 저장에는 세 가지 주요 구성 요소가 있습니다.

- 암호 재설정 클라우드 서비스(Azure AD의 암호 변경 페이지에도 통합되어 있음)
- 테넌트별 Azure 서비스 버스 릴레이
- 온-프레미스 암호 재설정 끝점

아래 다이어그램에 설명된 것처럼 연동됩니다.

  ![][001]

페더레이션 또는 암호 해시 동기화된 사용자가 클라우드에서 자신의 암호를 재설정하거나 변경하려고 하면 다음이 수행됩니다.

1.	사용자가 어떤 종류의 암호를 가지고 있는지 확인합니다. 암호가 온-프레미스로 관리되는 경우는 쓰기 저장 서비스가 가동되어 실행 중인지 확인합니다. 실행 중인 경우는 사용자가 작업을 계속 진행하도록 하고, 실행 중이 아닌 경우는 여기서 암호를 재설정할 수 없다고 사용자에게 알려줍니다.
2.	다음으로, 사용자는 적절한 인증 게이트를 통과하여 암호 재설정 화면에 도달합니다.
3.	사용자는 새 암호를 선택하고 다시 확인합니다.
4.	제출을 클릭하면 쓰기 저장 설정 과정에서 만든 대칭 키로 일반 텍스트 암호를 암호화합니다.
5.	암호를 암호화한 후 HTTPS 채널을 통해 테넌트별 서비스 버스 릴레이(쓰기 저장 설정 과정 중에 설정될 수도 있음)로 전송되는 페이로드에 해당 암호를 포함시킵니다. 이 릴레이는 온-프레미스 설치만 알고 있는 임의로 생성된 암호에 의해 보호됩니다.
6.	메시지가 서비스 버스에 도달하면 암호 재설정 끝점이 자동으로 절전 모드에서 해제되어 보류 중인 재설정 요청이 있는지 확인합니다.
7.	그런 다음, 서비스에서 클라우드 앵커 특성을 사용하여 해당 사용자를 찾습니다. 이 조회가 성공하기 위해서는 사용자 개체가 AD 커넥터 공간에 있고 해당 MV 개체에 연결되고 해당 AAD 커넥터 개체에 연결되어야 합니다. 마지막으로, 동기화에서 이 사용자 계정을 찾기 위해서는 AD 커넥터 개체에서 MV로의 링크에 `Microsoft.InfromADUserAccountEnabled.xxx` 동기화 규칙이 있어야 합니다. 클라우드에서 호출이 들어오면 동기화 엔진이 cloudAnchor 특성을 사용하여 AAD 커넥터 공간 개체를 조회한 후 MV 개체로 링크를 다시 따라간 다음 AD 개체로 다시 링크를 따라가기 때문에 이 규칙이 필요합니다. 동일한 사용자에 대해 여러 AD 개체(다중 포리스트)가 있을 수 있기 때문에 동기화 엔진은 `Microsoft.InfromADUserAccountEnabled.xxx` 링크에 의존하여 정확한 개체를 선택합니다.
8.	사용자 계정을 찾은 후에는 적절한 AD 포리스트에서 직접 암호를 재설정합니다.
9.	암호 설정 작업에 성공한 경우 사용자에게 암호가 수정되었으므로 작업을 계속 진행해도 된다고 알려줍니다.
10.	암호 설정 작업에 실패한 경우는 사용자에게 오류를 반환하고 다시 시도하라고 알려줍니다. 서비스가 다운되었거나, 선택한 암호가 조직의 정책과 맞지 않거나, 로컬 AD에서 해당 사용자를 찾을 수 없거나, 또는 기타 여러 가지 이유로 인해 작업에 실패할 수 있습니다. 이러한 경우를 위한 특정 메시지가 있어서 문제를 해결하기 위해 어떤 작업을 수행할 수 있는지 사용자에게 알려줍니다.

### 암호 쓰기 저장에 지원되는 시나리오
아래 테이블에서는 동기화 기능의 버전에 따라 지원되는 시나리오를 설명합니다. 일반적으로 암호 쓰기 저장 기능을 사용하려면 최신 버전의 [Azure AD Connect](active-directory-aadconnect.md#download-azure-ad-connect)를 설치하는 것이 좋습니다.

  ![][002]

### 암호 쓰기 저장 보안 모델
암호 쓰기 저장은 매우 안전하고 강력한 서비스입니다. 사용자 정보를 확실하게 보호하기 위해 아래에서 설명하는 4계층 보안 모델이 사용됩니다.

- **테넌트별 서비스 버스 릴레이** – 사용자가 이 서비스를 설정하면 임의로 생성된 강력한 암호에 의해 보호되는 테넌트별 서비스 버스 릴레이가 설정됩니다. 이때 해당 암호는 Microsoft에도 전혀 액세스 권한이 없습니다.
- **잠겨 있는 강력한 암호 암호화 키** – 서비스 버스 릴레이를 만든 후 회선을 통해 도착하는 암호를 암호화하는 데 사용할 강력한 대칭 키를 만듭니다. 이 키는 클라우드의 테넌트 암호 저장소에만 존재하며 디렉터리의 암호와 마찬가지로 강력하게 잠겨 있고 감사됩니다.
- **업계 표준 TLS** – 클라우드에서 암호 재설정 또는 변경 작업을 수행하면 일반 텍스트 암호를 가져와서 공용 키로 암호화합니다. 그런 다음, Microsoft의 SSL 인증서를 사용하여 암호화된 채널을 통해 서비스 버스 릴레이로 전송되는 HTTPS 메시지에 해당 암호를 삽입합니다. 서비스 버스에 해당 메시지가 도착한 후에는 온-프레미스 에이전트가 절전 모드에서 해제되고, 이전에 생성된 강력한 암호를 사용하여 서비스 버스에 대한 인증을 수행하고, 암호화된 메시지를 받아서 미리 생성된 개인 키를 사용하여 암호를 해독한 다음, AD DS SetPassword API를 통해 해당 암호를 설정합니다. 이 단계에서는 클라우드에서 AD 온-프레미스 암호 정책(복잡성, 나이, 기록, 필터 등)을 적용할 수 있습니다.
- **메시지 만료 정책** – 마지막으로, 어떤 이유로 온-프레미스 서비스가 다운되었기 때문에 메시지가 서비스 버스에 그대로 있는 경우는 보안을 더욱 강화하기 위해 몇 분 후 시간 초과되어 제거됩니다.

## 암호 재설정 포털의 작동 원리
사용자가 암호 재설정 포털로 이동하면 사용자 계정이 유효한지 여부, 사용자가 속한 조직, 사용자의 암호를 관리하는 위치, 해당 기능 사용에 대해 사용자가 사용 허가를 받았는지 여부 등을 확인하는 워크플로가 시작됩니다. 암호 재설정 페이지의 논리에 대해 자세히 알아보려면 아래 단계를 읽어보세요.

1.	계정에 액세스할 수 없습니까? 링크를 클릭하거나 [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com)으로 직접 이동합니다.
2.	사용자 ID를 입력하고 captcha를 전달합니다.
3.	Azure AD가 다음을 수행하여 사용자가 이 기능을 사용할 수 있는지 확인합니다.
    - 사용자가 이 기능을 사용할 수 있고 Azure AD 라이선스가 할당되어 있는지 확인합니다.
        - 사용자가 이 기능을 사용할 수 없거나 라이선스가 할당되어 있지 않은 경우에는 암호를 재설정하려면 관리자에게 문의하라는 메시지가 해당 사용자에게 표시됩니다.
    - 관리자 정책에 따라 사용자의 계정에 올바른 챌린지 데이터가 정의되어 있는지 확인합니다.
        - 정책에 하나의 챌린지만 필요하면 관리자 정책에서 사용하도록 설정한 최소 하나 이상의 챌린지에 대해 정의된 적절한 데이터가 해당 사용자에게 있는지 확인합니다.
          - 사용자가 구성되어 있지 않은 경우에는 암호를 재설정하려면 관리자에게 문의해야 한다고 사용자에게 알려줍니다.
        - 정책에 두 개의 챌린지가 필요하면 관리자 정책에서 사용하도록 설정한 최소 두 개 이상의 챌린지에 대해 정의된 적절한 데이터가 해당 사용자에게 있는지 확인합니다.
          - 사용자가 구성되어 있지 않은 경우에는 암호를 재설정하려면 관리자에게 문의해야 한다고 사용자에게 알려줍니다.
    - 사용자의 암호가 온-프레미스(페더레이션 또는 암호 해시 동기화)로 관리되는지 여부를 확인합니다.
       - 쓰기 저장 기능이 배포되어 있고 사용자의 암호가 온-프레미스로 관리되면 사용자는 인증을 진행하고 암호를 재설정할 수 있습니다.
       - 쓰기 저장 기능이 배포되어 있지 않고 사용자의 암호가 온-프레미스로 관리되는 경우에는 암호를 재설정하려면 관리자에게 문의해야 한다고 사용자에게 알려줍니다.
4.	사용자가 성공적으로 암호를 재설정할 수 있다고 판단되면 사용자가 재설정 프로세스를 계속 진행하도록 안내해줍니다.

암호 쓰기 저장을 배포하는 방법에 대한 자세한 내용은 [시작: Azure AD 암호 관리](active-directory-passwords-getting-started.md)를 참조하세요.

### 암호 재설정에서 사용되는 데이터
다음 테이블에는 암호 재설정 중에 이 데이터를 사용하는 위치와 방법이 나와 있으며 조직에 적합한 인증 옵션을 결정하는 데 도움이 되도록 설계되어 있습니다. 또한 이 테이블에서는 이 데이터의 유효성을 검사하지 않는 입력 경로에서 사용자를 대신하여 데이터를 제공하는 경우를 위한 형식 요구 사항도 보여줍니다.

> [AZURE.NOTE]사무실 전화는 등록 포털에 나타나지 않는데, 사용자가 현재 디렉터리에서 이 속성을 편집할 수 없기 때문입니다.

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>연락 방법 이름</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Azure Active Directory 데이터 요소</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>사용되는 위치/설정 가능한 위치</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>형식 요구 사항</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>사무실 전화</p>
            </td>
            <td>
              <p>PhoneNumber</p>
              <p>예: Set-MsolUser -UserPrincipalName JWarner@contoso.com -PhoneNumber "+1 1234567890x1234"</p>
            </td>
            <td>
              <p>사용 위치:</p>
              <p>암호 재설정 포털</p>
              <p>설정 가능한 위치:</p>
              <p>PhoneNumber는 PowerShell, DirSync, Azure 관리 포털 및 Office 관리자 포털에서 설정할 수 있습니다.</p>
            </td>
            <td>
              <p>+ccc xxxyyyzzzz(예: +1 1234567890)</p>
              <ul>
                <li class="unordered">
										국가 코드를 제공해야 합니다.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
										지역 번호를 제공해야 합니다(있는 경우).<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
										국가 코드 앞에 +를 제공해야 합니다.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
										국가 코드와 나머지 번호 사이에는 공백이 하나 있어야 합니다.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
										내선 번호는 지원되지 않습니다. 내선 번호가 지정된 경우에는 전화 통화를 하기 전에 번호에서 제거합니다.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>휴대폰</p>
            </td>
            <td>
              <p>AuthenticationPhone</p>
              <p>또는</p>
              <p>MobilePhone</p>
              <p>(인증 전화는 제공된 데이터가 있는 경우에 사용되며, 그렇지 않은 경우는 휴대폰 필드로 대체됩니다.)</p>
              <p>예: Set-MsolUser -UserPrincipalName JWarner@contoso.com -MobilePhone "+1 1234567890x1234"</p>
            </td>
            <td>
              <p>사용 위치:</p>
              <p>암호 재설정 포털</p>
              <p>등록 포털</p>
              <p>설정 가능한 위치: </p>
              <p>AuthenticationPhone은 암호 재설정 등록 포털 또는 MFA 등록 포털에서 설정할 수 있습니다.</p>
              <p>MobilePhone은 PowerShell, DirSync, Azure 관리 포털 및 Office 관리자 포털에서 설정할 수 있습니다.</p>
            </td>
            <td>
              <p>+ccc xxxyyyzzzz(예: +1 1234567890)</p>
              <ul>
                <li class="unordered">
										국가 코드를 제공해야 합니다.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
										지역 번호를 제공해야 합니다(있는 경우).<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
										국가 코드 앞에 +를 제공해야 합니다.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
										국가 코드와 나머지 번호 사이에는 공백이 하나 있어야 합니다.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
										내선 번호는 지원되지 않습니다. 내선 번호가 지정된 경우에는 전화 통화를 할 때 무시됩니다.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>대체 전자 메일</p>
            </td>
            <td>
              <p>AuthenticationEmail</p>
              <p>또는</p>
              <p>AlternateEmailAddresses[0] </p>
              <p>(인증 전자 메일은 제공된 데이터가 있는 경우에 사용되며, 그렇지 않은 경우는 대체 전자 메일로 대체됩니다.)</p>
              <p>참고: 대체 전자 메일 필드는 디렉터리에 문자열의 배열로 지정됩니다. 이 배열의 첫 번째 항목을 사용합니다.</p>
              <p>예: Set-MsolUser -UserPrincipalName JWarner@contoso.com -AlternateEmailAddresses "email@live.com"</p>
            </td>
            <td>
              <p>사용 위치:</p>
              <p>암호 재설정 포털</p>
              <p>등록 포털</p>
              <p>설정 가능한 위치: </p>
              <p>AuthenticationEmail은 암호 재설정 등록 포털 또는 MFA 등록 포털에서 설정할 수 있습니다.</p>
              <p>AlternateEmail은 PowerShell, Azure 관리 포털 및 Office 관리자 포털에서 설정할 수 있습니다.</p>
            </td>
            <td>
              <p>
                <a href="mailto:user@domain.com">user@domain.com</a> 또는 甲斐@黒川.日本</p>
              <ul>
                <li class="unordered">
										전자 메일은 표준 형식을 따라야 합니다.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
										유니코드 전자 메일이 지원됩니다.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>보안 질문 및 답변</p>
            </td>
            <td>
              <p>디렉터리에서 직접 수정할 수 없습니다.</p>
            </td>
            <td>
              <p>사용 위치:</p>
              <p>암호 재설정 포털</p>
              <p>등록 포털 </p>
              <p>설정 가능한 위치: </p>
              <p>보안 질문을 설정하는 유일한 방법은 Azure 관리 포털을 통해 설정하는 방법입니다.</p>
              <p>지정된 사용자의 보안 질문에 대한 답변을 설정하는 유일한 방법은 등록 포털을 통해 설정하는 방법입니다.</p>
            </td>
            <td>
              <p>보안 질문은 최대 200자, 최소 3자입니다.</p>
              <p>답변은 최대 40자, 최소 3자입니다.</p>
            </td>
          </tr>
        </tbody></table>

<br/> <br/> <br/>

**추가 리소스**


* [암호 관리 정의](active-directory-passwords.md)
* [암호 관리의 작동 원리](active-directory-passwords-how-it-works.md)
* [암호 관리 시작](active-directory-passwords-getting-started.md)
* [암호 관리 사용자 지정](active-directory-passwords-customize.md)
* [암호 관리 모범 사례](active-directory-passwords-best-practices.md)
* [암호 관리 보고서와 함께 Operational Insights를 얻는 방법](active-directory-passwords-get-insights.md)
* [암호 관리 FAQ](active-directory-passwords-faq.md)
* [암호 관리 문제 해결](active-directory-passwords-troubleshoot.md)
* [MSDN의 암호 관리](https://msdn.microsoft.com/library/azure/dn510386.aspx)



[001]: ./media/active-directory-passwords-learn-more/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-learn-more/002.jpg "Image_002.jpg"
 

<!---HONumber=July15_HO4-->