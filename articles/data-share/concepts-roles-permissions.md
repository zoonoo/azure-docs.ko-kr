---
title: 역할 및 Azure 데이터 공유 미리 보기에 대 한 요구 사항
description: 역할 및 Azure 데이터 공유 미리 보기에 대 한 요구 사항
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: a5d70b9aa611b4f939cb46b5d25655edd818cb35
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807533"
---
# <a name="roles-and-requirements-for-azure-data-share-preview"></a>역할 및 Azure 데이터 공유 미리 보기에 대 한 요구 사항

이 문서도 동의 하 고 Azure 데이터 공유 미리 보기를 사용 하 여 데이터 수신에 대 한 Azure 데이터 공유 미리 보기를 사용 하 여 데이터를 공유 하는 데 필요한 역할을 설명 합니다. 

## <a name="roles-and-requirements"></a>역할 및 요구 사항

를 공유 하거나 Azure 데이터 공유를 사용 하 여 데이터를 수신 하려면 Azure에 로그인 하는 데 사용 하는 사용자 계정에서 데이터를 공유 하는지 또는에서 데이터를 수신할 저장소 계정에 데이터 공유 권한을 부여할 수 있어야 합니다. 에 있는 사용 권한을이 일반적으로 **소유자** 역할 또는 Microsoft.Authorization/role 할당/쓰기 권한이 할당 된 사용자 지정 역할을 합니다. 

를 공유 하거나 또는 Azure Storage 계정에 데이터를 수신 하려면 저장소 계정의 소유자 여야 합니다. 저장소 계정을 만든 경우에이 부여 하지 않습니다 자동으로 저장소 계정의 소유권입니다. 직접에 Azure Storage 계정의 소유자 역할에 추가 하려면 다음이 단계를 수행 합니다.

1. Azure portal에서 저장소 계정으로 이동
1. 선택 **액세스 제어 (IAM)**
1. **추가**를 클릭합니다.
1. 자신을 소유자로 추가

구독에 있는 권한을 보려면 Azure Portal에서 오른쪽 위 모서리에 있는 사용자 이름을 선택한 다음, **권한**을 선택합니다. 여러 구독에 액세스할 수 있는 경우 적절한 구독을 선택합니다. 

## <a name="resource-provider-registration"></a>리소스 공급자 등록 

Azure 데이터 공유 초대를 수락 하는 경우 수동으로 Microsoft.DataShare 리소스 공급자에 구독을 등록 해야 합니다. Azure 구독에 Microsoft.DataShare 리소스 공급자를 등록 하려면 다음이 단계를 수행 합니다. 

1. Azure portal로 이동 **구독**
1. Azure 데이터 공유에 대 한 사용 중인 구독 선택
1. 클릭 **리소스 공급자**
1. Microsoft.DataShare 검색
1. **등록**을 클릭합니다.

## <a name="next-steps"></a>다음 단계

- Azure - [역할 정의 이해](../role-based-access-control/role-definitions.md)에서 역할에 대해 자세히 알아봅니다.

