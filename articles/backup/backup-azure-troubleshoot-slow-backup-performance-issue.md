---
title: Azure Backup에서 파일 및 폴더의 느린 백업 문제 해결
description: Azure Backup 성능 문제의 원인을 진단하는 데 도움이 되는 문제 해결 지침을 제공합니다.
services: backup
author: saurabhsensharma
manager: saurabhsensharma
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: saurse
ms.openlocfilehash: 592a46077bb9e3469f3a42a95173af1b6db93510
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704930"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Azure Backup에서 파일 및 폴더의 느린 백업 문제 해결
이 문서에서는 Azure Backup을 사용할 때 파일 및 폴더의 느린 백업 성능 문제에 대한 원인을 진단하는 데 도움이 되는 문제 해결 지침을 제공합니다. Azure Backup 에이전트를 사용하여 파일을 백업하는 경우 백업 프로세스가 예상보다 오래 걸릴 수 있습니다. 이러한 지연은 다음 중 하나 이상에 의해 발생할 수 있습니다.

* [백업 중인 컴퓨터에서 성능 병목 현상이 발생하는 경우](#cause1)
* [다른 프로세스 또는 바이러스 백신 소프트웨어가 Azure Backup 프로세스를 방해하는 경우](#cause2)
* [Backup 에이전트가 Azure VM(가상 컴퓨터)에서 실행 중인 경우](#cause3)  
* [많은 수(수백만 개)의 파일을 백업하는 경우](#cause4)

문제 해결을 시작하기 전에 [최신 Azure Backup 에이전트](https://aka.ms/azurebackup_agent)를 다운로드하여 설치하는 것이 좋습니다. Microsoft는 다양한 문제를 해결하고, 기능을 추가하고, 성능을 향상시키기 위해 Backup 에이전트를 자주 업데이트합니다.

일반적인 구성 문제가 발생하지 않도록 [Azure Backup 서비스 - FAQ](backup-azure-backup-faq.md) 를 검토하는 것이 좋습니다.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>원인: 컴퓨터의 성능 병목 상태
백업 중인 컴퓨터의 병목 현상으로 인해 지연이 발생할 수 있습니다. 예를 들어 컴퓨터가 디스크에 읽거나 쓰는 기능 또는 네트워크를 통해 데이터를 전송하는 데 사용할 수 있는 대역폭으로 인해 병목 현상이 발생할 수 있습니다.

Windows에서는 이러한 병목 상태를 검색할 수 있는 [성능 모니터](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon)라는 기본 제공 도구를 제공합니다.

다음은 최적의 백업을 위해 병목 현상을 진단하는 데 도움이 될 수 있는 몇 가지 성능 카운터 및 범위입니다.

| 카운터 | Status |
| --- | --- |
| Logical Disk(Physical Disk)--%idle |• 100% 유휴 ~ 50% 유휴 = 정상</br>• 49% 유휴 ~ 20% 유휴 = 경고 또는 모니터</br>• 19% 유휴 ~ 0% 유휴 = 위험 또는 사양을 벗어남 |
| 논리 디스크(실제 디스크)--%평균 Disk Sec Read or Write |• 0.001ms ~ 0.015ms = 정상</br>• 0.015ms ~ 0.025ms = 경고 또는 모니터</br>• 0.026ms 이상 = 위험 또는 사양을 벗어남 |
| 논리 디스크(실제 디스크)--현재 디스크 큐 길이(모든 인스턴스) |6분이 넘는 시간 동안 80개 요청 |
| Memory--Pool Non Paged Bytes |• 사용된 풀의 60% 미만 = 정상<br>• 사용된 풀의 61% ~ 80% = 경고 또는 모니터</br>• 사용된 풀의 80% 초과 = 위험 또는 사양을 벗어남 |
| Memory--Pool Paged Bytes |• 사용된 풀의 60% 미만 = 정상</br>• 사용된 풀의 61% ~ 80% = 경고 또는 모니터</br>• 사용된 풀의 80% 초과 = 위험 또는 사양을 벗어남 |
| Memory--Available Megabytes |• 사용 가능한 여유 메모리의 50% 이상 = 정상</br>• 사용 가능한 여유 메모리의 25% = 모니터링</br>• 사용 가능한 여유 메모리의 10% = 경고</br>• 사용 가능한 여유 메모리의 100MB 또는 5% 미만 = 위험 또는 사양을 벗어남 |
| Processor--\%Processor Time (all instances) |• Less than 60% consumed = Healthy</br>• 61% to 90% consumed = Monitor or Caution</br>• 91% to 100% consumed = Critical |

> [!NOTE]
> If you determine that the infrastructure is the culprit, we recommend that you defragment the disks regularly for better performance.
>
>

<a id="cause2"></a>

## Cause: Another process or antivirus software interfering with Azure Backup
We've seen several instances where other processes in the Windows system have negatively affected performance of the Azure Backup agent process. For example, if you use both the Azure Backup agent and another program to back up data, or if antivirus software is running and has a lock on files to be backed up, the multiple locks on files might cause contention. In this situation, the backup might fail, or the job might take longer than expected.

The best recommendation in this scenario is to turn off the other backup program to see whether the backup time for the Azure Backup agent changes. Usually, making sure that multiple backup jobs are not running at the same time is sufficient to prevent them from affecting each other.

For antivirus programs, we recommend that you exclude the following files and locations:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe as a process
* C:\Program Files\Microsoft Azure Recovery Services Agent\ folders
* Scratch location (if you're not using the standard location)

<a id="cause3"></a>

## Cause: Backup agent running on an Azure virtual machine
If you're running the Backup agent on a VM, performance will be slower than when you run it on a physical machine. This is expected due to IOPS limitations.  However, you can optimize the performance by switching the data drives that are being backed up to Azure Premium Storage. We're working on fixing this issue, and the fix will be available in a future release.

<a id="cause4"></a>

## Cause: Backing up a large number (millions) of files
Moving a large volume of data will take longer than moving a smaller volume of data. In some cases, backup time is related to not only the size of the data, but also the number of files or folders. This is especially true when millions of small files (a few bytes to a few kilobytes) are being backed up.

This behavior occurs because while you're backing up the data and moving it to Azure, Azure is simultaneously cataloging your files. In some rare scenarios, the catalog operation might take longer than expected.

The following indicators can help you understand the bottleneck and accordingly work on the next steps:

* **UI is showing progress for the data transfer**. The data is still being transferred. The network bandwidth or the size of data might be causing delays.
* **UI is not showing progress for the data transfer**. Open the logs located at C:\Program Files\Microsoft Azure Recovery Services Agent\Temp, and then check for the FileProvider::EndData entry in the logs. This entry signifies that the data transfer finished and the catalog operation is happening. Don't cancel the backup jobs. Instead, wait a little longer for the catalog operation to finish. If the problem persists, contact [Azure support](https://portal.azure.com/#create/Microsoft.Support).Processor--\`%Processor Time(모든 인스턴스)es and folders in Azure Backup
description: Provides troubleshooting guidance to help you diagnose the cause of Azure Backup performance issues
services: backup
author: saurabhsensharma
manager: saurabhsensharma
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: saurse
---
# Troubleshoot slow backup of files and folders in Azure Backup
This article provides troubleshooting guidance to help you diagnose the cause of slow backup performance for files and folders when you're using Azure Backup. When you use the Azure Backup agent to back up files, the backup process might take longer than expected. This delay might be caused by one or more of the following:

* [There are performance bottlenecks on the computer that’s being backed up.](#cause1)
* [Another process or antivirus software is interfering with the Azure Backup process.](#cause2)
* [The Backup agent is running on an Azure virtual machine (VM).](#cause3)  
* [You're backing up a large number (millions) of files.](#cause4)

Before you start troubleshooting issues, we recommend that you download and install the [latest Azure Backup agent](https://aka.ms/azurebackup_agent). We make frequent
updates to the Backup agent to fix various issues, add features, and improve performance.

We also strongly recommend that you review the [Azure Backup service FAQ](backup-azure-backup-faq.md) to make sure you're not experiencing any of the common configuration issues.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## Cause: Performance bottlenecks on the computer
Bottlenecks on the computer that's being backed up can cause delays. For example, the computer's ability to read or write to disk, or available bandwidth to send data over the network, can cause bottlenecks.

Windows provides a built-in tool that's called [Performance Monitor](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) to detect these bottlenecks.

Here are some performance counters and ranges that can be helpful in diagnosing bottlenecks for optimal backups.

| Counter | Status |
| --- | --- |
| Logical Disk(Physical Disk)--%idle |• 100% idle to 50% idle = Healthy</br>• 49% idle to 20% idle = Warning or Monitor</br>• 19% idle to 0% idle = Critical or Out of Spec |
| Logical Disk(Physical Disk)--%Avg. Disk Sec Read or Write |• 0.001 ms to 0.015 ms  = Healthy</br>• 0.015 ms to 0.025 ms = Warning or Monitor</br>• 0.026 ms or longer = Critical or Out of Spec |
| Logical Disk(Physical Disk)--Current Disk Queue Length (for all instances) |80 requests for more than 6 minutes |
| Memory--Pool Non Paged Bytes |• Less than 60% of pool consumed = Healthy<br>• 61% to 80% of pool consumed = Warning or Monitor</br>• Greater than 80% pool consumed = Critical or Out of Spec |
| Memory--Pool Paged Bytes |• Less than 60% of pool consumed = Healthy</br>• 61% to 80% of pool consumed = Warning or Monitor</br>• Greater than 80% pool consumed = Critical or Out of Spec |
| Memory--Available Megabytes |• 50% of free memory available or more = Healthy</br>• 25% of free memory available = Monitor</br>• 10% of free memory available = Warning</br>• Less than 100 MB or 5% of free memory available = Critical or Out of Spec |
| Processor--\%Processor Time (all instances) |• 60% 미만 사용 = 정상</br>• 61% ~ 90% 사용 = 모니터 또는 주의</br>• 91% ~ 100% 사용 = 위험 |

> [!NOTE]
> 인프라가 가능한 원인으로 격리되면 더 나은 성능을 위해 주기적으로 디스크의 조각 모음을 수행하는 것이 좋습니다.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>원인: 다른 프로세스 또는 바이러스 백신 소프트웨어가 Azure Backup을 방해
Windows 시스템 내의 다른 프로세스가 Azure Backup 에이전트 프로세스의 성능에 부정적인 영향을 미치는 경우가 확인되었습니다. 예를 들어 Azure Backup 에이전트 및 다른 프로그램을 함께 사용하여 데이터를 백업하거나 바이러스 백신 소프트웨어가 실행되고 있고 백업할 파일이 잠겨 있는 경우 파일을 여러 번 잠그면서 경합이 발생할 수 있습니다. 이 경우 백업이 실패하거나 작업이 예상보다 오래 걸릴 수 있습니다.

이 시나리오에서 가장 좋은 방법은 다른 백업 프로그램을 해제하고 Azure Backup 에이전트의 백업 시간이 달라지는지 확인하는 것입니다. 일반적으로 여러 개의 백업이 동시에 실행되지 않도록 하는 것만으로도 백업이 서로 영향을 주지 않도록 할 수 있습니다.

바이러스 백신 프로그램의 경우 다음 파일 및 위치를 제외하는 것이 좋습니다.

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe를 프로세스로 제외합니다.
* C:\Program Files\Microsoft Azure Recovery Services Agent\ 폴더를 제외합니다.
* 스크래치 위치를 제외합니다(표준 위치를 사용하지 않는 경우).

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>원인: Azure 가상 머신에서 실행 되는 backup 에이전트
VM에서 백업 에이전트를 실행하는 경우 실제 컴퓨터에서 실행할 때보다 성능이 더 느려집니다. 이것은 IOPS 제한 때문으로 예상됩니다.  그러나 백업되는 데이터 드라이브를 Azure Premium Storage로 전환하여 성능을 최적화할 수 있습니다. 이 문제를 해결하기 위해 계속 노력 중이며, 이후 릴리스에서 수정 사항을 사용할 수 있게 될 것입니다.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>원인: 많은 수 (수백만 개)의 파일 백업
이동하는 데이터의 양이 많을수록 시간이 더 오래 걸립니다. 경우에 따라 백업 시간은 데이터의 크기뿐만 아니라 파일 또는 폴더 수와도 관련이 있습니다. 특히 수백만 개의 작은 파일(몇 바이트에서 몇 킬로바이트 크기)을 백업하는 경우 더욱 그렇습니다.

이 동작은 데이터를 백업하고 Azure로 이동하는 동안 Azure에서 동시에 파일 카탈로그를 만들기 때문에 발생합니다. 간혹 카탈로그 작업이 예상보다 오래 걸릴 수도 있습니다.

다음 표시기는 병목 상태를 이해하고 그에 따라 다음 단계에서 작업을 진행하는 데 유용할 수 있습니다.

* **UI에 데이터 전송 진행률이 표시됨**. 데이터가 여전히 전송 중입니다. 네트워크 대역폭 또는 데이터 크기로 인해 지연이 발생할 수 있습니다.
* **UI에 데이터 전송 진행률이 표시되지 않음**. C:\Program Files\Microsoft Azure Recovery Services Agent\Temp에 있는 로그를 연 다음, 로그에서 FileProvider::EndData 항목을 확인합니다. 이 항목은 데이터 전송이 완료되었으며 카탈로그 작업이 진행 중임을 나타냅니다. 백업 작업을 취소하지 말고, 대신 카탈로그 작업가 끝날 때까지 좀 더 기다리세요. 문제가 지속되면 [Azure 지원](https://portal.azure.com/#create/Microsoft.Support)에 문의하세요.
