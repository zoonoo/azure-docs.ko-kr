---
title: 관리 되는 id를 사용 하 여 Azure Stream Analytics 작업에서 이벤트 허브에 액세스 (미리 보기)
description: 이 문서에서는 관리 되는 id를 사용 하 여 Azure Event Hubs 입력 및 출력에 대 한 Azure Stream Analytics 작업을 인증 하는 방법을 설명 합니다.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/04/2021
ms.openlocfilehash: 4dc718f21f2ef3beabc31821bd60b571ac07be05
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98018754"
---
# <a name="use-managed-identities-to-access-event-hubfrom-an-azure-stream-analytics-job-preview"></a>관리 되는 id를 사용 하 여 Azure Stream Analytics 작업에서 이벤트 허브에 액세스 (미리 보기)

Azure Stream Analytics는 Azure Event Hubs 입력 및 출력 모두에 대해 관리 Id 인증을 지원 합니다. 관리 되는 id는 90 일 마다 발생 하는 암호 변경 또는 사용자 토큰 만료로 인해 다시 인증 해야 하는 것과 같은 사용자 기반 인증 방법의 제한을 제거 합니다. 수동으로 인증할 필요가 없어지면 Stream Analytics 배포를 완전히 자동화할 수 있습니다.  

관리 ID는 지정된 Stream Analytics 작업을 나타내는 Azure Active Directory에 등록된 관리형 애플리케이션입니다. 관리 되는 응용 프로그램은 방화벽이 나 VNet (가상 네트워크) 뒤에 있는 Event Hubs를 포함 하 여 대상 리소스에 인증 하는 데 사용 됩니다. 방화벽을 우회 하는 방법에 대 한 자세한 내용은 [개인 끝점을 통해 Azure Event Hubs 네임 스페이스에 대 한 액세스 허용](../event-hubs/private-link-service.md#trusted-microsoft-services)을 참조 하세요.

이 문서에서는 Azure Portal를 통해 Stream Analytics 작업의 Event Hubs 입력 또는 출력에 대해 관리 되는 Id를 사용 하도록 설정 하는 방법을 보여 줍니다.관리 Id를 사용 하도록 설정 하기 전에 먼저 Stream Analytics 작업 및 이벤트 허브 리소스를 사용 해야 합니다.

### <a name="limitation"></a>제한 사항
미리 보기가 제공 되는 동안 관리 되는 Id 인증 모드를 사용 하는 경우 Azure Portal Event Hubs의 샘플링 입력이 작동 하지 않습니다.

## <a name="create-a-managedidentity"></a>관리 id 만들기  

먼저 Azure Stream Analytics 작업의 관리 ID를 만듭니다.  

1. Azure Portal에서 Azure Stream Analytics 작업을 엽니다.  

1. 왼쪽 탐색 메뉴에서 구성 아래에 있는 **관리 id** 를 선택   합니다. ** 그런 다음 **시스템 할당 관리 Id 사용** 옆의 확인란을 선택 하   고 **저장** 을 선택 합니다.

   :::image type="content" source="media/event-hubs-managed-identity/system-assigned-managed-identity.png" alt-text="시스템 할당 관리 ID":::  

1. Stream Analytics 작업 id의 서비스 사용자가 Azure Active Directory에 만들어집니다. 새로 만든 id의 수명 주기는 Azure에서 관리 됩니다. Stream Analytics 작업을 삭제하면 연결된 ID(즉, 서비스 주체)는 Azure에서 자동으로 삭제합니다.  

   구성을 저장하면 서비스 주체의 OID(개체 ID)가 아래와 같이 보안 주체 ID로 나열됩니다.  

   :::image type="content" source="media/event-hubs-managed-identity/principal-id.png" alt-text="보안 주체 ID":::

   서비스 주체에는 Stream Analytics 작업과 동일한 이름이 사용됩니다. 예를 들어, 작업 이름이 인 경우  `MyASAJob` 서비스 사용자의 이름도 이기도  `MyASAJob` 합니다.  

## <a name="grant-the-stream-analytics-job-permissionsto-access-the-event-hub"></a>Stream Analytics 작업에 이벤트 허브에 대 한 액세스 권한 부여

관리 id를 사용 하 여 이벤트 허브에 액세스 하는 Stream Analytics 작업의 경우 사용자가 만든 서비스 주체에는 이벤트 허브에 대 한 특별 한 권한이 있어야 합니다.

1. 이벤트 허브에서 **Access Control (IAM)** 로 이동 합니다.

1. **+ 추가** 및 **역할 할당 추가** 를 선택 합니다.

1. **역할 할당 추가** 페이지에서 다음 옵션을 입력 합니다.

   |매개 변수|값|
   |---------|-----|
   |역할|Azure Event Hubs 데이터 소유자|
   |다음에 대한 액세스 할당|사용자, 그룹 또는 서비스 사용자|
   |선택|Stream Analytics 작업의 이름을 입력 합니다.|

   :::image type="content" source="media/event-hubs-managed-identity/add-role-assignment.png" alt-text="역할 할당 추가":::

1. **저장** 을 선택 하 고 잠시 후에 변경 내용을 전파 합니다.

이벤트 허브 네임 스페이스 수준에서이 역할을 부여할 수도 있습니다. 그러면이 역할에서 생성 된 모든 Event Hubs에 사용 권한이 자연스럽 게 전파 됩니다. 즉, 네임 스페이스에 있는 모든 Event Hubs를 Stream Analytics 작업에서 관리 되는 id 인증 리소스로 사용할 수 있습니다.

## <a name="create-anevent-hub-input-or-output"></a>이벤트 허브 입력 또는 출력 만들기  

관리 id를 구성 했으므로 Stream Analytics 작업에 대 한 입력 또는 출력으로 이벤트 허브 리소스를 추가할 준비가 되었습니다.  

### <a name="add-the-event-hub-as-an-input"></a>이벤트 허브를 입력으로 추가 합니다. 

1. Stream Analytics 작업으로 이동 하 고 **작업 토폴로지** 아래의 **입력** 페이지로 이동 합니다.

1. **이벤트 허브 > 스트림 입력 추가** 를 선택 합니다. 입력 속성 창에서 이벤트 허브를 검색 하 고 선택 하 고 *인증 모드* 드롭다운 메뉴에서 **관리 id** 를 선택 합니다.

1. 나머지 속성을 입력 하 고 **저장** 을 선택 합니다.

### <a name="add-the-event-hub-as-an-output"></a>출력으로 이벤트 허브 추가

1. Stream Analytics 작업으로 이동 하 여 **작업 토폴로지** 아래의 **출력** 페이지로 이동 합니다.

1. **추가 > 이벤트 허브** 를 선택 합니다. 출력 속성 창에서 이벤트 허브를 검색 하 고 선택 하 고 *인증 모드* 드롭다운 메뉴에서 **관리 id** 를 선택 합니다.

1. 나머지 속성을 입력 하 고 **저장** 을 선택 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics에서 출력 Event Hubs](event-hubs-output.md)
* [이벤트 허브에서 데이터 스트리밍](stream-analytics-define-inputs.md#stream-data-from-event-hubs)
