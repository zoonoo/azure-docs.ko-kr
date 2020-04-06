---
title: Azure AD 도메인 서비스를 사용하여 SMB를 통해 파일 데이터에 대한 액세스 권한 부여
description: Azure Active Directory 도메인 서비스를 통해 Azure 파일에 대한 SMB(서버 메시지 블록)를 통해 ID 기반 인증을 사용하도록 설정하는 방법을 알아봅니다. 그런 다음 도메인에 가입한 Windows 가상 시스템(VM)은 Azure AD 자격 증명을 사용하여 Azure 파일 공유에 액세스할 수 있습니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cb173bcbf7cd163dca16c211d45018e0fe056edd
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666845"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>Azure 파일에서 Azure Active Directory 도메인 서비스 인증 사용

[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Azure 파일 공유에 대한 SMB에 대한 Azure AD 인증에 대한 개요는 [Azure 파일에 대한 SMB에 대한 Azure Active Directory 인증 개요를](storage-files-active-directory-overview.md)참조하십시오. 이 문서에서는 Azure 파일에서 Azure Active Directory 도메인 서비스(Azure AD DS)를 사용하여 인증을 활성화하는 방법에 중점을 두습니다.

> [!NOTE]
> Azure 파일은 RC4-HMAC 암호화를 사용한 Azure AD DS를 통해 Kerberos 인증을 지원합니다. AES Kerberos 암호화는 아직 지원되지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure 파일 공유에 대해 SMB를 통해 Azure AD를 사용하도록 설정하기 전에 다음 필수 구성 조건을 완료했는지 확인합니다.

1.  **Azure AD 테넌트를 선택하거나 만듭니다.**

    SMB를 통한 Azure AD 인증에는 새 테넌트나 기존 테넌트를 사용할 수 있습니다. 액세스하려는 파일 공유와 테넌트는 같은 구독과 연결되어 있어야 합니다.

    새 Azure AD 테넌트를 만들려는 경우 [Azure AD 테넌트 및 Azure AD 구독을 추가](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription)하면 됩니다. 기존 Azure AD 테넌트가 있지만 Azure 파일 공유에 사용할 새 테넌트를 만들려는 경우 [Azure Active Directory 테넌트 만들기](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant)를 참조하십시오.

1.  **Azure AD 테넌트에서 Azure AD Domain Services를 사용하도록 설정합니다.**

    Azure AD 자격 증명을 사용한 인증을 지원하려면 Azure AD 테넌트에 대해 Azure AD Domain Services를 사용하도록 설정해야 합니다. Azure AD 테넌트의 관리자가 아니라면 관리자에게 문의하고 [Azure Portal을 사용하여 Azure Active Directory Domain Services 활성화](../../active-directory-domain-services/tutorial-create-instance.md)를 위한 단계별 지침을 따르세요.

    일반적으로 Azure AD DS 배포를 완료하는 데 약 15분이 걸립니다. 다음 단계로 진행하기 전에 암호 해시 동기화를 사용하도록 설정한 상태에서 Azure AD DS의 상태 **실행**이 있는지 확인합니다.

1.  **Azure AD DS를 통해 Azure VM을 도메인에 조인합니다.**

    VM에서 Azure AD 자격 증명을 사용하여 파일 공유에 액세스하려면 VM이 Azure AD DS에 도메인으로 조인되어야 합니다. VM을 도메인 조인하는 방법에 대한 자세한 내용은 [Windows Server 가상 머신을 관리되는 도메인에 조인](../../active-directory-domain-services/join-windows-vm.md)을 참조하세요.

    > [!NOTE]
    > Azure 파일 공유를 가진 SMB를 통해 Azure AD DS 인증은 Windows 7 또는 Windows Server 2008 R2 위의 OS 버전에서 실행되는 Azure VM에서만 지원됩니다.

1.  **Azure 파일 공유를 선택하거나 만듭니다.**

    Azure AD 테넌트와 같은 구독에 연결된 신규 또는 기존 파일 공유를 선택합니다. 새 파일 공유를 만드는 방법에 대한 자세한 내용은 [Azure Files에 파일 공유 만들기](storage-how-to-create-file-share.md)를 참조하세요.
    최적의 성능을 위해 파일 공유가 공유에 액세스하려는 VM과 동일한 지역에 있는 것이 좋습니다.

1.  **스토리지 계정 키를 사용하여 Azure 파일 공유를 탑재해 Azure Files 연결을 확인합니다.**

    VM 및 파일 공유가 적절하게 구성되었는지 확인하려면 스토리지 계정 키를 사용하여 파일 공유를 탑재해 봅니다. 자세한 내용은 [Azure 파일 공유를 탑재하고 Windows에서 공유에 액세스](storage-how-to-use-files-windows.md)를 참조하세요.

## <a name="overview-of-the-workflow"></a>워크플로의 개요

Azure 파일 공유에 대해 SMB를 통해 Azure AD DS 인증을 사용하도록 설정하기 전에 Azure AD 및 Azure 저장소 환경이 제대로 구성되었는지 확인합니다. 필요한 모든 단계를 완료했는지 확인하기 위해 [필수 구성 조건을](#prerequisites) 안내하는 것이 좋습니다.

다음으로 Azure AD 자격 증명을 사용하여 Azure 파일 리소스에 대한 액세스 권한을 부여하려면 다음 작업을 수행합니다.

1. 저장소 계정에 대해 SMB를 통해 Azure AD DS 인증을 사용하도록 설정하여 연결된 Azure AD DS 배포에 저장소 계정을 등록합니다.
2. Azure AD ID(사용자, 그룹 또는 서비스 주체)에 공유 액세스 권한을 할당합니다.
3. 디렉터리와 파일에 대해 SMB를 통한 NTFS 권한을 구성합니다.
4. 도메인 조인 VM에서 Azure 파일 공유를 탑재합니다.

다음 다이어그램은 Azure 파일에 대한 SMB에 대한 Azure AD DS 인증을 사용하도록 설정하기 위한 종단 간 워크플로를 보여 줍니다.

![Azure Files용 SMB를 통한 Azure AD 워크플로를 보여 주는 다이어그램](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="1-enable-azure-ad-ds-authentication-for-your-account"></a>1. 계정에 대한 Azure AD DS 인증 사용

Azure 파일에 대한 SMB를 통해 Azure AD DS 인증을 사용하려면 Azure 포털, Azure PowerShell 또는 Azure CLI를 사용하여 저장소 계정에 속성을 설정할 수 있습니다. 이 속성을 암시적으로 "도메인 조인"으로 설정하여 연결된 Azure AD DS 배포와 함께 저장소 계정을 설정합니다. 그러면 SMB에 대한 Azure AD DS 인증이 저장소 계정의 모든 새 파일 및 기존 파일 공유에 대해 활성화됩니다.

Azure AD DS를 Azure AD 테넌트에 성공적으로 배포한 경우에만 SMB를 통해 Azure AD DS 인증을 활성화할 수 있습니다. 자세한 내용은 필수 [구성 조건을](#prerequisites)참조하십시오.

### <a name="azure-portal"></a>Azure portal

[Azure 포털을](https://portal.azure.com)사용하여 SMB를 통해 Azure AD DS 인증을 사용하려면 다음 단계를 따르십시오.

1. Azure 포털에서 기존 저장소 계정으로 이동하거나 [저장소 계정을 만듭니다.](../common/storage-account-create.md)
1. **설정** 섹션에서 **구성**을 선택합니다.
1. **파일 공유에 대한 ID 기반 액세스에서** **Azure Active Directory 도메인 서비스(AAD DS)의** 토글을 **사용 권한 있는**으로 전환합니다.
1. **저장**을 선택합니다.

다음 이미지는 저장소 계정에 대해 SMB를 통해 Azure AD DS 인증을 사용하도록 설정하는 방법을 보여 줍니다.

![Azure 포털에서 SMB를 통해 Azure AD DS 인증 사용](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

### <a name="powershell"></a>PowerShell  

Azure PowerShell을 사용하여 SMB를 통해 Azure AD DS 인증을 사용하려면 최신 Az 모듈(2.4 이상) 또는 Az.Storage 모듈(1.5 이상)을 설치합니다. PowerShell 설치에 대한 자세한 내용은 [PowerShellGet을 통해 Windows에서 Azure PowerShell 설치를](https://docs.microsoft.com/powershell/azure/install-Az-ps)참조하십시오.

새 저장소 계정을 만들려면 [New-AzStorageAccount를](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0)호출한 다음 **EnableAzureActiveDirectoryDomainServicesForFile** 매개 변수를 **true로**설정합니다. 다음 예제에서는 자리 표시자 값을 고유한 값으로 바꿔야 합니다. (이전 미리 보기 모듈을 사용 하는 경우 기능 인에이블먼트에 대 한 매개 변수는 **EnableAzureFilesAadIntegrationForSMB입니다.)**

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

기존 저장소 계정에서 이 기능을 사용하려면 다음 명령을 사용합니다.

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>Azure CLI

Azure CLI를 사용하여 SMB를 통해 Azure AD 인증을 사용하려면 최신 CLI 버전(버전 2.0.70 이상)을 설치합니다. Azure CLI 설치에 대한 자세한 내용은 [Azure CLI 설치를](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)참조하십시오.

새 저장소 계정을 만들려면[az 저장소 계정 create를](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)호출하고 `--enable-files-aadds` 속성을 **true로**설정합니다. 다음 예제에서는 자리 표시자 값을 고유한 값으로 바꿔야 합니다. (이전 미리 보기 모듈을 사용 하는 경우 피처 활성화에 대 한 매개 변수는 **파일 aad입니다.)**

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

기존 저장소 계정에서 이 기능을 사용하려면 다음 명령을 사용합니다.

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

이제 SMB를 통해 Azure AD DS 인증을 성공적으로 활성화하고 Azure AD ID를 사용하여 Azure 파일 공유에 대한 액세스를 제공하는 사용자 지정 역할을 할당했습니다. 추가 사용자에게 파일 공유에 대한 액세스 권한을 부여하려면 SMB 섹션을 통해 ID를 사용하고 [NTFS 권한을 구성할](#3-configure-ntfs-permissions-over-smb)수 있는 [액세스 권한 할당의](#2-assign-access-permissions-to-an-identity) 지침을 따릅니다.

## <a name="next-steps"></a>다음 단계

Azure 파일 및 SMB를 통해 Azure AD를 사용하는 방법에 대한 자세한 내용은 다음 리소스를 참조하십시오.

- [SMB 액세스에 대한 Azure 파일 ID 기반 인증 지원 개요](storage-files-active-directory-overview.md)
- [FAQ](storage-files-faq.md)
