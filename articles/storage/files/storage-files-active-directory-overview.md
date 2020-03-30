---
title: 개요 - Azure 파일 ID 기반 권한 부여
description: Azure Files는 Azure Active Directory 도메인 서비스(AD DS) 및 Active Directory를 통해 SMB(서버 메시지 블록)에 대한 ID 기반 인증을 지원합니다. 도메인 가입 Windows VM(가상 머신)은 Azure AD 자격 증명을 사용하여 Azure 파일 공유에 액세스할 수 있습니다.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: rogarana
ms.openlocfilehash: 737cdfaddca3a5f7532620bdafd86149e4d61f9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061065"
---
# <a name="overview-of-azure-files-identity-based-authentication-support-for-smb-access"></a>SMB 액세스에 대한 Azure 파일 ID 기반 인증 지원 개요
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Azure 파일 공유에 대한 AD 인증을 활성화하는 방법을 알아보려면 Azure 파일 공유에 [대한 SMB에 대한 Active Directory 인증 활성화를](storage-files-identity-auth-active-directory-enable.md)참조하십시오.

Azure 파일 공유에 대한 Azure AD DS 인증을 활성화하는 방법을 알아보려면 [Azure 파일에서 Azure Active Directory 도메인 서비스 인증 을 활성화합니다.](storage-files-identity-auth-active-directory-domain-service-enable.md)

## <a name="glossary"></a>용어 
Azure 파일 공유에 대한 SMB에 대한 Azure AD 도메인 서비스 인증과 관련된 몇 가지 주요 용어를 이해하는 것이 좋습니다.

-   **케르베로스 인증**

    Kerberos는 사용자 또는 호스트의 ID를 확인하는 데 사용되는 인증 프로토콜입니다. Kerberos에 대한 자세한 내용은 [Kerberos 인증 개요](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview)를 참조하세요.

-  **SMB(서버 메시지 블록) 프로토콜**

    SMB는 산업 표준 네트워크 파일 공유 프로토콜입니다. SMB는 CIFS(Common Internet File System)라고도 합니다. SMB에 대한 자세한 내용은 [Microsoft SMB Protocol and CIFS Protocol Overview](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview)(Microsoft SMB 프로토콜 및 CIFS 프로토콜 개요)를 참조하세요.

-   **Azure Active 디렉터리(Azure AD)**

    Azure AD(Azure Active Directory)는 Microsoft의 다중 테넌트 클라우드 기반 디렉터리 및 ID 관리 서비스입니다. Azure AD에서는 핵심 디렉터리 서비스, 애플리케이션 액세스 관리 및 ID 보호가 하나의 솔루션으로 결합되어 있습니다. Azure AD를 사용하면 도메인에 가입한 Windows 가상 시스템(VM)이 Azure AD 자격 증명을 사용하여 Azure 파일 공유에 액세스할 수 있습니다. 자세한 내용은 [Azure Active Directory란?](../../active-directory/fundamentals/active-directory-whatis.md)을 참조하세요.

-   **Azure AD 도메인 서비스(Azure AD DS)**

    Azure AD 도메인 서비스(GA)는 도메인 가입, 그룹 정책, LDAP 및 Kerberos/NTLM 인증과 같은 관리되는 도메인 서비스를 제공합니다. 이러한 서비스는 Windows Server Active Directory와 완벽하게 호환됩니다. 자세한 내용은 [Azure AD(Active Directory) Domain Services](../../active-directory-domain-services/overview.md)를 참조하세요.

- **활성 디렉터리 도메인 서비스(AD DS, AD라고도 함)**

    Active Directory(AD) (미리 보기)는 네트워크 사용자와 관리자가 사용할 수 있도록 하면서 디렉터리 데이터를 저장하는 방법을 제공합니다. 보안은 로그온 인증 및 디렉터리에 있는 개체에 대한 액세스 제어를 통해 Active Directory와 통합됩니다. 단일 네트워크 로그온을 통해 관리자는 네트워크 전체에서 디렉터리 데이터와 조직을 관리할 수 있으며 권한 있는 네트워크 사용자는 네트워크의 어느 곳에서나 리소스에 액세스할 수 있습니다. AD는 일반적으로 온-프레미스의 기업에서 채택되며 AD 자격 증명을 액세스 제어를 위한 ID로 사용합니다. 자세한 내용은 [활성 디렉터리 도메인 서비스 개요를](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)참조하십시오.

-   **Azure RBAC(역할 기반 액세스 제어)**

    Azure 역할 기반 Access Control(RBAC)을 통해 Azure에 대한 세밀한 액세스 관리가 가능합니다. RBAC를 사용하면 사용자에게 작업을 수행하는 데 필요한 최소한의 권한을 부여하여 리소스에 대한 액세스를 관리할 수 있습니다. RBAC에 대한 자세한 내용은 [Azure에서 역할 기반 액세스 제어(RBAC)란 무엇입니까?](../../role-based-access-control/overview.md)

## <a name="common-use-cases"></a>일반 사용 예

Azure 파일의 Windows ACL에 대한 ID 기반 인증 및 지원은 다음과 같은 사용 사례에 가장 적합합니다.

### <a name="replace-on-premises-file-servers"></a>온-프레미스 파일 서버 교체

분산된 온-프레미스 파일 서버를 더 이상 사용되지 않고 교체하는 것은 모든 기업이 IT 현대화 여정에서 직면하는 일반적인 문제입니다. AD(미리 보기) 인증을 가진 Azure 파일 공유는 Azure Files로 데이터를 마이그레이션할 수 있는 경우에 가장 적합합니다. 완전한 마이그레이션을 통해 고가용성 및 확장성 이점을 활용하는 동시에 클라이언트 측의 변경 사항, 특히 복잡한 AD 도메인 인프라를 최소화할 수 있습니다. 최종 사용자에게 원활한 마이그레이션 환경을 제공하므로 기존 도메인 에 가입한 컴퓨터를 사용하여 동일한 자격 증명으로 데이터에 계속 액세스할 수 있습니다.

### <a name="lift-and-shift-applications-to-azure"></a>응용 프로그램을 Azure로 리프트 및 이동

응용 프로그램을 클라우드로 "리프트 앤 시프트"하는 경우 데이터에 대해 동일한 인증 모델을 유지하려고 합니다. ID 기반 액세스 제어 환경을 Azure 파일 공유로 확장하면 응용 프로그램을 최신 인증 메서드로 변경하고 클라우드 채택을 가속화할 필요가 없습니다. Azure 파일 공유는 인증을 위해 Azure AD DS(GA) 또는 AD(미리 보기)와 통합할 수 있는 옵션을 제공합니다. 계획이 100% 클라우드 네이티브이고 클라우드 인프라를 관리하는 노력을 최소화하는 것이라면 Azure AD DS는 완벽하게 관리되는 도메인 서비스로 더 적합할 것입니다. AD DS(GA) 기능과의 완벽한 호환성이 필요한 경우 VM에서 도메인 컨트롤러를 자체 호스팅하여 AD 환경을 클라우드로 확장하는 것이 좋습니다. 어느 쪽이든 비즈니스 요구에 맞는 도메인 서비스를 유연하게 선택할 수 있습니다.

### <a name="backup-and-disaster-recovery-dr"></a>백업 및 재해 복구(DR)

기본 파일 저장소를 온-프레미스에 보관하는 경우 Azure 파일 공유는 비즈니스 연속성을 개선하기 위해 백업 또는 DR에 이상적인 저장소역할을 할 수 있습니다. Azure 파일 공유를 사용하여 Windows DACL을 유지하면서 기존 파일 서버에서 데이터를 백업할 수 있습니다. DR 시나리오의 경우 장애 조치(failover)에서 적절한 액세스 제어 적용을 지원하도록 인증 옵션을 구성할 수 있습니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

다음 표에서는 지원되는 Azure 파일 공유 Azure AD DS(GA) 및 AD(미리 보기)에 대한 인증 시나리오를 요약합니다. Azure Files와의 통합을 위해 클라이언트 환경에 대해 채택한 도메인 서비스를 선택하는 것이 좋습니다. 장치가 AD에 도메인으로 조인된 Azure 또는 Azure에서 이미 AD(미리 보기)가 설정된 경우 Azure 파일 공유 인증에 AD(미리 보기)를 활용하도록 선택해야 합니다. 마찬가지로 이미 AZURE AD DS(Ga)를 채택한 경우 Azure 파일 공유 인증에 해당 기능을 사용해야 합니다.


|AZURE AD DS(GA) 인증  |AD(미리 보기) 인증  |
|---------|---------|
|Azure AD DS 도메인에 가입한 Windows 컴퓨터는 SMB를 통해 Azure AD 자격 증명을 통해 Azure 파일 공유에 액세스할 수 있습니다.     |AD 도메인에 가입한 Windows 컴퓨터는 SMB를 통해 Azure AD에 동기화되는 AD 자격 증명을 사용하여 Azure 파일 공유에 액세스할 수 있습니다.         |

### <a name="unsupported-scenarios"></a>지원되지 않는 시나리오

- Azure AD DS(GA) 및 AD(미리 보기) 인증은 컴퓨터 계정에 대한 인증을 지원하지 않습니다. 대신 서비스 로그온 계정을 사용할 수 있습니다.
- Azure AD DS(GA) 인증은 Azure AD 클라우드 조인 장치에 대한 인증을 지원하지 않습니다.

## <a name="advantages-of-identity-based-authentication"></a>ID 기반 인증의 장점
Azure 파일에 대한 ID 기반 인증은 공유 키 인증을 사용하면 다음과 같은 몇 가지 이점을 제공합니다.

-   **AD 및 Azure AD DS를 사용하여 기존 ID 기반 파일 공유 액세스 환경을 클라우드로 확장**  
    응용 프로그램을 클라우드로 "리프트 앤 시프트"하여 기존 파일 서버를 Azure 파일 공유로 대체하려는 경우 응용 프로그램이 AD 또는 Azure AD 자격 증명으로 인증하여 파일 데이터에 액세스하도록 할 수 있습니다. Azure Files는 AD 또는 Azure AD 자격 증명을 모두 사용하여 AD 또는 Azure AD DS 도메인에 가입한 VM에서 SMB를 통해 Azure 파일 공유에 액세스합니다.

-   **Azure 파일 공유에 대한 세부적인 액세스 제어 적용**  
    공유, 디렉터리 또는 파일 수준에서 특정 ID에 대한 권한을 부여할 수 있습니다. 예를 들어 프로젝트 협업을 위해 단일 Azure 파일 공유를 사용하는 여러 팀이 있다고 가정할 수 있습니다. 이런 경우 중요하지 않은 디렉터리에 대한 액세스 권한은 모든 팀에 부여하면서 중요한 재무 데이터가 포함된 디렉터리에 대한 액세스 권한은 재무 팀으로만 제한할 수 있습니다. 

-   **데이터와 함께 Windows ACL(NTFS라고도 함)을 백업합니다.**  
    Azure 파일 공유를 사용하여 기존 온-프레미스 파일 공유를 백업할 수 있습니다. Azure 파일은 SMB를 통해 Azure 파일 공유에 파일 공유를 백업할 때 데이터와 함께 ACL을 유지합니다.

## <a name="how-it-works"></a>작동 방법
Azure 파일 공유는 AZURE AD DS(GA) 또는 AD(미리 보기)와의 통합을 위해 kerberos 인증을 지원합니다. Azure 파일 공유에서 인증을 활성화하려면 먼저 도메인 환경을 설정해야 합니다. Azure AD DS(GA) 인증의 경우 Azure AD 도메인 서비스 및 도메인이 파일 데이터에 액세스하도록 계획한 VM에 가입하도록 설정해야 합니다. 도메인 에 가입한 VM은 Azure AD 도메인 서비스와 동일한 VNET(가상 네트워크)에 있어야 합니다. 마찬가지로 AD(미리 보기) 인증의 경우 Active Directory 도메인 컨트롤러와 도메인을 컴퓨터 또는 VM에 가입하도록 설정해야 합니다.

VM에서 실행 중인 응용 프로그램과 연결된 ID가 Azure 파일 공유의 데이터에 액세스하려고 하면 요청을 Azure AD 도메인 서비스로 전송하여 ID를 인증합니다. 인증에 성공하면 Azure AD Domain Services가 Kerberos 토큰을 반환합니다. 응용 프로그램은 Kerberos 토큰을 포함하는 요청을 보내고 Azure 파일 공유는 해당 토큰을 사용하여 요청을 승인합니다. Azure 파일 공유는 토큰만 수신하며 Azure AD 자격 증명을 유지하지 않습니다. AD 인증은 AD가 Kerberos 토큰을 제공하는 유사한 방식으로 작동합니다.

![SMB를 통한 Azure AD 인증 다이어그램을 보여 주는 스크린샷](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-identity-based-authentication"></a>ID 기반 인증 사용

새 저장소 및 기존 저장소 계정에서 Azure 파일 공유에 대한 AZURE AD DS(GA) 또는 AD(미리 보기)를 사용하여 ID 기반 인증을 활성화할 수 있습니다. 저장소 계정의 파일 액세스 인증에는 하나의 도메인 서비스만 사용할 수 있으며, 이는 계정의 모든 파일 공유에 적용됩니다. 이 문서에서 Azure AD DS(Azure AD DS)를 사용하여 인증을 위해 파일 공유를 설정하는 단계별 지침에 따라 [Azure 파일에서 Azure Active Directory 도메인 서비스 인증 을 활성화하고](storage-files-identity-auth-active-directory-domain-service-enable.md) 다른 문서에서 AD(미리 보기)에 대한 지침, [Azure 파일 공유에 대한 SMB에 대한 Active Directory 인증 사용 에 대한](storage-files-identity-auth-active-directory-enable.md)지침입니다.

### <a name="configure-share-level-permissions-for-azure-files"></a>Azure Files에 대한 공유 수준 권한 구성

Azure AD DS(GA) 또는 AD(미리 보기) 인증이 활성화되면 기본 제공 RBAC 역할을 사용하거나 Azure AD ID에 대한 사용자 지정 역할을 구성하고 저장소 계정의 모든 파일 공유에 액세스 권한을 할당할 수 있습니다. 할당된 권한을 사용하면 부여된 ID가 루트 디렉터리도 아닌 다른 것만 공유에 액세스할 수 있습니다. Azure 파일 공유에 대한 디렉터리 또는 파일 수준 사용 권한을 별도로 구성해야 합니다.

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>Azure 파일에 대한 디렉터리 또는 파일 수준 사용 권한 구성

Azure 파일 공유는 루트 디렉터리를 포함하여 디렉터리 및 파일 수준 모두에서 표준 Windows 파일 권한을 적용합니다. 디렉터리 또는 파일 수준 사용 권한의 구성은 SMB 및 REST 모두에서 지원됩니다. VM에서 대상 파일 공유를 탑재하고 Windows 파일 탐색기, Windows icacls 또는 [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl?view=powershell-6) 명령을 사용하여 권한을 [구성합니다.](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>슈퍼 사용자 권한은 스토리지 계정 키 사용

저장소 계정 키를 소유한 사용자는 수퍼유저 권한을 통해 Azure 파일 공유에 액세스할 수 있습니다. 수퍼유저 권한은 모든 액세스 제어 제한을 우회합니다.

> [!IMPORTANT]
> 보안 권장 방법은 저장소 계정 키를 공유하지 않도록 하고 가능하면 ID 기반 인증을 활용하는 것입니다.

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Azure 파일 공유로 데이터를 가져올 때 디렉터리 및 파일 ACL 보존

Azure Files는 Azure 파일 공유에 데이터를 복사할 때 디렉터리 또는 파일 수준 ACL 보존을 지원합니다. Azure 파일 동기화 또는 공통 파일 이동 도구 집합을 사용하여 디렉터리 또는 파일에서 ACL을 Azure 파일 공유에 복사할 수 있습니다. 예를 들어 플래그가 `/copy:s` 있는 [로보카피를](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) 사용하여 Azure 파일 공유에 ACL뿐만 아니라 데이터를 복사할 수 있습니다. ACL은 기본적으로 보존되며, ACL을 보존하기 위해 저장소 계정에서 ID 기반 인증을 사용하도록 설정할 필요는 없습니다.

## <a name="pricing"></a>가격 책정
저장소 계정에서 SMB에 대한 ID 기반 인증을 활성화하기 위한 추가 서비스 요금은 없습니다. 가격 책정에 대한 자세한 내용은 AAD DS 정보를 찾는 경우 [Azure 파일 가격 책정](https://azure.microsoft.com/pricing/details/storage/files/) 및 Azure [AD 도메인 서비스 가격 책정](https://azure.microsoft.com/pricing/details/active-directory-ds/) 페이지를 참조하세요.

## <a name="next-steps"></a>다음 단계
SMB에 대한 Azure 파일 및 ID 기반 인증에 대한 자세한 내용은 다음 리소스를 참조하십시오.

- [Azure Files 배포에 대한 계획](storage-files-planning.md)
- [Azure 파일 공유에 대한 SMB를 통해 Active Directory 인증 사용](storage-files-identity-auth-active-directory-enable.md)
- [Azure 파일에서 Azure Active Directory 도메인 서비스 인증 사용](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [FAQ](storage-files-faq.md)
