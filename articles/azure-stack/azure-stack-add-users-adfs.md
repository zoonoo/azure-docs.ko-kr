---
title: Azure Stack ADFS에 대 한 사용자 추가 | Microsoft Docs
description: Azure Stack의 ADFS 배포에 대 한 사용자를 추가 하는 방법 알아보기
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2019
ms.author: patricka
ms.reviewer: unknown
ms.lastreviewed: 02/21/2019
ms.openlocfilehash: 1b47739200c79317273ea0c788f21a7ee4a3b818
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/21/2019
ms.locfileid: "56648509"
---
# <a name="add-azure-stack-users-in-ad-fs"></a>AD FS에서 Azure Stack 사용자를 추가 합니다.
사용할 수는 **Active Directory Users and Computers** 스냅인을 AD FS를 id 공급자로 활용 하 여 Azure Stack 환경에 사용자를 추가 합니다.

## <a name="add-windows-server-active-directory-users"></a>Windows Server Active Directory 사용자를 추가 합니다.
> [!TIP]
> 이 예제에서는 기본 azurestack.local ASDK active directory를 사용 합니다. 

1. Windows 관리 도구에 대 한 액세스를 제공 하는 계정을 사용 하 여 컴퓨터를 로그인 하 고 새 Microsoft Management Console (MMC)를 엽니다.
2. 선택 **파일 > 추가 / 스냅인 제거**합니다.
3. 선택 **Active Directory Users and Computers** > **AzureStack.local** > **사용자**합니다.
4. 선택 **동작** > **새** > **사용자**합니다.
5. 새 개체 – 사용자가 사용자 세부 정보를 제공 합니다. **다음**을 선택합니다.
6. 제공 하 고 암호를 확인 합니다.
7. 선택 **다음** 값을 마무리 합니다. 선택 **완료** 사용자를 만들려고 합니다.


## <a name="next-steps"></a>다음 단계
[서비스 주체 만들기](azure-stack-create-service-principals.md)