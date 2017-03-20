---
title: "VMware에서 Azure로의 Azure Site Recovery Deployment Planner | Microsoft Docs"
description: "Azure Site Recovery의 Deployment Planner 사용자 가이드입니다."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 2/21/2017
ms.author: nisoneji
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 2575621d72b7db2b090ba923324697b7fa7b8308
ms.lasthandoff: 03/15/2017


---
#<a name="azure-site-recovery-deployment-planner"></a>Azure Site Recovery Deployment Planner
이 문서는 VMware에서 Azure로의 프로덕션 배포를 위한 Azure Site Recovery의 Deployment Planner 사용자 가이드입니다.


##<a name="overview"></a>개요

Azure Site Recovery를 사용하여 VMware 가상 컴퓨터를 보호하려면 먼저 일일 데이터 변경률에 따라 충분한 대역폭을 할당하여 원하는 RPO를 충족해야 합니다. 적절한 수의 구성 서버와 프로세스 서버를 온-프레미스에 배포해야 합니다. 또한 시간이 지남에 따라 늘어나는 사용량으로 인한 원본 프로덕션 서버의 확장을 고려하여 적절한 유형 및 개수의 대상 Azure Storage(표준 또는 프리미엄) 계정을 생성해야 합니다. 저장소 유형은 워크로드 특성(읽기/쓰기 IOPS, 데이터 변동) 및 Azure Site Recovery 제한에 따라 가상 컴퓨터별로 결정됩니다.  

Azure Site Recovery Deployment Planner 공개 미리 보기는 현재 Azure 시나리오를 위한 VMware에서만 사용할 수 있는 명령줄 도구입니다. 이 도구를 통해 VMware 가상 컴퓨터를 원격으로 프로파일링하여(프로덕션에 전혀 영향을 미치지 않음) 성공적인 복제 및 테스트 장애 조치를 위한 대역폭 및 Azure 저장소 요구 사항을 이해할 수 있습니다.  온-프레미스 환경의 Azure Site Recovery 구성 요소를 설치하지 않고도 이 도구를 실행할 수 있지만, 달성된 처리량에 대한 정확한 결과를 얻으려면 프로덕션 배포의 첫 번째 단계 중 하나로 배포해야 하는 Azure Site Recovery 구성 서버의 최소 요구 사항을 충족하는 Windows Server에서 Planner를 실행하는 것이 좋습니다.

이 도구는 다음과 같은 세부 정보를 제공합니다.

**호환성 평가**<br>
* 디스크 수, 디스크 크기, IOPS 및 변동에 따른 가상 컴퓨터 적합성 평가

**네트워크 대역폭 요구량 대 RPO 평가**<br>
* 델타 복제에 필요한 예상 네트워크 대역폭<br>
* Azure Site Recovery를 통해 온-프레미스에서 Azure로 가져올 수 있는 처리량<br>
* 지정된 시간 내에 초기 복제를 완료하기 위해 예상 대역폭에 따라 일괄 처리할 가상 컴퓨터의 수<br>

**Microsoft Azure 인프라 요구 사항**<br>
* 각 가상 컴퓨터에 대한 저장소 유형(표준 또는 프리미엄 저장소) 요구 사항<br>
* 복제를 위해 프로비전되는 표준 및 프리미엄 저장소 계정의 총 수<br>
* Azure Storage 지침에 따른 저장소 계정 명명 제안<br>
* 모든 가상 컴퓨터의 저장소 계정 배치<br>
* 테스트 장애 조치 및 구독에 대한 장애 조치 이전에 프로비전되는 Microsoft Azure 코어의 수<br>
* 각 온-프레미스 가상 컴퓨터에 권장되는 Microsoft Azure 가상 컴퓨터의 크기<br>

**온-프레미스 인프라 요구 사항**<br>
* 온-프레미스 배포에 필요한 구성 서버 및 프로세스 서버의 수<br>

>[!IMPORTANT]
>
>이 도구의 모든 계산은 시간이 지남에 따라 사용량이 증가하고 모든 프로파일링 메트릭(읽기/쓰기 IOPS, 변동 등) 중에서 95번째 백분위 수를 차지하므로 워크로드 특성에서 30% 증가율을 가정하여 수행됩니다. 이러한 매개 변수, 즉 증가율과 백분위 수 계산은 모두 구성할 수 있습니다. 계산에 사용된 [증가율](site-recovery-deployment-planner.md#growth-factor) 및 [백분위 수 값](site-recovery-deployment-planner.md#percentile-value-used-for-the-calculation)에 대해 자세히 알아보세요.
>


## <a name="requirements"></a>요구 사항
이 도구에는 두 가지 주요 단계, 즉 프로파일링과 보고서 생성 단계가 있습니다. 또한 처리량만 계산하는 세 번째 옵션도 있습니다. 프로파일링/처리량 측정이 시작되는 서버에 대한 요구 사항은 다음과 같습니다.

| 요구 사항 | 설명|
|---|---|
|프로파일링 및 처리량 측정| <br>운영 체제: Microsoft Windows Server 2012 R2 <br>가장 적합한 최소한의 구성 서버 [크기](https://aka.ms/asr-v2a-on-prem-components)는 다음과 같습니다.<br>컴퓨터 구성: 8개 vCPus, 16GB RAM, 300GB HDD<br [Microsoft .NET Framework 4.5](https://aka.ms/dotnet-framework-45)<br>[VMware vSphere PowerCLI 6.0 R3](https://developercenter.vmware.com/tool/vsphere_powercli/6.0)<br>[Visual Studio 2012용 Microsoft Visual C++ 재배포 가능 패키지](https://aka.ms/vcplusplus-redistributable)<br> 이 서버에서 Microsoft Azure에 대한 인터넷 액세스<br> Microsoft Azure Storage 계정<Br>서버에 대한 관리자 액세스<br>사용 가능한 최소 디스크 공간: 100GB(각각 30일 동안 프로파일링하는 평균 3개의 디스크를 갖춘 1,000개의 가상 컴퓨터를 가정)|
| 보고서 생성| Microsoft Excel 2013 이상을 포함한 모든 Windows PC/Windows Server |
| 사용자 권한 | 프로파일링 중에 VMware vCenter/vSphere 서버에 액세스하는 데 사용되는 사용자 계정에 대한 읽기 전용 권한|


> [!NOTE]
>
> 이 도구는 VMDK 및 RDM 디스크를 갖춘 가상 컴퓨터만 프로파일링할 수 있습니다. iSCSI 또는 NFS 디스크를 갖춘 가상 컴퓨터는 프로파일링할 수 없습니다. Azure Site Recovery에서 VMware 서버용 iSCSI 및 NFS 디스크를 지원하는 동안 Deployment Planner가 게스트 내부에 있지 않고 vCenter 성능 카운터만 사용하여 프로파일링하는 경우 이러한 디스크 종류는 도구에서 식별할 수 없습니다.
>


##<a name="download"></a>다운로드
최신 버전의 Azure Site Recovery Deployment Planner 공개 미리 보기를 [다운로드](https://aka.ms/asr-deployment-planner)합니다.  이 도구는 zip 형식으로 패키지되어 있습니다.  현재 버전의 도구는 Azure 시나리오를 위해 VMware만 지원합니다.

zip 파일을 도구를 실행하려는 Windows Server에 복사합니다. 네트워크 액세스 권한이 있는 모든 Windows Server 2012 R2에서 도구를 실행하여 프로파일링할 가상 컴퓨터를 보유하고 있는 VMware vCenter 서버 또는 VMware vSphere ESXi 호스트에 연결할 수는 있지만, [구성 서버 크기 조정 지침](https://aka.ms/asr-v2a-on-prem-components)의 하드웨어 구성을 사용하는 서버에서 이 도구를 실행하는 것이 좋습니다.  Azure Site Recovery 구성 요소를 온-프레미스에 이미 배포한 경우 구성 서버에서 도구를 실행해야 합니다. 도구를 실행하는 서버에서 구성 서버(기본 제공 프로세스 서버가 있음)와 동일한 하드웨어 구성을 사용하면 도구에서 보고한 달성된 처리량이 Azure Site Recovery에서 복제하는 동안 달성할 수 있는 실제 처리량과 일치하도록 하는 것이 좋습니다. 처리량 계산은 서버의 사용 가능한 네트워크 대역폭과 하드웨어 구성(CPU, 저장소 등)에 따라 다릅니다. 다른 서버에서 도구를 실행하는 경우 처리량이 해당 서버에서 Microsoft Azure로 계산되고 서버의 하드웨어 구성이 구성 서버와 다를 수 있으므로, 도구에서 보고한 달성된 처리량이 정확하지 않을 수 있습니다.

Zip 폴더의 압축을 풉니다. 그러면 여러 개의 파일과 하위 폴더를 볼 수 있습니다. 실행 파일은 부모 폴더에 있는 ASRDeploymentPlanner.exe입니다.

예: .zip 파일을 E:\ 드라이브에 복사하고 압축을 풉니다.
E:\ASR Deployment Planner-Preview_v1.1.zip

E:\ASR Deployment Planner-Preview_v1.1\ ASR Deployment Planner-Preview_v1.1\ ASRDeploymentPlanner.exe

##<a name="capabilities"></a>기능
명령줄 도구(ASRDeploymentPlanner.exe)는 다음 세 가지 모드 중 하나에서 실행할 수 있습니다.

1.    프로파일링  
2.    보고서 생성
3.    처리량 가져오기

먼저 프로파일링 모드에서 도구를 실행하여 가상 컴퓨터의 데이터 변동 및 IOPS를 수집해야 합니다.  그런 다음 네트워크 대역폭, 저장소 요구 사항을 확인하기 위해 도구를 실행하여 보고서를 생성합니다.

##<a name="profiling"></a>프로파일링
프로파일링 모드에서 Deployment Planner 도구는 vCenter 서버 또는 vSphere ESXi 호스트에 연결하여 가상 컴퓨터에 대한 성능 데이터를 수집합니다.

* 프로파일링은 프로덕션 가상 컴퓨터에 직접 연결되지 않으므로 프로덕션 가상 컴퓨터의 성능에 영향을 주지 않습니다. 모든 성능 데이터는 vCenter 서버/vSphere ESXi 호스트에서 수집됩니다.
* 프로파일링으로 인해 서버에 미치는 영향을 무시할 수 있을 만큼 낮추기 위해 vCenter 서버/vSphere ESXi 호스트는 15분마다 한 번씩 쿼리됩니다. 그러나 이 도구는&1;분마다 성능 카운터 데이터를 저장하기 때문에 프로파일링 정확도를 손상하지 않습니다.

####<a name="create-a-list-of-virtual-machines-to-profile"></a>프로파일링할 가상 컴퓨터 목록 만들기
먼저 프로파일링하려는 가상 컴퓨터 목록이 있어야 합니다. 다음 VMware vSphere PowerCLI 명령을 사용하여 VMware vCenter 또는 VMware vSphere ESXi 호스트에서 모든 가상 컴퓨터 이름을 가져올 수 있습니다. 또는 파일에서 수동으로 프로파일링하려는 가상 컴퓨터의 이름/IP 주소를 나열하면 됩니다.

1.    VMware vSphere PowerCLI를 설치한 가상 컴퓨터에 로그온합니다.
2.    VMware vSphere PowerCLI 콘솔을 엽니다.
3.    스크립트의 실행 정책을 사용하지 않도록 설정되어 있는지 확인합니다. 사용하지 않도록 설정되어 있으면 관리자 모드에서 VMware vSphere PowerCLI 콘솔을 시작하고 다음 명령을 실행하여 실행 정책을 사용하도록 설정합니다.

            Set-ExecutionPolicy –ExecutionPolicy AllSigned

4.    다음 두 명령을 실행하여 VMware vCenter 또는 VMware vSphere ESXi에서 가상 컴퓨터의 모든 이름을 가져와서 .txt 파일로 저장합니다.
&lsaquo;서버 이름&rsaquo;, &lsaquo;사용자 이름&rsaquo;, &lsaquo;암호&rsaquo;, &lsaquo;outputfile.txt&rsaquo;을 입력 내용으로 바꿉니다.

            Connect-VIServer -Server <server name> -User <user name> -Password <password>

            Get-VM |  Select Name | Sort-Object -Property Name >  <outputfile.txt>


5.    [메모장]에서 출력 파일을 엽니다. 프로파일링하려는 모든 가상 컴퓨터의 이름을 한 줄에 하나씩 다른 파일(ProfileVMList.txt라고 함)에 복사합니다. 이 파일은 명령줄 도구의 -VMListFile 매개 변수에 대한 입력으로 사용됩니다

    ![Deployment Planner](./media/site-recovery-deployment-planner/profile-vm-list.png)


####<a name="start-profiling"></a>프로파일링 시작
프로파일링할 VM 목록이 있으면 이제 프로파일링 모드에서 도구를 실행할 수 있습니다. 다음은 프로파일링 모드에서 실행할 도구의 필수 및 선택적 매개 변수의 목록입니다. [](대괄호)로 묶은 매개 변수는 선택적 매개 변수입니다.

ASRDeploymentPlanner.exe -Operation StartProfiling /?

| 매개 변수 이름 | 설명 |
|---|---|
| -Operation |      StartProfiling |
| -Server | 가상 컴퓨터를 프로파일링할 vCenter 서버/ESXi 호스트의 정규화된 도메인 이름 또는 IP 주소입니다.|
| -User | vCenter 서버/ESXi 호스트에 연결할 사용자 이름입니다. 사용자는 적어도 읽기 전용 액세스 권한을 가지고 있어야 합니다.|
| -VMListFile |    프로파일링할 가상 컴퓨터의 목록을 포함하는 파일입니다. 파일 경로는 절대 경로 또는 상대 경로일 수 있습니다. 이 파일에는 가상 컴퓨터 이름/IP 주소가 한 줄에 하나씩 있어야 합니다. 파일에 지정된 가상 컴퓨터 이름은 vCenter 서버 또는 ESXi 호스트의 VM 이름과 동일해야 합니다. <br> 예: "VMList.txt" 파일에는 가상 컴퓨터가 다음과 같이 포함되어 있습니다.<br>virtual_machine_A <br>10.150.29.110<br>virtual_machine_B |
| -NoOfDaysToProfile | 프로파일링을 실행할 일 수입니다. 15일 이상 프로파일링을 실행하여 지정된 기간 동안 사용자 환경에서 워크로드 패턴을 관찰하고 정확한 권장 사항을 제공하는 데 사용하도록 하는 것이 좋습니다. |
| [-Directory] |    프로파일링 중에 생성된 프로파일링 데이터를 저장하기 위한 UNC 또는 로컬 디렉터리 경로입니다. 지정하지 않으면 현재 경로 아래에 'ProfiledData'라는 디렉터리가 기본 디렉터리로 사용됩니다. |
| [-Password ] | vCenter 서버/ESXi 호스트에 연결할 암호입니다. 지금 지정하지 않으면 나중에 명령을 실행할 때 지정하도록 요구하는 메시지가 표시됩니다.|
|  [-StorageAccountName]  | 온-프레미스 환경에서 Azure로의 데이터 복제에서 달성할 수 있는 처리량을 찾기 위해 사용하는 Azure Storage 계정 이름입니다. 도구에서 이 저장소 계정에 테스트 데이터를 업로드하여 처리량을 계산합니다.|
| [-StorageAccountKey] | 저장소 계정에 액세스하는 데 사용되는 Azure Storage 계정 키입니다. Azure Portal> 저장소 계정> [저장소 계정 이름]> 설정> 액세스 키 > Key1(또는 클래식 저장소 계정의 기본 액세스 키)로 차례로 이동합니다. |

가상 컴퓨터를 15~30일 동안 프로파일링하는 것이 좋습니다. ASRDeploymentPlanner.exe는 프로파일링 기간 동안 계속 실행됩니다. 도구에서는 프로파일링 시간을 일 단위로 입력합니다. 도구의 빠른 테스트를 위해 몇 시간 또는 몇 분 동안 프로파일링하려면 공개 미리 보기에서 시간을 동등한 측정 일로 변환해야 합니다.  예를 들어 30분 동안 프로파일링하려면 입력은 30 / (60 * 24) = 0.021일이어야 합니다.  허용되는 최소 프로파일링 시간은 30분입니다.

프로파일링 중에 구성 서버/프로세스 서버에서 Azure로 복제할 때 Azure Site Recovery에서 달성할 수 있는 처리량을 찾기 위해 Azure Storage 계정 이름과 키를 선택적으로 전달할 수 있습니다. Azure Storage 계정 이름과 키가 프로파일링 중에 전달되지 않으면 도구에서 달성할 수 있는 처리량을 계산하지 않습니다.


서로 다른 집합의 가상 컴퓨터에 대해서는 도구의 여러 인스턴스를 실행할 수 있습니다. 가상 컴퓨터 이름이 프로파일링 집합에서 반복되지 않도록 합니다. 예를 들어&10;개의 가상 컴퓨터(VM1 - VM10)를 프로파일링하고 며칠 후에 다른&5;개의 가상 컴퓨터(VM11 - VM15)를 프로파일링하려는 경우, 두 번째 가상 컴퓨터 집합(VM11 - VM15)에 대한 다른 명령줄 콘솔에서 도구를 실행할 수 있습니다 . 그러나 두 번째 가상 컴퓨터 집합에 첫 번째 프로파일링 인스턴스의 가상 컴퓨터 이름이 없어야 하거나 두 번째 실행을 위해 다른 출력 디렉터리를 사용해야 합니다. 도구의 두 인스턴스가 동일한 가상 컴퓨터를 프로파일링하고 동일한 출력 디렉터리를 사용하는 경우 생성된 보고서는 올바르지 않습니다.

가상 컴퓨터 구성은 프로파일링 작업을 시작할 때 한 번 캡처되어 VMDetailList.xml이라는 파일에 저장됩니다. 이 정보는 보고서를 생성할 때 사용됩니다. 프로파일링 시작 시간과 종료 시간 사이에 수행된 VM 구성에 대한 변경 내용(예: 코어 수, 디스크 수, NIC 수 등의 증가)은 캡처되지 않습니다. 프로파일링 과정에서 프로파일링된 가상 컴퓨터 구성이 변경된 상황이 있는 경우 공개 미리 보기에서 보고서를 생성할 때 가상 컴퓨터에 대한 최신 세부 정보를 가져오기 위한 해결 방법은 다음과 같습니다.   

* 'VMdetailList.xml'을 백업하고 현재 위치에서 파일을 삭제합니다.
* 보고서를 생성할 때 -User 및 -Password 인수를 전달합니다.

프로파일링 명령은 프로파일링 디렉터리에 여러 파일을 생성합니다. 어떤 파일도 삭제하지 마세요. 그렇지 않으면 보고서 생성에 영향을 줍니다.

#####<a name="example-1-to-profile-virtual-machines-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>예제 1: 30일 동안 가상 컴퓨터 프로파일링 및 온-프레미스에서 Azure로의 처리량 찾기
ASRDeploymentPlanner.exe **-Operation** StartProfiling -Directory “E:\vCenter1_ProfiledData” **-Server** vCenter1.contoso.com **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  **-NoOfDaysToProfile**  30  **-User** vCenterUser1 **-StorageAccountName**  asrspfarm1 **-StorageAccountKey** Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==

#####<a name="example-2-to-profile-virtual-machines-for-15-days"></a>예제 2: 15일 동안 가상 컴퓨터 프로파일링
ASRDeploymentPlanner.exe **-Operation** StartProfiling **-Directory** “E:\vCenter1_ProfiledData” **-Server** vCenter1.contoso.com **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  **-NoOfDaysToProfile**  15  -User vCenterUser1

#####<a name="example-3-to-profile-virtual-machines-for-1-hour-for-a-quick-test-of-the-tool"></a>예제 3: 도구를 빠르게 테스트하기 위해 1시간 동안 가상 컴퓨터 프로파일링
ASRDeploymentPlanner.exe **-Operation** StartProfiling **-Directory** “E:\vCenter1_ProfiledData” **-Server** vCenter1.contoso.com **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  **-NoOfDaysToProfile**  0.04  **-User** vCenterUser1


>[!NOTE]
>
> * 도구가 실행 중인 서버를 다시 부팅하거나 충돌이 발생하는 경우 또는 Ctrl + C를 사용하여 도구를 종료하는 경우 프로파일링된 데이터가 유지됩니다. 이로 인해 프로파일링된 데이터의 마지막 15분이 누락되는 경우가 있습니다. 서버를 다시 시작한 후에 프로파일링 모드에서 도구를 다시 실행해야 합니다.
>
> * Azure Storage 계정 이름과 키가 전달되면 도구는 프로파일링의 마지막 단계에서 처리량을 측정합니다. 프로파일링을 정상적으로 완료하기 전에 도구가 종료되면 처리량이 계산되지 않습니다. 보고서를 생성하기 전에 언제든지 명령줄 콘솔에서 GetThroughput 작업을 실행하여 처리량을 찾을 수 있습니다. 그렇지 않으면 생성된 보고서에 달성된 처리량 정보가 포함되지 않습니다.
>

##<a name="generate-report"></a>보고서 생성
이 도구는 모든 배포 권장 사항을 요약하는 보고서 출력으로 XLSM(Microsoft Excel 매크로 사용 파일)을 생성합니다. 보고서는 지정된 디렉터리에 DeploymentPlannerReport_<Unique Numeric Identifier>.xlsm이라는 파일 이름으로 저장됩니다.

프로파일링이 완료되면 보고서 생성 모드에서 도구를 실행할 수 있습니다. 다음은 보고서 생성 모드에서 실행할 도구의 필수 및 선택적 매개 변수의 목록입니다. [](대괄호)로 묶은 매개 변수는 선택적 매개 변수입니다.

ASRDeploymentPlanner.exe -Operation GenerateReport /?

|매개 변수 이름 | 설명 |
|-|-|
| -Operation | GenerateReport |
| -Server |  보고서가 생성될 프로파일링된 가상 컴퓨터가 있는 vCenter/vSphere 서버 정규화된 도메인 이름 또는 IP 주소(프로파일링할 때 사용한 것과 동일한 이름 또는 IP 주소 사용)입니다. 프로파일링할 때 vCenter 서버를 사용한 경우 보고서 생성에 vSphere 서버를 사용할 수 없으며, 그 반대의 경우도 마찬가지입니다.|
| -VMListFile | 보고서가 생성될 프로파일링된 가상 컴퓨터의 목록을 포함하는 파일입니다. 파일 경로는 절대 경로 또는 상대 경로일 수 있습니다. 이 파일에는 가상 컴퓨터 이름/IP 주소가 한 줄에 하나씩 있어야 합니다. 파일에 지정된 가상 컴퓨터 이름은 vCenter 서버 또는 ESXi 호스트의 가상 컴퓨터 이름과 동일해야 하며, 프로파일링 시간에 사용된 이름과 일치해야 합니다.|
| [-Directory] | 프로파일링된 데이터(프로파일링 중에 생성된 파일)가 저장되는 UNC 또는 로컬 디렉터리 경로입니다. 이 데이터는 보고서를 생성하는 데 필요합니다. 지정하지 않으면 'ProfiledData' 디렉터리가 사용됩니다. |
| [-GoalToCompleteIR] |    프로파일링된 가상 컴퓨터의 초기 복제를 완료해야 하는 시간의 수입니다. 생성된 보고서는 지정된 시간 내에 초기 복제를 완료할 수 있는 가상 컴퓨터의 수를 제공합니다. 기본값은 72시간입니다. |
| [-User] | vCenter/vSphere 서버에 연결할 사용자 이름입니다. 보고서에 사용할 디스크 수, 코어 수, NIC 수 등과 같은 가상 컴퓨터의 최신 구성 정보를 가져오는 데 사용됩니다. 제공하지 않으면 프로파일링을 시작할 때 수집되는 구성 정보가 사용됩니다. |
| [-Password] | vCenter 서버/ESXi 호스트에 연결할 암호입니다. 매개 변수로 지정하지 않으면 나중에 명령을 실행할 때 지정하도록 요구하는 메시지가 표시됩니다. |
| [-DesiredRPO] | 원하는 RPO(복구 시점 목표)(분)입니다. 기본값은 15분입니다.|
| [-Bandwidth] | 대역폭(Mbps)입니다. 지정된 대역폭에서 달성할 수 있는 RPO를 계산하는 데 사용됩니다. |
| [-StartDate]  | MM-DD-YYYY:HH:MM 형식(24시간 형식)의 시작 날짜 및 시간입니다. 'StartDate'는 'EndDate'와 함께 지정해야 합니다. 지정하면 StartDate와 EndDate 사이에 수집한 프로파일링된 데이터에 대한 보고서를 생성합니다. |
| [-EndDate] | MM-DD-YYYY:HH:MM 형식(24시간 형식)의 종료 날짜 및 시간입니다. 'EndDate'는 'StartDate'와 함께 지정해야 합니다. 지정하면 StartDate와 EndDate 사이에 수집한 프로파일링된 데이터에 대한 보고서를 생성합니다. |
| [-GrowthFactor] |증가율(%)입니다. 기본값은 30%입니다.  |


##### <a name="example-1-to-generate-report-with-default-values-when-profiled-data-is-on-the-local-drive"></a>예제 1: 프로파일링된 데이터가 로컬 드라이브에 있는 경우 기본값으로 보고서 생성
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** “E:\vCenter1_ProfiledData” **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt”


##### <a name="example-2-to-generate-report-when-profiled-data-is-on-a-remote-server-user-should-have-readwrite-access-on-the-remote-directory"></a>예제 2: 프로파일링된 데이터가 원격 서버에 있는 경우 보고서 생성 - 사용자는 원격 디렉터리에서 읽기/쓰기 액세스 권한을 가지고 있어야 합니다.
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** “\\\\PS1-W2K12R2\vCenter1_ProfiledData” **-VMListFile** “\\\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”

##### <a name="example-3-generate-report-with-specific-bandwidth-and-goal-to-complete-ir-within-specified-time"></a>예제 3: 지정된 시간 내에 IR을 완료하기 위한 특정 대역폭 및 목표로 보고서 생성
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** “E:\vCenter1_ProfiledData” **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt” **-Bandwidth** 100 **-GoalToCompleteIR** 24

##### <a name="example-4-generate-report-with-5-growth-factor-instead-of-the-default-30"></a>예제 4: 30%(기본값) 대신 5% 증가율로 보고서 생성
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** “E:\vCenter1_ProfiledData” **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt” **-GrowthFactor** 5

##### <a name="example-5-generate-report-with-a-subset-of-profiled-data-say-you-have-30-days-of-profiled-data-and-want-to-generate-the-report-for-only-20-days"></a>예제 5: 프로파일링된 데이터의 하위 집합으로 보고서 생성 - 30일 동안 프로파일링된 데이터가 있지만 20일 동안만의 보고서를 생성하려고 한다고 가정합니다.
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** “E:\vCenter1_ProfiledData” **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt” **-StartDate**  01-10-2017:12:30 -**EndDate** 01-19-2017:12:30

##### <a name="example-6-generate-report-for-5-minutes-rpo"></a>예제 6: 5분 동안의 RPO 보고서 생성
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** “E:\vCenter1_ProfiledData” **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  **-DesiredRPO** 5

### <a name="percentile-value-used-for-the-calculation"></a>계산에 사용된 백분위 수 값
**프로파일링 중에 수집된 성능 메트릭의 기본 백분위 수 값은 보고서를 생성할 때 사용하나요?**

도구에서는 모든 VM을 프로파일링하는 동안 수집된 읽기/쓰기 IOPS, 쓰기 IOPS 및 데이터 변동의 95번째 백분위 수 값을 기본적으로 사용합니다. 이렇게 하면 하루에 한 번 실행되는 백업 작업, 주기적인 데이터베이스 인덱싱, 분석 보고서 생성 작업 또는 프로파일링 기간 동안 다른 유사한 시점의 짧은 기간 동안 발생하는 이벤트처럼 일시적인 이벤트로 인해 VM에서 볼 수 있는 100번째 백분위 수 스파이크가 사용되지 않도록 하여 대상 Azure Storage 및 원본 대역폭 요구 사항을 결정할 수 있습니다. 95번째 백분위 수 값을 사용하면 실제 워크로드 특성을 정확히 보여 주며, Microsoft Azure에서 이러한 워크로드를 실행할 때 최상의 성능을 제공합니다. 이 백분위 수를 자주 변경하지는 않겠지만, 더 낮추도록 선택하는 경우(예: 90번째 백분위 수) 기본 폴더에서 'ASRDeploymentPlanner.exe.config' 구성 파일을 업데이트하고 저장하여 프로파일링된 기존 데이터에 대해 새 보고서를 생성할 수 있습니다.

        &lsaquo;add key="WriteIOPSPercentile" value="95" /&rsaquo;>      
        &lsaquo;add key="ReadWriteIOPSPercentile" value="95" /&rsaquo;>      
        &lsaquo;add key="DataChurnPercentile" value="95" /&rsaquo;

### <a name="growth-factor"></a>증가율
**배포 계획 중에 증가율을 고려해야 하는 이유는 무엇인가요?**

시간이 지남에 따라 잠재적 사용량이 증가할 것으로 가정할 때 워크로드 특성의 증가를 고려하는 것이 중요합니다. 현재는 워크로드 특성이 변경되는 경우 보호를 해제하고 다시 사용하도록 설정하지 않고는 다른 Azure Storage 계정으로 전환하여 보호할 수 없기 때문입니다. 예: 현재 가상 컴퓨터에서 실행되는 응용 프로그램 사용자의 수가 증가함에 따라&3;개월 이내에는 가상 컴퓨터가 표준 저장소 복제 계정에 적합하지만, VM 변동이 증가하여 Azure Site Recovery 복제가 증가하는 새로운 변동에 계속 대응할 수 있도록 프리미엄 저장소로 이동해야 하는 경우 프리미엄 저장소 계정에 대한 보호를 해제했다가 다시 사용하도록 설정해야 합니다. 따라서 배포를 계획하고 기본값이 30%인 동안에 증가를 계획하는 것이 좋습니다. 최적의 응용 프로그램 사용 패턴과 증가 예측을 알고 있어야 하며, 보고서를 생성하는 동안 이 숫자를 적절하게 변경할 수 있습니다. 실제로 동일한 프로파일링된 데이터를 사용하여 서로 다른 증가율로 여러 보고서를 생성하여 가장 적합한 대상 Azure Storage 및 원본 대역폭 권장 사항을 확인할 수 있습니다.

생성된 Microsoft Excel 보고서에 포함되는 시트는 다음과 같습니다.

* [입력](site-recovery-deployment-planner.md#input)
* [권장 사항](site-recovery-deployment-planner.md#recommendations-with-desired-rpo-as-input)
* [권장 사항 - 대역폭 입력](site-recovery-deployment-planner.md#recommendations-with-available-bandwidth-as-input)
* [VM<->저장소 배치](site-recovery-deployment-planner.md#vm-storage-placement)
* [호환되는 VM](site-recovery-deployment-planner.md#compatible-vms)
* [호환되지 않는 VM](site-recovery-deployment-planner.md#incompatible-vms)

![Deployment Planner](./media/site-recovery-deployment-planner/dp-report.png)


##<a name="get-throughput"></a>처리량 가져오기
Azure Site Recovery에서 복제 중에 온-프레미스 환경에서 Azure로 달성할 수 있는 처리량을 추정하려면 GetThroughput 모드에서 도구를 실행합니다. 처리량은 이 도구를 실행 중인 서버(이상적으로는 구성 서버 크기 조정 가이드에 따른 서버)에서 계산됩니다.  Azure Site Recovery 인프라 구성 요소를 온-프레미스에 이미 배포한 경우 구성 서버에서 도구를 실행합니다.

명령줄 콘솔을 열고 ASR 배포 계획 도구 폴더로 이동합니다.  다음 매개 변수를 사용하여 ASRDeploymentPlanner.exe를 실행합니다. [](대괄호)로 묶은 매개 변수는 선택적 매개 변수입니다.

ASRDeploymentPlanner.exe -Operation GetThroughput /?

|매개 변수 이름 | 설명 |
|-|-|
| -operation | GetThroughput |
| [-Directory] | 프로파일링된 데이터(프로파일링 중에 생성된 파일)가 저장되는 UNC 또는 로컬 디렉터리 경로입니다. 이 데이터는 보고서를 생성하는 데 필요합니다. 지정하지 않으면 'ProfiledData' 디렉터리가 사용됩니다.  |
| -StorageAccountName | 온-프레미스 환경에서 Azure로의 데이터 복제에서 사용되는 대역폭을 찾기 위해 사용하는 Azure Storage 계정 이름입니다. 도구에서 이 저장소 계정에 테스트 데이터를 업로드하여 사용되는 대역폭을 찾습니다. |
| -StorageAccountKey | 저장소 계정에 액세스하는 데 사용되는 Azure Storage 계정 키입니다. Azure Portal> 저장소 계정> [저장소 계정 이름]> 설정> 액세스 키 > Key1(또는 클래식 저장소 계정의 기본 액세스 키)로 차례로 이동합니다. |
| -VMListFile | 사용되는 대역폭을 계산하기 위해 프로파일링할 가상 컴퓨터의 목록을 포함하는 파일입니다. 파일 경로는 절대 경로 또는 상대 경로일 수 있습니다. 이 파일에는 가상 컴퓨터 이름/IP 주소가 한 줄에 하나씩 있어야 합니다. 파일에 지정된 가상 컴퓨터 이름은 vCenter 서버 또는 ESXi 호스트의 가상 컴퓨터 이름과 동일해야 합니다.<br>예: "VMList.txt" 파일에는 가상 컴퓨터가 다음과 같이 포함되어 있습니다.<br>virtual machine_A <br>10.150.29.110<br>virtual machine_B|

이 도구는 지정된 디렉터리에 여러 개의 'asrvhdfile<#>.vhd'(여기서 #은 숫자) 64MB 파일을 만듭니다.  Azure Storage 계정에 이러한 파일을 업로드하여 처리량을 찾습니다. 일단 처리량이 측정되면 Azure Storage 계정과 로컬 서버에서 이러한 파일을 모두 삭제합니다. 처리량을 계산하는 중에 어떤 이유로든 도구가 종료되는 경우 Azure Storage 또는 로컬 서버에서 파일을 삭제하지 않으며, 수동으로 삭제해야 합니다.

처리량은 특정 시점에서 측정되며, 다른 모든 요소가 동일하게 유지된다는 조건 하에 복제 중에 Azure Site Recovery에서 달성할 수 있는 최대 처리량입니다. 예를 들어 응용 프로그램이 동일한 네트워크에서 더 많은 대역폭을 사용하기 시작하면 복제 중에 실제 처리량이 달라집니다. 구성 서버에서 GetThroughput 명령을 실행하는 경우 이 도구는 보호된 가상 컴퓨터와 진행 중인 복제를 인식하지 못합니다. 보호된 가상 컴퓨터의 데이터 변동이 심한 경우와 약한 경우에 GetThroughput 작업을 실행하는 경우 측정된 처리량 결과는 다릅니다.  프로파일링 중에 여러 시점에서 도구를 실행하여 다양한 시간에서 어떤 처리량을 달성할 수 있는지 이해하는 것이 좋습니다. 보고서에는 도구에서 마지막으로 측정한 처리량이 표시됩니다.


##### <a name="example"></a>예
ASRDeploymentPlanner.exe **-Operation** GetThroughput **-Directory**  E:\vCenter1_ProfiledData **-VMListFile** E:\vCenter1_ProfiledData\ProfileVMList1.txt  **-StorageAccountName**  asrspfarm1 **-StorageAccountKey** by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==

>[!NOTE]
>
> * 구성 서버와 동일한 저장소 및 CPU 특성을 포함한 서버에서 도구를 실행합니다.
>
> * 복제의 경우 RPO 시간 100%를 충족하는 데 권장되는 대역폭을 프로비전합니다. 올바른 대역폭을 프로비전한 후에도 도구에서 보고한 달성된 처리량이 증가하지 않으면 다음 사항을 확인합니다.
>
> a. Azure Site Recovery 처리량을 제한하고 있는 네트워크 QoS(서비스 품질)가 있는지 확인합니다.
>
> b. 네트워크 대기 시간을 최소화하기 위해 Azure Site Recovery 자격 증명 모음이 물리적으로 지원되는 가장 가까운 Microsoft Azure 지역에 있는지 확인합니다
>
> c. 로컬 저장소 특성을 확인하고 하드웨어(SSD 등의 HDD)를 향상시킵니다.
>
> d. 프로세스 서버의 Azure Site Recovery 설정을 변경하여 [복제에 사용되는 네트워크 대역폭의 양을 늘립니다](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).
>

##<a name="recommendations-with-desired-rpo-as-input"></a>입력으로 원하는 RPO를 사용하는 권장 사항

###<a name="profiled-data"></a>프로파일링된 데이터

![Deployment Planner](./media/site-recovery-deployment-planner/profiled-data-period.png)

**프로파일링된 데이터 기간**은 프로파일링을 실행한 기간입니다. 기본적으로 보고서 생성 중에 StartDate 및 EndDate 옵션을 사용하여 특정 기간 동안 보고서를 생성하지 않는 한 도구에서는 계산을 위해 프로파일링된 모든 데이터를 사용합니다.

**서버 이름**은 가상 컴퓨터의 보고서가 생성된 VMware vCenter 또는 ESXi 호스트의 이름 또는 IP 주소입니다.

**원하는 RPO**는 배포를 위한 RPO(복구 지점 목표) 목표입니다. 기본적으로 필요한 네트워크 대역폭은 15, 30 및 60분의 RPO 값에 대해 계산됩니다. 선택에 따라 영향을 받은 값이 시트에서 업데이트됩니다. 보고서를 생성하는 동안 DesiredRPOinMin 매개 변수를 사용한 경우 이 값은 다음과 같이 원하는 RPO 드롭다운에 표시됩니다.

###<a name="profiling-overview"></a>프로파일링 개요

![Deployment Planner](./media/site-recovery-deployment-planner/profiling-overview.png)

**총 프로파일링된 가상 컴퓨터**는 프로파일링된 데이터를 사용할 수 있는 가상 컴퓨터의 총 수입니다. VMListFile에 프로파일링되지 않은 가상 컴퓨터의 이름이 있는 경우 해당 가상 컴퓨터는 보고서 생성에서 고려되지 않고 프로파일링된 가상 컴퓨터의 총 수에서 제외됩니다.

**호환되는 가상 컴퓨터**는 Azure Site Recovery를 사용하여 Azure로 보호할 수 있는 가상 컴퓨터의 수입니다. 필요한 네트워크 대역폭, Azure Storage 계정 수, Microsoft Azure 코어 수, 구성 서버 수 및 추가 프로세스 서버 수를 계산하는 데 호환되는 가상 컴퓨터의 총 수입니다. 호환되는 모든 가상 컴퓨터의 세부 정보는 보고서의 호환되는 VM 시트에서 제공합니다.

**호환되지 않는 가상 컴퓨터**는 Azure Site Recovery로 보호하는 데 호환되지 않는 프로파일링된 가상 컴퓨터의 수입니다. 비호환성에 대한 이유는 아래의 '호환되지 않는 VM' 섹션에서 설명하고 있습니다. VMListFile에 프로파일링되지 않은 가상 컴퓨터의 이름이 있는 경우 해당 가상 컴퓨터는 호환되지 않는 가상 컴퓨터 수에서 제외됩니다. 이러한 가상 컴퓨터는 호환되지 않는 VM 시트의 끝에 '데이터를 찾을 수 없음'으로 나열됩니다.

**원하는 RPO**는 분 단위의 원하는 RPO입니다. 15분, 30분 및 60분의 3개 RPO 값에 대한 보고서가 생성되며, 기본값은 15분입니다. 보고서의 대역폭 권장 사항은 시트의 오른쪽 위에 있는 원하는 RPO 드롭다운의 선택에 따라 변경됩니다. 사용자 지정 값을 포함한 "-DesiredRPO" 매개 변수를 사용하여 보고서를 생성한 경우 이 사용자 지정 값은 원하는 RPO 드롭다운에 기본값으로 표시됩니다.

###<a name="required-network-bandwidth-mbps"></a>필요한 네트워크 대역폭(Mbps)

![Deployment Planner](./media/site-recovery-deployment-planner/required-network-bandwidth.png)

**RPO 시간 100% 충족**: 원하는 RPO 시간을 100% 충족하는 데 할당되는 권장 대역폭(Mbps)입니다. 이 양의 대역폭은 RPO 위반을 방지하는 데 호환되는 모든 가상 컴퓨터의 안정적인 상태 델타 복제 전용이어야 합니다.

**RPO 시간 90% 충족**: 광대역 가격 또는 다른 이유로 인해 원하는 RPO 시간 100%를 충족시키는 데 필요한 대역폭을 프로비전할 수 없는 경우 원하는 RPO 시간 90%를 충족할 수 있는 더 낮은 대역폭 양을 프로비전하도록 선택할 수 있습니다. 이 낮은 대역폭을 프로비전하는 의미를 이해하기 위해 보고서에서 예상되는 RPO 위반 횟수 및 기간에 대한 가상(what-if) 분석을 제공합니다.

**달성된 처리량**: GetThroughput 명령을 실행한 서버에서 Azure Storage 계정이 있는 Microsoft Azure 지역으로의 처리량입니다. 구성 서버/프로세스 서버 저장소 및 네트워크 특성이 도구를 실행한 서버의 해당 항목과 동일하게 유지된다는 조건 하에 Azure Site Recovery를 사용하여 호환되는 가상 컴퓨터를 보호할 때 달성할 수 있는 개략적인 처리량을 나타냅니다.    

복제의 경우 RPO 시간 100%를 충족하는 데 권장되는 대역폭을 프로비전해야 합니다. 올바른 대역폭을 프로비전한 후에도 도구에서 보고한 달성된 처리량이 증가하지 않으면 다음 사항을 확인합니다.

a.    Azure Site Recovery 처리량을 제한하고 있는 네트워크 QoS(서비스 품질)가 있는지 확인합니다.

b.    네트워크 대기 시간을 최소화하기 위해 Azure Site Recovery 자격 증명 모음이 물리적으로 지원되는 가장 가까운 Microsoft Azure 지역에 있는지 확인합니다

c.    로컬 저장소 특성을 확인하고 하드웨어(SSD 등의 HDD)를 향상시킵니다.

d. 프로세스 서버의 Azure Site Recovery 설정을 변경하여 [복제에 사용되는 네트워크 대역폭의 양을 늘립니다](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

이미 보호된 가상 컴퓨터가 있는 구성 서버/프로세스 서버에서 이 도구를 실행하는 경우 해당 특정 시점에서 처리되는 변동량에 따라 달성된 처리량 수가 변경되기 때문에 도구를 몇 번 실행합니다.

모든 엔터프라이즈 Azure Site Recovery 배포의 경우 [ExpressRoute](https://aka.ms/expressroute)를 사용하는 것이 좋습니다.

###<a name="required-azure-storage-accounts"></a>필요한 Azure Storage 계정
이 차트에서는 호환되는 모든 가상 컴퓨터를 보호하는 데 필요한 Azure Storage 계정(표준 및 프리미엄)의 총 수를 보여 줍니다.  [권장되는 VM 배치 계획](site-recovery-deployment-planner.md#vm-storage-placement)을 클릭하여 각 가상 컴퓨터에 사용해야 하는 저장소 계정에 대해 알아봅니다.  

![Deployment Planner](./media/site-recovery-deployment-planner/required-azure-storage-accounts.png)

###<a name="required-number-of-azure-cores"></a>필요한 Azure 코어 수
호환되는 모든 가상 컴퓨터의 장애 조치 또는 테스트 장애 조치 이전에 프로비전하는 코어의 총 수입니다. 충분한 코어를 구독에서 사용할 수 없는 경우 Azure Site Recovery에서 테스트 장애 조치 또는 장애 조치 시 가상 컴퓨터를 만들지 못합니다.

![Deployment Planner](./media/site-recovery-deployment-planner/required-number-of-azure-cores.png)

###<a name="required-on-premises-infrastructure"></a>필요한 온-프레미스 인프라
호환되는 모든 가상 컴퓨터를 보호하도록 구성하는 구성 서버 및 추가 프로세스 서버의 총 수입니다. 구성 서버 또는 추가 프로세스 서버 중 어느 것에서 먼저 적중되든지 간에 가장 큰 구성에서 지원되는 [제한](https://aka.ms/asr-v2a-on-prem-components), 즉 일일 변동량 또는 보호된 대상 가상 컴퓨터의 최대 수(가상 컴퓨터당 평균&3;개의 디스크를 가정)에 따라 도구에서 추가 서버를 권장합니다. 일일 총 변동량 및 보호된 디스크의 총 수에 대한 세부 정보는 [입력](site-recovery-deployment-planner.md#input) 시트에 있습니다.

![Deployment Planner](./media/site-recovery-deployment-planner/required-on-premises-infrastructure.png)

###<a name="what-if-analysis"></a>가상 분석
이 분석은 원하는 RPO 시간 90%만 충족하도록 낮은 대역폭을 프로비전할 때 프로파일링 기간 동안 발생할 수 있는 위반의 수를 설명합니다. 지정된 날에 RPO 위반이 하나 이상 발생할 수 있으며, 그래프에서는 당일의 최고 RPO를 보여 줍니다.
이 분석에 따라 일일 RPO 위반 횟수와 일일 RPO 최고 적중 횟수를 지정된 낮은 대역폭으로 허용할 수 있는지 결정할 수 있습니다. 허용되는 경우 복제를 위해 더 낮은 대역폭을 할당할 수 있으며, 그렇지 않고 원하는 RPO 시간 100%를 충족하려면 제안된 대로 더 높은 대역폭을 할당합니다.

![Deployment Planner](./media/site-recovery-deployment-planner/what-if-analysis.png)

###<a name="recommended-vm-batch-size-for-initial-replication"></a>초기 복제에 권장되는 VM 일괄 처리 크기
이 섹션에서는 프로비전되는 원하는 RPO 시간 100%를 충족하도록 제안된 대역폭으로 72시간(구성 가능한 값 - 보고서 생성 시 GoalToCompleteIR 매개 변수로 변경) 내에 초기 복제를 완료하기 위해 병렬로 보호할 수 있는 가상 컴퓨터의 수를 권장합니다.  그래프에서는 호환되는 모든 가상 컴퓨터에서 검색된 평균적인 가상 컴퓨터 크기에 따라 72시간 내에 초기 복제를 완료하기 위한 대역폭 값 범위와 계산된 가상 컴퓨터 일괄 처리 크기를 보여 줍니다.  

공개 미리 보기에서 보고서는 일괄 처리에 포함되어야 하는 가상 컴퓨터를 지정하지 않습니다. 호환되는 VM 시트에 표시된 디스크 크기를 사용하여 각 가상 컴퓨터의 크기를 찾고, 일괄 처리를 위한 가상 컴퓨터를 선택하거나 알려진 워크로드 특성을 기반으로 하여 선택할 수 있습니다.  초기 복제 완료 시간은 실제 가상 컴퓨터 디스크 크기, 사용된 디스크 공간 크기 및 사용 가능한 네트워크 처리량에 따라 비례적으로 변경됩니다.

![Deployment Planner](./media/site-recovery-deployment-planner/recommended-vm-batch-size.png)

###<a name="growth-factor-and-percentile-values-used"></a>사용된 증가율 및 백분위 수 값
시트 아래쪽의 이 섹션에는 프로파일링된 가상 컴퓨터의 모든 성능 카운터에 사용된 백분위 수 값(기본값: 95번째 백분위 수)과 모든 계산에 사용된 증가율(기본값: 30%)이 표시됩니다.

![Deployment Planner](./media/site-recovery-deployment-planner/max-iops-and-data-churn-setting.png)

##<a name="recommendations-with-available-bandwidth-as-input"></a>입력으로 사용 가능한 대역폭을 사용하는 권장 사항

![Deployment Planner](./media/site-recovery-deployment-planner/profiling-overview-bandwidth-input.png)

Azure Site Recovery 복제를 위해 xMbps 이상의 대역폭을 프로비전할 수 없다는 것을 알고 있는 상황이 있을 수 있습니다. 이 도구를 사용하면 사용 가능한 대역폭을 입력하고(보고서 생성 중에 -Bandwidth 매개 변수 사용), 달성 가능한 RPO(분)를 가져올 수 있습니다. 이처럼 달성 가능한 RPO 값을 사용하여 추가 대역폭을 프로비전해야 하는지 또는 이 RPO로 재해 복구 솔루션을 사용하는 것이 좋은지 여부를 결정할 수 있습니다.

![Deployment Planner](./media/site-recovery-deployment-planner/achievable-rpos.png)

##<a name="input"></a>입력
입력 페이지에서는 프로파일링된 VMware 환경에 대한 개요를 제공합니다.

![Deployment Planner](./media/site-recovery-deployment-planner/Input.png)

**시작 날짜 및 종료 날짜**: 보고서를 생성하도록 고려된 데이터 프로파일링의 시작 날짜와 종료 날짜입니다. 기본적으로 시작 날짜는 프로파일링을 시작한 날짜이고, 종료 날짜는 프로파일링을 중지한 날짜입니다.  'StartDate' 및 'EndDate' 매개 변수를 사용하여 보고서를 생성하는 경우 이는 해당 매개 변수의 값일 수 있습니다. 시작 날짜 및 종료 날짜: 보고서를 생성하도록 고려된 데이터 프로파일링의 시작 날짜와 종료 날짜입니다. 기본적으로 시작 날짜는 프로파일링을 시작한 날짜이고, 종료 날짜는 프로파일링을 중지한 날짜입니다.  'StartDate' 및 'EndDate' 매개 변수를 사용하여 보고서를 생성하는 경우 이는 해당 매개 변수의 값일 수 있습니다.

**총 프로파일링 일 수**: 보고서를 생성한 시작 날짜와 종료 날짜 사이에 프로파일링한 총 날짜 수입니다. 총 프로파일링 일 수는 보고서를 생성한 시작 날짜와 종료 날짜 사이에 프로파일링한 총 날짜 수입니다.

**호환되는 가상 컴퓨터 수**: 필요한 네트워크 대역폭, 필요한 수의 Azure Storage 계정, Microsoft Azure 코어, 구성 서버 및 추가 프로세스 서버를 계산하는 데 호환되는 가상 컴퓨터의 총 수입니다.
호환되는 모든 가상 컴퓨터의 총 디스크 수는 호환되는 모든 가상 컴퓨터의 총 디스크 수입니다. 이 숫자는 배포에서 사용되는 구성 서버 및 추가 프로세스 서버의 수를 결정하기 위한 입력 중 하나로 사용됩니다.

**호환되는 가상 컴퓨터당 평균 디스크 수**: 호환되는 모든 가상 컴퓨터에서 계산된 평균 디스크 수입니다.

**평균 디스크 크기(GB)**: 호환되는 모든 가상 컴퓨터에서 계산된 평균 디스크 크기입니다.

**원하는 RPO(분)**: 기본 RPO이거나 필요한 대역폭을 추정하기 위해 보고서 생성 시 'DesiredRPO' 매개 변수에 전달한 값입니다.

**원하는 대역폭(Mbps)**: 달성 가능한 RPO를 추정하기 위해 보고서 생성 시 'Bandwidth' 매개 변수에 대해 전달한 값입니다.

**관찰된 일반적 일일 데이터 변동량(GB)**: 모든 프로파일링 일 동안 관찰된 평균 데이터 변동량입니다. 이 숫자는 배포에서 사용되는 구성 서버 및 추가 프로세스 서버의 수를 결정하기 위한 입력 중 하나로 사용됩니다.


##<a name="vm-storage-placement"></a>VM-저장소 배치

![Deployment Planner](./media/site-recovery-deployment-planner/vm-storage-placement.png)

**디스크 저장 유형**: '배치할 VM' 열에 언급된 해당 가상 컴퓨터를 모두 복제하는 데 사용되는 '표준' 또는 '프리미엄' Azure Storage 계정입니다.

**제안된 접두사**: Azure Storage 계정 이름을 지정하는 데 사용할 수 있는&3;자로 구성된 접두사입니다. 고유한 접두사를 항상 사용할 수 있지만, 도구에서는 [Azure Storage 계정의 파티션 명명 규칙](https://aka.ms/storage-performance-checklist)을 따르도록 제안합니다.

**제안된 계정 이름**: 제안된 접두사를 포함한 후 Azure Storage 계정 이름이 표시되는 방법을 나타냅니다. <>(각괄호)로 묶은 이름을 사용자 지정 입력으로 바꿉니다.

**로그 저장소 계정**: 모든 복제 로그는 표준 Azure Storage 계정에 저장됩니다. 프리미엄 Azure Storage 계정에 복제하는 가상 컴퓨터의 경우 추가 표준 Azure Storage 계정을 로그 저장소에 프로비전해야 합니다. 여러 프리미엄 복제 저장소 계정에서 단일 표준 로그 저장소 계정을 사용할 수 있습니다. 표준 저장소 계정에 복제된 가상 컴퓨터는 로그에 대해 동일한 저장소 계정을 사용합니다.

**제안된 로그 계정 이름**: 제안된 접두사를 포함한 후 로그 Azure Storage 계정 이름이 표시되는 방법을 나타냅니다. <>(각괄호)로 묶은 이름을 사용자 지정 입력으로 바꿉니다.

**배치 요약**: 복제 및 테스트 장애 조치/장애 조치 시 Azure Storage 계정에 로드된 가상 컴퓨터 전체에 대한 요약을 제공합니다. Azure Storage 계정에 매핑된 총 가상 컴퓨터 수, 이 Azure Storage 계정에 배치되는 모든 가상 컴퓨터의 총 읽기/쓰기 IOPS, 총 쓰기(복제) IOPS, 모든 디스크에 프로비전된 총 크기 및 총 디스크 수를 포함합니다.

**배치할 가상 컴퓨터**: 최적의 성능과 활용을 위해 지정된 Azure Storage 계정에 배치해야 하는 가상 컴퓨터를 모두 나열합니다.

## <a name="compatible-vms"></a>호환되는 VM
![Deployment Planner](./media/site-recovery-deployment-planner/compatible-vms.png)

**VM 이름**: 보고서 생성 시 VMListFile에서 사용되는 가상 컴퓨터 이름 또는 IP 주소입니다. 또한 이 열에서는 가상 컴퓨터에 연결된 디스크(VMDK)를 나열합니다. 중복된 이름 또는 IP 주소가 있는 vCenter의 가상 컴퓨터는 각 가상 컴퓨터를 구분하기 위해 ESXi 호스트 이름으로 언급됩니다. 나열된 ESXi 호스트는 프로파일링 기간 동안 도구에서 호스트를 처음 발견한 가상 컴퓨터가 배치된 호스트입니다.

**VM 호환성**: 예/아니요의 두 가지 값이 있습니다*. 예*는 가상 컴퓨터가 P20 또는 P30 범주의 프로파일링된 높은 변동/IOPS 디스크 맞춤을 사용하는 [프리미엄 Azure Storage](https://aka.ms/premium-storage-workload)에 적합한 경우를 위한 것이지만, 디스크의 크기로 인해 P10 또는 P20에 매핑됩니다. Azure Storage는 디스크 크기에 따라 디스크를 매핑할 프리미엄 저장소 디스크 유형을 결정합니다. 예를 들어 128GB 미만은 P10이고, 128-512GB는 P20이며, 512-1,023GB는 P30입니다. 따라서 디스크의 워크로드 특성에서 P20 또는 P30에 배치하지만 더 작은 프리미엄 저장소 디스크 유형으로 매핑되는 경우, 도구는 해당 가상 컴퓨터를 예*로 표시하고 원본 디스크 크기를 변경하여 권장되는 프리미엄 저장소 디스크 유형으로 제대로 맞추거나 장애 조치 후 대상 디스크 유형을 변경하도록 권장합니다.
저장소 유형은 표준 또는 프리미엄입니다.

**제안된 접두사**:&3;자로 구성된 Azure Storage 계정 접두사입니다.

**저장소 계정**: 제안된 접두사를 사용하는 이름입니다.

**읽기/쓰기 IOPS(증가율 포함)**: 향후 증가율(기본값: 30%)을 포함한 디스크의 최고 워크로드 IOPS(기본값: 95번째 백분위 수)입니다. 가상 컴퓨터의 최고 읽기/쓰기 IOPS가 프로파일링 기간의 매분마다 개별 디스크 읽기/쓰기 IOPS 합계의 최고값이기 때문에 가상 컴퓨터의 총 읽기/쓰기 IOPS가 항상 가상 컴퓨터에 속한 개별 디스크의 읽기/쓰기 IOPS 합계가 되는 것은 아닙니다.

**데이터 변동률(Mbps)(증가율 포함)**: 향후 증가율(기본값: 30%)을 포함한 디스크의 최고 변동률(기본값: 95번째 백분위 수)입니다. 가상 컴퓨터의 총 데이터 변동은 프로파일링 기간의 매분마다 개별 디스크 변동 합계의 최고값이기 때문에 가상 컴퓨터의 총 데이터 변동이 항상 가상 컴퓨터에 속한 개별 디스크의 데이터 변동 합계가 되는 것은 아닙니다.

**Azure VM 크기**: 이 온-프레미스 가상 컴퓨터에 대해 이상적으로 매핑된 Azure 계산 가상 컴퓨터 크기입니다. 이 매핑은 온-프레미스 가상 컴퓨터의 메모리, 디스크/코어/NIC 수 및 읽기/쓰기 IOPS에 따라 수행됩니다. 항상 이러한 모든 온-프레미스 가상 컴퓨터 특성과 일치하는 가장 작은 Azure 가상 컴퓨터 크기를 사용하는 것이 좋습니다.

**디스크 수**: 가상 컴퓨터의 총 디스크 수(VMDK)입니다.

**디스크 크기(GB)**: 가상 컴퓨터에 속한 모든 디스크의 프로비전된 총 크기입니다. 또한 도구에서는 가상 컴퓨터의 개별 디스크에 대한 디스크 크기도 보여 줍니다.

**코어**: 가상 컴퓨터의 CPU 코어 수입니다.

**메모리(MB)**: 가상 컴퓨터의 RAM입니다.

**NIC**: 가상 컴퓨터의 NIC 수입니다.

##<a name="incompatible-vms"></a>호환되지 않는 VM

![Deployment Planner](./media/site-recovery-deployment-planner/incompatible-vms.png)

**VM 이름**: 보고서 생성 시 VMListFile에서 사용되는 가상 컴퓨터 이름 또는 IP 주소입니다. 또한 이 열에서는 가상 컴퓨터에 연결된 디스크(VMDK)를 나열합니다. 중복된 이름 또는 IP 주소가 있는 vCenter의 가상 컴퓨터는 각 가상 컴퓨터를 구분하기 위해 ESXi 호스트 이름으로 언급됩니다. 나열된 ESXi 호스트는 프로파일링 기간 동안 도구에서 호스트를 처음 발견한 가상 컴퓨터가 배치된 호스트입니다.

**VM 호환성**: 지정된 가상 컴퓨터를 Azure Site Recovery에서 사용할 수 없는 이유를 나타냅니다. 이유는 가상 컴퓨터의 호환되지 않는 디스크별로 요약되며, 게시된 Azure Storage [제한](https://aka.ms/azure-storage-scalbility-performance)에 따라 다음 이유 중 하나일 수 있습니다.

* 디스크 크기가 1,023GB보다 큽니다. - Azure Storage에서는 현재 1TB 이상의 디스크 크기를 지원하지 않습니다.
* 총 VM 크기(복제 + TFO)가 지원되는 Azure Storage 계정 크기 제한(35TB)을 초과합니다 - 이는 일반적으로 가상 컴퓨터를 프리미엄 저장소 영역으로 푸시하는 표준 저장소에 대해 지원되는 최대 Microsoft Azure/Azure Site Recovery 제한을 초과하는 일부 성능 특성이 있는 단일 디스크가 가상 컴퓨터에 있는 경우에 발생합니다. 그러나 프리미엄 Azure Storage 계정의 최대 지원 크기는 35TB이며, 보호된 단일 가상 컴퓨터를 여러 저장소 계정에서 보호할 수 없습니다. 또한 TFO(테스트 장애 조치)가 보호된 가상 컴퓨터에서 실행될 때 복제가 진행 중인 동일한 저장소 계정에서 실행되므로, 복제를 진행하기 위해 디스크 크기의 2배를 프로비전하고 장애 조치가 병렬로 성공하는지 테스트해야 합니다.
* 원본 IOPS가 디스크당 지원되는 Azure Storage IOPS 제한(5,000)을 초과합니다.
* 원본 IOPS가 VM당 지원되는 Azure Storage IOPS 제한(80,000)을 초과합니다.
* 평균 데이터 변동률이 디스크의 평균 IO 크기에 대해 지원되는 Azure Site Recovery 데이터 변동률 제한(10Mbps)을 초과합니다.
* VM의 모든 디스크의 총 데이터 변동률이 지원되는 최대 Azure Site Recovery 데이터 변동률 제한(54Mbps)을 초과합니다.
* 평균 유효 쓰기 IOPS가 디스크에 대해 지원되는 Azure Site Recovery IOPS 제한(840)을 초과합니다.
* 계산된 스냅숏 저장소가 지원되는 스냅숏 저장소 제한(10TB)을 초과합니다.

**읽기/쓰기 IOPS(증가율 포함)**: 향후 증가율(기본값: 30%)을 포함한 디스크의 최고 워크로드 IOPS(기본값: 95번째 백분위 수)입니다. 가상 컴퓨터의 최고 읽기/쓰기 IOPS가 프로파일링 기간의 매분마다 개별 디스크 읽기/쓰기 IOPS 합계의 최고값이기 때문에 가상 컴퓨터의 총 읽기/쓰기 IOPS가 항상 가상 컴퓨터에 속한 개별 디스크의 읽기/쓰기 IOPS 합계가 되는 것은 아닙니다.

**데이터 변동률(Mbps)(증가율 포함)**: 향후 증가율(기본값: 30%)을 포함한 디스크의 최고 변동률(기본값: 95번째 백분위 수)입니다. 가상 컴퓨터의 총 데이터 변동은 프로파일링 기간의 매분마다 개별 디스크 변동 합계의 최고값이기 때문에 가상 컴퓨터의 총 데이터 변동이 항상 가상 컴퓨터에 속한 개별 디스크의 데이터 변동 합계가 되는 것은 아닙니다.

**디스크 수**: 가상 컴퓨터의 총 디스크 수(VMDK)입니다.

**디스크 크기(GB)**: 가상 컴퓨터에 속한 모든 디스크의 프로비전된 총 크기입니다. 또한 도구에서는 가상 컴퓨터의 개별 디스크에 대한 디스크 크기도 보여 줍니다.

**코어**: 가상 컴퓨터의 CPU 코어 수입니다.

**메모리(MB)**: 가상 컴퓨터의 RAM입니다.

**NIC**: 가상 컴퓨터의 NIC 수입니다.


##<a name="azure-site-recovery-limits"></a>Azure Site Recovery 제한

**복제 저장소 대상** | **평균 원본 디스크 I/O 크기** |**평균 원본 디스크 데이터 변동률** | **일일 총 원본 디스크 데이터 변동량**
---|---|---|---
Standard Storage | 8KB    | 2MB/초 | 디스크당&168;GB
프리미엄 P10 디스크 | 8KB    | 2MB/초 | 디스크당&168;GB
프리미엄 P10 디스크 | 16KB | 4MB/초 |    디스크당&336;GB
프리미엄 P10 디스크 | 32KB 이상 | 8MB/초 | 디스크당&672;GB
프리미엄 P20/P30 디스크 | 8KB    | 5MB/초 | 디스크당&421;GB
프리미엄 P20/P30 디스크 | 16KB 이상 |10MB/초    | 디스크당&842;GB


여기서는 IO가 30% 겹치고 있다고 가정하는 평균 숫자입니다. Azure Site Recovery는 중첩 비율, 더 큰 쓰기 크기 및 실제 워크로드 I/O 동작에 따라 더 높은 처리량을 다룰 수 있습니다. 위의 숫자는 5분 이하의 일반적인 백로그를 가정하고 있습니다. 즉 업로드된 데이터는 처리하고 복구 지점은 5분 이내에 만듭니다.

위에 게시된 제한은 테스트를 기반으로 하지만 모든 가능한 응용 프로그램 I/O 조합을 다룰 수는 없습니다. 실제 결과는 응용 프로그램 I/O 조합에 따라 달라집니다. 최상의 결과를 얻으려면 배포를 계획한 후에도 항상 테스트 장애 조치를 통해 광범위한 응용 프로그램 테스트를 수행하여 진정한 성능 상황을 이해하는 것이 좋습니다.

## <a name="how-to-update-the-deployment-planner"></a>Deployment Planner를 업데이트하려면 어떻게 하나요?
최신 버전의 Azure Site Recovery Deployment Planner를 [다운로드](site-recovery-deployment-planner.md#download)합니다. zip 파일을 실행하려는 서버에 복사합니다. zip 파일의 압축을 풉니다.
이전 버전의 Deployment Planner가 이미 있고 프로파일링 중인 경우 새 버전에 프로파일링 수정이 없다면 프로파일링을 중지하지 않아도 됩니다. 릴리스에 프로파일링 구성 요소의 수정 사항이 포함된 경우 이전 버전을 사용하는 프로파일링을 중지하고 새 버전을 사용하여 프로파일링을 다시 시작하는 것이 좋습니다. 새 버전을 사용하여 프로파일링을 시작하는 경우 동일한 출력 디렉터리 경로를 전달하여 도구가 프로필 데이터를 기존 파일에 추가하고 보고서 생성 시 프로파일링된 전체 데이터 집합을 사용하도록 해야 합니다. 다른 출력 디렉터리를 전달하는 경우 새 파일이 생성되고 이전의 프로파일링된 데이터는 보고서 생성 시 사용할 수 없습니다.<br> 모든 업데이트는 zip 파일을 포함한 누적 업데이트입니다. 새 버전 파일을 사용할 이전 버전 폴더에 복사하지 않아도 됩니다. 새 폴더를 사용할 수 있습니다.


##<a name="version-history"></a>버전 기록
### <a name="11"></a>1.1
업데이트한 날짜: 2017년 3월 09일 <br>

다음 문제가 수정됨<br>

* 여러 ESXi 호스트에서 동일한 이름/IP 주소를 가진 둘 이상의 가상 컴퓨터가 vCenter에 있는 경우 가상 컴퓨터를 프로파일링할 수 없습니다.<br>
* 호환되는 VM 및 호환되지 않는 VM 시트에서 복사 및 검색을 사용할 수 없었습니다.


### <a name="10"></a>1.0 
업데이트한 날짜: 2017년 2월 23일 

Azure Site Recovery Deployment Planner 공개 미리 보기 1.0에는 향후 업데이트에서 해결할 다음과 같은 알려진 문제가 있습니다.

* 도구는 Hyper-V에서 Azure로의 배포가 아닌 VMware에서 Azure로의 시나리오에 대해서만 작동합니다. Hyper-V에서 Azure로의 시나리오에 대해서는 [Hyper-V Capacity Planner 도구](./site-recovery-capacity-planning-for-hyper-v-replication.md)를 사용합니다.
* 미국 정부 및 중국 Microsoft Azure 지역에서는 GetThroughput 작업이 지원되지 않습니다.
* 여러 ESXi 호스트에서 동일한 이름/IP 주소를 가진 둘 이상의 가상 컴퓨터가 vCenter에 있는 경우 도구에서 가상 컴퓨터를 프로파일링할 수 없습니다. 이 버전에서 도구는 VMListFile에서 중복된 가상 컴퓨터 이름/IP 주소에 대한 프로파일링을 건너뜁니다. 해결 방법은 vCenter 서버 대신 ESXi 호스트를 사용하여 가상 컴퓨터를 프로파일링하는 것입니다. 각 ESXi 호스트에 대해 인스턴스 하나만 실행해야 합니다.

