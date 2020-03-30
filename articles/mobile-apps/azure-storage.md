---
title: Azure Storage를 사용하여 매우 안전하고 내구성이 뛰어나며 확장 가능한 앱을 빌드하는 클라우드 스토리지
description: 클라우드에 대규모 구조화 및 비구조식 모바일 애플리케이션 데이터를 저장하는 서비스에 대해 알아봅니다.
author: codemillmatt
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 682f06936480d9018d759b4df3bed80be1206faf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240984"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>Azure 스토리지를 통해 매우 안전하고 내구성이 뛰어나고 확장 가능한 앱을 위한 클라우드 스토리지
[Azure Storage는](https://azure.microsoft.com/services/storage/) 데이터 개체에 대한 대규모 확장 가능한 개체 저장소, 클라우드용 파일 시스템 서비스, 신뢰할 수 있는 메시징 을 위한 메시징 저장소 및 NoSQL 저장소를 제공하는 최신 응용 프로그램을 위한 Microsoft의 클라우드 저장소 솔루션입니다. Azure Storage는:
- **내구성및고가용성:** 중복성은 일시적인 하드웨어 오류 발생 시 데이터를 안전하게 보호합니다. 또한 로컬 재해 또는 자연 재해로부터 추가 보호를 위해 데이터 센터 또는 지리적 영역에서 데이터를 복제하도록 선택할 수도 있습니다. 이러한 방식으로 복제된 데이터는 예기치 않은 중단이 발생할 경우 항상 사용 가능한 상태로 유지됩니다.
- **보안:** Azure Storage에 기록된 모든 데이터는 서비스에 의해 암호화됩니다. Azure Storage는 데이터에 액세스할 수 있는 사용자에 대한 세분화된 제어를 제공합니다.
- **확장 가능:** 서비스는 오늘날 애플리케이션의 데이터 스토리지 및 성능 요구 사항을 충족하도록 확장성이 크게 확장가능하도록 설계되었습니다.
- **관리되는:** Azure는 하드웨어 유지 관리, 업데이트 및 중요한 문제를 처리합니다.
- **액세스 가능:** 데이터는 HTTP 또는 HTTPS를 통해 전 세계 어디에서나 액세스할 수 있습니다. Microsoft는 .NET, Java, Node.js, 파이썬, PHP, 루비 및 이동 및 성숙한 REST API와 같은 다양한 언어로 클라이언트 라이브러리를 제공합니다. 스크립팅은 Azure PowerShell 또는 Azure CLI에서 지원됩니다. Azure 포털 및 Azure 저장소 탐색기는 데이터 작업을 위한 간편한 시각적 솔루션을 제공합니다.

다음 서비스를 사용하여 모바일 앱에서 클라우드 스토리지를 활성화합니다.

## <a name="azure-blob-storage"></a>Azure Blob 스토리지
[Azure Blob 저장소는](https://azure.microsoft.com/services/storage/blobs/) 클라우드에 대한 개체 저장소 솔루션을 제공합니다. Blob 저장소는 텍스트 나 바이너리와 같은 특정 데이터 모델이나 정의를 준수하지 않는 대량의 비정형 데이터를 저장하도록 최적화되어 있습니다. 클라이언트 라이브러리에서 사용하는 다양한 언어를 지원합니다. Blob 스토리지는 다음을 수행하도록 설계되었습니다.
- 이미지 나 문서를 브라우저에 직접 제공합니다.
- 분산 액세스를 위해 파일을 저장합니다.
- 비디오 및 오디오를 스트리밍합니다.
- 로그 파일에 씁니다.
- 백업 및 복원, 재해 복구 및 보관을 위해 데이터를 저장합니다.
- 온-프레미스 또는 Azure 호스팅 서비스에서 분석을 위해 데이터를 저장합니다.

**참조**
- [Azure 포털](https://portal.azure.com)
- [Azure Blob Storage 설명서](/azure/storage/blobs/storage-blobs-introduction)
- [빠른 시작](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [샘플](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Azure Table Storage
[Azure Table 저장소는](https://azure.microsoft.com/services/storage/tables/) 클라우드에 구조화된 NoSQL 데이터를 저장하고 스키마없는 디자인으로 키 또는 특성 저장소를 제공하는 서비스입니다. Azure Table Storage는 많은 양의 구조화된 데이터를 저장합니다. 이 서비스는 Azure 클라우드 내부 및 외부에서 인증된 호출을 허용하는 NoSQL 데이터 저장소입니다. Azure 테이블은 구조화된 비관계형 데이터를 저장하는 데 이상적입니다. 테이블 저장소는 일반적으로 다음과 같은 데 사용됩니다.
- 웹 규모 애플리케이션을 처리할 수 있는 테라바이트 규모의 데이터를 저장합니다.
- 복잡한 조인, 외래 키 또는 저장 프로시저가 필요하지 않고 빠른 액세스를 위해 비정규화될 수 있는 데이터 집합을 저장합니다.
- 클러스터된 인덱스를 사용하여 데이터를 빠르게 쿼리합니다.
- WCF(Windows 통신 재단) 데이터 서비스 .NET 라이브러리를 사용하여 OData 프로토콜 및 LINQ 쿼리를 사용하여 데이터에 액세스합니다.

Table 저장소를 사용하여 거대한 구조화 된 비관계형 데이터 집합을 저장하고 쿼리할 수 있습니다. 수요가 증가함에 따라 테이블의 규모가 확장됩니다.

**참조**
- [Azure 포털](https://portal.azure.com)
- [Azure Table Storage 설명서](/azure/storage/tables/table-storage-overview)
- [샘플](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [빠른 시작](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-files"></a>Azure 파일
[Azure Files를](https://azure.microsoft.com/services/storage/files/)사용하면 표준 SMB(서버 메시지 블록) 프로토콜을 사용하여 액세스할 수 있는 가용성이 높은 네트워크 파일 공유를 설정할 수 있습니다. 여러 VM은 읽기 및 쓰기 액세스 모두에서 동일한 파일을 공유할 수 있습니다. REST 인터페이스 또는 저장소 클라이언트 라이브러리를 사용하여 파일을 읽을 수도 있습니다. 파일을 가리키고 SAS(공유 액세스 서명) 토큰을 포함하는 URL을 사용하여 전 세계 어디에서나 파일에 액세스할 수 있습니다. SAS 토큰을 생성할 수 있습니다. 특정 시간 동안 개인 자산에 대한 특정 액세스를 허용합니다.

Azure 파일 공유를 사용하여 다음을 수행할 수 있습니다.
- **온-프레미스 파일 서버 교체 또는 보완:** Windows, macOS 및 Linux와 같은 인기 있는 운영 체제는 전 세계 어디에서나 Azure 파일 공유를 직접 탑재할 수 있습니다. Azure 파일 공유는 사용되는 데이터의 성능 및 분산 캐싱을 위해 Azure 파일 동기화를 사용하여 온-프레미스 또는 클라우드의 Windows Server에 복제될 수도 있습니다.
- **리프트 및 변속 어플리케이션:** 파일 공유가 파일 응용 프로그램 또는 사용자 데이터를 저장하기를 예상하는 클라우드로 응용 프로그램을 마이그레이션합니다.
- **클라우드 개발 간소화:** Azure Files는 여러 가지 방법으로 새 클라우드 개발 프로젝트를 단순화하는 데 사용할 수도 있습니다. 예를 들어:
    - **공유 응용 프로그램 설정:** 분산 응용 프로그램의 일반적인 패턴은 구성 파일을 중앙 집중식 위치에 두어 많은 응용 프로그램 인스턴스에서 액세스할 수 있는 것입니다. 응용 프로그램 인스턴스는 파일 REST API를 통해 구성을 로드할 수 있습니다. 사용자는 SMB 공유를 로컬로 장착하여 필요에 따라 액세스할 수 있습니다.
    - **진단 공유:** Azure 파일 공유는 클라우드 응용 프로그램이 로그, 메트릭 및 크래시 덤프를 작성하기에 편리한 위치입니다. 로그는 파일 REST API를 통해 응용 프로그램 인스턴스에 의해 작성할 수 있습니다. 개발자는 로컬 컴퓨터에 파일 공유를 탑재하여 액세스할 수 있습니다. 이 기능을 사용하면 유연성이 매우 뛰어나습니다. 개발자는 알고 있는 기존 툴링을 포기하지 않고도 클라우드 개발을 수용할 수 있습니다.

**참조**
- [Azure 포털](https://portal.azure.com)
- [Azure Files 설명서](/azure/storage/files/storage-files-introduction)
- [빠른 시작](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queue-storage"></a>Azure Queue storage
[Azure 큐 저장소는](https://azure.microsoft.com/services/storage/queues/) 많은 수의 메시지를 저장하는 서비스입니다. HTTP 또는 HTTPS를 사용하여 인증된 호출을 통해 전 세계 어디서나 메시지에 액세스할 수 있습니다. 큐 메시지의 크기는 최대 64KB입니다. 큐에는 저장소 계정의 총 용량 제한까지 수백만 개의 메시지가 포함될 수 있습니다. 큐는 비동기적으로 처리할 작업의 백로그를 만드는 데 일반적으로 사용됩니다.

**참조**
- [Azure 포털](https://portal.azure.com)
- [Azure Queue storage 설명서](/azure/storage/queues/)
- [빠른 시작](/azure/storage/queues/storage-quickstart-queues-portal)
- [샘플](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
