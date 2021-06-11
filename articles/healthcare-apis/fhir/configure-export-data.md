---
title: Azure API에서 FHIR에 대 한 내보내기 설정 구성
description: 이 문서에서는 Azure API for FHIR에서 내보내기 설정을 구성 하는 방법을 설명 합니다.
author: CaitlinV39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 5/11/2021
ms.author: cavoeg
ms.openlocfilehash: 936728cba33a81f65a6607ca3d46aca9621145ee
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111970310"
---
# <a name="configure-export-setting-and-set-up-the-storage-account"></a>내보내기 설정 구성 및 저장소 계정 설정

FHIR 용 azure API는 Azure API 용 Azure API에서 저장소 계정으로 데이터를 내보낼 수 있는 $export 명령을 지원 합니다.

FHIR 용 Azure API에서 내보내기를 구성 하는 데는 세 가지 단계가 있습니다.

1. Azure API에서 관리 되는 Id를 사용 하도록 설정 합니다.
2. Azure storage 계정 만들기 (이전에 수행 되지 않은 경우) 및 저장소 계정에 대 한 Azure API에 대 한 사용 권한 할당
3. Azure API에서 저장소 계정 내보내기로 FHIR의 저장소 계정을 선택 합니다.

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>Azure API에서 관리 되는 Id를 사용 하도록 설정

내보내기에 대 한 Azure API를 구성 하는 첫 번째 단계는 서비스에서 시스템 차원의 관리 id를 사용 하도록 설정 하는 것입니다. Azure의 관리 되는 id에 대 한 자세한 내용은 [azure 리소스에 대 한 관리 id](../../active-directory/managed-identities-azure-resources/overview.md)정보를 참조 하세요.

이렇게 하려면 Azure API for FHIR 서비스로 이동 하 여 **id** 를 선택 합니다. 상태를 **켜기** 로 변경 하면 Azure API for FHIR 서비스에서 관리 id를 사용할 수 있습니다.

![관리 ID 사용](media/export-data/fhir-mi-enabled.png)

이제 저장소 계정을 만들고 서비스에 사용 권한을 할당 하 여 다음 단계로 이동할 수 있습니다.

## <a name="adding-permission-to-storage-account"></a>저장소 계정에 사용 권한 추가

데이터 내보내기의 다음 단계는 FHIR 서비스에 대 한 Azure API에 저장소 계정에 쓸 수 있는 권한을 할당 하는 것입니다.

저장소 계정을 만든 후 저장소 계정의 **Access Control (IAM)** 로 이동한 후 **역할 할당 추가** 를 선택 합니다. 

Azure Portal에서 역할을 할당 하는 방법에 대 한 자세한 내용은 [Azure 기본 제공 역할](../../role-based-access-control/role-assignments-portal.md)을 참조 하세요.

여기서는 서비스 이름에 역할 [저장소 Blob 데이터 참가자](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) 를 추가 하 고 **저장** 을 선택 합니다.

![역할 할당 추가 페이지](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

이제 $export에 대 한 기본 저장소 계정으로 FHIR 용 Azure API의 저장소 계정을 선택할 준비가 되었습니다.

## <a name="selecting-the-storage-account-for-export"></a>$export에 대 한 저장소 계정 선택

마지막 단계는 Azure API 용 Azure API가 데이터를로 내보내는 데 사용할 Azure storage 계정을 할당 하는 것입니다. 이렇게 하려면 FHIR 서비스에 대 한 Azure API의 **통합** 으로 이동 하 고 저장소 계정을 선택 합니다.

![저장소 내보내기 저장소](media/export-data/fhir-export-storage.png)

이 마지막 단계를 완료 한 후에는 $export 명령을 사용 하 여 데이터를 내보낼 준비가 된 것입니다.

> [!Note]
> FHIR 용 Azure API와 동일한 구독의 저장소 계정만 $export 작업에 대 한 대상으로 등록할 수 있습니다.

데이터베이스 설정, 액세스 제어, 진단 로깅 사용 및 사용자 지정 헤더를 사용 하 여 감사 로그에 데이터를 추가 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

>[!div class="nextstepaction"]
>[추가 설정](azure-api-for-fhir-additional-settings.md)