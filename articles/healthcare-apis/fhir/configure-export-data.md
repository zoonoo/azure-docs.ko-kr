---
title: Azure API for FHIR 내보내기 설정 구성
description: 이 문서에서는 Azure API for FHIR 내보내기 설정을 구성하는 방법을 설명합니다.
author: CaitlinV39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 5/11/2021
ms.author: cavoeg
ms.openlocfilehash: b245fcaecde13c4a751926e9b7bd639a43b36701
ms.sourcegitcommit: a434cfeee5f4ed01d6df897d01e569e213ad1e6f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111809049"
---
# <a name="configure-export-setting-and-set-up-the-storage-account"></a>내보내기 설정 구성 및 스토리지 계정 설정

Azure API for FHIR Azure API for FHIR 계정에서 스토리지 계정으로 데이터를 내보낼 수 있는 $export 명령을 지원합니다.

Azure API for FHIR 내보내기 구성에는 다음과 같은 세 가지 단계가 있습니다.

1. Azure API for FHIR 서비스에서 관리 ID를 사용하도록 설정합니다.
2. Azure Storage 계정을 만들고(이전에 수행하지 않은 경우) 스토리지 계정에 Azure API for FHIR 권한을 할당합니다.
3. Azure API for FHIR 스토리지 계정을 내보내기 계정으로 선택합니다.

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>Azure API for FHIR 관리 ID 사용

내보내기를 위한 Azure API for FHIR 구성하는 첫 번째 단계는 서비스에서 시스템 차원의 관리 ID를 사용하도록 설정하는 것입니다. Azure의 관리 ID에 대한 자세한 내용은 Azure [리소스에 대한 관리 ID 정보를 참조하세요.](../../active-directory/managed-identities-azure-resources/overview.md)

이렇게 하려면 Azure API for FHIR 서비스로 이동하여 **ID를** 선택합니다. 상태를 **켜기로** 변경하면 Azure API for FHIR 서비스에서 관리 ID를 사용할 수 있습니다.

![관리 ID 사용](media/export-data/fhir-mi-enabled.png)

이제 스토리지 계정을 만들어 다음 단계로 이동하고 서비스에 권한을 할당할 수 있습니다.

## <a name="adding-permission-to-storage-account"></a>스토리지 계정에 권한 추가

데이터 내보내기의 다음 단계는 Azure API for FHIR 서비스가 스토리지 계정에 쓸 수 있는 권한을 할당하는 것입니다.

스토리지 계정을 만든 후 스토리지 계정의 **Access Control(IAM)로** 이동한 다음, 역할 **할당 추가를** 선택합니다. 

Azure Portal 역할을 할당하는 자세한 내용은 Azure 기본 제공 역할 를 [참조하세요.](../../role-based-access-control/role-assignments-portal.md)

여기서는 서비스 이름에 Storage [Blob 데이터 기여자](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) 역할을 추가한 다음, **저장을** 선택합니다.

![역할 할당 추가 페이지](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

이제 Azure API for FHIR 스토리지 계정을 $export 기본 스토리지 계정으로 선택할 준비가 되었습니다.

## <a name="selecting-the-storage-account-for-export"></a>$export 스토리지 계정 선택

마지막 단계는 Azure API for FHIR 데이터를 내보내는 데 사용할 Azure Storage 계정을 할당하는 것입니다. 이렇게 하려면 Azure API for FHIR 서비스의 **통합으로** 이동하여 스토리지 계정을 선택합니다.

![FHIR 스토리지 내보내기](media/export-data/fhir-export-storage.png)

이 마지막 단계를 완료하면 이제 $export 명령을 사용하여 데이터를 내보낼 준비가 되었습니다.

> [!Note]
> Azure API for FHIR 경우와 동일한 구독에 있는 스토리지 계정만 $export 작업의 대상으로 등록할 수 있습니다.

데이터베이스 설정 구성, 액세스 제어, 진단 로깅 사용 및 사용자 지정 헤더를 사용하여 감사 로그에 데이터를 추가하는 방법에 대한 자세한 내용은 다음을 참조하세요.

>[!div class="nextstepaction"]
>[추가 설정](azure-api-for-fhir-additional-settings.md)
