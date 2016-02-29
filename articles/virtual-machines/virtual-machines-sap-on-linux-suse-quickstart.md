<properties
   pageTitle="Microsoft Azure SUSE Linux VM에서 SAP NetWeaver 테스트 | Microsoft Azure"
   description="Microsoft Azure SUSE Linux VM에서 SAP NetWeaver 테스트"
   services="virtual-machines,virtual-network,storage"
   documentationCenter="saponazure"
   authors="hermanndms"
   manager="juergent"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="na"
   ms.date="02/12/2016"
   ms.author="hermannd"/>

# Microsoft Azure SUSE Linux VM에서 SAP NetWeaver 테스트


여기에는 Microsoft Azure SUSE Linux VM에서 SAP NetWeaver를 테스트할 때 고려할 항목이 나열되어 있습니다. 현 시점에는 SAP-Linux-Azure에 대해 공식적인 SAP 지원 설명이 없습니다. 그렇기는 하지만 고객이 공식적인 SAP 지원에 의존하지 않는다면 테스트나 데모 또는 프로토타입 생성을 수행할 수 있습니다.

다음 정보는 일부 잠재적인 문제를 예방하는 데 도움이 됩니다.



## SAP 테스트용 Microsoft Azure SUSE 이미지

Azure에서 SAP 테스트를 수행하는 경우, SUSE Linux Enterprise Server(SLES) SP4 11 및 SLES 12만 사용합니다. 특수한 SUSE 이미지는 Azure 마켓플레이스("SLES 11 SP3 for SAP CAL")에 있지만, 일반 용도는 아닙니다. 이는 [SAP 클라우드 어플라이언스 라이브러리](https://cal.sap.com/) 솔루션을 위한 것입니다. 대중으로부터 이 이미지를 숨길 수 있는 옵션이 없었습니다. 그러니 일반 용도로는 사용하지 마십시오.

Azure의 모든 새로운 테스트에 대해 Azure 리소스 관리자를 사용해야 합니다. Azure PowerShell 또는 Azure 명령줄 인터페이스(CLI)를 사용하여 SUSE SLES 이미지나 버전을 찾아보려면 다음 명령을 사용합니다. 그런 다음 새 SUSE Linux VM 배포용 JSON 템플릿에서 OS 이미지를 정의하는 등 출력 내용을 사용할 수 있습니다. 아래 PowerShell 명령은 Azure PowerShell 1.0.1 이상 버전에 유효합니다.

* SUSE를 포함하는 기존 게시자를 찾습니다.

   ```
   PS  : Get-AzureRmVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```

* SUSE에서 기존 제품을 찾습니다.

   ```
   PS  : Get-AzureRmVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```

* SUSE SLES 제품을 찾습니다.

   ```
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   CLI : azure vm image list-skus westeurope SUSE SLES
   ```

* 특정 버전의 SLES SKU를 찾습니다.

   ```
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12"
   CLI : azure vm image list westeurope SUSE SLES 12
   ```

## SUSE VM에 WALinuxAgent 설치

WALinuxAgent라고 하는 이 에이전트는 Azure 마켓플레이스 내 SLES 이미지의 일부분입니다. 다음은 에이전트를 수동으로(예: 온-프레미스에서 SLES OS VHD를 업로드하는 경우) 설치하는 것과 관련된 내용을 찾을 수 있는 위치입니다.

- [OpenSUSE](http://software.opensuse.org/package/WALinuxAgent)

- [Azure](virtual-machines-linux-endorsed-distributions.md)

- [SUSE](https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## Azure Linux VM에 Azure 데이터 디스크 연결

장치 ID를 통해 Azure 데이터 디스크를 Azure Linux VM에 절대 탑재하지 마세요. 대신 UUID를 사용하세요. Azure 데이터 디스크 탑재에 그래픽 도구 등을 사용하는 경우 주의해야 합니다. /etc/fstab의 항목을 재차 확인하세요.

장치 ID의 문제점은 변경될 가능성이 있고 그렇게 되면 Azure VM이 부팅 프로세스에 머무를 수 있다는 점입니다. 문제를 완화시키기 위해서 /etc/fstab에 nofail 매개 변수를 추가할 수 있습니다. 하지만 nofail 응용프로그램은 전과 같이 탑재 지점을 사용할 수 있고, 외부 Azure 데이터 디스크가 부팅 중에 탑재되지 않은 경우에는 루트 파일 시스템에 쓸 수 있습니다.

UUID를 통한 탑재와 관련하여 유일한 예외는 다음 섹션에 설명된 대로 문제 해결을 위해 OS 디스크를 연결하는 것과 관련이 있습니다.

## 더 이상 액세스할 수 없는 SUSE VM 문제 해결

Azure에서 SUSE VM 부팅 프로세스가 중지되는 상황이 발생할 수 있습니다(예: 디스크 탑재와 관련된 실수). 이 문제는 예를 들어 v2 VM에 대한 포털에서 부팅 진단 기능으로 확인할 수 있습니다([이 블로그 참조](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/)).

문제를 해결하는 옵션은 Azure에서 손상된 VM의 OS 디스크를 다른 SUSE VM에 연결하고 /etc/fstab를 편집하거나 다음 섹션에 설명된 대로 네트워크 udev 규칙을 제거하는 등의 적절한 변경을 수행하는 것입니다.

하지만 고려해야 할 한 가지 중요한 사항이 있습니다. 동일한 Azure 갤러리 이미지(예: SLES 11 SP4 )에서 여러 SUSE VM을 배포하면 OS 디스크가 항상 동일한 UUID로 탑재되는 것으로 나타납니다. 동일한 Azure 갤러리 이미지를 사용하여 배포된 UUID로 서로 다른 VM에서 OS 디스크를 연결하므로 두 개의 동일한 UUID가 발생합니다. 이로 인해 문제가 발생하며 문제 해결을 위해 VM이 사실상 원본 디스크 대신 연결 및 손상된 OS 디스크에서 부팅하는 것을 의미할 수 있습니다.

두 가지 방법으로 이 문제를 방지할 수 있습니다.

* VM 문제 해결을 위해 서로 다른 Azure 갤러리 이미지를 사용합니다(예를 들어, SLES 11 SP4 대신 SLES 12).
* UUID 통해 다른 VM에서 손상된 OS 디스크를 연결하지 말고 다른 디스크를 사용합니다.

## 온-프레미스에서 Azure로 SUSE VM 업로드

[이 문서](virtual-machines-linux-create-upload-vhd-suse.md)는 온-프레미스에서 Azure로 SUSE VM을 업로드하는 단계를 설명합니다.

예를 들면, 기존 SAP 설치는 물론 호스트 이름을 유지하기 위해서 마지막 단계에서 프로비전을 해제하지 않고 VM을 업로드하려면 다음 항목을 확인합니다.

* 장치 ID가 아닌 UUID를 통해 OS 디스크가 탑재되어야 합니다. /etc/fstab에서만 UUID로 변경하는 것은 OS 디스크에 충분하지 않습니다. 또한, YaST를 통해 또는 /boot/grub/menu.lst를 편집하여 부팅 로더를 변경하는 것도 잊지 말아야 합니다.
* SUSE OS 디스크에 대해 VHDX 형식을 사용하고 Azure에 업로드하기 위해서 이를 VHD로 변환하면 네트워크 장치가 eth0에서 eth1로 변경될 가능성이 높습니다. 나중에 Azure에서 부팅할 때 문제를 방지하려면 [다음 문서](https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/)에 설명된 것처럼 eth0으로 다시 변경해야 합니다.

문서에 설명되어 있는 내용뿐만 아니라 다음 항목을 삭제하는 것이 좋습니다.

   /lib/udev/rules.d/75-persistent-net-generator.rules

Azure Linux 에이전트(waagent)를 설치하면 NIC이 여러 개만 아니라면 잠재적인 문제를 방지할 수 있습니다.

## Azure에 SUSE VM 배포

새 SUSE VM은 새 Azure 리소스 관리자 모델의 JSON 템플릿 파일을 통해 만들어져야 합니다. JSON 템플릿 파일이 만들어지면 PowerShell을 대신하여 다음 CLI 명령을 사용해 VM을 배포할 수 있습니다.

   ``` azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ``` [이 문서](resource-group-authoring-templates.md) 및 [이 웹 페이지](https://azure.microsoft.com/documentation/templates/)에서 JSON 템플릿 파일에 대한 자세한 내용을 확인할 수 있습니다.

CLI 및 Azure 리소스 관리자에 대한 자세한 내용은 [이 문서](xplat-cli-azure-resource-manager.md)에서 확인할 수 있습니다.

## SAP 라이선스 및 하드웨어 키

공식적인 SAP-Windows-Azure 인증을 위해, SAP 라이선스에 사용되는 SAP 하드웨어 키 계산을 위한 새로운 메커니즘이 도입되었습니다. 이것을 이용하려면 SAP 커널이 적용되어야 합니다. 현재 Linux용 SAP 커널 버전에는 이 코드 변경이 포함되어 있지 않습니다. 따라서 특정한 상황(예: Azure VM 크기 조정)에서 SAP 하드웨어 키 변경 및 SAP 라이선스가 더 이상 유효하지 않게 될 수 있습니다.

## SUSE sapconf 패키지

SUSE는 SAP 관련 설정 집합을 처리하는 "sapconf"라는 패키지를 제공합니다. 이 패키지가 수행하는 작업과 설치 방법 및 사용 방법에 대한 자세한 내용은 [이 SUSE 블로그 항목](https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/) 및 [이 SAP 블로그 항목](http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems)에서 확인할 수 있습니다.

## 분산된 SAP 설치에서 NFS 공유

데이터베이스 및 SAP 응용프로그램 서버 등을 별도의 VM에 설치하려는 분산 설치의 경우, 네트워크 파일 시스템(NFS)을 통해 /sapmnt 디렉토리를 공유하기도 합니다. /sapmnt 검사를 위해 NFS 공유를 생성한 후에 설치 단계에서 문제가 발생하면 공유에 대해 "no\_root\_squash"가 설정되어 있는지 확인합니다. 이것이 내부 테스트 사례에 대한 솔루션이었습니다.


## 논리 볼륨

논리 볼륨 관리자(LVM)는 Azure에서 완벽하게 유효성 검사가 완료되지 않습니다. 다수의 Azure 데이터 디스크(예: SAP 데이터베이스)에 대해 큰 논리 볼륨이 필요한 경우에는 mdadm을 사용해야 합니다. [이 문서](virtual-machines-linux-configure-raid.md)에서는 mdadm을 사용하여 Azure에서 Linux RAID를 설정하는 방법이 설명되어 있습니다.


## Azure SUSE 리포지토리

표준 Azure SUSE 리포지토리에 액세스하는 데 문제가 있는 경우, 리포지토리를 다시 설정하는 간단한 명령이 있습니다. Azure 지역에 개인 OS 이미지를 만든 후에 이 개인 OS 이미지를 기반으로 하는 새 VM을 배포하려는 다른 지역으로 해당 이미지를 복사하는 경우에 이런 상황이 발생할 수 있습니다. VM 내부에서 다음 명령을 실행합니다.

   ```
   service guestregister restart
   ```

## Gnome 데스크톱

SAP GUI, 브라우저, SAP 관리 콘솔 등 단일 VM 내에 전체 SAP 데모 시스템을 설치하기 위해 Gnome 데스크톱을 사용하려는 경우, Azure SLES 이미지에 이를 설치하기 위한 작은 힌트가 여기에 있습니다.

   SLES 11

   ```
   zypper in -t pattern gnome
   ```

   SLES 12

   ```
   zypper in -t pattern gnome-basic
   ```

## 클라우드 Linux의 SAP-Oracle 지원

가상화된 환경에서 Linux의 Oracle을 지원하는 것에 관한 제한 사항이 있습니다. 이는 일반 항목이며, Azure 관련 항목이 아닙니다. 그렇기는 하지만 상황을 이해하는 것이 중요합니다. SAP은 Azure와 같은 공용 클라우드에 있는 SUSE 또는 Red Hat에서 Oracle을 지원하지 않습니다. 이 문제를 논의하려면 고객이 Oracle에 직접 문의해야 합니다.

<!---HONumber=AcomDC_0218_2016-->