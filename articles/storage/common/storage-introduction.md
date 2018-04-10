---
title: Azure Storage 소개 | Microsoft Docs
description: 클라우드의 Microsoft 데이터 저장소인 Azure Storage 개요입니다.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: get-started-article
ms.date: 03/06/2018
ms.author: tamram
ms.openlocfilehash: 18a8065bba8a4a0ec2025d6b9134fe9fab21eb5f
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="introduction-to-microsoft-azure-storage"></a>Microsoft Azure Storage 소개

Microsoft Azure Storage는 가용성, 보안, 내구성, 확장성 및 중복성이 높은 저장소를 제공하는 Microsoft 관리 클라우드 서비스입니다. Microsoft는 유지 관리를 담당하고 사용자에 대한 중요한 문제를 처리합니다.

Azure Storage는 Blob Storage, File Storage 및 Queue Storage라는 세 개의 데이터 서비스로 구성됩니다. Blob Storage는 가장 빠른 성능을 가진 SSD를 사용하는 프리미엄 저장소에서 표준 및 프리미엄 저장소를 지원합니다. 다른 기능은 쿨 저장소이며 낮은 비용으로 자주 액세스하지 않는 대용량 데이터를 저장할 수 있습니다.

이 문서에서는 다음에 대해 알아봅니다.
* Azure Storage 서비스
* 저장소 계정 유형
* Blob, 큐 및 파일에 액세스
* 암호화
* 복제
* 저장소 간에 데이터 전송
* 사용할 수 있는 여러 저장소 클라이언트 라이브러리

Azure Storage를 사용하여 가동하고 실행하려면 [저장소 계정 만들기](storage-quickstart-create-account.md)를 참조하세요.

## <a name="introducing-the-azure-storage-services"></a>Azure Storage 서비스 소개

Blob Storage, File Storage 및 Queue Storage와 같은 Azure Storage에서 제공하는 서비스 중 하나를 사용하려면 먼저 저장소 계정을 만들고 해당 저장소 계정에서 특정 서비스 간에 데이터를 전송할 수 있습니다.

## <a name="blob-storage"></a>Blob 저장소

Blob은 기본적으로 사용자가 컴퓨터(또는 태블릿, 모바일 장치 등)에 저장한 항목과 같은 파일입니다. 사진, Microsoft Excel 파일, HTML 파일, 가상 하드 디스크(VHD), 로그와 같은 빅 데이터, 데이터베이스 백업 등 거의 모든 것이 가능합니다. Blob은 폴더와 유사한 컨테이너에 저장됩니다.

Blob Storage에 파일을 저장한 후에 URL, REST 인터페이스 또는 Azure SDK 저장소 클라이언트 라이브러리 중 하나를 사용하여 전 세계 어디에서든지 해당 파일에 액세스할 수 있습니다. Storage 클라이언트 라이브러리는 Node.js, Java, PHP, Ruby, Python 및 .NET을 비롯한 여러 언어에서 사용할 수 있습니다.

블록 Blob, 페이지 Blob(VHD 파일에 사용됨) 및 추가 Blob라는 세 가지 Blob 유형이 있습니다.

* 블록 Blob은 최대 약 4.7 TB의 일반 파일을 저장하는 데 사용됩니다.
* 페이지 Blob은 최대 8TB 크기의 임의 액세스 파일을 저장하는 데 사용됩니다. 이러한 Blob은 VM을 백업하는 VHD 파일에 사용됩니다.
* 추가 Blob은 블록 Blob과 같이 블록으로 구성되지만 추가 작업에 최적화되어 있습니다. 이러한 Blob은 여러 VM에서 동일한 Blob에 정보를 기록하는 등의 작업에 사용됩니다.

네트워크 제약 조건으로 인해 네트워크를 통해 Blob 저장소를 대상으로 데이터를 업로드하거나 다운로드하는 것이 불가능한 대규모 데이터 집합의 경우, 일련의 하드 드라이브를 Microsoft로 운송하여 데이터 센터에서 바로 데이터를 가져오거나 내보내도록 요청할 수 있습니다. [Microsoft Azure Import/Export 서비스를 사용하여 Blob Storage로 데이터 전송](../storage-import-export-service.md)을 참조하세요.

## <a name="azure-files"></a>Azure 파일
[Azure Files](../files/storage-files-introduction.md)를 사용하면 표준 SMB(서버 메시지 블록) 프로토콜을 사용하여 액세스할 수 있는 고가용성 네트워크 파일 공유를 설정할 수 있습니다. 즉, 여러 VM이 읽기 및 쓰기 권한을 모두 사용하여 동일한 파일을 공유할 수 있습니다. 또한 REST 인터페이스 또는 저장소 클라이언트 라이브러리를 사용하여 파일을 읽을 수 있습니다.

Azure Files가 회사 파일 공유의 파일과 다른 점 한 가지는 파일을 가리키고 SAS(공유 액세스 서명) 토큰을 포함하고 있는 URL을 사용하여 전 세계 어디서나 파일에 액세스할 수 있다는 것입니다. SAS 토큰은 생성 가능하며 특정 기간에 개인 자산에 대한 특정 액세스를 허용합니다.

파일 공유는 다음과 같은 여러 가지 일반적인 시나리오에 사용할 수 있습니다.

* 여러 온-프레미스 응용 프로그램에서 파일 공유를 사용합니다. 이 기능을 사용하면 데이터를 공유하는 응용 프로그램을 Azure로 보다 쉽게 마이그레이션할 수 있습니다. 파일 공유를 온-프레미스 응용 프로그램에서 사용하는 것과 동일한 드라이브 문자에 탑재하면 파일 공유에 액세스하는 응용 프로그램의 일부가 최소한의 변경 내용(있는 경우)으로 작동해야 합니다.

* 구성 파일을 파일 공유에 저장하고 여러 VM에서 액세스할 수 있습니다. 그룹의 여러 개발자가 사용하는 도구 및 유틸리티를 파일 공유에 저장할 수 있으며, 이렇게 하면 모든 사람이 찾아서 동일한 버전을 사용할 수 있습니다.

* 파일 공유에 쓰고 나중에 처리하거나 분석할 수 있는 데이터의 세 가지 예로 진단 로그, 메트릭 및 크래시 덤프를 들 수 있습니다.

현재는 Active Directory 기반 인증과 ACL(액세스 제어 목록)이 지원되지 않지만 향후 지원할 예정입니다. 파일 공유 액세스에 대한 인증을 제공하기 위해 저장소 계정 자격 증명이 사용됩니다. 즉, 공유가 탑재된 모든 사용자는 공유에 대한 전체 읽기/쓰기 액세스 권한을 갖습니다.

## <a name="queue-storage"></a>큐 저장소

Azure 큐 서비스는 메시지를 저장하고 검색하는 데 사용됩니다. 큐 메시지의 크기는 최대 64KB일 수 있고 큐에는 수 많은 메시지가 포함될 수 있습니다. 큐는 일반적으로 비동기적으로 처리될 메시지의 목록을 저장하는 데 사용됩니다.

예를 들어 고객이 사진을 업로드하여 각 사진에 대한 썸네일을 만들려고 한다고 가정하겠습니다. 고객이 사진을 업로드하는 동안 썸네일을 만들 때까지 기다리게 할 수 있습니다. 대신 큐를 사용할 수 있습니다. 고객이 업로드를 완료하면 큐에 메시지를 작성합니다. 그런 다음 Azure Function에서는 큐의 메시지를 검색하고 썸네일을 만듭니다. 이 프로세스의 일부는 각각 별도로 확장될 수 있으며 사용하기 위해 조정하는 경우 더 세밀하게 조정할 수 있습니다.

## <a name="table-storage"></a>테이블 저장소

Azure Table Storage는 이제 Azure Cosmos DB의 일부입니다. Azure Table Storage 설명서를 보려면 [Azure Table Storage 개요](../../cosmos-db/table-storage-overview.md)를 참조하세요. 기존 Azure Table Storage 서비스 외에도 처리량 최적화 테이블, 전역 분산 및 자동 보조 인덱스를 제공하는 새로운 Azure Cosmos DB 테이블 API가 있습니다. 새로운 프리미엄 환경에 대해 알아보고 사용해 보려면 [Azure Cosmos DB 테이블 API](https://aka.ms/premiumtables)를 확인하세요.

## <a name="disk-storage"></a>디스크 저장소

또한 Azure Storage는 가상 머신에서 사용하는 관리되는 디스크 및 관리되지 않는 디스크 기능을 포함합니다. 이러한 기능에 대한 자세한 내용은 [Compute Service 설명서](https://docs.microsoft.com/azure/#pivot=services&panel=Compute)를 참조하세요.

## <a name="types-of-storage-accounts"></a>저장소 계정 유형

이 표에서는 개체에서 사용할 수 있는 다양한 종류의 저장소 계정을 보여줍니다.

|**저장소 계정의 유형**|**범용 표준**|**범용 프리미엄**|**Blob Storage, 핫 및 쿨 액세스 계층**|
|-----|-----|-----|-----|
|**지원되는 서비스**| Blob, File, Queue 서비스 | Blob Service | Blob Service|
|**지원되는 Blob 유형**|블록 Blob, 페이지 Blob 및 추가 Blob | 페이지 Blob | 블록 Blob 및 추가 Blob|

### <a name="general-purpose-storage-accounts"></a>범용 저장소 계정

범용 저장소 계정에는 두 가지가 있습니다.

#### <a name="standard-storage"></a>Standard Storage

가장 널리 사용되는 저장소 계정은 모든 유형의 데이터에 사용할 수 있는 표준 저장소 계정입니다. 표준 저장소 계정은 자기 미디어를 사용하여 데이터를 저장합니다.

#### <a name="premium-storage"></a>Premium Storage

프리미엄 저장소는 VHD 파일에 주로 사용되는 페이지 Blob에 고성능 저장소를 제공합니다. 프리미엄 저장소 계정은 SSD를 사용하여 데이터를 저장합니다. 모든 VM에 Premium Storage를 사용하는 것이 좋습니다.

### <a name="blob-storage-accounts"></a>Blob Storage 계정

Blob Storage 계정은 블록 Blob 및 추가 Blob을 저장하는 데 사용되는 특별한 저장소 계정입니다. 이러한 계정에 페이지 Blob을 저장할 수 없습니다. 따라서 VHD 파일을 저장할 수 없습니다. 이러한 계정을 사용하면 액세스 계층을 핫 또는 쿨로 설정할 수 있습니다. 계층은 언제든지 변경할 수 있습니다.

핫 액세스 계층은 자주 액세스 하는 파일에 대해 사용합니다. 저장소의 경우 비용이 많이 들지만 Blob에 액세스하는 비용이 훨씬 낮습니다. 쿨 액세스 계층에 저장된 Blob의 경우 Blob에 액세스하기 위한 비용이 많이 들지만 저장소 비용이 훨씬 낮습니다.

## <a name="accessing-your-blobs-files-and-queues"></a>Blob, 파일 및 큐에 액세스

각 저장소 계정에는 모든 작업에 사용할 수 있는 두 개의 인증 키가 있습니다. 경우에 따라 보안을 강화하기 위해 키를 롤백할 수 있도록 구 개의 키가 있습니다. 키 소유권과 계정 이름이 있으면 저장소 계정의 모든 데이터에 무제한 액세스할 수 있으므로 이러한 키를 안전하게 보관하는 것이 중요합니다.

이 섹션에서는 저장소 계정 및 해당 데이터를 보호하는 두 가지 방법을 알아봅니다. 저장소 계정 및 데이터를 보호하는 방법에 대한 자세한 내용은 [Azure Storage 보안 가이드](storage-security-guide.md)를 참조하세요.

### <a name="securing-access-to-storage-accounts-using-azure-ad"></a>Azure AD를 사용하여 저장소 계정에 대한 액세스 보호

저장소 데이터에 대한 액세스를 보호하는 한 가지 방법은 저장소 계정 키에 대한 액세스를 제어하는 것입니다. 리소스 관리자 RBAC(역할 기반 Access Control)를 사용하여 사용자, 그룹 또는 응용 프로그램에 역할을 할당할 수 있습니다. 이러한 역할은 허용되거나 허용되지 않는 작업의 특정 집합에 연결됩니다. RBAC를 사용하여 저장소 계정에 대한 액세스 권한을 부여하는 경우 액세스 계층을 변경하는 등 해당 저장소 계정에 대한 관리 작업만을 처리합니다. RBAC를 사용하여 특정 컨테이너 또는 파일 공유와 같은 데이터 개체에 대한 액세스 권한을 부여할 수 없습니다. 그러나 RBAC를 사용하여 저장소 계정 키에 대한 액세스 권한을 부여할 수 있습니다. 그러면 데이터 개체를 읽는 데 사용할 수 있습니다.

### <a name="securing-access-using-shared-access-signatures"></a>공유 액세스 서명을 사용하여 액세스 보호

공유 액세스 서명 및 저장된 액세스 정책을 사용하여 데이터 개체를 보호할 수 있습니다. SAS(공유 액세스 서명)은 자산의 URI에 추가할 수 있는 보안 토큰을 포함하는 문자열로, 특정 저장소 개체에 대한 액세스 권한을 위임하고 사용 권한 및 액세스의 날짜/시간 범위와 같은 제한을 지정할 수 있도록 합니다. 이 기능에는 광범위한 기능이 있습니다. 자세한 내용은 [SAS(공유 액세스 서명) 사용](storage-dotnet-shared-access-signature-part-1.md)을 참조하세요.

### <a name="public-access-to-blobs"></a>Blob에 대한 공용 액세스

Blob Service를 사용하면 컨테이너와 해당 Blob 또는 특정 Blob에 대한 공용 액세스를 제공할 수 있습니다. 컨테이너 또는 Blob을 공개로 지정하면 모든 사용자가 이 컨테이너 또는 Blob를 익명으로 읽을 수 있으며 인증이 필요 없습니다. 이 작업을 수행하는 예제는 Blob Storage의 이미지, 비디오 또는 문서를 사용하는 웹 사이트가 있는 경우입니다. 자세한 내용은 [컨테이너 및 Blob에 대한 익명 읽기 권한 관리](../blobs/storage-manage-access-to-resources.md)를 참조하세요.

## <a name="encryption"></a>암호화

Storage 서비스에 사용할 수 있는 기본적인 종류의 두 가지 암호화가 있습니다. 보안 및 암호화에 대한 자세한 내용은 [Azure Storage 보안 가이드](storage-security-guide.md)를 참조하세요.

### <a name="encryption-at-rest"></a>휴지 상태의 암호화

미사용 Azure SSE(저장소 서비스 암호화)를 사용하면 조직의 보안 및 규정 준수 약정에 맞게 데이터를 보호할 수 있습니다. 이 기능을 통해 Azure Storage는 저장소를 유지하기 전에 데이터를 자동으로 암호화하고 검색하기 전에 암호를 해독합니다. 암호화, 암호 해독 및 키 관리는 사용자에게 완전히 투명하게 처리됩니다.

SSE는 모든 성능 계층(표준 및 프리미엄), 모든 배포 모델(Azure Resource Manager 및 클래식) 및 모든 Azure Storage 서비스(Blob, 큐, 테이블 및 파일)의 데이터를 자동으로 암호화합니다. SSE는 Azure Storage 성능에 영향을 주지 않습니다.

미사용 SSE 암호화에 대한 자세한 내용은 [미사용 데이터에 대한 Azure 저장소 서비스 암호화](storage-service-encryption.md)를 참조하세요.

### <a name="client-side-encryption"></a>클라이언트 쪽 암호화

저장소 클라이언트 라이브러리에는 네트워크를 통해 데이터를 클라이언트에서 Azure로 보내기 전에 프로그래밍 방식으로 암호화하도록 호출할 수 있는 메서드가 있습니다. 암호화되어 저장됩니다. 즉, 휴지 시 암호화됩니다. 데이터를 다시 읽을 경우 받은 이후 정보의 암호를 해독합니다.

클라이언트 쪽 암호화에 대한 자세한 내용은 [Microsoft Azure Storage용 .NET을 사용하는 클라이언트 쪽 암호화](storage-client-side-encryption.md)를 참조하세요.

## <a name="replication"></a>복제

데이터가 지속되는지 확인하기 위해 Azure Storage는 여러 데이터 복사본을 복제합니다. 저장소 계정을 설정할 때 복제 형식을 선택합니다. 대부분의 경우 저장소 계정을 만든 후 이 설정을 수정할 수 있습니다. 

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

재해 복구 정보는 [Azure Storage 중단이 발생할 경우 수행할 작업](storage-disaster-recovery-guidance.md)을 참조하세요.

## <a name="transferring-data-to-and-from-azure-storage"></a>Azure Storage 간에 데이터 전송

AzCopy 명령줄 유틸리티를 사용하여 저장소 계정 내에서 또는 저장소 계정 간에 Blob 및 파일 데이터를 복사할 수 있습니다. 도움말은 다음 문서 중 하나를 참조하세요.

* [Windows에서 AzCopy를 사용하여 데이터 전송](storage-use-azcopy.md)
* [Linux에서 AzCopy를 사용하여 데이터 전송](storage-use-azcopy-linux.md)

AzCopy는 [Azure 데이터 이동 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)를 기반으로 구축되며 현재 미리 보기에서 사용할 수 있습니다.

Azure Import/Export 서비스는 저장소 계정 간에 대량의 Blob 데이터를 가져오거나 내보내는 데 사용할 수 있습니다. 여러 하드 드라이브를 준비하고 Azure 데이터 센터에 전자 메일로 보내면 여기에서 하드 드라이브 간에 데이터를 전송하고 하드 드라이브를 다시 사용자에게 보냅니다. Import/Export 서비스에 대한 자세한 내용은 [Microsoft Azure Import/Export 서비스를 사용하여 Blob Storage에 데이터 전송](../storage-import-export-service.md)을 참조하세요.

## <a name="pricing"></a>가격

Azure Storage에서 가격 책정에 대한 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/blobs/)를 참조하세요.

## <a name="storage-apis-libraries-and-tools"></a>Storage API, 라이브러리 및 도구
Azure Storage 리소스는 HTTP/HTTPS 요청을 수행할 수 있는 모든 언어로 액세스할 수 있습니다. 또한 Azure storage는 많이 사용되는 몇 가지 언어를 위한 프로그래밍 라이브러리를 제공합니다. 이 라이브러리는 동기/비동기 호출, 작업 일괄 처리, 예외 관리, 자동 재시도, 작업자 동작 등과 같은 세부 사항을 처리하여 Azure Storage 작업의 많은 측면을 간소화합니다. 현재 이 라이브러리는 파이프라인의 다른 라이브러리와 함께 다음 언어 및 플랫폼에 대해 사용할 수 있습니다.

### <a name="azure-storage-data-services"></a>Azure Storage 데이터 서비스
* [Storage 서비스 REST API](/rest/api/storageservices/)
* [.NET용 Storage 클라이언트 라이브러리](https://docs.microsoft.com/dotnet/api/?view=azurestorage-8.1.1)
* [Storage Client Library for C++](https://github.com/Azure/azure-storage-cpp)
* [Java/Android용 Storage 클라이언트 라이브러리](https://azure.microsoft.com/develop/java/)
* [Node.js용 Storage 클라이언트 라이브러리](http://dl.windowsazure.com/nodestoragedocs/index.html)
* [PHP용 Storage 클라이언트 라이브러리](https://azure.microsoft.com/develop/php/)
* [Python용 Storage 클라이언트 라이브러리](https://azure.microsoft.com/develop/python/)
* [Ruby용 Storage 클라이언트 라이브러리](https://azure.microsoft.com/develop/ruby/)
* [PowerShell용 Storage Cmdlet](/powershell/module/azure.storage/?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)
* [CLI 2.0의 Storage 명령](/cli/azure/storage)

## <a name="next-steps"></a>다음 단계

* [Blob Storage에 대한 세부 정보](../blobs/storage-blobs-introduction.md)
* [File Storage에 대한 세부 정보](../storage-files-introduction.md)
* [Queue Storage에 대한 세부 정보](../queues/storage-queues-introduction.md)

Azure Storage를 사용하여 가동하고 실행하려면 [저장소 계정 만들기](storage-quickstart-create-account.md)를 참조하세요.

<!-- FIGURE OUT WHAT TO DO WITH ALL THESE LINKS.

Azure Storage resources can be accessed by any language that can make HTTP/HTTPS requests. Additionally, Azure Storage offers programming libraries for several popular languages. These libraries simplify many aspects of working with Azure Storage by handling details such as synchronous and asynchronous invocation, batching of operations, exception management, automatic retries, operational behavior and so forth. Libraries are currently available for the following languages and platforms, with others in the pipeline:

### Azure Storage data services
* [Storage Services REST API](https://docs.microsoft.com/rest/api/storageservices/)
* [Storage Client Library for .NET](https://docs.microsoft.com/dotnet/api/?view=azurestorage-8.1.1)
* [Storage Client Library for C++](https://github.com/Azure/azure-storage-cpp)
* [Storage Client Library for Java/Android](https://azure.microsoft.com/develop/java/)
* [Storage Client Library for Node.js](http://dl.windowsazure.com/nodestoragedocs/index.html)
* [Storage Client Library for PHP](https://azure.microsoft.com/develop/php/)
* [Storage Client Library for Python](https://azure.microsoft.com/develop/python/)
* [Storage Client Library for Ruby](https://azure.microsoft.com/develop/ruby/)
* [Storage Cmdlets for PowerShell](/powershell/module/azure.storage/?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)

### Azure Storage management services
* [Storage Resource Provider REST API Reference](/rest/api/storagerp/)
* [Storage Resource Provider Client Library for .NET](/dotnet/api/microsoft.azure.management.storage)
* [Storage Resource Provider Cmdlets for PowerShell 1.0](/powershell/module/azure.storage)
* [Storage Service Management REST API (Classic)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### Azure Storage data movement services
* [Storage Import/Export Service REST API](../storage-import-export-service.md)
* [Storage Data Movement Client Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)

### Tools and utilities
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) is a free, standalone app from Microsoft that enables you to work visually with Azure Storage data on Windows, macOS, and Linux.
* [Azure Storage Client Tools](../storage-explorers.md)
* [Azure SDKs and Tools](https://azure.microsoft.com/tools/)
* [Azure Storage Emulator](http://www.microsoft.com/download/details.aspx?id=43709)
* [Azure PowerShell](/powershell/azure/overview)
* [AzCopy Command-Line Utility](http://aka.ms/downloadazcopy)

## Next steps
To learn more about Azure Storage, explore these resources:

### Documentation
* [Azure Storage Documentation](https://azure.microsoft.com/documentation/services/storage/)
* [Create a storage account](../storage-create-storage-account.md)

-->

### <a name="for-administrators"></a>관리자용
* [Azure Storage와 함께 Azure PowerShell 사용](storage-powershell-guide-full.md)
* [Azure Storage에서 Azure CLI 사용](../storage-azure-cli.md)

### <a name="for-net-developers"></a>.NET 개발자용
* [.NET을 사용하여 Azure Blob 저장소 시작](../blobs/storage-dotnet-how-to-use-blobs.md)
* [.NET을 사용하여 Azure Files 개발](../files/storage-dotnet-how-to-use-files.md)
* [.NET을 사용하여 Azure 테이블 저장소 시작](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [.NET을 사용하여 Azure 큐 저장소 시작](../storage-dotnet-how-to-use-queues.md)

### <a name="for-javaandroid-developers"></a>Java/Android 개발자용
* [Java에서 Blob 저장소를 사용하는 방법](../blobs/storage-java-how-to-use-blob-storage.md)
* [Java를 사용하여 Azure Files 개발](../files/storage-java-how-to-use-file-storage.md)
* [Java에서 테이블 저장소를 사용하는 방법](../../cosmos-db/table-storage-how-to-use-java.md)
* [Java에서 큐 저장소를 사용하는 방법](../storage-java-how-to-use-queue-storage.md)

### <a name="for-nodejs-developers"></a>Node.js 개발자용
* [Node.js에서 Blob 저장소를 사용하는 방법](../blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Node.js에서 테이블 저장소를 사용하는 방법](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [Node.js에서 큐 저장소를 사용하는 방법](../storage-nodejs-how-to-use-queues.md)

### <a name="for-php-developers"></a>PHP 개발자용
* [PHP에서 Blob 저장소를 사용하는 방법](../blobs/storage-php-how-to-use-blobs.md)
* [PHP에서 테이블 저장소를 사용하는 방법](../../cosmos-db/table-storage-how-to-use-php.md)
* [PHP에서 큐 저장소를 사용하는 방법](../storage-php-how-to-use-queues.md)

### <a name="for-ruby-developers"></a>Ruby 개발자용
* [Ruby에서 Blob 저장소를 사용하는 방법](../blobs/storage-ruby-how-to-use-blob-storage.md)
* [Ruby에서 테이블 저장소를 사용하는 방법](../../cosmos-db/table-storage-how-to-use-ruby.md)
* [Ruby에서 큐 저장소를 사용하는 방법](../storage-ruby-how-to-use-queue-storage.md)

### <a name="for-python-developers"></a>Python 개발자용
* [Python에서 Blob 저장소를 사용하는 방법](../blobs/storage-python-how-to-use-blob-storage.md)
* [Python을 사용하여 Azure Files 개발](../files/storage-python-how-to-use-file-storage.md)
* [Python에서 테이블 저장소를 사용하는 방법](../../cosmos-db/table-storage-how-to-use-python.md)
* [Python에서 큐 저장소를 사용하는 방법](../storage-python-how-to-use-queue-storage.md)
