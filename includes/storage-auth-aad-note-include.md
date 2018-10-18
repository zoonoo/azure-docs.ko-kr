---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/06/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3a3ee0cda3643ac73581f868e47905841d9f1563
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44095594"
---
> [!IMPORTANT]
> - Blob 및 큐에 대한 Azure AD 인증 미리 보기는 비프로덕션 용도로만 사용됩니다. 현재 프로덕션 SLA(서비스 수준 계약)는 사용할 수 없습니다. 사용자 시나리오에서 Azure AD 인증이 아직 지원되지 않는 경우, 응용 프로그램에서 공유 키 인증 또는 SAS 토큰을 계속 사용합니다.
>
> - 미리 보기 동안 RBAC 역할 할당을 전파하는 데 최대 5분이 소요될 수 있습니다.
>
> - Blob 및 큐 작업을 호출할 때 Azure AD를 인증하려면 HTTPS를 사용해야 합니다.
>
> - 미리 보기 릴리스에서 Azure Portal은 Azure AD 자격 증명을 사용하여 Blob 및 큐 데이터를 읽거나 작성할 수 없습니다. 그 대신, Azure Portal은 계속해서 계정 액세스 키를 사용합니다. Azure Portal에서 Blob 또는 큐 데이터를 보거나 업데이트하려면 사용자에게 [Storage 계정 - 키 나열](https://docs.microsoft.com/rest/api/storagerp/storageaccounts/listkeys) 호출에 대한 권한을 부여하는 [Microsoft.Storage/storageAccounts/listkeys/action](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role)을 포함하는 RBAC 역할을 할당해야 합니다. Blob 및 큐의 Contributor 및 Reader 역할은 현재 미리 보기 릴리스의 일부로써 **listkeys** 작업을 포함하지 않으므로 Azure Portal을 통해 데이터 액세스 권한을 제공하지 않습니다. 미리 보기 릴리스 중에 Blob 및 큐 데이터에 대한 ID 기반 액세스를 탐색하려면 PowerShell 또는 Azure CLI를 사용하세요.
