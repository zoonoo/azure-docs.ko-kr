---
title: "서버 제거 및 보호 사용 안 함 | Microsoft Docs"
description: "이 문서에서는 사이트 복구 자격 증명 모음에서 서버 등록을 취소하고 가상 컴퓨터 및 물리적 서버의 보호를 사용하지 않도록 설정하는 방법을 설명합니다."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: ef1f31d5-285b-4a0f-89b5-0123cd422d80
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 03/27/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: f57c88cbace41af233f542880c6199b3e278700e
ms.openlocfilehash: c8d893dbac1a4f6cb3f05f857e186bca155e5865
ms.lasthandoff: 01/05/2017


---

# <a name="remove-servers-and-disable-protection"></a>서버 제거 및 보호 사용 안 함

Azure Site Recovery 서비스는 BCDR(비즈니스 연속성 및 재해 복구 개선) 전략에 기여하는 서비스로 가상 컴퓨터 및 물리적 서버의 복제, 장애 조치(failover) 및 복구(failback)를 처리합니다. 컴퓨터는 Azure 또는 보조 온-프레미스 데이터 센터로 복제할 수 있습니다. 빠른 개요를 알아보려면 [Azure Site Recovery란?](site-recovery-overview.md)

이 문서에는 Azure Portal의 Recovery Services 자격 증명 모음에서 서버의 등록을 취소하는 방법 및 Site Recovery를 통해 보호되는 컴퓨터에 대한 보호를 사용하지 않도록 설정하는 방법을 설명합니다.

이 문서의 하단 또는 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견이나 질문을 게시합니다.

## <a name="unregister-a-connected-configuration-server"></a>연결된 구성 서버 등록 취소

VMware VM 또는 Windows/Linux 물리적 서버를 Azure에 복제하는 경우 다음과 같이 자격 증명 모음에서 연결된 구성 서버를 등록 취소할 수 있습니다.

1. 컴퓨터 보호 사용 안 함. **보호된 항목** > **복제된 항목**을 클릭하고 컴퓨터를 마우스 오른쪽 단추로 클릭한 후 > **삭제**를 클릭합니다.
2. 모든 정책 연결 해제. **사이트 복구 인프라** > **VMWare 및 물리적 컴퓨터** > **복제 정책**에서 연결된 정책을 두 번 클릭합니다. 구성 서버를 마우스 오른쪽 단추로 클릭하고 > **연결 해제**를 클릭합니다.
3. 추가 온-프레미스 프로세스 또는 마스터 대상 서버를 제거합니다. **사이트 복구 인프라** > **VMWare 및 물리적 컴퓨터** > **구성 서버**에서 서버를 마우스 오른쪽 단추로 클릭하고 > **삭제**를 클릭합니다.
4. 구성 서버를 삭제합니다.
5. 마스터 대상 서버에서 실행되는 이동성 서비스를 수동으로 제거합니다(별도의 서버이거나 구성 서버에서 실행 중).
6. 추가 프로세스 서버를 제거합니다.
7. 구성 서버를 제거합니다.
8. 구성 서버에서 Site Recovery에 의해 설치된 MySQL 인스턴스를 제거합니다.
9. 구성 서버의 레지스트리에서 ``HKEY_LOCAL_MACHINE\Software\Microsoft\Azure Site Recovery`` 키를 삭제합니다.

## <a name="unregister-a-unconnected-configuration-server"></a>연결되지 않은 구성 서버 등록 취소

VMware VM 또는 Windows/Linux 물리적 서버를 Azure에 복제하는 경우 다음과 같이 자격 증명 모음에서 연결되지 않은 구성 서버를 등록 취소할 수 있습니다.

1. 컴퓨터 보호 사용 안 함. **보호된 항목** > **복제된 항목**을 클릭하고 컴퓨터를 마우스 오른쪽 단추로 클릭한 후 > **삭제**를 클릭합니다. **컴퓨터 관리 중지**를 선택합니다.
2. 추가 온-프레미스 프로세스 또는 마스터 대상 서버를 제거합니다. **사이트 복구 인프라** > **VMWare 및 물리적 컴퓨터** > **구성 서버**에서 서버를 마우스 오른쪽 단추로 클릭하고 > **삭제**를 클릭합니다.
3. 구성 서버를 삭제합니다.
4. 마스터 대상 서버에서 실행되는 이동성 서비스를 수동으로 제거합니다(별도의 서버이거나 구성 서버에서 실행 중).
5. 추가 프로세스 서버를 제거합니다.
6. 구성 서버를 제거합니다.
7. 구성 서버에서 Site Recovery에 의해 설치된 MySQL 인스턴스를 제거합니다.
8. 구성 서버의 레지스트리에서 ``HKEY_LOCAL_MACHINE\Software\Microsoft\Azure Site Recovery`` 키를 삭제합니다.

## <a name="unregister-a-connected-vmm-server"></a>연결된 VMM 서버 등록 취소

Azure에 연결된 경우 VMM 서버의 등록을 취소하는 것이 가장 좋습니다. 이렇게 하면 VMM 서버(및 페어링된 클라우드가 있는 다른 VMM 서버)의 설정이 알맞게 정리됩니다. 연결에 영구적인 문제가 있는 경우에만 연결되지 않은 서버를 제거해야 합니다. VMM 서버가 연결되지 않은 경우 스크립트를 직접 실행하여 설정을 정리해야 합니다.

1. 제거할 VMM에서 클라우드에 VM 복제를 중지합니다.
2. 삭제할 VMM 서버에서 클라우드에 사용된 모든 네트워크 매핑을 삭제합니다. **사이트 복구 인프라** > **System Center VMM** > **네트워크 매핑**에서 네트워크 매핑을 마우스 오른쪽 단추로 클릭하고 > **삭제**를 클릭합니다.
3. 제거할 VMM 서버의 클라우드에서 복제 정책을 연결 해제합니다.  **사이트 복구 인프라** > **System Center VMM** >  **복제 정책**에서 연결된 정책을 두 번 클릭합니다. 클라우드를 마우스 오른쪽 단추로 클릭하고 > **연결 해제**를 클릭합니다.
4. VMM 서버 또는 활성 VMM 노드를 삭제합니다. **사이트 복구 인프라** > **System Center VMM** > **VMM 서버**에서 서버를 마우스 오른쪽 단추로 클릭하고 > **삭제**를 클릭합니다.
5. VMM 서버에서 공급자를 수동으로 제거합니다. 클러스터가 있는 경우 모든 노드에서 제거합니다.
6. Azure에 복제하는 경우 삭제된 클라우드의 Hyper-V 호스트에서 Microsoft Recovery Services 에이전트를 수동으로 제거합니다.



### <a name="unregister-an-unconnected-vmm-server"></a>연결되지 않은 VMM 서버 등록 취소

1. 제거할 VMM에서 클라우드에 VM 복제를 중지합니다.
2. 삭제할 VMM 서버에서 클라우드에 사용된 모든 네트워크 매핑을 삭제합니다. **사이트 복구 인프라** > **System Center VMM** > **네트워크 매핑**에서 네트워크 매핑을 마우스 오른쪽 단추로 클릭하고 > **삭제**를 클릭합니다.
3. VMM 서버의 ID를 메모해 둡니다.
4. 제거할 VMM 서버의 클라우드에서 복제 정책을 연결 해제합니다.  **사이트 복구 인프라** > **System Center VMM** >  **복제 정책**에서 연결된 정책을 두 번 클릭합니다. 클라우드를 마우스 오른쪽 단추로 클릭하고 > **연결 해제**를 클릭합니다.
5. VMM 서버 또는 활성 노드를 삭제합니다. **사이트 복구 인프라** > **System Center VMM** > **VMM 서버**에서 서버를 마우스 오른쪽 단추로 클릭하고 > **삭제**를 클릭합니다.
6. VMM 서버에서 [정리 스크립트](http://aka.ms/asr-cleanup-script-vmm)를 다운로드하여 실행합니다. 기본(LocalMachine) 범위에 대한 실행 정책을 변경하려면 **관리자 권한으로 실행** 옵션으로 PowerShell을 엽니다. 스크립트에서 제거할 VMM 서버의 ID를 지정합니다. 이 스크립트는 서버에서 등록 및 클라우드 페어링을 제거합니다.
5. 제거할 VMM 서버에서 클라우드와 페어링되는 클라우드가 포함된 다른 모든 VMM 서버에서 정리 스크립트를 실행합니다.
6. 공급자가 설치된 다른 모든 수동 VMM 클러스터 노드에서 정리 스크립트를 실행합니다.
7. VMM 서버에서 공급자를 수동으로 제거합니다. 클러스터가 있는 경우 모든 노드에서 제거합니다.
8. Azure에 복제하는 경우 삭제된 클라우드의 Hyper-V 호스트에서 Microsoft Recovery Services 에이전트를 제거할 수 있습니다.

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>Hyper-V 사이트에서 Hyper-V 호스트 등록 취소

VMM에 의해 관리되지 않는 Hyper-V 호스트가 Hyper-V 사이트로 수집됩니다. 다음과 같이 Hyper-V 사이트에서 호스트를 제거합니다.

1. 호스트에 있는 Hyper-V VM에 대한 복제를 사용하지 않도록 설정합니다.
2. Hyper-V 사이트에 대한 정책을 연결 해제합니다. **사이트 복구 인프라** > **Hyper-V 사이트** >  **복제 정책**에서 연결된 정책을 두 번 클릭합니다. 사이트를 마우스 오른쪽 단추로 클릭하고 > **연결 해제**를 클릭합니다.
3. Hyper-V 호스트를 삭제합니다. **사이트 복구 인프라** > **System Center VMM** > **Hyper-V 호스트**에서 서버를 마우스 오른쪽 단추로 클릭하고 > **삭제**를 클릭합니다.
4. 모든 호스트가 제거되었으면 Hyper-V 사이트를 삭제합니다. **사이트 복구 인프라** > **System Center VMM** > **Hyper-V 사이트**에서 사이트를 마우스 오른쪽 단추로 클릭하고 > **삭제**를 클릭합니다.
5. 제거한 각 Hyper-V 호스트에서 다음 스크립트를 실행합니다. 스크립트는 서버에서 설정을 정리하고 자격 증명 모음에서 설정을 등록 취소합니다.


        `` pushd .
        try
        {
             $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
             $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
             $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
             $isAdmin=$principal.IsInRole($administrators)
             if (!$isAdmin)
             {
                "Please run the script as an administrator in elevated mode."
                $choice = Read-Host
                return;       
             }

            $error.Clear()    
            "This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
            $choice =  Read-Host

            if (!($choice -eq 'Y' -or $choice -eq 'y'))
            {
            "Stopping cleanup."
            return;
            }

            $serviceName = "dra"
            $service = Get-Service -Name $serviceName
            if ($service.Status -eq "Running")
            {
                "Stopping the Azure Site Recovery service..."
                net stop $serviceName
            }

            $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
            $registrationPath = $asrHivePath + '\Registration'
            $proxySettingsPath = $asrHivePath + '\ProxySettings'
            $draIdvalue = 'DraID'

            if (Test-Path $asrHivePath)
            {
                if (Test-Path $registrationPath)
                {
                    "Removing registration related registry keys."    
                    Remove-Item -Recurse -Path $registrationPath
                }

                if (Test-Path $proxySettingsPath)
            {
                    "Removing proxy settings"
                    Remove-Item -Recurse -Path $proxySettingsPath
                }

                $regNode = Get-ItemProperty -Path $asrHivePath
                if($regNode.DraID -ne $null)
                {            
                    "Removing DraId"
                    Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
                }
                "Registry keys removed."
            }

            # First retrive all the certificates to be deleted
            $ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
            # Open a cert store object
            $store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
            $store.Open('ReadWrite')
            # Delete the certs
            "Removing all related certificates"
            foreach ($cert in $ASRcerts)
            {
                $store.Remove($cert)
            }
        }catch
        {    
            [system.exception]
            Write-Host "Error occured" -ForegroundColor "Red"
            $error[0]
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd``



## <a name="disable-protection-for-a-vmware-vm-or-physical-server"></a>VMware VM 또는 물리적 서버에 대한 보호를 사용 안 함

1. **보호된 항목** > **복제된 항목**을 클릭하고 컴퓨터를 마우스 오른쪽 단추로 클릭한 후 > **삭제**를 클릭합니다.
2. **컴퓨터 제거**에서 다음 중 하나를 선택합니다.
    - **컴퓨터에 대한 보호 사용 안 함(권장)**. 컴퓨터 복제를 중지하려면 이 옵션을 사용합니다. Site Recovery 설정은 자동으로 정리됩니다. 다음 상황에서만 이 옵션이 표시됩니다.
        - **VM 볼륨을 크기 조정했음** - 볼륨 크기를 조정하면 가상 컴퓨터 상태가 심각해집니다. 이 옵션을 선택하면 Azure의 복구 지점을 유지하는 동시에 보호가 비활성화됩니다. 컴퓨터에 보호를 사용하도록 다시 설정할 경우 크기 조정된 볼륨의 데이터가 Azure로 전송됩니다.
        - **최근에 장애 조치를 실행했음** - 장애 조치를 실행하여 환경을 테스트한 후 이 옵션을 선택하여 온-프레미스 컴퓨터에서 보호를 다시 시작합니다. 그러면 각각의 가상 컴퓨터가 비활성화되며, 다시 보호를 사용하도록 설정해야 합니다. 이 설정으로 컴퓨터를 비활성화할 경우 Azure의 복제 가상 컴퓨터에 영향을 미치지 않습니다. 컴퓨터에서 모바일 서비스를 제거하지 마세요.
    - **컴퓨터 관리 중지**. 이 옵션을 선택하면 컴퓨터가 자격 증명 모음에서만 제거됩니다. 컴퓨터에 대한 온-프레미스 보호 설정은 영향을 받지 않습니다. 컴퓨터의 설정을 제거하고 Azure 구독에서 컴퓨터를 제거하려면 이동성 서비스를 제거하여 설정을 제거해야 합니다.

## <a name="disable-protection-for-a-hyper-v-vm-in-a-vmm-cloud"></a>VMM 클라우드에서 Hyper-V VM에 대한 보호 사용 안 함

1. **보호된 항목** > **복제된 항목**을 클릭하고 컴퓨터를 마우스 오른쪽 단추로 클릭한 후 > **삭제**를 클릭합니다.
2. **컴퓨터 제거**에서 다음 중 하나를 선택합니다.

    - **컴퓨터에 대한 보호 사용 안 함(권장)**. 컴퓨터 복제를 중지하려면 이 옵션을 사용합니다. Site Recovery 설정은 자동으로 정리됩니다.
    - **컴퓨터 관리 중지**. 이 옵션을 선택하면 컴퓨터가 자격 증명 모음에서만 제거됩니다. 컴퓨터에 대한 온-프레미스 보호 설정은 영향을 받지 않습니다. 컴퓨터의 설정을 제거하고 Azure 구독에서 컴퓨터를 제거하려면 아래 명령을 사용하여 설정을 수동으로 제거해야 합니다. 가상 컴퓨터와 해당 하드 디스크를 삭제하도록 선택하면 대상 위치에서 제거됩니다.

### <a name="clean-up-protection-settings---replication-to-a-secondary-vmm-site"></a>보호 설정 정리 - 보조 VMM 사이트로 복제

**컴퓨터 관리 중지**를 선택했고 보조 사이트로 복제하는 경우 주 서버에서 이 스크립트를 실행하여 주 가상 컴퓨터에 대한 설정을 정리합니다. VMM 콘솔에서 VMM PowerShell 콘솔을 열려면 PowerShell 단추를 클릭합니다. SQLVM1을 가상 컴퓨터의 이름으로 바꿉니다.

         ``$vm = get-scvirtualmachine -Name "SQLVM1"
         Set-SCVirtualMachine -VM $vm -ClearDRProtection``
2. 보조 VMM 서버에서 이 스크립트를 실행하여 보조 가상 컴퓨터의 설정을 정리합니다.

        ``$vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force``
3. 두 번째 VMM 서버에서 Hyper-V 호스트 서버에서 가상 컴퓨터를 새로 고쳐 보조 VM이 VMM 콘솔에서 다시 감지되도록 합니다.
4. 위의 단계는 VMM 서버에서 복제 설정을 정리합니다. 가상 컴퓨터에 대한 복제를 중지하려면 주 및 보조 VM에서 다음 스크립트를 실행합니다. SQLVM1을 가상 컴퓨터의 이름으로 바꿉니다.

        ``Remove-VMReplication –VMName “SQLVM1”``

### <a name="clean-up-protection-settings---replication-to-azure"></a>보호 설정 정리 - Azure로 복제

1. **컴퓨터 관리 중지**를 선택했고 Azure로 복제하는 경우 VMM 콘솔에서 PowerShell을 사용하여 원본 VMM 서버에서 이 스크립트를 실행합니다.
        ``$vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection``
2. 위의 단계는 VMM 서버에서 복제 설정을 지웁니다. Hyper-V 호스트 서버에서 실행하는 가상 컴퓨터의 복제를 중지하려면 이 스크립트를 실행합니다. SQLVM1을 가상 컴퓨터의 이름으로 바꾸고 host01.contoso.com을 Hyper-V 호스트 서버의 이름으로 바꿉니다.

        ``$vmName = "SQLVM1"
        $hostName  = "host01.contoso.com"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
        $replicationService.RemoveReplicationRelationship($vm.__PATH)``


## <a name="disable-protection-for-a-hyper-v-vm-in-a-hyper-v-site"></a>Hyper-V 사이트에서 Hyper-V VM에 대한 보호 사용 안 함

VMM 서버 없이 Hyper-V VM을 Azure에 복제하는 경우 이 절차를 사용합니다.

1. **보호된 항목** > **복제된 항목**을 클릭하고 컴퓨터를 마우스 오른쪽 단추로 클릭한 후 > **삭제**를 클릭합니다.
2. **컴퓨터 제거**에서 다음 옵션을 선택할 수 있습니다.

   - **컴퓨터에 대한 보호 사용 안 함(권장)**. 컴퓨터 복제를 중지하려면 이 옵션을 사용합니다. Site Recovery 설정은 자동으로 정리됩니다.
   - **컴퓨터 관리 중지**. 이 옵션을 선택하면 컴퓨터가 자격 증명 모음에서만 제거됩니다. 컴퓨터에 대한 온-프레미스 보호 설정은 영향을 받지 않습니다. 컴퓨터의 설정을 제거하고 Azure 구독에서 가상 컴퓨터를 제거하려면 설정을 수동으로 제거해야 합니다. 가상 컴퓨터와 해당 하드 디스크를 삭제하도록 선택하면 대상 위치에서 제거됩니다.
3. **컴퓨터 관리 중지**를 선택한 경우, 원본 Hyper-V 호스트 서버에서 이 스크립트를 실행하여 가상 컴퓨터에 대한 복제를 제거합니다. SQLVM1을 가상 컴퓨터의 이름으로 바꿉니다.

        $vmName = "SQLVM1"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
        $replicationService.RemoveReplicationRelationship($vm.__PATH)

