---
title: 포함 파일
description: 포함 파일
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: a142de62f1ab7046d47c29753863b16864d97536
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075629"
---
서비스 데이터 암호화 키는 StorSimple 관리자 서비스에서 StorSimple 디바이스로 전송된 스토리지 계정 자격 증명 등의 기밀 고객 데이터를 암호화하는 데 사용됩니다. IT 조직에 스토리지 디바이스에 대한 키 회전 정책이 있는 경우 이러한 키를 주기적으로 변경해야 합니다. 키 변경 프로세스는 StorSimple Manager 서비스에서 관리하는 디바이스가 단일 디바이스인지, 여러 디바이스인지에 따라 약간 달라질 수 있습니다. 자세한 내용은 [StorSimple 보안 및 데이터 보호](../articles/storsimple/storsimple-8000-security.md)로 이동합니다.

서비스 데이터 암호화 키는 3단계 프로세스에 따라 변경됩니다.

1. Azure Resource Manager에 Windows PowerShell 스크립트를 사용하여 서비스 데이터 암호화 키를 변경하도록 디바이스에 권한을 부여합니다.
2. StorSimple용 Windows PowerShell을 사용하여 서비스 데이터 암호화 키 변경을 시작합니다.
3. 둘 이상의 StorSimple 디바이스를 사용하는 경우에는 다른 디바이스에서 서비스 데이터 암호화 키를 업데이트합니다.

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>1단계: Windows PowerShell 스크립트를 사용하여 디바이스에 서비스 데이터 암호화 키를 변경할 권한을 부여합니다.
일반적으로 디바이스 관리자는 서비스 관리자가 디바이스에 서비스 데이터 암호화 키를 변경할 수 있는 권한을 부여하도록 요청합니다. 그러면 서비스 관리자는 디바이스에 키를 변경할 수 있는 권한을 부여합니다.

Azure Resource Manager 기반 스크립트를 사용하여 이 단계를 수행합니다. 서비스 관리자는 권한을 부여할 수 있는 디바이스를 선택할 수 있습니다. 그러면 디바이스에 서비스 데이터 암호화 키 변경 프로세스를 시작할 수 있는 권한이 부여됩니다. 

스크립트를 사용하는 방법에 대한 자세한 내용은 [Authorize-ServiceEncryptionRollover.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1)로 이동합니다.

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>서비스 데이터 암호화 키를 변경할 권한이 부여될 수 있는 디바이스
서비스 데이터 암호화 키 변경을 시작할 권한을 부여받으려면 디바이스가 다음 조건을 충족해야 합니다.

* 디바이스는 온라인 상태여야 서비스 데이터 암호화 키 변경 권한 부여 대상이 됩니다.
* 키 변경이 시작되지 않은 경우 30분 후에 동일한 디바이스에 권한을 다시 부여할 수 있습니다.
* 이전에 권한을 부여받은 디바이스에서 키 변경을 시작하지 않은 경우 다른 디바이스에 권한을 부여할 수 있습니다. 새 디바이스에 권한을 부여하면 이전 디바이스는 변경을 시작할 수 없습니다.
* 서비스 데이터 암호화 키 롤오버가 진행 중인 동안에는 디바이스에 권한을 부여할 수 없습니다.
* 서비스에 등록된 디바이스 중 일부만 롤오버한 경우에는 디바이스에 권한을 부여할 수 있습니다. 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>2단계: StorSimple용 Windows PowerShell을 사용하여 서비스 데이터 암호화 키 변경 시작
이 단계는 권한이 부여된 StorSimple 디바이스의 StorSimple용 Windows PowerShell 인터페이스에서 수행됩니다.

> [!NOTE]
> 키 롤오버가 완료될 때까지 StorSimple Manager 서비스의 Azure Portal에서 수행할 수 있는 작업은 없습니다.


디바이스 직렬 콘솔을 사용하여 Windows PowerShell 인터페이스에 연결하는 경우 다음 단계를 수행합니다.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>서비스 데이터 암호화 키 변경을 시작하려면
1. 옵션 1을 선택하여 모든 권한으로 로그온합니다.
2. 명령 프롬프트에 다음을 입력합니다.
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. cmdlet이 성공적으로 완료되면 새 서비스 데이터 암호화 키를 얻을 수 있습니다. 이 키를 복사하고 저장해 두었다가 이 프로세스의 3단계에서 사용합니다. 이 키는 StorSimple 관리자 서비스에 등록된 나머지 모든 디바이스를 업데이트하는 데 사용됩니다.
   
   > [!NOTE]
   > 이 프로세스는 StorSimple 디바이스에 권한을 부여한 후 4시간 이내에 시작되어야 합니다.
   > 
   > 
   
   이 새 키는 서비스로 전송되어 서비스에 등록된 모든 디바이스에 푸시됩니다. 서비스 대시보드에 경고가 표시됩니다. 서비스는 등록된 디바이스에서 모든 작업을 사용할 수 없도록 설정합니다. 그러면 디바이스 관리자는 다른 디바이스에서 서비스 데이터 암호화 키를 업데이트해야 합니다. 그러나 I/O(클라우드로 데이터를 보내는 호스트)는 중단되지 않습니다.
   
   단일 디바이스를 서비스에 등록한 경우 이제 롤오버 프로세스가 완료되었으므로 다음 단계를 건너뛸 수 있습니다. 여러 디바이스를 서비스에 등록한 경우 3단계를 진행합니다.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>3단계: 다른 StorSimple 디바이스에서 서비스 데이터 암호화 키 업데이트
여러 디바이스를 StorSimple 관리자 서비스에 등록한 경우 StorSimple 디바이스의 Windows PowerShell 인터페이스에서 다음 단계를 수행해야 합니다. StorSimple Manager 서비스로 등록된 나머지 StorSimple 디바이스를 업데이트하는 데 2단계에서 가져온 키를 사용해야 합니다.

다음 단계를 수행하여 디바이스에서 서비스 데이터 암호화를 업데이트합니다.

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>물리적 디바이스에서 서비스 데이터 암호화 키를 업데이트하려면
1. StorSimple용 Windows PowerShell을 사용하여 콘솔에 연결합니다. 옵션 1을 선택하여 모든 권한으로 로그온합니다.
2. 명령 프롬프트에 `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`를 입력합니다.
3. [2단계: StorSimple용 Windows PowerShell을 사용하여 서비스 데이터 암호화 키 변경 시작](#to-initiate-the-service-data-encryption-key-change)에서 얻은 서비스 데이터 암호화 키를 제공합니다.

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>모든 8010/8020 클라우드 어플라이언스에서 서비스 데이터 암호화 키를 업데이트하려면
1. [Update-CloudApplianceServiceEncryptionKey.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1) PowerShell 스크립트를 다운로드하여 설치합니다. 
2. PowerShell을 열고 명령 프롬프트에서 다음을 입력 합니다. `Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

이 스크립트는 디바이스 관리자 아래의 모든 8010/8020 클라우드 어플라이언스에 서비스 데이터 암호화 키가 설정되었는지 확인합니다.