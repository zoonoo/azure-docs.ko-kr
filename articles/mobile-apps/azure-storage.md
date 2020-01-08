---
title: Azure Storage를 사용 하 여 매우 안전 하 고 내구성이 뛰어난 응용 프로그램을 빌드하는 클라우드 저장소
description: 클라우드에 구조화 된 크고 구조화 되지 않은 모바일 응용 프로그램 데이터를 저장 하는 서비스에 대해 알아봅니다.
author: elamalani
ms.assetid: 12bbb070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 53754976a6e1f6d893cf7d6ab73d57c95dae193f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453264"
---
# <a name="cloud-storage-for-highly-secure-durable-scalable-apps-with-azure-storage"></a>Azure Storage로 안전 하 고 내구성이 뛰어난 응용 프로그램을 위한 클라우드 저장소
[Azure Storage](https://azure.microsoft.com/services/storage/) 은 데이터 개체에 대 한 대규모 확장 가능한 개체 저장소, 클라우드의 파일 시스템 서비스, 신뢰할 수 있는 메시징의 메시징 저장소 및 nosql 저장소를 제공 하는 최신 응용 프로그램을 위한 Microsoft 클라우드 저장소 솔루션입니다. Azure Storage는:
- **내구성 및 고가용성:** 중복성을 사용 하면 일시적인 하드웨어 오류가 발생할 경우 데이터가 안전 하 게 보호 됩니다. 또한 로컬 재해 또는 자연 재해로부터 추가 보호를 위해 데이터 센터 또는 지리적 영역에서 데이터를 복제하도록 선택할 수도 있습니다. 이러한 방식으로 복제된 데이터는 예기치 않은 중단이 발생할 경우 항상 사용 가능한 상태로 유지됩니다.
- **보안:** Azure Storage에 작성 된 모든 데이터는 서비스에 의해 암호화 됩니다. Azure Storage는 데이터에 액세스할 수 있는 사용자에 대한 세분화된 제어를 제공합니다.
- **확장 가능:** 서비스는 오늘날의 응용 프로그램의 데이터 저장 및 성능 요구 사항을 충족 하기 위해 대규모 확장 가능 하도록 설계 되었습니다.
- **관리:** Azure는 하드웨어 유지 관리, 업데이트 및 중요 한 문제를 처리 합니다.
- **액세스 가능:** HTTP 또는 HTTPS를 통해 전 세계 어디서 나 데이터에 액세스할 수 있습니다. Microsoft는 .NET, Java, node.js, Python, PHP, Ruby, Go 및 완성 된 REST API 등 다양 한 언어로 클라이언트 라이브러리를 제공 합니다. 스크립팅은 Azure PowerShell 또는 Azure CLI에서 지원 됩니다. Azure Portal 및 Azure Storage 탐색기는 데이터 작업을 위한 쉬운 시각적 솔루션을 제공 합니다.

다음 서비스를 사용 하 여 모바일 앱에서 클라우드 저장소를 사용 하도록 설정 합니다.

## <a name="azure-blob-storage"></a>Azure Blob Storage
[Azure Blob storage](https://azure.microsoft.com/services/storage/blobs/) 는 클라우드에 대 한 개체 저장소 솔루션을 제공 합니다. Blob 저장소는 텍스트 또는 이진 등의 특정 데이터 모델 또는 정의를 따르지 않는 대량의 구조화 되지 않은 데이터를 저장 하는 데 최적화 되어 있습니다. 클라이언트 라이브러리에서 사용 하는 다양 한 언어를 지원 합니다. Blob storage는 다음을 수행 하도록 설계 되었습니다.
- 이미지나 문서를 브라우저에 직접 제공 합니다.
- 분산 액세스를 위한 파일을 저장 합니다.
- 비디오 및 오디오를 스트리밍합니다.
- 로그 파일에 씁니다.
- 백업 및 복원, 재해 복구 및 보관을 위한 데이터를 저장 합니다.
- 온-프레미스 또는 Azure 호스 티 드 서비스에의 한 분석을 위해 데이터를 저장 합니다.

**참조**
- [Azure Portal](https://portal.azure.com)
- [Azure Blob storage 설명서](/azure/storage/blobs/storage-blobs-introduction)
- [빠른 시작](/azure/storage/blobs/storage-quickstart-blobs-portal)
- [샘플](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="azure-table-storage"></a>Azure Table Storage
[Azure Table storage](https://azure.microsoft.com/services/storage/tables/) 는 구조화 된 nosql 데이터를 클라우드에 저장 하 고 스키마 없는 디자인을 사용 하 여 키 또는 특성 저장소를 제공 하는 서비스입니다. Azure Table Storage는 많은 양의 구조화된 데이터를 저장합니다. 이 서비스는 Azure 클라우드 내부 및 외부에서 인증 된 호출을 수락 하는 NoSQL 데이터 저장소입니다. Azure 테이블은 구조화 된 비관계형 데이터를 저장 하는 데 적합 합니다. 테이블 저장소는 일반적으로 다음 작업에 사용 됩니다.
- 웹 크기의 응용 프로그램을 서비스할 수 있는 tb의 구조화 된 데이터를 저장 합니다.
- 복잡 한 조인, 외래 키 또는 저장 프로시저가 필요 하지 않은 데이터 집합을 저장 하 고 빠른 액세스를 위해 비 정규화 할 수 있습니다.
- 클러스터형 인덱스를 사용 하 여 데이터를 신속 하 게 쿼리 합니다.
- Windows Communication Foundation (WCF) Data Services .NET 라이브러리를 사용 하 여 OData 프로토콜 및 LINQ 쿼리를 사용 하 여 데이터에 액세스 합니다.

테이블 저장소를 사용 하 여 방대한 구조화 된 비관계형 데이터 집합을 저장 하 고 쿼리할 수 있습니다. 테이블이 수요를 늘릴 때 확장 됩니다.

**참조**
- [Azure Portal](https://portal.azure.com)
- [Azure 테이블 저장소 설명서](/azure/storage/tables/table-storage-overview)
- [샘플](/azure/cosmos-db/tutorial-develop-table-dotnet?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fstorage%2Ftables%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [빠른 시작](/azure/storage/tables/table-storage-quickstart-portal)

## <a name="azure-files"></a>Azure Files
[Azure Files](https://azure.microsoft.com/services/storage/files/)를 사용 하면 표준 SMB (서버 메시지 블록) 프로토콜을 사용 하 여 액세스할 수 있는 항상 사용 가능한 네트워크 파일 공유를 설정할 수 있습니다. 여러 Vm은 읽기 및 쓰기 권한으로 동일한 파일을 공유할 수 있습니다. REST 인터페이스나 저장소 클라이언트 라이브러리를 사용 하 여 파일을 읽을 수도 있습니다. 파일을 가리키고 SAS (공유 액세스 서명) 토큰을 포함 하는 URL을 사용 하 여 전 세계 어디에서 든 파일에 액세스할 수 있습니다. SAS 토큰을 생성할 수 있습니다. 특정 시간 동안 개인 자산에 대 한 특정 액세스를 허용 합니다.

Azure 파일 공유를 사용하여 다음을 수행할 수 있습니다.
- **온-프레미스 파일 서버 교체 또는 보완:** Windows, macOS 및 Linux와 같은 인기 있는 운영 체제는 전 세계 어디에서 든 Azure 파일 공유를 직접 탑재할 수 있습니다. Azure 파일 공유는 사용되는 데이터의 성능 및 분산 캐싱을 위해 Azure 파일 동기화를 사용하여 온-프레미스 또는 클라우드의 Windows Server에 복제될 수도 있습니다.
- **리프트 앤 시프트 응용 프로그램:** 파일 공유를 필요로 하는 클라우드로 응용 프로그램을 마이그레이션하고 파일 응용 프로그램 또는 사용자 데이터를 저장 합니다.
- **클라우드 개발 간소화:** 새 클라우드 개발 프로젝트를 간소화 하기 위해 다양 한 방법으로 Azure Files를 사용할 수도 있습니다. 예:
    - **공유 응용 프로그램 설정:** 분산 응용 프로그램의 일반적인 패턴은 여러 응용 프로그램 인스턴스에서 액세스할 수 있는 중앙 위치에 구성 파일을 포함 하는 것입니다. 응용 프로그램 인스턴스는 REST API 파일을 통해 구성을 로드할 수 있습니다. 사용자는 필요에 따라 SMB 공유를 로컬로 탑재 하 여 액세스할 수 있습니다.
    - **진단 공유:** Azure 파일 공유는 클라우드 응용 프로그램에서 로그, 메트릭 및 크래시 덤프를 작성 하는 데 편리한 장소입니다. REST API 파일을 통해 응용 프로그램 인스턴스에서 로그를 작성할 수 있습니다. 개발자는 로컬 컴퓨터에 파일 공유를 탑재 하 여 액세스할 수 있습니다. 이 기능을 통해 뛰어난 유연성을 발휘할 수 있습니다. 개발자는 알고 있는 기존 도구를 중단 하지 않고도 클라우드 개발을 수용할 수 있습니다.

**참조**
- [Azure Portal](https://portal.azure.com)
- [Azure Files 설명서](/azure/storage/files/storage-files-introduction)
- [빠른 시작](/azure/storage/files/storage-files-quick-create-use-windows)

## <a name="azure-queue-storage"></a>Azure Queue storage
[Azure Queue storage](https://azure.microsoft.com/services/storage/queues/) 는 많은 수의 메시지를 저장 하기 위한 서비스입니다. HTTP 또는 HTTPS를 사용 하 여 인증 된 호출을 통해 전 세계 어디에서 나 메시지에 액세스할 수 있습니다. 큐 메시지의 크기는 최대 64KB입니다. 큐에는 저장소 계정의 총 용량 제한까지 수백만 개의 메시지가 포함 될 수 있습니다. 큐는 비동기적으로 처리할 작업의 백로그를 만드는 데 일반적으로 사용됩니다.

**참조**
- [Azure Portal](https://portal.azure.com)
- [Azure Queue storage 설명서](/azure/storage/queues/)
- [빠른 시작](/azure/storage/queues/storage-quickstart-queues-portal)
- [샘플](/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
