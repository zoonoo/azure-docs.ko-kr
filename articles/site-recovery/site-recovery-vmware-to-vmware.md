---
title: "보조 사이트에 온-프레미스 VMWare 가상 컴퓨터 또는 물리적 서버 복제 | Microsoft Docs"
description: "Azure Site Recovery를 사용하여 VMware VM 또는 Windows/Linux 물리적 서버를 보조 사이트에 복제하려면 이 문서를 사용합니다."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: jwhit
editor: 
ms.assetid: b2cba944-d3b4-473c-8d97-9945c7eabf63
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: nisoneji
translationtype: Human Translation
ms.sourcegitcommit: 5614c39d914d5ae6fde2de9c0d9941e7b93fc10f
ms.openlocfilehash: d325ce41e79ec85e08fbf4bb86e0cd7e0edf1c8f


---
# <a name="replicate-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>보조 사이트에 온-프레미스 VMWare 가상 컴퓨터 또는 물리적 서버 복제
## <a name="overview"></a>개요
Azure Site Recovery의 InMage Scout는 온-프레미스 VMWare 사이트 간의 실시간 복제 기능을 제공합니다. InMage Scout는 Azure Site Recovery 서비스 구독에 포함되어 있습니다.

## <a name="prerequisites"></a>필수 조건
**Azure 계정**: [Microsoft Azure](https://azure.microsoft.com/) 계정이 있어야 합니다. [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)으로 시작할 수 있습니다. [자세히 알아보세요](https://azure.microsoft.com/pricing/details/site-recovery/) .

## <a name="step-1-create-a-vault"></a>1단계: 자격 증명 모음 만들기
1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 새로 만들기 > 관리 > 백업 및 사이트 복구(OMS)를 클릭합니다. 또는 찾아보기 > Recovery Services 자격 증명 모음 > 추가를 클릭합니다.
3. **이름** 에 자격 증명 모음을 식별하기 위한 이름을 지정합니다. 구독이 두 개 이상인 경우 그 중에서 하나를 선택합니다.
4. **리소스 그룹**에서 새 리소스 그룹을 만들거나 기존 리소스 그룹을 선택합니다. 필수 필드를 완료할 Azure 지역을 지정합니다.
5. **위치**에서 자격 증명 모음에 대한 지리적 지역을 선택합니다. 지원되는 지역을 확인하려면 [Azure 사이트 복구 가격](https://azure.microsoft.com/pricing/details/site-recovery/)을 참조합니다.
6. 대시보드에서 자격 증명 모음에 빠르게 액세스하려면 대시보드에 고정을 클릭하고 만들기를 클릭합니다.
7. 대시보드 > 모든 리소스와 주 Recovery Services 자격 증명 모음 블레이드에 새 자격 증명 모음이 표시됩니다.

## <a name="step-2-configure-the-vault-and-download-inmage-scout-components"></a>2단계: 자격 증명 모음을 구성하고 InMage Scout 구성 요소 다운로드
1. Recovery Services 자격 증명 모음 블레이드에서 자격 증명 모음을 선택하고 설정을 클릭합니다.
2. **설정** > **시작**에서 **Site Recovery** > 1단계: **인프라 준비** > **보호 목표**를 클릭합니다.
3. **보호 목표**에서 복구 사이트에를 선택하고 예, VMware vSphere 하이퍼바이저 사용을 선택합니다. 그런 후 확인을 클릭합니다.
4. **Scout 설치**에서 InMage Scout 8.0.1 GA 소프트웨어 및 등록 키를 클릭하여 다운로드합니다. 모든 필수 구성 요소에 대한 설치 파일은 다운로드한 zip 파일에 있습니다.

## <a name="step-3-install-component-updates"></a>3단계: 구성 요소 업데이트를 설치합니다.
최신 [업데이트](#updates)에 대해 읽습니다. 다음 순서로 서버에 업데이트 파일을 설치합니다.

1. 하나인 경우 RX 서버
2. 서버 구성
3. 프로세스 서버
4. 마스터 대상 서버
5. vContinuum 서버.
6. 원본 서버(Windows 및 Linux 서버)

다음과 같이 업데이트를 설치합니다.

1. [update](https://aka.ms/asr-scout-update4) zip 파일을 다운로드합니다. 이 zip 파일에는 다음 파일이 포함됩니다.

   * RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
   * CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe
   * UA_Windows_8.0.4.0_GA_Update_4_9035261_27Sep16.exe
   * UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
   * vCon_Windows_8.0.4.0_GA_Update_4_8921562_16Sep16.exe
   * UA update4 bits for RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
2. .zip 파일의 압축을 풉니다.<br>
3. **RX 서버**: **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz**를 RX 서버에 복사하고 압축을 풉니다. 압축을 푼 폴더에서 **/Install**을 실행합니다.<br>
4. **구성 서버/프로세스 서버**: **CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe**를 구성 서버 및 프로세스 서버에 복사합니다. 실행하려면 두 번 클릭합니다.<br>
5. **Windows 마스터 대상 서버**: 통합된 에이전트를 업데이트하려면 **UA_Windows_8.0.4.0_GA_Update_4_9035261_27Sep16.exe**를 마스터 대상 서버에 복사합니다. 실행하려면 두 번 클릭합니다. 통합된 에이전트는 원본 서버에 적용할 수도 없습니다. 이 목록의 뒷부분에서 설명했듯이 원본 서버에도 설치해야 합니다.<br>
6. **vContinuum 서버**: **vCon_Windows_8.0.4.0_GA_Update_4_8921562_16Sep16.exe**를 vContinuum 서버에 복사합니다.  VContinuum 마법사를 닫았는지 확인합니다. 실행하려면 파일을 두 번 클릭합니다.<br>
7. **Linux 마스터 대상 서버**: 통합된 에이전트를 업데이트하려면 **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz**를 마스터 대상 서버로 복사하고 압축을 풉니다. 압축을 푼 폴더에서 **/Install**을 실행합니다.<br>
8. **Windows 원본 서버**: 통합된 에이전트를 업데이트하려면 **UA_Windows_8.0.4.0_GA_Update_4_9035261_27Sep16.exe**를 원본 서버에 복사합니다. 실행하려면 두 번 클릭합니다.<br>
9. **Linux 원본 서버**: 통합된 에이전트를 업데이트하려면 Linux 서버에 해당 버전의 UA 파일을 복사하고 압축을 풉니다. 압축을 푼 폴더에서 **/Install**을 실행합니다.  예: RHEL 6.7 64비트 서버의 경우 **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz**를 서버에 복사하고 압축을 풉니다. 압축을 푼 폴더에서 **/Install**을 실행합니다.

## <a name="step-4-set-up-replication"></a>4단계: 복제 설정
1. 원본과 대상 VMware 사이트 간 복제를 설정합니다.
2. 지침은 제품과 함께 다운로드된 InMage Scout 설명서를 사용합니다. 또는 다음과 같이 설명서에 액세스할 수 있습니다.

   * [릴리스 정보](https://aka.ms/asr-scout-release-notes)
   * [호환성 매트릭스](https://aka.ms/asr-scout-cm)
   * [사용자 가이드](https://aka.ms/asr-scout-user-guide)
   * [RX 사용자 가이드](https://aka.ms/asr-scout-rx-user-guide)
   * [빠른 설치 가이드](https://aka.ms/asr-scout-quick-install-guide)

## <a name="updates"></a>업데이트
### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery 서비스 Scout 8.0.1 업데이트 4
Scout 업데이트 4는 누적 업데이트입니다. 업데이트 1부터 업데이트 3까지의 모든 수정 프로그램과 다음 새 버그 수정 및 향상된 기능이 있습니다.

**새 플랫폼 지원**

* vCenter/vSphere 6.0, 6.1 및 6.2에 대한 지원이 추가되었습니다.
* 다음 Linux 운영 체제에 대한 지원이 추가되었습니다.
  * Red Hat Enterprise Linux(RHEL)7.0, 7.1 및 7.2
  * CentOS 7.0, 7.1 및 7.2
  * Red Hat Enterprise Linux(RHEL) 6.8
  * CentOS 6.8

> [!NOTE]
> RHEL/CentOS 7 64비트 **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz**가 기본 Scout GA 패키지 **InMage_Scout_Standard_8.0.1 GA.zip**에 패키지되어 있습니다. [1단계](#step-1-create-a-vault)에 설명된 대로 Scout GA 패키지를 다운로드합니다.
>
>

**버그 수정 및 향상된 기능**

* 원치 않는 다시 동기화 문제를 방지하기 위해 다음 Linux OS 및 복제본에 대한 종료 처리가 향상되었습니다.
  * Red Hat Enterprise Linux(RHEL) 6.x
  * Oracle Linux(OL) 6.x
* Linux에 대해 통합된 에이전트 설치 디렉터리의 전체 폴더 액세스 권한이 이제 로컬 사용자로만 제한되었습니다.
* Windows에서 SQL 및 SharePoint 클러스터와 같은 과도한 부하의 분산 응용 프로그램에서 일반적인 분산된 일관성 북마크가 발생하는 동안의 시간 초과 문제가 해결되었습니다.
* CX 기본 설치 관리자에 로그 관련 수정 프로그램이 추가되었습니다.
* Windows 마스터 대상 기본 설치 관리자에 VMware vCLI 6.0 다운로드 링크가 추가되었습니다.
* 장애 조치 및 DR 드릴 중 네트워크 구성 변경에 대한 추가 확인 및 로그가 추가되었습니다.
* 보존 정보가 CX에 보고되지 않는 경우가 있습니다.  
* 실제 클러스터의 경우 원본 볼륨 축소가 발생했을 때 vContinuum 마법사를 통해 볼륨 크기 조정 작업이 실패합니다.
* 클러스터 디스크가 PRDM 디스크인 경우 "디스크 서명을 찾을 수 없습니다" 오류로 인해 클러스터 보호에 실패합니다.
* 범위 이탈 예외로 인해 cxps 전송 서버 작동이 중단됩니다.
* vContinuum 마법사의 푸시 설치 페이지에서 서버 이름 및 IP 열의 크기를 조정할 수 있습니다.
* RX API 향상 기능
  * 사용 가능한 5개의 최신 공통된 일관성 지점을 제공합니다(보장됨 태그만).
  * 보호된 모든 장치에 대한 용량 및 여유 공간 세부 정보를 제공합니다.
  * 원본 서버에서 Scout 드라이버 상태를 제공합니다.

> [!NOTE]
> * 이제 **InMage_Scout_Standard_8.0.1_GA.zip** 기본 패키지에서 CX 기본 설치 관리자 **InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe** 및 Windows 마스터 대상 기본 설치 관리자 **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**가 업데이트되었습니다. 모든 신규 설치에 새 CX 및 Windows 마스터 대상 GA 비트가 사용됩니다.
> * 업데이트 4를 8.0.1 GA에 직접 적용할 수 있습니다.
> * 구성 서버 및 RX 업데이트는 시스템에 적용하면 다시 롤백할 수 없습니다.
>
>

### <a name="azure-site-recovery-scout-801-update-3"></a>Azure Site Recovery 서비스 Scout 8.0.1 업데이트 3
업데이트 3에는 다음 버그 수정 및 향상된 기능이 포함됩니다.

* 구성 서버 및 RX가 프록시 뒤에 있는 경우 Site Recovery 자격 증명 모음에 등록하는 데 실패했습니다.
* 복구 지점 목표(RPO)가 충족되지 않은 시간 수는 상태 보고서에 업데이트 되지 않습니다.
* ESX 하드웨어 세부 설명 및 네트워크 세부 설명이 UTF-8 문자를 포함하는 경우 구성 서버는 RX와 동기화하지 않습니다.
* Windows Server 2008 R2 도메인 컨트롤러가 복구 후 부팅되지 않습니다.
* 오프라인 동기화는 예상대로 작동하지 않습니다.
* 가상 컴퓨터(VM) 장애 조치 후 복제 쌍 삭제는 오랜 시간 동안 CX UI에서 중단되고 사용자는 장애 복구를 완료하거나 작업을 재개할 수 없습니다.
* 응용 프로그램을 감소시킬 수 있도록 최적화된 일관성 작업에 의해 수행된 전체 스냅숏 작업은 SQL 클라이언트와 같은 연결을 끊습니다.
* Windows에서 스냅숏을 만드는 데 필요한 메모리 사용량을 줄여 일관성 도구 (VACP.exe)의 성능이 향상되었습니다.
* 암호가 16자 이상인 경우 푸시 설치 서비스가 충돌합니다.
* 자격 증명을 변경할 경우 vContinuum은 새로운 vCenter 자격 증명을 검사하고 묻는 메시지를 표시하지 않습니다.
* Linux에서 마스터 대상 캐시 관리자(cachemgr)는 복제 쌍을 제한하는 프로세스 서버에서 파일을 다운로드하지 않습니다.
* 물리적 장애 조치 클러스터 (MSCS) 디스크 순서가 모든 노드에서 같지 않을 때 클러스터 볼륨 중 일부에 복제를 설정하지 않습니다.
  <br/>클러스터를 다시 보호해야 이 수정을 활용할 수 있다는 점에 유의하세요.  
* RX가 Scout 7.1에서 Scout 8.0.1로 업그레이드된 후에는 SMTP 기능이 예상대로 작동하지 않습니다.
* 롤백 작업에 대한 로그에서 자세한 통계를 추가하여 완료하는 데 걸린 시간을 추적했습니다.
* 원본 서버에서 Linux 운영 체제에 대한 지원이 추가되었습니다.
  * RHEL(Red Hat Enterprise Linux) 6 업데이트 7
  * CentOS 6 업데이트 7
* CX 및 RX UI는 이제 비트맵 모드로 전환되는 쌍에 대한 알림을 보여 줍니다.
* 다음 보안 수정이 RX에 추가되었습니다.

| **문제 설명** | **구현 절차** |
| --- | --- |
| 매개 변수 변조를 통해 권한 부여 바이패스 |적합하지 않은 사용자에게 제한된 액세스 |
| 교차 사이트 요청 위조 |모든 페이지에 대해 임의로 생성되는 구현된 페이지 토큰 개념입니다. <br/>이를 통해 다음을 확인할 수 있습니다. <li> 동일한 사용자에 대해 단일 로그인 인스턴스만 있습니다.</li><li>페이지 새로 고침이 작동하지 않고 대시보드로 리디렉션됩니다.</li> |
| 악성 파일 업로드 |특정 확장에 대해 제한된 파일입니다. 허용된 확장자: 7z, aiff, asf, avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, log, mid, mov, mp3, mp4, mpc, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmi, rmvb, rtf, sdc, sitd, swf, sxc, sxw, tar, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml, and zip. |
| 영구 사이트 간 스크립팅 |추가된 입력 유효성 검사 |

> [!NOTE]
> * 모든 사이트 복구 업데이트는 누적됩니다. 업데이트3은 업데이트1 및 업데이트2를 모두 수정합니다. 업데이트3은 8.0.1 GA에 직접 적용할 수 있습니다.
> * 구성 서버 및 RX 업데이트는 시스템에 적용하면 다시 롤백할 수 없습니다.
>
>

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site Recovery 서비스 Scout 8.0.1 업데이트 2(2015년 12월 3일 업데이트)
업데이트 2에서 수정 사항은 다음과 같습니다.

* **구성 서버**: 사이트 복구에 구성 서버가 등록되었을 때 31일 무료 계량 기능이 예상대로 작동하지 않는 문제 해결.
* **통합 에이전트**: 버전 8.0에서 8.0.1로 업그레이드할 때 마스터 대상 서버에 업데이트가 설치되지 않는 업데이트 1 문제 해결.

### <a name="azure-site-recovery-scout-801-update-1"></a>Azure Site Recovery 서비스 Scout 8.0.1 업데이트 1
업데이트 1에는 다음 버그 수정 및 새로운 기능이 포함됩니다.

* 서버 인스턴스 당 31일간 무료 보호됩니다. 기능을 테스트 하거나 개념 증명을 설정할 수 있습니다.
  * 장애 조치 및 장애 복구를 포함한 서버의 모든 작업은 사이트 복구 Scout를 사용하여 서버를 먼저 보호한 시점부터 시작하여 처음 31일에 대해 무료입니다.
  * 32일째부터는 보호된 각 서버가 사이트를 소유하는 고객에게 Azure Site Recovery 보호를 위해 표준 인스턴스 요금으로 청구됩니다.
  * 언제든지 현재 부과되고 있는 보호된 서버의 수는 Azure Site Recovery 자격 증명의 대시보드 페이지에서 사용할 수 있습니다.
* vSphere 명령줄 인터페이스 (vCLI) 5.5 업데이트 2에 대한 지원이 추가됩니다.
* 원본 서버에서 Linux 운영 체제에 대한 추가 지원:
  * RHEL 6 업데이트 6
  * RHEL 5 업데이트 11
  * CentOS 6 업데이트 6
  * CentOS 5 업데이트 11
* 다음과 같은 문제를 해결하는 버그 수정:
  * 서버 구성 또는 RX 서버에 대한 자격 증명 모음 등록에 실패합니다.
  * 다시 시작할 때 가상 컴퓨터가 다시 보호되면 클러스터 볼륨이 예상대로 표시되지 않습니다.
  * 장애 복구는 마스터 대상 서버가 온-프레미스 프로덕션 가상 컴퓨터에서 다른 ESXi 서버에서 호스트 되는 경우 실패합니다.
  * 구성 파일 사용 권한은 8.0.1로 업그레이드 중 변경되어 보호 및 작업에 영향을 줍니다.
  * 다시 동기화 임계값은 예상대로 적용되지 않으며, 일치하지 않는 복제 동작을 수행합니다.
  * 구성 서버 인터페이스에 제대로 나타나지 않는 RPO 설정입니다. 압축되지 않은 데이터 값은 압축된 값을 잘못 표시합니다.
  * 제거 작업은 vContinuum 마법사에서 예상대로 삭제되지 않으며 복제는 구성 서버 인터페이스에서 삭제되지 않습니다.
  * vContinuum 마법사에서 MSCS 가상 컴퓨터를 보호하는 동안 디스크 보기에서 **세부 정보** 를 클릭하면 디스크는 자동으로 선택되지 않습니다.
  * P2V(physical-to-virtual) 시나리오 도중에 CIMnotify나 CqMgHost와 같은 필요한 HP 서비스는 가상 컴퓨터 복구에서 수동으로 이동되지 않습니다. 이 때문에 부팅 시간이 늘어납니다.
  * 마스터 대상 서버에 26개 이상의 디스크가 있으면 Linux 가상 컴퓨터 보호가 실패합니다.

## <a name="next-steps"></a>다음 단계
문의 사항이 있으시다면 [Azure 복구 서비스 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에 게시하세요.



<!--HONumber=Nov16_HO3-->


