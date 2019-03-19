---
title: Azure Stack (서비스 관리자 및 테 넌 트)에서 사용자 당 리소스에 대 한 사용 권한 관리 | Microsoft Docs
description: 서비스 관리자 또는 테 넌 트, RBAC 권한을 관리 하는 방법에 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: acec53a99fd6d809dc01ce12b02987d66579b0c5
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58118292"
---
# <a name="manage-role-based-access-control"></a>역할 기반 Access Control 관리

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

Azure Stack의 사용자는 구독, 리소스 그룹 또는 서비스의 각 인스턴스에 대해 읽기 권한자, 소유자 또는 참가자일 수 있습니다. 예를 들어, 사용자는 구독 한 읽기 권한이 되었지만 가상 머신 7에 대 한 소유자 권한이 됩니다.

 - 읽기 권한자: 사용자는 모든 항목을 볼 수 있지만 변경할 수 없습니다.
 - Contributor: 사용자는 리소스에 대 한 액세스를 제외한 모든 것을 관리할 수 있습니다.
 - 소유자: 사용자는 리소스에 대 한 액세스를 포함 한 모든 것을 관리할 수 있습니다.

## <a name="set-access-permissions-for-a-user"></a>사용자에 대한 액세스 권한을 설정합니다

1. 관리하려는 리소스에 대한 소유자 권한이 있는 계정으로 로그인합니다.
2. 리소스에 대 한 블레이드에서 다음을 클릭 합니다 **액세스** 아이콘 ![](media/azure-stack-manage-permissions/image1.png)합니다.
3. 에 **사용자가** 블레이드에서 클릭 **역할**입니다.
4. 에 **역할** 블레이드에서 클릭 **추가** 사용자 권한을 추가 하려면.

## <a name="set-access-permissions-for-a-universal-group"></a>유니버설 그룹에 대 한 액세스 권한을 설정합니다 

> [!Note]
> Active Directory 적용할 페더레이션 서비스 (AD FS).

1. 관리하려는 리소스에 대한 소유자 권한이 있는 계정으로 로그인합니다.
2. 리소스에 대 한 블레이드에서 다음을 클릭 합니다 **액세스** 아이콘 ![](media/azure-stack-manage-permissions/image1.png)합니다.
3. 에 **사용자가** 블레이드에서 클릭 **역할**입니다.
4. 에 **역할** 블레이드에서 클릭 **추가** 유니버설 그룹 Active Directory 그룹에 대 한 권한을 추가 하려면.

## <a name="next-steps"></a>다음 단계
[Azure Stack 테넌트 추가](azure-stack-add-new-user-aad.md)

