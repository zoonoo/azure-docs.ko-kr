---
title: AD FS 인증서의 응급 순환 Microsoft Docs
description: 이 문서에서는 AD FS 인증서를 즉시 해지 및 업데이트 하는 방법을 설명 합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 9035c0a91bbbd7493437c692540fcbb3136a094e
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105613131"
---
# <a name="emergency-rotation-of-the-ad-fs-certificates"></a>AD FS 인증서의 응급 순환
AD FS 인증서를 즉시 회전 해야 하는 경우이 섹션의 아래에 설명 된 단계를 수행할 수 있습니다.

> [!IMPORTANT]
> AD FS 환경에서 아래 단계를 수행 하면 이전 인증서가 즉시 해지 됩니다.  이 작업은 즉시 수행 되기 때문에 일반적으로 페더레이션 파트너가 새 인증서를 사용 하는 데 일반적으로 허용 되는 시간입니다. 새 인증서를 사용 하기 위해 트러스트 업데이트로 인해 서비스가 중단 될 수 있습니다.  모든 페더레이션 파트너에 새 인증서가 있는 경우이 문제를 해결 해야 합니다.

> [!NOTE]
> Microsoft는 HSM (하드웨어 보안 모듈)을 사용 하 여 인증서를 보호 하 고 보호 하는 것이 좋습니다.
> 자세한 내용은 AD FS 보안 설정에 대 한 모범 사례에서 [하드웨어 보안 모듈](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#hardware-security-module-hsm) 을 참조 하세요.

## <a name="determine-your-token-signing-certificate-thumbprint"></a>토큰 서명 인증서 지문 확인
현재 사용 중인 AD FS 이전 토큰 서명 인증서를 해지 하려면 토큰 sigining 인증서의 지문을 확인 해야 합니다.  이렇게 하려면 다음 단계를 사용 합니다.

 1. Microsoft 온라인 서비스에 연결 `PS C:\>Connect-MsolService`
 2. 온-프레미스 및 클라우드 토큰 서명 인증서 지문 및 만료 날짜를 모두 문서화 합니다.
`PS C:\>Get-MsolFederationProperty -DomainName <domain>` 
 3.  손 도장 (thumbprint)을 복사 합니다.  나중에 기존 인증서를 제거 하는 데 사용 됩니다.

AD FS 관리를 사용 하 고, 서비스/인증서로 이동 하 고, 인증서를 마우스 오른쪽 단추로 클릭 하 고, 인증서 보기를 선택 하 고 세부 정보를 선택 하 여 지문을 가져올 수도 있습니다. 

## <a name="determine-whether-ad-fs-renews-the-certificates-automatically"></a>AD FS에서 인증서를 자동으로 갱신할 지 여부 결정
기본적으로 AD FS는 초기 구성 시 및 인증서의 만료 날짜가 도달하는 경우 토큰 서명 및 토큰 암호 해독 인증서를 자동으로 생성하도록 구성되어 있습니다.

다음 Windows PowerShell 명령을 실행할 수 있습니다 `PS C:\>Get-AdfsProperties | FL AutoCert*, Certificate*` ..

AutoCertificateRollover 속성은 AD FS가 토큰 서명 및 토큰 암호 해독 인증서를 자동으로 갱신 하도록 구성 되었는지 여부를 설명 합니다.  AutoCertificateRollover가 TRUE로 설정 된 경우 [AutoCertificateRollover이 TRUE로 설정 된 경우 새 자체 서명 된 인증서 생성]에서 아래에 설명 된 지침을 따르세요.  AutoCertificateRollover가 FALSE로 설정 된 경우 [AutoCertificateRollover가 FALSE로 설정 된 경우 새 인증서를 수동으로 생성]에서 아래에 설명 된 지침을 따르세요.


## <a name="generating-new-self-signed-certificate-if-autocertificaterollover-is-set-to-true"></a>AutoCertificateRollover이 TRUE로 설정 된 경우 자체 서명 된 새 인증서를 생성 합니다.
이 섹션에서는 **두 개의** 토큰 서명 인증서를 만듭니다.  첫 번째는 **-긴급** 플래그를 사용 하 여 현재 기본 인증서를 즉시 바꿉니다.  두 번째는 보조 인증서에 사용 됩니다.  

>[!IMPORTANT]
>두 인증서를 만드는 이유는 Azure가 이전 인증서와 관련 된 정보를 제공 하기 때문입니다.  두 번째를 만들면 Azure에서 이전 인증서에 대 한 정보를 해제 하 고 두 번째 인증서에 대 한 정보로 대체 합니다.
>
>두 번째 인증서를 만들지 않고 Azure를 업데이트 하는 경우 이전 토큰 서명 인증서를 사용 하 여 사용자를 인증할 수 있습니다.

다음 단계를 사용 하 여 새 토큰 서명 인증서를 생성할 수 있습니다.

 1. 기본 AD FS 서버에 로그온 했는지 확인 합니다.
 2. 관리자 권한으로 Windows PowerShell을 엽니다. 
 3. AutoCertificateRollover가 True로 설정 되어 있는지 확인 합니다.
`PS C:\>Get-AdfsProperties | FL AutoCert*, Certificate*`
 4. 새 토큰 서명 인증서를 생성 `Update-ADFSCertificate –CertificateType token-signing -Urgent` 하려면:
 5. 다음 명령을 실행 하 여 업데이트를 확인 합니다. `Get-ADFSCertificate –CertificateType token-signing`
 6. 이제 두 번째 토큰 서명 인증서를 생성 `Update-ADFSCertificate –CertificateType token-signing` 합니다.
 7. 다음 명령을 다시 실행 하 여 업데이트를 확인할 수 있습니다. `Get-ADFSCertificate –CertificateType token-signing`


## <a name="generating-new-certificates-manually-if-autocertificaterollover-is-set-to-false"></a>AutoCertificateRollover이 FALSE로 설정 된 경우 새 인증서를 수동으로 생성
자동으로 생성 된 기본 자체 서명 된 토큰 서명 및 토큰 암호 해독 인증서를 사용 하지 않는 경우 이러한 인증서를 수동으로 갱신 하 고 구성 해야 합니다.  여기에는 두 개의 새 토큰 서명 인증서를 만들고 가져오는 작업이 포함 됩니다.  그런 다음, 하나를 주로 승격 하 고 이전 인증서를 해지 하 고 두 번째 인증서를 보조 인증서로 구성 합니다.

먼저 인증 기관에서 두 개의 새 인증서를 가져와 각 페더레이션 서버의 로컬 컴퓨터 개인 인증서 저장소로 가져와야 합니다. 자세한 내용은 [인증서 가져오기](https://technet.microsoft.com/library/cc754489.aspx) 문서를 참조 하세요.

>[!IMPORTANT]
>두 인증서를 만드는 이유는 Azure가 이전 인증서와 관련 된 정보를 제공 하기 때문입니다.  두 번째를 만들면 Azure에서 이전 인증서에 대 한 정보를 해제 하 고 두 번째 인증서에 대 한 정보로 대체 합니다.
>
>두 번째 인증서를 만들지 않고 Azure를 업데이트 하는 경우 이전 토큰 서명 인증서를 사용 하 여 사용자를 인증할 수 있습니다.

### <a name="to-configure-a-new-certificate-as-a-secondary-certificate"></a>보조 인증서로 새 인증서를 구성 하려면
그런 다음 인증서 하나를 보조 AD FS 토큰 서명 또는 암호 해독 인증서로 구성 하 고 주 인증서로 수준을 올립니다.

1. 인증서를 가져온 다음 **AD FS Management** console을 엽니다.
2. **서비스** 를 확장하고 **인증서** 를 선택합니다.
3. 작업 창에서 **Token-Signing 인증서 추가** 를 클릭 합니다.
4. 표시된 인증서 목록에서 새 인증서를 선택하고 확인을 클릭합니다.

### <a name="to-promote-the-new-certificate-from-secondary-to-primary"></a>새 인증서의 수준을 보조 인증서에서 기본 인증서로 올리려면
AD FS에서 새 인증서를 가져오고 구성 했으므로를 기본 인증서로 설정 해야 합니다.
1. **AD FS Management** console을 엽니다.
2. **서비스** 를 확장하고 **인증서** 를 선택합니다.
3. 보조 토큰 서명 인증서를 클릭합니다.
4. **작업** 창에서 **기본으로 설정** 을 클릭합니다. 확인 메시지가 표시되면 예를 클릭합니다.
5. 새 인증서를 기본 인증서로 승격 한 후에는 계속 사용할 수 있으므로 이전 인증서를 제거 해야 합니다. 아래의 [이전 인증서 제거](#remove-your-old-certificates) 섹션을 참조 하세요.  

### <a name="to-configure-the-second-certificate-as-a-secondary-certificate"></a>보조 인증서로 두 번째 인증서를 구성 하려면
첫 번째 인증서를 추가 하 고이를 기본 인증서로 설정 하 고 이전 인증서를 제거 했으므로 두 번째 인증서를 가져옵니다.  그런 다음 인증서를 보조 AD FS 토큰 서명 인증서로 구성 해야 합니다.

1. 인증서를 가져온 다음 **AD FS Management** console을 엽니다.
2. **서비스** 를 확장하고 **인증서** 를 선택합니다.
3. 작업 창에서 **Token-Signing 인증서 추가** 를 클릭 합니다.
4. 표시된 인증서 목록에서 새 인증서를 선택하고 확인을 클릭합니다.

## <a name="update-azure-ad-with-the-new-token-signing-certificate"></a>새 토큰 서명 인증서를 사용 하 여 Azure AD 업데이트
Windows PowerShell용 Microsoft Azure Active Directory 모듈을 엽니다. 또는 Windows PowerShell을 열고 명령을 실행 합니다. `Import-Module msonline`

다음 명령을 실행 하 여 Azure AD에 연결 하 `Connect-MsolService` 고 전역 관리자 자격 증명을 입력 합니다.

>[!Note]
> 기본 페더레이션 서버가 아닌 컴퓨터에서 이러한 명령을 실행 하는 경우 먼저 다음 명령을 입력 `Set-MsolADFSContext –Computer <servername>` 합니다. <servername>을 AD FS 서버의 이름으로 바꿉니다. 그런 다음 메시지가 표시 되 면 AD FS 서버에 대 한 관리자 자격 증명을 입력 합니다.

필요에 따라 Azure AD에서 현재 인증서 정보를 확인 하 여 업데이트가 필요한 지 여부를 확인 합니다. 이렇게 하려면 다음 명령을 실행 `Get-MsolFederationProperty` 합니다. 메시지가 표시 되 면 페더레이션된 도메인의 이름을 입력 합니다.

Azure AD에서 인증서 정보를 업데이트 하려면 다음 명령을 실행 하 `Update-MsolFederatedDomain` 고 메시지가 표시 되 면 도메인 이름을 입력 합니다.

>[!Note]
> 이 명령을 실행할 때 오류가 표시 되 면 다음 명령을 실행 하 `Update-MsolFederatedDomain –SupportMultipleDomain` 고 메시지가 표시 되 면 도메인 이름을 입력 합니다.

## <a name="replace-ssl-certificates"></a>SSL 인증서 바꾸기
손상으로 인해 토큰 서명 인증서를 교체 해야 하는 경우 AD FS 및 WAP 서버에 대 한 SSL 인증서를 해지 하 고 바꾸어야 합니다.  

SSL 인증서 해지는 인증서를 발급 한 CA (인증 기관)에서 수행 해야 합니다.  이러한 인증서는 일반적으로 GoDaddy와 같은 타사 공급자가 발급 합니다.  예제를 보려면 (인증서 취소 | SSL 인증서-GoDaddy를 도와 드리겠습니다.  자세한 내용은 [인증서 해지 작동 방법](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee619754(v=ws.10)?redirectedfrom=MSDN)을 참조 하세요.

이전 SSL 인증서가 해지 되 고 새 인증서가 발급 되 면 SSL 인증서를 바꿀 수 있습니다. 자세한 내용은 [AD FS에 대 한 SSL 인증서 바꾸기](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap#replacing-the-ssl-certificate-for-ad-fs)를 참조 하십시오.


## <a name="remove-your-old-certificates"></a>이전 인증서 제거
이전 인증서를 바꾼 후에는 계속 사용할 수 있기 때문에 이전 인증서를 제거 해야 합니다. . 이렇게 하려면 다음 단계를 수행 합니다.  이렇게 하려면 아래 단계를 수행합니다.

1. 기본 AD FS 서버에 로그온 했는지 확인 합니다.
2. 관리자 권한으로 Windows PowerShell을 엽니다. 
4. 이전 토큰 서명 인증서를 제거 `Remove-ADFSCertificate –CertificateType token-signing -thumbprint <thumbprint>` 하려면:

## <a name="updating-federation-partners-who-can-consume-federation-metadata"></a>페더레이션 메타 데이터를 사용할 수 있는 페더레이션 파트너 업데이트
새 토큰 서명 또는 토큰 암호 해독 인증서를 갱신 하 고 구성한 경우에는 모든 페더레이션 파트너 (신뢰 당사자 트러스트와 클레임 공급자 트러스트를 통해 AD FS에 표시 되는 리소스 조직 또는 계정 조직 파트너)가 새 인증서를 선택 했는지 확인 해야 합니다.

## <a name="updating-federation-partners-who-can-not-consume-federation-metadata"></a>페더레이션 메타 데이터를 사용할 수 없는 페더레이션 파트너 업데이트
페더레이션 파트너가 페더레이션 메타 데이터를 사용할 수 없는 경우 새 토큰 서명/토큰 암호 해독 인증서의 공개 키를 수동으로 전송 해야 합니다. 모든 리소스 조직 또는 계정 조직 파트너 (신뢰 당사자 트러스트와 클레임 공급자 트러스트를 통해 AD FS에 표시)에 새 인증서 공개 키 (.cer 파일 또는 전체 체인을 포함 하려는 경우 p7b)를 보냅니다. 파트너가 새 인증서를 신뢰 하기 위해 그 쪽에서 변경 내용을 구현 하도록 합니다.



## <a name="revoke-refresh-tokens-via-powershell"></a>PowerShell을 통해 새로 고침 토큰 해지
이제 사용자에 대 한 새로 고침 토큰을 취소 하 고 다시 로그온 하 여 새 토큰을 가져오는 데 사용할 수 있습니다.  그러면 토큰을 사용 하는 다른 항목 및 새로 고침 토큰을 사용 하 여 사용자의 휴대폰, 현재 웹 메일 세션에서 사용자를 로그 아웃 합니다.  [여기](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0&preserve-view=true) 에서 정보를 찾을 수 있으며 [Azure Active Directory에서 사용자 액세스를 취소](../../active-directory/enterprise-users/users-revoke-access.md)하는 방법을 참조할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

- [Windows Server 2016의 AD FS 및 WAP에서 SSL 인증서 관리](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap#replacing-the-ssl-certificate-for-ad-fs)
- [AD FS에 대 한 토큰 서명 및 토큰 암호 해독 인증서 가져오기 및 구성](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn781426(v=ws.11)#updating-federation-partners)
- [Microsoft 365 및 Azure Active Directory에 대 한 페더레이션 인증서 갱신](how-to-connect-fed-o365-certs.md)



















