---
title: "Azure 스택 (서비스 관리자 및 테 넌 트)에 사용자별 리소스에 대 한 사용 권한 관리 | Microsoft Docs"
description: "서비스 관리자 또는 테 넌 트 RBAC 사용 권한을 관리 하는 방법에 설명 합니다."
services: azure-stack
documentationcenter: 
author: Heathl17
manager: byronr
editor: 
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: e558f9de9bc3182bbe20ceb9d8f3f96e47fa542c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-role-based-access-control"></a>역할 기반 Access Control 관리

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

Azure Stack의 사용자는 구독, 리소스 그룹 또는 서비스의 각 인스턴스에 대해 읽기 권한자, 소유자 또는 참가자일 수 있습니다. 예를 들어 사용자 A는 구독 1에 대한 읽기 권한이 있지만 가상 컴퓨터 7에 대한 소유자 권한이 있을 수 있습니다.

* 읽기 권한자: 사용자는 모든 항목을 볼 수 있지만 변경할 수 없습니다.
* 참여자: 사용자는 리소스에 대한 액세스를 제외한 모든 것을 관리할 수 있습니다.
* 소유자: 사용자는 리소스에 대한 액세스를 포함한 모든 것을 관리할 수 있습니다.

## <a name="set-access-permissions-for-a-user"></a>사용자에 대한 액세스 권한을 설정합니다
1. 관리하려는 리소스에 대한 소유자 권한이 있는 계정으로 로그인합니다.
2. 리소스에 대 한 블레이드에서 클릭는 **액세스** 아이콘 ![](media/azure-stack-manage-permissions/image1.png)합니다.
3. 에 **사용자** 블레이드에서 클릭 **역할**합니다.
4. 에 **역할** 블레이드에서 클릭 **추가** 사용자에 대 한 사용 권한을 추가 하 합니다.

## <a name="next-steps"></a>다음 단계
[Azure Stack 테넌트 추가](azure-stack-add-new-user-aad.md)

