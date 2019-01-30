---
title: 관리 포털에서 Azure Stack에 대 한 백업을 사용 하도록 설정 | Microsoft Docs
description: 오류가 발생 하는 경우 Azure Stack을 복원할 수 있도록 관리 포털을 통해 인프라 Backup 서비스를 사용 합니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 56C948E7-4523-43B9-A236-1EF906A0304F
ms.service: azure-stack
ms.workload: naS
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/05/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: db2c55ec30e766496b98ef66b584df26f2dfe116
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55239284"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>관리 포털에서 Azure Stack에 대 한 백업을 사용 하도록 설정
Azure Stack 백업을 생성할 수 있도록 관리 포털을 통해 인프라 Backup 서비스를 사용 합니다. 경우 클라우드 복구를 사용 하 여 환경을 복원 하려면 이러한 백업에 사용할 수 있습니다 [치명적인 오류](./azure-stack-backup-recover-data.md)합니다. 클라우드 복구의 목적은 운영자와 사용자 수에 다시 로그인 포털 복구가 완료 된 후 확인 합니다. 사용자 구독 역할 기반 액세스 권한 및 역할, 원래 계획, 제품 및 이전에 정의 된 계산, 저장소 및 네트워크 할당량을 비롯 하 여 복원 해야 합니다.

그러나 IaaS Vm, 네트워크 구성 및 저장소 계정, blob, 테이블와 같은 저장소 리소스 인프라 백업 서비스를 백업 하지 않습니다. 하 고 등등으로 클라우드 복구 후 로그인 하는 사용자가 완료 되도록 하면서 이전에 기존의 모든 표시 되지 않습니다. 리소스입니다. 플랫폼 (PaaS) 서비스로 리소스 및 데이터도 백업 되지 서비스입니다. 

관리자와 사용자가 백업 및 인프라 백업 프로세스에서 IaaS 및 PaaS 리소스를 개별적으로 복원 하는 일을 담당 합니다. IaaS 및 PaaS 리소스에 대 한 자세한 내용은 다음 링크를 참조 합니다.

- [Virtual Machines](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-protect)
- [App Service](https://docs.microsoft.com/azure/app-service/manage-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


## <a name="enable-or-reconfigure-backup"></a>백업 다시 구성 하거나 사용

1. 엽니다는 [Azure Stack 관리 포털](azure-stack-manage-portals.md)합니다.
2. 선택 **모든 서비스**를 선택한 다음는 **관리** 범주 선택 **인프라 백업**합니다. 선택 **Configuration** 에 **인프라 백업** 블레이드입니다.
3. 경로를 입력 합니다 **백업 저장소 위치**합니다. 별도 장치에서 호스트 되는 파일 공유 경로 대 한 범용 명명 규칙 (UNC) 문자열을 사용 합니다. UNC 문자열로 공유 파일 또는 장치와 같은 리소스의 위치를 지정합니다. 서비스에 대 한 IP 주소를 사용할 수 있습니다. 재해 발생 후 백업 데이터의 가용성을 보장 하려면 장치 별도 위치에 있어야 합니다.

    > [!Note]  
    > 사용자 환경에서 엔터프라이즈 환경에 Azure Stack 인프라 네트워크에서 이름 확인을 지 원하는 경우에 IP 대신 FQDN을 사용할 수 있습니다.
    
4. 형식 합니다 **Username** 파일 읽기 및 쓰기를 충분 한 액세스를 사용 하 여 도메인 및 사용자를 사용 하 여 합니다. 예: `Contoso\backupshareuser`
5. 형식 합니다 **암호** 사용자에 대 한 합니다.
6. 암호를 다시 입력 **암호 확인**합니다.
7. 합니다 **시간 빈도** 빈도 결정 백업은 만들어집니다. 기본값은 12입니다. Scheduler는 최대 12 및 4 개를 지원합니다. 
8. 합니다 **보존 기간 (일)** 외부 위치에 유지 됩니다 백업 기간 (일)을 결정 합니다. 기본값은 7입니다. Scheduler는 최대 14 및 2 개를 지원합니다. 백업 보존 기간 보다 오래 된 외부 위치에서 자동으로 삭제 됩니다.

    > [!Note]  
    > 보존 기간 보다 오래 된 백업을 보관 하려는 경우 스케줄러 백업을 삭제 하기 전에 파일을 백업 해야 합니다. 백업 보존 기간을 축소 하는 경우 (예: 7 일에서 5 일), 스케줄러는 모든 백업을 삭제 새 보존 기간 보다 오래 된 합니다. 이 값을 업데이트 하기 전에 삭제 백업으로 확인 되어 있는지 확인 합니다. 

9. 미리 공유한 키를 제공 합니다 **암호화 키** 상자입니다. 백업 파일이이 키를 사용 하 여 암호화 됩니다. 이 키는 안전한 위치에 저장 해야 합니다. 사용자가 처음으로이 키를 설정 하거나 나중에 키를 회전, 후이 인터페이스에서 키를 확인할 수 없습니다. 키를 만들려면 다음 Azure Stack PowerShell 명령을 실행 합니다.
    ```powershell
    New-AzsEncryptionKeyBase64
    ```
10. 선택 **확인** 컨트롤러 백업 설정을 저장 합니다.

    ![Azure Stack 백업 컨트롤러 설정](media/azure-stack-backup/backup-controller-settings.png)

## <a name="start-backup"></a>백업 시작
백업을 시작 하려면 클릭 **지금 Backup** 주문형 백업을 시작 하려면. 요청 시 백업을 예약 된 다음 백업에 대 한 시간을 수정 하지 않습니다. 작업이 완료 된 후의 설정을 확인할 수 있습니다 **Essentials**:

![Azure Stack-주문형 백업](media/azure-stack-backup/scheduled-backup.png)

PowerShell cmdlet을 실행할 수도 있습니다 **시작 AzsBackup** Azure Stack 관리 컴퓨터에 있습니다. 자세한 내용은 [Azure Stack 백업](azure-stack-backup-back-up-azure-stack.md)합니다.

## <a name="enable-or-disable-automatic-backups"></a>자동 백업을 사용할지 설정 합니다.
Backup을 사용 하도록 설정 하면 백업이 자동으로 예약 되어 있습니다. 다음 일정 백업 시를 체크 인할 수 있습니다 **Essentials**합니다. 

![Azure Stack-주문형 백업](media/azure-stack-backup/on-demand-backup.png)

이후 예약 된 백업을 사용 하지 않도록 설정 해야 할 경우 클릭할 **자동 백업을 사용 하지 않도록 설정**합니다. 사용 하지 않도록 설정 자동 백업 구성 하는 백업 설정이 유지 됩니다 및 백업 일정을 유지 합니다. 이 작업에는 간단히 나중에 백업이 표시 하지 않으려면 scheduler 알려 줍니다. 

![Azure Stack-백업 예약 하는 사용 하지 않도록 설정](media/azure-stack-backup/disable-auto-backup.png)

나중에 예약 된 백업이 사용할 수 없습니다 확인 **Essentials**:

![Azure Stack-백업을 사용 하지 않도록 설정 되었는지 확인 합니다.](media/azure-stack-backup/confirm-disable.png)

클릭할 **자동 백업을 사용 하도록 설정** 향후 백업이 예약된 된 시간에 시작 되도록 스케줄러에 알림을 보내야 합니다. 

![Azure Stack 백업 예약 사용](media/azure-stack-backup/enable-auto-backup.png)


> [!Note]  
> 1807를 업데이트 하기 전에 백업 인프라를 구성한 경우 자동 백업 비활성화 됩니다. 이러한 방식으로 외부 작업 예약 엔진에 의해 시작 되는 백업을 사용 하 여 Azure Stack에 의해 시작 백업을 충돌 하지 않습니다. 모든 외부 작업 스케줄러를 비활성화 하면 면 클릭할 **자동 백업 사용**합니다.


## <a name="next-steps"></a>다음 단계

- 백업을 실행 하는 방법을 알아봅니다. 참조 [Azure Stack 백업](azure-stack-backup-back-up-azure-stack.md )합니다.
- 백업 실행 되었는지 확인 하는 방법을 알아봅니다. 참조 [관리 포털에서 완료할 확인 백업](azure-stack-backup-back-up-azure-stack.md)합니다.
