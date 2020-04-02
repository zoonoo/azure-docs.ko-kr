---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 57368331e83111836e13b6c3796cacad9816e318
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521275"
---
Azure 포털에서 저장소 계정 액세스 키 또는 연결 문자열을 보고 복사하려면 다음을 수행하십시오.

1. [Azure 포털로](https://portal.azure.com)이동합니다.
2. 스토리지 계정을 찾습니다.
3. **설정에서** **액세스 키를 선택합니다.** 계정 액세스 키는 물론 각 키의 전체 연결 문자열이 나타납니다.
4. **key1** 아래에서 **키** 값을 찾고, **복사** 단추를 클릭하여 계정 키를 복사합니다.
5. 또는 전체 연결 문자열을 복사할 수 있습니다. **key1** 아래에서 **연결 문자열** 값을 찾고, **복사** 단추를 클릭하여 연결 문자열을 복사합니다.

    ![Azure 포털에서 액세스 키를 보는 방법을 보여 주시는 스크린샷](media/storage-view-keys-include/portal-connection-string.png)

두 키 중 하나를 사용하여 Azure Storage에 액세스할 수 있지만 일반적으로 첫 번째 키를 사용하고 키를 회전할 때 두 번째 키를 사용하는 것이 좋습니다.

계정의 액세스 키를 보거나 읽으려면 사용자가 서비스 관리자이거나 **Microsoft.Storage/StorageAccounts/listkeys/action을**포함하는 RBAC 역할을 할당받아야 합니다. 이 작업을 포함하는 일부 기본 제공 RBAC 역할은 **소유자,** **기여자**및 **저장소 계정 키 운영자 서비스 역할** 역할입니다. 서비스 관리자 역할에 대한 자세한 내용은 [클래식 구독 관리자 역할, Azure RBAC 역할 및 Azure AD 역할을](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)참조하십시오. Azure 저장소에 대 한 기본 제공 역할에 대 한 자세한 내용은 [Azure RBAC에 대 한 Azure 기본 제공 역할의](../articles/role-based-access-control/built-in-roles.md#storage) **저장소** 섹션을 참조 합니다.
