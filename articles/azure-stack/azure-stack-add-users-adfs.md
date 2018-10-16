---
title: Azure Stack ADFS에 대 한 사용자 추가 | Microsoft Docs
description: Azure Stack의 ADFS 배포에 대 한 사용자를 추가 하는 방법 알아보기
services: azure-stack
documentationcenter: ''
author: patricka
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: patricka
ms.reviewer: unknown
ms.openlocfilehash: f8abacbcb05d1346931b5c2e1097660cfbd8e594
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344169"
---
# <a name="add-azure-stack-users-in-ad-fs"></a>AD FS에서 Azure Stack 사용자를 추가 합니다.
사용할 수는 **Active Directory Users and Computers** 스냅인을 AD FS를 id 공급자로 활용 하 여 Azure Stack 환경에 사용자를 추가 합니다.

## <a name="add-windows-server-active-directory-users"></a>Windows Server Active Directory 사용자를 추가 합니다.
> [!TIP]
> 이 예제에서는 기본 azurestack.local ASDK active directory를 사용 합니다. 

1.  Windows 관리 도구에 대 한 액세스를 제공 하는 계정을 사용 하 여 컴퓨터를 로그인 하 고 새 Microsoft Management Console (MMC)를 엽니다.
2.  클릭 **파일 > 추가 / 스냅인 제거**합니다.
3.  선택 **Active Directory Users and Computers** > **AzureStack.local** > **사용자**합니다.
4.  클릭 **동작** > **새** > **사용자**합니다.
5.  새 개체 – 사용자 창에서에서 제공 하 고 암호를 확인 합니다.
6.  클릭 **다음** 에 값을 마무리 하 고 사용자를 만들려면 마침을 클릭 합니다.


## <a name="next-steps"></a>다음 단계
[서비스 주체 만들기](azure-stack-create-service-principals.md)