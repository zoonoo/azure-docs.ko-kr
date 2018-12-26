---
title: Azure Storage에 대한 액세스 권한 부여 | Microsoft Docs
description: Azure Active Directory, 공유 키 인증 또는 공유 액세스 서명을 포함하여 Azure Storage에 대한 액세스 권한을 부여하는 다양한 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 05/18/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: ab4f9d6cbdbc047d688b57e906ea60aec6fff2fa
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39530487"
---
# <a name="authorizing-access-to-azure-storage"></a>Azure Storage에 대한 액세스 권한 부여

저장소 계정의 데이터에 액세스할 때마다 클라이언트에서 HTTP/HTTPS를 통해 Azure Storage에 요청합니다. 보안 리소스에 대한 모든 요청은 권한을 부여받아 클라이언트에서 데이터 액세스에 필요한 권한을 갖도록 해야 합니다. 보안 리소스에 대한 액세스 권한을 부여하기 위해 Azure Storage에서 제공하는 옵션은 다음과 같습니다.

- Blob 및 큐에 대한 **Azure AD(Azure Active Directory) 통합(미리 보기)**. Azure AD는 저장소 계정의 리소스에 대한 클라이언트 액세스를 자세히 제어하기 위해 RBAC(역할 기반 액세스 제어)를 제공합니다. 자세한 내용은 [Azure Active Directory를 사용하여 Azure Storage에 대한 요청 인증(미리 보기)](storage-auth-aad.md)을 참조하세요.
- Blob, 파일, 큐 및 테이블에 대한 **공유 키 권한 부여**. 공유 키를 사용하는 클라이언트는 저장소 계정 액세스 키를 사용하여 서명된 모든 요청에 헤더를 전달합니다. 자세한 내용은 [공유 키를 사용하여 권한 부여](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)를 참조하세요.
- Blob, 파일, 큐 및 테이블에 대한 **공유 액세스 서명**. SAS(공유 액세스 서명)는 저장소 계정의 리소스에 대해 제한적으로 위임된 권한을 제공합니다. 서명이 유효한 시간 간격 또는 부여되는 권한에 제약 조건을 추가하면 액세스를 유연하게 관리할 수 있습니다. 자세한 내용은 [SAS(공유 액세스 서명) 사용](storage-dotnet-shared-access-signature-part-1.md)을 참조하세요.
- 컨테이너 및 Blob에 대한 **익명 공용 읽기 액세스**. 권한 부여가 필요하지 않습니다. 자세한 내용은 [컨테이너 및 Blob에 대한 익명 읽기 권한 관리](../blobs/storage-manage-access-to-resources.md)를 참조하세요.  

기본적으로 Azure Storage의 모든 리소스는 보안을 유지하며 계정 소유자만 사용할 수 있습니다. 위에서 설명한 권한 부여 전략 중 하나를 사용하여 클라이언트에서 저장소 계정의 리소스에 액세스할 수 있도록 허용할 수 있지만, 최대한 보안을 유지하고 쉽게 사용할 수 있는 경우 Azure AD를 사용하는 것이 좋습니다. 



