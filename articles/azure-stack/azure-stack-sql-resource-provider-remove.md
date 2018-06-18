---
title: Azure 스택 SQL 리소스 공급자 제거 | Microsoft Docs
description: Azure 스택 배포에서 SQL 리소스 공급자를 제거 하는 방법에 대해 알아봅니다.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 9f90201cad0f74923460c2f25eff4de98dc6690a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294783"
---
# <a name="removing-the-mysql-resource-provider"></a>MySQL 리소스 공급자를 제거합니다.  
SQL 리소스 공급자를 제거 하기 전에 먼저 모든 종속성을 제거에 필수적입니다.

## <a name="remove-the-mysql-resource-provider"></a>MySQL 리소스 공급자를 제거 합니다. 

1. 기존 SQL 리소스 공급자 종속성을 제거 했는지 확인 합니다.

  > [!NOTE]
  > SQL 리소스 공급자를 제거 하면 종속 리소스에서 리소스 공급자를 현재 사용 중인 경우에 진행 됩니다. 
  
2. 이 버전의 SQL 리소스 공급자 어댑터에 대 한 다운로드 하는 원래 배포 패키지가 있는지 확인 합니다.
3. 다음 매개 변수를 사용 하 여 배포 스크립트를 다시 실행 하십시오.
    - 사용 하 여-매개 변수를 제거 합니다.
    - IP 주소 또는 권한 있는 끝점의 DNS 이름입니다.
    - 권한 있는 끝점에 액세스 하는 데 필요한 클라우드 관리자에 대 한 자격 증명입니다.
    - Azure 스택 서비스 관리자 계정에 대 한 자격 증명입니다. Azure 스택 배포에 사용한 것과 동일한 자격 증명을 사용 합니다.

## <a name="next-steps"></a>다음 단계
[PaaS로 응용 프로그램 서비스를 제공 합니다.](azure-stack-app-service-overview.md)
