---
title: 데이터 작업 권한 부여
titleSuffix: Azure Storage
description: Azure Active Directory, 공유 키 권한 부여 또는 SAS(공유 액세스 서명)를 포함하여 Azure 저장소에 대한 액세스를 승인하는 다양한 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 53eca8a0b9e7cc9abb8f89cd56fca5df28f2de0f
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521937"
---
# <a name="authorizing-access-to-data-in-azure-storage"></a>Azure 저장소의 데이터에 대한 액세스 권한 부여

스토리지 계정의 데이터에 액세스할 때마다 클라이언트에서 HTTP/HTTPS를 통해 Azure Storage에 요청합니다. 보안 리소스에 대한 모든 요청은 권한을 부여받아 클라이언트에서 데이터 액세스에 필요한 권한을 갖도록 해야 합니다.

다음 표에서는 Azure Storage가 리소스에 대한 액세스 권한을 부여하기 위해 제공하는 옵션에 대해 설명합니다.

|  |공유 키(저장소 계정 키)  |공유 액세스 서명(SAS)  |Azure AD(Azure Active Directory)  |활성 디렉토리(미리 보기) |익명 공개 읽기 액세스  |
|---------|---------|---------|---------|---------|---------|
|Azure Blob     |[지원됨](/rest/api/storageservices/authorize-with-shared-key/)         |[지원됨](storage-sas-overview.md)         |[지원됨](storage-auth-aad.md)         |지원되지 않음|[지원됨](../blobs/storage-manage-access-to-resources.md)         |
|Azure 파일(SMB)     |[지원됨](/rest/api/storageservices/authorize-with-shared-key/)         |지원되지 않음         |[AAD 도메인 서비스에서만 지원](../files/storage-files-active-directory-overview.md)         |[지원되는 자격 증명을 Azure AD에 동기화해야 합니다.](../files/storage-files-active-directory-overview.md)|지원되지 않음         |
|Azure 파일(REST)     |[지원됨](/rest/api/storageservices/authorize-with-shared-key/)         |[지원됨](storage-sas-overview.md)         |지원되지 않음         |지원되지 않음 |지원되지 않음         |
|Azure 큐     |[지원됨](/rest/api/storageservices/authorize-with-shared-key/)         |[지원됨](storage-sas-overview.md)         |[지원됨](storage-auth-aad.md)         |지원되지 않음 | 지원되지 않음         |
|Azure Tables     |[지원됨](/rest/api/storageservices/authorize-with-shared-key/)         |[지원됨](storage-sas-overview.md)         |지원되지 않음         |지원되지 않음| 지원되지 않음         |

각 권한 부여 옵션은 아래에 간략하게 설명되어 있습니다.

- Blob 및 큐에 대한 **Azure Active Directory(Azure AD) 통합입니다.** Azure AD는 저장소 계정의 리소스에 대한 클라이언트의 액세스를 제어하기 위한 RBAC(역할 기반 액세스 제어)를 제공합니다. Blob 및 큐에 대한 Azure AD 통합에 대한 자세한 내용은 [Azure Active Directory를 사용하여 Azure Blob 및 큐에 대한 액세스 권한 부여를](storage-auth-aad.md)참조하세요.

- Azure 파일에 대한 **Azure Active Directory 도메인 서비스(Azure AD DS) 인증입니다.** Azure 파일은 Azure AD DS를 통해 SMB(서버 메시지 블록)에 대한 ID 기반 권한 부여를 지원합니다. 저장소 계정에서 Azure Files 리소스에 대한 클라이언트의 액세스를 세분화된 제어에 RBAC를 사용할 수 있습니다. 도메인 서비스를 사용하는 Azure Files 인증에 대한 자세한 내용은 [개요를](../files/storage-files-active-directory-overview.md)참조하십시오.

- Azure 파일에 대한 **AD(활성 디렉터리) 인증(미리 보기)입니다.** Azure Files는 AD를 통해 SMB에 대한 ID 기반 권한 부여를 지원합니다. AD 도메인 서비스는 온-프레미스 컴퓨터 또는 Azure VM에서 호스팅할 수 있습니다. SMB 는 온-프레미스 또는 Azure에서 도메인 에 가입된 컴퓨터의 AD 자격 증명을 사용하여 파일에 대한 SMB 액세스를 지원합니다. RBAC를 사용하여 공유 수준 액세스 제어 및 NTFS DACL을 디렉터리/파일 수준 권한 적용에 사용할 수 있습니다. 도메인 서비스를 사용하는 Azure Files 인증에 대한 자세한 내용은 [개요를](../files/storage-files-active-directory-overview.md)참조하십시오.

- Blob, 파일, 큐 및 테이블에 대한 **공유 키 권한 부여**. 공유 키를 사용하는 클라이언트는 스토리지 계정 액세스 키를 사용하여 서명된 모든 요청에 헤더를 전달합니다. 자세한 내용은 [공유 키를 사용하여 권한 부여](/rest/api/storageservices/authorize-with-shared-key/)를 참조하세요.
- Blob, 파일, 큐 및 테이블에 대한 **공유 액세스 서명**. SAS(공유 액세스 서명)는 스토리지 계정의 리소스에 대해 제한적으로 위임된 권한을 제공합니다. 서명이 유효한 시간 간격 또는 부여되는 권한에 제약 조건을 추가하면 액세스를 유연하게 관리할 수 있습니다. 자세한 내용은 [SAS(공유 액세스 서명) 사용](storage-sas-overview.md)을 참조하세요.
- 컨테이너 및 Blob에 대한 **익명 공용 읽기 액세스**. 권한 부여가 필요하지 않습니다. 자세한 내용은 [컨테이너 및 Blob에 대한 익명 읽기 권한 관리](../blobs/storage-manage-access-to-resources.md)를 참조하세요.  

기본적으로 Azure Storage의 모든 리소스는 보안을 유지하며 계정 소유자만 사용할 수 있습니다. 위에서 설명한 권한 부여 전략 중 하나를 사용하여 클라이언트에서 스토리지 계정의 리소스에 액세스할 수 있도록 허용할 수 있지만, 최대한 보안을 유지하고 쉽게 사용할 수 있는 경우 Azure AD를 사용하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory를 사용하여 Azure Blob 및 큐에 대한 액세스 권한 부여](storage-auth-aad.md)
- [공유 키로 권한 부여](/rest/api/storageservices/authorize-with-shared-key/)
- [공유 액세스 서명(SAS)을 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](storage-sas-overview.md)
