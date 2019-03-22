---
title: Azure Stack에서 App Service 백업 | Microsoft Docs
description: Azure Stack의 App Service 백업에 대 한 자세한 지침입니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: jeffgilb
ms.reviewer: apwestgarth
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: 66f1f1389a7e4ceebc38544f2eb9836fad2bd96a
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340551"
---
# <a name="back-up-app-service-on-azure-stack"></a>Azure Stack에서 App Service 백업

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*  

이 문서에서는 Azure Stack에서 App Service를 백업 하는 방법에 대 한 지침을 제공 합니다.

> [!IMPORTANT]
> Azure Stack에서 app Service의 일부로 백업 되지 않습니다 [Azure Stack 인프라 백업](azure-stack-backup-infrastructure-backup.md)합니다. Azure Stack 연산자로, 필요한 경우 App Service 성공적으로 복구할 수 있으려면 단계를 수행 해야 합니다.

Azure Stack의 azure App Service에는 재해 복구를 계획할 때 고려해 야 할 네 가지 주요 구성 요소에 있습니다.
1. 리소스 공급자 인프라; 서버 역할, 작업자 계층 등입니다. 
2. App Service 암호
3. 앱 서비스 SQL 서버 데이터베이스 호스팅 및 측정
4. App Service 사용자 워크 로드를 콘텐츠 App Service 파일 공유에 저장   

## <a name="back-up-app-service-secrets"></a>App Service 비밀 백업
App Service 백업에서 복구 하는 경우 초기 배포에서 사용 되는 App Service 키를 제공 해야 합니다. App Service 성공적으로 배포 하 고 안전한 위치에 저장 된 즉시이 정보를 저장 해야 합니다. App Service 복구 PowerShell cmdlet을 사용 하 여 복구 중 리소스 공급자 인프라 구성은 백업에서 다시 만들어집니다.

다음이 단계를 수행 하 여 앱 서비스 비밀을 백업 하려면 관리 포털을 사용 합니다. 

1. 서비스 관리자로 Azure Stack 관리 포털에 로그인 합니다.

2. 이동할 **App Service** -> **비밀**합니다. 

3. 선택 **비밀 다운로드**합니다.

   ![비밀 다운로드](./media/app-service-back-up/download-secrets.png)

4. 비밀 다운로드에 대 한 준비 되 면 클릭 **저장** App Service 암호를 저장 하 고 (**SystemSecrets.JSON**) 파일을 안전한 위치에 있습니다. 

   ![암호를 저장 합니다.](./media/app-service-back-up/save-secrets.png)

> [!NOTE]
> App Service 암호 회전 될 때마다 이러한 단계를 반복 합니다.

## <a name="back-up-the-app-service-databases"></a>App Service 데이터베이스를 백업
App Service를 복원 해야 합니다 **Appservice_hosting** 및 **Appservice_metering** 데이터베이스 백업 합니다. SQL Server 유지 관리 계획 또는 Azure Backup Server를 사용 하 여 이러한 데이터베이스를 백업 되 고 정기적으로 안전 하 게 저장 되도록 하는 것이 좋습니다. 그러나 모든 메서드의 일반 SQL 백업 되도록 생성 됩니다 사용할 수 있습니다.

수동으로 SQL Server에 로그인 하는 동안 이러한 데이터베이스를 백업 하려면 다음 PowerShell 명령을 사용할 수 있습니다.

  ```powershell
  $s = "<SQL Server computer name>"
  $u = "<SQL Server login>" 
  $p = read-host "Provide the SQL admin password"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_hosting TO DISK = '<path>\hosting.bak'"
  sqlcmd -S $s -U $u -P $p -Q "BACKUP DATABASE appservice_metering TO DISK = '<path>\metering.bak'"
  ```

> [!NOTE]
> SQL AlwaysOn 데이터베이스를 백업 해야 할 경우에 따라 [이러한 지침](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-backup-on-availability-replicas-sql-server?view=sql-server-2017)합니다. 

모든 데이터베이스가 성공적으로 백업 된, 후 App Service 암호 정보와 함께 안전한 위치에.bak 파일을 복사 합니다.

## <a name="back-up-the-app-service-file-share"></a>App Service 파일 공유 백업
App Service 저장소 테 넌 트 앱의에서 정보는 파일 공유. 이를 백업 해야 App Service 데이터베이스와 함께 정기적으로 복원 해야 하는 경우 최대한 적은 데이터 손실 되도록 합니다. 

콘텐츠를 정기적으로 파일 공유를 복사 하려면 Azure Backup Server 또는 다른 메서드를 사용할 수 있습니다 App Service 파일 공유를 백업 하려면 콘텐츠 위치를 저장 한 모든 이전 복구 정보입니다. 

예를 들어, Windows PowerShell (없습니다 PowerShell ISE) 콘솔 세션에서 robocopy를 사용 하려면 다음이 단계를 사용할 수 있습니다.

```powershell
$source = "<file share location>"
$destination = "<remote backup storage share location>"
net use $destination /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy $source $destination
net use $destination /delete
```

## <a name="next-steps"></a>다음 단계
[Azure Stack에서 App Service를 복원 합니다.](app-service-recover.md)