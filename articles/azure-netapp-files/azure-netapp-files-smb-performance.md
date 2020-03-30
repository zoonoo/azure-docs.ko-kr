---
title: Azure NetApp 파일의 SMB 성능에 대한 자주 묻는 질문 | 마이크로 소프트 문서
description: Azure NetApp 파일의 SMB 성능에 대한 자주 묻는 질문에 대한 답변입니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: b-juche
ms.openlocfilehash: 24b3710861f0ee158619ae9103584dcdb181f3d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460452"
---
# <a name="faqs-about-smb-performance-for-azure-netapp-files"></a>Azure NetApp 파일에 대한 SMB 성능에 대한 자주 묻는 질문

이 문서에서는 Azure NetApp 파일에 대한 SMB 성능 모범 사례에 대한 자주 묻는 질문(FAQ)에 대해 답변합니다.

## <a name="is-smb-multichannel-enabled-in-smb-shares"></a>SMB 멀티채널이 SMB 주식에서 활성화되어 있습니까? 

예. SMB 멀티채널은 기본적으로 활성화되어 있으며, 2020년 1월 초에 변경이 시행됩니다. 기존 SMB 볼륨을 미리 데이트하는 모든 SMB 주식은 기능을 사용하도록 설정했으며 새로 생성된 모든 볼륨은 생성 시 에도 기능을 사용하도록 설정합니다. 

SMB 멀티채널 기능을 활용하려면 기능 활성화 전에 설정된 모든 SMB 연결을 재설정해야 합니다. 재설정하려면 SMB 공유연결을 끊고 다시 연결할 수 있습니다.

## <a name="is-rss-supported"></a>RSS가 지원되고 있습니까?

예. Azure NetApp 파일은 수신 측 크기 조정(RSS)을 지원합니다.

SMB 멀티채널을 사용하도록 설정하면 SMB3 클라이언트는 단일 RSS가 가능한 네트워크 인터페이스 카드(NIC)를 통해 Azure NetApp 파일 SMB 서버에 대한 여러 TCP 연결을 설정합니다. 

## <a name="which-windows-versions-support-smb-multichannel"></a>어떤 Windows 버전이 SMB 멀티채널을 지원합니까?

Windows는 최상의 성능을 구현하기 위해 Windows 2012 이후 SMB 멀티채널을 지원해 왔습니다.  자세한 내용은 [SMB 멀티채널 배포](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11)) 및 [SMB 멀티채널의 기본 사항을](https://blogs.technet.microsoft.com/josebda/2012/06/28/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0/) 참조하십시오. 


## <a name="does-my-azure-virtual-machine-support-rss"></a>Azure 가상 시스템이 RSS를 지원합니까?

Azure 가상 컴퓨터 NIC가 RSS를 지원하는지 `Get-SmbClientNetworkInterface` 확인하려면 다음과 같이 `RSS Capable`명령을 실행하고 필드를 확인합니다. 

![Azure 가상 시스템에 대한 RSS 지원](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)

## <a name="does-azure-netapp-files-support-smb-direct"></a>Azure NetApp 파일이 SMB Direct를 지원합니까?

아니요, Azure NetApp 파일은 SMB Direct를 지원하지 않습니다. 

## <a name="what-is-the-benefit-of-smb-multichannel"></a>SMB 멀티채널의 장점은 무엇인가요? 

SMB 멀티채널 기능을 사용하면 SMB3 클라이언트가 단일 네트워크 인터페이스 카드(NIC) 또는 여러 NIC를 통해 연결 풀을 설정하고 이를 사용하여 단일 SMB 세션에 대한 요청을 보낼 수 있습니다. 반대로 SMB1과 SMB2는 클라이언트가 하나의 연결을 설정하고 해당 연결을 통해 지정된 세션에 대해 모든 SMB 트래픽을 보내야 합니다. 이 단일 연결은 단일 클라이언트에서 수행할 수 있는 전체 프로토콜 성능을 제한합니다.

## <a name="should-i-configure-multiple-nics-on-my-client-for-smb"></a>SMB에 대한 클라이언트에서 여러 NIC를 구성해야 합니까?

아니요. SMB 클라이언트는 SMB 서버에서 반환하는 NIC 수와 일치합니다.  각 저장소 볼륨은 하나의 저장소 끝점에서만 액세스할 수 있습니다.  즉, 지정된 SMB 관계에 대해 하나의 NIC만 사용됩니다.  

`Get-SmbClientNetworkInterace` 아래 출력에서 볼 수 있듯이 가상 시스템에는 두 개의 네트워크 인터페이스(15및 12)가 있습니다.  명령 `Get-SmbMultichannelConnection`아래에 표시된 것처럼 두 개의 RSS 지원 NICS가 있더라도 SMB 공유와 관련하여 인터페이스(12)만 사용됩니다. 인터페이스(15)가 사용되지 않습니다.

![RSS 지원 NICS](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="is-nic-teaming-supported-in-azure"></a>NIC 팀 구성이 Azure에서 지원되고 있습니까?

NIC 팀 구성은 Azure에서 지원되지 않습니다. Azure 가상 컴퓨터에서 는 여러 네트워크 인터페이스가 지원되지만 실제 구문이 아닌 논리적 인터페이스를 나타냅니다. 따라서 내결함성을 제공하지 않습니다.  또한 Azure 가상 컴퓨터에서 사용할 수 있는 대역폭은 개별 네트워크 인터페이스가 아닌 컴퓨터 자체에 대해 계산됩니다.

## <a name="whats-the-performance-like-for-smb-multichannel"></a>SMB 멀티채널의 실적은 어떻습니까?

다음 테스트 및 그래프는 단일 인스턴스 워크로드에서 SMB 멀티채널의 강력한 힘을 보여 줍니다.

### <a name="random-io"></a>랜덤 I/O  

클라이언트에서 SMB 멀티채널을 사용하지 않도록 설정한 경우 FIO 및 40GiB 작업 집합을 사용하여 순수 한 8-KiB 읽기 및 쓰기 테스트를 수행했습니다.  SMB 공유는 RSS 네트워크 인터페이스 설정당 `1``4``8``16` `set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>`SMB 클라이언트 연결 횟수가 증가하여 각 테스트 간에 분리되었습니다. 테스트는 기본 설정이 `4` I/O 집약적 워크로드에 충분하다는 것을 보여 주며, `8` 증가하며 `16` 아무런 영향을 미치지 않았습니다. 

이 `netstat -na | findstr 445` 명령은 에 대한 추가 연결이 로 `1` 및 `4` `8` 에 `16`단위로 설정되었다는 것을 증명했습니다.  perfmon `Per Processor Network Activity Cycles` 통계에 의해 확인된 바와 같이, 4개의 CPU 코어는 각 테스트 동안 SMB에 완전히 사용되었습니다(이 문서에는 포함되지 않음).

![무작위 I/O 테스트](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

Azure 가상 시스템은 SMB(NFS) 저장소 I/O 제한에 영향을 주지 않습니다.  아래 그림과 같이 D16 인스턴스 유형은 캐시된 저장소 IOPS의 경우 32,000개, 캐시되지 않은 저장소 IOPS의 경우 25,600의 제한된 비율을 가지고 있습니다.  그러나 위의 그래프는 SMB에 비해 훨씬 더 많은 I/O를 보여줍니다.

![랜덤 I/O 비교](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

### <a name="sequential-io"></a>순차 IO 

위에서 설명한 무작위 I/O 시험과 유사한 시험은 64-KiB 순차I/O로 수행되었다. RSS 네트워크 인터페이스당 클라이언트 연결 수가 4%를 초과하는 경우 임의 I/O에는 눈에 띄는 영향을 미치지 않지만 순차적 I/O에는 적용되지 않습니다. 다음 그래프에서 볼 수 있듯이 각 증가는 읽기 처리량의 해당 증가와 관련이 있습니다. 쓰기 처리량은 각 인스턴스 유형/크기에 대해 Azure에서 배치한 네트워크 대역폭 제한으로 인해 평평하게 유지되었습니다. 

![순차적 I/O 테스트](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Azure는 각 가상 시스템 유형/크기에 네트워크 속도 제한을 배치합니다. 요금 제한은 아웃바운드 트래픽에만 부과됩니다. 가상 시스템에 있는 NIC 수는 컴퓨터에서 사용할 수 있는 총 대역폭 양에 영향을 미치지 않습니다.  예를 들어 D16 인스턴스 유형에는 8000Mbps(1,000MiB/s)의 네트워크 제한이 부과됩니다.  위의 순차 그래프에서 볼 수 있듯이 제한은 아웃바운드 트래픽(쓰기)에 영향을 주지만 다중 채널 읽기에는 영향을 미치지 않습니다.

![순차적 I/O 비교](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="is-accelerated-networking-recommended"></a>가속 네트워킹이 권장되는가요?

성능을 극대화하려면 가능한 경우 [가속 네트워킹을](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) 구성하는 것이 좋습니다. 다음 고려 사항을 염두에 두십시오.  

* Azure 포털에서는 이 기능을 지원하는 가상 시스템에 대해 기본적으로 가속 네트워킹을 사용할 수 있습니다.  그러나 Ansible 및 유사한 구성 도구와 같은 다른 배포 방법은 그렇지 않을 수 있습니다.  가속 네트워킹을 사용하도록 설정하지 않으면 컴퓨터의 성능이 저하될 수 있습니다.  
* 인스턴스 유형 이나 크기에 대 한 지원 부족으로 인해 가상 시스템의 네트워크 인터페이스에서 가속 네트워킹을 사용 하지 않는 경우 더 큰 인스턴스 형식으로 사용 안 성 상태로 유지 됩니다. 이러한 경우 수동 개입이 필요합니다.

## <a name="are-jumbo-frames-supported"></a>점보 프레임이 지원되나요?

점보 프레임은 Azure 가상 컴퓨터에서 지원되지 않습니다.

## <a name="is-smb-signing-supported"></a>SMB 서명이 지원되고 있습니까? 

SMB 프로토콜은 파일 및 인쇄 공유 및 원격 Windows 관리와 같은 기타 네트워킹 작업의 기초를 제공합니다. 전송 중인 SMB 패킷을 수정하는 중간자(man-in-the-middle) 공격을 방지하기 위해 SMB 프로토콜은 SMB 패킷의 디지털 서명을 지원합니다. 

SMB 서명은 Azure NetApp 파일에서 지원하는 모든 SMB 프로토콜 버전에서 지원됩니다. 

## <a name="what-is-the-performance-impact-of-smb-signing"></a>SMB 서명의 성능 영향은 무엇입니까?  

SMB 서명은 SMB 성능에 해로운 영향을 미칩니다. 성능 저하의 다른 잠재적인 원인 중 각 패킷의 디지털 서명은 아래 의 perfmon 출력과 같이 추가 클라이언트 측 CPU를 사용합니다. 이 경우 코어 0은 SMB 서명을 포함하여 SMB를 담당합니다.  이전 섹션의 비다채널 순차 읽기 처리량 번호와 비교하면 SMB 서명이 전체 처리량을 875MiB/s에서 약 250MiB/s로 줄인다는 것을 알 수 있습니다. 

![SMB 서명 성능 영향](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)


## <a name="next-steps"></a>다음 단계  

- [Azure NetApp 파일에 대한 자주 묻는 질문](azure-netapp-files-faqs.md)
- Azure [NetApp 파일: Azure NetApp 파일에서 SMB](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1) 파일 공유를 사용하는 것에 대한 SMB 워크로드에 대한 관리되는 엔터프라이즈 파일 공유를 참조하십시오.
