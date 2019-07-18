---
title: Azure Files용 SMB를 통한 Azure Active Directory 인증(미리 보기) 개요 - Azure Storage
description: Azure Files는 Azure AD(Azure Active Directory) Domain Services를 사용하여 SMB(서버 메시지 블록)를 통한 ID 기반 인증(미리 보기)을 지원합니다. 도메인 조인 Windows VM(가상 머신)은 Azure AD 자격 증명을 사용하여 Azure 파일 공유에 액세스할 수 있습니다.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 06/18/2019
ms.author: rogarana
ms.openlocfilehash: 21087424be1a7a3edfe2dddcbec830bd74559b23
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67269344"
---
# <a name="overview-of-azure-files-azure-active-directory-domain-service-aad-ds-authentication-support-for-smb-access-preview"></a>Azure Files Azure Active Directory 도메인 서비스 (AAD DS) 인증 지원에 대 한 SMB 액세스 (미리 보기) 개요
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Azure Files에 대 한 AAD DS 인증을 사용 하는 방법에 알아보려면 참조 [Azure Active Directory 도메인 서비스 인증 사용 (미리 보기) Azure files는 SMB 통한](storage-files-active-directory-enable.md)합니다.

## <a name="glossary"></a>용어 
Azure Files에 대 한 SMB를 통한 Azure AD 도메인 서비스 인증에 관련 된 몇 가지 주요 용어를 이해 하는 것이 유용 합니다.

-   **Azure AD(Azure Active Directory)**  
    Azure AD(Azure Active Directory)는 Microsoft의 다중 테넌트 클라우드 기반 디렉터리 및 ID 관리 서비스입니다. Azure AD에서는 핵심 디렉터리 서비스, 애플리케이션 액세스 관리 및 ID 보호가 하나의 솔루션으로 결합되어 있습니다. 자세한 내용은 [Azure Active Directory란?](../../active-directory/fundamentals/active-directory-whatis.md)을 참조하세요.

-   **Azure AD Domain Services**  
    Azure AD Domain Services는 도메인 가입, 그룹 정책, LDAP, Kerberos/NTLM 인증 등 관리되는 도메인 서비스를 제공합니다. 이러한 서비스는 Windows Server Active Directory와 완벽하게 호환됩니다. 자세한 내용은 [Azure AD(Active Directory) Domain Services](../../active-directory-domain-services/overview.md)를 참조하세요.

-   **Azure RBAC(역할 기반 액세스 제어)**  
    Azure 역할 기반 Access Control(RBAC)을 통해 Azure에 대한 세밀한 액세스 관리가 가능합니다. RBAC를 사용하면 사용자에게 작업을 수행하는 데 필요한 최소한의 권한을 부여하여 리소스에 대한 액세스를 관리할 수 있습니다. RBAC에 대한 자세한 내용은 [Azure의 RBAC(역할 기반 액세스 제어)란?](../../role-based-access-control/overview.md)을 참조하세요.

-   **Kerberos 인증**

    Kerberos는 사용자 또는 호스트의 ID를 확인하는 데 사용되는 인증 프로토콜입니다. Kerberos에 대한 자세한 내용은 [Kerberos 인증 개요](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview)를 참조하세요.

-  **SMB(서버 메시지 블록) 프로토콜**  
    SMB는 산업 표준 네트워크 파일 공유 프로토콜입니다. SMB는 CIFS(Common Internet File System)라고도 합니다. SMB에 대한 자세한 내용은 [Microsoft SMB Protocol and CIFS Protocol Overview](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview)(Microsoft SMB 프로토콜 및 CIFS 프로토콜 개요)를 참조하세요.

## <a name="advantages-of-azure-ad-domain-service-authentication"></a>Azure AD 도메인 서비스 인증의 장점
Azure Files에 대 한 azure AD 도메인 서비스 인증은 공유 키 인증을 사용 하 여 여러 가지 이점을 제공 합니다.

-   **기존 id를 기반으로 파일 공유 액세스 환경을 Azure AD와 Azure AD를 사용 하 여 클라우드로 확장할 도메인 서비스**  
    "리프트 앤 시프트" 하려는 경우 Azure AD로 인증 하도록 응용 프로그램을 사용할 수 있습니다 Azure Files를 사용 하 여 기존의 파일 서버를 교체 하 고 클라우드로 응용 프로그램 파일 데이터에 액세스 하려면 자격 증명입니다. AAD DS에서 SMB를 통한 Azure Files에 액세스할를 Azure AD 자격 증명을 사용 하 여 azure 파일에서는 도메인에 가입 된 Windows Vm입니다. 모든 온-프레미스 Active Directory 개체를 Azure AD에 동기화하여 사용자 이름, 암호 및 기타 그룹 할당을 유지하도록 선택할 수도 있습니다.

-   **Azure 파일 공유에 대한 세부적인 액세스 제어 적용**  
    공유, 디렉터리 또는 파일 수준에서 특정 id로 사용 권한을 부여할 수 있습니다. 예를 들어 프로젝트 협업을 위해 단일 Azure 파일 공유를 사용하는 여러 팀이 있다고 가정할 수 있습니다. 이런 경우 중요하지 않은 디렉터리에 대한 액세스 권한은 모든 팀에 부여하면서 중요한 재무 데이터가 포함된 디렉터리에 대한 액세스 권한은 재무 팀으로만 제한할 수 있습니다. 

-   **데이터와 함께 ACL 백업**  
    Azure Files를 사용하여 기존 온-프레미스 파일 공유를 백업할 수 있습니다. SMB를 통해 Azure Files로 파일 공유를 백업하는 경우 Azure Files에서는 데이터와 함께 ACL이 유지됩니다.

## <a name="how-it-works"></a>작동 방법
Azure Files는 Azure AD Domain Services를 사용하여 도메인 가입 VM의 Azure AD 자격 증명을 사용한 Kerberos 인증을 지원합니다. Azure Files에서 Azure AD를 사용하려면 먼저 Azure AD Domain Services를 사용하도록 설정하고 파일 데이터에 액세스하려는 VM에서 도메인에 가입해야 합니다. 도메인에 가입 된 VM은 Azure AD Domain Services와 동일한 가상 네트워크 (VNET)에 있어야 합니다. 

VM에서 실행 중인 애플리케이션과 연결된 ID가 Azure Files의 데이터에 액세스하려고 시도하면 ID를 인증하도록 Azure AD Domain Services로 요청이 전송됩니다. 인증에 성공하면 Azure AD Domain Services가 Kerberos 토큰을 반환합니다. 애플리케이션에서 Kerberos 토큰이 포함된 요청을 보내면 Azure Files에서 해당 토큰을 사용하여 요청에 권한을 부여합니다. Azure Files는 토큰만 받고 Azure AD 자격 증명은 유지하지 않습니다.

![SMB를 통한 Azure AD 인증 다이어그램을 보여 주는 스크린샷](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-azure-ad-domain-service-authentication-for-smb-access"></a>SMB 액세스를 위한 Azure AD 도메인 서비스 인증을 사용 하도록 설정
2018 년 9 월 24 일 이후에 만든 신규 및 기존 저장소 계정에서 Azure Files에 대 한 Azure AD 도메인 서비스 인증을 사용할 수 있습니다. 

이 기능을 사용 하기 전에 확인 주에 대 한 Azure AD Domain Services가 배포 된 저장소 계정에 연결 된 Azure AD 테 넌 트입니다. Azure AD Domain Services를 아직 설정하지 않은 경우 [Azure Portal을 사용하여 Azure Active Directory Domain Services 사용](../../active-directory-domain-services/create-instance.md)에 제공된 단계별 지침을 따르세요.

Azure AD Domain Services 배포에는 일반적으로 10-15분이 소요됩니다. Azure AD Domain Services가 배포되면 Azure Files용 SMB를 통한 Azure AD 인증을 사용하도록 설정할 수 있습니다. 자세한 내용은 [SMB 통한 Azure Files (미리 보기)에 대 한 사용 Azure Active Directory 도메인 서비스 인증](storage-files-active-directory-enable.md)합니다. 

### <a name="configure-share-level-permissions-for-azure-files"></a>Azure Files에 대한 공유 수준 권한 구성
Azure AD 도메인 서비스 인증 하도록 설정한 후에 Azure AD id에 대 한 사용자 지정 RBAC 역할을 구성할 수 있으며 저장소 계정에 있는 모든 파일 공유에 액세스 권한을 할당할 수 있습니다.

도메인 가입 VM에서 실행 중인 애플리케이션이 Azure 파일 공유를 탑재하거나 디렉터리 또는 파일에 액세스하려고 하면 적절한 공유 수준 권한 및 NTFS 권한이 있는지 애플리케이션의 Azure AD 자격 증명을 확인합니다. 공유 수준 사용 권한을 구성 하는 방법에 대 한 내용은 [SMB (미리 보기)를 통한 인증을 사용 하도록 설정 Azure Active Directory Domain Service](storage-files-active-directory-enable.md)합니다.

### <a name="configure-directory--or-file-level-permissions-for-azure-files"></a>Azure Files에 대한 디렉터리 또는 파일 수준 권한 구성 
Azure Files는 루트 디렉터리를 포함하여 디렉터리 및 파일 수준에서 표준 NTFS 파일 권한을 적용합니다. 디렉터리 또는 파일 수준 권한 구성은 SMB를 통해서만 지원됩니다. VM에서 대상 파일 공유를 탑재하고 Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) 또는 [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) 명령을 사용하여 권한을 구성합니다. 

> [!NOTE]
> Windows 파일 탐색기를 통한 NTFS 권한 구성은 미리 보기에서 지원되지 않습니다.

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>슈퍼 사용자 권한은 저장소 계정 키 사용 
저장소 계정 키를 보유한 사용자는 슈퍼 사용자 권한으로 Azure Files에 액세스할 수 있습니다. 슈퍼 사용자 권한은 RBAC를 사용하여 공유 수준에서 구성하고 Azure AD에서 적용하는 모든 액세스 제어 제한을 초월하는 권한입니다. Azure 파일 공유를 탑재하려면 슈퍼 사용자 권한이 필요합니다. 

> [!IMPORTANT]
> 보안을 위해 가능한 한 저장소 계정 키를 공유하지 말고 Azure AD 권한을 활용하는 것이 좋습니다.

### <a name="preserve-directory-and-file-acls-for-data-import-to-azure-file-shares"></a>Azure 파일 공유로 데이터를 가져오는 경우 디렉터리 및 파일 ACL 유지
Azure Files는 이제 Azure 파일 공유에 데이터를 복사할 때 디렉터리 또는 파일 Acl을 유지 합니다. 디렉터리 또는 파일의 Acl은 Azure Files로 복사할 수 있습니다. 예를 들어 [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy)를 `/copy:s` 플래그와 함께 사용하여 데이터와 ACL을 둘 다 Azure 파일 공유로 복사할 수 있습니다. ACL 유지가 기본적으로 켜져 있으며 명시적으로 저장소 계정에서 Azure AD 도메인 서비스 인증 기능을 사용할 필요가 없습니다. 

## <a name="pricing"></a>가격
저장소 계정에 대해 SMB를 통한 Azure AD 인증을 사용하도록 설정하는 데는 서비스 요금이 추가되지 않습니다. 가격에 대한 자세한 내용은 [Azure Files 가격](https://azure.microsoft.com/pricing/details/storage/files/) 및 [Azure AD Domain Services 가격](https://azure.microsoft.com/pricing/details/active-directory-ds/) 페이지를 참조하세요.

## <a name="next-steps"></a>다음 단계
Azure Files 및 SMB를 통한 Azure AD 인증에 대한 자세한 내용은 다음 리소스를 참조하세요.

- [Azure Files 소개](storage-files-introduction.md)
- [Enable Azure Active Directory authentication over SMB for Azure Files (Preview)](storage-files-active-directory-enable.md)(Azure Files용 SMB를 통한 Azure Active Directory 인증(미리 보기) 사용)
- [FAQ](storage-files-faq.md)
