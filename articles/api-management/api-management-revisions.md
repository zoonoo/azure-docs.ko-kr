---
title: Azure API Management의 수정 버전 Microsoft Docs
description: Azure API Management의 수정 개념에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 06/12/2020
ms.author: jodowns
ms.custom: fasttrack-new
ms.openlocfilehash: b099a6ea706482e25b2c37a87cf0a24f2fe475bb
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531593"
---
# <a name="revisions-in-azure-api-management"></a>Azure API Management의 수정 버전

수정 버전을 통해 제어 되 고 안전한 방식으로 Api를 변경할 수 있습니다. 변경 하려면 새 수정 버전을 만듭니다. 그런 다음 API 소비자를 방해 하지 않고 API를 편집 하 고 테스트할 수 있습니다. 준비가 되 면 수정 사항을 최신으로 설정 합니다. 동시에 변경 로그에 항목을 게시 하 여 API 소비자를 최신 상태로 유지할 수 있습니다. 변경 로그는 개발자 포털에 게시 됩니다.

> [!NOTE]
> 개발자 포털은 소비 계층에서 사용할 수 없습니다.

수정 버전으로 다음을 수행할 수 있습니다.

- 프로덕션 API를 방해 하지 않고 API 정의 및 정책을 안전 하 게 변경 합니다.
- 게시 하기 전에 변경 내용을 사용해 보세요.
- 개발자가 새로운 기능을 이해할 수 있도록 변경 내용을 문서화 합니다.
- 문제가 발견 되 면 롤백합니다.

[연습을 수행 하 여 수정 작업을 시작 하세요.](./api-management-get-started-revise-api.md)

## <a name="accessing-specific-revisions"></a>특정 수정 버전 액세스

API에 대 한 각 수정 버전에는 특별 한 형식의 URL을 사용 하 여 액세스할 수 있습니다. API URL의 끝에를 추가 하 고 쿼리 문자열 앞에를 추가 하 여 `;rev={revisionNumber}` 해당 api의 특정 수정 버전에 액세스 합니다. 예를 들어이 URL을 사용 하 여 API의 수정 버전 3에 액세스할 수 있습니다 `customers` .

`https://apis.contoso.com/customers;rev=3?customerId=123`

기본적으로 각 수정 버전에는 현재 수정 버전과 동일한 보안 설정이 있습니다. 각 수정 버전에 대해 다른 보안을 적용 하려는 경우 특정 수정 버전에 대 한 정책을 의도적으로 변경할 수 있습니다. 예를 들어 외부 호출자가 아직 개발 중인 수정 버전에 액세스 하지 못하도록 [IP 허용 목록 정책을](./api-management-access-restriction-policies.md#RestrictCallerIPs) 추가 하려고 할 수 있습니다.

수정 버전을 오프 라인 상태로 만들 수 있습니다 .이 경우 해당 URL을 통해 수정 버전에 액세스 하려고 하는 경우에도 호출자가 액세스할 수 없게 됩니다. Azure Portal를 사용 하 여 수정 버전을 오프 라인으로 표시할 수 있습니다. PowerShell을 사용 하는 경우 cmdlet을 사용 하 여 `Set-AzApiManagementApiRevision` 인수를로 설정할 수 있습니다 `Path` `$null` .

> [!NOTE]
> 테스트에 사용 하지 않는 경우 수정 버전을 오프 라인으로 만드는 것이 좋습니다.

## <a name="current-revision"></a>현재 수정 버전

단일 수정 버전을 *현재* 수정 버전으로 설정할 수 있습니다. 이 수정 버전은 URL에 명시적 수정 번호를 지정 하지 않는 모든 API 요청에 사용 됩니다. 해당 수정 버전을 현재 버전으로 설정 하 여 이전 수정 버전으로 롤백할 수 있습니다.

Azure Portal를 사용 하 여 수정 버전을 현재 상태로 설정할 수 있습니다. PowerShell을 사용 하는 경우 cmdlet을 사용할 수 있습니다 `New-AzApiManagementApiRelease` .

## <a name="revision-descriptions"></a>수정 설명

수정 버전을 만들 때 사용자 고유의 추적에 대 한 설명을 설정할 수 있습니다. 설명은 API 사용자에 게 재생 되지 않습니다.

수정 버전을 현재로 설정 하는 경우 필요에 따라 공용 변경 로그 정보도 지정할 수 있습니다. 사용자 API 사용자가 볼 수 있도록 변경 로그가 개발자 포털에 포함 됩니다. PowerShell cmdlet을 사용 하 여 변경 로그 메모를 수정할 수 있습니다 `Update-AzApiManagementApiRelease` .

## <a name="versions-and-revisions"></a>버전 및 수정 버전

버전 및 수정 버전은 고유 기능입니다. 각 버전에는 버전이 없는 API와 마찬가지로 여러 개의 수정이 있을 수 있습니다. 버전을 사용 하지 않고 수정 버전을 사용 하거나 다른 방법을 사용할 수 있습니다. 일반적으로 버전은 api 버전을 주요 변경 내용과 분리 하는 데 사용 되 고, 수정 버전은 API에 대 한 사소한 변경 내용에 사용할 수 있습니다.

수정 버전의 변경 내용이 포함 되어 있거나, 정식 버전을 베타/테스트 버전으로 변환 하려는 경우 수정 버전에서 버전을 만들 수 있습니다. Azure Portal를 사용 하 여 수정 버전 탭의 수정 상황에 맞는 메뉴에서 ' 수정 버전에서 버전 만들기 '를 클릭 합니다.
