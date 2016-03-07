<properties
	pageTitle="서버 제거 및 보호 사용 안 함 | Microsoft Azure" 
	description="이 문서에서는 사이트 복구 자격 증명 모음에서 서버 등록을 취소하고 가상 컴퓨터 및 물리적 서버의 보호를 사용하지 않도록 설정하는 방법을 설명합니다." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="02/22/2016" 
	ms.author="raynew"/>

# 서버 제거 및 보호 사용 안 함

Azure Site Recovery 서비스는 가상 컴퓨터와 물리적 서버의 복제, 장애 조치(Failover) 및 복구를 오케스트레이션하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다. 컴퓨터는 Azure 또는 보조 온-프레미스 데이터 센터로 복제할 수 있습니다. 빠른 개요를 알아보려면 [Azure Site Recovery란?](site-recovery-overview.md)을 확인하세요.

## 개요

이 문서에는 사이트 복구 자격 증명 모음에서 서버의 등록을 취소 하는 방법 및 사이트 복구를 통해 보호되는 가상 컴퓨터에 대 한 보호를 사용하지 않도록 설정하는 방법을 설명합니다.

이 문서의 하단 또는 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에서 의견이나 질문을 게시합니다.

## VMM 서버 등록 취소

Azure Site Recovery 포털의 **서버**에서 해당 서버를 삭제하여 자격 증명 모음에서 VMM 서버의 등록을 취소합니다. 다음 사항에 유의하세요.

-  **VMM 서버에 연결된 경우**: Azure에 연결된 경우 VMM 서버의 등록을 취소하는 것이 좋습니다. 이렇게 하면 온-프레미스 VMM 서버 및 연결된 VMM 서버(삭제하려는 서버의 클라우드에 매핑되는 클라우드를 포함하는 VMM 서버)가 적절하게 정리됩니다. 연결에 영구적인 문제가 있는 경우에만 연결되지 않은 서버를 제거하는 것이 좋습니다.
- **VMM 서버가 연결되지 않은 경우**: 삭제할 때 VMM 서버기 연결되지 않은 경우 정리 작업을 수행하는 스크립트를 수동으로 실행해야 합니다. 이 스크립트는 [Microsoft 갤러리](https://gallery.technet.microsoft.com/scriptcenter/Cleanup-Script-for-Windows-95101439)에서 사용할 수 있습니다. 수동 정리 프로세스를 완료하기 위해 서버의 VMM ID를 기록해 둡니다.
- **VMM 서버가 클러스터에 있는 경우 **클러스터에 배포되는 VMM 서버 등록을 취소해야 하는 경우 다음을 수행합니다.

	- 서버에 연결되는 경우 **서버** 탭에서 연결된 VMM 서버를 삭제합니다. 서버에서 공급자를 제거하려면 모든 클러스터 노드에 로그인하 고 제어판에서 제거합니다. 등록 항목을 삭제하려면 클러스터의 모든 패시브 노드에서 이전 섹션에서 참조하는 정리 스크립트를 실행합니다.
	- 서버에 연결되지 않은 경우 모든 클러스터 노드에서 정리 스크립트를 실행해야 합니다.

### 연결되지 않은 VMM 서버 등록 취소

제거할 VMM 서버:

1. Azure 포털에서 VMM 서버 등록을 취소합니다.
2. VMM 서버에서 정리 스크립트를 다운로드합니다.
3. 기본(LocalMachine) 범위에 대한 실행 정책을 변경하려면 관리자 권한으로 실행 옵션으로 PowerShell을 엽니다.
4. 스크립트의 지침을 따르세요. 

제거 중인 서버의 클라우드와 쌍을 이루는 클라우드가 있는 VMM 서버:

1. 정리 스크립트를 실행하고 2~4단계를 따릅니다.
2. 등록된 VMM 서버에 대한 VMM ID를 지정합니다. 
3. 이 스크립트는 VMM 서버 및 클라우드 페어링 정보에 대한 등록 정보를 제거합니다.


## Hyper-V 사이트에서 Hyper-V 서버 등록 취소

Hyper-V 사이트의 Hyper-V 서버에 있는 가상 컴퓨터를 보호하기 위해 Azure Site Recovery가 배포되면(VMM 서버 없음) 다음과 같이 자격 증명 모음에서 Hyper-V 서버 등록을 취소할 수 있습니다.

1. Hyper-V 서버에 있는 가상 컴퓨터에 보호 기능을 사용하지 않도록 설정합니다.
2. Azure Site Recovery 포털의 **서버** 탭에서 서버 > 삭제를 선택합니다. 이 작업을 수행할 때 Azure에 서버를 연결할 필요는 없습니다.
3. 서버에서 설정을 정리하고 자격 증명 모음에서 등록 취소하도록 다음 스크립트를 실행합니다. 

	    pushd .
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
	    popd


## Hyper-V 가상 컴퓨터 보호 중지

Hyper-V 가상 컴퓨터 보호를 중지하려면 보호를 제거해야 합니다. 보호를 제거하는 방식에 따라, 컴퓨터에서 보호 설정을 수동으로 삭제해야 할 수 있습니다.

### 보호 제거

1. 클라우드 속성의 **가상 컴퓨터** 탭에서 가상 컴퓨터 > **제거**를 선택합니다.
2. **가상 컴퓨터 제거 확인** 페이지에 몇 가지 선택 사항이 있습니다.

	- **보호 사용 안 함**—이 옵션을 사용하도록 설정하고 저장하면 가상 컴퓨터가 더 이상 Site Recovery를 통해 보호되지 않습니다. 가상 컴퓨터의 보호 설정이 자동으로 정리됩니다.
	- **자격 증명 모음에서 제거**—이 옵션을 선택하면 가상 컴퓨터가 Site Recovery 자격 증명 모음에서만 제거됩니다. 가상 컴퓨터에 대한 온-프레미스 보호 설정은 영향을 받지 않습니다. 보호 설정을 제거하고 Azure 구독에서 가상 컴퓨터를 제거하려면 아래 지침에 따라 수동으로 설정을 제거해야 합니다.

가상 컴퓨터와 해당 하드 디스크를 삭제하도록 선택하면 대상 위치에서 제거됩니다.

### 보호 설정을 수동으로 정리(VMM 사이트간)

**가상 컴퓨터 관리 중지**를 선택한 경우 설정을 수동으로 정리합니다.

1. 기본 서버의 VMM 콘솔에서 이 스크립트를 실행하여 기본 가상 컴퓨터의 설정을 정리합니다. VMM 콘솔에서 VMM PowerShell 콘솔을 열려면 PowerShell 단추를 클릭합니다. SQLVM1을 가상 컴퓨터의 이름으로 바꿉니다.

	     $vm = get-scvirtualmachine -Name "SQLVM1"
	     Set-SCVirtualMachine -VM $vm -ClearDRProtection

2. 보조 VMM 서버에서 이 스크립트를 실행하여 보조 가상 컴퓨터의 설정을 정리합니다.

	    $vm = get-scvirtualmachine -Name "SQLVM1"
	    Remove-SCVirtualMachine -VM $vm -Force

3. 두 번째 VMM 서버에서 스크립트를 실행한 후 Hyper-V 호스트 서버에서 가상 컴퓨터를 새로 고쳐 보조 가상 컴퓨터가 VMM 콘솔에서 다시 감지되도록 합니다.
4. 위의 단계는 VMM 서버에서 복제 설정만 지웁니다. 가상 컴퓨터에 대한 가상 컴퓨터 복제를 제거하려면 주 및 보조 가상 컴퓨터 모두에서 아래 단계를 수행해야 합니다. 복제를 제거하고 SQLVM1를 가상 컴퓨터의 이름으로 바꾸려면 아래 스크립트를 실행합니다.

	    Remove-VMReplication –VMName “SQLVM1”


### 보호 설정을 수동으로 정리(온-프레미스 VMM 사이트와 Azure 간)

1. 원본 VMM 서버에서 이 스크립트를 실행하여 기본 가상 컴퓨터의 설정을 정리합니다.

	    $vm = get-scvirtualmachine -Name "SQLVM1"
	    Set-SCVirtualMachine -VM $vm -ClearDRProtection

2. 위의 단계는 VMM 서버에서 복제 설정만 지웁니다. VMM 서버에서 복제를 제거한 후 이 스크립트를 사용하여 Hyper-V 호스트 서버에서 실행 중인 가상 컴퓨터에 대한 복제를 제거해야 합니다. SQLVM1을 가상 컴퓨터의 이름으로 바꾸고 host01.contoso.com을 Hyper-V 호스트 서버의 이름으로 바꿉니다.

	    $vmName = "SQLVM1"
	    $hostName  = "host01.contoso.com"
	    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
	    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
	    $replicationService.RemoveReplicationRelationship($vm.__PATH)

### 보호 설정을 수동으로 정리(Hyper-V 사이트와 Azure 간)

1. 원본 Hyper-V 호스트 서버에서 가상 컴퓨터에 대한 복제를 제거 하려면 이 스크립트를 사용합니다. SQLVM1을 가상 컴퓨터의 이름으로 바꿉니다.

	    $vmName = "SQLVM1"
	    $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
	    $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
	    $replicationService.RemoveReplicationRelationship($vm.__PATH)

## VMware 가상 컴퓨터 또는 물리적 서버 보호 중지

VMware 가상 컴퓨터 또는 물리적 서버의 보호를 중지하려면 보호를 제거해야 합니다. 보호를 제거하는 방식에 따라, 컴퓨터에서 보호 설정을 수동으로 삭제해야 할 수 있습니다.

### 보호 제거

1. 클라우드 속성의 **가상 컴퓨터** 탭에서 가상 컴퓨터 > **제거**를 선택합니다.
2. **가상 컴퓨터 제거**에서 다음 중 한 가지 옵션을 선택합니다.

	- **보호 해제(복구 드릴 및 볼륨 크기 조정용)**—다음과 같이 수행한 경우에만 이 옵션을 보고 사용하도록 설정할 수 있습니다.
		- **크기 조정된 가상 컴퓨터 볼륨**—볼륨 크기를 조정하면 가상 컴퓨터 상태가 심각해집니다. 이 경우 이 옵션을 선택합니다. 그러면 Azure의 복구 지점을 유지하는 동시에 보호가 비활성화됩니다. 컴퓨터에 보호를 사용하도록 다시 설정할 경우 크기 조정된 볼륨의 데이터가 Azure로 전송됩니다.
		- **장애 조치(Failover) 실행**—온-프레미스 VMware 가상 컴퓨터 또는 물리적 서버에서 Azure에 대해 장애 조치(Failover)를 실행하여 환경을 테스트한 다음 이 옵션을 선택하면 온-프레미스 가상 컴퓨터 보호를 다시 시작할 수 있습니다. 이 옵션을 설정하면 각각의 가상 컴퓨터가 비활성화되며, 다시 보호를 사용하도록 설정해야 합니다. 다음 사항에 유의하세요.
			- 이 설정으로 가상 컴퓨터를 비활성화할 경우 Azure의 복제 가상 컴퓨터에 영향을 미치지 않습니다.
			- 가상 컴퓨터에서 모바일 서비스를 제거하면 안 됩니다.
	
	- **보호 사용 안 함**—이 옵션을 사용하도록 설정하고 저장하면 컴퓨터가 더 이상 Site Recovery를 통해 보호되지 않습니다. 컴퓨터의 보호 설정이 자동으로 제거됩니다.
	- **자격 증명 모음에서 제거**—이 옵션을 선택하면 컴퓨터가 Site Recovery 자격 증명 모음에서만 제거됩니다. 컴퓨터에 대한 온-프레미스 보호 설정은 영향을 받지 않습니다. 컴퓨터의 설정을 제거하고 Azure 구독에서 가상 컴퓨터를 제거하려면 모바일 서비스를 제거하여 설정을 제거해야 합니다.
	
		![제거 옵션](./media/site-recovery-manage-registration-and-protection/remove-vm.png)

<!---HONumber=AcomDC_0224_2016-->