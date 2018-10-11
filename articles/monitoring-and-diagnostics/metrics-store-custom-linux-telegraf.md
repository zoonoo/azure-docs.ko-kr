---
title: InfluxData Telegraf 에이전트를 사용하여 Linux VM에 대한 사용자 지정 메트릭 수집
description: InfluxData Telegraf 에이전트를 사용하여 Linux VM에 대한 사용자 지정 메트릭 수집
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 651706b269cf24eca85e0601384ef63cb6ed06a2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990708"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>InfluxData Telegraf 에이전트를 사용하여 Linux VM에 대한 사용자 지정 메트릭 수집

Azure Monitor를 사용하면 응용 프로그램 원격 분석, Azure 리소스에서 실행되는 에이전트 또는 외부 모니터링 시스템을 통해서 사용자 지정 메트릭을 수집하고 Azure Monitor에 직접 제출할 수 있습니다. 이 문서에서는 Azure의 Linux VM에서 [InfluxData](https://www.influxdata.com/) Telegraf 에이전트를 배포하고 메트릭을 Azure Monitor로 게시하도록 에이전트를 구성하는 방법에 대한 지침을 제공합니다. 

## <a name="influxdata-telegraf-agent"></a>InfluxData Telegraf 에이전트 

[Telegraf](https://docs.influxdata.com/telegraf/v1.7/)는 150개의 다양한 원본에서 메트릭을 수집할 수 있는 플러그 인 기반 에이전트입니다. VM에서 실행되는 워크로드(예: MySQL, NGINX, Apache 등)에 따라 특수한 입력 플러그 인을 활용하도록 에이전트를 구성하여 메트릭을 수집할 수 있습니다. 플러그 인을 출력한 다음, 에이전트가 선택한 대상에 작성하도록 설정합니다. Telegraf 에이전트는 Azure Monitor 사용자 지정 메트릭 REST API와 직접 통합되고 "Azure Monitor 출력 플러그 인"을 지원합니다. 이를 통해 에이전트가 Linux VM에서 워크로드 특정 메트릭을 수집하고 사용자 지정 메트릭을 Azure Monitor로 제출할 수 있습니다. 

 ![Telegraph 에이전트 개요](./media/metrics-store-custom-linux-telegraf/telegraf-agent-overview.png)

## <a name="send-custom-metrics"></a>사용자 지정 메트릭 보내기 

이 자습서에서는 Ubuntu 16.04 LTS 운영 체제를 실행하는 Linux VM을 배포합니다. Telegraf 에이전트는 대부분의 Linux 운영 체제에 대해 지원됩니다. Debian 및 RPM 패키지는 모두 InfluxData 다운로드 포털에서 패키지되지 않은 Linux 이진 파일과 함께 사용할 수 있습니다. 추가 Telegraf 설치 지침 및 옵션은 이 설치 가이드를 참조하세요. 

[Azure Portal](https://portal.azure.com)에 로그인합니다.

새 Linux VM을 만들려면: 

1. 왼쪽 탐색 창에서  **리소스 만들기** 옵션을 클릭합니다. 
1. *가상 머신* 을 검색합니다.  
1. *Ubuntu 16.04 LTS*를 선택하고 **만들기** 를 클릭합니다. 
1. MyTelegrafVM 과 같은  *VM* 이름을 입력합니다.  
1. 디스크 형식을 **SSD**로 유지한 다음, **사용자 이름**(예:  *azureuser* )을 입력합니다. 
1. 인증  *형식* 에  **암호** 를 선택한 다음, 나중에 사용할 암호를 입력하여 이 VM에 SSH합니다. 
1. 새  **리소스 그룹 만들기** 를 선택한 다음, 이름(예:  *myResourceGroup* )을 입력합니다.  원하는 위치를 선택한 다음,  **확인**을 선택합니다. 

     ![Ubuntu VM 만들기](./media/metrics-store-custom-linux-telegraf/create-vm.png)

1. VM의 크기를 선택합니다. 예를 들어 계산 형식 또는 디스크 형식으로 필터링할 수 있습니다. 

     ![Virtual Machine 크기 Telegraph 에이전트 개요](./media/metrics-store-custom-linux-telegraf/vm-size.png)

1. 설정  **페이지** 의  **네트워크** > **네트워크 보안 그룹** > **공용 인바운드 포트 선택**에서  *HTTP* 및  *SSH(22)* 를 선택합니다. 나머지는 기본값으로 두고  **확인**을 선택합니다. 

1. 요약 페이지에서 만들기를 선택하여 VM 배포를 시작합니다. 

1. VM이 Azure Portal 대시보드에 고정됩니다. 배포가 완료되면 VM 요약이 자동으로 열립니다. 

1. VM 블레이드에서 **ID** 탭으로 이동하고 VM에서 시스템 할당 ID가 *켜기*인지 확인합니다. 
 
![fillin](./media/metrics-store-custom-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>VM에 연결 

VM과의 SSH 연결을 만듭니다. VM의 개요 페이지에서 연결 단추를 선택합니다. 

![fillin](./media/metrics-store-custom-linux-telegraf/connect-VM-button2.png)

가상 머신에 연결 페이지에서 22 포트를 통해 DNS 이름으로 연결하는 기본 옵션을 유지합니다. VM 로컬 계정을 사용하여 로그인에 연결 명령이 표시됩니다. 단추를 클릭하여 명령을 복사합니다. 다음 예제에서는 SSH 연결 명령의 모양을 보여줍니다. 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

Azure Cloud Shell이나 Windows Ubuntu의 Bash와 같은 셸에 SSH 연결 명령을 붙여넣거나 선택한 SSH 클라이언트를 사용하여 연결을 만듭니다. 

## <a name="install-and-configure-telegraf"></a>Telegraf 설치 및 구성 

VM에 Telegraf Debian 패키지를 설치하려면 SSH 세션에서 다음 명령을 실행합니다. 

```cmd
# download the package to the VM 
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.0~rc1-1_amd64.deb 
# install the package 
sudo dpkg -i telegraf_1.8.0~rc1-1_amd64.deb
```
Telegraf의 구성 파일은 Telegraf의 작업을 정의합니다. 기본적으로는 예제 구성 파일을 “/etc/telegraf/telegraf.conf” 경로에 설치합니다. 예제 구성 파일은 가능한 모든 입력 및 출력 플러그 인을 나열합니다. 그러나 다음 명령을 실행하여 사용자 지정 구성 파일을 만들고 에이전트에서 사용하려고 합니다. 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]
> 위에서는 머신에서 실행 중인 워크로드에 따라 "cpu" 및 "mem"이라는 두 개의 입력 플러그 인에서 원하는 대로 입력 플러그 인(Docker, MySQL, NGINX 등)을 추가할 수 있습니다. 입력 플러그 인의 전체 목록은 여기에서 찾을 수 있습니다. 

마지막으로, 새 구성을 사용하여 에이전트를 시작하려면 다음 명령을 실행하여 해당 에이전트를 중지하고 시작하도록 합니다. 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
이제 에이전트는 지정된 각 입력 플러그 인에서 메트릭을 수집하고 Azure Monitor로 내보내게 됩니다. 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>Azure Portal에서 Telegraf 메트릭 그리기 

1. [Azure Portal](https://portal.azure.com)을 엽니다. 

1. 새 모니터 탭으로 이동한 다음,  **메트릭**을 선택합니다.  
     ![fillin](./media/metrics-store-custom-linux-telegraf/metrics.png)

1. 리소스 선택기에서 VM 선택

     ![fillin](./media/metrics-store-custom-linux-telegraf/metric-chart.png)

1. *Telegraf/CPU* 네임 스페이스를 선택하고, *usage_system* 메트릭을 선택합니다. 이 메트릭의 차원으로 필터를 선택하거나 분할할 수 있습니다.  

     ![fillin](./media/metrics-store-custom-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>추가 구성 

위의 연습에서는 몇 가지 기본 입력 플러그 인에서 메트릭을 수집하도록 Telegraf 에이전트를 구성하는 방법에 대한 정보를 제공합니다. Telegraf 에이전트는 일부 지원 추가 구성 옵션을 비롯하여 150개가 넘는 입력 플러그 인을 지원합니다. InfluxData는 [지원되는 플러그 인 목록](https://docs.influxdata.com/telegraf/v1.7/plugins/inputs/) 및 [구성 방법](https://docs.influxdata.com/telegraf/v1.7/administration/configuration/)에 대한 지침을 게시합니다.  

또한 이 연습에서는 Telegraf 에이전트를 사용하여 에이전트를 배포한 VM에 대한 메트릭을 내보낼 수 있습니다. Telegraf 에이전트를 다른 리소스에 대한 메트릭의 수집기와 전달자로 사용할 수도 있습니다. 다른 Azure 리소스에 대한 메트릭을 내보내도록 에이전트를 구성하는 방법을 알아보려면 [Telegraf에 대한 Azure Monitor 사용자 지정 메트릭 출력](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md)을 참조하세요.  

## <a name="clean-up-resources"></a>리소스 정리 

리소스 그룹, 가상 머신 및 모든 관련 리소스가 더 이상 필요 없는 경우 삭제해도 됩니다. 삭제하려면 가상 머신의 리소스 그룹을 선택하고, 삭제를 선택한 다음, 삭제할 리소스 그룹의 이름을 확인합니다. 

## <a name="next-steps"></a>다음 단계
- [사용자 지정 메트릭](metrics-custom-overview.md)에 대해 자세히 알아보세요.


