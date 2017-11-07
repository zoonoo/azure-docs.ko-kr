---
title: "미사용 데이터에 대한 Azure Storage Service Encryption | Microsoft Docs"
description: "Azure 저장소 서비스 암호화 기능을 사용하여 데이터를 저장할 때 서비스 쪽에서 Azure Blob 저장소를 암호화하고 데이터를 검색할 때 암호 해독합니다."
services: storage
documentationcenter: .net
author: tamram
manager: timlt
editor: tysonn
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: tamram
ms.openlocfilehash: 772c36c8310a4bf30c62def507382fe74427e0d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>휴지 상태의 데이터에 대한 Azure 저장소 서비스 암호화
미사용 데이터에 대한 Azure 저장소 서비스 암호화(SSE)를 사용하면 조직의 보안 및 규정 준수 약정에 맞게 데이터를 보호할 수 있습니다. 이 기능을 통해 Azure 저장소는 저장소를 유지하기 전에 데이터를 자동으로 암호화하고 검색하기 전에 암호를 해독합니다. 암호화, 암호 해독 및 키 관리는 사용자에게 완전히 투명하게 처리됩니다.

다음 섹션에서는 저장소 서비스 암호화 기능, 지원되는 시나리오 및 사용자 환경을 사용하는 방법에 대한 자세한 지침을 제공합니다.

## <a name="overview"></a>개요
Azure 저장소는 여러 개발자가 보안 응용 프로그램을 함께 빌드할 수 있도록 하는 포괄적인 보안 기능을 제공합니다. [클라이언트 쪽 암호화](../storage-client-side-encryption.md), HTTP 또는 SMB 3.0을 사용하여 응용 프로그램과 Azure 간에 전송 중인 데이터의 보안을 유지할 수 있습니다. 저장소 서비스 암호화는 휴지 상태의 암호화를 제공하며 암호화, 암호 해독, 키 관리를 완전히 투명한 방식으로 처리합니다. 모든 데이터는 가장 강력한 블록 암호화 중 하나인 256비트 [AES 암호화](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)를 사용하여 암호화됩니다.

SSE는 데이터를 Azure Storage에 기록할 때 데이터를 암호화하는 방식으로 작동하며 Azure Blob Storage 및 File Storage에 사용할 수 있습니다. 다음에 대해 작동합니다.

* 표준 저장소: Blob에 대한 범용 저장소 계정과 File Storage 및 Blob Storage 계정
* Premium Storage 
* 모든 중복 수준(LRS, ZRS, GRS, RA-GRS)
* Azure Resource Manager 저장소 계정(클래식 아님) 
* 모든 지역

자세히 알아보려면 FAQ를 참조하세요.

저장소 계정에 대한 저장소 서비스 암호화를 사용하거나 사용하지 않도록 설정하려면 [Azure Portal](https://portal.azure.com) 에 로그인한 후 저장소 계정을 선택합니다. 설정 블레이드에서 이 스크린샷에 표시된 것처럼 Blob 서비스 섹션을 찾은 후 암호화를 클릭합니다.

![암호화 옵션을 보여 주는 포털 스크린샷](./media/storage-service-encryption/image1.png)
<br/>*그림 1: Blob Service에 SSE 사용(1단계)*

![암호화 옵션을 보여 주는 포털 스크린샷](./media/storage-service-encryption/image3.png)
<br/>*그림 2: 파일 서비스에 SSE 사용(1단계)*

암호화 설정을 클릭하면 저장소 서비스 암호화를 사용하거나 사용하지 않도록 설정할 수 있습니다.

![암호화 속성을 보여 주는 포털 스크린샷](./media/storage-service-encryption/image2.png)
<br/>*그림 3: Blob 및 파일 서비스에 SSE 사용(2단계)*

## <a name="encryption-scenarios"></a>암호화 시나리오
저장소 계정 수준에서 저장소 서비스 암호화를 사용할 수 있습니다. 사용하도록 설정하면 고객이 암호화할 서비스를 선택합니다. 다음과 같은 고객 시나리오가 지원됩니다.

* 리소스 관리자 계정의 Blob Storage 및 File Storage 암호화
* 리소스 관리자 저장소 계정으로 마이그레이션된 후 클래식 저장소 계정의 Blob 및 파일 서비스 암호화

SSE에는 다음 제한 사항이 있습니다.

* 클래식 저장소 계정의 암호화는 지원되지 않습니다.
* 기존 데이터 - SSE만 암호화가 사용된 후 새로 만든 데이터를 암호화합니다. 예를 들어 새 Resource Manager 저장소 계정을 만들지만 암호화를 켜지 않는 경우 Blob 또는 보관된 VHD를 해당 저장소 계정으로 업로드한 후 SSE를 켜면 해당 Blob가 재작성되거나 복사되지 않은 경우 Blob가 암호화되지 않습니다.
* 마켓플레이스 지원 - [Azure 포털](https://portal.azure.com), PowerShell 및 Azure CLI를 사용하여 마켓플레이스에서 만든 VM의 암호화를 사용합니다. VHD 기본 이미지는 암호화되지 않은 상태로 유지되지만 VM이 작동된 후 작성된 내용은 모두 암호화됩니다.
* 테이블 및 큐 데이터는 암호화되지 않습니다.

## <a name="getting-started"></a>시작하기
### <a name="step-1-create-a-new-storage-accountstorage-create-storage-accountmd"></a>1단계: [새 저장소 계정 만들기](../storage-create-storage-account.md).
### <a name="step-2-enable-encryption"></a>2단계: 암호화 사용.
[Azure 포털](https://portal.azure.com)을 사용하여 암호화를 사용하도록 설정할 수 있습니다.

> [!NOTE]
> 저장소 계정에 대해 저장소 서비스 암호화를 프로그래밍 방식으로 사용 또는 사용하지 않으려면 [Azure Storage Resource Provider REST API](https://msdn.microsoft.com/library/azure/mt163683.aspx), [.NET용 Storage Resource Provider 클라이언트 라이브러리](https://msdn.microsoft.com/library/azure/mt131037.aspx), [Azure PowerShell](/powershell/azureps-cmdlets-docs) 또는 [Azure CLI](../storage-azure-cli.md)를 사용하면 됩니다.
> 
> 

### <a name="step-3-copy-data-to-storage-account"></a>3단계: 저장소 계정에 데이터 복사
Blob service에 SSE를 사용하는 경우 해당 저장소 계정에 기록된 모든 Blob은 암호화됩니다. 저장소 계정에 이미 있는 모든 Blob는 다시 작성할 때까지 암호화되지 않습니다. 한 저장소 계정에서 SSE 암호화된 다른 계정으로 데이터를 복사하거나 SSE를 사용하도록 설정하고 Blob를 한 컨테이너에서 다른 컨테이너로 복사하여 이전 데이터가 암호화되는지 확인할 수 있습니다. 이렇게 하려면 다음 도구를 사용할 수 있습니다. 이는 File Storage에도 동일한 동작입니다.

#### <a name="using-azcopy"></a>AzCopy 사용
AzCopy는 간단한 명령과 최적의 성능으로 데이터를 Microsoft Azure Blob, 파일 및 테이블 저장소에(로부터) 복사하도록 디자인된 Windows 명령줄 유틸리티입니다. 한 저장소 계정에서 SSE가 설정된 다른 계정으로 Blob 또는 파일을 복사하는 데 사용할 수 있습니다. 

자세한 내용은 [AzCopy 명령줄 유틸리티로 데이터 전송](storage-use-azcopy.md)을 참조하세요.

#### <a name="using-smb"></a>SMB 사용
Azure Files는 표준 SMB 프로토콜을 사용하여 클라우드에서 파일 공유를 제공합니다. 온-프레미스 또는 Azure의 클라이언트에서 파일 공유를 마운트할 수 있습니다. 마운트되면 Azure 파일 공유에 파일을 복사하는 데 Robocopy와 같은 도구를 사용할 수 있습니다. 자세한 내용은 [Windows에서 Azure 파일 공유를 마운트하는 방법](../files/storage-how-to-use-files-windows.md) 및 [Linux에서 Azure 파일 공유를 마운트하는 방법](../files/storage-how-to-use-files-linux.md)을 참조하세요.


#### <a name="using-the-storage-client-libraries"></a>저장소 클라이언트 라이브러리 사용
.NET, C++, Java, Android, Node.js, PHP, Python 및 Ruby와 같은 다양한 저장소 클라이언트 라이브러리 집합을 사용하여 Blob 저장소 간 또는 저장소 계정 간에 Blob 또는 파일 데이터를 복사할 수 있습니다.

자세한 내용은 [.NET을 사용하여 Azure Blob 저장소 시작](../blobs/storage-dotnet-how-to-use-blobs.md)을 참조하세요.

#### <a name="using-a-storage-explorer"></a>저장소 탐색기 사용
저장소 탐색기를 사용하여 저장소 계정을 만들고 데이터를 업로드 및 다운로드하며 Blob 콘텐츠를 보고 디렉터리를 탐색할 수 있습니다. 이중 하나를 사용하여 암호화가 설정된 저장소 계정으로 Blob를 업로드할 수 있습니다. 일부 저장소 탐색기에서 기존 Blob 저장소 계정에서 저장소 계정의 다른 컨테이너로 또는 SSE가 설정된 새 저장소 계정으로 데이터를 복사할 수도 있습니다.

자세한 내용은 [Azure 저장소 탐색기](../storage-explorers.md)를 참조하세요.

### <a name="step-4-query-the-status-of-the-encrypted-data"></a>4단계: 암호화된 데이터 상태 쿼리
업데이트된 버전의 저장소 클라이언트 라이브러리가 배포되었으며 이를 통해 개체 상태를 쿼리하여 암호화 여부를 확인할 수 있습니다. 현재 Blob 저장소에만 사용할 수 있습니다. 파일 저장소에 대한 지원은 준비 중입니다. 

그동안은 [계정 속성 가져오기](https://msdn.microsoft.com/library/azure/mt163553.aspx) 를 호출하여 저장소 계정에 암호화가 사용되었는지 확인하거나 Azure 포털에서 저장소 계정 속성을 볼 수 있습니다.

## <a name="encryption-and-decryption-workflow"></a>암호화 및 암호 해독 워크플로
암호화/암호 해독 워크플로에 대한 간단한 설명은 다음과 같습니다.

* 고객이 저장소 계정에 대해 암호화를 설정합니다.
* 고객이 새 데이터(PUT Blob, PUT 블록, PUT 페이지, PUT 파일 등)를 Blob 또는 파일 저장소에 기록할 경우 모든 기록 내용이 가장 강력한 블록 암호화 중 하나인 256비트 [AES 암호화](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)를 사용하여 암호화됩니다.
* 고객이 데이터(GET Blob 등)에 액세스해야 하는 경우 사용자에게 반환되기 전에 데이터가 자동으로 해독됩니다.
* 암호화를 사용하지 않도록 설정하면 새로운 기록 내용은 더 이상 암호화되지 않으며 기존 암호화된 데이터는 사용자가 다시 작성할 때까지 암호화된 상태로 유지됩니다. 암호화를 사용하는 동안 Blob 또는 파일 저장소에 기록된 내용이 암호화됩니다. 저장소 계정에 대해 암호화를 사용/사용하지 않는 것으로 사용자 전환하는 것으로 데이터의 상태는 변경되지 않습니다.
* 모든 암호화 키는 Microsoft에서 저장, 암호화 및 관리합니다.

## <a name="frequently-asked-questions-about-storage-service-encryption-for-data-at-rest"></a>미사용 데이터에 대한 저장소 서비스 암호화에 대한 질문과 대답
**Q: 기존 클래식 저장소 계정이 있습니다. 이 계정에 SSE를 사용할 수 있나요?**

A: 아니요, SSE는 Resource Manager 저장소 계정에만 지원됩니다.

**Q: 내 클래식 저장소 계정에서 데이터를 암호화하려면 어떻게 해야 하나요?**

A: 새 Resource Manager 저장소 계정을 만들고 [AzCopy](storage-use-azcopy.md) 를 사용하여 기존 클래식 저장소 계정에서 데이터를 새로 만든 Resource Manager 저장소 계정으로 복사합니다. 

클래식 저장소 계정을 리소스 관리자 저장소 계정으로 마이그레이션하는 경우 이 작업은 즉각적이며 계정의 유형을 변경하지만 기존 데이터에는 영향을 주지 않습니다. 새로 기록되는 데이터는 암호화를 사용하도록 설정한 후에만 암호화됩니다. 자세한 내용은 [클래식에서 Resource Manager로 IaaS 리소스의 플랫폼 지원 마이그레이션](https://azure.microsoft.com/blog/iaas-migration-classic-resource-manager/)을 참조하세요. 이 기능은 Blob 및 파일 서비스에 대해서만 지원됩니다.

**Q: 기존의 Resource Manager 저장소 계정이 있습니다. 이 계정에 SSE를 사용할 수 있나요?**

A: 예, 하지만 새로 작성한 데이터만 암호화됩니다. 돌아가 기존에 있는 데이터를 암호화하지 않습니다. File Storage 미리 보기에는 아직 지원되지 않습니다.

**Q: 기존 Resource Manager 저장소 계정에서 현재 데이터를 암호화하고 싶습니다.**

A: Resource Manager 저장소 계정에서 언제든지 SSE를 사용하도록 설정할 수 있습니다. 그러나 이미 있던 데이터는 암호화되지 않습니다. 기존 데이터를 암호화하려면 다른 이름이나 다른 컨테이너에 복사한 후 암호화되지 않은 버전을 제거할 수 있습니다.

**Q: 프리미엄 저장소를 사용 중입니다. SSE를 사용할 수 있나요?**

A: 예, SSE는 표준 저장소 및 프리미엄 저장소 모두에서 지원됩니다.  파일 서비스에 대한 Premium Storage는 지원되지 않습니다.

**Q: 새 저장소 계정을 만들고 SSE를 사용한 경우 이 저장소 계정을 사용하여 새 VM을 만들면 내 VM이 암호화되어 있나요?**

A: 예. 새 저장소 계정을 사용하여 만든 모든 디스크는 SSE가 사용된 후 만들어졌다면 암호화됩니다. VM을 Azure 마켓플레이스를 사용하여 만든 경우 VHD 기본 이미지는 암호화되지 않은 상태로 유지되지만 VM이 작동된 후 작성된 내용은 모두 암호화됩니다.

**Q: Azure PowerShell 및 Azure CLI를 사용하여 SSE가 사용된 새 저장소 계정을 만들 수 있나요?**

A: 예.

**Q: SSE를 사용하는 경우 Azure 저장소 비용은 얼마나 늘어나나요?**

A: 추가 비용은 없습니다.

**Q: 암호화 키는 누가 관리하나요?**

A: 키는 Microsoft에서 관리합니다.

**Q: 나만의 암호화 키를 사용할 수 있나요?**

A: 고객이 자신의 고유한 암호화 키를 가져오는 기능을 제공하기 위한 작업 중에 있습니다.

**Q: 암호화 키에 대한 액세스를 해지할 수 있나요?**

A: 현재는 없습니다. 키는 전적으로 Microsoft에서 관리합니다.

**Q: 새 저장소 계정을 만들 때 기본적으로 SSE가 사용되도록 설정되어 있나요?**

A: SSE는 기본적으로 사용되지 않습니다. Azure 포털로 사용하도록 설정할 수 있습니다. 또한 프로그래밍 방식으로 저장소 리소스 공급자 REST API를 사용하여 이 기능을 사용하도록 설정할 수 있습니다.

**Q: Azure Disk Encryption과 어떻게 다른가요?**

A: 이 기능은 Azure Blob 저장소에서 데이터를 암호화하는 데 사용합니다. Azure 디스크 암호화는 OS 및 IaaS VM에서 데이터 디스크를 암호화하는 데 사용합니다. 자세한 내용은 [저장소 보안 가이드](../storage-security-guide.md)를 참조하세요.

**Q: SSE가 사용되도록 설정된 경우 이동하여 디스크에서 Azure 디스크 암호화를 사용하도록 설정하면 어떻게 되나요?**

A: 이것은 원활하게 작동합니다. 데이터는 두 가지 방법으로 암호화됩니다.

**Q: 내 저장소 계정이 지역 중복되어 복제되도록 설정됩니다. SSE를 사용하도록 설정하는 경우 내 중복 복사본도 암호화되나요?**

A: 예, 저장소 계정의 모든 복사본이 암호화되며 로컬 중복 저장소(LRS), 영역 중복 저장소(ZRS), 지역 중복 저장소(GRS), 읽기 액세스 지역 중복 저장소(RA-GRS)의 모든 중복성 옵션이 지원됩니다.

**Q: 내 저장소 계정에서 암호화를 사용할 수 없습니다.**

A: Resource Manager 저장소 계정인가요? 클래식 저장소 계정은 지원되지 않습니다. 

**Q: SSE는 특정 지역에만 허용되나요?**

A: SSE는 Blob 저장소에 대해 모든 지역에서 제공됩니다. 파일 저장소에 대한 가용성 섹션을 확인하세요. 

**Q: 문제가 있거나 피드백을 제공하고 싶은 경우 어떻게 연락하나요?**

A: 저장소 서비스 암호화에 대한 문제는 [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) 으로 문의하세요.

## <a name="next-steps"></a>다음 단계
Azure 저장소는 여러 개발자가 보안 응용 프로그램을 함께 빌드할 수 있도록 하는 포괄적인 보안 기능을 제공합니다. 자세한 내용은 [저장소 보안 가이드](../storage-security-guide.md)를 참조하세요.

