<properties
   pageTitle="가상 컴퓨터 실행(Windows) | 청사진 | Microsoft Azure"
   description="확장성, 복원력, 관리 효율성 및 보안에 주의하면서 Azure에서 단일 VM을 실행하는 방법입니다."
   services=""
   documentationCenter="na"
   authors="mikewasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/17/2016"
   ms.author="mikewasson"/>

# Azure에서 단일 Windows VM 실행

이 문서는 확장성, 복원력, 관리 효율성 및 보안에 주의하면서 Azure에서 단일 Windows VM을 실행하기 위한 일련의 검증된 작업 방식을 간략하게 설명합니다.

> [AZURE.WARNING] Azure에서 단일 VM에 대한 가동 시간 SLA는 없습니다. 이 구성은 개발 및 테스트 용도로는 사용하지만 프로덕션 배포에는 사용하지 않습니다.

Azure에는 [리소스 관리자][resource-manager-overview] 및 클래식이라는 두 가지 서로 다른 배포 모델이 있습니다. 이 문서에서는 새로운 배포를 위해 Microsoft에서 권장하는 리소스 관리자를 사용합니다. 리소스 관리자를 사용하는 방법에는 [Azure 포털][azure-portal], [Azure PowerShell][azure-powershell], [Azure CLI][azure-cli] 명령 또는 [리소스 관리자 템플릿][arm-templates] 등 다양한 방법이 있습니다. 이 문서는 Azure CLI를 사용하는 예제를 포함합니다.

![IaaS: 단일 VM](media/guidance-compute-single-vm.png)

Azure에서 단일 VM을 프로비전하는 데는 코어 VM 자체보다 더 많은 움직이는 부분이 포함됩니다. 계산, 네트워킹 및 저장소 요소가 있습니다.

- **리소스 그룹**. 이 VM에 대한 리소스를 보유할 [리소스 그룹][resource-manager-overview]을 만듭니다. _리소스 그룹_은 관련된 리소스를 보유하는 컨테이너입니다.

- **VM**. 게시된 이미지 목록 또는 Azure Blob 저장소에 업로드된 VHD 파일에서 VM을 프로비전할 수 있습니다.

- **OS 디스크**. OS 디스크는 [Azure 저장소][azure-storage]에 저장된 VHD입니다. 즉, 호스트 컴퓨터가 중단되어도 유지됩니다.

- **임시 디스크**. VM은 임시 디스크를 사용하여 만들어집니다(Windows에서 `D:` 드라이브). 이 디스크는 호스트 컴퓨터의 실제 드라이브에 저장됩니다. Azure 저장소에는 저장되지 _않으며_ 다시 부팅되는 동안 및 다른 VM의 수명 주기 이벤트 동안 사라질 수 있습니다. 페이지 또는 스왑 파일과 같은 임시 데이터에 대해서만 이 디스크를 사용합니다.

- **데이터 디스크**. [데이터 디스크][data-disk]는 응용 프로그램 데이터에 사용되는 영구 VHD입니다. 데이터 디스크는 OS 디스크와 같은 Azure 저장소에 저장됩니다.

- **가상 네트워크(VNet) 및 서브넷**. Azure의 모든 VM은 서브넷으로 세분화되는 가상 네트워크(VNet)에 배포됩니다.

- **공용 IP 주소**. 공용 IP 주소는 예를 들어, 원격 데스크톱 (RDP)을 통해 VM과 통신하는 데 필요합니다.

- **NSG(네트워크 보안 그룹)**. [NSG][nsg]는 VM에 대한 네트워크 트래픽을 허용/거부하는 데 사용됩니다. 기본 NSG 규칙은 모든 들어오는 인터넷 트래픽을 허용하지 않습니다.

- **NIC(네트워크 인터페이스 카드)**. NIC를 통해 VM을 가상 네트워크와 통신하도록 할 수 있습니다.

- **진단** VM을 관리 및 문제 해결하는 데 진단 로깅이 중요합니다.

## VM 권장 사항

- 기존 워크로드를 Azure로 이동할 때 온-프레미스 서버와 가장 근접하게 일치하는 [VM 크기][virtual-machine-sizes]를 선택합니다. I/O 사용량이 많은 워크로드에 프리미엄 저장소를 사용할 수 있는 DS 및 GS시리즈를 사용하는 것이 좋습니다.

    - 워크로드에 고성능, 낮은 대기 시간 디스크 액세스가 필요하지 않는 경우 A 시리즈 또는 D 시리즈와 같은 다른 표준 계층 VM을 사용하는 것이 좋습니다.

- VM 및 기타 리소스를 프로비전할 경우 위치를 지정해야 합니다. 일반적으로 내부 사용자 또는 고객에게 가장 가까운 위치를 선택합니다. 그러나 모든 VM SKU를 모든 위치에서 사용할 수는 없습니다. 자세한 내용은 [지역별 서비스][services-by-region]를 참조하세요.

- 게시된 VM 이미지를 선택하는 방법에 대한 자세한 내용은 [Azure 가상 컴퓨터 이미지 탐색 및 선택][select-vm-image]을 참조하세요.

## 디스크 및 저장소 권장 사항

최상의 디스크 I/O 성능을 위해 [프리미엄 저장소][premium-storage]를 사용하는 것이 좋습니다. 하지만 프리미엄 저장소에는 DS 또는 GS 시리즈 VM이 필요합니다.

- 프리미엄 저장소의 경우 비용은 프로비전된 디스크 크기를 기준으로 산정됩니다. IOPS 및 처리량(즉, 데이터 전송 속도)도 디스크 크기에 따라 달라지므로 디스크를 프로비전할 때 세 가지 요소(용량, IOPS 및 처리량)를 모두 고려합니다.

- 표준 저장소의 경우 비용은 디스크에 기록된 데이터 양을 기준으로 산정됩니다. 따라서 최대 크기(1023GB)를 프로비전하는 것이 좋습니다. 단, 디스크를 포맷할 때 빠른 포맷을 사용해야 합니다. 전체 디스크 포맷은 디스크에 0을 기록하므로 실제 저장소를 사용합니다. [Azure 저장소 가격][storage-price]을 참조하세요.

- 표준 저장소를 선택하면 전체 지역 가동 중단 또는 주 지역을 복구할 수 없는 재해이더라도 데이터는 지속되므로 GRS(지역 중복 저장소)를 사용하는 것이 좋습니다.

- 하나 이상의 데이터 디스크를 추가합니다. 새 VHD를 만들 때 형식은 지정되지 않습니다. 디스크를 포맷하려면 VM에 로그인합니다.

- 데이터 디스크 수가 많은 경우 저장소 계정의 총 I/O 제한에 주의해야 합니다. 자세한 내용은 [가상 컴퓨터 디스크 제한][vm-disk-limits]을 참조하세요.

- 최상의 성능을 위해 진단 로그를 저장할 별도의 저장소 계정을 만듭니다. 표준 LRS(로컬 중복 저장소) 계정은 진단 로그에 충분합니다.

## 네트워크 권장 사항

- 단일 VM의 경우 하나의 서브넷이 있는 VNet을 하나 만듭니다. 또한 NSG와 공용 IP 주소도 만듭니다.

- 이 공용 IP 주소는 동적 또는 정적일 수 있습니다. 기본값은 동적입니다.

    - 변경되지 않는 고정 IP 주소가 필요한 경우 [정적 IP 주소][static-ip]를 예약합니다(예를 들어 DNS에 A 레코드를 만들어야 하는 경우 또는 허용 목록에 추가할 IP 주소가 필요한 경우).

    - 기본적으로 IP 주소에는 정규화된 도메인 이름(FQDN)이 없습니다. 자세한 내용은 [Azure 포털에서 정규화된 도메인 이름 만들기][fqdn]를 참조하세요.

- NIC를 할당하고 이를 IP 주소, 서브넷 및 NSG와 연결합니다.

- 기본 NSG 규칙은 RDP를 허용하지 않습니다. RDP를 사용하도록 설정하려면 TCP 포트 3389에 인바운드 트래픽을 허용하는 규칙을 NSG에 추가합니다.

## 확장성

VM 크기를 변경하여 VM을 규모 확장 또는 축소할 수 있습니다. 다음 Azure CLI 명령은 VM 크기를 조정합니다.

```text
azure vm set -g <<resource-group>> --vm-size <<new-vm-size>
    --boot-diagnostics-storage-uri <<storage-account-uri>> <<vm-name>>
```

VM 크기를 조정하면 시스템 재시작을 트리거하고 재시작 후 기존 OS 및 데이터 디스크를 다시 매핑합니다. 임시 디스크에 있는 모든 내용이 손실됩니다. `--boot-diagnostics-storage-uri` 옵션을 사용하면 [진단 부팅][boot-diagnostics]으로 시작과 관련된 모든 오류를 기록할 수 있습니다.

한 SKU 제품군에서 다른 제품군으로 확장하지 못할 수 있습니다(예를 들어, A 시리즈에서 G 시리즈로). 기존 VM에 대해 사용 가능한 크기 목록을 가져오려면 다음 CLI 명령을 사용합니다.

```text
azure vm sizes -g <<resource-group>> --vm-name <<vm-name>>
```

목록에 없는 크기로 조정하려면 VM 인스턴스를 삭제하고 새 것을 만들어야 합니다. VM을 삭제해도 VHD는 삭제되지 않습니다.

## Availability

- 단일 VM에 대한 가용성 SLA는 없습니다. 가용성에 관한 SLA를 달성하려면 여러 VM을 하나의 가용성 집합에 배포해야 합니다.

- 사용자의 VM은 [계획된 유지 관리][planned-maintenance] 또는 [계획되지 않은 유지 관리][manage-vm-availability]의 영향을 받을 수 있습니다. [VM 다시 부팅 로그][reboot-logs]를 사용하여 VM 재부팅이 계획된 유지 관리로 발생했는지 여부를 결정할 수 있습니다.

- 가용성 집합에 단일 VM을 배치하지 마세요. VM을 가용성 집합에 배치한다는 것은 Azure에 VM을 다중 인스턴스 집합의 일부로 처리하라고 알리는 것이며 계획된 유지 관리 재부팅에 대한 알림 또는 사전 경고를 받지 못합니다.

- VHD는 [Azure 저장소][azure-storage]에 의해 지원되며 내구성 및 가용성을 위해 복제됩니다.

- 정상 작업 중 실수로 인한 데이터 손실(예: 사용자 오류 때문에 발생)을 막기 위해 [Blob 스냅숏][blob-snapshot] 또는 다른 도구를 사용하여 지정 시간 백업도 구현해야 합니다.

## 관리 효율성

- VM 진단을 사용하도록 설정하려면 다음 CLI 명령을 실행합니다.
    
    ```text
    azure vm enable-diag <<resource-group>> <<vm-name>>
    ```
    
    이 명령으로 기본 상태 메트릭, 진단 인프라 로그 및 부팅 진단을 사용하도록 설정할 수 있습니다. 자세한 내용은 [모니터링 및 진단 사용][enable-monitoring]을 참조하세요.

- 로그를 수집하고 이를 Azure 저장소에 업로드하는 데는 [Azure 로그 수집][log-collector] 확장을 사용합니다.

- Azure에서는 "중지됨"과 "할당 취소됨" 상태를 구분합니다. VM 상태가 "중지됨"이면 비용이 청구됩니다. VM이 할당 취소되면 비용이 청구되지 않습니다. [Azure VM FAQ][vm-faq]를 참조하세요.

    VM을 할당 취소하려면 다음 CLI 명령을 사용합니다.
    
    ```text
    azure vm deallocate <<resource-group>> <<vm-name>>
    ```
    
    참고: Azure 포털의 **중지** 단추 또한 VM 할당을 취소합니다. 그러나 Windows(RDP를 통해) 내부에서 종료하면 VM은 중지되지만 할당 취소되지 _않으므로_ 비용이 계속 청구됩니다.

- VM을 삭제하는 경우 VHD는 삭제되지 않습니다. 즉, 데이터 손실 없이 안전하게 VM을 삭제할 수 있습니다. 그러나 저장소에 대한 비용은 계속 청구됩니다. VHD를 삭제하려면 [Blob 저장소][blog-storage]에서 파일을 삭제합니다.

- OS 디스크의 크기를 조정하려면 .vhd 파일을 다운로드하고 [Resize-VHD][Resize-VHD]와 같은 도구를 사용하여 VHD의 크기를 조정합니다. 크기 조정된 VHD를 Blob 저장소에 업로드한 후 VM 인스턴스를 삭제하고 크기 조정된 VHD를 사용하는 새 인스턴스를 프로비전합니다.


## 보안

- [Azure 보안 센터][security-center]를 사용하여 Azure 리소스의 보안 상태를 중앙에서 살펴볼 수 있습니다. 보안 센터는 시스템 업데이트, 맬웨어 방지 및 끝점 ACL과 같은 잠재적인 보안 문제를 모니터링하고 배포의 보안 상태에 대한 종합적인 그림을 제공합니다. **참고:** 이 문서를 작성한 시점에 보안 센터는 아직 미리 보기 상태입니다.

- [RBAC(역할 기반 액세스 제어)][rbac]를 사용하여 DevOps 팀의 어떤 구성원이 사용자가 배포하는 Azure 리소스(VM, 네트워크 등)를 관리할 수 있는지 정의합니다.

- [보안 확장][security-extensions]을 설치하는 것이 좋습니다.

- OS 및 데이터 디스크를 암호화하는 데 [Azure 디스크 암호화][disk-encryption]를 사용합니다. **참고:** 이 문서를 작성한 시점에 Azure 디스크 암호화는 아직 미리 보기 상태입니다.

## 문제 해결

- 로컬 관리자 암호를 재설정하려면 `vm reset-access` Azure CLI 명령을 실행합니다.
    
    ```text
    azure vm reset-access -u <<user>> -p <<new-password>> <<resource-group>> <<vm-name>>
    ```
    
- 사용자의 VM이 부팅할 수 없는 상태로 전환되는 경우 [부팅 진단][boot-diagnostics]을 사용하여 부팅 오류를 진단합니다.

- [감사 로그][audit-logs]에서 프로비전 작업 및 기타 VM 이벤트를 확인합니다.

## Azure CLI 명령(예)

이전 다이어그램에 표시된 것처럼 다음 Windows 배치 스크립트는 단일 VM 인스턴스, 관련 네트워크 및 저장소 리소스를 배치하기 위해 [Azure CLI][azure-cli] 명령을 실행합니다.

```bat
ECHO OFF
SETLOCAL

IF "%~1"=="" (
    ECHO Usage: %0 subscription-id
    EXIT /B
    )

:: Set up variables to build out the naming conventions for deploying
:: the cluster

SET LOCATION=eastus2
SET APP_NAME=app1
SET ENVIRONMENT=dev
SET USERNAME=testuser
SET PASSWORD=AweS0me@PW

:: Explicitly set the subscription to avoid confusion as to which subscription
:: is active/default
SET SUBSCRIPTION=%1

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

:: For Windows, use the following command to get the list of URNs:
:: azure vm image list %LOCATION% MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
SET WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

:: For a list of VM sizes see...
SET VM_SIZE=Standard_DS1

:: Set up the postfix variables attached to most CLI commands
SET POSTFIX=--resource-group %RESOURCE_GROUP% --location %LOCATION% ^
  --subscription %SUBSCRIPTION%

CALL azure config mode arm

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Create resources

:: Create the enclosing resource group
CALL azure group create --name %RESOURCE_GROUP% --location %LOCATION%

:: Create the VNet
CALL azure network vnet create --address-prefixes 172.17.0.0/16 ^
  --name %VNET_NAME% %POSTFIX%

:: Create the subnet
CALL azure network vnet subnet create --vnet-name %VNET_NAME% --address-prefix ^
  172.17.0.0/24 --name %SUBNET_NAME% --resource-group %RESOURCE_GROUP% ^
  --subscription %SUBSCRIPTION%

:: Create the public IP address (dynamic)
CALL azure network public-ip create --name %IP_NAME% %POSTFIX%

:: Create the network security group
CALL azure network nsg create --name %NSG_NAME% %POSTFIX%

:: Create the NIC
CALL azure network nic create --network-security-group-name %NSG_NAME% ^
  --public-ip-name %IP_NAME% --subnet-name %SUBNET_NAME% --subnet-vnet-name ^
  %VNET_NAME%  --name %NIC_NAME% %POSTFIX%

:: Create the storage account for the OS VHD
CALL azure storage account create --type PLRS %POSTFIX% %VHD_STORAGE%

:: Create the storage account for diagnostics logs
CALL azure storage account create --type LRS %POSTFIX% %DIAGNOSTICS_STORAGE%

:: Create the VM
CALL azure vm create --name %VM_NAME% --os-type Windows --image-urn ^
  %WINDOWS_BASE_IMAGE% --vm-size %VM_SIZE%   --vnet-subnet-name %SUBNET_NAME% ^
  --nic-name %NIC_NAME% --vnet-name %VNET_NAME% --storage-account-name ^
  %VHD_STORAGE% --os-disk-vhd "%VM_NAME%-osdisk.vhd" --admin-username ^
  "%USERNAME%" --admin-password "%PASSWORD%" --boot-diagnostics-storage-uri ^
  "https://%DIAGNOSTICS_STORAGE%.blob.core.windows.net/" %POSTFIX%

:: Attach a data disk
CALL azure vm disk attach-new -g %RESOURCE_GROUP% --vm-name %VM_NAME% ^
  --size-in-gb 128 --vhd-name data1.vhd --storage-account-name %VHD_STORAGE%

:: Allow RDP
CALL azure network nsg rule create -g %RESOURCE_GROUP% --nsg-name %NSG_NAME% ^
  --direction Inbound --protocol Tcp --destination-port-range 3389 ^
  --source-port-range * --priority 100 --access Allow RDPAllow
```

<!-- links -->

[arm-templates]: ../virtual-machines/virtual-machines-deploy-rmtemplates-azure-cli.md
[audit-logs]: https://azure.microsoft.com/ko-KR/blog/analyze-azure-audit-logs-in-powerbi-more/
[azure-cli]: ../virtual-machines/virtual-machines-command-line-tools.md
[azure-portal]: ../azure-portal/resource-group-portal.md
[azure-powershell]: ../powershell-azure-resource-manager.md
[azure-storage]: ../storage/storage-introduction.md
[blob-snapshot]: ../storage/storage-blob-snapshots.md
[blog-storage]: ../storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/ko-KR/blog/boot-diagnostics-for-virtual-machines-v2/
[data-disk]: ../virtual-machines/virtual-machines-disks-vhds.md
[disk-encryption]: ../azure-security-disk-encryption.md
[enable-monitoring]: ../azure-portal/insights-how-to-use-diagnostics.md
[fqdn]: ../virtual-machines/virtual-machines-create-fqdn-on-portal.md
[log-collector]: https://azure.microsoft.com/ko-KR/blog/simplifying-virtual-machine-troubleshooting-using-azure-log-collector/
[manage-vm-availability]: ../virtual-machines/virtual-machines-manage-availability.md
[nsg]: ../virtual-network/virtual-networks-nsg.md
[password-reset]: ../virtual-machines/virtual-machines-windows-reset-password.md
[planned-maintenance]: ../virtual-machines/virtual-machines-planned-maintenance.md
[premium-storage]: ../storage/storage-premium-storage-preview-portal.md
[rbac]: ../active-directory/role-based-access-control-configure.md
[reboot-logs]: https://azure.microsoft.com/ko-KR/blog/viewing-vm-reboot-logs/
[Resize-VHD]: https://technet.microsoft.com/ko-KR/library/hh848535.aspx
[resource-manager-overview]: ../resource-group-overview.md
[security-center]: https://azure.microsoft.com/ko-KR/services/security-center/
[security-extensions]: ../virtual-machines/virtual-machines-extensions-features.md#security-and-protection
[select-vm-image]: ../virtual-machines/resource-groups-vm-searching.md
[services-by-region]: https://azure.microsoft.com/ko-KR/regions/#services
[static-ip]: ../virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[virtual-machine-sizes]: ../virtual-machines/virtual-machines-size-specs.md
[vm-disk-limits]: ../azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-faq]: ../virtual-machines/virtual-machines-questions.md

<!---HONumber=AcomDC_0302_2016-->