---
title: 개요-id 기반 권한 부여 Azure Files
description: Azure Files Azure Active Directory Domain Services (AD DS) 및 Active Directory를 통해 SMB (서버 메시지 블록)를 통한 id 기반 인증을 지원 합니다. 도메인 가입 Windows VM(가상 머신)은 Azure AD 자격 증명을 사용하여 Azure 파일 공유에 액세스할 수 있습니다.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: rogarana
ms.openlocfilehash: db256c8361af740ac536e059969a5085e57df485
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84263363"
---
# <a name="overview-of-azure-files-identity-based-authentication-options-for-smb-access"></a>SMB 액세스를 위한 Azure Files id 기반 인증 옵션 개요
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Azure 파일 공유에 대해 온-프레미스 Active Directory Domain Services 인증을 사용 하도록 설정 하는 방법을 알아보려면 [azure 파일 공유에 대해 SMB를 통한 온-프레미스 Active Directory Domain Services 인증 사용](storage-files-identity-auth-active-directory-enable.md)을 참조 하세요.

Azure 파일 공유에 대해 Azure AD DS 인증을 사용 하도록 설정 하는 방법을 알아보려면 [Azure Files에서 Azure Active Directory Domain Services 인증 사용](storage-files-identity-auth-active-directory-domain-service-enable.md)을 참조 하세요.

## <a name="glossary"></a>용어 
Azure 파일 공유를 위해 SMB를 통한 Azure AD 도메인 서비스 인증과 관련 된 몇 가지 주요 용어를 이해 하는 것이 유용 합니다.

-   **Kerberos 인증**

    Kerberos는 사용자 또는 호스트의 ID를 확인하는 데 사용되는 인증 프로토콜입니다. Kerberos에 대한 자세한 내용은 [Kerberos 인증 개요](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview)를 참조하세요.

-  **SMB(서버 메시지 블록) 프로토콜**

    SMB는 산업 표준 네트워크 파일 공유 프로토콜입니다. SMB는 CIFS(Common Internet File System)라고도 합니다. SMB에 대한 자세한 내용은 [Microsoft SMB Protocol and CIFS Protocol Overview](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview)(Microsoft SMB 프로토콜 및 CIFS 프로토콜 개요)를 참조하세요.

-   **Azure Active Directory (Azure AD)**

    Azure AD(Azure Active Directory)는 Microsoft의 다중 테넌트 클라우드 기반 디렉터리 및 ID 관리 서비스입니다. Azure AD에서는 핵심 디렉터리 서비스, 애플리케이션 액세스 관리 및 ID 보호가 하나의 솔루션으로 결합되어 있습니다. Azure AD에 가입 된 Windows Vm (가상 머신)은 azure AD 자격 증명을 사용 하 여 Azure 파일 공유에 액세스할 수 있습니다. 자세한 내용은 [Azure Active Directory 란?](../../active-directory/fundamentals/active-directory-whatis.md) 을 참조 하세요.

-   **Azure AD DS(Azure Active Directory Domain Services)**

    Azure AD DS는 도메인 가입, 그룹 정책, LDAP, Kerberos/NTLM 인증 등의 관리 되는 도메인 서비스를 제공 합니다. 이러한 서비스는 Active Directory Domain Services와 완전히 호환 됩니다. 자세한 내용은 [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md)를 참조 하세요.

- **온-프레미스 Active Directory Domain Services (AD DS)**

    온-프레미스 Active Directory Domain Services (AD DS) Azure Files와 통합 되어 네트워크 사용자 및 관리자가 사용할 수 있도록 하는 동안 디렉터리 데이터를 저장 하는 방법을 제공 합니다. 보안은 디렉터리의 개체에 대 한 로그온 인증 및 액세스 제어를 통해 AD DS와 통합 됩니다. 관리자는 단일 네트워크 로그온을 통해 네트워크에서 디렉터리 데이터 및 조직을 관리할 수 있으며, 권한 있는 네트워크 사용자는 네트워크의 모든 위치에서 리소스에 액세스할 수 있습니다. AD DS은 일반적으로 온-프레미스 환경에서 기업에 의해 채택 되 고 AD DS 자격 증명은 액세스 제어를 위한 id로 사용 됩니다. 자세한 내용은 [Active Directory Domain Services 개요](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)를 참조 하세요.

-   **Azure RBAC(역할 기반 액세스 제어)**

    Azure 역할 기반 Access Control(RBAC)을 통해 Azure에 대한 세밀한 액세스 관리가 가능합니다. RBAC를 사용하면 사용자에게 작업을 수행하는 데 필요한 최소한의 권한을 부여하여 리소스에 대한 액세스를 관리할 수 있습니다. RBAC에 대 한 자세한 내용은 [Azure의 rbac (역할 기반 액세스 제어) 란?](../../role-based-access-control/overview.md)을 참조 하세요.

## <a name="common-use-cases"></a>일반 사용 예

Azure Files의 Windows Acl에 대 한 id 기반 인증 및 지원은 다음과 같은 사용 사례에서 가장 잘 활용 됩니다.

### <a name="replace-on-premises-file-servers"></a>온-프레미스 파일 서버 바꾸기

분산 된 온-프레미스 파일 서버를 사용 중단 하 고 교체 하는 것은 모든 기업이 IT 현대화 경험을 경험 하는 일반적인 문제입니다. 온-프레미스 AD DS 인증을 사용 하는 Azure 파일 공유는 데이터를 Azure Files로 마이그레이션할 수 있는 경우에 가장 적합 합니다. 마이그레이션을 완료 하면 클라이언트 쪽 변경을 최소화 하면서 고가용성 및 확장성 이점을 활용할 수 있습니다. 최종 사용자에 게 원활한 마이그레이션 환경을 제공 하므로 기존 도메인에 가입 된 컴퓨터를 사용 하 여 동일한 자격 증명을 사용 하 여 데이터에 계속 액세스할 수 있습니다.

### <a name="lift-and-shift-applications-to-azure"></a>응용 프로그램을 Azure로 리프트 앤 시프트

응용 프로그램을 클라우드로 리프트 앤 시프트 하는 경우 데이터에 대해 동일한 인증 모델을 유지 하려고 합니다. Id 기반 액세스 제어 환경을 Azure 파일 공유로 확장할 때 응용 프로그램을 최신 인증 방법으로 변경 하 고 클라우드 도입을 촉진 하지 않아도 됩니다. Azure 파일 공유는 인증을 위해 Azure AD DS 또는 온-프레미스 AD DS와 통합 하는 옵션을 제공 합니다. 요금제가 100% 클라우드 기본이 되 고 클라우드 인프라 관리를 최소화 하는 경우 Azure AD DS는 완전히 관리 되는 도메인 서비스로 적합 합니다. AD DS 기능과 완벽 하 게 호환 되어야 하는 경우 Vm에서 도메인 컨트롤러 자체를 호스트 하 여 AD DS 환경을 클라우드로 확장 하는 것을 고려할 수 있습니다. 어떤 방법이 든 비즈니스 요구에 적합 한 도메인 서비스를 선택할 수 있는 유연성을 제공 합니다.

### <a name="backup-and-disaster-recovery-dr"></a>백업 및 재해 복구 (DR)

기본 파일 저장소를 온-프레미스에 유지 하는 경우 Azure 파일 공유는 비즈니스 연속성을 향상 시키기 위해 백업 또는 DR에 이상적인 저장소 역할을 할 수 있습니다. Azure 파일 공유를 사용 하 여 Windows Dacl을 유지 하면서 기존 파일 서버에서 데이터를 백업할 수 있습니다. DR 시나리오의 경우 장애 조치 (failover) 시 적절 한 액세스 제어 적용을 지원 하도록 인증 옵션을 구성할 수 있습니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

다음 표에는 Azure AD DS 및 온-프레미스 AD DS에 대해 지원 되는 Azure 파일 공유 인증 시나리오가 요약 되어 있습니다. Azure Files와 통합 하기 위해 클라이언트 환경에 채택 된 도메인 서비스를 선택 하는 것이 좋습니다. 온-프레미스 또는 장치가 AD에 도메인에 가입 되어 있는 경우 온-프레미스 또는 Azure에서 이미 설치 AD DS 경우 Azure 파일 공유 인증에 대 한 AD DS를 활용 하도록 선택 해야 합니다. 마찬가지로 Azure AD DS를 이미 채택한 경우 Azure 파일 공유에 인증 하는 데이를 사용 해야 합니다.


|Azure AD DS 인증  | 온-프레미스 AD DS 인증  |
|---------|---------|
|Azure AD DS에 가입 된 Windows 컴퓨터는 SMB를 통해 Azure AD 자격 증명을 사용 하 여 Azure 파일 공유에 액세스할 수 있습니다.     |온-프레미스 AD DS 가입 된 또는 Azure AD DS에 가입 된 Windows 컴퓨터는 SMB를 통해 Azure AD에 동기화 되는 온-프레미스 Active Directory 자격 증명을 사용 하 여 Azure 파일 공유에 액세스할 수 있습니다. 클라이언트는 AD DS에 대 한 시야를가지고 있어야 합니다.        |

### <a name="restrictions"></a>제한 사항

- Azure AD DS와 온-프레미스 AD DS 인증은 컴퓨터 계정에 대 한 인증을 지원 하지 않습니다. 대신 서비스 로그온 계정을 사용 하는 것을 고려할 수 있습니다.
- Azure AD에 가입 된 장치 또는 Azure AD 등록 장치에 대해 Azure AD DS 인증 및 온-프레미스 AD DS 인증을 지원 하지 않습니다.
- Azure 파일 공유는 [Azure Active Directory Domain Services (Azure AD DS)](#azure-ad-ds) 또는 [온-프레미스 Active Directory Domain Services (AD DS)](#ad-ds)도메인 서비스 중 하나에 대해서만 id 기반 인증을 지원 합니다.

## <a name="advantages-of-identity-based-authentication"></a>Id 기반 인증의 이점
Azure Files에 대 한 id 기반 인증은 공유 키 인증 사용에 대 한 몇 가지 이점을 제공 합니다.

-   **온-프레미스 AD DS 및 Azure를 사용 하 여 기존 id 기반 파일 공유 액세스 환경을 클라우드로 확장 AD DS**  
    응용 프로그램을 클라우드로 전환 하려는 경우 기존 파일 서버를 Azure 파일 공유로 바꿔서 응용 프로그램에서 온-프레미스 AD DS 또는 Azure AD DS 자격 증명을 사용 하 여 인증 하 여 파일 데이터에 액세스 하도록 할 수 있습니다. Azure Files 온-프레미스 AD DS 또는 Azure AD DS 자격 증명을 사용 하 여 온-프레미스 AD DS 또는 Azure AD DS 도메인 가입 Vm에서 SMB를 통해 Azure 파일 공유에 액세스 하도록 지원 합니다.

-   **Azure 파일 공유에 대한 세부적인 액세스 제어 적용**  
    공유, 디렉터리 또는 파일 수준에서 특정 id에 대 한 사용 권한을 부여할 수 있습니다. 예를 들어 프로젝트 협업을 위해 단일 Azure 파일 공유를 사용하는 여러 팀이 있다고 가정할 수 있습니다. 이런 경우 중요하지 않은 디렉터리에 대한 액세스 권한은 모든 팀에 부여하면서 중요한 재무 데이터가 포함된 디렉터리에 대한 액세스 권한은 재무 팀으로만 제한할 수 있습니다. 

-   **데이터와 함께 Windows Acl (NTFS 라고도 함) 백업**  
    Azure 파일 공유를 사용 하 여 기존 온-프레미스 파일 공유를 백업할 수 있습니다. Azure Files은 SMB를 통해 Azure 파일 공유에 파일 공유를 백업할 때 데이터와 함께 Acl을 유지 합니다.

## <a name="how-it-works"></a>작동 방법

Azure 파일 공유는 온-프레미스 AD DS 또는 Azure AD DS를 사용 하 여 인증 하기 위해 Kerberos 프로토콜을 활용 합니다. 클라이언트에서 실행 되는 사용자 또는 응용 프로그램과 연결 된 id가 Azure 파일 공유의 데이터에 액세스 하려고 하면 요청은 AD DS 또는 Azure AD DS에서 id를 인증 하는 도메인 서비스로 전송 됩니다. 인증에 성공 하면 Kerberos 토큰을 반환 합니다. 클라이언트는 Kerberos 토큰을 포함 하는 요청을 보내고, Azure 파일 공유는 해당 토큰을 사용 하 여 요청에 권한을 부여 합니다. Azure 파일 공유는 액세스 자격 증명이 아닌 Kerberos 토큰만 받습니다.

Azure 파일 공유에서 id 기반 인증을 사용 하도록 설정 하려면 먼저 도메인 환경을 설정 해야 합니다.

### <a name="ad-ds"></a>AD DS

온-프레미스 AD DS 인증의 경우 AD 도메인 컨트롤러와 도메인 가입 컴퓨터 또는 Vm을 설정 해야 합니다. Azure Vm 또는 온-프레미스에서 도메인 컨트롤러를 호스트할 수 있습니다. 어느 쪽이 든 도메인에 가입 된 클라이언트는 도메인 서비스에 대 한 시야를가지고 있어야 하므로 도메인 서비스의 회사 네트워크 또는 VNET (가상 네트워크) 내에 있어야 합니다.

다음 다이어그램은 SMB를 통해 Azure 파일 공유에 대 한 온-프레미스 AD DS 인증을 보여 줍니다. 온-프레미스 AD DS Azure AD Connect sync를 사용 하 여 Azure AD에 동기화 해야 합니다. 온-프레미스 AD DS 및 Azure AD 둘 다에 존재 하는 하이브리드 사용자만 인증 하 고 Azure 파일 공유 액세스 권한을 부여할 수 있습니다. 이는 AD DS에서 디렉터리/파일 수준 사용 권한이 적용 되는 Azure AD에 표시 된 id에 대해 공유 수준 권한이 구성 되기 때문입니다. 동일한 하이브리드 사용자에 대해 사용 권한을 올바르게 구성 해야 합니다.

:::image type="content" source="media/storage-files-active-directory-overview/Files-on-premises-AD-DS-Diagram.png" alt-text="다이어그램":::

### <a name="azure-ad-ds"></a>Azure AD DS

Azure AD DS 인증의 경우 파일 데이터에 액세스 하려는 Vm에 대 한 Azure AD Domain Services 및 도메인 가입을 사용 하도록 설정 해야 합니다. 도메인에 가입 된 VM은 Azure AD DS와 동일한 VNET (가상 네트워크)에 상주해 야 합니다. 

다음 다이어그램은 SMB를 통해 Azure 파일 공유에 대 한 Azure AD DS 인증 워크플로를 나타냅니다. Azure 파일 공유에 대 한 온-프레미스 AD DS 인증과 비슷한 패턴을 따릅니다. 다음과 같은 두 가지 주요 차이점이 있습니다.

- 먼저 저장소 계정을 나타내기 위해 Azure AD DS에서 id를 만들 필요가 없습니다. 이 작업은 백그라운드에서 활성화 프로세스에 의해 수행 됩니다.

- 둘째, Azure AD에 있는 모든 사용자가 인증 되 고 권한이 부여 될 수 있습니다. 사용자는 클라우드 전용 또는 하이브리드 일 수 있습니다. Azure AD에서 Azure AD DS로의 동기화는 사용자 구성을 요구 하지 않고 플랫폼에 의해 관리 됩니다. 그러나 클라이언트는 Azure AD DS에 가입 된 도메인 이어야 하며, Azure AD에 조인 되거나 등록 될 수 없습니다. 

:::image type="content" source="media/storage-files-active-directory-overview/Files-Azure-AD-DS-Diagram.png" alt-text="다이어그램":::

### <a name="enable-identity-based-authentication"></a>Id 기반 인증 사용

새 및 기존 저장소 계정의 Azure 파일 공유에 대해 Azure AD DS 또는 온-프레미스 AD DS를 사용 하 여 id 기반 인증을 사용 하도록 설정할 수 있습니다. 저장소 계정의 파일 액세스 인증에는 하나의 도메인 서비스만 사용할 수 있으며,이는 계정의 모든 파일 공유에 적용 됩니다. Azure AD DS을 사용 하 여 인증을 위한 파일 공유 설정에 대 한 자세한 지침은 다른 문서에서 온-프레미스 AD DS에 대 한 [Azure Active Directory Domain Services Azure Files 인증 사용](storage-files-identity-auth-active-directory-domain-service-enable.md) 및 [azure 파일 공유에 대해 SMB를 통한 온-프레미스 Active Directory Domain Services 인증 사용](storage-files-identity-auth-active-directory-enable.md)을 참조 하세요.

### <a name="configure-share-level-permissions-for-azure-files"></a>Azure Files에 대한 공유 수준 권한 구성

Azure AD DS 또는 온-프레미스 AD DS 인증을 사용 하는 경우 기본 제공 RBAC 역할을 사용 하거나 Azure AD id에 대 한 사용자 지정 역할을 구성 하 고 저장소 계정의 모든 파일 공유에 대 한 액세스 권한을 할당할 수 있습니다. 할당 된 사용 권한을 통해 부여 된 id는 루트 디렉터리 뿐만 아니라 공유에 대해서만 액세스 권한을 얻을 수 있습니다. Azure 파일 공유에 대 한 디렉터리 또는 파일 수준 권한을 별도로 구성 해야 합니다.

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>Azure Files에 대 한 디렉터리 또는 파일 수준 권한 구성

Azure 파일 공유는 루트 디렉터리를 포함 하 여 디렉터리와 파일 수준 모두에서 표준 Windows 파일 사용 권한을 적용 합니다. 디렉터리 또는 파일 수준 권한 구성은 SMB와 REST 모두에서 지원 됩니다. VM에서 대상 파일 공유를 탑재 하 고 Windows 파일 탐색기, Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)또는 [ACL 설정](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl?view=powershell-6) 명령을 사용 하 여 사용 권한을 구성 합니다.

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>슈퍼 사용자 권한은 스토리지 계정 키 사용

저장소 계정 키가 있는 사용자는 수퍼유저 권한으로 Azure 파일 공유에 액세스할 수 있습니다. 수퍼유저 권한은 모든 액세스 제어 제한을 무시 합니다.

> [!IMPORTANT]
> 권장 되는 보안 모범 사례는 저장소 계정 키를 공유 하지 않고 가능 하면 id 기반 인증을 활용 하는 것입니다.

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Azure 파일 공유로 데이터를 가져올 때 디렉터리 및 파일 Acl 유지

Azure Files는 Azure 파일 공유에 데이터를 복사할 때 디렉터리 또는 파일 수준 Acl 유지를 지원 합니다. Azure File Sync 또는 공통 파일 이동 도구 집합을 사용 하 여 디렉터리 또는 파일의 Acl을 Azure 파일 공유에 복사할 수 있습니다. 예를 들어, [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) 를 플래그와 함께 사용 `/copy:s` 하 여 Azure 파일 공유에 대 한 acl 뿐만 아니라 데이터를 복사할 수 있습니다. Acl은 기본적으로 유지 되므로 저장소 계정에서 Acl을 유지 하기 위해 id 기반 인증을 사용 하도록 설정할 필요는 없습니다.

## <a name="pricing"></a>가격 책정
저장소 계정에서 SMB를 통한 id 기반 인증을 사용 하도록 설정 하는 추가 서비스 요금은 없습니다. 가격 책정에 대 한 자세한 내용은 [Azure Files 가격](https://azure.microsoft.com/pricing/details/storage/files/) 책정 및 [Azure AD Domain Services 가격](https://azure.microsoft.com/pricing/details/active-directory-ds/)책정을 참조 하세요.

## <a name="next-steps"></a>다음 단계
SMB를 통한 Azure Files 및 id 기반 인증에 대 한 자세한 내용은 다음 리소스를 참조 하세요.

- [Azure 파일 배포에 대한 계획](storage-files-planning.md)
- [Azure 파일 공유를 위해 SMB를 통한 온-프레미스 Active Directory Domain Services 인증 사용](storage-files-identity-auth-active-directory-enable.md)
- [Azure Files에서 Azure Active Directory Domain Services 인증 사용](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [FAQ](storage-files-faq.md)
