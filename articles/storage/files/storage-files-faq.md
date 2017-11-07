---
title: "Azure Files에 대한 질문과 대답 | Microsoft Docs"
description: "Azure Files에 대한 질문과 대답을 확인합니다."
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/13/2017
ms.author: renash
ms.openlocfilehash: 91c46ea0897f83811cfa5c1a8b67677caff14569
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2017
---
# <a name="frequently-asked-questions-about-azure-files"></a>Azure Files에 대한 질문과 대답
[Azure Files](storage-files-introduction.md)는 산업 표준 [SMB(서버 메시지 블록) 프로토콜](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)(일반 인터넷 파일 시스템 또는 CIFS라고도 함)을 통해 액세스할 수 있는, 클라우드에서 완전히 관리되는 파일 공유를 제공합니다. Azure File 공유는 Windows, Linux 및 macOS의 클라우드 또는 온-프레미스 배포를 통해 동시에 탑재될 수 있습니다. 또한 데이터가 사용되는 위치 가까이에 빠르게 액세스하기 위해 Azure File Sync(미리 보기)를 사용하여 Azure 파일 공유를 Windows Server에서 캐시할 수 있습니다.

이 문서에서는 Azure File Sync를 비롯하여 Azure Files 기능에 대한 일반적인 질문을 설명합니다. 여기에서 질문에 대한 답을 찾지 못한 경우 다음 채널을 통해 (제시된 채널 순서에 따라) 문의하시기 바랍니다.

1. 이 문서의 의견 섹션.
2. [Azure Storage 포럼](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredata)
3. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) 
4. Microsoft 지원: 새 지원 사례를 만들려면 Azure Portal의 “도움말 + 지원” 탭으로 이동하여 “새 지원 요청”을 클릭합니다.

## <a name="general"></a>일반
* <a id="why-files-useful"></a>**Azure Files가 유용한 이유는 무엇인가요?**  
   Azure Files를 사용하면 물리적 서버 또는 장치/어플라이언스의 오버헤드를 관리할 필요 없이 클라우드에서 파일 공유를 만들 수 있습니다. 즉, OS 업데이트를 적용하고 잘못된 디스크를 교체하느라 낭비하는 시간을 줄일 수 있습니다. 이러한 단조로운 작업은 모두 자동으로 수행되기 때문입니다. Azure Files를 유용하게 사용할 수 있는 시나리오에 대한 자세한 내용은 [Azure Files가 유용한 이유](storage-files-introduction.md#why-azure-files-is-useful)를 참조하세요.

* <a id="file-access-options"></a>**Azure Files에서 파일에 액세스하는 다른 방법은 무엇인가요?**  
    SMB 3.0 프로토콜을 사용하여 로컬 컴퓨터에 파일 공유를 탑재하거나 [저장소 탐색기](http://storageexplorer.com/)와 같은 도구를 사용하여 파일 공유의 파일을 액세스할 수 있습니다. 응용 프로그램에서 저장소 클라이언트 라이브러리, REST API, Powershell 또는 CLI를 사용하여 Azure Files 공유의 파일에 액세스할 수 있습니다.

* <a id="what-is-afs"></a>**Azure File Sync란?**  
    Azure File Sync를 사용하여 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 희생하지 않고 Azure Files에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. 이 작업은 Windows Server를 Azure 파일 공유의 빠른 캐시로 변환하여 수행합니다. Windows Server에서 사용할 수 있는 아무 프로토콜이나 사용하여 데이터를 로컬로(SMB, NFS 및 FTPS 포함) 액세스할 수 있으며 세계 전역에 걸쳐 필요한 만큼 캐시를 보유할 수 있습니다.

* <a id="files-versus-blobs"></a>**데이터를 위해 Azure 파일 공유 및 Azure Blob Storage를 사용해야 하는 이유는 각각 무엇인가요?**  
    Azure Files 및 Azure Blob Storage는 둘 다 대량의 데이터를 클라우드에 저장하는 방법을 제공하지만 약간 다른 용도에서 유용합니다. Azure Blob Storage는 구조화되지 않은 데이터를 저장해야 하는 대규모 클라우드 네이티브 응용 프로그램에 유용합니다. 성능 및 확장성을 최대화하기 위해 Azure Blob Storage는 실제 파일 시스템보다 더 간단한 저장소 추상화를 구현합니다. 또한 Azure Blob Storage는 REST 기반 클라이언트 라이브러리를 통해서만(또는 REST 기반 프로토콜을 통해 직접) 액세스할 수 있습니다.

    반면에 Azure Files는 온-프레미스 운영 체제가 사용되던 수년 동안 사용자가 잘 알고 선호하는 모든 파일 추상을 포함하는 파일 시스템이 되려고 합니다. Azure Blob Storage와 마찬가지로, Azure Files는 REST 인터페이스와 REST 기반 클라이언트 라이브러리를 제공하지만 Azure Blob Storage와 달리 Azure Files는 Azure 파일 공유에 대한 SMB 액세스를 제공합니다. 즉, 코드를 쓰거나 파일 시스템에 특수한 드라이버를 연결하지 않고도, Windows, Linux 또는 macOS, 온-프레미스 또는 클라우드 VM에 Azure 파일 공유를 직접 탑재할 수 있습니다. 또한 데이터가 사용되는 위치 가까이에 빠르게 액세스하기 위해 Azure File Sync를 사용하여 Azure 파일 공유를 온-프레미스 파일 서버에 캐시할 수 있습니다. 
   
    Azure Files 및 Azure Blob Storage 간의 차이점에 대한 심도 있는 논의 내용을 보려면 [Azure Blob Storage, Azure Files 또는 Azure 디스크를 사용해야 하는 경우 결정](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)을 참조하세요. Azure Blob Storage에 대한 자세한 내용은 [Blob Storage 소개](../blobs/storage-blobs-introduction.md)를 참조하세요.

* <a id="files-versus-disks"></a>**Azure 파일 공유 및 Azure 디스크를 사용하는 이유는 각각 무엇인가요?**  
    Azure 디스크는 디스크일 뿐입니다. 독립 실행형 디스크 자체는 그리 유용하지 않습니다. Azure 디스크를 가치 있게 사용하려면 Azure에서 실행되는 가상 컴퓨터에 연결해야 합니다. Azure 디스크는 OS 시스템 디스크, OS용 스왑 공간 또는 응용 프로그램을 위한 전용 저장소와 같이 온-프레미스 서버에서 디스크를 사용해야 하는 모든 경우에서 사용할 수 있습니다. Azure 디스크의 유용한 용도는 Azure 파일 공유를 사용할 수 있는 정확히 동일한 장소에서 사용하기 위해 클라우드에 파일 서버를 만들 수 있다는 것입니다. Azure VM에 파일 서버를 배포하는 일은 현재 Azure Files에서 지원하지 않는 배포 옵션(예: NFS 프로토콜 지원 또는 프리미엄 저장소)이 필요할 때 Azure에서 파일 저장소를 얻는 매우 유용한 고성능 방식입니다. 

    반면 Azure 디스크를 백 엔드 저장소로 사용해서 파일 서버를 실행하게 되면 몇 가지 이유로 인해 Azure 파일 공유를 사용할 때보다 일반적으로 훨씬 더 많은 비용이 초래됩니다. 첫째, 디스크 저장소에 대해 요금을 지불하는 것 외에, 하나 이상의 Azure VM을 실행하는 비용도 지불해야 합니다. 둘째, OS 업그레이드 등을 담당하는 VM 등과 같이 파일 서버를 실행하는 데 사용되는 VM도 관리해야 합니다. 마지막으로 온-프레미스에 데이터를 캐시하려는 경우 사용자가 이를 위해 복제 토폴로지(예: 분산 파일 시스템 복제)를 설정하고 관리해야 합니다.

    Azure Disks를 백 엔드 저장소로 사용하여 Azure VM에 호스트된 Azure Files 및 파일 서버를 둘 다 적절히 활용하는 유용한 방법 중 하나는 클라우드 VM 호스티드 파일 서버에 Azure File Sync를 설치하는 것입니다. Azure 파일 공유가 파일 서버와 동일한 지역에 있으면 클라우드 계층화를 사용하도록 설정하고 사용 가능한 볼륨 공간 비율을 최대값(99%)으로 설정할 수 있습니다. 이렇게 하면 파일 서버에 대해 NFS 프로토콜 지원을 요구하는 응용 프로그램과 같은 원하는 응용 프로그램을 사용할 수 있도록 하면서 데이터의 중복을 최소화할 수 있습니다.

    Azure에서 고성능 및 고가용성 파일 서버를 설정하는 한 가지 방법에 대한 자세한 내용은 [Microsoft Azure에서 IaaS VM 게스트 클러스터 배포](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/)를 참조하세요. Azure Files 및 Azure 디스크 간의 차이점에 대한 심도 있는 논의 내용을 보려면 [Azure Blob Storage, Azure Files 또는 Azure 디스크를 사용해야 하는 경우 결정](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)을 참조하세요. Azure 디스크에 대한 자세한 내용은 [Azure 관리되는 디스크 개요](../../virtual-machines/windows/managed-disks-overview.md)를 참조하세요.

* <a id="get-started"></a>**Azure Files 사용을 시작하려면 어떻게 해야 하나요?**  
    Azure Files 시작은 간단합니다. [파일 공유를 만들고](storage-how-to-create-file-share.md) 기본 운영 체제에 탑재하면 됩니다. 

    - [Windows에 탑재](storage-how-to-use-files-windows.md)
    - [Linux에 탑재](storage-how-to-use-files-linux.md)
    - [macOS에 탑재](storage-how-to-use-files-mac.md)

    Azure 파일 공유를 배포하여 조직에서 프로덕션 파일 공유를 대신하는 방법에 대한 자세한 지침을 보려면 [Azure Files 배포에 대한 계획](storage-files-planning.md)을 참조하세요.

* <a id="redundancy-options"></a>**Azure Files에서 지원되는 저장소 중복 옵션은 무엇인가요?**  
    Azure Files는 현재 LRS(로컬 중복 저장소)와 GRS(지역 중복 저장소)만 지원합니다. 앞으로 ZRS(영역 중복 저장소) 및 RA-GRS(읽기 액세스 지역 중복 저장소)를 지원할 예정이지만 지금은 일정 계획이 나와 있지 않습니다.

* <a id="tier-options"></a>**현재 Azure Files에서 어떤 저장소 계층이 지원되나요?**  
    Azure Files는 현재 표준 저장소 계층만 지원합니다. 현재 프리미엄 및 쿨 지원을 위한 일정이 나와 있지 않습니다. Blob 전용 저장소 계정 또는 Premium 저장소 계정에서 Azure 파일 공유를 만들 수 없습니다.

* <a id="give-us-feedback"></a>**Azure Files에 *x* 기능이 추가되는 것을 정말 보고 싶습니다. 추가해줄 수 있나요?**  
    물론입니다. Azure Files 팀은 당사 서비스에 대한 여러분의 모든 의견을 환영합니다. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)에서 기능 요청에 투표하세요. 많은 새로운 기능으로 고객에게 만족을 드리고 싶습니다.

## <a name="azure-file-sync"></a>Azure 파일 동기화
* <a id="afs-region-availability"></a>**현재 어떤 지역에서 Azure File Sync(미리 보기)가 지원되나요?**  
    Azure File Sync는 현재 미국 서부, 유럽 서부, 오스트레일리아 동부, 동남 아시아에서 사용할 수 있습니다. 일반 공급을 준비하면서 추가 지역에 대한 지원을 추가할 예정입니다. 추가 정보를 보려면 [지역 가용성](storage-sync-files-planning.md#region-availability)을 참조하세요.

* <a id="cross-domain-sync"></a>**도메인에 가입된 서버와 도메인에 가입되지 않은 서버를 같은 동기화 그룹에 둘 수 있나요?**  
    예. 동기화 그룹에는 도메인에 가입되지 않은 서버를 포함하여 다른 Active Directory 구성원 자격을 갖는 서버 끝점이 포함될 수 있습니다. 구성이 기술적으로는 작동하지만, 한 서버의 파일/폴더에 대해 정의된 ACL은 동기화 그룹의 다른 서버에 의해 적용될 수 없으므로 이러한 구성은 정상 구성으로 권장되지 않습니다. 최상의 결과를 얻으려면 동일한 Active Directory 포리스트의 서버, 설정된 트러스트 관계를 갖는 다른 Active Directory 포리스트의 서버 또는 도메인에 들어 있지 않은 서버 간의 동기화는 권장되지만 위의 모든 방식의 혼합은 바람직하지 않습니다.

* <a id="afs-change-detection"></a>**SMB 또는 포털을 통해 Azure 파일 공유에서 직접 파일을 만들었습니다. 파일이 동기화 그룹의 서버와 동기화되는 데 시간이 얼마나 걸리나요?** 
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**같은 파일이 두 서버에서 거의 동시에 변경되면 어떻게 되나요?**  
    Azure File Sync는 간단한 충돌 해결 전략을 사용하므로 두 변경 내용이 모두 유지됩니다. 가장 최근에 수정한 파일에 원래 파일 이름이 사용됩니다. 이전 파일에 '원본' 컴퓨터가 있으며 다음 분류법에 따라 이름 앞에 충돌하는 번호가 추가됩니다. 
   
    `<FileNameWithoutExtension>-<MachineName>[-#].<ext>`  

    예를 들어 `CompanyReport.docx`의 첫 번째 충돌은 `CentralServer`이 이전 쓰기 발생한 위치인 경우 `CompanyReport-CentralServer.docx`가 됩니다. 두 번째 충돌은 `CompanyReport-CentralServer-1.docx`로 식별됩니다.

* <a id="afs-storage-redundancy"></a>**GRS(지역 중복 저장소)가 Azure File Sync에서 지원되나요?**  
    예. Azure 파일 저장소는 LRS(로컬 중복 저장소) 및 GRS(지역 중복 저장소)를 둘 다 지원합니다. 페어링된 지역 간에 GRS 장애 조치(Failover)가 수행되는 경우 새 지역을 데이터 백업 용도로만 사용하는 것이 좋습니다. Azure File Sync는 새 주 영역과의 동기화를 자동으로 시작하지 않습니다. 

* <a id="sizeondisk-versus-size"></a>**Azure File Sync를 사용한 후에 Azure 파일에 대한 *디스크 크기* 속성이 *크기* 속성과 일치하지 않는 이유는 무엇인가요?**  
    Windows 파일 탐색기는 파일 크기를 나타내기 위해 **크기** 및 **디스크 크기**의 두 속성을 표시합니다. 이러한 속성은 약간 다른 의미를 갖습니다. **크기**는 파일의 전체 크기를 나타내는 반면, **디스크 크기**는 디스크에 실제로 저장된 파일 스트림의 크기를 나타냅니다. 이러한 두 크기는 압축, 데이터 중복 제거의 사용 또는 Azure File Sync를 사용한 클라우드 계층화 같은 다양한 이유로 차이를 보일 수 있습니다. 파일이 Azure 파일 공유로 계층화되면 파일 스트림이 디스크가 아닌 Azure 파일 공유에 저장되므로 디스크 크기는 0이 됩니다. 파일을 부분적으로 계층화할 수 있습니다(또는 부분적으로 회수). 이것은 파일 일부가 디스크에 있는 것을 의미합니다. 멀티미디어 플레이어 또는 압축 유틸리티와 같은 응용 프로그램에 의해 파일이 부분적으로 읽힐 때 이러한 현상이 발생할 수 있습니다. 

* <a id="is-my-file-tiered"></a>**파일이 계층화되어 있는지를 어떻게 알 수 있나요?**  
    파일이 Azure 파일 공유로 계층화되었는지 확인하는 몇 가지 방법이 있습니다.
    
    1. **파일의 파일 특성을 확인합니다.** 이렇게 하려면 파일을 마우스 오른쪽 단추로 클릭하고 **세부 정보**로 이동한 후 아래로 스크롤하여 **특성** 속성으로 이동합니다. 계층화된 파일에는 다음과 같은 특성 집합이 적용됩니다.     
        
        | 특성 문자 | 특성 | 정의 |
        |:----------------:|-----------|------------|
        | A | 보관 | 파일을 백업 소프트웨어로 백업해야 함을 나타냅니다. 이 특성은 파일이 계층화되는지 또는 디스크에 완전히 저장되는지에 관계없이 항상 설정됩니다. |
        | P | 스파스 파일 | 파일이 스파스 파일인지를 나타냅니다. 스파스 파일은 디스크 스트림의 파일이 대부분 비어 있을 때 효율적으로 사용하기 위해 NTFS가 제공하는 특수한 형식의 파일입니다. Azure File Sync는 파일이 완전히 계층화되어 파일 스트림이 클라우드에만 저장되어 있거나 부분적으로 회수되어 파일 부분이 이미 디스크에 있기 때문에 스파스 파일을 사용합니다. 파일이 디스크에 완전히 회수되면 Azure File Sync는 스파스 파일에서 일반 파일로 변환합니다. |
        | L | 재분석 지점 | 파일에 파일 시스템 필터에서 사용되는 특별한 포인터인 재분석 지점에 있음을 나타냅니다. Azure File Sync는 재분석 지점을 사용하여 Azure File Sync 파일 시스템 필터(StorageSync.sys)에 클라우드에서 파일이 저장되는 위치를 정의합니다. 이를 통해 Azure File Sync가 사용되고 있다는 사실 또는 Azure File Sync에서 파일에 대해 액세스 권한을 얻는 방법을 최종 사용자가 알지 못해도 원활하게 액세스할 수 있습니다. 파일을 완전하게 회수되면 Azure File Sync는 파일에서 재분석 지점을 제거합니다. |
        | O | 오프라인 | 파일 콘텐츠 일부 또는 전체가 디스크에 저장되지 않음을 나타냅니다. 파일을 완전하게 회수되면 Azure File Sync는 이 특성을 제거합니다. |

        ![세부 정보 탭이 선택되어 있는 파일에 대한 속성 대화 상자](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        **특성** 필드를 파일 탐색기의 표 화면에 추가하여 폴더의 모든 파일에 대한 특성을 볼 수도 있습니다. 이렇게 하려면 기존 열을 마우스 오른쪽 단추로 클릭하고(예: 크기) **더 보기**를 선택하고 드롭다운 목록에서 **특성**을 선택합니다.
        
    2. **`fsutil`을 사용하여 파일에 대한 재분석 지점을 확인합니다.** 이전 옵션에서 언급한 것처럼 계층화된 파일에는 항상 재분석 지점이나 Azure File Sync 파일 시스템 필터(StorageSync.sys)에 대한 특수 포인터가 설정됩니다. 관리자 권한 명령 프롬프트 또는 PowerShell 세션에에서 `fsutil` 유틸리티를 사용하여 파일에 재분석 지점이 있는지 확인할 수 있습니다.
    
        ```PowerShell
        fsutil reparsepoint query <your-file-name>
        ```

        파일에는 재분석 지점이 있으면 **재분석 태그 값: 0x8000001e**이 표시됩니다. 이 16진수 값은 Azure File Sync에서 소유하는 재분석 지점 값입니다. 또한 출력에는 Azure 파일 공유의 파일 경로를 나타내는 재분석 데이터도 포함됩니다.

        > [!Warning]  
        > `fsutil reparsepoint` 유틸리티 명령은 재분석 지점을 삭제하는 기능도 포함합니다. Azure File Sync 엔지니어링 팀에서 지시한 경우가 아니면 이 명령을 실행하지 않도록 합니다. 이 명령을 실행하면 데이터가 손실될 수 있습니다. 

* <a id="afs-recall-file"></a>**사용하려는 파일이 계층화되어 있습니다. 로컬로 사용하기 위해 디스크로 회수하려면 어떻게 해야 하나요?**  
    디스크로 파일을 회수하는 가장 쉬운 방법은 여는 것입니다. Azure File Sync 파일 시스템 필터(StorageSync.sys)는 사용자가 별다른 작업을 수행하지 않아도 Azure 파일 공유에서 파일을 원활하게 다운로드합니다. 부분적으로 읽을 수 있는 파일 형식(예: 멀티미디어 또는 zip 파일)의 경우 파일을 열면 전체 파일이 다운로드되지 않습니다.

    PowerShell을 사용하여 파일을 강제로 회수할 수도 있습니다. 예를 들어 많은 파일을 한 번에 회수하려는 경우 이 방법이 유용할 수 있습니다(예: 폴더 내의 모든 파일). Azure File Sync가 설치되어 있는 서버 노드로 PowerShell 세션을 열고 다음 PowerShell 명령을 실행합니다.
    
    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncFileRecall -Path <file-or-directory-to-be-recalled>
    ```

* <a id="afs-force-tiering"></a>**파일 또는 디렉터리를 강제로 계층화하려면 어떻게 해야 하나요?**  
    클라우드 계층화 기능을 사용하도록 설정하면 클라우드 끝점에 지정된 사용 가능한 볼륨 공간 비율에 맞게 마지막 액세스 및 수정 시간을 기준으로 파일이 자동으로 계층화됩니다. 그러나 파일을 강제로 계층화하려는 경우도 있을 수 있습니다. 예를 들어 장시간 다시 사용하지 않으려는 큰 파일을 저장하고 다른 파일/폴더를 위해 볼륨 공간을 확보하려는 경우에 이러한 강제 계층화가 유용할 수 있습니다. 다음 PowerShell 명령을 사용하여 강제로 계층화할 수 있습니다.

    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
    ```

* <a id="afs-files-excluded"></a>**어떤 파일과 폴더가 Azure File Sync에서 자동으로 제외되나요?**
    기본적으로 Azure File Sync는 다음 파일을 제외합니다.
    
    - desktop.ini
    - thumbs.db
    - ehthumbs.db
    - ~$\*.\*
    - \*.laccdb
    - \*.tmp
    - 635D02A9D91C401B97884B82B3BCDAEA.\*

    또한 다음 폴더는 기본적으로 제외됩니다.

    - \System Volume Information
    - \$RECYCLE.BIN
    - \SyncShareState

* <a id="afs-os-support"></a>**Windows Server 2008 R2, Linux 또는 NAS 장치에서 Azure File Sync를 사용하려고 합니다. Azure File Sync에서 지원되나요?**
    현재, Azure File Sync는 Windows Server 2016 및 Windows Server 2012 R2만 지원합니다. 지금은 알려드릴 수 있는 다른 계획이 없지만 고객의 요구에 따라 얼마든지 추가 플랫폼을 지원할 수도 있습니다. 지원 받고 싶은 플랫폼이 있으면 [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)에서 알려주세요.

## <a name="security-authentication-and-access-control"></a>보안, 인증 및 액세스 제어
* <a id="ad-support"></a>**Azure Files에서 Active Directory 기반 인증 및 액세스 제어를 지원하나요?**  
    Azure Files는 액세스 제어를 관리할 수 있는 두 가지 방법을 제공합니다.

    - SAS를 사용하면 지정된 시간 간격 동안 유효한 특정 권한을 가진 토큰을 생성할 수 있습니다. 예를 들어 만료 시간으로 10분이 지정된 파일에 대한 읽기 전용 액세스 권한이 있는 토큰을 생성할 수 있습니다. 이 토큰이 유효한 동안 이 토큰을 소유한 사람은 해당 10분 동안 해당 파일에 대한 읽기 전용 액세스 권한을 가집니다. SAS 키는 현재 REST API 또는 클라이언트 라이브러리를 통해서만 지원되므로 저장소 계정 키를 사용하여 SMB를 통해 Azure 파일 공유를 탑재해야 합니다.

    - Azure File Sync는 모든 ACL(AD 기반 또는 로컬)을 보존하고 동기화하는 모든 서버 끝점으로 복제합니다. Windows Server는 Active Directory에서 이미 인증을 받을 수 있으므로 Azure File Sync는 AD 기반 인증 및 ACL의 전체 지원이 제공될 때까지 유용한 일시적 해결책을 제공할 수 있습니다.

    Azure Files는 현재 Active Directory를 직접 지원하지 않습니다.

* <a id="encryption-at-rest"></a>**Azure 파일 공유가 미사용 암호화되도록 하려면 어떻게 해야 하나요?**  
    미사용 암호화는 모든 지역에서 기본적으로 활성화되도록 처리되고 있습니다. 이런 지역에서는 암호화를 사용하기 위해 아무 것도 할 필요가 없습니다. 다른 지역의 경우 [서버 쪽 암호화](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)를 참조하세요.

* <a id="access-via-browser"></a>**웹 브라우저를 사용하여 특정 파일에 대한 액세스를 제공하려면 어떻게 해야 하나요?**  
    SAS를 사용하면 지정된 시간 간격 동안 유효한 특정 권한을 가진 토큰을 생성할 수 있습니다. 예를 들어, 특정 기간 동안 특정 파일에 대해 읽기 전용 액세스 권한이 있는 토큰을 생성할 수 있습니다. 이 URL을 소유한 사람은 토큰이 유효한 동안 모든 웹 브라우저에서 파일에 직접 액세스할 수 있습니다. 저장소 탐색기 같은 UI에서 SAS 키를 쉽게 생성할 수 있습니다.

* <a id="file-level-permissions"></a>**공유 내 폴더에 대한 읽기 전용 또는 쓰기 전용 권한을 지정할 수 있나요?**  
    SMB를 통해 파일 공유를 마운트하는 경우 이 수준의 사용 권한 제어는 없습니다. 그러나 REST API 또는 클라이언트 라이브러리를 통해 공유 액세스 서명(SAS)를 만들어 이 목적을 달성할 수 있습니다.

* <a id="ip-restrictions"></a>**Azure 파일 공유에 대한 IP 제한을 구현할 수 있나요?**  
    예, Azure 파일 공유에 대한 액세스는 저장소 계정 수준에서 제한할 수 있습니다. 자세한 내용은 [Azure Storage 방화벽 및 Virtual Network 구성](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)을 참조하세요.

* <a id="data-compliance-policies"></a>**Azure Files에서 지원되는 데이터 규정 준수 정책은 무엇인가요?**  
   Azure Files는 Azure Storage의 다른 저장소 서비스와 동일한 저장소 아키텍처를 기반으로 하여 실행되며 동일한 데이터 규정 준수 정책을 적용합니다. Azure Storage 데이터 규정 준수에 대한 자세한 내용은 [Microsoft Azure Data Protection 문서](http://go.microsoft.com/fwlink/?LinkID=398382&clcid=0x409) 및 [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/default.aspx)를 다운로드하여 참조할 수 있습니다.

## <a name="on-premises-access"></a>온-프레미스 액세스
* <a id="expressroute-not-required"></a>**Azure Files에 연결하거나 온-프레미스에서 Azure File Sync를 사용하려면 Azure ExpressRoute를 사용해야 하나요?**  
    아니요. ExpressRoute는 Azure 파일 공유에 액세스하는 데 필요하지 않습니다. Azure 파일 공유를 온-프레미스에 직접 탑재하는 경우 인터넷 액세스를 위해 포트 445(TCP 아웃바운드)만 열어 두면 됩니다(SMB가 이 포트를 통해 통신함). Azure File Sync를 사용하는 경우에는 HTTPS 액세스를 위해 포트 443(TCP 아웃바운드)만 열어 두면 됩니다(SMB 필요 없음). 그러나 원할 경우 ExpressRoute를 이러한 액세스 옵션과 함께 사용할 수 있습니다.

* <a id="mount-locally"></a>**내 로컬 컴퓨터에서 Azure 파일 공유를 탑재하려면 어떻게 해야 하나요?**  
    445 포트(TCP 아웃바운드)가 열려 있고 클라이언트(예: Windows 10 또는 Windows Server 2016 사용)에서 SMB 3.0 프로토콜을 지원하는 한 SMB 프로토콜을 통해 파일 공유를 탑재할 수 있습니다. 포트 445가 조직의 정책이나 ISP에 의해 차단되어 있으면 Azure File Sync를 사용하여 Azure 파일 공유에 액세스할 수 있습니다.

## <a name="backup"></a>백업
* <a id="backup-share"></a>**내 Azure 파일 공유를 백업하려면 어떻게 하나요?**  
    실수로 삭제하지 않도록 보호하기 위해 주기적인 [공유 스냅숏(미리 보기)](storage-how-to-use-files-snapshots.md)을 사용할 수 있습니다. 탑재된 파일 공유를 백업할 수 있는 AzCopy, RoboCopy 또는 타사 백업 도구를 사용할 수 있습니다. 

## <a name="share-snapshots"></a>공유 스냅숏
### <a name="share-snapshots---general"></a>공유 스냅숏 - 일반
* <a id="what-are-snaphots"></a>**파일 공유 스냅숏이란?**  
    Azure 파일 공유 스냅숏을 사용하여 파일 공유의 읽기 전용 버전을 만들 수 있습니다. 또한 이전 버전의 콘텐츠를 같은 공유 또는 Azure 또는 온-프레미스의 대체 위치에 복사하여 추가로 수정할 수도 있습니다. 공유 스냅숏에 대해 더 자세히 알아보려면 [공유 스냅숏 개요](storage-snapshots-files.md)를 참조하세요.

* <a id="where-are-snapshots-stored"></a>**내 공유 스냅숏은 어디에 저장되나요?**  
    공유 스냅숏은 파일 공유와 동일한 저장소 계정에 저장됩니다.

* <a id="snapshot-perf-impact"></a>**성능에 미치는 영향이 있나요?**  
    공유 스냅숏은 성능 오버헤드를 발생하지 않습니다.

* <a id="snapshot-consistency"></a>**공유 스냅숏은 응용 프로그램 일치 스냅숏인가요?**  
    아니요. 공유 스냅숏은 응용 프로그램 일치 스냅숏이 아닙니다. 사용자는 공유 스냅숏을 작성하기 전에 응용 프로그램에서 공유로 쓰기를 플러시해야 합니다.

* <a id="snapshot-limits"></a>**공유 스냅숏 수에 제한이 있나요?**  
    Azure Files가 유지할 수는 공유 스냅숏 수는 200개로 제한됩니다. 공유 스냅숏은 공유 할당량에 포함되지 않으므로 모든 공유 스냅숏이 사용하는 총 공간에 대한 공유별 제한은 없습니다. 저장소 계정 제한은 여전히 적용됩니다. 공유 스냅숏이 200개를 넘는 경우 새 공유 스냅숏을 생성하려면 이전 스냅숏을 삭제해야 합니다.

### <a name="create-share-snapshots"></a>공유 스냅숏 만들기
* <a id="file-snaphsots"></a>**개별 파일의 공유 스냅숏을 만들 수 있나요?**  
    공유 스냅숏은 파일 공유 수준에서 만들어집니다. 파일 공유 스냅숏에서 개별 파일을 복원할 수 있지만 파일 수준 공유 스냅숏을 만들 수 없습니다. 그러나 공유 수준 공유 스냅숏을 작성했으며 특정 파일이 변경된 공유 스냅숏을 나열하려면 Windows 탑재 공유의 "이전 버전" 환경에서 이 작업을 수행할 수 있습니다. 파일 스냅숏 기능이 필요하면 [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)에 알려주세요.

* <a id="encypted-snapshots"></a>**암호화된 파일 공유의 공유 스냅숏을 만들 수 있나요?**  
    미사용 암호화가 사용하도록 설정된 Azure 파일 공유의 공유 스냅숏을 작성할 수 있습니다. 공유 스냅숏에서 암호화된 파일 공유로 파일을 복원할 수 있습니다. 공유가 암호화된 경우 공유 스냅숏도 암호화됩니다.

* <a id="geo-redundant-snaphsots"></a>**공유 스냅숏이 지역 중복 스냅숏인가요?**
    공유 스냅숏은 생성된 Azure 파일 공유와 동일한 중복성을 갖습니다. 계정에 대해 GRS(지역 중복 저장소)를 선택한 경우 공유 스냅숏이 페어링된 지역에도 중복해서 저장됩니다.

### <a name="manage-share-snapshots"></a>공유 스냅숏 관리
* <a id="browse-snapshots-linux"></a>**Linux에서 내 공유 스냅숏을 찾아볼 수 있나요?**  
    Azure CLI 2.0을 사용하여 Linux에서 생성, 나열, 검색 및 복원할 수 있습니다.

* <a id="copy-snapshots-to-other-storage-account"></a>**다른 저장소 계정으로 공유 스냅숏을 복사할 수 있나요?**  
    공유 스냅숏의 파일을 다른 위치로 복사할 수 있지만 공유 스냅숏 자체는 복사할 수 없습니다.

### <a name="restore-data-from-share-snapshots"></a>공유 스냅숏에서 데이터 복원
* <a id="promote-share-snapshot"></a>**공유 스냅숏을 기본 공유로 승격할 수 있나요?**  
    공유 스냅숏의 데이터를 원하는 대상으로 복사할 수만 있습니다. 그렇지만 공유 스냅숏을 기본 공유로 승격할 수 없습니다.

* <a id="restore-snapshotted-file-to-other-share"></a>**공유 스냅숏의 데이터를 다른 저장소 계정으로 복원할 수 있나요?**  
    예. 공유 스냅숏의 파일을 원본 위치 또는 같거나 다른 지역에서 같거나 다른 저장소 계정을 포함하는 대체 위치로 복사할 수 있습니다. 또한 파일을 온-프레미스 또는 다른 클라우드로 복사할 수도 있습니다.    
  
### <a name="cleanup-share-snapshot"></a>공유 스냅숏 정리
* <a id="delete-share-keep-snapshots"></a>**공유 스냅숏을 제외하고 내 공유만 삭제할 수 있나요?**
    공유에 활성 공유 스냅숏이 있는 경우 공유를 삭제할 수 없습니다. 공유와 함께 공유 스냅숏을 삭제하는 데 사용할 수 있는 API가 있으며 Azure Portal에서도 동일한 작업을 수행할 수 있습니다.

* <a id="delete-share-with-snapshots"></a>**저장소 계정을 삭제하는 경우 공유 스냅숏은 어떻게 되나요?**
    저장소 계정을 삭제하는 경우 공유 스냅숏도 삭제됩니다.

## <a name="billing-and-pricing"></a>가격 및 대금 청구
* <a id="vm-file-share-network-traffic"></a>**Azure VM과 Azure 파일 공유 사이의 네트워크 트래픽은 구독에 청구되는 외부 대역폭으로 간주되나요?**  
    파일 공유와 VM이 동일한 Azure 지역에 있으면 이들 간의 트래픽은 무료입니다. 서로 다른 지역에 있는 경우 이들 간의 트래픽은 외부 대역폭으로 청구됩니다.

* <a id="share-snapshot-price"></a>**스냅숏 공유 비용은 얼마나 되나요?**
     미리 보기 동안 스냅숏 용량 공유에는 요금이 청구되지 않습니다. 표준 저장소 송신 및 트랜잭션 비용이 적용됩니다. 일반 공급 후에는 공유 스냅숏에 대한 용량 및 트랜잭션에 요금이 청구될 예정입니다.
     
     공유 스냅숏은 기본적으로 증분합니다. 기본 공유 스냅숏은 공유 자체입니다. 모든 후속 공유 스냅숏은 증분하며, 이전 공유 스냅숏과의 차이만 저장합니다. 변경된 콘텐츠에 대해서만 요금이 청구됩니다. 100GiB의 데이터 공유가 있으나 마지막 공유 스냅숏 후에 5GiB만 변경된 경우 해당 공유 스냅숏은 추가로 5GiB만 사용하게 되며 105GiB에 대한 요금만 청구됩니다. 트랜잭션 및 표준 송신 요금에 대한 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/files/)를 참조하세요.

## <a name="scale-and-performance"></a>확장 및 성능
* <a id="files-scale-limits"></a>**Azure Files의 크기 제한은 무엇인가요?**  
    Azure Files의 확장성 및 성능 목표에 대한 자세한 내용은 [Azure Files 확장성 및 성능 목표](storage-files-scale-targets.md)를 참조하세요.

* <a id="need-larger-share"></a>**현재 Azure Files 제품보다 큰 파일 공유가 필요합니다. Azure 파일 공유의 크기를 늘릴 수 있나요?**  
    아니요, Azure 파일 공유의 최대 크기는 5TiB입니다. 현재 조정할 수 없는 하드 한도입니다. 공유 크기를 100TiB로 늘리기 위한 솔루션을 개발 중이지만 지금은 일정이 나와 있지 않습니다.

* <a id="open-handles-quota"></a>**동일한 파일에 동시에 액세스할 수 있는 클라이언트는 몇 개인가요?**  
    단일 파일에 대한 열린 핸들 할당량은 2000개입니다. 열린 핸들 수가 2000개가 된 후에는 할당량에 도달했다는 오류가 표시됩니다.

* <a id="zip-slow-performance"></a>**Azure Files에 파일의 압축을 풀려고 하면 성능이 느려집니다. 어떻게 해야 하나요?**  
    많은 파일을 Azure Files로 전송하려면 네트워크 전송을 위해 최적화된 도구인 AzCopy(Windows, Linux/Unix 용 미리 보기) 또는 Azure PowerShell을 사용하는 것이 좋습니다.

* <a id="slow-perf-windows-81-2012r2"></a>**Windows Server 2012 R2 또는 Windows 8.1에 Azure 파일 공유를 탑재했더니 성능이 느려졌습니다. 이유가 무엇인가요?**  
    Windows Server 2012 R2 및 Windows 8.1에서 Azure 파일 공유를 탑재할 때 알려진 문제가 발생합니다. 이 문제는 Windows 8.1 및 Windows Server 2012 R2용 2014년 4월 누적 업데이트에서 패치되었습니다. 최적의 성능을 위해 Windows Server 2012 R2 및 Windows 8.1의 모든 인스턴스에 이 패치가 적용되었는지 확인하세요(물론 Windows 업데이트를 통해 모든 Windows 패치를 적용하는 것이 권장됨). 자세한 내용은 관련된 KB 문서인 [Windows 8.1 또는 Server 2012 R2에서 Azure Files에 액세스할 때 성능 저하](https://support.microsoft.com/kb/3114025)를 확인하세요.

## <a name="features-and-interoperability-with-other-services"></a>다른 서비스와의 기능 및 상호 운용성
* <a id="cluster-witness"></a>**내 Windows Server 장애 조치(failover) 클러스터에 대해 Azure 파일 공유를 *파일 공유 감시*로 사용할 수 있나요?**  
    현재 이 기능은 Azure 파일 공유에서 지원되지 않습니다. Azure Blob Storage에 대해 이 기능을 설정하는 방법에 대한 자세한 내용은 [장애 조치(Failover) 클러스터에 대한 클라우드 감시 배포](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)를 참조하세요.

* <a id="containers"></a>**Azure Container Instance에 Azure 파일 공유를 탑재할 수 있나요?**  
    예, Azure 파일 공유는 컨테이너 인스턴스의 수명을 넘어 정보를 유지할 수 있는 탁월한 옵션입니다. 자세한 내용은 [Azure Container Instances를 사용하여 Azure 파일 공유 탑재](../../container-instances/container-instances-mounting-azure-files-volume.md)를 참조하세요.

* <a id="rest-rename"></a>**REST API에 이름 바꾸기 작업이 있나요?**  
    지금은 없습니다.

* <a id="nested-shares"></a>**중첩된 공유 즉, 공유 밑에 공유를 지정할 수 있나요?**  
    아니요. 파일 공유는 마운트할 수 있는 가상 드라이버이므로 포함된 공유는 지원되지 않습니다.

* <a id="ibm-mq"></a>**IBM MQ에서 Azure Files를 사용하고 있습니다.**  
    IBM은 이 서비스로 Azure Files를 구성할 때 IBM MQ 고객에게 안내하는 문서를 발표했습니다. 자세한 내용은 [Microsoft Azure 파일 서비스와 IBM MQ 다중 인스턴스 큐 관리자를 설치하는 방법](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service)을 참조하세요.

## <a name="see-also"></a>참고 항목
* [Windows에서 Azure Files 문제 해결](storage-troubleshoot-windows-file-connection-problems.md)
* [Linux에서 Azure Files 문제 해결](storage-troubleshoot-linux-file-connection-problems.md)
* [Azure 파일 동기화(미리 보기) 문제 해결](storage-sync-files-troubleshoot.md)