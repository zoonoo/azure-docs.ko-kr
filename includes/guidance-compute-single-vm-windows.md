이 문서는 확장성, 가용성, 관리 효율성 및 보안에 주의하면서 Azure에서 Windows VM(가상 컴퓨터)을 실행하기 위한 일련의 검증된 작업 방식을 간략하게 설명합니다.

> [AZURE.NOTE] Azure에는 [Resource Manager][resource-manager-overview] 및 클래식이라는 두 가지 서로 다른 배포 모델이 있습니다. 이 문서에서는 새로운 배포를 위해 Microsoft에서 권장하는 리소스 관리자를 사용합니다.

Azure에는 단일 VM에 대한 가동 시간 SLA가 없으므로 프로덕션 워크로드에는 단일 VM을 사용하지 않는 것이 좋습니다. SLA를 가져오려면 가용성 집합에 여러 VM을 배포해야 합니다. 자세한 내용은 [Azure에서 여러 Windows VM 실행][multi-vm]을 참조하세요.

## 아키텍처 다이어그램

Azure에서 VM을 프로비전할 때에는 VM 자체 이외에도 움직일 부분이 많습니다. 계산, 네트워킹 및 저장소 요소가 있습니다.

![IaaS: 단일 VM](./media/guidance-blueprints/compute-single-vm.png)

- **리소스 그룹.** [_리소스 그룹_][resource-manager-overview]은 관련된 리소스를 보유하는 컨테이너입니다. 이 VM에 대한 리소스를 보유할 리소스 그룹을 만듭니다.

- **VM**. 게시된 이미지 목록 또는 Azure Blob 저장소에 업로드된 VHD 파일에서 VM을 프로비전할 수 있습니다.

- **OS 디스크.** OS 디스크는 [Azure 저장소][azure-storage]에 저장된 VHD입니다. 즉, 호스트 컴퓨터가 중단되어도 유지됩니다.

- **임시 디스크.** VM은 임시 디스크를 사용하여 만들어집니다(Windows에서 `D:` 드라이브). 이 디스크는 호스트 컴퓨터의 실제 드라이브에 저장됩니다. Azure 저장소에는 저장되지 _않으며_ 다시 부팅되는 동안에 그리고 다른 VM의 수명 주기 이벤트 동안에 사라질 수 있습니다. 페이지 또는 스왑 파일과 같은 임시 데이터에 대해서만 이 디스크를 사용합니다.

- **데이터 디스크.** [데이터 디스크][data-disk]는 응용 프로그램 데이터에 사용되는 영구 VHD입니다. 데이터 디스크는 OS 디스크와 같은 Azure 저장소에 저장됩니다.

- **가상 네트워크(VNet) 및 서브넷.** Azure의 모든 VM은 서브넷으로 세분화되는 가상 네트워크(VNet)에 배포됩니다.

- **공용 IP 주소.** 공용 IP 주소는 예를 들어, 원격 데스크톱 (RDP)을 통해 VM과 통신하는 데 필요합니다.

- **네트워크 인터페이스(NIC)**. NIC를 통해 VM을 가상 네트워크와 통신하도록 할 수 있습니다.

- **NSG(네트워크 보안 그룹)**. [NSG][nsg]는 서브넷에 대한 네트워크 트래픽을 허용/거부하는 데 사용됩니다. NSG를 개별 NIC 또는 서브넷에 연결할 수 있습니다. 서브넷에 연결하는 경우 NSG 규칙이 해당 서브넷의 모든 VM에 적용됩니다.
 
- **진단** VM을 관리 및 문제 해결하는 데 진단 로깅이 중요합니다.

## 추천

### VM 권장 사항

- 고성능 컴퓨팅과 같은 특수한 워크로드가 발생하지 않는다면 DS 및 GS 시리즈가 좋습니다. 자세한 내용은 [가상 컴퓨터 크기][virtual-machine-sizes]를 참조하세요. 기존 워크로드를 Azure로 이동할 때 온-프레미스 서버와 가장 근접하게 일치하는 VM 크기부터 사용하기 시작합니다. 그런 다음 CPU, 메모리, 디스크 IOPS에 따라 실제 워크로드의 성능을 측정하고 필요에 따라 크기를 조정합니다. 또한 여러 NIC가 필요한 경우 각 크기에 대한 NIC 제한을 알아야 합니다.  

- VM 및 기타 리소스를 프로비전할 경우 위치를 지정해야 합니다. 일반적으로 내부 사용자 또는 고객에게 가장 가까운 위치를 선택합니다. 그러나 일부 위치에서는 일부 VM 크기를 사용할 수 없을 수 있습니다. 자세한 내용은 [지역별 서비스][services-by-region]를 참조하세요. 지정된 위치에서 사용할 수 있는 VM 크기를 나열하려면 다음 Azure CLI 명령을 실행합니다.

    ```
    azure vm sizes --location <location>
    ```

- 게시된 VM 이미지를 선택하는 방법에 대한 자세한 내용은 [Azure 가상 컴퓨터 이미지 탐색 및 선택][select-vm-image]을 참조하세요.

### 디스크 및 저장소 권장 사항

- 최상의 디스크 I/O 성능을 위해서는 SSD(Solid State Drive)에 데이터를 저장하는 [프리미엄 저장소][premium-storage]가 권장됩니다. 비용은 프로비전된 디스크 크기를 기준으로 산정됩니다. IOPS 및 처리량(즉, 데이터 전송 속도)도 디스크 크기에 따라 달라지므로 디스크를 프로비전할 때 세 가지 요소(용량, IOPS, 처리량)를 모두 고려합니다. 

- 하나 이상의 데이터 디스크를 추가합니다. 새 VHD를 만들 때 형식은 지정되지 않습니다. 디스크를 포맷하려면 VM에 로그인합니다.

- 데이터 디스크 수가 많은 경우 저장소 계정의 총 I/O 제한에 주의해야 합니다. 자세한 내용은 [가상 컴퓨터 디스크 제한][vm-disk-limits]을 참조하세요.

- 최상의 성능을 위해 진단 로그를 저장할 별도의 저장소 계정을 만듭니다. 표준 LRS(로컬 중복 저장소) 계정은 진단 로그에 충분합니다.

- 가능한 경우 OS 디스크가 아닌 데이터 디스크에 응용 프로그램을 설치합니다. 그러나 일부 레거시 응용 프로그램은 C: 드라이브에 해당 구성 요소를 설치해야 할 수 있습니다. 이 경우 PowerShell을 사용하여 [OS 디스크의 크기를 조정][resize-os-disk]합니다.

### 네트워크 권장 사항

- 이 공용 IP 주소는 동적 또는 정적일 수 있습니다. 기본값은 동적입니다.

    - 변경되지 않는 고정 IP 주소가 필요한 경우 [정적 IP 주소][static-ip]를 예약합니다(예를 들어 DNS에 A 레코드를 만들어야 하는 경우 또는 허용 목록에 추가할 IP 주소가 필요한 경우).

    - IP 주소의 FQDN(정규화된 도메인 이름)을 만들 수도 있습니다. 그런 후 FQDN을 가리키는 DNS에 [CNAME 레코드][cname-record]를 등록할 수 있습니다. 자세한 내용은 [Azure 포털에서 정규화된 도메인 이름 만들기][fqdn]를 참조하세요.

- 모든 NSG에는 모든 인바운드 인터넷 트래픽을 차단하는 규칙을 포함하여 [기본 규칙][nsg-default-rules] 집합이 있습니다. 기본 규칙은 삭제할 수 없으나 다른 규칙으로 재정의할 수 있습니다. 인터넷 트래픽을 사용하도록 설정하려면 특정 포트(예: HTTP용 포트 80)로의 인바운드 트래픽을 허용하는 규칙을 만듭니다.

- RDP를 사용하도록 설정하려면 TCP 포트 3389에 인바운드 트래픽을 허용하는 NSG 규칙을 추가합니다.

## 확장성 고려 사항

- [VM 크기를 변경][vm-resize]하여 VM 규모를 확장하거나 축소할 수 있습니다. 

- 규모를 확장하려면 부하 분산 장치를 기준으로 두 개 이상의 VM을 가용성 집합에 추가합니다. 자세한 내용은 [Azure에서 여러 Windows VM 실행][multi-vm]을 참조하세요.

## 가용성 고려 사항

- 앞서 설명한 것처럼 단일 VM에 대한 SLA는 없습니다. SLA를 가져오려면 가용성 집합에 여러 VM을 배포해야 합니다.

- 사용자의 VM은 [계획된 유지 관리][planned-maintenance] 또는 [계획되지 않은 유지 관리][manage-vm-availability]의 영향을 받을 수 있습니다. [VM 다시 부팅 로그][reboot-logs]를 사용하여 VM 다시 부팅이 계획된 유지 관리로 발생했는지 여부를 결정할 수 있습니다.

- VHD는 [Azure 저장소][azure-storage]에 의해 지원되며 내구성 및 가용성을 위해 복제됩니다.

- 정상 작업 중 실수로 인한 데이터 손실(예: 사용자 오류 때문에 발생)을 막기 위해 [Blob 스냅숏][blob-snapshot] 또는 다른 도구를 사용하여 지정 시간 백업도 구현해야 합니다.

## 관리 효율성 고려 사항

- **리소스 그룹.** 동일한 수명 주기를 공유하는 긴밀하게 결합된 리소스를 동일한 [리소스 그룹][resource-manager-overview]에 추가합니다. 리소스 그룹을 사용하여 리소스를 그룹 단위로 배포 및 모니터링하고, 리소스 그룹별로 비용을 청구할 수 있습니다. 리소스를 하나의 집합으로 삭제할 수도 있습니다. 이러한 기능은 테스트 배포에서 매우 유용합니다. 리소스에 의미 있는 이름을 지정합니다. 이렇게 하면 보다 쉽게 특정 리소스를 찾고 역할을 이해할 수 있습니다. [Azure 리소스에 대한 권장되는 명명 규칙][naming conventions]을 참조하세요.

- **VM 진단.** 기본 상태 메트릭, 진단 인프라 로그 및 [부팅 진단][boot-diagnostics]을 모니터링 및 진단을 사용하도록 설정할 수 있습니다. 부팅 진단은 VM이 부팅할 수 없는 상태로 전환되는 경우 부팅 오류를 진단하는 데 도움이 될 수 있습니다. 자세한 내용은 [모니터링 및 진단 사용][enable-monitoring]을 참조하세요. Azure 플랫폼 로그를 수집하고 이를 Azure 저장소에 업로드하는 데는 [Azure 로그 수집][log-collector] 확장을 사용합니다.

    다음 CLI 명령을 통해 진단을 사용할 수 있습니다.

    ```text
    azure vm enable-diag <resource-group> <vm-name>
     ```

- **VM 중지.** Azure에서는 "중지됨"과 "할당 취소됨" 상태를 구분합니다. VM 상태가 "중지됨"이면 비용이 청구됩니다. VM이 할당 취소되면 비용이 청구되지 않습니다.

    VM을 할당 취소하려면 다음 CLI 명령을 사용합니다.

    ```text
    azure vm deallocate <resource-group> <vm-name>
    ```

    Azure 포털의 **중지** 단추 또한 VM 할당을 취소합니다. 그러나 로그인한 상태에서 OS를 통해 종료하면 VM은 중지되지만 할당 취소되지 _않으므로_ 비용이 계속 청구됩니다.

- **VM 삭제.** VM을 삭제하는 경우 VHD는 삭제되지 않습니다. 즉, 데이터 손실 없이 안전하게 VM을 삭제할 수 있습니다. 그러나 저장소에 대한 비용은 계속 청구됩니다. VHD를 삭제하려면 [Blob 저장소][blob-storage]에서 파일을 삭제합니다.

  실수로 삭제하지 않도록 하려면 [리소스 잠금][resource-lock]을 사용하여 전체 리소스 그룹을 잠그거나 VM과 같은 개별 리소스를 잠급니다.



## 보안 고려 사항

- [Azure 보안 센터][security-center]를 사용하여 Azure 리소스의 보안 상태를 중앙에서 살펴볼 수 있습니다. 보안 센터는 시스템 업데이트, 맬웨어 방지와 같은 잠재적인 보안 문제를 모니터링하고 배포의 보안 상태에 대한 종합적인 그림을 제공합니다. 

    - 보안 센터는 각 Azure 구독을 기준으로 구성됩니다. [보안 센터 사용]에 설명된 것처럼 보안 데이터 수집을 사용하도록 설정합니다.
    - 데이터 수집이 사용되도록 설정되면 보안 센터는 해당 구독에서 만든 모든 VM을 자동으로 검색합니다.

- **패치 관리.** 이 기능이 설정된 경우 보안 센터는 보안 및 중요 업데이트 누락 여부를 확인합니다. VM의 [그룹 정책 설정][group-policy] 사용하여 자동 시스템 업데이트를 사용하도록 설정합니다.

- **맬웨어 방지.** 이 기능이 설정되면 보안 센터는 맬웨어 방지 소프트웨어 설치 여부를 확인합니다. 또한 보안 센터를 사용하여 Azure 포털 내에서 맬웨어 방지 소프트웨어를 설치할 수도 있습니다.

- RBAC([역할 기반 액세스 제어][rbac])를 사용하여 배포하는 Azure 리소스에 대한 액세스를 제어합니다. RBAC를 통해 DevOps 팀의 구성원에게 권한 역할을 할당할 수 있습니다. 예를 들어 읽기 권한자 역할은 Azure 리소스를 볼 수 있지만 만들거나 관리하거나 삭제할 수는 없습니다. 일부 역할은 특정 Azure 리소스 유형에 따라 다릅니다. 예를 들어 가상 컴퓨터 참가자 역할은 VM을 다시 시작하거나 할당을 취소하고, 관리자 암호를 재설정하고, 새 VM을 만드는 등의 작업을 수행할 수 있습니다. 이 참조 아키텍처에 유용할 수 있는 기타 [기본 제공 RBAC 역할][rbac-roles]에는 [DevTest Lab 사용자][rbac-devtest] 및 [네트워크 참가자][rbac-network]가 포함됩니다. 한 명의 사용자가 여러 역할에 할당될 수 있으며 좀 더 세분화된 권한의 사용자 지정 역할을 만들 수도 있습니다.

    > [AZURE.NOTE] RBAC는 VM에 로그온한 사용자가 수행할 수 있는 작업을 제한하지 않습니다. 이러한 사용 권한은 게스트 OS의 계정 유형에 따라 결정됩니다.

- 로컬 관리자 암호를 재설정하려면 `vm reset-access` Azure CLI 명령을 실행합니다.

    ```text
    azure vm reset-access -u <user> -p <new-password> <resource-group> <vm-name>
    ```

- [감사 로그][audit-logs]를 사용하여 프로비전 작업 및 기타 VM 이벤트를 확인합니다.

- OS 및 데이터 디스크를 암호화해야 하는 경우 [Azure 디스크 암호화][disk-encryption]를 고려하세요.

## 예제 배포 스크립트

이전 다이어그램에 표시된 것처럼 다음 Windows 배치 스크립트는 단일 VM 인스턴스, 관련 네트워크 및 저장소 리소스를 배치하기 위해 [Azure CLI][azure-cli] 명령을 실행합니다.

이 스크립트에서는 [Azure 리소스에 대한 권장되는 명명 규칙][naming conventions]에 설명된 명명 규칙이 사용됩니다.

```bat
ECHO OFF
SETLOCAL

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Set up variables for deploying resources to Azure.
:: Change these variables for your own deployment.

:: The APP_NAME variable must not exceed 4 characters in size.
:: If it does the 15 character size limitation of the VM name may be exceeded.

SET APP_NAME=app1
SET LOCATION=eastus2
SET ENVIRONMENT=dev
SET USERNAME=testuser


:: For Windows, use the following command to get the list of URNs:
:: azure vm image list %LOCATION% MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
SET WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

:: For a list of VM sizes see:
::   https://azure.microsoft.com/documentation/articles/virtual-machines-size-specs/
:: To see the VM sizes available in a region:
:: 	azure vm sizes --location <location>
SET VM_SIZE=Standard_DS1

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

IF "%2"=="" (
    ECHO Usage: %0 subscription-id admin-password
    EXIT /B
    )

:: Explicitly set the subscription to avoid confusion as to which subscription
:: is active/default
SET SUBSCRIPTION=%1
SET PASSWORD=%2

:: Set up the names of things using recommended conventions
SET RESOURCE_GROUP=%APP_NAME%-%ENVIRONMENT%-rg
SET VM_NAME=%APP_NAME%-vm0

SET IP_NAME=%APP_NAME%-pip
SET NIC_NAME=%VM_NAME%-0nic
SET NSG_NAME=%APP_NAME%-nsg
SET SUBNET_NAME=%APP_NAME%-subnet
SET VNET_NAME=%APP_NAME%-vnet
SET VHD_STORAGE=%VM_NAME:-=%st0
SET DIAGNOSTICS_STORAGE=%VM_NAME:-=%diag

:: Set up the postfix variables attached to most CLI commands
SET POSTFIX=--resource-group %RESOURCE_GROUP% --subscription %SUBSCRIPTION%

CALL azure config mode arm

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Create resources

:: Create the enclosing resource group
CALL azure group create --name %RESOURCE_GROUP% --location %LOCATION% ^
  --subscription %SUBSCRIPTION%

:: Create the VNet
CALL azure network vnet create --address-prefixes 172.17.0.0/16 ^
  --name %VNET_NAME% --location %LOCATION% %POSTFIX%

:: Create the network security group
CALL azure network nsg create --name %NSG_NAME% --location %LOCATION% %POSTFIX%

:: Create the subnet
CALL azure network vnet subnet create --vnet-name %VNET_NAME% --address-prefix ^
  172.17.0.0/24 --name %SUBNET_NAME% --network-security-group-name %NSG_NAME% ^
  %POSTFIX%

:: Create the public IP address (dynamic)
CALL azure network public-ip create --name %IP_NAME% --location %LOCATION% %POSTFIX%

:: Create the NIC
CALL azure network nic create --public-ip-name %IP_NAME% --subnet-name ^
  %SUBNET_NAME% --subnet-vnet-name %VNET_NAME%  --name %NIC_NAME% --location ^
  %LOCATION% %POSTFIX%

:: Create the storage account for the OS VHD
CALL azure storage account create --type PLRS --location %LOCATION% %POSTFIX% ^
  %VHD_STORAGE%

:: Create the storage account for diagnostics logs
CALL azure storage account create --type LRS --location %LOCATION% %POSTFIX% ^
  %DIAGNOSTICS_STORAGE%

:: Create the VM
CALL azure vm create --name %VM_NAME% --os-type Windows --image-urn ^
  %WINDOWS_BASE_IMAGE% --vm-size %VM_SIZE%   --vnet-subnet-name %SUBNET_NAME% ^
  --vnet-name %VNET_NAME% --nic-name %NIC_NAME% --storage-account-name ^
  %VHD_STORAGE% --os-disk-vhd "%VM_NAME%-osdisk.vhd" --admin-username ^
  "%USERNAME%" --admin-password "%PASSWORD%" --boot-diagnostics-storage-uri ^
  "https://%DIAGNOSTICS_STORAGE%.blob.core.windows.net/" --location %LOCATION% ^
  %POSTFIX%

:: Attach a data disk
CALL azure vm disk attach-new --vm-name %VM_NAME% --size-in-gb 128 --vhd-name ^
  data1.vhd --storage-account-name %VHD_STORAGE% %POSTFIX%

:: Allow RDP
CALL azure network nsg rule create --nsg-name %NSG_NAME% --direction Inbound ^
  --protocol Tcp --destination-port-range 3389 --source-port-range * ^
  --priority 100 --access Allow RDPAllow %POSTFIX%
```

## 다음 단계

[가상 컴퓨터에 대한 SLA][vm-sla]를 적용하려면 가용성 집합에서 두 개 이상의 인스턴스를 배포해야 합니다. 자세한 내용은 [Azure에서 여러 Windows VM 실행][multi-vm]을 참조하세요.

<!-- links -->

[arm-templates]: ../articles/virtual-machines/virtual-machines-windows-cli-deploy-templates.md
[audit-logs]: https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/
[azure-cli]: ../articles/virtual-machines-command-line-tools.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/virtual-machines/virtual-machines-windows-about-disks-vhds.md
[disk-encryption]: ../articles/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/azure-portal/insights-how-to-use-diagnostics.md
[fqdn]: ../articles/virtual-machines/virtual-machines-windows-portal-create-fqdn.md
[group-policy]: https://technet.microsoft.com/ko-KR/library/dn595129.aspx
[log-collector]: https://azure.microsoft.com/blog/simplifying-virtual-machine-troubleshooting-using-azure-log-collector/
[manage-vm-availability]: ../articles/virtual-machines/virtual-machines-windows-manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[planned-maintenance]: ../articles/virtual-machines/virtual-machines-windows-planned-maintenance.md
[premium-storage]: ../articles/storage/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-lab-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/blog/viewing-vm-reboot-logs/
[resize-os-disk]: ../articles/virtual-machines/virtual-machines-windows-expand-os-disk.md
[Resize-VHD]: https://technet.microsoft.com/ko-KR/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/resource-group-overview.md
[security-center]: https://azure.microsoft.com/services/security-center/
[select-vm-image]: ../articles/virtual-machines/virtual-machines-windows-cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/regions/#services
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[보안 센터 사용]: ../articles/security-center/security-center-get-started.md#use-security-center
[virtual-machine-sizes]: ../articles/virtual-machines/virtual-machines-windows-sizes.md
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]: ../articles/virtual-machines/virtual-machines-linux-change-vm-size.md
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/

<!---HONumber=AcomDC_0622_2016-->