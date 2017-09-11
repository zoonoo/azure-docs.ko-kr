---
title: "Azure File Storage 소개 | Microsoft Docs"
description: "Microsoft Cloud에서 네트워크 파일 공유를 제공하는 Azure File Storage 소개"
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
ms.topic: get-started-article
ms.date: 05/27/2017
ms.author: renash
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 498af5cffb76e026c9a87127cab238f0f23b668a
ms.contentlocale: ko-kr
ms.lasthandoff: 08/21/2017

---

# <a name="introduction-to-azure-file-storage"></a>Azure File Storage 소개

Azure File Storage는 업계 표준 [SMB(서버 메시지 블록) 프로토콜](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) 및 [CIFS(Common Internet File System)](https://technet.microsoft.com/library/cc939973.aspx)를 사용하여 클라우드에 네트워크 파일 공유를 제공합니다. Azure 파일 공유는 Windows, macOS, Linux를 실행하는 Azure Virtual Machines 또는 온-프레미스 환경에서 동시에 탑재할 수 있습니다. 범용 저장소 계정을 사용하여 Azure File Storage, Azure Blob Storage 및 Azure Queue Storage에 액세스할 수 있습니다.

## <a name="videos"></a>비디오
| Azure File Storage 소개(27분) | Azure File Storage 자습서(5분)  |
|-|-|
| [![Azure File Storage 소개 비디오의 동영상 가이드 - 재생하려면 클릭하세요.](./media/storage-files-introduction/azure-files-introduction-video-snapshot1.png)](https://www.youtube.com/watch?v=zlrpomv5RLs) | [![Azure File Storage 자습서 비디오의 동영상 가이드 - 재생하려면 클릭하세요.](./media/storage-files-introduction/azure-files-introduction-video-snapshot2.png)](https://channel9.msdn.com/Blogs/Azure/Azure-File-storage-with-Windows/) |

## <a name="why-azure-file-storage-is-useful"></a>Azure File Storage가 유용한 이유

Azure File Storage를 사용하면 온-프레미스 또는 클라우드에서 호스팅되는 Windows Server, Linux 또는 NAS 기반 파일 서버를 OS가 필요 없는 클라우드 파일 공유로 바꿀 수 있습니다. Azure 파일 저장소에는 다음과 같은 장점이 있습니다.

* **공유 액세스** Azure 파일 공유는 산업 표준 SMB 프로토콜을 지원합니다. 즉, 응용 프로그램 호환성에 대한 걱정 없이 온-프레미스 파일 공유를 Azure 파일 공유로 원활하게 바꿀 수 있습니다. 여러 컴퓨터, 응용 프로그램/인스턴스의 파일 시스템에 액세스할 수 있다는 것은 Azure File Storage의 중요한 장점입니다.

* **완전히 관리** 하드웨어 또는 OS를 관리할 필요 없이 Azure 파일 공유를 만들 수 있습니다. 즉, 중요한 보안 업그레이드로 서버 OS를 패치하거나 결함이 있는 하드 디스크를 교체할 필요가 없습니다.

* **스크립팅 및 도구** PowerShell cmdlet 및 Azure CLI를 사용하여 Azure 응용 프로그램 관리의 일부로 Azure 파일 공유를 만들고 탑재하고 관리할 수 있습니다. [Azure Portal](https://portal.azure.com) 및 [Azure Storage 탐색기](https://storageexplorer.com)를 사용하여 Azure 파일 공유를 만들고 관리할 수 있습니다. 

* **복원력** Azure File Storage는 처음부터 항상 사용할 수 있도록 빌드되었습니다. 온-프레미스 파일 공유를 Azure File Storage로 바꾸는 경우 더 이상 로컬 정전 또는 네트워크 문제를 처리하는 데 주의할 필요가 없습니다. 

* **친숙한 프로그래밍** Azure에서 실행 중인 응용 프로그램은 [파일 시스템 I/O API](https://msdn.microsoft.com/library/system.io.file.aspx)를 통해 공유 데이터에 액세스할 수 있습니다. 따라서 개발자는 기존의 코드와 기술을 이용하여 기존 응용 프로그램을 마이그레이션할 수 있습니다. 시스템 IO API 외에도 [.NET](/dotnet/api/overview/azure/storage?view=azure-dotnet) 또는 [Azure Storage REST API](/rest/api/storageservices/file-service-rest-api)용 Azure Storage 클라이언트 라이브러리 중 하나를 사용할 수 있습니다.

Azure 파일 공유를 사용하여 다음을 수행할 수 있습니다.

* **온-프레미스 파일 서버 바꾸기** Azure File Storage는 기존의 온-프레미스 파일 서버 또는 NAS 장치에서 파일 공유를 완전히 바꾸는 데 사용할 수 있습니다. Windows, macOS 및 Linux와 같이 자주 사용되는 운영 체제는 전세계 어디서나 Azure File 공유를 쉽게 탑재할 수 있습니다.

* **응용 프로그램 "전환"**

    Azure File Storage를 사용하면 온-프레미스 파일 공유를 통해 응용 프로그램의 구성 요소 간에 데이터를 공유하는 클라우드로 응용 프로그램을 손쉽게 "리프트 앤 시프트"할 수 있습니다. 이렇게 구현하려면 각 VM을 파일 공유에 연결한 다음 온-프레미스 파일 공유와 마찬가지로 파일을 읽고 쓸 수 있습니다.

* **클라우드 개발 간소화**
    
    Azure File Storage는 새로운 클라우드 개발 프로젝트를 간소화하기 위해 다양한 방법으로 사용할 수 있습니다.
    
    * **공유 응용 프로그램 설정**
    
        분산 응용 프로그램의 일반적인 패턴은 다양한 가상 컴퓨터에서 액세스할 수 있는 중앙 위치에 구성 파일을 저장하는 것입니다. 이제 이러한 구성 파일을 Azure 파일 공유에 저장하고 모든 응용 프로그램 인스턴스에서 읽을 수 있습니다. 또한 이러한 설정은 REST 인터페이스를 통해 관리할 수 있습니다. 이 인터페이스를 사용하면 전 세계에서 구성 파일에 액세스할 수 있습니다.

    * **진단 공유**
    
        Azure 파일 공유는 로그, 메트릭 및 크래시 덤프와 같은 진단 파일을 저장하는 데 사용할 수도 있습니다. SMB 및 REST 인터페이스 모두를 통해 파일 공유를 사용하도록 설정하면 응용 프로그램에서 진단 데이터를 처리 및 분석하기 위한 다양한 분석 도구를 빌드하거나 활용할 수 있습니다.

    * **개발/테스트/디버그**

        개발자 또는 관리자가 클라우드의 VM에서 작업할 때 종종 도구 또는 유틸리티 모음이 필요합니다. 필요로 하는 각 가상 컴퓨터에 이러한 유틸리티를 설치 및 배포하는 데에는 시간이 많이 걸릴 수 있습니다. Azure File Storage를 사용하면 개발자 또는 관리자가 가상 컴퓨터에서 쉽게 연결할 수 있는 파일 공유에 자신이 좋아하는 도구를 저장할 수 있습니다.
        
## <a name="how-does-it-work"></a>작동 원리

Azure 파일 공유 관리는 온-프레미스 파일 공유 관리보다 훨씬 간단합니다. 다음 다이어그램에서는 Azure File Storage 관리 구조를 보여 줍니다.

![파일 구조](./media/storage-files-introduction/files-concepts.png)

* **저장소 계정** Azure Storage에 대한 모든 액세스는 저장소 계정을 통해 수행됩니다. 저장소 계정 용량에 대한 자세한 내용은 [확장성 및 성능 목표](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)를 참조하세요.

* **공유** File Storage 공유는 Azure의 SMB 파일 공유입니다. 모든 디렉터리 및 파일을 부모 공유에 만들어야 합니다. 계정에 포함할 수 있는 공유 수에는 제한이 없으며, 공유에 저장할 수 있는 파일 수에는 파일 공유의 최대 5TB의 최대 용량까지 제한이 없습니다.

* **디렉터리** 선택적인 디렉터리 계층 구조입니다.

* **파일** 공유에 있는 파일입니다. 파일 1개의 크기는 최대 1TB일 수 있습니다.

* **URL 형식** 다음 URL 형식을 사용하여 파일에 주소를 지정할 수 있습니다.  

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory/directories>/<file>
    ```

## <a name="next-steps"></a>다음 단계

* [Azure 파일 공유 만들기](storage-how-to-create-file-share.md)
* [Windows에 연결 및 탑재](storage-how-to-use-files-windows.md)
* [Linux에 연결 및 탑재](storage-how-to-use-files-linux.md)
* [macOS에 연결 및 탑재](storage-how-to-use-files-mac.md)
* [FAQ](../storage-files-faq.md)
* [Windows에서 문제 해결](storage-troubleshoot-windows-file-connection-problems.md)   
* [Linux에서 문제 해결](storage-troubleshoot-linux-file-connection-problems.md)   

<!-- Rena I would remove any articles from here that are more than a year old. - Robin-->
### <a name="conceptual-articles-and-videos"></a>개념 문서 및 비디오
* [Azure File Storage: 원활한 Windows 및 Linux용 클라우드 SMB 파일 시스템(영문)](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)

### <a name="tooling-support-for-azure-file-storage"></a>Azure File Storage용 도구 지원
* [Microsoft Azure Storage와 함께 AzCopy를 사용하는 방법](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Azure Storage에서 Azure CLI 사용](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)

### <a name="blog-posts"></a>블로그 게시물
* [Azure 파일 저장소 일반적으로 사용 가능(영문)](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Azure File Storage 내부 구조(영문)](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Microsoft Azure 파일 서비스 소개](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Azure 파일로 데이터 마이그레이션(영문)](https://azure.microsoft.com/blog/migrating-data-to-microsoft-azure-files/)

### <a name="reference"></a>참조
* [Storage Client Library for .NET 참조](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [파일 서비스 REST API 참조](http://msdn.microsoft.com/library/azure/dn167006.aspx)

