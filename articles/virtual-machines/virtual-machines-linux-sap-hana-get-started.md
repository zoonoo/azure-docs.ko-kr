---
title: "빠른 시작 가이드: Azure VM에 단일 인스턴스 SAP HANA 수동 설치 | Microsoft Docs"
description: "Azure VM에 단일 인스턴스 SAP HANA를 수동으로 설치하기 위한 빠른 시작 가이드입니다."
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
translationtype: Human Translation
ms.sourcegitcommit: bd70596bcc34684a6f751076e71cb3d0aa3877dd
ms.openlocfilehash: 4c40fd95f42f4e89e86d829c8a32583a0398c74e
ms.lasthandoff: 02/22/2017


---
# <a name="quick-start-guide-manual-installation-of-single-instance-sap-hana-on-azure-vms"></a>빠른 시작 가이드: Azure VM에 단일 인스턴스 SAP HANA 수동 설치
## <a name="introduction"></a>소개
이 빠른 시작 가이드를 사용하면 SAP NetWeaver 7.5 및 SAP HANA SP12를 수동으로 설치할 때 Azure VM(가상 컴퓨터)에서 단일 인스턴스 SAP HANA 프로토타입 또는 데모 시스템을 설정할 수 있습니다.

이 가이드에서는 다음과 같은 Azure IaaS 기본 사항에 대해 잘 알고 있다고 가정합니다.
 * Azure Portal 또는 PowerShell을 통해 가상 컴퓨터 또는 가상 네트워크를 배포하는 방법
 * JSON(JavaScript Object Notification) 템플릿을 사용하는 옵션을 포함한 Azure 플랫폼 간 명령줄 도구(CLI)

SAP HANA, SAP NetWeaver 및 이러한 플랫폼을 온-프레미스에 설치하는 방법도 잘 알고 있다고 가정합니다.

또한 이 가이드의 끝 부분에 있는 "일반 정보" 섹션에서 언급하는 SAP Azure 설명서도 알고 있어야 합니다.

이 가이드의 내용은 프로덕션 이외의 시스템으로 한정되므로 HA(고가용성), 백업, DR(재해 복구), 고성능 또는 특수 보안 고려 사항과 같은 항목은 다루지 않습니다.

SAP-Linux-Azure는 Azure Resource Manager에서만 지원하며 클래식 배포 모델에서는 지원하지 않습니다. 따라서 Azure Resource Manager 모델을 통해 분산형 SAP NetWeaver 설치를 수행하기 위해 두 개의 가상 컴퓨터를 사용하는 샘플 설치를 수행했습니다. Resource Manager에 대한 자세한 내용은 이 가이드의 끝 부분에 있는 "일반 정보" 섹션을 참조하세요.

샘플 설치의 경우 다음 두 가지 테스트 VM을 사용했습니다.

* NetWeaver 7.5 ASCS(ABAP SAP Central Services) 인스턴스 + PAS를 호스팅하는 hana-appsrv(유형 DS3)
* HANA SP12를 호스팅하는 hana-dbsrv(형식 GS4)

두 VM은 하나의 Azure 가상 네트워크(azure-hana-test-vnet)에 속했고 두 경우 모두 OS는 SLES 12 SP1이었습니다.

2016년 7월부터 SAP HANA는 Azure VM 형식 GS5의 OLAP(BW) 프로덕션 시스템에서만 완벽하게 지원됩니다. 테스트 목적으로 공식 SAP 지원이 필요하지 않은 경우 GS4와 같이 더 낮은 버전을 사용하는 것이 좋습니다. Azure의 SAP HANA에서는 HANA 데이터 및 로그 파일에 항상 Azure Premium Storage를 사용해야 합니다(이 가이드 뒷부분의 "디스크 설정" 섹션 참조). Azure에서 지원되는 SAP 제품에 대한 자세한 내용은 이 가이드의 끝 부분에 있는 "일반 정보" 섹션을 참조하세요.

이 가이드에서는 다음 두 가지 방법으로 Azure VM에 SAP HANA를 수동으로 설치하는 방법에 대해 설명합니다.

* "데이터베이스 인스턴스 설치" 단계에서 분산형 NetWeaver 설치의 일부로 SWPM(SAP Software Provisioning Manager)을 통해 SAP HANA를 설치합니다.
* HANA 수명 주기 관리 도구(hdblcm)를 사용하여 SAP HANA를 설치한 후 나중에 NetWeaver를 설치합니다.

또한 SWPM을 사용하여 하나의 단일 VM에 모든 구성 요소(SAP HANA, SAP 응용 프로그램 서버, ASCS 인스턴스, SAP GUI)를 설치할 수도 있습니다. 이 옵션은 가이드에서 설명하지 않지만 고려해야 할 항목은 같습니다.

설치를 시작하기 전에 SAP HANA 설치를 위한 두 개의 검사 목록 바로 뒤에 나오는 "SAP HANA 수동 설치를 위한 Azure VM 준비"를 반드시 읽어보세요. 이렇게 하면 기본 Azure VM 구성만 사용할 때 발생할 수 있는 몇 가지 기본적인 실수를 방지할 수 있습니다.

## <a name="checklist-for-sap-hana-installation-using-sap-swpm"></a>SAP SWPM으로 SAP HANA 설치를 위한 검사 목록
이 섹션에서는 SAP SWPM을 사용하여 분산형 SAP NetWeaver 7.5를 설치할 때의 데모 또는 프로토타입을 위한 단일 인스턴스 SAP HANA 수동 설치의 주요 단계를 나열합니다. 문서 전체에서 항목을 개별적으로 설명하고 스크린샷 형식으로 자세히 보여 줍니다.

* 2개 테스트 VM이 포함된 Azure가상 네트워크 만들기
* Azure Resource Manager 모델에 따라 OS SLES/SLES-for-SAP 응용 프로그램 12 SP1이 설치된 2개의 Azure VM 배포
* 앱 서버 VM에 2개 표준 저장소 디스크 연결(예: 75GB 및 500GB 디스크)
* HANA DB 서버 VM에 4개 디스크 연결 - 2개 표준 저장소 디스크(앱 서버 VM용) + 2개 프리미엄 저장소 디스크(예: 2x512GB 디스크)
* 크기 또는 처리량 요구 사항에 따라 여러 디스크 연결 및 VM 내부의 OS 수준에서 LVM(논리 볼륨 관리) 또는 mdadm(다중 장치 관리 유틸리티)을 사용하여 스트라이프 볼륨 만들기
* 연결된 디스크/논리 볼륨에 XFS 파일 시스템 만들기
* OS 수준에서 새 XFS 파일 시스템 탑재 - 파일 시스템 하나를 사용하여 모든 SAP 소프트웨어를 보관하고, 다른 하나는 예를 들어 /sapmnt 디렉터리와 백업용으로 사용할 수 있습니다. SAP HANA DB 서버에서 XFS 파일 시스템을 프리미엄 저장소 디스크(예: /hana 및 /usr/sap)에 탑재합니다. 이 프로세스는 Linux Azure VM에서 크지 않은 루트 파일 시스템이 가득 차지 않도록 방지하기 위해 필요합니다.
* /etc/hosts에 테스트 VM의 로컬 IP 주소 입력
* /etc/fstab에 nofail 매개 변수 입력
* HANA-SLES-12 SAP Note에 따라 커널 매개 변수 설정 - 자세한 내용은 "커널 매개 변수" 섹션을 참조하세요.
* 교환 공간 추가
* 선택적으로 테스트 VM에 그래픽 데스크톱 설치, 그렇지 않은 경우 원격 SAPinst 설치 사용
* SAP Service Marketplace에서 SAP 소프트웨어 다운로드
* 앱 서버 VM에 SAP ASCS 인스턴스 설치
* NFS를 사용하여 테스트 VM 간에 /sapmnt 디렉터리 공유 - 앱 서버 VM은 NFS 서버입니다.
* DB 서버 VM에서 SWPM을 사용하여 HANA를 포함한 데이터베이스 인스턴스 설치
* 앱 서버 VM에 PAS(기본 응용 프로그램 서버) 설치
* SAP MC(관리 콘솔) 시작 및 연결(예: SAP GUI/HANA Studio)

## <a name="checklist-for-sap-hana-installation-using-hdblcm"></a>hdblcm으로 SAP HANA 설치를 위한 검사 목록
이 섹션에서는 SAP hdblcm을 사용하여 분산형 SAP NetWeaver 7.5를 설치할 때의 데모 또는 프로토타입을 위한 단일 인스턴스 SAP HANA 수동 설치의 주요 단계를 나열합니다. 문서 전체에서 항목을 개별적으로 설명하고 스크린샷 형식으로 자세히 보여 줍니다.

* 2개 테스트 VM이 포함된 Azure가상 네트워크 만들기
* Azure Resource Manager 모델에 따라 OS SLES/SLES-for-SAP 응용 프로그램 12 SP1이 설치된 2개의 Azure VM 배포
* 앱 서버 VM에 2개 표준 저장소 디스크 연결(예: 75GB 및 500GB 디스크)
* HANA DB 서버 VM에 4개 디스크 연결 - 2개 표준 저장소 디스크(앱 서버 VM용) + 2개 프리미엄 저장소 디스크(예: 2x512GB 디스크)
* 크기 또는 처리량 요구 사항에 따라 여러 디스크 연결 및 VM 내부의 OS 수준에서 LVM(논리 볼륨 관리) 또는 mdadm(다중 장치 관리 유틸리티)을 사용하여 스트라이프 볼륨 만들기
* 연결된 디스크/논리 볼륨에 XFS 파일 시스템 만들기
* OS 수준에서 새 XFS 파일 시스템 탑재 - 파일 시스템 하나를 사용하여 모든 SAP 소프트웨어를 보관하고, 다른 하나는 예를 들어 /sapmnt 디렉터리와 백업용으로 사용할 수 있습니다. SAP HANA DB 서버에서 XFS 파일 시스템을 프리미엄 저장소 디스크(예: /hana 및 /usr/sap)에 탑재합니다. 이 프로세스는 Linux Azure VM에서 크지 않은 루트 파일 시스템이 가득 차지 않도록 방지하기 위해 필요합니다.
* /etc/hosts에 테스트 VM의 로컬 IP 주소 입력
* /etc/fstab에 nofail 매개 변수 입력
* HANA-SLES-12 SAP Note에 따라 커널 매개 변수 설정 - 자세한 내용은 "커널 매개 변수" 섹션을 참조하세요.
* 교환 공간 추가
* 선택적으로 테스트 VM에 그래픽 데스크톱 설치, 그렇지 않은 경우 원격 SAPinst 설치 사용
* SAP Service Marketplace에서 SAP 소프트웨어 다운로드
* HANA DB 서버 VM에 1001 그룹 ID인 "sapsys" 그룹 만들기
* HDBLCM(HANA 데이터베이스 수명 주기 관리자)을 사용하여 DB 서버 VM에 SAP HANA 설치
* 앱 서버 VM에 SAP ASCS 인스턴스 설치
* NFS를 사용하여 테스트 VM 간에 /sapmnt 디렉터리 공유 - 앱 서버 VM은 NFS 서버입니다.
* HANA DB 서버 VM에 SWPM을 통해 HANA를 포함한 데이터베이스 인스턴스 설치
* 앱 서버 VM에 PAS(기본 응용 프로그램 서버) 설치
* SAP MC 시작 및 SAP GUI/HANA Studio를 통해 연결

## <a name="prepare-azure-vms-for-a-manual-installation-of-sap-hana"></a>SAP HANA의 수동 설치를 위한 Azure VM 준비
이 섹션에서는 다음 항목을 다룹니다.

* OS 업데이트
* 디스크 설정
* 커널 매개 변수
* 파일 시스템
* /etc/hosts
* /etc/fstab

### <a name="os-updates"></a>OS 업데이트
SUSE가 보안 및 원활한 작업을 유지 관리할 수 있는 OS의 업데이트 및 픽스를 제공하므로 추가 소프트웨어를 설치하기 전에 사용할 수 있는 업데이트가 있는지 확인하는 것이 좋습니다.
시스템이 실제 패치 수준인 경우 지원 호출을 여러 번 회피할 수 있습니다.

Azure 주문형 이미지는 추가 소프트웨어 및 업데이트를 제공하는 SUSE 업데이트 인프라에 자동으로 연결됩니다.
BYOS 이미지를 SUSE 고객 센터( https://scc.suse.com )에 등록해야 합니다.

다음 항목을 사용하여 사용 가능한 패치를 단순히 확인합니다.

 `sudo zypper list-patches`

오류의 종류에 따라 패치는 범주 및 심각도별로 분류됩니다.

범주에 대해 자주 사용되는 값은 보안, 권장됨, 선택 사항, 기능, 문서 또는 YaST입니다.

심각도에 대해 자주 사용되는 값은 위험, 중요, 보통, 낮음 또는 지정되지 않음입니다.

zypper는 설치된 패키지에 필요한 업데이트를 찾습니다.

따라서 예제 명령은 다음과 같습니다.

`sudo zypper patch  --category=security,recommended --severity=critical,important`

매개 변수 *--dry-run*을 추가하는 경우 업데이트를 테스트할 수 있지만 실제로 시스템을 업데이트하지 않습니다.


### <a name="disk-setup"></a>디스크 설정
Azure에서 Linux VM의 루트 파일 시스템 크기는 제한됩니다. 따라서 SAP를 실행하기 위한 추가 디스크 공간을 VM에 연결해야 합니다. SAP 앱 서버 VM을 순수 프로토타입 또는 데모 환경에서 사용하는 경우 Azure 표준 저장소 디스크를 사용하는 것이 좋습니다. SAP HANA DB 데이터 및 로그 파일의 경우 프로덕션 환경이 아닌 환경에서도 Azure 프리미엄 저장소 디스크를 사용합니다.

자세한 내용은 [Linux VM에 디스크를 연결하는 방법](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.

Azure 디스크 캐싱의 경우 HANA 트랜잭션 로그를 저장하는 데 사용할 디스크에 **없음**을 입력합니다. HANA 데이터 파일의 경우 읽기 캐싱을 사용하는 것이 좋습니다. HANA는 메모리 내 데이터베이스이므로 Azure 디스크 수준의 읽기 캐시가 성능을 향상시킬 수 있는 정도(예: HANA 시작 및 디스크에서 메모리로 데이터 읽기)는 전체 사용 패턴에 따라 다릅니다.

자세한 내용은 [Premium Storage: Azure Virtual Machine 워크로드를 위한 고성능 저장소](../storage/storage-premium-storage.md)를 참조하세요.

VM 만들기를 위한 샘플 JSON 템플릿을 찾으려면 [Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates)으로 이동합니다.
"vm-simple-sles" 템플릿은 추가 100GB 데이터 디스크가 있는 저장소 섹션을 포함하고 있는 기본 템플릿을 보여 줍니다.

PowerShell 또는 CLI를 사용하여 SUSE 이미지를 찾는 방법을 알아보고 UUID를 사용하여 디스크를 연결하는 중요성을 이해하려면 [Microsoft Azure SUSE Linux VM에서 SAP NetWeaver 실행](virtual-machines-linux-sap-on-suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.

시스템의 크기 또는 처리량 요구 사항에 따라 둘 이상의 여러 디스크를 연결하고 나중에 OS 수준에서 디스크에 스트라이프 세트를 만들어야 할 수도 있습니다. 다음과 같은 두 가지 이유로 여러 Azure 디스크에 스트라이프 세트를 만듭니다.

* 처리량을 늘릴 수 있습니다.
* 현재 Azure 디스크 크기 제한이 1TB이므로(2016년 7월 기준) 1TB보다 큰 단일 파일 시스템이 필요합니다.

스트라이핑을 구성하기 위한 두 가지 주요 도구에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Linux에서 소프트웨어 RAID 구성](virtual-machines-linux-configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure에서 Linux VM에 LVM 구성](virtual-machines-linux-configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

테스트 환경에서 다음 스크린샷과 같이&2;개 Azure 표준 저장소 디스크가 SAP 앱 서버 VM에 연결되었습니다. 한 디스크에는 설치를 위한 모든 SAP 소프트웨어(즉, NetWeaver 7.5, SAP GUI, SAP HANA 등)를 저장했습니다. 다른 한 디스크에는 추가 요구 사항(예: 백업 및 테스트 데이터) 및 동일한 SAP 환경에 속한 모든 VM 간에 공유할 /sapmnt 디렉터리에 사용할 수 있을 만큼 충분한 여유 공간을 확보하도록 했습니다.

![2개 데이터 디스크와 해당 크기를 표시하는 SAP HANA 앱 서버 디스크 창](./media/virtual-machines-linux-sap-hana-get-started/image003.jpg)

앱 서버 VM 시나리오와 달리 다음 스크린샷과 같이&4;개 디스크가 SAP HANA 서버 VM에 연결되었습니다. 2개 디스크는 SAP 소프트웨어를 저장하는 데 사용되었으며(NFS를 사용하여 SAP 소프트웨어 디스크를 공유할 수도 있음) 사용 가능한 여유 공간(예: 백업용)을 확보했습니다. 2개 추가 디스크는 SAP HANA 데이터와 로그 파일 및 /usr/sap 디렉터리를 저장하는 Azure 프리미엄 저장소 디스크였습니다.

![4개 데이터 디스크와 해당 크기를 표시하는 SAP HANA 앱 서버 디스크 창](./media/virtual-machines-linux-sap-hana-get-started/image004.jpg)

### <a name="kernel-parameters"></a>커널 매개 변수
SAP HANA에는 표준 Azure 갤러리 이미지의 일부가 아닌 특정 Linux 커널 설정이 필요하며, 수동으로 설정해야 합니다. SAP HANA Note에서 SLES 12/SLES for SAP Applications 12에 권장되는 운영 체제 설정에 대해 설명합니다([SAP Note 2205917](https://launchpad.support.sap.com/#/notes/2205917)).


SLES-for-SAP Applications 12 GA 및 SP1에는 이전 sapconf 유틸리티를 대체하는 새로운 도구가 있습니다. tuned-adm이라고 하며, 특별한 SAP HANA 프로필을 사용할 수 있습니다. SAP HANA에 대해 시스템을 조정하려면 루트 사용자로 'tuned-adm profile sap-hana'를 입력하면 됩니다.

tuned-adm에 대한 자세한 내용은 다음에 있는 SUSE 문서를 참조하세요.

* [tuned-adm profile sap-hana에 대한 SLES-for-SAP Applications 12 SP1 설명서](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)

다음 스크린샷에서는 필수 SAP HANA 설정에 따라 tuned-adm에서 transparent_hugepage 및 numa_balancing 값을 변경한 방법을 확인할 수 있습니다.

![tuned-adm 도구는 필수 SAP HANA 설정에 따라 값을 변경합니다.](./media/virtual-machines-linux-sap-hana-get-started/image005.jpg)

SAP HANA 커널을 영구적으로 설정하려면 SLES 12에서 grub2를 사용합니다. grub2에 대한 자세한 내용은 [SUSE 설명서의 "구성 파일 구조" 섹션](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)(영문)을 참조하세요.

다음 스크린샷에서는 구성 파일에서 커널 설정을 변경한 다음 grub2-mkconfig를 사용하여 컴파일하는 방법을 보여 줍니다.

![커널 설정은 구성 파일에서 변경되고 grub2-mkconfig를 사용하여 컴파일됩니다.](./media/virtual-machines-linux-sap-hana-get-started/image006.jpg)

또 다른 옵션은 YaST 및 **부팅 로더** > **커널 매개 변수** 설정을 사용하여 커널 설정을 변경할 수 있습니다.

![YaST 부팅 로더의 커널 매개 변수 설정 탭](./media/virtual-machines-linux-sap-hana-get-started/image007.jpg)

### <a name="file-systems"></a>파일 시스템
다음 스크린샷에서는&2;개 Azure 표준 저장소 디스크에 연결된 SAP 앱 서버 VM에 만들어진&2;개 파일 시스템을 보여 줍니다. 두 파일 시스템은 모두 XFS 형식이며 /sapdata 및 /sapsoftware에 탑재됩니다.

파일 시스템을 반드시 이 방식으로 구조화하는 것은 아닙니다. 디스크 공간을 구조화하기 위한 다른 옵션이 있습니다. 가장 중요한 고려 사항은 루트 파일 시스템의 여유 공간이 부족하지 않도록 하는 것입니다.

![SAP 앱 서버 VM에 만든&2;개 파일 시스템](./media/virtual-machines-linux-sap-hana-get-started/image008.jpg)

SAP HANA DB VM과 관련하여 데이터베이스 설치 중에 SAPinst(SWPM) 및 간단한 "일반" 설치 옵션을 사용할 때는 기본적으로 /hana 및 /usr/sap 아래에 모든 항목을 설치한다는 것을 알고 있어야 합니다. SAP HANA 로그 백업의 기본 설정은 /usr/sap 아래에 있습니다. 즉 루트 파일 시스템의 저장소 공간이 부족하지 않도록 하는 것이 중요하기 때문에 SWPM을 사용하여 SAP HANA를 설치하기 전에 /hana 및 /usr/sap 아래에 충분한 여유 공간이 있는지 확인합니다.

SAP HANA의 표준 파일 시스템 레이아웃에 대한 설명은 [SAP HANA 서버 설치 및 업데이트 가이드](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm)(영문)를 참조하세요.
![SAP 앱 서버 VM에 만든 추가 파일 시스템](./media/virtual-machines-linux-sap-hana-get-started/image009.jpg)

표준 SLES/SLES-for-SAP 응용 프로그램 12 Azure 갤러리 이미지에 SAP NetWeaver를 설치하면 교환 공간이 없다는 메시지가 표시됩니다. 이 메시지를 해제하려면 dd, mkswap 및 swapon을 사용하여 교환 파일을 수동으로 추가하면 됩니다. 자세한 내용은 [SUSE 설명서의 "YaST Partitioner 사용" 섹션](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)(영문)에서 "수동으로 교환 파일 추가"를 검색해 보세요.

또 다른 옵션은 Linux VM 에이전트를 사용하여 교환 공간을 구성하는 것입니다. 자세한 내용은 [Azure Linux 에이전트 사용자 가이드](virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

![교환 공간이 부족하다고 알리는 팝업 메시지](./media/virtual-machines-linux-sap-hana-get-started/image010.jpg)


### <a name="etchosts"></a>/etc/hosts
SAP 설치를 시작하기 전에 SAP VM의 호스트 이름 및 IP 주소를 /etc/hosts 파일에 포함하는 것이 중요합니다. 하나의 Azure 가상 네트워크 내에 모든 SAP VM을 배포한 다음 내부 IP 주소를 사용합니다.

![/etc/hosts 파일에 나열된 SAP VM의 호스트 이름 및 IP 주소](./media/virtual-machines-linux-sap-hana-get-started/image011.jpg)

### <a name="etcfstab"></a>/etc/fstab

테스트 단계에서 fstab에 nofail 매개 변수를 추가하는 것이 좋습니다. 디스크에 오류가 발생하더라도 VM이 계속 작동하며 부팅 프로세스에서 중단하지 않습니다. 그러나 이 시나리오에서와 같이 추가 디스크 공간을 사용할 수 없으며 프로세스가 루트 파일 시스템을 가득 채울 수 있으므로 세심한 주의를 기울여야 합니다. /hana가 누락되면 SAP HANA가 시작되지 않습니다.

![fstab에 nofail 매개 변수 추가](./media/virtual-machines-linux-sap-hana-get-started/image000c.jpg)

## <a name="install-graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>SLES 12/SLES-for-SAP 응용 프로그램 12에 그래픽 Gnome 데스크톱 설치
이 섹션에서는 다음 항목을 다룹니다.

* SLES 12/SLES-for-SAP 응용 프로그램 12에 Gnome 데스크톱 및 xrdp 설치
* SLES 12/SLES-for-SAP 응용 프로그램 12에서 Firefox를 사용하여 Java 기반 SAP MC 실행

Xterminal 또는 VNC와 같은 대안을 사용할 수도 있지만 이 가이드에서는 2016년 9월부터 xrdp만 설명합니다.

### <a name="installing-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>SLES 12/SLES-for-SAP 응용 프로그램 12에 Gnome 데스크톱 및 xrdp 설치
백그라운드에서 Microsoft Windows를 사용하는 경우 SAP Linux VM 내에서 그래픽 데스크톱을 직접 사용하여 Firefox, Sapinst, SAP GUI, SAP MC 또는 HANA Studio를 실행하고 Windows 컴퓨터에서 RDP를 통해 VM에 연결할 수 있습니다. 프로시저가 프로덕션 데이터베이스 서버에 적합하지 않을 수 있지만 순수 프로토타입/데모 환경에는 적합합니다. Azure SLES 12/SLES-for-SAP 응용 프로그램 12 VM에 Gnome 데스크톱을 설치하는 방법은 다음과 같습니다.

다음 명령을 입력하여 gnome 데스크톱을 설치합니다(예: Putty 창에서).

`zypper in -t pattern gnome-basic`

xrdp를 설치하여 RDP를 통해 VM에 연결할 수 있도록 합니다.

`zypper in xrdp`

/etc/sysconfig/windowmanager를 편집하고 기본 창 관리자를 Gnome으로 설정합니다.

`DEFAULT_WM="gnome"`

chkconfig를 실행하여 다시 부팅한 후 xrdp가 자동으로 시작되도록 합니다.

`chkconfig -level 3 xrdp on`

RDP 연결에 문제가 있으면 다시 시작합니다(아마도 Putty 창에서).

`/etc/xrdp/xrdp.sh restart`

앞에서 설명한 대로 xrdp restart가 작동하지 않으면 .pid 파일이 있는지 확인하고 제거합니다.

`check /var/run` 및 `xrdp.pid` 찾기

제거한 다음 restart를 다시 시도합니다.

### <a name="sap-mc"></a>SAP MC
Gnome 데스크톱을 설치한 후 Azure SLES 12/SLES-for-SAP 응용 프로그램 12 VM에서 실행되는 Firefox에서 그래픽 Java 기반 SAP MC를 시작하면 누락된 Java 브라우저 플러그 인으로 인해 오류를 표시할 수 있습니다.

SAP MC를 시작할 URL은 <server>:5<instance_number>13입니다.

자세한 내용은 [웹 기반 SAP 관리 콘솔 시작](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm)(영문)을 참조하세요.

다음 스크린샷에서는 Java 브라우저 플러그 인이 없는 경우 표시되는 오류 메시지를 보여 줍니다.

![누락된 Java 브라우저 플러그 인을 나타내는 오류 메시지](./media/virtual-machines-linux-sap-hana-get-started/image013.jpg)

이 문제를 간단히 해결하는 한 가지 방법은 다음 스크린샷과 같이 YaST를 사용하여 누락된 플러그 인을 설치하는 것입니다.

![YaST를 사용하여 누락된 플러그 인 설치](./media/virtual-machines-linux-sap-hana-get-started/image014.jpg)

SAP 관리 콘솔 URL을 반복하면 플러그 인을 활성화하도록 요청하는 대화 상자를 표시합니다.

![플러그 인 활성화를 요청하는 대화 상자](./media/virtual-machines-linux-sap-hana-get-started/image015.jpg)

발생할 수 있는 또 다른 문제 하나는 누락된 javafx.properties 파일에 대한 오류 메시지입니다. 이 항목은 SAP GUI 7.4용 Oracle Java 1.8의 요구 사항과 관련이 있습니다. [SAP 참고 사항 2059429 참조](https://launchpad.support.sap.com/#/notes/2059424) IBM Java 버전 또는 SLES/SLES-for-SAP 응용 프로그램 12와 함께 제공된 openjdk 패키지에는 필요한 javafx가 포함됩니다. 솔루션은 Oracle에서 Java SE8를 다운로드하고 설치하는 것입니다.

openjdk와 openSUSE에 대한 비슷한 문제를 다루는 문서는 [SAPGui 7.4 Java for openSUSE 42.1 Leap](https://scn.sap.com/thread/3908306)에서 찾을 수 있습니다.

## <a name="install-sap-hana-manually-by-using-swpm-as-part-of-a-netweaver-75-installation"></a>NetWeaver 7.5 설치의 일부로 SWPM을 사용하여 수동으로 SAP HANA 설치
이 섹션에서 보여 주는 일련의 스크린샷은 SWPM(SAPinst)을 사용할 때 SAP NetWeaver 7.5 및 SAP HANA SP12를 설치하기 위한 주요 단계를 보여 줍니다. NetWeaver 7.5 설치의 일부로 SWPM은 HANA 데이터베이스를 단일 인스턴스로 설치할 수도 있습니다.

샘플 테스트 환경에서는 ABAP(Advanced Business Application Programming) 앱 서버 하나만 설치했습니다. 다음 스크린샷에서는 "분산 시스템" 옵션을 사용하여 하나의 Azure VM에 ASCS 및 기본 응용 프로그램 서버 인스턴스를, 다른 하나의 Azure VM에 데이터베이스 시스템으로 SAP HANA를 설치했습니다.

!["분산 시스템" 옵션을 사용하여 설치된 ASCS 및 기본 응용 프로그램 서버 인스턴스](./media/virtual-machines-linux-sap-hana-get-started/image012.jpg)

ASCS 인스턴스를 앱 서버 VM에 설치하고 SAP 관리 콘솔에서 "녹색"으로 설정한 후에는 SAP 프로필 디렉터리를 포함한 /sapmnt 디렉터리를 SAP HANA DB 서버 VM과 공유해야 합니다. DB 설치 단계에서 이 정보에 액세스해야 합니다. 액세스를 제공하는 가장 좋은 방법은 YaST를 사용하여 구성할 수 있는 NFS를 사용하는 것입니다.

![앱 서버 VM에 설치하되 "녹색"으로 설정된 ASCS 인스턴스를 보여 주는 SAP 관리 콘솔](./media/virtual-machines-linux-sap-hana-get-started/image016.jpg)

앱 서버 VM에서 /sapmnt 디렉터리는 **rw** 및 **no_root_squash** 옵션을 사용하여 NFS를 통해 공유해야 합니다. 기본값은 "ro" 및 "root_squash"이며, 데이터베이스 인스턴스를 설치할 때 문제가 발생할 수 있습니다.

!["rw" 및 "no_root_squash" 옵션을 사용하여 NFS를 통한 /sapmnt 디렉터리 공유](./media/virtual-machines-linux-sap-hana-get-started/image017b.jpg)

다음 스크린샷에서 보여 주듯이 앱 서버 VM의 /sapmnt 공유는 **NFS 클라이언트**를 사용하여(YaST 사용) SAP HANA DB 서버 VM에 구성해야 합니다.

!["NFS 클라이언트"를 사용하여 구성된 /sapmnt 공유](./media/virtual-machines-linux-sap-hana-get-started/image018b.jpg)

분산형 NetWeaver 7.5 설치를 수행하려면 다음 스크린샷과 같이 **데이터베이스 인스턴스**에서 SAP HANA DB 서버 VM에 로그인하고 SWPM을 시작합니다.

![SAP HANA DB 서버 VM에 로그인하고 SWPM을 시작하여 데이터베이스 인스턴스 설치](./media/virtual-machines-linux-sap-hana-get-started/image019.jpg)

"일반" 설치 및 설치 미디어 경로를 선택한 후 DB SID, 호스트 이름, 인스턴스 번호 및 DB 시스템 관리자 암호를 입력합니다.

![SAP HANA 데이터베이스 시스템 관리자 로그인 페이지](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

DBACOCKPIT 스키마의 암호를 입력합니다.

![DBACOCKPIT 스키마의 암호 입력 상자](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

SAPABAP1 스키마 암호에 대한 질문을 입력합니다.

![SAPABAP1 스키마 암호에 대한 질문 입력](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

작업이 완료되면 DB 설치 프로세스의 각 단계 옆에 녹색 확인 표시가 나타납니다. "데이터베이스 인스턴스의 실행이 완료되었습니다."라는 작은 메시지 상자가 나타나야 합니다.

![확인 메시지가 표시된 작업 완료 창](./media/virtual-machines-linux-sap-hana-get-started/image023.jpg)

성공적으로 설치되면 SAP 관리 콘솔에서 DB 인스턴스를 "녹색"으로 표시하고 SAP HANA 프로세스의 전체 목록(hdbindexserver, hdbcompileserver 등)을 표시해야 합니다.

![SAP HANA 프로세스 목록을 보여 주는 SAP 관리 콘솔 창](./media/virtual-machines-linux-sap-hana-get-started/image024.jpg)

다음 스크린샷에서는 HANA 설치 중에 SWPM에서 만든 /hana/shared 아래의 파일 구조 일부를 보여 줍니다. 다른 경로를 지정하는 옵션이 없으므로 SWPM을 사용하여 SAP HANA를 설치하기 전에 /hana 아래에 추가 디스크 공간을 탑재하여 루트 파일 시스템의 여유 공간이 부족하지 않도록 해야 합니다.

![HANA 설치 중에 만들어진 /hana/shared 파일 구조](./media/virtual-machines-linux-sap-hana-get-started/image025.jpg)

다음 스크린샷에서는 /usr/sap 디렉터리의 파일 구조를 보여 줍니다.

![/usr/sap 디렉터리 파일 구조](./media/virtual-machines-linux-sap-hana-get-started/image026.jpg)

분산형 ABAP 설치의 마지막 단계는 "기본 응용 프로그램 서버 인스턴스"입니다.

![마지막 단계로 기본 응용 프로그램 서버 인스턴스를 보여 주는 ABAP 설치](./media/virtual-machines-linux-sap-hana-get-started/image027b.jpg)

기본 응용 프로그램 서버 인스턴스 및 SAP GUI를 설치한 후에는 "dbacockpit" 트랜잭션을 사용하여 SAP HANA 설치가 올바르게 완료되었는지 확인합니다.

![성공적인 설치를 확인하는 DBA Cockpit 창](./media/virtual-machines-linux-sap-hana-get-started/image028b.jpg)

마지막 단계로 SAP HANA Studio를 SAP 앱 서버 VM에 설치한 다음 DB 서버 VM에서 실행 중인 SAP HANA 인스턴스에 연결할 수도 있습니다.

![SAP 앱 서버 VM에 SAP HANA Studio 설치](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

## <a name="install-sap-hana-manually-by-using-the-hana-lifecycle-management-tool-hdblcm"></a>hdblcm(HANA 수명 주기 관리 도구)을 사용하여 SAP 수동으로 HANA 설치
SWPM을 사용하여 분산 설치의 일부로 SAP HANA를 설치하는 것 외에도 먼저 hdblcm을 사용하여 독립 실행형 HANA를 설치할 수 있습니다. 그런 다음 예를 들어 SAP NetWeaver 7.5를 설치할 수 있습니다. 다음 스크린샷에서는 이 프로세스의 작동 방식을 보여 줍니다.

다음은 HANA hdblcm 도구에 대해 설명하는 세 가지 문서입니다.

* [Choosing the Correct SAP HANA HDBLCM for Your Task(작업을 위해 올바른 SAP HANA HDBLCM 선택)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)
* [SAP HANA Lifecycle Management Tools(SAP HANA 수명 주기 관리 도구)](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)
* [SAP HANA Server Installation and Update Guide(SAP HANA 서버 설치 및 업데이트 가이드)](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)

hdblcm 도구로 만든 \<HANA SID\> adm 사용자에 대한 기본 그룹 ID 설정에 문제가 발생하지 않도록 hdblcm을 통해 SAP HANA를 설치하기 전에 1001 그룹 ID를 사용하여 "sapsys"라는 새 그룹을 정의합니다.

![1001 그룹 ID를 사용하여 정의한 새로운 "sapsys" 그룹](./media/virtual-machines-linux-sap-hana-get-started/image030.jpg)

hdblcm을 처음으로 시작하면 간단한 시작 메뉴가 표시됩니다. 다음 스크린샷과 같이 항목 1, **새 시스템 설치**를 선택합니다.

![hdblcm 시작 창의 "새 시스템 설치" 옵션](./media/virtual-machines-linux-sap-hana-get-started/image031.jpg)

다음 스크린샷에서는 이전에 선택한 주요 옵션을 모두 보여 줍니다.

> [!IMPORTANT]
> HANA 로그 및 데이터 볼륨뿐만 아니라 설치 경로(이 샘플의 경우 /hana/shared) 및 /usr/sap의 이름이 지정된 디렉터리는 루트 파일 시스템의 일부가 아니어야 합니다. 디렉터리는 Azure VM 설정 섹션에서 설명한 대로 VM에 연결된 Azure 데이터 디스크에 속합니다. 이 방법은 루트 파일 시스템의 공간이 부족하게 되는 위험을 방지하는 데 도움이 됩니다. 또한 HANA 관리자가 1005 사용자 ID를 가지고 있으며 설치 전에 정의한 sapsys 그룹(1001 ID)에 속하는 것도 알 수 있습니다.

![이전에 선택한 주요 SAP HANA 구성 요소 전체 목록](./media/virtual-machines-linux-sap-hana-get-started/image032.jpg)

/etc/passwd에서 HANA \<HANA SID\>adm(다음 스크린샷의 경우 azdadm) 사용자에 대한 세부 정보를 확인할 수 있습니다.

![/etc/passwd에 나열된 HANA \<HANA SID\>adm 사용자 세부 정보](./media/virtual-machines-linux-sap-hana-get-started/image033.jpg)

hdblcm을 사용하여 SAP HANA를 설치하면 SAP HANA Studio에서 파일 구조를 볼 수 있습니다. 모든 SAP NetWeaver 테이블을 포함하는 SAPABAP1 스키마는 아직 사용할 수 없습니다.

![SAP HANA Studio의 SAP HANA 파일 구조](./media/virtual-machines-linux-sap-hana-get-started/image034.jpg)

SAP HANA를 설치한 후에는 이를 기반으로 하여 SAP NetWeaver를 설치할 수 있습니다. 다음 스크린샷에서 보여 주듯이 설치는 이전 섹션에서 설명한 대로 SWPM을 사용하여 "분산 설치"로 수행되었습니다. SWPM을 사용하여 데이터베이스 인스턴스를 설치할 때 hdblcm을 사용하여 동일한 데이터(예: 호스트 이름, HANA SID 및 인스턴스 번호)를 입력합니다. 그러면 SWPM에서 기존 HANA 설치를 사용하고 더 많은 스키마를 추가합니다.

![SWPM을 사용하여 수행한 분산 설치](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

다음 스크린샷에서는 DBACOCKPIT 스키마에 대한 데이터를 입력하는 SWPM 설치 단계를 보여 줍니다.

![DBACOCKPIT 스키마 데이터를 입력하는 SWPM 설치 단계](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

SAPABAP1 스키마에 대한 데이터를 입력합니다.

![SAPABAP1 스키마에 대한 데이터 입력](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

SWPM 데이터베이스 인스턴스 설치가 완료되면 SAP HANA Studio에서 SAPABAP1 스키마를 볼 수 있습니다.

![SAP HANA Studio의 SAPABAP1 스키마](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

마지막으로 SAP 앱 서버 및 SAP GUI 설치가 완료되면 "dbacockpit" 트랜잭션을 사용하여 HANA DB 인스턴스를 확인할 수 있습니다.

!["dbacockpit" 트랜잭션으로 확인한 HANA DB 인스턴스](./media/virtual-machines-linux-sap-hana-get-started/image039b.jpg)

## <a name="about-sap-azure-certifications-and-running-sap-hana-on-azure"></a>SAP Azure 인증 및 Azure에서 SAP HANA 실행 정보
자세한 내용은 다음 설명서를 참조하세요.
* [Azure의 Windows 가상 컴퓨터에서 SAP 사용](virtual-machines-windows-classic-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) - 클래식 모드에서 Windows OS를 사용하여 Azure에서 SAP를 실행하는 방법에 대한 SAP Azure 일반 정보
* [SAP용 Azure 빠른 시작 템플릿](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/) - 고객이 사용할 수 있는 기존 SAP 템플릿에 대한 정보
* [Linux VM(가상 컴퓨터)에서 SAP 사용](virtual-machines-linux-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - Azure Resource Manager 모델에서 Linux OS를 사용하여 Azure에서 SAP를 실행하는 방법에 대한 SAP Azure 일반 정보
* [공인 SAP HANA® 하드웨어 디렉터리](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)(영문) - 프로덕션을 위해 지원되는 Azure VM 유형을 나열하고 있는 공인 SAP HANA 하드웨어 디렉터리
* [Azure에서 가상 컴퓨터에 대한 크기](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 특히 Linux 워크로드를 위한 가상 컴퓨터 크기 정보
* [SAP Note 1928533](https://launchpad.support.sap.com/#/notes/1928533/E)(영문) - Azure에서 지원되는 모든 SAP 제품 및 SAP를 위해 지원되는 Azure VM 유형을 나열하고 있는 SAP Note
* [SAP Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E)(영문) - Azure에서 Linux VM을 사용하여 SAP "고급 모니터링"에 대한 SAP Note
* Azure "큰 인스턴스"에 SAP HANA 제공 - 이 정보가 Azure VM에서 SAP HANA를 실행하는 것이 아님을 이해해야 합니다. SAP 앱 서버가 Azure VM에서 실행되지만 SAP HANA는 운영 체제 미설치 서버에서 실행되는 하이브리드 환경에 관한 것입니다([SAP Note 2316233](https://launchpad.support.sap.com/#/notes/2316233/E)(영문)).
* [SAP Note 1102124](https://launchpad.support.sap.com/#/notes/1102124/E)(영문) - Linux의 SAPOSCOL에 대한 정보를 포함하고 있는 SAP Note
* [SAP Note 2178632](https://launchpad.support.sap.com/#/notes/2178632/E)(영문) - Microsoft Azure의 SAP용 주요 모니터링 메트릭
* [Azure Resource Manager 개요](../azure-resource-manager/resource-group-overview.md) - Azure Resource Manager에 대한 정보
* [Azure Resource Manager 템플릿 및 Azure CLI를 사용하여 가상 컴퓨터 배포 및 관리](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - 템플릿을 사용한 Linux VM 배포에 대한 정보
* [Azure Resource Manager 및 클래식 배포: 배포 모델 및 리소스 상태 이해](../azure-resource-manager/resource-manager-deployment-model.md) - Azure Resource Manager와 클래식 배포 모델의 비교

## <a name="sap-software-downloads"></a>SAP 소프트웨어 다운로드
다음 스크린샷과 같이 SAP Service Marketplace에서 소프트웨어를 다운로드할 수 있습니다.

* Linux/HANA용 NetWeaver 7.5 다운로드:

 ![NetWeaver 7.5를 다운로드하기 위한 SAP 서비스 설치 및 업그레이드 창](./media/virtual-machines-linux-sap-hana-get-started/image001.jpg)
* HANA SP12 Platform Edition 다운로드:

 ![HANA SP12 Platform Edition을 다운로드하기 위한 SAP 서비스 설치 및 업그레이드 창](./media/virtual-machines-linux-sap-hana-get-started/image002.jpg)

