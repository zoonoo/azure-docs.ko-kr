---
title: Azure Video Analyzer를 사용한 관리 ID
description: 이 항목에서는 Azure Video Analyzer로 관리 ID를 사용하는 방법에 대해 설명합니다.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 073f9ee8d039435b4cf8c7dac59831a2870b2ce0
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114601416"
---
# <a name="managed-identity"></a>관리 ID

개발자는 일반적으로 서로 다른 서비스 간의 통신을 보호하기 위해 비밀과 자격 증명을 관리하는 데 어려움을 겪고 있습니다. Azure에서 관리 ID를 사용하면 개발자가 Azure Active Directory(Azure AD)에서 Azure 리소스에 대한 ID를 제공하고 이를 사용하여 Azure AD 토큰을 가져오는 방식으로 자격 증명을 관리할 필요가 없습니다.

Azure Video Analyzer 계정을 만들 때 Azure Storage 계정을 연결해야 합니다. Video Analyzer를 사용하여 카메라에서 라이브 비디오를 녹화하는 경우 해당 데이터는 스토리지 계정의 컨테이너에 BLOB으로 저장됩니다. 다음과 같이 관리 ID를 사용하여 Video Analyzer 계정에 스토리지 계정에 대한 적절한 액세스 권한을 부여해야 합니다.


## <a name="user-assigned-managed-identity-for-video-analyzer"></a>사용자가 할당한 Video Analyzer용 관리 ID

1. [사용자가 할당한 관리 ID](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)를 만듭니다.

1. Azure Storage 계정 만들기

   [!INCLUDE [the video analyzer account and storage account must be in the same subscription and region](./includes/note-account-storage-same-subscription.md)]

1. 위의 스토리지 계정에 대한 [Storage BLOB 데이터 기여자](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) 및 [읽기 권한자](../../role-based-access-control/built-in-roles.md#reader) 역할을 관리 ID에 추가합니다.

1. Video Analyzer 계정을 만들어 사용자 할당 관리 ID 및 스토리지 계정을 관련 속성에 대한 값으로 제공합니다.

그러면 Video Analyzer는 관리 ID를 사용하여 사용자를 대신해 스토리지 계정에 액세스할 수 있습니다.

Azure Portal을 사용하여 위의 작업을 수행하는 예제는 [이](create-video-analyzer-account.md) 문서를 참조하세요.


## <a name="next-steps"></a>다음 단계

관리 ID가 사용자 및 Azure 애플리케이션에 대해 수행할 수 있는 작업에 대한 자세한 내용은 [Azure AD 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.
