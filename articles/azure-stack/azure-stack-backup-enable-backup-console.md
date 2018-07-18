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
ms.date: 07/11/2018
ms.author: jeffgilb
ms.openlocfilehash: fba04490aca4c7123ca478ae07a5f0c865d9a826
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968700"
---
# <a name="enable-backup-for-azure-stack-from-the-administration-portal"></a>관리 포털에서 Azure Stack에 대 한 백업을 사용 하도록 설정
Azure Stack 백업을 생성할 수 있도록 관리 포털을 통해 인프라 Backup 서비스를 사용 합니다. 경우 클라우드 복구를 사용 하 여 환경을 복원 하려면 이러한 백업에 사용할 수 있습니다 [치명적인 오류](.\azure-stack-backup-recover-data.md)합니다. 클라우드 복구의 목적은 운영자와 사용자 수에 다시 로그인 포털 복구가 완료 된 후 확인 합니다. 사용자 구독 역할 기반 액세스 권한 및 역할, 원래 계획, 제품 및 이전에 정의 된 계산, 저장소 및 네트워크 할당량을 비롯 하 여 복원 해야 합니다.

그러나 IaaS Vm, 네트워크 구성 및 저장소 계정, blob, 테이블와 같은 저장소 리소스 인프라 백업 서비스를 백업 하지 않습니다. 하 고 등등으로 클라우드 복구 후 로그인 하는 사용자가 완료 되도록 하면서 이전에 기존의 모든 표시 되지 않습니다. 리소스입니다. 플랫폼 (PaaS) 서비스로 리소스 및 데이터도 백업 되지 서비스입니다. 

관리자와 사용자가 백업 및 인프라 백업 프로세스에서 IaaS 및 PaaS 리소스를 개별적으로 복원 하는 일을 담당 합니다. IaaS 및 PaaS 리소스에 대 한 자세한 내용은 다음 링크를 참조 합니다.

- [Virtual Machines](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-protect)
- [App Service](https://docs.microsoft.com/azure/app-service/web-sites-backup)
- [SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview)


> [!Note]  
> 콘솔을 통해 backup을 사용 하기 전에 백업 서비스를 구성 해야 합니다. PowerShell을 사용 하 여 백업 서비스를 구성할 수 있습니다. 자세한 내용은 [PowerShell 사용 하 여 Azure Stack에 대 한 백업을 사용 하도록 설정](azure-stack-backup-enable-backup-powershell.md)합니다.

## <a name="enable-backup"></a>백업을 사용 하도록 설정

1. Azure Stack 관리 포털을 엽니다 [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external)합니다.
2. 선택 **더 많은 서비스** > **인프라 백업**합니다. 선택 **Configuration** 에 **인프라 백업** 블레이드입니다.

    ![Azure Stack 백업 컨트롤러 설정](media\azure-stack-backup\azure-stack-backup-settings.png)에서도 확인할 수 있습니다.

3. 경로를 입력 합니다 **백업 저장소 위치**합니다. 별도 장치에서 호스트 되는 파일 공유 경로 대 한 범용 명명 규칙 (UNC) 문자열을 사용 합니다. UNC 문자열로 공유 파일 또는 장치와 같은 리소스의 위치를 지정합니다. 서비스에 대 한 IP 주소를 사용할 수 있습니다. 재해 발생 후 백업 데이터의 가용성을 보장 하려면 장치 별도 위치에 있어야 합니다.
    > [!Note]  
    > 사용자 환경에서 엔터프라이즈 환경에 Azure Stack 인프라 네트워크에서 이름 확인을 지 원하는 경우에 IP 대신 FQDN을 사용할 수 있습니다.
4. 형식 합니다 **Username** 파일 읽기 및 쓰기를 충분 한 액세스를 사용 하 여 도메인 및 사용자를 사용 하 여 합니다. 예: `Contoso\backupshareuser`
5. 형식 합니다 **암호** 사용자에 대 한 합니다.
5. 암호를 다시 입력 **암호 확인**합니다.
6. 미리 공유한 키를 제공 합니다 **암호화 키** 상자입니다. 백업 파일이이 키를 사용 하 여 암호화 됩니다. 이 키는 안전한 위치에 저장 해야 합니다. 을 처음으로이 키를 설정 하거나 나중에 키를 회전 되 면이 키는이 인터페이스에서 확인할 수 없습니다. 미리 공유한 키를 생성 하는 자세한 지침은, 스크립트의 따릅니다 [PowerShell 사용 하 여 Azure Stack에 대 한 백업을 사용 하도록 설정](azure-stack-backup-enable-backup-powershell.md)합니다.
7. 선택 **확인** 컨트롤러 백업 설정을 저장 합니다.

Azure Stack Tools를 다운로드 한 후 PowerShell cmdlet을 실행 해야 백업을 실행할 **시작 AzSBackup** Azure Stack 관리 노드. 자세한 내용은 [Azure Stack 백업](azure-stack-backup-back-up-azure-stack.md )합니다.

## <a name="next-steps"></a>다음 단계

- 백업을 실행 하는 방법을 알아봅니다. 참조 [Azure Stack 백업](azure-stack-backup-back-up-azure-stack.md )합니다.
- 백업 실행 되었는지 확인 하는 방법을 알아봅니다. 참조 [관리 포털에서 완료할 확인 백업](azure-stack-backup-back-up-azure-stack.md)합니다.
