---
title: Azure 스택 SQL 리소스 공급자 제거 | Microsoft Docs
description: 자세한 내용은 방법 Azure 스택 배포에서 SQL 리소스 공급자를 제거할 수 있습니다.
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
ms.date: 06/20/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 150d1c40463aa04527bdd6e356a4c24ef68b02ef
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36301901"
---
# <a name="remove-the-sql-resource-provider"></a>SQL 리소스 공급자를 제거 합니다.

SQL 리소스 공급자를 제거 하기 전에 모든 공급자 종속성을 제거 해야 합니다. 또한는 리소스 공급자를 설치 하는 데 사용 된 배포 패키지의 복사본을 해야 합니다.

## <a name="to-remove-the-sql-resource-provider"></a>SQL 리소스 공급자를 제거 하려면

1. 모든 기존 SQL 리소스 공급자 종속성을 제거를 확인 합니다.

   > [!NOTE]
   > SQL 리소스 공급자를 제거 하면 종속 리소스에서 리소스 공급자를 현재 사용 중인 경우에 진행 됩니다.
  
2. SQL 리소스 공급자의 복사본을 이진 가져오고 후 임시 디렉터리에 콘텐츠를 추출 자동 압축 풀기 실행 합니다.

3. 관리자 권한으로 새 PowerShell 콘솔 창을 열고 SQL 리소스 공급자 이진 파일의 압축을 푼 디렉터리로 변경 합니다.

4. 다음 매개 변수를 사용 하 여 DeploySqlProvider.ps1 스크립트를 실행 합니다.

    - **제거**합니다. 리소스 공급자와 관련 된 모든 리소스를 제거합니다.
    - **PrivilegedEndpoint**합니다. IP 주소 또는 권한 있는 끝점의 DNS 이름입니다.
    - **CloudAdminCredential**합니다. 권한 있는 끝점에 액세스 하는 데 필요한 클라우드 관리자에 대 한 자격 증명입니다.
    - **AzCredential**합니다. Azure 스택 서비스 관리자 계정에 대 한 자격 증명입니다. Azure 스택을 배포 하는 데 사용한 것과 동일한 자격 증명을 사용 합니다.

## <a name="next-steps"></a>다음 단계

[PaaS로 응용 프로그램 서비스를 제공 합니다.](azure-stack-app-service-overview.md)
