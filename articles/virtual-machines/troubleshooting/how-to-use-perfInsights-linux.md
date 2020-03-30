---
title: 마이크로소프트 Azure에서 PerfInsights 리눅스를 사용 하는 방법 | 마이크로 소프트 문서
description: PerfInsights를 사용하여 Linux VM 성능 문제를 해결하는 방법을 알아봅니다.
services: virtual-machines-linux'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 7/10/2019
ms.author: genli
ms.openlocfilehash: 19b2fcaed2c80d4ca52ada9f9f0898479e73bcf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266989"
---
# <a name="how-to-use-perfinsights"></a>PerfInsights를 사용하는 방법

[PerfInsights Linux는](https://aka.ms/perfinsightslinuxdownload) 진단 데이터를 수집 및 분석하는 자체 진단 도구이며 Azure에서 Linux 가상 시스템 성능 문제를 해결하는 데 도움이 되는 보고서를 제공합니다. PerfInsights는 지원되는 가상 컴퓨터에서 독립 실행형 도구로 실행하거나 Azure 가상 시스템에 대한 성능 진단을 사용하여 포털에서 직접 실행할 수 [있습니다.](performance-diagnostics.md)

가상 머신 성능 문제가 있는 경우 기술 지원에 문의하기 전에 이 도구를 실행합니다.

## <a name="supported-troubleshooting-scenarios"></a>지원되는 문제 해결 시나리오

PerfInsights는 여러 종류의 정보를 수집하고 분석할 수 있습니다. 다음 섹션에서 일반적인 시나리오를 설명합니다.

### <a name="quick-performance-analysis"></a>빠른 성능 분석

이 시나리오에서는 가상 시스템의 저장소 및 하드웨어 구성과 같은 기본 정보( 다음을 포함한 다양한 로그)를 수집합니다.

- 운영 체제 정보

- PCI 장치 정보

- 일반 게스트 OS 로그

- 구성 파일

- 스토리지 정보

- Azure 가상 컴퓨터 [구성(Azure 인스턴스 메타데이터 서비스를](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)사용하여 수집)

- 실행 중인 프로세스, 디스크, 메모리 및 CPU 사용량 목록

- 네트워킹 정보

이 모음은 시스템에 영향을 주지 않아야 하는 수동적인 정보 모음입니다.

>[!Note]
>빠른 성능 분석 시나리오는 다음 각 시나리오에 자동으로 포함됩니다.

### <a name="performance-analysis"></a>성능 분석

이 시나리오는 빠른 성능 분석과 유사하지만 진단 정보를 더 긴 기간 동안 캡처할 수 있습니다.

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>PerfInsights에서 수집하는 정보의 종류

Linux 가상 컴퓨터, 운영 체제, 블록 장치, 높은 리소스 소비자, 구성 및 다양한 로그에 대한 정보가 수집됩니다. 자세한 내용은 다음과 같습니다.

- 운영 체제
  - 리눅스 배포판 및 버전
  - 커널 정보
  - 운전자 정보

- 하드웨어
  - PCI 장치`*`[ ]

- 프로세스 및 메모리
  - 프로세스 목록(작업 이름, 사용된 메모리, 파일 열거)
  - 총, 사용 가능 및 사용 가능한 실제 메모리
  - 총, 사용 가능 및 사용 가능한 스왑 메모리
  - CPU 프로파일링 캡처 및 5초 간격으로 CPU 사용량 처리
  - 5초 간격으로 프로세스 메모리 사용량 의 프로파일링 캡처

- 네트워킹  
  - 어댑터 통계가 있는 네트워크 어댑터 목록
  - 네트워크 라우팅 테이블
  - 열린 포트 및 상태

- 스토리지
  - 장치 목록 차단
  - 파티션 목록
  - 마운트 포인트 리스트
  - MDADM 볼륨 정보
  - LVM 볼륨 정보
  - 5초 간격으로 모든 디스크에서 캡처 프로파일링

- 로그
  - /var/log/messages
  - /var/로그/시슬로그
  - /var/log/kern.log
  - /var/log/cron.log
  - /var/log/boot.log
  - /var/log/yum.log
  - /var/log/dpkg.log
  - /var/로그/클라우드-init.log
  - /var/로그/클라우드-이릿 출력.log
  - /var/로그/gpu-manager.log
  - /var/log/waagent.log
  - /var/로그/azure/[확장 폴더]/로그\*\*
  - /var/옵트/마이크로소프트/옴스콘피그/옴스콘피그.log
  - /var/opt/microsoft/omsagent/log/omsagent.log
  - /etc/waagent.config
  - 지난 5일 동안의 저널ctl 출력

- [Azure 가상 시스템 인스턴스 메타데이터](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

>[!Note]
>[`*`] 데비안 및 SLES 배포판에서 PCI 정보가 아직 수집되지 않았습니다.

## <a name="run-the-perfinsights-linux-on-your-vm"></a>VM에서 PerfInsights 리눅스를 실행

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>도구를 실행하기 전에 알아야 할 사항

#### <a name="tool-requirements"></a>도구 요구 사항

- 이 도구는 성능 문제가 있는 VM에서 실행해야 합니다.
- 파이썬 2.7은 VM에 설치되어야 합니다.

- 다음 배포는 현재 지원됩니다.

    | 배포               | 버전                                         |
    |----------------------------|-------------------------------------------------|
    | 오라클 리눅스 서버        | 6.10`*`[], 7.3, 7.6, 7.5 (오라클-데이터베이스-Ee 13.8 마켓플레이스 이미지)|
    | CentOS                     | 6.5`*`[], 7.6                                    |
    | RHEL                       | 7.2, 7.5, 8.0 []`*`                               |
    | Ubuntu                     | 14.04, 16.04, 18.04                               |
    | Debian                     | 8, 9, 10 [`*`                                    |
    | SLES                       | 12 SP4`*`[ ]                                      |
    |                            |                                                   |

>[!Note]
>[`*`] [알려진 문제](#known-issues) 섹션을 참조하십시오.

### <a name="known-issues"></a>알려진 문제

- RHEL 8에는 기본적으로 파이썬이 설치되어 있지 않습니다. PerfInsights 리눅스를 실행하려면 먼저 파이썬 2.7을 설치해야 합니다.

- 게스트 에이전트 정보 수집이 CentOS 6.x에서 실패할 수 있습니다.

- 데비안 기반 배포판에서 PCI 장치 정보가 수집되지 않음

- LVM 정보는 일부 배포판에서 부분적으로 수집됩니다.

### <a name="how-do-i-run-perfinsights"></a>PerfInsights를 실행하려면 어떻게 해야 합니까?

Azure 포털에서 Azure 성능 진단을 설치하여 가상 컴퓨터에서 PerfInsights를 실행할 수 있습니다. 독립 실행형 도구로도 실행할 수 있습니다.

>[!Note]
>PerfInsights는 단순히 데이터를 수집하고 분석합니다. 시스템을 수정하지 않습니다.

#### <a name="install-and-run-perfinsights-from-the-azure-portal"></a>Azure Portal에서 PerfInsights 설치 및 실행

이 옵션에 대한 자세한 내용은 [Azure 성능 진단을](performance-diagnostics.md)참조하십시오.  

#### <a name="run-perfinsights-in-standalone-mode"></a>독립 실행형 모드에서 PerfInsights 실행

PerfInsights 도구를 실행하려면 다음 단계를 수행합니다.

1. [PerfInsights.tar.gz를](https://aka.ms/perfinsightslinuxdownload) 가상 머신의 폴더에 다운로드하고 터미널에서 아래 명령을 사용하여 내용을 추출합니다.

   ```bash
   wget https://download.microsoft.com/download/9/F/8/9F80419C-D60D-45F1-8A98-718855F25722/PerfInsights.tar.gz
   ```

   ```bash
   tar xzvf PerfInsights.tar.gz
   ```

2. 파일이 포함된 `perfinsights.py` 폴더로 이동한 `perfinsights.py` 다음 실행하여 사용 가능한 명령줄 매개 변수를 봅니다.

    ```bash
    cd <the path of PerfInsights folder>
    sudo python perfinsights.py
    ```

    ![PerfInsights 리눅스 명령줄 출력의 스크린 샷](media/how-to-use-perfinsights-linux/perfinsights-linux-commandline.png)

    PerfInsights 시나리오를 실행하기 위한 기본 구문은 다음과 같습니다.

    ```bash
    sudo python perfinsights.py -r <ScenarioName> -d [duration]<H | M | S> [AdditionalOptions]
    ```

    아래 예제를 사용하여 1분 동안 빠른 성능 분석 시나리오를 실행하고 /tmp/output 폴더 아래에 결과를 만들 수 있습니다.

    ```bash
    sudo python perfinsights.py -r quick -d 1M -a -o /tmp/output
    ```

    아래 예제를 사용하여 5분 동안 성능 분석 시나리오를 실행하고 결과 tar ball을 저장소 계정에 업로드할 수 있습니다.

    ```bash
    sudo python perfinsights.py -r vmslow -d 300S -a -t <StorageAccountName> -k <StorageAccountKey> -i <full resource Uri of the current VM>
    ```

    >[!Note]
    >시나리오를 실행하기 전에 PerfInsights는 사용자가 진단 정보 공유 및 EULA에 동의할 것을 요청합니다. **-a 또는 --accept-고지 사항 및 공유 진단** 옵션을 사용하여 이러한 프롬프트를 건너뜁니다.
    >
    >Microsoft에서 활성 지원 티켓을 사용하고 작업 중인 지원 엔지니어의 요청에 따라 PerfInsights를 실행하는 경우 **-s 또는 --support-request** 옵션을 사용하여 지원 티켓 번호를 제공해야 합니다.

실행이 완료되면 출력 폴더가 지정되지 않은 경우 PerfInsights와 동일한 폴더에 새 타르 파일이 나타납니다. 파일의 이름은 **성능 진단\_yyyy-MM-dd\_hh-mm-s-fff.tar.gz입니다.** 분석을 위해 이 파일을 지원 에이전트에 보내거나 파일 내부의 보고서를 열어 결과 및 권장 사항을 검토할 수 있습니다.

## <a name="review-the-diagnostics-report"></a>진단 보고서 검토

성능 **진단\_yyyy-MM-dd\_hh-mm-ss-fff.tar.gz** 파일 내에서 PerfInsights의 결과를 자세히 설명하는 HTML 보고서를 찾을 수 있습니다. 보고서를 검토하려면 **PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.tar.gz** 파일을 확장한 다음 **PerfInsights Report.html** 파일을 엽니다.

### <a name="overview-tab"></a>개요 탭

**개요** 탭에서는 기본 실행 세부 정보와 가상 시스템 정보를 제공합니다. **결과** 탭에는 PerfInsights 보고서의 모든 다른 섹션의 권장 사항 요약이 표시됩니다.

![퍼프인사이트 보고서의 스크린샷](media/how-to-use-perfinsights-linux/perfinsights-linux-overview.png)  
![퍼프인사이트 보고서의 스크린샷](media/how-to-use-perfinsights-linux/perfinsights-linux-findings-tab.png)

> [!NOTE]
> 높음으로 분류된 검색 결과는 성능 문제를 일으킬 수 있는 알려진 문제입니다. 보통으로 분류된 검색 결과는 반드시 성능 문제를 일으키지는 않지만 최적이 아닌 구성을 나타냅니다. 낮음으로 분류되는 검색 결과는 정보만 제공합니다.

권장 사항과, 모든 높음 및 보통 결과에 대한 링크를 검토합니다. 성능에 어떻게 영향을 미칠 수 있고 성능 최적화 구성을 위한 모범 사례는 무엇인지 살펴봅니다.

### <a name="cpu-tab"></a>CPU 탭

**CPU** 탭은 PerfInsights 실행 중에 시스템 전체CPU 사용량에 대한 정보를 제공합니다. 높은 CPU 사용 기간 과 최고 장기 실행 CPU 소비자에 대 한 정보는 높은 CPU 관련 문제를 해결 하는 데 도움이 될 것입니다.

![PerfInsights 보고서 CPU 탭의 스크린샷](media/how-to-use-perfinsights-linux/perfinsights-linux-cpu-tab.png)

### <a name="storage-tab"></a>스토리지 탭

**검색 결과** 섹션은 스토리지와 관련된 다양한 검색 결과 및 권장 사항을 표시합니다.

**블록 장치** 및 **파티션,** **LVM**및 **MDADM** 탭과 같은 기타 관련 섹션은 블록 장치가 서로 구성되고 서로 관련되는 방법을 설명합니다.

![저장소 탭의 스크린샷](media/how-to-use-perfinsights-linux/perfinsights-linux-storage-tab.png)  
![MDADM 탭의 스크린 샷](media/how-to-use-perfinsights-linux/perfinsights-linux-mdadm-config.png)

### <a name="linux-tab"></a>리눅스 탭

**Linux** 탭에는 VM에서 실행되는 하드웨어 및 운영 체제에 대한 정보가 포함되어 있습니다. 자세한 내용으로는 실행 중인 프로세스 목록과 게스트 에이전트, PCI, CPU, 드라이버 및 LIS 드라이버에 대한 정보가 포함되어 있습니다.

![리눅스 탭의 스크린 샷](media/how-to-use-perfinsights-linux/perfinsights-linux-tab.png)

## <a name="next-steps"></a>다음 단계

추가 검토를 위해 Microsoft 지원 서비스에 진단 로그 및 보고서를 업로드할 수 있습니다. Microsoft 지원 담당자와 함께 작업할 때 문제 해결 프로세스를 지원하기 위해 PerfInsights에서 생성한 출력을 전송하도록 요청할 수 있습니다.

다음 스크린샷과 유사한 메시지가 표시됩니다.

![Microsoft 지원 웹 사이트의 샘플 메시지 스크린샷](media/how-to-use-perfinsights-linux/support-email.png)

메시지의 지침에 따라 파일 전송 작업 영역에 액세스합니다. 보안을 강화하기 위해 처음 사용할 때 암호를 변경해야 합니다.

로그인한 후 PerfInsights에서 수집한 **PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.gz** 파일을 업로드하는 대화 상자를 찾을 수 있습니다.
