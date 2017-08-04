---
title: "Azure File Storage 보안에 대한 FAQ(질문과 대답) | Microsoft Docs"
description: "Azure File Storage에 대해 자주 묻는 질문과 대답(FAQ)을 확인합니다."
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
ms.date: 07/19/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: cb2134502a585c4f9772e594f635c10f1841e46f
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---
# <a name="frequently-asked-questions-about-azure-file-storage"></a>Azure File Storage 보안에 대한 FAQ(질문과 대답)

## <a name="general"></a>일반
* **Q. Azure File Storage란?**  
   
    Azure File Storage는 Azure의 분산 파일 시스템입니다. 지원되는 Azure Virtual Machine 또는 온-프레미스 컴퓨터에서 저장소를 네이티브 공유로 탑재할 수 있는 SMB 프로토콜 인터페이스를 제공합니다.

* **Q. Azure File Storage가 유용한 이유는 무엇인가요?**  
   
    Azure File Storage는 여러 VM 및 플랫폼 간에 공유 데이터 액세스를 제공합니다. [Azure File Storage가 유용한 이유](storage-files-introduction.md#why-azure-file-storage-is-useful)를 참조하세요.

* **Q. Azure File, Azure Blob 및 Azure Disk는 언제 사용해야 하나요?**  
   
    Microsoft Azure에서는 클라우드에 데이터를 저장하고 액세스하는 여러 방법을 제공합니다.  
   
    Azure File Storage - 어디서나 저장된 파일에 쉽게 액세스할 수 있는 SMB 인터페이스, 클라이언트 라이브러리 및 REST 인터페이스를 제공합니다.  
   
    Azure Blobs - 구조화되지 않은 데이터를 블록 Blob에서 대규모로 저장하고 액세스할 수 있는 클라이언트 라이브러리 및 REST 인터페이스를 제공합니다.  
   
    Azure Data Disks - 연결된 가상 하드 디스크에서 데이터를 영구적으로 저장하고 액세스할 수 있는 클라이언트 라이브러리 및 REST 인터페이스를 제공합니다.  
   
    [Azure Blobs, Azure Files 또는 Azure Data Disks를 사용할 시기 결정](storage-decide-blobs-files-disks.md)에 대해 자세히 알아보세요.

* **Q. Azure File Storage로 시작하려면 어떻게 해야 하나요?**  
   
    Azure 파일 공유를 만들어 시작할 수 있습니다. Azure Portal, Azure Storage PowerShell cmdlet, Azure Storage 클라이언트 라이브러리 또는 Azure Storage REST API를 사용하여 Azure 파일 공유를 만들 수 있습니다. 이 자습서에서는 다음 항목에 대해 알아봅니다.

    * [Azure Portal을 사용하여 Azure 파일 공유를 만드는 방법](storage-file-how-to-create-file-share.md#create-file-share-through-the-portal)
    * [Powershell을 사용하여 Azure 파일 공유를 만드는 방법](storage-file-how-to-create-file-share.md#create-file-share-through-powershell)
    * [CLI를 사용하여 Azure 파일 공유를 만드는 방법](storage-file-how-to-create-file-share.md#create-file-share-through-command-line-interface-cli)

* **Q. Azure File Storage는 어떤 복제를 지원하나요?**  
   
    Azure File Storage는 현재 LRS 또는 GRS만 지원합니다. 우리는 RA-GRS를 지원할 계획이지만 아직 공유할 일정이 없습니다.

## <a name="security-authentication-and-access-control"></a>보안, 인증 및 액세스 제어

* **Q. Azure File Storage에서 파일에 액세스하는 다른 방법은 무엇인가요?**
    
    SMB 3.0 프로토콜을 사용하여 로컬 컴퓨터에 파일 공유를 탑재하거나 [저장소 탐색기](http://storageexplorer.com/)와 같은 도구를 사용하여 파일 공유의 파일을 액세스할 수 있습니다. 응용 프로그램에서 저장소 클라이언트 라이브러리, REST API 또는 Powershell을 사용하여 Azure 파일 공유에서 파일에 액세스할 수 있습니다.

* **Q. 웹 브라우저를 통해 특정 파일에 대한 액세스를 제공하려면 어떻게 해야 하나요?**
    
    SAS를 사용하면 지정된 시간 간격 동안 유효한 특정 권한을 가진 토큰을 생성할 수 있습니다. 예를 들어, 특정 기간 동안 특정 파일에 대해 읽기 전용 액세스 권한이 있는 토큰을 생성할 수 있습니다. 이 URL을 소유한 사람은 토큰이 유효한 동안 모든 웹 브라우저에서 파일에 직접 액세스할 수 있습니다. 저장소 탐색기 같은 UI에서 SAS 키를 쉽게 생성할 수 있습니다.

* **Q. 공유 내의 폴더에 대한 읽기 전용 또는 쓰기 전용 권한을 지정할 수 있나요?**
    
    SMB를 통해 파일 공유를 마운트하는 경우 이 수준의 사용 권한 제어는 없습니다. 그러나 REST API 또는 클라이언트 라이브러리를 통해 공유 액세스 서명(SAS)를 만들어 이 목적을 달성할 수 있습니다.  

* **Q. Azure File Storage에 대한 서버 쪽 암호화를 사용하려면 어떻게 해야 하나요?**

    Azure File Storage에 대한 [서버 쪽 암호화](storage-service-encryption.md)는 일반적으로 모든 지역과 공용 및 국가별 클라우드에서 사용할 수 있습니다. [Azure Portal](https://portal.azure.com/), [Microsoft Azure Storage 리소스 공급자 API](/rest/api/storagerp/storageaccounts), [Azure Powershell](https://msdn.microsoft.com/library/azure/mt607151.aspx) 또는 [Azure CLI](storage-azure-cli.md)를 사용하여 Azure File Storage에 대한 SSE를 사용하도록 설정할 수 있습니다.
    
    Azure File Storage에서 SSE를 사용하도록 설정한 후에는 해당 저장소 계정의 파일 저장소에 기록된 모든 새 데이터가 자동으로 암호화됩니다. 이 기능은 기존 또는 새 저장소 계정에서 기존 또는 새 공유에 기록된 모든 새로운 데이터에 대해 사용할 수 있습니다. 이 기능을 사용하는 추가 비용은 없습니다. [Azure File Storage에서 SSE를 사용하도록 설정하는 방법](storage-service-encryption.md)에 대해 자세히 알아보세요.

* **Q. Azure File Storage에서 Active Directory 기반 인증을 지원하나요?**
   
    우리는 현재 AD 기반 인증 또는 ACL을 지원하지 않지만 우리의 기능 요청 목록에 해당 기능을 포함합니다. 현재 Azure 저장소 계정 키는 파일 공유에 대한 인증을 제공합니다. REST API 또는 클라이언트 라이브러리를 통해 공유 액세스 서명(SAS)을 사용하여 해결 방법을 제공합니다. SAS를 사용하면 지정된 시간 간격 동안 유효한 특정 권한을 가진 토큰을 생성할 수 있습니다. 예를 들어 만료 시간으로 10분이 지정된 파일에 대한 읽기 전용 액세스 권한이 있는 토큰을 생성할 수 있습니다. 이 토큰이 유효한 동안 이 토큰을 소유한 사람은 해당 10분 동안 해당 파일에 대한 읽기 전용 액세스 권한을 가집니다.
   
    SAS는 REST API 또는 클라이언트 라이브러리를 통해서만 지원됩니다. SMB 프로토콜을 통해 파일 공유를 탑재하면 SAS를 사용하여 해당 콘텐츠에 대한 액세스 권한을 위임할 수 없습니다. 
    
* **Q. Azure File Storage에서 지원되는 데이터 규정 준수 정책이란?**

   Azure File Storage는 Azure Storage의 다른 저장소 서비스와 동일한 저장소 아키텍처에 기반하여 실행되며 동일한 데이터 규정 준수 정책을 적용합니다. Azure Storage 데이터 규정 준수에 대한 자세한 내용은 [Microsoft Azure Data Protection 문서](http://go.microsoft.com/fwlink/?LinkID=398382&clcid=0x409)를 다운로드하여 참조할 수 있습니다.

## <a name="on-premises-access"></a>온-프레미스 액세스

* **Q. Azure ExpressRoute를 사용하여 온-프레미스 VM에서 Azure File Storage에 연결해야 하나요?**
   
    아니요. ExpressRoute가 없더라도 인터넷 액세스를 위해 포트 445(TCP 아웃바운드)를 열어 놓기만 하면 온-프레미스에서 여전히 파일 공유에 액세스할 수 있습니다. 그러나 원하는 경우 파일 저장소와 함께 ExpressRoute를 사용할 수 있습니다.

* **Q. 내 로컬 컴퓨터에서 Azure 파일 공유를 탑재하려면 어떻게 해야 하나요?** 
    
    445 포트(TCP 아웃바운드)가 열려 있고 클라이언트(예: Windows 10 또는 Windows Server 2012 사용)에서 SMB 3.0 프로토콜을 지원하는 한 SMB 프로토콜을 통해 파일 공유를 탑재할 수 있습니다. 로컬 ISP 공급자를 사용하여 포트의 차단을 해제합니다. 중간에 [저장소 탐색기](../vs-azure-tools-storage-explorer-files.md#view-a-file-shares-contents)를 사용하여 파일을 볼 수 있습니다.


## <a name="billing-and-pricing"></a>가격 및 대금 청구
* **Q. Azure VM과 파일 공유 사이의 네트워크 트래픽은 구독에 청구되는 외부 대역폭으로 간주되나요?**
   
    파일 공유와 VM이 동일한 Azure 지역에 있으면 이들 간의 트래픽은 무료입니다. 서로 다른 지역에 있는 경우 이들 간의 트래픽은 외부 대역폭으로 청구됩니다.

## <a name="backup"></a>백업

* **Q. 내 Azure 파일 공유를 백업하려면 어떻게 하나요?**
    
    탑재된 파일 공유를 백업할 수 있는 AzCopy, RoboCopy 또는 타사 백업 도구를 사용할 수 있습니다. 가까운 장래에 파일 공유의 스냅숏을 만들 수 있을 것으로 예상합니다. 이 기능을 사용하여 Azure 파일 공유를 백업할 수 있습니다.

## <a name="performance"></a>성능

* **Q. Azure File Storage의 크기 제한은 무엇인가요?**
    Azure File Storage의 확장성 및 성능 목표에 대한 자세한 내용은 [Azure 저장소 확장성 및 성능 목표](storage-scalability-targets.md#scalability-targets-for-blobs-queues-tables-and-files)를 참조하세요.

* **Q. Azure File Storage에 파일의 압축을 풀려고 하면 성능이 느려졌습니다. 어떻게 해야 하나요?**
    
    많은 파일을 Azure File Storage로 전송하려면 네트워크 전송을 위해 최적화된 도구인 AzCopy(Windows, Linux/Unix 용 미리 보기) 또는 Azure Powershell를 사용하는 것이 좋습니다.

* **Q. Azure File Storage의 성능 저하 문제를 해결하기 위해 어떤 패치가 릴리스되었나요?**
    
    Windows 팀은 고객이 Windows 8.1 또는 Windows Server 2012 R2에서 Azure File Storage에 액세스할 때 발생하는 성능 저하 문제를 해결하기 위한 패치를 최근에 출시했습니다. 자세한 내용은 관련된 KB 문서인 [Windows 8.1 또는 Server 2012 R2 Azure Files Storage에 액세스할 때 성능 저하](https://support.microsoft.com/kb/3114025)를 확인하세요.

## <a name="features-and-interoperability-with-other-services"></a>다른 서비스와의 기능 및 상호 운용성
* **Q. 장애 조치 클러스터에 대한 "파일 공유 감시"는 Azure File Storage의 사용 사례 중 하나인가요?**
   
    현재 지원되지 않습니다.

* **Q. REST API에 이름 바꾸기 작업이 있나요?**
   
    지금은 없습니다.

* **Q. 중첩된 공유, 즉 공유 아래에 공유를 가질 수 있나요?**
    
    아니요. 파일 공유는 마운트할 수 있는 가상 드라이버이므로 포함된 공유는 지원되지 않습니다.

* **Q. IBM MQ에서 Azure File Storage를 사용하고 있습니다.**
    
    IBM은 이 서비스로 Azure File Storage를 구성할 때 IBM MQ 고객에게 안내하는 문서를 발표했습니다. 자세한 내용은 [Microsoft Azure 파일 서비스와 IBM MQ 다중 인스턴스 큐 관리자를 설치하는 방법](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service)을 참조하세요.


## <a name="troubleshooting"></a>문제 해결
* **Q. Azure File Storage 오류를 어떻게 해결하나요?**
    
    종단 간 문제 해결 지침에 대해서는 [Azure File Storage 문제 해결 문서](storage-troubleshoot-file-connection-problems.md)를 참조할 수 있습니다. 


## <a name="see-also"></a>참고 항목
Azure 파일 저장소에 대한 자세한 내용은 다음 링크를 참조합니다.

### <a name="conceptual-articles-and-videos"></a>개념 문서 및 비디오
* [Azure File Storage: 원활한 Windows 및 Linux용 클라우드 SMB 파일 시스템(영문)](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Linux에서 Azure File Storage 사용 방법](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>파일 저장소용 도구 지원
* [Azure 저장소와 함께 Azure PowerShell 사용](storage-powershell-guide-full.md)
* [Microsoft Azure 저장소와 함께 AzCopy를 사용하는 방법](storage-use-azcopy.md)
* [Azure 저장소에서 Azure CLI 사용](storage-azure-cli.md)
* [Azure File Storage 문제 해결](storage-troubleshoot-file-connection-problems.md)

### <a name="blog-posts"></a>블로그 게시물
* [Azure 파일 저장소 일반적으로 사용 가능(영문)](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Azure File Storage 내부 구조(영문)](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Microsoft Azure 파일 서비스 소개](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Azure File Storage로 데이터 마이그레이션(영문)](https://azure.microsoft.com/blog/migrating-data-to-microsoft-azure-files/)

### <a name="reference"></a>참조
* [Storage Client Library for .NET 참조](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [파일 서비스 REST API 참조](http://msdn.microsoft.com/library/azure/dn167006.aspx)

