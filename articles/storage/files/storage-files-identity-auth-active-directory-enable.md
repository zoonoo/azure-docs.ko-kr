---
title: Azure Files SMB를 통한 Active Directory 인증 사용
description: Active Directory를 통해 Azure 파일 공유에 대 한 SMB를 통해 id 기반 인증을 사용 하도록 설정 하는 방법을 알아봅니다. 그런 다음 도메인에 가입 된 Windows Vm (가상 머신)에서 AD 자격 증명을 사용 하 여 Azure 파일 공유에 액세스할 수 있습니다.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: rogarana
ms.openlocfilehash: febb796a47b9f5e78906d513c115b62b35c7c7d5
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196512"
---
# <a name="enable-on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>Azure 파일 공유를 위해 SMB를 통한 온-프레미스 Active Directory Domain Services 인증 사용

[Azure Files](storage-files-introduction.md)   에서는 두 가지 유형의 도메인 Azure Active Directory Domain Services 서비스 (Azure AD DS)와 온-프레미스 Active Directory Domain Services (AD DS) (미리 보기)를 통해 SMB (서버 메시지 블록)를 통해 id 기반 인증을 지원 합니다. 이 문서에서는 Azure 파일 공유에 대 한 인증에 Active Directory 도메인 서비스를 활용 하는 새로 도입 된 (미리 보기) 지원을 중점적으로 다룹니다. Azure 파일 공유에 대 한 GA (Azure AD DS) 인증을 사용 하려는 경우 [주제에 대 한 문서](storage-files-identity-auth-active-directory-domain-service-enable.md)를 참조 하세요.

> [!NOTE]
> Azure 파일 공유는 Azure Active Directory 도메인 서비스 (Azure AD DS) 또는 온-프레미스 Active Directory Domain Services (AD DS) 중 하나의 도메인 서비스에 대 한 인증만 지원 합니다. 
>
> Azure 파일 공유 인증에 사용 되는 AD DS id를 Azure AD에 동기화 해야 합니다. 암호 해시 동기화는 선택 사항입니다. 
> 
> 온-프레미스 AD DS 인증은 AD DS에서 만든 컴퓨터 계정에 대 한 인증을 지원 하지 않습니다. 
> 
> 온-프레미스 AD DS 인증은 저장소 계정이 등록 된 하나의 AD 포리스트에 대해서만 지원 될 수 있습니다. 기본적으로 단일 포리스트의 AD DS 자격 증명을 사용 하 여 Azure 파일 공유에 액세스할 수 있습니다. 다른 포리스트에서 Azure 파일 공유에 액세스 해야 하는 경우 적절 한 포리스트 트러스트를 구성 했는지 확인 하세요. 자세한 내용은 [FAQ](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) 를 참조 하세요.  
> 
> Azure File Sync에서 관리 하는 Azure 파일 공유에 대해 SMB 액세스 및 ACL 지 속성에 대 한 AD DS 인증이 지원 됩니다.
>
> Azure Files는 RC4-HMAC 암호화를 사용 하 여 AD에서 Kerberos 인증을 지원 합니다. AES Kerberos 암호화는 아직 지원 되지 않습니다.

SMB를 통해 Azure 파일 공유에 대 한 AD DS를 사용 하도록 설정 하면 AD DS 가입 된 컴퓨터에서 기존 AD 자격 증명을 사용 하 여 Azure 파일 공유를 탑재할 수 있습니다. 온-프레미스 컴퓨터 또는 Azure에서 호스트 되는 AD DS 환경을 사용 하 여이 기능을 사용 하도록 설정할 수 있습니다.

Azure 파일 공유에 액세스 하는 데 사용 되는 id는 [RBAC (역할 기반 액세스 제어)](../../role-based-access-control/overview.md) 모델을 통해 공유 수준 파일 사용 권한을 적용 하기 위해 azure AD에 동기화 되어야 합니다. 기존 파일 서버에서 전달 되는 파일/디렉터리의 [Windows 스타일 dacl](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) 은 유지 되 고 적용 됩니다. 이 기능은 엔터프라이즈 AD DS 환경과의 원활한 통합을 제공 합니다. 온-프레미스 파일 서버를 Azure 파일 공유로 바꾸면 기존 사용자가 사용 중인 자격 증명을 변경 하지 않고 Single Sign-On 환경을 사용 하 여 현재 클라이언트에서 Azure 파일 공유에 액세스할 수 있습니다.  

> [!NOTE]
> 몇 가지 일반적인 사용 사례에 대 한 Azure Files AD 인증을 설정 하는 데 도움이 되도록 단계별 지침을 포함 하는 [두 개의 비디오](https://docs.microsoft.com/azure/storage/files/storage-files-introduction#videos) 를 게시 했습니다.
> - 온-프레미스 파일 서버를 Azure Files로 바꾸기 (파일 및 AD 인증을 위한 개인 링크의 설정 포함)
> - Windows 가상 데스크톱에 대 한 프로필 컨테이너로 Azure Files 사용 (AD 인증 및 FsLogix 구성의 설정 포함)

## <a name="prerequisites"></a>필수 구성 요소 

Azure 파일 공유에 대 한 AD DS 인증을 사용 하도록 설정 하기 전에 다음 필수 구성 요소를 완료 했는지 확인 합니다. 

- AD DS 환경을 선택 하거나 만들고 [AZURE AD와 동기화](../../active-directory/hybrid/how-to-connect-install-roadmap.md)합니다. 

    새 온-프레미스 또는 기존 온-프레미스 AD DS 환경에서이 기능을 사용 하도록 설정할 수 있습니다. 액세스에 사용 되는 id를 Azure AD와 동기화 해야 합니다. 액세스 하는 Azure AD 테 넌 트와 파일 공유는 동일한 구독과 연결 되어 있어야 합니다. 

    AD 도메인 환경을 설정 하려면 [Active Directory Domain Services 개요](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)를 참조 하세요. AD를 Azure AD와 동기화 하지 않은 경우 Azure AD Connect의 지침을 따르고 [설치 로드맵을 Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-install-roadmap.md) 하 여 Azure AD Connect를 구성 하 고 설정 합니다. 

- 온-프레미스 컴퓨터 또는 Azure VM을 온-프레미스 AD DS에 도메인 가입 합니다. 

    컴퓨터 또는 VM에서 AD 자격 증명을 사용 하 여 파일 공유에 액세스 하려면 장치가 AD DS에 도메인에 가입 되어 있어야 합니다. 도메인에 가입 하는 방법에 대 한 자세한 내용은 [도메인에 컴퓨터 가입](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain)을 참조 하세요. 

- Azure storage 계정을 선택 하거나 만듭니다.  성능을 최적화 하려면 공유에 액세스할 예정인 VM과 동일한 지역에 저장소 계정을 배포 하는 것이 좋습니다.

    파일 공유가 포함 된 저장소 계정이 Azure AD DS 인증에 대해 아직 구성 되지 않았는지 확인 합니다. 저장소 계정에서 Azure AD DS 인증을 사용 하도록 설정한 Azure Files 경우 온-프레미스 AD DS를 사용 하도록 변경 하기 전에 사용 하지 않도록 설정 해야 합니다. 이는 Azure AD DS 환경에서 구성 된 기존 Acl을 적절 한 사용 권한을 적용 하도록 다시 구성 해야 함을 의미 합니다.
    
    새 파일 공유를 만드는 방법에 대 한 자세한 내용은 [Azure Files에서 파일 공유 만들기](storage-how-to-create-file-share.md)를 참조 하세요.

- 저장소 계정 키를 사용 하 여 Azure 파일 공유를 탑재 하 여 연결을 확인 합니다. 

    장치 및 파일 공유가 올바르게 구성 되었는지 확인 하려면 저장소 계정 키를 사용 하 여 [파일 공유를 탑재](storage-how-to-use-files-windows.md) 해 보세요. Azure Files에 연결 하는 데 문제가 발생 하는 경우 [Windows에서 Azure Files 탑재 오류에 대해 게시 한 문제 해결 도구](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5)를 참조 하세요. 또한 포트 445이 차단 될 때 시나리오를 해결 하기 위한 [지침](https://docs.microsoft.com/azure/storage/files/storage-files-faq#on-premises-access) 을 제공 합니다. 

## <a name="regional-availability"></a>국가별 가용성

AD DS (미리 보기)를 사용 하 여 Azure Files 인증은 [모든 공용 지역 및 Azure .gov 지역](https://azure.microsoft.com/global-infrastructure/locations/)에서 사용할 수 있습니다.

## <a name="overview"></a>개요

파일 공유에서 네트워킹 구성을 사용 하도록 설정 하려는 경우 AD DS 인증을 사용 하도록 설정 하기 전에 먼저 [네트워킹 고려](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview) 사항을 평가 하 고 관련 구성을 완료 하는 것이 좋습니다.

Azure 파일 공유에 대 한 AD DS 인증을 사용 하도록 설정 하면 온-프레미스 AD DS 자격 증명을 사용 하 여 Azure 파일 공유에 인증할 수 있습니다. 또한 세부적인 액세스 제어를 허용 하기 위해 사용 권한을 보다 잘 관리할 수 있습니다. 이렇게 하려면 AD connect를 사용 하 여 온-프레미스 AD DS에서 Azure AD로 id를 동기화 해야 합니다. 온-프레미스 AD DS 자격 증명을 사용 하 여 파일/공유 수준 액세스를 관리 하는 동안 Azure AD에 동기화 된 id로 공유 수준 액세스를 제어 합니다.

다음으로 AD DS 인증에 대 한 Azure Files를 설정 하려면 다음 단계를 수행 합니다. 

1. [저장소 계정에 대 한 Azure Files AD DS 인증 사용](#1-enable-ad-ds-authentication-for-your-account)

1. [대상 AD id와 동기화 되는 Azure AD id (사용자, 그룹 또는 서비스 주체)에 공유에 대 한 액세스 권한을 할당 합니다.](#2-assign-access-permissions-to-an-identity)

1. [디렉터리 및 파일에 대해 SMB를 통한 Acl 구성](#3-configure-ntfs-permissions-over-smb)
 
1. [AD DS에 연결 된 VM에 Azure 파일 공유를 탑재 합니다.](#4-mount-a-file-share-from-a-domain-joined-vm)

1. [AD DS에서 저장소 계정 id의 암호를 업데이트 합니다.](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)

다음 다이어그램에서는 Azure 파일 공유에 대해 SMB를 통한 Azure AD 인증을 사용 하도록 설정 하는 종단 간 워크플로를 보여 줍니다. 

![파일 광고 워크플로 다이어그램](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> Azure 파일 공유에 대 한 SMB를 통한 AD DS 인증은 Windows 7 또는 Windows Server 2008 r 2 보다 최신 버전의 OS에서 실행 되는 컴퓨터 또는 Vm 에서만 지원 됩니다. 

## <a name="1-enable-ad-ds-authentication-for-your-account"></a>1 계정에 대 한 AD DS 인증 사용 

Azure 파일 공유에 대해 SMB를 통한 AD DS 인증을 사용 하도록 설정 하려면 먼저 AD DS에 저장소 계정을 등록 한 후 저장소 계정에 필요한 도메인 속성을 설정 해야 합니다. 저장소 계정에서이 기능을 사용 하도록 설정 하면 계정에 있는 모든 신규 및 기존 파일 공유에 적용 됩니다. AzFilesHybrid Powershell 모듈을 다운로드 하 고 `join-AzStorageAccountForAuth` 를 사용 하 여 기능을 사용 하도록 설정 합니다. 이 섹션 내의 스크립트에서 종단 간 워크플로에 대 한 자세한 설명을 찾을 수 있습니다. 

> [!IMPORTANT]
> `Join-AzStorageAccountForAuth`이 cmdlet은 AD 환경을 수정 합니다. 다음 설명을 참조 하 여 명령을 실행할 수 있는 적절 한 권한이 있고 적용 된 변경 내용이 준수 및 보안 정책과 일치 하는지 확인 하기 위해 수행 하는 작업을 더 잘 이해 합니다. 

`Join-AzStorageAccountForAuth`이 cmdlet은 표시 된 저장소 계정을 대신 하 여 오프 라인 도메인 조인과 동일한 기능을 수행 합니다. 이 스크립트는 cmdlet을 사용 하 여 AD 도메인에서 [컴퓨터 계정](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (기본값) 또는 [서비스 로그온 계정](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts)에 계정을 만듭니다. 수동으로이 작업을 수행 하도록 선택 하는 경우 사용자 환경에 가장 적합 한 계정을 선택 해야 합니다.

Cmdlet에서 만든 AD DS 계정은 AD 도메인의 저장소 계정을 나타냅니다. 암호 만료를 적용 하는 OU (조직 구성 단위)에서 AD DS 계정을 만든 경우 최대 암호 사용 기간 전에 암호를 업데이트 해야 합니다. 계정 암호를 업데이트 하지 못하면 Azure 파일 공유에 액세스할 때 인증 오류가 발생 합니다. 암호를 업데이트 하는 방법을 알아보려면 [업데이트 AD DS 계정 암호](#5-update-the-password-of-your-storage-account-identity-in-ad-ds)를 참조 하세요.

다음 스크립트를 사용 하 여 등록을 수행 하 고 기능을 사용 하도록 설정 하거나 스크립트에서 수행 하는 작업을 수동으로 수행할 수 있습니다. 이러한 작업은 스크립트 다음에 나오는 섹션에 설명 되어 있습니다. 두 작업을 모두 수행할 필요는 없습니다.

### <a name="11-script-prerequisites"></a>1.1 스크립트 필수 조건
- [AzFilesHybrid 모듈 다운로드 및 압축 풀기](https://github.com/Azure-Samples/azure-files-samples/releases)
- 대상 AD에서 서비스 로그온 계정 또는 컴퓨터 계정을 만들 수 있는 권한이 있는 AD DS 자격 증명을 사용 하 여 온-프레미스 AD DS에 도메인에 가입 된 장치에 모듈을 설치 하 고 실행 합니다.
-  Azure AD에 동기화 된 온-프레미스 AD DS 자격 증명을 사용 하 여 스크립트를 실행 합니다. 온-프레미스 AD DS 자격 증명에는 저장소 계정 소유자 또는 참가자 RBAC 역할 권한이 있어야 합니다.
- 저장소 계정이 [지원 되는 지역](#regional-availability)에 있는지 확인 하세요.

### <a name="12-domain-join-your-storage-account"></a>1.2 도메인 가입 저장소 계정
PowerShell에서 실행 하기 전에 아래 매개 변수에서 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.
> [!IMPORTANT]
> 도메인 가입 cmdlet은 ad의 저장소 계정 (파일 공유)을 나타내는 AD 계정을 만듭니다. 컴퓨터 계정 또는 서비스 로그온 계정으로 등록 하도록 선택할 수 있습니다. 자세한 내용은 [FAQ](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) 를 참조 하십시오. 컴퓨터 계정의 경우 AD에 30 일 동안 기본 암호 만료 기간이 설정 되어 있습니다. 마찬가지로, 서비스 로그온 계정에는 AD 도메인 또는 OU (조직 구성 단위)에 대 한 기본 암호 만료 기간이 설정 되어 있을 수 있습니다.
> 두 계정 유형 모두에서 AD 환경에 구성 된 암호 만료 기간을 확인 하 고 최대 암호 사용 기간 이전에 AD 계정의 [ad에서 저장소 계정 id의 암호를 업데이트](#5-update-the-password-of-your-storage-account-identity-in-ad-ds) 하도록 계획 하는 것이 좋습니다. [Ad에서 새 AD OU (조직 구성 단위)를 만들고](https://docs.microsoft.com/powershell/module/addsadministration/new-adorganizationalunit?view=win10-ps) 이에 따라 [컴퓨터 계정](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)?redirectedfrom=MSDN) 또는 서비스 로그온 계정에 대 한 암호 만료 정책을 사용 하지 않도록 설정할 수 있습니다. 

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Define parameters
$SubscriptionId = "<your-subscription-id-here>"
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId $SubscriptionId 

# Register the target storage account with your active directory environment under the target OU (for example: specify the OU with Name as "UserAccounts" or DistinguishedName as "OU=UserAccounts,DC=CONTOSO,DC=COM"). 
# You can use to this PowerShell cmdlet: Get-ADOrganizationalUnit to find the Name and DistinguishedName of your target OU. If you are using the OU Name, specify it with -OrganizationalUnitName as shown below. If you are using the OU DistinguishedName, you can set it with -OrganizationalUnitDistinguishedName. You can choose to provide one of the two names to specify the target OU.
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account (default parameter value), depends on the AD permission you have and preference. 
# You can run Get-Help Join-AzStorageAccountForAuth to find more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -Name $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" `
        -OrganizationalUnitName "<ou-name-here>" #You can also use -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" instead. If you don't provide the OU name as an input parameter, the AD identity that represents the storage account will be created under the root directory.

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, go to Azure Files FAQ.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose

```

다음 설명에는 cmdlet이 실행 될 때 수행 되는 모든 작업이 요약 되어 `Join-AzStorageAccountForAuth` 있습니다. 명령을 사용 하지 않으려는 경우 다음 단계를 수동으로 수행할 수 있습니다.

> [!NOTE]
> 위의 스크립트를 이미 실행 한 경우 `Join-AzStorageAccountForAuth` 다음 섹션인 "1.3 확인 기능이 활성화 되어 있는지 확인 하십시오."로 이동 합니다. 아래 작업을 다시 수행 하지 않아도 됩니다.

#### <a name="a-checking-environment"></a>a. 환경 확인

먼저 스크립트가 사용자 환경을 확인 합니다. 특히 [Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) 이 설치 되어 있는지와 셸이 관리자 권한으로 실행 되 고 있는지 확인 합니다. 그런 다음 [Az. Storage 1.11.1-preview 모듈이](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) 설치 되어 있는지 확인 하 고 그렇지 않으면 설치 합니다. 이러한 검사를 통과 하면 AD DS를 확인 하 여 이미 SPN/UPN을 사용 하 여 생성 된 [컴퓨터 계정](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (기본값) 또는 [서비스 로그온 계정이](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) "cifs/your-name-w o w s. w o w s. w o w s. w o w s. w o w s. w o w s. w o w s. w i n. 계정이 존재 하지 않는 경우 아래 섹션 b의 설명에 따라 계정을 만듭니다.

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. 수동으로 AD의 저장소 계정을 나타내는 id 만들기

이 계정을 수동으로 만들려면를 사용 하 여 저장소 계정에 대 한 새 Kerberos 키를 만듭니다 `New-AzStorageAccountKey -KeyName kerb1` . 그런 다음 해당 Kerberos 키를 계정의 암호로 사용 합니다. 이 키는 설정 하는 동안에만 사용 되며 저장소 계정에 대 한 모든 컨트롤이 나 데이터 평면 작업에는 사용할 수 없습니다.

해당 키가 있으면 OU에서 서비스 또는 컴퓨터 계정을 만듭니다. 저장소 계정에 대 한 SPN: "cifs/저장소-이름-파일-이름-w i n. w i n.

OU에서 암호 만료를 적용 하는 경우 Azure 파일 공유에 액세스할 때 인증 오류가 발생 하지 않도록 최대 암호 사용 기간 전에 암호를 업데이트 해야 합니다. 자세한 내용은 [AD DS에서 저장소 계정 id의 암호 업데이트](#5-update-the-password-of-your-storage-account-identity-in-ad-ds) 를 참조 하세요.

새로 만든 계정의 SID를 유지 하 고 다음 단계에 필요 합니다. 저장소 계정을 나타내는 id를 Azure AD에 동기화 할 필요가 없습니다.

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. 저장소 계정에서 기능을 사용 하도록 설정

그런 다음 스크립트는 저장소 계정에서이 기능을 사용 하도록 설정 합니다. 이 설치를 수동으로 수행 하려면 다음 명령에서 도메인 속성에 대 한 구성 세부 정보를 제공 하 고 실행 합니다. 다음 명령에 필요한 저장소 계정 SID는 [이전 섹션](#b-creating-an-identity-representing-the-storage-account-in-your-ad-manually)의 AD DS에서 만든 ID의 sid입니다.

```PowerShell
# Set the feature flag on the target storage account and provide the required AD domain information
Set-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>" `
        -EnableActiveDirectoryDomainServicesForFile $true `
        -ActiveDirectoryDomainName "<your-domain-name-here>" `
        -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" `
        -ActiveDirectoryForestName "<your-forest-name-here>" `
        -ActiveDirectoryDomainGuid "<your-guid-here>" `
        -ActiveDirectoryDomainsid "<your-domain-sid-here>" `
        -ActiveDirectoryAzureStorageSid "<your-storage-account-sid>"
```


### <a name="13-confirm-that-the-feature-is-enabled"></a>1.3 기능이 사용 하도록 설정 되었는지 확인

저장소 계정에서 기능을 사용할 수 있는지 여부를 확인 하려면 다음 스크립트를 사용할 수 있습니다.

```PowerShell
# Get the target storage account
$storageaccount = Get-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>"

# List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

# List the directory domain information if the storage account has enabled AD DS authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

이제 저장소 계정에서이 기능을 사용 하도록 설정 했습니다. 이 기능을 사용 하도록 설정 했으므로 기능을 사용 하기 위해 수행 해야 하는 추가 단계가 있습니다.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

이제 SMB를 통해 AD DS 인증을 사용 하도록 설정 하 고 AD DS id를 사용 하 여 Azure 파일 공유에 대 한 액세스를 제공 하는 사용자 지정 역할을 할당 했습니다. 추가 사용자에 게 파일 공유에 대 한 액세스 권한을 부여 하려면 [액세스 권한 할당](#2-assign-access-permissions-to-an-identity) 을 사용 하 여 id를 사용 하 고 [SMB를 통한 NTFS 사용 권한 구성](#3-configure-ntfs-permissions-over-smb) 섹션의 지침을 따르세요.

## <a name="5-update-the-password-of-your-storage-account-identity-in-ad-ds"></a>5 AD DS에서 저장소 계정 id의 암호를 업데이트 합니다.

암호 만료 시간을 적용 하는 OU에서 저장소 계정을 나타내는 AD DS identity/account를 등록 한 경우 최대 암호 사용 기간 보다 먼저 암호를 회전 해야 합니다. AD DS 계정의 암호를 업데이트 하지 못하면 인증 오류가 발생 하 여 Azure 파일 공유에 액세스 하지 못합니다.  

암호 회전을 트리거하려면 `Update-AzStorageAccountADObjectPassword` AzFilesHybrid 모듈에서 명령을 실행할 수 있습니다. Cmdlet은 스토리지 계정 키 회전과 유사한 작업을 수행합니다. 저장소 계정의 두 번째 Kerberos 키를 가져온 다음이 키를 사용 하 여 AD DS에서 등록 된 계정의 암호를 업데이트 합니다. 그런 다음 저장소 계정의 대상 Kerberos 키를 다시 생성 하 고 AD DS에서 등록 된 계정의 암호를 업데이트 합니다. 온-프레미스 AD DS 도메인 가입 환경에서이 cmdlet을 실행 해야 합니다.

```PowerShell
# Update the password of the AD DS account registered for the storage account
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="next-steps"></a>다음 단계

Azure Files 및 SMB에서 AD를 사용 하는 방법에 대 한 자세한 내용은 다음 리소스를 참조 하세요.

- [SMB 액세스를 위한 Azure Files id 기반 인증 지원 개요](storage-files-active-directory-overview.md)
- [FAQ](storage-files-faq.md)
