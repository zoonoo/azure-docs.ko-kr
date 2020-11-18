---
title: Azure Marketplace에 대 한 VM (가상 컴퓨터) 인증 문제 해결
description: Azure Marketplace에 대 한 VM (가상 머신) 이미지를 테스트 하 고 인증 하는 것과 관련 된 일반적인 문제를 해결 합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/19/2020
ms.openlocfilehash: 5124e7084a8e878e54e594424ec0926410f002ca
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682488"
---
# <a name="troubleshoot-virtual-machine-certification"></a>가상 컴퓨터 인증 문제 해결

VM (가상 컴퓨터) 이미지를 Azure Marketplace에 게시 하는 경우 Azure 팀은 부팅 가능 하 고, 안전 하며, Azure와 호환 되는지 확인 하기 위해 유효성을 검사 합니다. VM 이미지가 고품질 테스트 중 하나라도 실패 하면 게시 되지 않습니다. 문제를 설명 하는 오류 메시지가 표시 됩니다.

이 문서에서는 관련 솔루션과 함께 VM 이미지를 게시 하는 동안 발생 하는 일반적인 오류 메시지에 대해 설명 합니다.

> [!NOTE]
> 이 문서 또는 향상 된 제안 사항에 대 한 질문이 있는 경우 [파트너 센터 지원](https://aka.ms/marketplacepublishersupport)에 문의 하세요.

## <a name="approved-base-image"></a>승인 된 기본 이미지

업데이트를 사용 하 여 이미지를 다시 게시 하는 요청을 제출 하면 파트 번호 확인 테스트 사례가 실패할 수 있습니다. 실패 하면 이미지가 승인 되지 않습니다.

이 오류는 다른 게시자에 속하는 기본 이미지를 사용 하 여 이미지를 업데이트 한 경우에 발생 합니다. 이 경우 이미지를 게시할 수 없습니다.

이 문제를 해결 하려면 Azure Marketplace에서 이미지를 검색 하 고 변경 합니다. 자세한 내용은 다음 아티클을 참조하세요.

- [Linux 이미지](../virtual-machines/linux/endorsed-distros.md?toc=/azure/virtual-machines/linux/toc.json)
- [Windows 이미지](azure-vm-create-using-approved-base.md)

> [!Note]
> Azure Marketplace에서 가져오지 않은 Linux 기본 이미지를 사용 하는 경우 첫 번째 파티션을 2048 KB로 오프셋할 수 있습니다. 이렇게 하면 포맷 되지 않은 공간을 새 청구 정보를 추가 하는 데 사용할 수 있으며 Azure에서 Azure Marketplace에 VM을 게시 하는 과정을 진행할 수 있습니다.  

## <a name="vm-extension-failure"></a>VM 확장 오류

이미지에서 VM 확장을 지원 하는지 확인 합니다.

VM 확장을 사용 하도록 설정 하려면

1. Linux VM을 선택 합니다.
1. **진단 설정** 으로 이동 합니다.
1. **저장소 계정을** 업데이트 하 여 기본 행렬을 사용 하도록 설정 합니다.
1. **저장** 을 선택합니다.

   ![게스트 수준 모니터링을 사용 하도록 설정 하는 방법을 보여 주는 스크린샷](./media/create-vm/vm-certification-issues-solutions-1.png)

VM 확장이 제대로 활성화 되었는지 확인 하려면:

1. VM에서 **vm 확장** 탭을 선택한 후 **Linux 진단 확장** 의 상태를 확인 합니다.
1. 프로 비전 상태를 확인 합니다.

   - 상태가 *프로 비전 성공* 이면 확장 테스트 사례가 통과 한 것입니다.  
   - 상태가 *프로 비전 실패* 인 경우 확장 테스트 사례가 실패 하 고 확정 된 플래그를 설정 해야 합니다.

   ![프로 비전이 성공 했음을 보여 주는 스크린샷](./media/create-vm/vm-certification-issues-solutions-2.png)

   VM 확장이 실패 하는 경우 사용 하도록 설정 하려면 [Linux 진단 확장을 사용 하 여 메트릭 및 로그 모니터링을](../virtual-machines/extensions/diagnostics-linux.md) 참조 하세요. VM 확장을 사용 하지 않으려면 지원 팀에 문의 하 여 사용 하지 않도록 설정 하세요.

## <a name="vm-provisioning-issue"></a>VM 프로 비전 문제

제품을 제출 하기 전에 VM 프로 비전 프로세스를 엄격 하 게 준수 했는지 확인 합니다. VM을 프로 비전 하기 위한 JSON 형식을 보려면 [가상 컴퓨터 이미지 테스트](azure-vm-image-test.md)를 참조 하세요.

프로 비전 문제는 다음과 같은 오류 시나리오를 포함할 수 있습니다.

|시나리오|오류|이유|솔루션|
|---|---|---|---|
|1|잘못 된 VHD (가상 하드 디스크)|VHD 바닥글의 지정 된 쿠키 값이 잘못 된 경우 VHD가 잘못 된 것으로 간주 됩니다.|이미지를 다시 만들고 요청을 제출 합니다.|
|2|잘못 된 blob 유형|사용 된 블록이 페이지 유형이 아닌 blob 유형 이므로 VM을 프로 비전 하지 못했습니다.|이미지를 다시 만들고 요청을 제출 합니다.|
|3|프로 비전 시간 초과 또는 제대로 일반화 되지 않음|VM 일반화에 문제가 있습니다.|일반화를 사용 하 여 이미지를 다시 만들고 요청을 제출 합니다.|

> [!NOTE]
> VM 일반화에 대 한 자세한 내용은 다음을 참조 하세요.
> - [Linux 설명서](azure-vm-create-using-approved-base.md#generalize-the-image)
> - [Windows 설명서](../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)


## <a name="vhd-specifications"></a>VHD 사양

### <a name="conectix-cookie-and-other-vhd-specifications"></a>Conectix 쿠키 및 기타 VHD 사양

' Conectix ' 문자열은 VHD 사양의 일부입니다. 이 파일은 VHD 바닥글에서 파일 작성자를 식별 하는 8 바이트 쿠키로 정의 됩니다. Microsoft에서 만든 모든 VHD 파일에는이 쿠키가 있습니다. 

VHD 형식 blob의 경우 다음 형식으로 512 바이트 바닥글이 있어야 합니다.

|하드 디스크 바닥글 필드|크기(바이트)|
|---|---|
쿠키|8
기능|4
파일 형식 버전|4
데이터 오프셋|8
타임스탬프|4
Creator 응용 프로그램|4
작성자 버전|4
작성자 호스트 OS|4
원래 크기|8
현재 크기|8
디스크 기 하 도형|4
디스크 유형|4
체크섬|4
고유 ID|16
저장 된 상태|1
예약됨|427


### <a name="vhd-specifications"></a>VHD 사양

원활한 게시 환경을 보장 하려면 VHD가 다음 조건을 충족 하는지 확인 합니다.

- 쿠키에는 ' conectix ' 문자열이 포함 되어 있습니다.
- 디스크 유형이 고정 되어 있습니다.
- VHD의 가상 크기는 20mb 이상입니다.
- VHD가 정렬 됩니다. 가상 크기는 1mb의 배수 여야 합니다.
- VHD blob 길이는 가상 크기와 VHD 바닥글 길이 (512)를 더한 값과 같습니다.

[VHD 사양을](https://www.microsoft.com/download/details.aspx?id=23850)다운로드 합니다.

## <a name="software-compliance-for-windows"></a>Windows 용 소프트웨어 준수

소프트웨어 규정 준수 문제로 인해 Windows 이미지 요청이 거부 된 경우 설치 된 SQL Server 인스턴스를 사용 하 여 Windows 이미지를 만들었을 수 있습니다. 대신 Azure Marketplace에서 관련 SQL Server 버전 기본 이미지를 사용 해야 합니다.

SQL Server 설치 된 사용자 고유의 Windows 이미지를 만들지 마세요. Azure Marketplace에서 승인 된 SQL Server 기본 이미지 (Enterprise/Standard/web)를 사용 합니다.

Visual Studio 또는 Office 사용이 허가 된 제품을 설치 하려는 경우 지원 팀에 문의 하 여 사전 승인을 받아야 합니다.

승인 된 기본을 선택 하는 방법에 대 한 자세한 내용은 [승인 된 기본에서 가상 컴퓨터 만들기](azure-vm-create-using-approved-base.md)를 참조 하세요.

## <a name="toolkit-test-case-execution-failed"></a>Toolkit 테스트 사례 실행 실패

Microsoft 인증 도구 키트는 테스트 사례를 실행 하 고 VHD 또는 이미지가 Azure 환경과 호환 되는지 확인 하는 데 도움이 될 수 있습니다.

[Microsoft 인증 도구 키트](azure-vm-image-test.md)를 다운로드 합니다.

### <a name="linux-test-cases"></a>Linux 테스트 사례

다음 표에서는 도구 키트가 실행 될 Linux 테스트 사례를 보여 줍니다. 테스트 유효성 검사는 설명에 명시 되어 있습니다.

|시나리오|테스트 사례|설명|
|---|---|---|
|1|Bash 기록|Bash 기록 파일은 VM 이미지를 만들기 전에 지워야 합니다.|
|2|Linux 에이전트 버전|Azure Linux Agent 2.2.41 이상을 설치 해야 합니다.|
|3|필수 커널 매개 변수|다음 커널 매개 변수가 설정 되었는지 확인 합니다. <br>콘솔 = ttyS0<br>earlyprintk = ttyS0<br>rootdelay = 300 |
|4|OS 디스크에서 파티션 바꾸기|스왑 파티션이 OS 디스크에 생성 되지 않았는지 확인 합니다.|
|5|OS 디스크의 루트 파티션|OS 디스크에 대해 단일 루트 파티션을 만듭니다.|
|6|OpenSSL 버전|OpenSSL 버전은 v 0.9.8 이상 이어야 합니다.|
|7|Python 버전|Python 버전 2.6 이상을 권장 합니다.|
|8|클라이언트 연결 간격|ClientAliveInterval를 180로 설정 합니다. 응용 프로그램 요구에 따라 30에서 235로 설정할 수 있습니다. 최종 사용자에 대해 SSH를 사용 하도록 설정 하는 경우이 값은 설명 된 대로 설정 해야 합니다.|
|9|OS 아키텍처|64비트 운영 체제만 지원됩니다.|
|10|자동 업데이트|Linux 에이전트 자동 업데이트가 사용 되는지 여부를 식별 합니다.|

### <a name="common-test-case-errors"></a>일반적인 테스트 사례 오류

테스트 사례를 실행할 때 나타날 수 있는 일반적인 오류는 다음 표를 참조 하세요.

| 시나리오 | 테스트 사례 | Error | 해결 방법 |
| --- | --- | --- | --- |
| 1 | Linux 에이전트 버전 테스트 사례 | 최소 Linux 에이전트 버전은 2.2.41 이상입니다. 이 요구 사항은 2020 년 5 월 1 일 이후 필수입니다. | Linux 에이전트 버전을 업데이트 합니다. 2.241 이상 이어야 합니다. 자세한 내용은 [Linux 에이전트 버전 업데이트 페이지](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)를 참조 하세요. |
| 2 | Bash 기록 테스트 사례 | 제출 된 이미지의 Bash 기록 크기가 1kb를 초과 하는 경우 오류가 발생 합니다. 이 크기는 Bash 기록 파일에 잠재적으로 중요 한 정보가 포함 되지 않도록 1kb로 제한 됩니다. | VHD를 다른 작업 중인 VM에 탑재 하 여 해결 하 고 크기를 1kb 이하로 줄이기 위해 변경 합니다. 예를 들어 `.bash` 기록 파일을 삭제 합니다. |
| 3 | 필수 커널 매개 변수 테스트 사례 | 의 값이 `console` 로 설정 되지 않은 경우이 오류가 표시 됩니다 `ttyS0` . 다음 명령을 실행 하 여 확인 합니다. <br /> `cat /proc/cmdline` | 의 값을로 설정 하 `console` `ttyS0` 고 요청을 다시 제출 합니다. |
| 4 | ClientAlive 간격 테스트 사례 | 도구 키트가이 테스트 사례에 대해 실패 한 결과를 제공 하는 경우에는 적합 하지 않은 값이 있습니다 `ClientAliveInterval` . | 의 값을 `ClientAliveInterval` 235 보다 작거나 같게 설정한 다음 요청을 다시 제출 합니다. |


### <a name="windows-test-cases"></a>Windows 테스트 사례

다음 표에서는 도구 키트가 실행 되는 Windows 테스트 사례와 테스트 유효성 검사에 대 한 설명을 보여 줍니다.

|시나리오 |테스트 사례|설명|
|---|---|---|---|
|1|OS 아키텍처|Azure는 64 비트 운영 체제만 지원 합니다.|
|2|사용자 계정 종속성|응용 프로그램 실행은 관리자 계정에 종속 되지 않아야 합니다.|
|3|장애 조치 클러스터|Windows Server 장애 조치 (failover) 클러스터링 기능은 아직 지원 되지 않습니다. 응용 프로그램은이 기능에 종속 되지 않아야 합니다.|
|4|IPV6|I p v 6은 Azure 환경에서 아직 지원 되지 않습니다. 응용 프로그램은이 기능에 종속 되지 않아야 합니다.|
|5|DHCP|동적 호스트 구성 프로토콜 서버 역할은 아직 지원 되지 않습니다. 응용 프로그램은이 기능에 종속 되지 않아야 합니다.|
|6|Hyper-V|Hyper-v 서버 역할은 아직 지원 되지 않습니다. 응용 프로그램은이 기능에 종속 되지 않아야 합니다.|
|7|원격 액세스|원격 액세스 (직접 액세스) 서버 역할은 아직 지원 되지 않습니다. 응용 프로그램은이 기능에 종속 되지 않아야 합니다.|
|8|Rights Management Services|Rights Management 서비스. 서버 역할은 아직 지원 되지 않습니다. 응용 프로그램은이 기능에 종속 되지 않아야 합니다.|
|9|Windows Deployment Services|Windows 배포 서비스. 서버 역할은 아직 지원 되지 않습니다. 응용 프로그램은이 기능에 종속 되지 않아야 합니다.|
|10|BitLocker 드라이브 암호화|BitLocker 드라이브 암호화는 운영 체제 하드 디스크에서 지원 되지 않지만 데이터 디스크에 사용 될 수 있습니다.|
|11|인터넷 저장소 이름 서버|인터넷 저장소 이름 서버 기능은 아직 지원 되지 않습니다. 응용 프로그램은이 기능에 종속 되지 않아야 합니다.|
|12|다중 경로 I/O|다중 경로 i/o 이 서버 기능은 아직 지원 되지 않습니다. 응용 프로그램은이 기능에 종속 되지 않아야 합니다.|
|13|Network Load Balancing|네트워크 부하 분산. 이 서버 기능은 아직 지원 되지 않습니다. 응용 프로그램은이 기능에 종속 되지 않아야 합니다.|
|14|피어 이름 확인 프로토콜|피어 이름 확인 프로토콜입니다. 이 서버 기능은 아직 지원 되지 않습니다. 응용 프로그램은이 기능에 종속 되지 않아야 합니다.|
|15|SNMP 서비스|SNMP (Simple Network Management Protocol) 서비스 기능은 아직 지원 되지 않습니다. 응용 프로그램은이 기능에 종속 되지 않아야 합니다.|
|16|Windows Internet Name Service|Windows 인터넷 이름 서비스입니다. 이 서버 기능은 아직 지원 되지 않습니다. 응용 프로그램은이 기능에 종속 되지 않아야 합니다.|
|17|무선 LAN 서비스|무선 LAN 서비스. 이 서버 기능은 아직 지원 되지 않습니다. 응용 프로그램은이 기능에 종속 되지 않아야 합니다.|

이전 테스트 사례와 관련 된 모든 오류를 발생 하는 경우 솔루션에 대 한 표의 **설명** 열을 참조 하세요. 자세한 내용은 지원 팀에 문의 하세요. 

## <a name="data-disk-size-verification"></a>데이터 디스크 크기 확인

크기가 1023 gb 보다 큰 데이터 디스크 요청은 승인 되지 않습니다. 이 규칙은 Linux 및 Windows 모두에 적용 됩니다.

1023 GB 보다 작거나 같은 크기의 요청을 다시 제출 합니다.

## <a name="os-disk-size-validation"></a>OS 디스크 크기 확인

OS 디스크 크기에 대 한 제한 사항은 다음 규칙을 참조 하세요. 요청을 제출할 때 OS 디스크 크기가 Linux 또는 Windows에 대 한 제한 내에 있는지 확인 합니다.

|OS|권장 VHD 크기|
|---|---|
|Linux|30GB ~ 1023 g b|
|Windows|30GB ~ 250 g b|

Vm은 기본 운영 체제에 대 한 액세스를 허용 하므로 vhd 크기가 VHD에 대해 충분히 큰지 확인 합니다. 가동 중지 시간 없이 디스크를 확장할 필요가 없습니다. 30GB에서 50 GB 까지의 디스크 크기를 사용 합니다.

|VHD 크기|실제 차지 크기|솔루션|
|---|---|---|
|>500 tebibytes (TiB)|해당 없음|예외 승인에 대해서는 지원 팀에 문의 하세요.|
|250-500 TiB|Blob 크기와 >200 GiB) 차이|예외 승인에 대해서는 지원 팀에 문의 하세요.|

> [!NOTE]
> 디스크 크기가 클수록 비용이 더 많이 들 며 설정 및 복제 프로세스 중에 지연이 발생 합니다. 이러한 지연 및 비용으로 인해 지원 팀은 예외 승인에 대 한 근거를 검색할 수 있습니다.

## <a name="wannacry-patch-verification-test-for-windows"></a>Windows 용 WannaCry patch 확인 테스트

WannaCry 바이러스와 관련 된 잠재적인 공격을 방지 하려면 모든 Windows 이미지 요청이 최신 패치로 업데이트 되었는지 확인 합니다.

또는에서 이미지 파일 버전을 확인할 수 `C:\windows\system32\drivers\srv.sys` 있습니다 `srv2.sys` .

다음 표에서는 Windows Server의 최소 패치 버전을 보여 줍니다. 

|OS|버전|
|---|---|
|Windows 서비스 2008 R2|6.1.7601.23689|
|Windows Server 2012|6.2.9200.22099|
|Windows Server 2012 R2|6.3.9600.18604|
|Windows Server 2016|10.0.14393.953|
|Windows Server 2019|해당 없음|

> [!NOTE]
> Windows Server 2019에는 필수 버전 요구 사항이 없습니다.

## <a name="sack-vulnerability-patch-verification"></a>SACK 취약성 패치 확인

Linux 이미지를 제출할 때 커널 버전 문제로 인해 요청이 거부 될 수 있습니다.

승인 된 버전으로 커널을 업데이트 하 고 요청을 다시 제출 합니다. 승인 된 커널 버전은 다음 표에 나와 있습니다. 버전 번호는 여기에 나열 된 숫자 보다 크거나 같아야 합니다.

다음 커널 버전 중 하나를 사용 하 여 이미지를 설치 하지 않은 경우 올바른 패치로 업데이트 합니다. 이러한 필수 패치를 사용 하 여 이미지를 업데이트 한 후 지원 팀의 필요한 승인을 요청 합니다.

- CVE-2019-11477 
- CVE-2019-11478 
- CVE-2019-11479

|OS 제품군|버전|커널|
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

Azure의 모든 Vhd에는 1 메가바이트 (MB)의 배수로 크기를 맞춘 가상 크기가 있어야 합니다. VHD가 권장 가상 크기를 준수 하지 않는 경우 요청이 거부 될 수 있습니다.

원시 디스크에서 VHD로 변환 하는 경우 지침을 따릅니다. 원시 디스크 크기가 1mb의 배수 인지 확인 합니다. 자세한 내용은 [보증 되지 않는 배포에 대 한 정보](../virtual-machines/linux/create-upload-generic.md)를 참조 하세요.

## <a name="vm-access-denied"></a>VM 액세스 거부 됨

권한 부족으로 인해 VM에서 테스트 사례를 실행 하는 데 대 한 _액세스 거부_ 문제가 발생할 수 있습니다.

자체 테스트 사례가 실행 되 고 있는 계정에 대 한 적절 한 액세스를 사용 하도록 설정 했는지 확인 합니다. 사용 하도록 설정 되지 않은 경우 테스트 사례를 실행 하려면 액세스를 사용 하도록 설정 합니다. 액세스를 사용 하지 않으려면 자체 테스트 사례 결과를 지원 팀과 공유할 수 있습니다.

인증 프로세스를 위해 SSH 사용 안 함 이미지를 사용 하 여 요청을 제출 하려면:

1. 이미지에서 [Azure vm에 대 한 최신 인증 테스트 도구](https://aka.ms/AzureCertificationTestTool) 를 실행 합니다.

2. [지원 티켓](https://aka.ms/marketplacepublishersupport)을 발생 시킵니다. Toolkit 보고서를 첨부 하 고 제품 세부 정보를 제공 해야 합니다.
   - Offer name
   - Publisher name
   - 계획 ID/SKU 및 버전

3. 인증 요청을 다시 제출 합니다.

## <a name="download-failure"></a>다운로드 실패
    
SAS (공유 액세스 서명) URL을 사용 하 여 VM 이미지를 다운로드 하는 경우 발생 하는 문제에 대해서는 다음 표를 참조 하세요.

|시나리오|오류|이유|솔루션|
|---|---|---|---|
|1|Blob을 찾을 수 없음|VHD는 지정 된 위치에서 삭제 되거나 이동 될 수 있습니다.|| 
|2|사용 중인 Blob|다른 내부 프로세스에서 VHD를 사용 합니다.|SAS URL을 사용 하 여 VHD를 다운로드 하는 경우 VHD가 사용 된 상태 여야 합니다.|
|3|잘못 된 SAS URL|VHD에 연결 된 SAS URL이 잘못 되었습니다.|올바른 SAS URL을 가져옵니다.|
|4|잘못 된 서명|VHD에 연결 된 SAS URL이 잘못 되었습니다.|올바른 SAS URL을 가져옵니다.|
|6|HTTP 조건부 헤더|SAS URL이 잘못 되었습니다.|올바른 SAS URL을 가져옵니다.|
|7|잘못 된 VHD 이름|% 기호 또는 따옴표와 같은 특수 문자가 `%` `"` VHD 이름에 있는지 확인 하십시오.|특수 문자를 제거 하 여 VHD 파일의 이름을 바꿉니다.|

## <a name="first-mb-2048-kb-partition-linux-only"></a>첫 번째 MB (2048 KB) 파티션 (Linux에만 해당)

VHD를 제출할 때 VHD의 첫 2048 KB가 비어 있는지 확인 합니다. 그렇지 않으면 요청이 실패 합니다.

>[!NOTE]
>Azure Marketplace에서 가져온 Azure Windows 기본 이미지를 기반으로 하는 특정 이미지의 경우 청구 태그를 확인 하 고 청구 태그가 있고 내부 사용 가능한 값과 일치 하는 경우 MB 파티션을 무시 합니다.

### <a name="create-a-first-mb-2048-kb-partition-on-an-empty-vhd"></a>빈 VHD에 첫 번째 MB (2048 KB) 파티션 만들기

이러한 단계는 Linux에만 적용 됩니다.

1. Ubuntu, 센트 OS 등의 모든 종류의 Linux VM을 만듭니다. 필수 필드를 입력 하 고 **다음: 디스크 >** 를 선택 합니다.

   ![' 다음: 디스크 명령 ' 단추가 강조 표시 된 가상 머신 만들기 페이지를 보여 주는 스크린샷](./media/create-vm/vm-certification-issues-solutions-15.png)

1. VM에 대 한 관리 되지 않는 디스크를 만듭니다.

   기본값을 사용 하거나 NIC, NSG 및 공용 IP와 같은 필드에 대 한 값을 지정 합니다.

   ![가상 컴퓨터 흐름 만들기에서 ' 데이터 디스크 ' 페이지의 스크린샷 이미지입니다.](./media/create-vm/vm-certification-issues-solutions-16.png)

1. VM을 만든 후 왼쪽 창에서 **디스크** 를 선택 합니다.

   ![VM에 대 한 디스크를 선택 하는 방법을 보여 주는 스크린샷](./media/create-vm/vm-certification-issues-solutions-17.png)

1. 파티션 테이블을 만들기 위해 VHD를 VM에 데이터 디스크로 연결 합니다.

   1. **Datadisk**  >  **기존 Blob** 추가를 선택 합니다.

      ![VHD에 데이터 디스크를 추가 하는 방법을 보여 주는 스크린샷](./media/create-vm/vm-certification-issues-solutions-18.png)

   1. VHD 저장소 계정을 찾습니다.
   1. **컨테이너** 를 선택 하 고 VHD를 선택 합니다.
   1. **확인** 을 선택합니다.

      ![관리 되지 않는 디스크 연결 페이지의 스크린샷](./media/create-vm/vm-certification-issues-solutions-19.png)

      VHD가 데이터 디스크 LUN 0으로 추가 됩니다.

   1. VM을 다시 시작합니다.

1. VM을 다시 시작한 후 Putty 또는 다른 클라이언트를 사용 하 여 VM에 로그인 하 고 `sudo  -i` 명령을 실행 하 여 루트 액세스 권한을 얻습니다.

   ![Sudo-i 명령을 보여 주는 Putty client 명령줄 스크린샷](./media/create-vm/vm-certification-issues-solutions-20.png)

1. VHD에서 파티션을 만듭니다.

   1. Enter `fdisk /dev/sdb` 명령을 입력 합니다.
   1. VHD에서 기존 파티션 목록을 보려면을 입력 `p` 합니다.
   1. `d`을 입력 하 여 VHD에서 사용 가능한 기존 파티션을 모두 삭제 합니다. 필요 하지 않은 경우이 단계를 건너뛸 수 있습니다.

      ![기존 파티션을 삭제 하는 명령을 보여 주는 Putty client 명령줄 스크린샷](./media/create-vm/vm-certification-issues-solutions-21.png)

   1. `n`을 입력 하 여 새 파티션을 만들고 `p` for (주 파티션)을 선택 합니다.

   1. _첫 번째 섹터_ 값으로 2048를 입력 합니다. _마지막 섹터_ 를 기본값으로 그대로 둘 수 있습니다.

      >[!IMPORTANT]
      >모든 기존 데이터는 2048 KB까지 지워집니다. 새 파티션을 만들기 전에 VHD를 백업 합니다.

      ![지운 데이터의 명령 및 출력을 보여 주는 Putty client 명령줄 스크린샷](./media/create-vm/vm-certification-issues-solutions-22.png)

   1. `w`을 입력 하 여 파티션 생성을 확인 합니다. 

      ![파티션을 만들기 위한 명령을 보여 주는 Putty client 명령줄 스크린샷](./media/create-vm/vm-certification-issues-solutions-23.png)

   1. 명령을 실행 하 고를 입력 하 여 파티션 테이블을 확인할 수 있습니다 `n fdisk /dev/sdb` `p` . 2048 오프셋 값을 사용 하 여 파티션이 생성 되는 것을 볼 수 있습니다. 

      ![2048 오프셋을 만들기 위한 명령을 보여 주는 Putty client 명령줄 스크린샷](./media/create-vm/vm-certification-issues-solutions-24.png)

1. VM에서 VHD를 분리 하 고 VM을 삭제 합니다.

### <a name="create-a-first-mb-2048-kb-partition-by-moving-existing-data-on-vhd"></a>VHD에서 기존 데이터를 이동 하 여 첫 번째 MB (2048 KB) 파티션 만들기

이러한 단계는 Linux에만 적용 됩니다.

1. Ubuntu, 센트 OS 등의 모든 종류의 Linux VM을 만듭니다. 필수 필드를 입력 하 고 **다음: 디스크 >** 를 선택 합니다.

   ![' 다음: 디스크 명령 ' 단추가 강조 표시 된 가상 머신 만들기 페이지를 보여 주는 스크린샷](./media/create-vm/vm-certification-issues-solutions-15.png)

1. VM에 대 한 관리 되지 않는 디스크를 만듭니다.

   ![가상 컴퓨터 흐름 만들기에서 ' 데이터 디스크 ' 페이지의 스크린샷 이미지입니다.](./media/create-vm/vm-certification-issues-solutions-16.png)

   기본값을 사용 하거나 NIC, NSG 및 공용 IP와 같은 필드에 대 한 값을 지정 합니다.

1. VM을 만든 후 왼쪽 창에서 **디스크** 를 선택 합니다.

   ![VM에 대 한 디스크를 선택 하는 방법을 보여 주는 스크린샷](./media/create-vm/vm-certification-issues-solutions-17.png)

1. 파티션 테이블을 만들기 위해 VHD를 VM에 데이터 디스크로 연결 합니다.

   1. 파티션 테이블을 만들기 위해 VHD를 VM에 데이터 디스크로 연결 합니다.

   1. **Datadisk**  >  **기존 Blob** 추가를 선택 합니다.

      ![VHD에 데이터 디스크를 추가 하는 방법을 보여 주는 스크린샷](./media/create-vm/vm-certification-issues-solutions-18.png)

   1. VHD 저장소 계정을 찾습니다.
   1. **컨테이너** 를 선택 하 고 VHD를 선택 합니다.
   1. **확인** 을 선택합니다.

      ![관리 되지 않는 디스크 연결 페이지의 스크린샷](./media/create-vm/vm-certification-issues-solutions-19.png)

      VHD가 데이터 디스크 LUN 0으로 추가 됩니다.

   1. VM을 다시 시작합니다.

1. Putty 또는 다른 클라이언트를 사용 하 여 VM에 로그인 하 고 `sudo  -i` 명령을 실행 하 여 루트 액세스 권한을 얻습니다.

   ![로그인 및 sudo-i 명령을 보여 주는 Putty client 명령줄 스크린샷](./media/create-vm/vm-certification-issues-solutions-20.png)

1. `echo '+1M,' | sfdisk --move-data /dev/sdc -N 1` 명령을 실행합니다.

   ![명령의 실행을 보여 주는 Putty client 명령줄 스크린샷](./media/create-vm/vm-certification-issues-solutions-25.png)

   >[!NOTE]
   >이 명령은 디스크의 크기에 따라 달라 지기 때문에 완료 하는 데 다소 시간이 걸릴 수 있습니다.

1. VM에서 VHD를 분리 하 고 VM을 삭제 합니다.


## <a name="default-credentials"></a>기본 자격 증명

전송 된 VHD로 기본 자격 증명을 보내지 않습니다. 기본 자격 증명을 추가 하면 VHD가 보안 위협에 더 취약해 집니다. 대신 VHD를 제출할 때 사용자 고유의 자격 증명을 만듭니다.
  
## <a name="datadisk-mapped-incorrectly"></a>DataDisk가 잘못 매핑 되었습니다.

순서에 없는 여러 데이터 디스크를 사용 하 여 요청을 제출 하면 매핑 문제가 발생할 수 있습니다. 예를 들어 세 개의 데이터 디스크에 대 한 번호 매기기 순서는 *0, 1, 2* 여야 합니다. 다른 모든 순서는 매핑 문제로 처리 됩니다.

적절 한 데이터 디스크 시퀀싱으로 요청을 다시 제출 합니다.

## <a name="incorrect-os-mapping"></a>잘못 된 OS 매핑

이미지가 생성 되 면 잘못 된 OS 레이블에 매핑될 수도 있고 그렇지 않을 수도 있습니다. 예를 들어 이미지를 만드는 동안 OS 이름의 일부로 **windows** 를 선택 하는 경우 os 디스크는 windows에만 설치 해야 합니다. Linux에는 동일한 요구 사항이 적용 됩니다.

## <a name="vm-not-generalized"></a>일반화 되지 않은 VM

Azure Marketplace에서 가져온 모든 이미지를 다시 사용 하는 경우 운영 체제 VHD를 일반화 해야 합니다.

* **Linux** 의 경우 다음 프로세스는 linux vm을 일반화 하 고 별도의 vm으로 다시 배포 합니다.

  SSH 창에서 다음 명령을 입력합니다. `sudo waagent -deprovision+user`

* **Windows** 의 경우를 사용 하 여 windows 이미지를 일반화 `sysreptool` 합니다.

  이 도구에 대 한 자세한 내용은 `sysreptool` [시스템 준비 (Sysprep) 개요](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)를 참조 하세요.

## <a name="datadisk-errors"></a>DataDisk 오류

데이터 디스크와 관련 된 오류에 대 한 해결 방법은 다음 표를 사용 하십시오.

|오류|이유|솔루션|
|---|---|---|
|`DataDisk- InvalidUrl:`|이 오류는 제품이 제출 될 때 잘못 된 LUN (논리 단위 번호)으로 인해 발생할 수 있습니다.|데이터 디스크에 대 한 LUN 번호 시퀀스가 파트너 센터에 있는지 확인 합니다.|
|`DataDisk- NotFound:`|이 오류는 데이터 디스크가 지정 된 SAS URL에 있지 않기 때문에 발생할 수 있습니다.|데이터 디스크가 지정 된 SAS URL에 있는지 확인 합니다.|

## <a name="remote-access-issue"></a>원격 액세스 문제

Windows 이미지에 RDP (원격 데스크톱 프로토콜) 옵션을 사용할 수 없는 경우이 오류가 발생 합니다.

Windows 이미지를 제출 하기 전에 RDP 액세스를 사용 하도록 설정 합니다.

## <a name="bash-history-failed"></a>Bash 기록 실패

제출 된 이미지의 Bash 기록 크기가 1kb를 초과 하는 경우이 오류가 표시 됩니다. 크기는 잠재적으로 중요 한 정보를 포함 하는 파일을 제한 하기 위해 1kb로 제한 됩니다.

Bash 기록을 삭제 하려면:

1. VM을 배포 하 고 Azure Portal에서 **명령 실행** 옵션을 선택 합니다.

   ![왼쪽 창에서 ' 명령 실행 ' 옵션을 사용 하는 Azure Portal의 스크린샷](./media/create-vm/vm-certification-issues-solutions-3.png)

1. 첫 번째 옵션인 **Runshellscript** 를 선택 하 고 명령을 실행 합니다 `cat /dev/null > ~/.bash_history && history -c` .

   ![Azure Portal에서 ' 명령 스크립트 실행 ' 페이지의 스크린샷](./media/create-vm/vm-certification-issues-solutions-4.png)

1. 명령이 성공적으로 실행 되 면 VM을 다시 시작 합니다.

1. VM을 일반화 하 고 이미지 VHD를 사용 하 여 VM을 중지 합니다.

1. 일반화 된 이미지를 다시 제출 합니다.

## <a name="request-an-exception-on-vm-images-for-select-tests"></a>Select 테스트에 대 한 VM 이미지에서 예외 요청

게시자는 VM 인증 중에 수행 되는 몇 가지 테스트에 대 한 예외를 요청할 수 있습니다. 예외는 드물게 요청을 지원 하기 위해 증명 정보를 제공 하는 경우에 제공 됩니다. 인증 팀은 언제 든 지 예외를 거부 하거나 승인할 수 있는 권리를 보유 합니다.

이 섹션에서는 게시자가 예외를 요청 하는 일반적인 시나리오와이를 요청 하는 방법을 설명 합니다.

### <a name="scenarios-for-exception"></a>예외 시나리오

일반적으로 게시자는 다음과 같은 경우에 예외를 요청 합니다.

- **하나 이상의 테스트 사례에 대 한 예외** 입니다. 테스트 사례에 대 한 예외를 요청 하려면 [파트너 센터 지원](https://aka.ms/marketplacepublishersupport) 담당자에 게 문의 하세요.

- **잠긴 vm/루트 액세스 권한이 없습니다**. 몇 가지 게시자에는 VM이 VM에 설치 된 방화벽과 같은 소프트웨어가 있기 때문에 Vm을 잠가야 하는 시나리오가 있습니다. 이 경우 [인증 된 테스트 도구](https://aka.ms/AzureCertificationTestTool) 를 다운로드 하 고 [파트너 센터 지원](https://aka.ms/marketplacepublishersupport)에서 보고서를 제출 합니다.

- **사용자 지정 템플릿**. 일부 게시자는 vm을 배포 하기 위해 사용자 지정 Azure Resource Manager (ARM) 템플릿이 필요한 VM 이미지를 게시 합니다. 이 경우 인증 팀에서 유효성 검사에 사용할 수 있도록 [파트너 센터 지원](https://aka.ms/marketplacepublishersupport) 에서 사용자 지정 템플릿을 제출 합니다.

### <a name="information-to-provide-for-exception-scenarios"></a>예외 시나리오에 제공할 정보

[파트너 센터 지원](https://aka.ms/marketplacepublishersupport) 에 문의 하 여 시나리오 중 하나에 대 한 예외를 요청 하 고 다음 정보를 포함 합니다.

- **게시자 ID** 입니다. 파트너 중앙 포털 게시자 ID를 입력 합니다.
- **제품 ID/이름** 입니다. 제품 ID 또는 이름을 입력 합니다.
- **SKU/계획 ID** 입니다. VM 제공 계획 ID 또는 SKU를 입력 합니다.
- **버전**. 예외가 필요한 VM 제품 버전을 입력 합니다.
- **예외 유형** 입니다. 테스트, 잠긴 VM 또는 사용자 지정 템플릿을 선택 합니다.
- **요청에 대 한 이유** 입니다. 예외 요청에 대 한 이유 및 테스트 예외에 대 한 정보를 포함 합니다.
- **타임 라인**. 예외의 종료 날짜를 입력 합니다.
- **첨부 파일**. 연결 된 중요 증거 문서:

  - 잠긴 Vm의 경우 테스트 보고서를 연결 합니다.
  - 사용자 지정 템플릿의 경우 사용자 지정 ARM 템플릿을 첨부 파일로 제공 합니다.

  이러한 첨부 파일을 포함 하지 않으면 요청이 거부 됩니다.

## <a name="address-a-vulnerability-or-an-exploit-in-a-vm-offer"></a>문제 해결 또는 VM 제품의 악용

이 섹션에서는 VM 이미지 중 하나를 사용 하 여 취약성 또는 익스플로잇을 검색할 때 새 VM 이미지를 제공 하는 방법을 설명 합니다. Azure Marketplace에 게시 된 Azure VM 제공에만 적용 됩니다.

> [!NOTE]
> 계획에서 마지막 VM 이미지를 제거 하거나 제품에 대 한 마지막 계획을 중지할 수 없습니다.

다음 작업 중 하나를 수행합니다.

- 취약 한 VM 이미지를 대체할 새 VM 이미지가 있는 경우 [고정 vm 이미지 제공](#provide-a-fixed-vm-image)을 참조 하세요.
- 계획의 유일한 VM 이미지를 대체할 새 VM 이미지가 없거나 계획을 완료 한 경우 [계획의 판매를 중지](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan)합니다.
- 제품의 유일한 VM 이미지를 바꾸지 않으려는 경우 [제품 판매를 중지](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan)하는 것이 좋습니다.

### <a name="provide-a-fixed-vm-image"></a>고정 VM 이미지 제공

취약성 또는 익스플로잇이 있는 VM 이미지를 대체할 고정 VM 이미지를 제공 하려면:

1. 새 VM 이미지를 제공 하 여 보안 취약성 또는 악용을 해결 합니다.
1. 보안 취약성 또는 익스플로잇으로 VM 이미지를 제거 합니다.
1. 제품을 다시 게시 합니다.

#### <a name="provide-a-new-vm-image-to-address-the-security-vulnerability-or-exploit"></a>새 VM 이미지를 제공 하 여 보안 취약성 또는 악용을 해결 합니다.

이러한 단계를 완료 하려면 추가 하려는 VM 이미지에 대 한 기술 자산을 준비 합니다. 자세한 내용은 [승인 된 기본을 사용 하 여 가상 머신 만들기](azure-vm-create-using-approved-base.md)또는 [고유한 이미지를 사용 하 여 가상 머신](azure-vm-create-using-own-image.md) 만들기 및 [VM 이미지에 대 한 SAS URI 생성](azure-vm-get-sas-uri.md)을 참조 하세요.

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.
1. 왼쪽 창에서 **상업용 마켓플레이스**  >  **개요** 를 선택 합니다.
1. **제품 별칭** 열에서 제품을 선택 합니다.
1. **계획 개요** 탭의 **이름** 열에서 적절 한 계획을 선택 합니다.
1. **기술 구성** 탭의 **vm 이미지** 에서 **+ vm 이미지 추가** 를 선택 합니다.

   > [!NOTE]
   > 한 번에 하나의 계획에 VM 이미지를 하나만 추가할 수 있습니다. 여러 VM 이미지를 추가 하려면 다음 VM 이미지를 추가 하기 전에 첫 번째 이미지를 게시 합니다.

1. 표시 되는 상자에 새 디스크 버전 및 가상 머신 이미지를 제공 합니다.
1. **초안 저장** 을 선택합니다.

다음으로, 보안 취약성으로 VM 이미지를 제거 합니다.

#### <a name="remove-the-vm-image-with-the-security-vulnerability-or-exploit"></a>보안 취약성 또는 익스플로잇으로 VM 이미지 제거

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.
2. 왼쪽 창에서 **상업용 마켓플레이스**  >  **개요** 를 선택 합니다.
3. **제품 별칭** 열에서 제품을 선택 합니다.
4. **계획 개요** 탭의 **이름** 열에서 적절 한 계획을 선택 합니다.
5. **기술 구성** 탭의 **vm 이미지** 에서 제거 하려는 vm 이미지 옆에 있는 **vm 이미지 제거** 를 선택 합니다.
6. 대화 상자에서 **계속** 을 선택 합니다.
7. **초안 저장** 을 선택합니다.

다음으로 제품을 다시 게시 합니다.

#### <a name="republish-the-offer"></a>제품 다시 게시

1. **검토 및 게시** 를 선택 합니다.
2. 인증 팀에 정보를 제공 해야 하는 경우 **인증에 대 한** 정보 상자에 추가 합니다.
3. **게시** 를 선택합니다.

게시 프로세스를 완료 하려면 [제안 검토 및 게시](review-publish-offer.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [VM 제품 속성 구성](azure-vm-create-properties.md)
- [활성 마켓플레이스 보상](partner-center-portal/marketplace-rewards.md)
- 개선 사항에 대 한 질문이 나 피드백이 있는 경우 [파트너 센터 지원](https://aka.ms/marketplacepublishersupport)에 문의 하세요.
