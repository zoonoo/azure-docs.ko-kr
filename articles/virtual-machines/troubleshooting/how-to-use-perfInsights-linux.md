---
title: Microsoft Azure에서 PerfInsights Linux를 사용 하는 방법 | Microsoft Docs
description: PerfInsights를 사용 하 여 Linux VM 성능 문제를 해결 하는 방법을 학습 합니다.
services: virtual-machines-linux'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 7/10/2019
ms.author: genli
ms.openlocfilehash: f618066f19a5cbbf25bc1fcc872cc654ce96dae3
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857223"
---
# <a name="how-to-use-perfinsights"></a>PerfInsights를 사용하는 방법

[PerfInsights linux](https://aka.ms/perfinsightslinuxdownload) 는 진단 데이터를 수집 및 분석 하는 자체 진단 도구 이며, Azure에서 linux 가상 머신 성능 문제를 해결 하는 데 도움이 되는 보고서를 제공 합니다. PerfInsights은 지원 되는 가상 컴퓨터에서 독립 실행형 도구로 실행 하거나 [Azure 가상 컴퓨터에 대 한 성능 진단을](performance-diagnostics.md)사용 하 여 포털에서 직접 실행할 수 있습니다.

가상 머신 성능 문제가 있는 경우 기술 지원에 문의하기 전에 이 도구를 실행합니다.

## <a name="supported-troubleshooting-scenarios"></a>지원되는 문제 해결 시나리오

PerfInsights는 여러 종류의 정보를 수집하고 분석할 수 있습니다. 다음 섹션에서 일반적인 시나리오를 설명합니다.

### <a name="quick-performance-analysis"></a>빠른 성능 분석

이 시나리오에서는 다음을 포함 하 여 가상 머신의 저장소 및 하드웨어 구성과 같은 기본 정보를 수집 하 고 다양 한 로그를 수집 합니다.

- 운영 체제 정보

- PCI 장치 정보

- 일반 게스트 OS 로그

- 구성 파일

- 저장소 정보

- Azure 가상 머신 구성 ( [azure Instance Metadata Service](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)를 사용 하 여 수집)

- 실행 중인 프로세스, 디스크, 메모리 및 CPU 사용량 목록

- 네트워킹 정보

이 모음은 시스템에 영향을 주지 않아야 하는 수동적인 정보 모음입니다.

>[!Note]
>빠른 성능 분석 시나리오는 다음과 같은 각각의 시나리오에 자동으로 포함 됩니다.

### <a name="performance-analysis"></a>성능 분석

이 시나리오는 빠른 성능 분석과 유사 하지만, 더 긴 기간 동안 진단 정보를 캡처할 수 있습니다.

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>PerfInsights에서 수집 되는 정보의 종류

Linux 가상 머신, 운영 체제, 블록 장치, 높은 리소스 소비자, 구성 및 다양 한 로그에 대 한 정보가 수집 됩니다. 자세한 내용은 다음과 같습니다.

- 운영 체제
  - Linux 배포 및 버전
  - 커널 정보
  - 드라이버 정보

- 하드웨어
  - PCI 장치 [`*`]

- 프로세스 및 메모리
  - 프로세스 목록 (작업 이름, 사용 된 메모리, 열린 파일)
  - 총, 사용 가능 및 사용 가능한 실제 메모리
  - 총, 사용 가능 및 사용 가능한 스왑 메모리
  - 5 초 간격으로 CPU를 프로 파일링 하 고 CPU 사용량을 처리 합니다.
  - 프로 파일링 캡처는 5 초 간격으로 메모리 사용을 처리 합니다.

- 네트워킹  
  - 어댑터 통계를 포함 하는 네트워크 어댑터 목록
  - 네트워크 라우팅 테이블
  - 열린 포트 및 상태

- 저장 공간
  - 장치 목록 차단
  - 파티션 목록
  - 탑재 위치 목록
  - MDADM 볼륨 정보
  - LVM 볼륨 정보
  - 5 초 간격으로 모든 디스크에서 프로 파일링 캡처

- 로그
  - /var/log/messages
  - /var/log/syslog
  - /var/log/kern.log
  - /var/log/cron.log
  - /var/log/boot.log
  - /var/log/yum.log
  - /var/log/dpkg.log
  - /var/log/cloud-init.log
  - /var/log/cloud-init-output.log
  - /var/log/gpu-manager.log
  - /var/log/waagent.log
  - /var/log/azure/[확장 폴더]/\*로그\*
  - /var/opt/microsoft/omsconfig/omsconfig.log
  - /var/opt/microsoft/omsagent/log/omsagent.log
  - /etc/waagent.config
  - 최근 5 일간 journalctl의 출력

- [Azure 가상 머신 인스턴스 메타 데이터](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

>[!Note]
>[`*`] PCI 정보는 Debian 및 SLES 배포판에서 아직 수집 되지 않습니다.

## <a name="run-the-perfinsights-linux-on-your-vm"></a>VM에서 PerfInsights Linux를 실행 합니다.

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>도구를 실행 하기 전에 알아두어야 할 사항

#### <a name="tool-requirements"></a>도구 요구 사항

- 이 도구는 성능 문제가 있는 VM에서 실행해야 합니다.
- Python 2.7이 VM에 설치 되어 있어야 합니다.

- 현재 지원 되는 배포는 다음과 같습니다.

    | 배포               | 버전                                         |
    |----------------------------|-------------------------------------------------|
    | Oracle Linux 서버        | 6.10 [`*`], 7.3, 7.6, 7.5 (Oracle-Database-Ee 13.8 marketplace 이미지)|
    | CentOS                     | 6.5 [`*`], 7.6                                    |
    | RHEL                       | 7.2, 7.5, 8.0 [`*`]                               |
    | Ubuntu                     | 14.04, 16.04, 18.04                               |
    | Debian                     | 8, 9, 10 [`*`]                                    |
    | SLES                       | 12 SP4 [`*`]                                      |
    |                            |                                                   |

>[!Note]
>[`*`] [알려진 문제](#known-issues) 섹션을 참조 하세요.

### <a name="known-issues"></a>알려진 문제

- RHEL 8에는 기본적으로 Python이 설치 되어 있지 않습니다. PerfInsights Linux를 실행 하려면 먼저 Python 2.7을 설치 해야 합니다.

- CentOS 6.x에서 게스트 에이전트 정보 수집이 실패할 수 있음

- PCI 장치 정보는 Debian 기반 배포에서 수집 되지 않습니다.

- 일부 배포에서 LVM 정보가 부분적으로 수집 됨

### <a name="how-do-i-run-perfinsights"></a>PerfInsights 실행 어떻게 할까요?

Azure Portal에서 Azure Performance Diagnostics를 설치 하 여 가상 머신에서 PerfInsights을 실행할 수 있습니다. 독립 실행형 도구로도 실행할 수 있습니다.

>[!Note]
>PerfInsights는 단순히 데이터를 수집 하 고 분석 합니다. 시스템을 수정 하지는 않습니다.

#### <a name="install-and-run-perfinsights-from-the-azure-portal"></a>Azure Portal에서 PerfInsights를 설치 하 고 실행 합니다.

이 옵션에 대 한 자세한 내용은 [Azure 성능 진단](performance-diagnostics.md)을 참조 하세요.  

#### <a name="run-perfinsights-in-standalone-mode"></a>독립 실행형 모드에서 PerfInsights 실행

PerfInsights 도구를 실행하려면 다음 단계를 수행합니다.

1. 가상 컴퓨터의 폴더에 [PerfInsights](https://aka.ms/perfinsightslinuxdownload) 를 다운로드 하 고 터미널에서 아래 명령을 사용 하 여 콘텐츠를 추출 합니다.

   ```bash
   wget https://download.microsoft.com/download/9/F/8/9F80419C-D60D-45F1-8A98-718855F25722/PerfInsights.tar.gz
   ```

   ```bash
   tar xzvf PerfInsights.tar.gz
   ```

2. 파일이 포함 `perfinsights.py` 된 폴더로 이동한 다음를 실행 `perfinsights.py` 하 여 사용 가능한 명령줄 매개 변수를 확인 합니다.

    ```bash
    cd <the path of PerfInsights folder>
    sudo python perfinsights.py
    ```

    ![PerfInsights Linux 명령줄 출력의 스크린샷](media/how-to-use-perfinsights-linux/perfinsights-linux-commandline.png)

    PerfInsights 시나리오를 실행하기 위한 기본 구문은 다음과 같습니다.

    ```bash
    sudo python perfinsights.py -r <ScenarioName> -d [duration]<H | M | S> [AdditionalOptions]
    ```

    아래 예제를 사용 하 여 1 분 동안 빠른 성능 분석 시나리오를 실행 하 고/tmp/output 폴더 아래에 결과를 만들 수 있습니다.

    ```bash
    sudo python perfinsights.py -r quick -d 1M -a -o /tmp/output
    ```

    아래 예제를 사용 하 여 5 분 동안 성능 분석 시나리오를 실행 하 고 결과 tar 구슬을 저장소 계정에 업로드할 수 있습니다.

    ```bash
    sudo python perfinsights.py -r vmslow -d 300S -a -t <StorageAccountName> -k <StorageAccountKey> -i <full resource Uri of the current VM>
    ```

    >[!Note]
    >시나리오를 실행하기 전에 PerfInsights는 사용자가 진단 정보 공유 및 EULA에 동의할 것을 요청합니다. 이러한 프롬프트를 건너뛰려면 **-a 또는----------** --------------
    >
    >Microsoft와 함께 사용 하는 지원 엔지니어의 요청에 따라 PerfInsights을 실행 하는 활성 지원 티켓이 있는 경우 **-s 또는--지원 요청** 옵션을 사용 하 여 지원 티켓 번호를 제공 해야 합니다.

실행이 완료 되 면 출력 폴더가 지정 되지 않은 경우 PerfInsights와 동일한 폴더에 새 tar 파일이 표시 됩니다. 파일 이름은 **PerformanceDiagnostics\_yyyy-MM-\_dd hh-mm-ss-fff.zip release.tar.gz입니다** . 분석을 위해이 파일을 지원 에이전트로 전송 하거나 파일 내에서 보고서를 열어 결과와 권장 사항을 검토할 수 있습니다.

## <a name="review-the-diagnostics-report"></a>진단 보고서 검토

PerformanceDiagnostics hh-mm-ss-fff.zip release.tar.gz 파일 내에서 PerfInsights의 결과를 자세히 설명 하는 HTML 보고서를 찾을 수 있습니다. **\_\_** 보고서를 검토 하려면 **\_PerformanceDiagnostics yyyy-MM-\_dd hh-mm-ss-fff.zip release.tar.gz** 파일을 확장 한 다음 **PerfInsights report .html** 파일을 엽니다.

### <a name="overview-tab"></a>개요 탭

**개요** 탭에서는 기본 실행 세부 정보 및 가상 컴퓨터 정보를 제공 합니다. 검색 **결과** 탭에는 PerfInsights 보고서의 모든 다른 섹션에 있는 권장 사항에 대 한 요약이 표시 됩니다.

![PerfInsights 보고서의 스크린샷](media/how-to-use-perfinsights-linux/perfinsights-linux-overview.png)  
![PerfInsights 보고서의 스크린샷](media/how-to-use-perfinsights-linux/perfinsights-linux-findings-tab.png)

> [!NOTE]
> 높음으로 분류된 검색 결과는 성능 문제를 일으킬 수 있는 알려진 문제입니다. 보통으로 분류된 검색 결과는 반드시 성능 문제를 일으키지는 않지만 최적이 아닌 구성을 나타냅니다. 낮음으로 분류되는 검색 결과는 정보만 제공합니다.

권장 사항과, 모든 높음 및 보통 결과에 대한 링크를 검토합니다. 성능에 어떻게 영향을 미칠 수 있고 성능 최적화 구성을 위한 모범 사례는 무엇인지 살펴봅니다.

### <a name="cpu-tab"></a>CPU 탭

**Cpu** 탭 PerfInsights을 실행 하는 동안 시스템 차원의 cpu 사용량에 대 한 정보를 제공 합니다. 높은 cpu 사용량 기간 및 상위 장기 실행 CPU 소비자에 대 한 정보는 높은 CPU 관련 문제를 해결 하는 데 도움이 됩니다.

![PerfInsights 보고서 CPU 탭의 스크린샷](media/how-to-use-perfinsights-linux/perfinsights-linux-cpu-tab.png)

### <a name="storage-tab"></a>저장소 탭

**검색 결과** 섹션은 저장소와 관련된 다양한 검색 결과 및 권장 사항을 표시합니다.

**블록 장치** 및 기타 관련 섹션 (예: **파티션**, **lvm**및 **mdadm** 탭)에는 차단 장치를 구성 하 고 서로 관련 하는 방법이 설명 되어 있습니다.

![저장소 탭의 스크린샷](media/how-to-use-perfinsights-linux/perfinsights-linux-storage-tab.png)  
![MDADM 탭의 스크린샷](media/how-to-use-perfinsights-linux/perfinsights-linux-mdadm-config.png)

### <a name="linux-tab"></a>Linux 탭

**Linux** 탭에는 VM에서 실행 되는 하드웨어 및 운영 체제에 대 한 정보가 포함 되어 있습니다. 세부 정보에는 실행 중인 프로세스 목록 및 게스트 에이전트, PCI, CPU, 드라이버 및 LIS 드라이버에 대 한 정보가 포함 됩니다.

![Linux 탭의 스크린샷](media/how-to-use-perfinsights-linux/perfinsights-linux-tab.png)

## <a name="next-steps"></a>다음 단계

추가 검토를 위해 Microsoft 지원 서비스에 진단 로그 및 보고서를 업로드할 수 있습니다. Microsoft 지원 직원으로 작업 하는 경우 문제 해결 프로세스를 지원 하기 위해 PerfInsights에서 생성 된 출력을 전송 하도록 요청할 수 있습니다.

다음 스크린샷과 유사한 메시지가 표시됩니다.

![Microsoft 지원 웹 사이트의 샘플 메시지 스크린샷](media/how-to-use-perfinsights-linux/support-email.png)

메시지의 지침에 따라 파일 전송 작업 영역에 액세스합니다. 보안을 강화하기 위해 처음 사용할 때 암호를 변경해야 합니다.

로그인 한 후 PerfInsights에 의해 수집 된 **\_PerformanceDiagnostics\_hh-mm-ss-fff.zip release.tar.gz** 파일을 업로드 하는 대화 상자가 표시 됩니다.
