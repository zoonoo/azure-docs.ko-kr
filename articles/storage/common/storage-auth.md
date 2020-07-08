---
title: 데이터 작업 권한 부여
titleSuffix: Azure Storage
description: Azure Active Directory, 공유 키 인증 또는 SAS (공유 액세스 서명)를 포함 하 여 Azure Storage에 대 한 액세스 권한을 부여 하는 다양 한 방법에 대해 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 1db26e6e1308299325e15b4c856fc2ebbaed8326
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84807604"
---
# <a name="authorizing-access-to-data-in-azure-storage"></a>Azure Storage 데이터에 대 한 액세스 권한 부여

스토리지 계정의 데이터에 액세스할 때마다 클라이언트에서 HTTP/HTTPS를 통해 Azure Storage에 요청합니다. 보안 리소스에 대한 모든 요청은 권한을 부여받아 클라이언트에서 데이터 액세스에 필요한 권한을 갖도록 해야 합니다.

다음 표에서는 리소스에 대 한 액세스 권한을 부여 하기 위해 Azure Storage 제공 하는 옵션을 설명 합니다.

|  |공유 키 (저장소 계정 키)  |공유 액세스 서명(SAS)  |Azure AD(Azure Active Directory)  |온-프레미스 Active Directory Domain Services (미리 보기) |익명 공용 읽기 액세스  |
|---------|---------|---------|---------|---------|---------|
|Azure Blob     |[지원됨](/rest/api/storageservices/authorize-with-shared-key/)         |[지원됨](storage-sas-overview.md)         |[지원됨](storage-auth-aad.md)         |지원되지 않음|[지원됨](../blobs/storage-manage-access-to-resources.md)         |
|SMB (Azure Files)     |[지원됨](/rest/api/storageservices/authorize-with-shared-key/)         |지원되지 않음         |[지원 됨, AAD 도메인 서비스에만 해당](../files/storage-files-active-directory-overview.md)         |[지원 됨, 자격 증명을 Azure AD에 동기화 해야 합니다.](../files/storage-files-active-directory-overview.md)|지원되지 않음         |
|Azure Files (REST)     |[지원됨](/rest/api/storageservices/authorize-with-shared-key/)         |[지원됨](storage-sas-overview.md)         |지원되지 않음         |지원되지 않음 |지원되지 않음         |
|Azure 큐     |[지원됨](/rest/api/storageservices/authorize-with-shared-key/)         |[지원됨](storage-sas-overview.md)         |[지원됨](storage-auth-aad.md)         |지원되지 않음 | 지원되지 않음         |
|Azure Tables     |[지원됨](/rest/api/storageservices/authorize-with-shared-key/)         |[지원됨](storage-sas-overview.md)         |지원되지 않음         |지원되지 않음| 지원되지 않음         |

각 권한 부여 옵션에 대 한 간략 한 설명은 다음과 같습니다.

- Blob 및 큐에 대 한 **Azure Active Directory (AZURE AD) 통합** . Azure AD는 저장소 계정의 리소스에 대 한 클라이언트 액세스를 제어 하기 위해 RBAC (역할 기반 액세스 제어)를 제공 합니다. Blob 및 큐에 대 한 Azure AD 통합에 대 한 자세한 내용은 [Azure Active Directory를 사용 하 여 azure blob 및 큐에 대 한 액세스 권한 부여](storage-auth-aad.md)를 참조 하세요.

- Azure Files에 대 한 **Azure Active Directory Domain Services (Azure AD DS) 인증** . Azure Files는 Azure AD DS를 통해 SMB (서버 메시지 블록)를 통해 id 기반 권한 부여를 지원 합니다. 클라이언트의 액세스에 대 한 세분화 된 제어를 위해 RBAC를 사용 하 여 저장소 계정에서 리소스를 Azure Files 수 있습니다. 도메인 서비스를 사용 하는 Azure Files 인증에 대 한 자세한 내용은 [개요](../files/storage-files-active-directory-overview.md)를 참조 하세요.

- Azure Files에 대 한 **온-프레미스 Active Directory Domain Services (AD DS 또는 온-프레미스 AD DS) 인증 (미리 보기)** Azure Files는 AD DS를 통해 SMB를 통한 id 기반 권한 부여를 지원 합니다. AD DS 환경은 온-프레미스 컴퓨터 또는 Azure Vm에서 호스팅될 수 있습니다. 파일에 대 한 SMB 액세스는 온-프레미스 또는 Azure에서 도메인에 가입 된 컴퓨터의 AD DS 자격 증명을 사용 하 여 지원 됩니다. 디렉터리/파일 수준 사용 권한 적용에 대 한 공유 수준 액세스 제어 및 NTFS Dacl의 조합을 사용할 수 있습니다. 도메인 서비스를 사용 하는 Azure Files 인증에 대 한 자세한 내용은 [개요](../files/storage-files-active-directory-overview.md)를 참조 하세요.

- Blob, 파일, 큐 및 테이블에 대한 **공유 키 권한 부여**. 공유 키를 사용하는 클라이언트는 스토리지 계정 액세스 키를 사용하여 서명된 모든 요청에 헤더를 전달합니다. 자세한 내용은 [공유 키를 사용하여 권한 부여](/rest/api/storageservices/authorize-with-shared-key/)를 참조하세요.
- Blob, 파일, 큐 및 테이블에 대한 **공유 액세스 서명**. SAS(공유 액세스 서명)는 스토리지 계정의 리소스에 대해 제한적으로 위임된 권한을 제공합니다. 서명이 유효한 시간 간격 또는 부여되는 권한에 제약 조건을 추가하면 액세스를 유연하게 관리할 수 있습니다. 자세한 내용은 [SAS(공유 액세스 서명) 사용](storage-sas-overview.md)을 참조하세요.
- 컨테이너 및 Blob에 대한 **익명 공용 읽기 액세스**. 권한 부여가 필요하지 않습니다. 자세한 내용은 [컨테이너 및 Blob에 대한 익명 읽기 권한 관리](../blobs/storage-manage-access-to-resources.md)를 참조하세요.  

기본적으로 Azure Storage의 모든 리소스는 보안을 유지하며 계정 소유자만 사용할 수 있습니다. 위에서 설명한 권한 부여 전략 중 하나를 사용하여 클라이언트에서 스토리지 계정의 리소스에 액세스할 수 있도록 허용할 수 있지만, 최대한 보안을 유지하고 쉽게 사용할 수 있는 경우 Azure AD를 사용하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory를 사용 하 여 Azure blob 및 큐에 대 한 액세스 권한 부여](storage-auth-aad.md)
- [공유 키로 권한 부여](/rest/api/storageservices/authorize-with-shared-key/)
- [SAS (공유 액세스 서명)를 사용 하 여 Azure Storage 리소스에 대 한 제한 된 액세스 권한 부여](storage-sas-overview.md)
