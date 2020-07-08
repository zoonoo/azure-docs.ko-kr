---
title: 가상 머신 인증 - 문제 및 해결
description: 이 문서에서는 VM 이미지에 대 한 일반적인 오류 메시지에 대해 설명 합니다. 관련 솔루션에 대해서도 설명 합니다.
author: v-miegge
ms.author: v-krmall
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
ms.date: 06/16/2020
ms.openlocfilehash: 7bd3f1a5b242ee5196e92456cb3fc8c97f8f5b27
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2020
ms.locfileid: "85958534"
---
# <a name="virtual-machine-certification---issues-and-solutions"></a>가상 머신 인증 - 문제 및 해결

VM (가상 컴퓨터) 이미지를 Azure Marketplace에 게시 하는 동안 Azure 팀은 VM 이미지의 유효성을 검사 하 여 bootability, 보안 및 Azure 호환성을 보장 합니다. 고품질 테스트 중 하나라도 실패 하면 게시는 오류를 포함 하는 메시지와 함께 실패 합니다.

이 문서에서는 VM 이미지에 대 한 일반적인 오류 메시지에 대해 설명 합니다. 관련 솔루션에 대해서도 설명 합니다.

> [!NOTE]
> 개선 사항에 대 한 질문이 나 피드백이 있는 경우 [파트너 센터 지원](https://partner.microsoft.com/support/v2/?stage=1)에 문의 하세요.

## <a name="approved-base-image"></a>승인 된 기본 이미지

업데이트를 사용 하 여 이미지를 다시 게시 하는 요청을 제출 하면 파트 번호 확인 테스트 사례가 실패할 수 있습니다. 해당 인스턴스에서 이미지는 승인 되지 않습니다.

이 오류는 다른 게시자에 속하는 기본 이미지를 사용 하 고 이미지를 업데이트 한 경우에 발생 합니다. 이 경우 이미지를 게시할 수 없습니다.

이 문제를 해결 하려면 Azure Marketplace에서 최신 이미지를 검색 하 고 해당 이미지를 변경 합니다. 이미지를 검색할 수 있는 승인 된 기본 이미지를 보려면 다음을 참조 하세요.

- [Linux-이미지](../../virtual-machines/linux/endorsed-distros.md?toc=/azure/virtual-machines/linux/toc.json)
- [Windows-이미지](create-azure-vm-technical-asset.md#create-a-vm-image-using-an-approved-base))

## <a name="vm-extension-failure"></a>VM 확장 오류

이미지에서 VM 확장을 지원 하는지 여부를 확인 하려면 다음 단계를 수행 합니다.

VM 확장을 사용 하도록 설정 합니다.

1. Linux VM을 선택 합니다.
2. **진단 설정**으로 이동 합니다.
3. **저장소 계정을**업데이트 하 여 기본 행렬을 사용 하도록 설정 합니다.
4. **저장**을 선택합니다.

   ![게스트 수준 모니터링 사용](./media/vm-certification-issues-solutions-1.png)

VM 확장이 제대로 활성화 되었는지 확인 합니다.

5. VM의 **vm 확장** 탭으로 이동 하 여 **Linux 진단 확장**을 확인 합니다.
6. 상태가 **프로 비전 성공** 이면 확장 테스트 사례가 통과 한 것입니다.
7. 상태가 **프로 비전 실패** 인 경우 확장 테스트 사례가 실패 하 고 확정 된 플래그를 설정 해야 합니다.

   ![프로 비전 성공](./media/vm-certification-issues-solutions-2.png)

   VM 확장이 실패 하면 [Linux 진단 확장을 사용 하 여 메트릭 및 로그 모니터링](../../virtual-machines/extensions/diagnostics-linux.md) 으로 이동 하 여 사용 하도록 설정 합니다. VM 확장을 사용 하지 않으려면 지원 팀에 문의 하 고 확장을 사용 하지 않도록 설정 하도록 요청 합니다.

## <a name="virtual-machine-provisioning-issue"></a>가상 컴퓨터 프로 비전 문제

제품을 제출 하기 전에 VM에 대 한 프로 비전 프로세스를 엄격 하 게 준수 하는지 확인 합니다. VM을 프로 비전 하기 위한 json 형식을 보려면 [AZURE vm (가상 머신) 이미지 인증](azure-vm-image-certification.md)으로 이동 합니다.

프로 비전 문제는 다음과 같은 오류 시나리오를 포함할 수 있습니다.

|S.NO|오류|reason|솔루션|
|---|---|---|---|
|1|잘못 된 VHD (가상 하드 디스크)|VHD 바닥글의 지정 된 쿠키 값이 올바르지 않으면 VHD는 잘못 된 것으로 간주 됩니다.|이미지를 다시 만들고 요청을 제출 합니다.|
|2|잘못 된 Blob 유형|사용 된 블록이 페이지 유형이 아닌 blob 유형 이므로 VM 프로 비전에 실패 했습니다.|이미지를 다시 만들고 요청을 제출 합니다.|
|3|프로 비전 시간 초과 또는 제대로 일반화 되지 않음|VM 일반화에 문제가 있습니다.|일반화를 사용 하 여 이미지를 다시 만들고 요청을 제출 합니다.|

> [!NOTE]
> VM 일반화와 관련 된 설명서는 다음 링크를 참조 하세요.
> - [Linux](create-azure-vm-technical-asset.md#generalize-the-image))
> - [Windows](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep))

## <a name="software-compliance-for-windows"></a>Windows 용 소프트웨어 준수

소프트웨어 규정 준수로 인해 windows 이미지 요청이 거부 되 면 SQL server가 설치 된 Windows 이미지를 만들 수 있습니다 .이는 Azure Marketplace에서 관련 SQL 버전 기반 이미지를 가져오는 것이 아니라

SQL server가 설치 된 windows 이미지를 만들지 마세요. 대신 Azure Marketplace에서 승인 된 SQL 기반 이미지 (Enterprise/Standard/web)를 사용 합니다.

Visual studio 또는 office 사용이 허가 된 제품을 설치 하려는 경우 지원 팀에 문의 하 여 사전 승인을 받아야 합니다.

자세한 내용은 [Azure Virtual Machine 기술 자산 만들기](create-azure-vm-technical-asset.md#create-a-vm-image-using-an-approved-base)를 참조 하 여 승인 된 기본을 선택 합니다.

## <a name="tool-kit-test-case-execution-failed"></a>도구 키트 테스트 사례 실행 실패

Microsoft 인증 도구 키트는 VHD/이미지가 Azure 환경과 호환 되는지 확인 하는 테스트 사례를 실행 하는 데 도움이 됩니다.

[Microsoft 인증 도구 키트](azure-vm-image-certification.md)를 다운로드 합니다.

## <a name="linux-test-cases"></a>Linux 테스트 사례

도구 키트에서 실행할 Linux 테스트 사례는 다음과 같습니다. 테스트 유효성 검사는 설명에 명시 되어 있습니다.

|S.No|테스트 사례|description|
|---|---|---|
|1|Bash 기록|Bash 기록 파일은 VM 이미지를 만들기 전에 지워야 합니다.|
|2|Linux 에이전트 버전|Azure Linux Agent 2.2.41 이상을 설치 해야 합니다.|
|3|필수 커널 매개 변수|다음 커널 매개 변수가 설정 되었는지 확인 합니다. <br>콘솔 = ttyS0<br>earlyprintk = ttyS0<br>rootdelay = 300|
|4|OS 디스크에서 파티션 바꾸기|스왑 파티션이 OS 디스크에 생성 되지 않았는지 확인 합니다.|
|5|OS 디스크의 루트 파티션|OS 디스크에 대해 단일 루트 파티션을 만듭니다.|
|6|OpenSSL 버전|OpenSSL 버전은 v 0.9.8 보다 크거나 같아야 합니다.|
|7|Python 버전|Python 버전 2.6 이상을 권장 합니다.|
|8|클라이언트 연결 간격|ClientAliveInterval를 180로 설정 합니다. 응용 프로그램 요구에 따라 30에서 235 사이로 설정할 수 있습니다. 최종 사용자에 대해 SSH를 사용 하도록 설정 하는 경우이 값은 설명 된 대로 설정 해야 합니다.|
|9|OS 아키텍처|64비트 운영 체제만 지원됩니다.|
|10|자동 업데이트|Linux 에이전트 자동 업데이트가 사용 되는지 여부를 식별 합니다.|

### <a name="common-errors-found-while-executing-the-previous-test-cases"></a>이전 테스트 사례를 실행 하는 동안 발견 된 일반적인 오류

이전 테스트 사례를 실행 하는 동안 발견 된 일반적인 오류입니다.
 
|S.NO|테스트 사례|오류|솔루션|
|---|---|---|---|
|1|Linux 에이전트 버전 테스트 사례|최소 Linux 에이전트 버전은 2.241 이상입니다. 이 요구 사항은 2020 년 5 월 1 일 이후 필수입니다.|[요청을 제출](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)하는 데 필요한 버전으로 이미지를 업데이트 해야 합니다.|
|2|Bash 기록 테스트 사례|제출 된 이미지의 bash 기록 크기가 1kb 이상인 경우 오류가 표시 됩니다. 이 크기는 잠재적으로 중요 한 정보가 bash 기록 파일에 캡처되지 않도록 1kb로 제한 됩니다.|이 문제를 해결 하려면 다른 작업 중인 VM에 VHD를 탑재 하 고 변경 내용을 적용 합니다 (예: `.bash` 기록 파일 삭제). 크기를 1kb 이하로 줄이려고 합니다.|
|3|필수 커널 매개 변수 테스트 사례|**콘솔** 의 값이 **ttyS0**로 설정 되어 있지 않으면이 오류가 표시 됩니다. 다음 명령을 실행 하 여 확인 합니다.<br>`cat /proc/cmdline`|**콘솔** 의 값을 **ttyS0** 로 설정 하 고 요청을 다시 제출 합니다.|
|4|ClientAlive 간격 테스트 사례|도구 키트 결과가이 테스트 사례에 대해 실패 한 결과를 제공 하는 경우 **ClientAliveInterval**에 적합 하지 않은 값이 있습니다.|**ClientAliveInterval** 의 값을 235 미만으로 설정 하 고 요청을 다시 제출 합니다.|

### <a name="windows-test-cases"></a>Windows 테스트 사례

다음은 도구 키트가 실행 하는 Windows 테스트 사례입니다. 테스트 유효성 검사는 설명에 명시 되어 있습니다.

|S.No|테스트 사례|description|
|---|---|---|---|
|1|OS 아키텍처|Azure는 64 비트 운영 체제만 지원 합니다.|
|2|사용자 계정 종속성|응용 프로그램 실행은 관리자 계정에 종속 되지 않아야 합니다.|
|3|장애 조치(failover) 클러스터|Windows Server 장애 조치 (Failover) 클러스터링 기능은 아직 지원 되지 않습니다. 응용 프로그램은이 기능에 종속 되지 않아야 합니다.|
|4|IPV6|I p v 6은 Azure 환경에서 아직 지원 되지 않습니다. 응용 프로그램은이 기능에 종속 되지 않아야 합니다.|
|5|DHCP|동적 호스트 구성 프로토콜 서버 역할은 아직 지원 되지 않습니다. 응용 프로그램은이 기능에 종속 되지 않아야 합니다.|
|6|Hyper-V|Hyper-v 서버 역할은 아직 지원 되지 않습니다. 응용 프로그램은이 기능에 종속 되지 않아야 합니다.|
|7|원격 액세스|원격 액세스 (직접 액세스) 서버 역할은 아직 지원 되지 않습니다. 응용 프로그램은이 기능에 종속 되지 않아야 합니다.|
|8|Rights Management Services|Rights Management 서비스. 서버 역할은 아직 지원 되지 않습니다. 응용 프로그램은이 기능에 종속 되지 않아야 합니다.|
|9|Windows Deployment Services|Windows 배포 서비스. 서버 역할은 아직 지원 되지 않습니다. 응용 프로그램은이 기능에 종속 되지 않아야 합니다.|
|10|BitLocker 드라이브 암호화|BitLocker 드라이브 암호화는 운영 체제 하드 디스크에서 지원 되지 않지만 데이터 디스크에서 사용할 수 있습니다.|
|11|인터넷 저장소 이름 서버|인터넷 저장소 이름 서버 기능은 아직 지원 되지 않습니다. 응용 프로그램은이 기능에 종속 되지 않아야 합니다.|
|12|다중 경로 I/O|다중 경로 i/o 이 서버 기능은 아직 지원 되지 않습니다. 응용 프로그램은이 기능에 종속 되지 않아야 합니다.|
|13|Network Load Balancing|네트워크 부하 분산. 이 서버 기능은 아직 지원 되지 않습니다. 응용 프로그램은이 기능에 종속 되지 않아야 합니다.|
|14|피어 이름 확인 프로토콜|피어 이름 확인 프로토콜입니다. 이 서버 기능은 아직 지원 되지 않습니다. 응용 프로그램은이 기능에 종속 되지 않아야 합니다.|
|15|SNMP 서비스|SNMP 서비스 기능은 아직 지원 되지 않습니다. 응용 프로그램은이 기능에 종속 되지 않아야 합니다.|
|16|Windows Internet Name Service|Windows 인터넷 이름 서비스입니다. 이 서버 기능은 아직 지원 되지 않습니다. 응용 프로그램은이 기능에 종속 되지 않아야 합니다.|
|17|무선 LAN 서비스|무선 LAN 서비스. 이 서버 기능은 아직 지원 되지 않습니다. 응용 프로그램은이 기능에 종속 되지 않아야 합니다.|

위의 테스트 사례와 관련 된 오류가 발생 하는 경우 솔루션에 대 한 이전 표에 **설명** 열을 참조 하세요. 자세한 정보가 필요한 경우 지원 팀에 문의 하세요. 

## <a name="data-disk-size-verification"></a>데이터 디스크 크기 확인

데이터 디스크를 사용 하 여 제출 된 요청의 크기가 1023 GB 보다 크면 요청이 승인 되지 않습니다. 이 규칙은 Linux & Windows에 모두 적용 됩니다.

1023 GB 보다 작거나 같은 크기의 요청을 다시 제출 합니다.

## <a name="os-disk-size-validation"></a>OS 디스크 크기 확인

OS 디스크 크기에 대 한 제한 사항은 다음 규칙을 참조 하세요. 요청을 제출할 때 OS 디스크 크기가 Linux 또는 Windows에 대 한 제한 내에 있는지 확인 합니다.

|OS|권장 VHD 크기|
|---|---|
|Linux|30GB ~ 1023 g b|
|Windows|30GB ~ 250 g b|

Vm에서 기본 운영 체제에 대 한 액세스를 허용 하므로 vhd 크기가 VHD에 대해 충분히 큰지 확인 합니다. 디스크가 가동 중지 시간 없이 확장 되지 않기 때문에 30에서 50 GB 사이의 디스크 크기를 사용 합니다.

|VHD 크기|실제 차지 크기|솔루션|
|---|---|---|
|>500 TiB|해당 없음|예외 승인에 대해서는 지원 팀에 문의 하세요.|
|250-500 TiB|>200 GiB blob 크기와 다름|예외 승인에 대해서는 지원 팀에 문의 하세요.|

> [!NOTE]
> 디스크 크기가 클수록 비용이 더 많이 듭니다. 프로 비전 및 복제 단계에서 지연이 발생 합니다. 이러한 지연 및 비용으로 인해 지원 팀은 예외 승인에 대 한 근거를 검색할 수 있습니다.

## <a name="wannacry-patch-verification-test-for-windows"></a>Windows 용 WannaCry patch 확인 테스트

WannaCry 바이러스와 관련 된 잠재적인 공격을 방지 하려면 모든 windows 이미지 요청이 최신 패치로 업데이트 되었는지 확인 합니다.

Windows Server 패치 버전을 확인 하려면 다음 표를 참조 하 여 OS 세부 정보 및 지원 되는 최소 버전을 참조 하세요. 

이미지 파일 버전은 또는에서 확인할 수 `C:\windows\system32\drivers\srv.sys` 있습니다 `srv2.sys` .

> [!NOTE]
> WindowsServer2019에는 필수 버전 요구 사항이 없습니다.

|OS|버전|
|---|---|
|WindowsServer2008R2|6.1.7601.23689|
|WindowsServer2012|6.2.9200.22099|
|WindowsServer2012R2|6.3.9600.18604|
|WindowsServer2016|10.0.14393.953|
|WindowsServer2019|해당 없음|

## <a name="sack-vulnerability-patch-verification"></a>SACK 취약성 패치 확인

Linux 이미지를 제출할 때 커널 버전 문제 때문에 요청이 거부 될 수 있습니다.

승인 된 버전으로 커널을 업데이트 하 고 요청을 다시 제출 합니다. 승인 된 커널 버전은 다음 표에 나와 있습니다. 버전 번호는 아래 나열 된 것 보다 크거나 같아야 합니다.

다음 커널 버전 중 하나를 사용 하 여 이미지를 설치 하지 않은 경우 올바른 패치로 이미지를 업데이트 합니다. 다음 링크에서 자세한 정보를 찾을 수 있습니다. 이러한 필수 패치를 사용 하 여 이미지를 업데이트 한 후 지원 팀의 필요한 승인을 요청 합니다.

- CVE-2019-11477 
- CVE-2019-11478 
- CVE-2019-11479

|OS 제품군|버전|커널(kernel)|
|---|---|---|
|Ubuntu|14.04 LTS|4.4.0| 
||14.04 LTS|4.15.0-1049-*-azure|
||16.04 LTS|4.15.0-1049|
||18.04 LTS|4.18.0-1023|
||18.04 LTS|5.0.0-1025|
||18.10|4.18.0-1023|
||19.04|5.0.0-1010|
||19.04|5.3.0-1004|
|RHEL 및 운영 체제|6.10|2.6.32 커널을-754.15.3|
||7.2|3.10.0-327.79.2|
||7.3|3.10.0-514.66.2|
||7.4|3.10.0-693.50.3|
||7.5|3.10.0-862.34.2|
||7.6|3.10.0-957.21.3|
||7.7|3.10.0-1062.1.1|
||8.0|4.18.0-80.4.2|
||8.1|4.18.0-147|
||"7-RAW" (7.6)||
||"7LVM" (7.6)|3.10.0-957.21.3|
||RHEL-SAP 7.4|TBD|
||RHEL-SAP 7.5|TBD|
|SLES|SLES11SP4 (SAP 포함)|3.0.101-108.95.2|
||SAP 용 SLES12SP1|3.12.74-60.64.115.1|
||SAP 용 SLES12SP2|4.4.121-92.114.1|
||SLES12SP3|4.4180-4.31.1 (커널-azure)|
||SAP 용 SLES12SP3|4.4.180-94.97.1|
||SLES12SP4|4.12.14-6.15.2 (커널-azure)|
||SAP 용 SLES12SP4|4.12.14-95.19.1|
||SLES15|4.12.14-5.30.1 (커널-azure)|
||SAP 용 SLES15|4.12.14-5.30.1 (커널-azure)|
||SLES15SP1|4.12.14-5.30.1 (커널-azure)|
|Oracle|6.10|UEK2 2.6.39-400.312.2<br>UNBREAKABLE 3.8.13-118.35.2<br>RHCK 2.6.32 커널을-754.15.3 
||7.0-7.5|UNBREAKABLE 3.8.13-118.35.2<br>UEK4 4.1.12-124.28.3<br>RHCK는 위의 RHEL을 따릅니다.|
||7.6|RHCK 3.10.0-957.21.3<br>UEK5 4.14.35-1902.2.0|
|CoreOS 안정 2079.6.0|4.19.43*|
||베타 2135.3.1|4.19.50*|
||알파 2163.2.1|4.19.50*|
|Debian|jessie (보안)|3.16.68-2|
||jessie backports|4.9.168 + deb9u3|
||스트레치 (보안)|4.9.168 + deb9u3|
||Debian GNU/Linux 10 (buster)|Debian 6.3.0-18 + deb9u1|
||buster, sid (스트레치 백 포트)|4.19.37-5|

## <a name="image-size-should-be-in-multiples-of-megabytes"></a>이미지 크기는 메가바이트의 배수 여야 합니다.

Azure의 모든 Vhd는 가상 크기를 1mb의 배수로 맞춰야 합니다. VHD가 권장 가상 크기를 준수 하지 않는 경우 요청이 거부 될 수 있습니다.

원시 디스크를 VHD로 변환 하 고 원시 디스크 크기가 1mb의 배수 인지 확인 해야 하는 경우 지침을 따릅니다. 자세한 내용은 [보증 되지 않는 배포에 대 한 정보](../../virtual-machines/linux/create-upload-generic.md) 를 참조 하세요.

## <a name="vm-access-denied"></a>VM 액세스 거부 됨

VM에서 테스트 사례를 실행 하는 동안 액세스 거부 문제를 해결 하는 경우 테스트 사례를 실행할 수 있는 권한이 부족 하기 때문일 수 있습니다.

자체 테스트 사례가 실행 되 고 있는 계정에 대 한 적절 한 액세스를 사용 하도록 설정 했는지 확인 합니다. 그렇지 않은 경우 액세스를 활성화 하 여 테스트 사례를 실행 합니다. 액세스를 사용 하지 않으려면 자체 테스트 사례 결과를 지원 팀과 공유할 수 있습니다.

## <a name="download-failure"></a>다운로드 실패
    
SAS URL을 사용 하 여 VM 이미지를 다운로드할 때 발생 하는 문제에 대해서는 다음 표를 참조 하세요.

|S.NO|오류|reason|솔루션|
|---|---|---|---|
|1|Blob을 찾을 수 없음|VHD는 지정 된 위치에서 삭제 되거나 이동 될 수 있습니다.|| 
|2|사용 중인 Blob|다른 내부 프로세스에서 VHD를 사용 하 고 있습니다.|SAS URL을 사용 하 여 다운로드 하는 경우 VHD는 사용 된 상태 여야 합니다.|
|3|잘못 된 SAS URL|VHD에 대 한 연결 된 SAS URL이 잘못 되었습니다.|올바른 SAS URL을 가져옵니다.|
|4|Invalid Signature|VHD에 대 한 연결 된 SAS URL이 잘못 되었습니다.|올바른 SAS URL을 가져옵니다.|
|6|HTTP 조건부 헤더|SAS URL이 잘못 되었습니다.|올바른 SAS URL을 가져옵니다.|
|7|잘못 된 VHD 이름|또는 ""와 같은 특수 문자가 **%** VHD **“ “** 이름에 있는지 확인 합니다.|특수 문자를 제거 하 여 VHD 파일 이름 바꾸기|

## <a name="first-1-mb-partition"></a>첫 1mb 파티션

VHD를 제출할 때 VHD의 첫 1mb 파티션이 비어 있는지 확인 합니다. 그렇지 않으면 요청이 실패 합니다.

## <a name="default-credentials"></a>기본 자격 증명

항상 기본 자격 증명이 전송 된 VHD와 함께 전송 되지 않도록 합니다. 기본 자격 증명을 추가 하면 VHD가 보안 위협에 더 취약해 집니다. 대신 VHD를 제출할 때 사용자 고유의 자격 증명을 만듭니다.
  
## <a name="datadisk-mapped-incorrectly"></a>DataDisk가 잘못 매핑 되었습니다.

여러 데이터 디스크를 사용 하 여 요청을 제출 했지만 순서가 순서 대로 되어 있지 않은 경우 매핑 문제로 간주 됩니다. 예를 들어 데이터 디스크가 3 개인 경우 번호 매기기 순서는 **0, 1, 2**여야 합니다. 다른 모든 주문은 매핑 문제로 처리 됩니다.

적절 한 데이터 디스크 시퀀싱으로 요청을 다시 제출 합니다.

## <a name="incorrect-os-mapping"></a>잘못 된 OS 매핑

생성 된 이미지는 잘못 된 OS 레이블에 매핑되거나 할당 될 수 있습니다. 예를 들어 이미지를 만드는 동안 **windows** 가 os 이름의 일부로 선택 된 경우 os 디스크는 windows와 함께 설치 해야 합니다. Linux에도 동일 하 게 적용 됩니다.

## <a name="vm-not-generalized"></a>일반화 되지 않은 VM

Azure Marketplace에서 가져온 모든 이미지를 다시 사용 하는 경우 운영 체제 VHD를 일반화 해야 합니다.

Linux:

다음 프로세스에서 Linux VM을 일반화하여 별도의 VM으로 다시 배포합니다.

SSH 창에서 다음 명령을 입력 합니다.`sudo waagent -deprovision+user`

Windows:

Windows 이미지는로 일반화 됩니다 `sysreptool` .

이 도구에 대 한 자세한 내용은 [Sysprep (시스템 준비) 개요]( https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) 를 확인 하세요.

## <a name="datadisk-error"></a>DataDisk 오류

데이터 디스크와 관련 된 오류에 대 한 해결 방법은 다음 표를 참조 하십시오.

|Error|reason|솔루션|
|---|---|---|
|`DataDisk- InvalidUrl:`|이 오류는 제품을 제출 하는 동안 LUN에 지정 된 숫자가 잘못 되었기 때문일 수 있습니다.|데이터 디스크에 대 한 LUN 번호 시퀀스가 파트너 센터에 있는지 확인 합니다.|
|`DataDisk- NotFound:`|이 오류는 데이터 디스크가 지정 된 SAS URL에 있지 않기 때문에 발생할 수 있습니다.|데이터 디스크가 요청에 지정 된 SAS URL에 있는지 확인 합니다.|

## <a name="remote-access-issue"></a>원격 액세스 문제

Windows 이미지에 RDP 옵션을 사용 하도록 설정 하지 않은 경우이 오류가 표시 됩니다. 

제출 하기 전에 Windows 이미지에 대 한 RDP 액세스를 사용 하도록 설정 합니다.

## <a name="next-steps"></a>다음 단계

개선 사항에 대 한 질문이 나 피드백이 있는 경우 [파트너 센터 지원](https://partner.microsoft.com/support/v2/?stage=1)에 문의 하세요.
