---
title: Azure Storage에 대한 액세스 권한 부여 | Microsoft Docs
description: Azure Active Directory, 공유 키 인증 또는 공유 액세스 서명을 포함하여 Azure Storage에 대한 액세스 권한을 부여하는 다양한 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: a0717785f4f9c1c21a18d081d157a6cdc8c12f18
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371192"
---
# <a name="authorizing-access-to-azure-storage"></a>Azure Storage에 대한 액세스 권한 부여

스토리지 계정의 데이터에 액세스할 때마다 클라이언트에서 HTTP/HTTPS를 통해 Azure Storage에 요청합니다. 보안 리소스에 대한 모든 요청은 권한을 부여받아 클라이언트에서 데이터 액세스에 필요한 권한을 갖도록 해야 합니다.

다음 표에서는 리소스에 대 한 액세스 권한을 부여 하기 위해 Azure Storage 제공 하는 옵션을 설명 합니다.

|  |공유 키 (저장소 계정 키)  |공유 액세스 서명(SAS)  |Azure AD(Azure Active Directory)  |익명 공용 읽기 액세스  |
|---------|---------|---------|---------|---------|
|Azure Blob     |[지원됨](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[지원됨](storage-dotnet-shared-access-signature-part-1.md)         |[지원됨](storage-auth-aad.md)         |[지원됨](../blobs/storage-manage-access-to-resources.md)         |
|SMB (Azure Files)     |[지원됨](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |지원되지 않음         |[지원 됨, AAD 도메인 서비스에만 해당](../files/storage-files-active-directory-overview.md)         |지원되지 않음         |
|Azure Files (REST)     |[지원됨](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[지원됨](storage-dotnet-shared-access-signature-part-1.md)         |지원되지 않음         |지원되지 않음         |
|Azure 큐     |[지원됨](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[지원됨](storage-dotnet-shared-access-signature-part-1.md)         |[지원됨](storage-auth-aad.md)         |지원되지 않음         |
|Azure Tables     |[지원됨](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[지원됨](storage-dotnet-shared-access-signature-part-1.md)         |지원되지 않음         |지원되지 않음         |

각 권한 부여 옵션에 대 한 간략 한 설명은 다음과 같습니다.

- Blob 및 큐에 대 한 **Azure Active Directory (AZURE AD) 통합** . Azure AD는 저장소 계정의 리소스에 대한 클라이언트 액세스를 자세히 제어하기 위해 RBAC(역할 기반 액세스 제어)를 제공합니다. Blob 및 큐에 대 한 Azure AD 통합에 대 한 자세한 내용은 [Azure Active Directory를 사용 하 여 Azure Storage 요청 인증](storage-auth-aad.md)을 참조 하세요.

- 파일에 대 한 **DS (Azure AD Domain Services) 통합 (미리 보기)** Azure Files는 Azure AD DS를 통해 SMB (서버 메시지 블록)를 통해 id 기반 인증을 지원 합니다. 이는 저장소 계정의 리소스에 대 한 클라이언트의 액세스를 세부적으로 제어 하기 위해 RBAC를 제공 합니다. 도메인 서비스를 사용 하는 파일에 대 한 Azure AD 통합에 대 한 자세한 내용은 [SMB 액세스를 위한 AAD DS (Azure Files Azure Active Directory 도메인 서비스) 인증 지원 개요 (미리 보기)](../files/storage-files-active-directory-overview.md)를 참조 하세요.

- Blob, 파일, 큐 및 테이블에 대한 **공유 키 권한 부여**. 공유 키를 사용하는 클라이언트는 저장소 계정 액세스 키를 사용하여 서명된 모든 요청에 헤더를 전달합니다. 자세한 내용은 [공유 키를 사용하여 권한 부여](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)를 참조하세요.
- Blob, 파일, 큐 및 테이블에 대한 **공유 액세스 서명**. SAS(공유 액세스 서명)는 저장소 계정의 리소스에 대해 제한적으로 위임된 권한을 제공합니다. 서명이 유효한 시간 간격 또는 부여되는 권한에 제약 조건을 추가하면 액세스를 유연하게 관리할 수 있습니다. 자세한 내용은 [SAS(공유 액세스 서명) 사용](storage-dotnet-shared-access-signature-part-1.md)을 참조하세요.
- 컨테이너 및 Blob에 대한 **익명 공용 읽기 액세스**. 권한 부여가 필요하지 않습니다. 자세한 내용은 [컨테이너 및 Blob에 대한 익명 읽기 권한 관리](../blobs/storage-manage-access-to-resources.md)를 참조하세요.  

기본적으로 Azure Storage의 모든 리소스는 보안을 유지하며 계정 소유자만 사용할 수 있습니다. 위에서 설명한 권한 부여 전략 중 하나를 사용하여 클라이언트에서 저장소 계정의 리소스에 액세스할 수 있도록 허용할 수 있지만, 최대한 보안을 유지하고 쉽게 사용할 수 있는 경우 Azure AD를 사용하는 것이 좋습니다. 
