---
title: Azure Data Lake Storage Gen2 스토리지 보안 가이드 | Microsoft Docs
description: RBAC 및 스토리지 서비스 암호화를 포함하지만 이에 국한되지 않는 Azure Storage 보안을 유지하는 다양한 방법을 자세히 설명합니다.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 12/04/2018
ms.author: rogarana
ms.component: common
ms.openlocfilehash: d2182942b8d1ce78fd4a72ff387c7a6a1cfead5a
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52976721"
---
# <a name="azure-data-lake-storage-gen2-security-guide"></a>Azure Data Lake Storage Gen2 보안 가이드

Azure Data Lake Storage Gen2 미리 보기는 Azure Storage 계정을 기반으로 하는 기능 세트입니다. 따라서 이 문서의 모든 참조는 계층 구조 네임스페이스를 사용하도록 설정된 Azure Storage 계정(Data Lake Storage Gen2 기능)을 대상으로 합니다.

- Azure Storage에 쓴 모든 데이터는 [SSE(Storage 서비스 암호화)](storage-service-encryption.md)를 사용하여 자동으로 암호화됩니다. 자세한 내용은 [Azure Blob, Files, Tables 및 Queue Storage에 대한 기본 암호화 발표](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/)를 참조하세요.
- Azure AD(Azure Active Directory) 및 RBAC(역할 기반 액세스 제어)는 다음과 같이 리소스 관리 작업 및 데이터 작업 모두에 대한 Azure Storage에 지원됩니다.
    - 저장소 계정으로 범위가 지정된 RBAC 역할을 보안 주체에 할당하고 Azure AD를 사용하여 키 관리와 같은 리소스 관리 작업의 권한을 부여할 수 있습니다.
    - Azure AD 통합은 Azure Storage의 데이터 작업에 대한 미리 보기로 지원됩니다. 구독, 리소스 그룹, 스토리지 계정 또는 개별 파일 시스템으로 범위가 지정된 RBAC 역할은 Azure 리소스에 대한 보안 주체 또는 관리 ID에 할당할 수 있습니다. 자세한 내용은 [Azure Active Directory(미리 보기)를 사용하여 Azure Storage에 대한 액세스 인증](storage-auth-aad.md)을 참조하세요.
- [공유 액세스 서명](../storage-dotnet-shared-access-signature-part-1.md)을 사용하여 Azure Storage의 데이터 개체에 대한 위임된 액세스 권한을 부여할 수 있습니다.

이 문서에서는 Azure Storage에서 사용할 수 있는 이러한 각 보안 기능에 대해 간략히 설명합니다. 각 항목을 좀 더 자세히 확인할 수 있도록 각 기능의 세부 정보를 제공하는 문서에 대한 링크가 제공됩니다.

다음은 이 문서에 설명된 항목입니다.

* [관리 평면 보안](#management-plane-security) - Storage 계정 보안 유지

  관리 평면은 저장소 계정 관리에 사용되는 리소스로 구성됩니다. 이 섹션에서는 Azure Resource Manager 배포 모델 및 RBAC(역할 기반 Access Control)를 사용하여 저장소 계정에 대한 액세스를 제어하는 방법을 다룹니다. 또한 저장소 계정 키의 관리와 이러한 키를 다시 생성하는 방법에 대해서도 설명합니다.
* [데이터 평면 보안](#data-plane-security) – 데이터에 대한 액세스 보안 유지

  이 섹션에서는 공유 액세스 서명 및 저장된 액세스 정책을 사용하여 Storage 계정에 있는 실제 데이터 개체, 즉 파일 및 디렉터리에 대한 액세스를 허용하는 방법을 살펴봅니다. 서비스 수준 SAS 및 계정 수준 SAS에 대한 설명이 제공됩니다. 또한 특정 IP 주소(또는 IP 주소 범위)에 대한 액세스를 제한하는 방법, 사용되는 프로토콜을 HTTPS로 제한하는 방법 및 만료를 기다리지 않고 공유 액세스 서명을 해지하는 방법도 알아봅니다.
* [전송 중 암호화](#encryption-in-transit)

이 섹션에서는 Data Lake Storage Gen2를 사용하도록 설정된 스토리지 계정에서 데이터를 보내거나 받을 때 데이터를 보호하는 방법에 대해 설명합니다. 여기서는 추천되는 HTTPS 사용 방법을 살펴봅니다.

## <a name="management-plane-security"></a>관리 평면 보안

관리 평면은 저장소 계정 자체에 영향을 주는 작업으로 구성되어 있습니다. 예를 들어 저장소 계정을 생성 또는 삭제하거나, 구독에서 저장소 계정 목록을 가져오거나, 저장소 계정 키를 검색하거나, 저장소 계정 키를 다시 생성할 수 있습니다.

이 가이드에서는 Data Lake Storage Gen2 기능을 사용하여 스토리지 계정을 만드는 수단인 Resource Manager 배포 모델을 중점적으로 설명합니다. 전체 구독에 대한 액세스를 허용하지 않고 Resource Manager 저장소 계정을 사용하면 RBAC(역할 기반 Access Control)를 사용하여 관리 평면에 대해 보다 명확히 한정된 수준으로 액세스를 제어할 수 있습니다.

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>RBAC(역할 기반 Access Control)를 사용하여 저장소 계정의 보안을 유지하는 방법

RBAC의 기능과 사용 방법에 대해 살펴보겠습니다. 각 Azure 구독에는 Azure Active Directory가 있습니다. 해당 디렉터리의 사용자, 그룹 및 응용 프로그램에 리소스 관리자 배포 모델을 사용하는 Azure 구독에서 리소스를 관리할 수 있는 권한을 부여할 수 있습니다. 이러한 유형의 보안을 RBAC(역할 기반 액세스 제어)라고 합니다. 이 액세스를 관리하려면 Azure Portal에서 **액세스 제어(IAM)** 를 사용합니다.

리소스 관리자 모델에서는 Azure Active Directory를 사용하여 리소스 그룹에 저장소 계정을 추가하고 해당 특정 저장소 계정의 관리 평면에 대한 액세스를 제어합니다. 예를 들어 특정 사용자에게는 저장소 계정 키에 액세스할 수 있는 기능을 제공하고 다른 사용자에게는 저장소 계정에 대한 정보는 볼 수 있지만 저장소 계정 키에는 액세스하지 못하게 할 수 있습니다.

#### <a name="granting-access"></a>액세스 권한 부여

올바른 범위에서 사용자, 그룹 및 응용 프로그램에 적절한 RBAC 역할을 할당하여 액세스 권한을 부여합니다. 전체 구독에 대한 액세스 권한을 부여하려면 구독 수준에서 역할을 할당합니다. 리소스 그룹 자체에 대한 권한을 부여하여 리소스 그룹의 모든 리소스에 대한 액세스 권한을 부여할 수 있습니다. 또한 저장소 계정 등의 특정 리소스에 특정 역할을 할당할 수도 있습니다.

RBAC를 사용하여 Azure Storage 계정 관리 작업에 액세스하는 작업과 관련해서 알아야 할 주요 사항은 다음과 같습니다.

* 누군가에게 스토리지 계정의 데이터 개체에 액세스할 수 있는 권한을 부여하려면 스토리지 계정 키를 읽을 수 있는 권한을 부여할 수 있습니다. 그러면 해당 사용자가 해당 키를 사용하여 파일과 디렉터리에 액세스할 수 있습니다.
* 특정 사용자 계정, 사용자 그룹 또는 특정 응용 프로그램에 역할을 할당할 수 있습니다.
* 각 역할에는 작업 목록 및 작업 안 함 목록이 있습니다. 예를 들어 Virtual Machine 참여자 역할에는 저장소 계정 키를 읽을 수 있도록 하는 “listKeys” 작업이 있습니다. 참여자 역할에는 Active Directory의 사용자에 대한 액세스를 업데이트하는 것과 같은 작업을 포함하는 “작업 안 함” 목록이 있습니다.
* 저장소에 대한 역할에는 다음 역할이 포함됩니다(이에 국한되지 않음).

  * 소유자 - 액세스를 제외한 모든 것을 관리할 수 있습니다.
  * 참여자 – 액세스 할당을 제외하고 소유자가 수행할 수 있는 모든 작업을 수행할 수 있습니다. 이 역할을 가진 사람은 저장소 계정 키를 보고 다시 생성할 수 있습니다. 저장소 계정 키가 있으면 데이터 개체에 액세스할 수 있습니다.
  * 읽기 권한자 - 암호를 제외한 저장소 계정에 대한 정보를 볼 수 있습니다. 예를 들어 저장소 계정에 대한 읽기 권한자 권한이 있는 역할을 다른 사람에게 할당하는 경우 해당 사용자는 저장소 계정의 속성을 볼 수 있지만 속성을 변경하거나 저장소 계정 키를 볼 수는 없습니다.
  * Storage 계정 참여자 – Storage 계정을 관리할 수 있고, 구독의 리소스 그룹 및 리소스를 읽을 수 있고, 구독 리소스 그룹 배포를 만들고 관리할 수 있습니다. 저장소 계정 키에도 액세스할 수 있으므로 데이터 평면에도 액세스할 수 있습니다.
  * 사용자 액세스 관리자 - 저장소 계정에 대한 사용자 액세스를 관리할 수 있습니다. 예를 들어 특정 사용자에게 읽기 관리자 액세스 권한을 부여할 수 있습니다.
  * Virtual Machine 참여자 - 가상 컴퓨터를 관리할 수 있으나 해당 컴퓨터가 연결된 저장소 계정은 관리할 수 없습니다. 이 역할이 있으면 저장소 계정 키를 나열할 수 있습니다. 즉, 이 역할을 할당 받은 사용자는 데이터 평면을 업데이트할 수 있습니다.

    사용자가 가상 머신을 만들 수 있으려면 저장소 계정에 해당 VHD 파일을 만들 수 있어야 합니다. 이렇게 하려면 저장소 계정 키를 검색하고 VM을 만드는 API에 전달할 수 있어야 합니다. 따라서 이러한 권한이 있어야 저장소 계정 키를 나열할 수 있습니다.
* 사용자 지정 역할을 정의할 수 있으면 Azure 리소스에 대해 수행할 수 있는 사용 가능한 작업 목록에서 작업 집합을 작성할 수 있습니다.
* 사용자에게 역할을 할당하려면 먼저 Azure Active Directory에서 해당 사용자를 설정해야 합니다.
* PowerShell 또는 Azure CLI를 사용하여 누가 어떤 사용자에게 어떤 종류의 액세스 권한을 어떤 범위까지 부여/해지했는지에 대한 보고서를 만들 수 있습니다.

#### <a name="resources"></a>리소스

* [Azure Active Directory 역할 기반 Access Control](../../role-based-access-control/role-assignments-portal.md)

  이 문서에서는 Azure Active Directory 역할 기반 Access Control 및 작동 방식에 대해 설명합니다.
* [RBAC: 기본 제공 역할](../../role-based-access-control/built-in-roles.md)

  이 문서에서는 RBAC에서 사용할 수 있는 기본 제공 역할의 모든 사항을 자세히 설명합니다.
* [리소스 관리자 배포 및 클래식 배포 이해](../../azure-resource-manager/resource-manager-deployment-model.md)

### <a name="managing-your-storage-account-keys"></a>Storage 계정 키 관리

Storage 계정 키는 Azure에서 생성되는 512비트 문자열로, Storage 계정에 저장된 데이터 개체(예: Blob, 테이블 내 엔터티, 큐 메시지, Azure 파일 공유의 파일)에 액세스하기 위해 Storage 계정 이름과 함께 사용될 수 있습니다. 저장소 계정 키에 대한 액세스를 제어하면 해당 저장소 계정의 데이터 평면에 대한 액세스도 제어됩니다.

각 저장소 계정에는 [Azure Portal](http://portal.azure.com/) 및 PowerShell cmdlet에서 "키 1"과 "키 2"로 지칭되는 두 개의 키가 있습니다. 이러한 키는 [Azure 포털](https://portal.azure.com/), PowerShell, Azure CLI를 사용하거나 .NET Storage Client Library 또는 Azure Storage Services REST API를 사용하여 프로그래밍 방식으로 다시 생성할 수도 있습니다.

저장소 계정 키를 다시 생성하는 이유는 많습니다.

* 보안상의 이유로 정기적으로 다시 생성할 수 있습니다.
* 누군가가 응용 프로그램에 간신히 해킹하여 구성 파일에 하드 코딩되었거나 저장된 키를 검색함으로써 사용자의 저장소 계정에 대한 모든 권한을 얻게 되면 저장소 계정 키를 다시 생성해야 합니다.
* 키를 다시 생성하는 또 다른 경우는 팀이 Storage 계정 키를 유지하는 저장소 탐색기 응용 프로그램을 사용하고 있는데 팀 구성원 중 하나가 팀을 탈퇴하는 경우입니다. 응용 프로그램은 계속 작동되고 해당 구성원이 나간 후에도 저장소 계정에 대한 액세스 권한을 부여합니다. 실제로 이것이 액세스 수준 공유 액세스 서명을 만드는 주요 이유입니다. 이러한 문제를 방지하기 위해 구성 파일에 액세스 키를 저장하는 대신 계정 수준 SAS를 사용할 수 있습니다.

#### <a name="key-regeneration-plan"></a>키 다시 생성 계획

사용 중인 키를 계획 없이 그냥 다시 생성하고 싶지는 않을 것입니다. 이렇게 하면 해당 저장소 계정에 대한 모든 액세스가 차단되어 심각한 업무 중단이 발생할 수 있습니다. 이 때문에 두 개의 키를 준비하는 것입니다. 한 번에 하나의 키를 다시 생성해야 합니다.

키를 다시 생성하기 전에 저장소 계정에 종속되는 모든 응용 프로그램의 목록과 Azure에서 사용하는 다른 서비스 목록을 준비해야 합니다. 예를 들어 저장소 계정에 종속된 Azure Media Services를 사용하는 경우 키를 다시 생성한 후 미디어 서비스와 액세스 키를 다시 동기화해야 합니다. 저장소 탐색기와 같은 응용 프로그램을 사용하는 경우 해당 응용 프로그램에도 새 키를 제공 해야 합니다. 해당 VHD 파일이 저장소 계정에 저장되어 있는 VM의 경우 저장소 계정 키를 다시 생성해도 영향을 받지 않습니다.

Azure 포털에서 키를 다시 생성할 수 있습니다. 키가 다시 생성되면 Storage 서비스에서 동기화되는 데 최대 10분이 걸릴 수 있습니다.

준비가 완료되면 키를 변경하는 방법을 자세히 설명하는 다음의 일반 프로세스를 참조하세요. 이 경우 사용자가 현재 키 1을 사용하고 있으며 키 2를 대신 사용하도록 모든 항목을 변경하려 한다고 가정합니다.

1. 확실한 결과를 얻기 위해 키 2를 다시 생성합니다. 이 작업은 Azure 포털에서 수행할 수 있습니다.
2. 저장소 키가 저장되는 모든 응용 프로그램에서 키 2의 새 값을 사용하도록 저장소 키를 변경합니다. 응용 프로그램을 테스트한 후 게시합니다.
3. 모든 응용 프로그램과 서비스가 성공적으로 시작되고 실행되면 키 1을 다시 생성합니다. 이렇게 하면 새 키를 명시적으로 부여받지 못한 사람은 누구나 더 이상 저장소 계정에 액세스할 수 없습니다.

현재 키 2를 사용할 경우 동일한 프로세스를 사용할 수 있지만 키 이름을 되돌려야 합니다.

며칠에 걸쳐 마이그레이션을 수행하여 각 응용 프로그램이 새 키를 사용하도록 변경한 후 게시할 수 있습니다. 이러한 작업이 모두 완료되면 돌아가서 더 이상 작동하지 않도록 이전 키를 다시 생성해야 합니다.

다른 옵션은 저장소 계정 키를 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)에 암호로 추가하고 응용 프로그램이 해당 위치에서 키를 검색하도록 하는 것입니다. 그런 다음 키를 다시 생성하고 Azure Key Vault를 업데이트하면 응용 프로그램이 Azure Key Vault에서 자동으로 새 키를 선택하게 되므로 응용 프로그램을 다시 배포하지 않아도 됩니다. 필요할 때마다 애플리케이션에서 키를 읽도록 하거나 키를 메모리에 캐시할 수 있으며, 이 키를 사용할 때 실패하면 Azure Key Vault에서 키를 다시 검색합니다.

또한 Azure Key Vault를 사용하면 저장소 키에 대한 보안이 한 층 더 강화됩니다. 이 방법을 사용하는 경우 저장소 키가 구성 파일에 하드 코딩되지 않게 되며, 누군가가 특정 권한 없이도 키에 액세스할 수 있는 공간이 없어지는 셈이 됩니다.

Azure Key Vault를 사용하면 Azure Active Directory를 사용하여 키에 대한 액세스를 제어할 수 있다는 또 다른 장점도 있습니다. 즉, Azure Key Vault에서 키를 검색해야 하는 다양한 응용 프로그램에 액세스 권한을 부여할 수 있으며, 특별히 권한을 부여 받지 못한 다른 응용 프로그램은 키에 액세스할 수 없다는 것을 알 수 있습니다.

> [!NOTE]
> 모든 애플리케이션에서 키 중 하나만 동시에 사용하는 것이 좋습니다. 어떤 경우에는 키 1을 사용하고, 다른 경우에는 키 2를 사용하면 어떤 응용 프로그램이 액세스 권한을 상실해야만 키를 순환할 수 있게 됩니다.

#### <a name="resources"></a>리소스

* [Azure Portal에서 저장소 계정 설정 관리](storage-account-manage.md)
* [Azure Storage 리소스 공급자 REST API 참조](https://msdn.microsoft.com/library/mt163683.aspx)

## <a name="data-plane-security"></a>데이터 평면 보안
데이터 평면 보안은 Azure Storage에 저장된 데이터 개체를 보호하는 데 사용되는 방법을 나타냅니다. 데이터가 전송되는 동안 데이터 및 보안을 암호화하는 방법을 살펴보았습니다. 그렇다면 개체에 대한 액세스를 제어하는 방법은 어디에서 확인할까요?

Azure Storage에서 데이터 개체에 대한 액세스를 인증하는 데 다음을 포함한 세 가지 옵션이 있습니다.

- Azure AD를 사용하여 파일 시스템 및 큐에 대한 액세스 권한을 부여합니다(미리 보기). Azure AD는 코드에서 암호를 저장할 필요성 제거를 포함하여 권한 부여에 대한 다른 인증 방법에 비해 여러 가지 이점을 제공합니다. 자세한 내용은 [Azure Active Directory(미리 보기)를 사용하여 Azure Storage에 대한 액세스 인증](storage-auth-aad.md)을 참조하세요. 
- 저장소 계정 키를 사용하여 공유 키를 통해 액세스 권한을 부여합니다. 공유 키를 통한 권한 부여는 응용 프로그램에 저장소 계정 키를 저장해야 하므로 가능한 경우 대신 Azure AD를 사용하는 것이 좋습니다. 프로덕션 응용 프로그램 또는 Azure 테이블 및 파일에 대한 액세스 권한 부여의 경우 Azure AD 통합이 미리 보기로 제공되는 동안 계속해서 공유 키를 사용합니다.
- 공유 액세스 서명을 사용하여 특정 시간 동안 특정 데이터 개체에 제어된 권한을 부여합니다.

권한 부여를 통해 액세스를 제한할 뿐 아니라 [방화벽 및 가상 네트워크](storage-network-security.md)를 사용하여 네트워크 규칙에 따라 저장소 계정에 대한 액세스를 제한할 수도 있습니다.  이 방법을 통해 공용 인터넷 트래픽에 대한 액세스를 거부하고 특정 Azure Virtual Network 또는 공용 인터넷 IP 주소 범위에 대한 액세스 권한만 부여할 수 있습니다.

### <a name="storage-account-keys"></a>Storage 계정 키

Storage 계정 키는 Azure에서 생성되는 512비트 문자열로, Storage 계정에 저장된 데이터 개체에 액세스하기 위해 Storage 계정 이름과 함께 사용될 수 있습니다.

예를 들어 파일을 읽을 수 있습니다. Azure 포털을 통해 또는 많은 Storage Explorer 응용 프로그램 중 하나를 사용하여 이러한 많은 작업을 수행할 수 있습니다. 또한 REST API를 사용하여 이러한 작업을 수행하는 코드를 작성할 수도 있습니다.

[관리 평면 보안](#management-plane-security) 섹션에서 설명한 대로 Azure Resource Manager 모델을 사용하는 스토리지 계정의 스토리지 키에 대한 액세스는 RBAC(역할 기반 액세스 제어)를 통해 제어할 수 있습니다.

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>공유 액세스 서명 및 저장된 액세스 정책을 사용하여 계정의 개체에 대한 액세스를 위임하는 방법

공유 액세스 서명은 URI에 추가할 수 있는 보안 토큰을 포함하는 문자열로, 저장소 개체에 대한 액세스 권한을 위임하고 사용 권한 및 액세스의 날짜/시간 범위와 같은 제한을 지정할 수 있도록 합니다.

파일 또는 디렉터리에 대한 액세스 권한을 부여할 수 있습니다.

한 고객에게 Blob Storage의 파일 시스템에 사진을 업로드하는 데 사용할 수 있는 SAS 토큰을 제공하고, 이러한 사진을 읽을 수 있는 권한을 웹 애플리케이션에 부여할 수 있습니다. 두 경우에서 중요한 측면은 각기 다릅니다. 즉, 각 응용 프로그램에 해당 작업을 수행하는 데 필요한 액세스 권한만 부여할 수 있습니다. 이러한 작업은 공유 액세스 서명을 사용하여 수행할 수 있습니다.

#### <a name="why-you-want-to-use-shared-access-signatures"></a>공유 액세스 서명을 사용하려는 이유

저장소 계정 키를 제공하는 대신 SAS를 사용하려고 하는 이유는 무엇일까요? 어떤 방법이 훨씬 더 쉬울까요? 저장소 계정 키를 제공하는 것은 저장소 왕국의 키를 공유하는 것과 비슷합니다. 완전한 액세스 권한을 부여하게 됩니다. 누군가가 사용자의 키를 사용하여 전체 음악 라이브러리를 저장소 계정에 업로드할 수 있습니다. 파일을 바이러스에 감염된 버전으로 바꿀 수도 있고 데이터를 훔칠 수도 있습니다. 저장소 계정에 대해 무제한 액세스 권한을 부여하는 일은 가볍게 넘어갈 수 있는 일이 아닙니다.

공유 액세스 서명을 사용하여 제한된 기간 동안만 필요한 사용 권한을 클라이언트에 제공할 수 있습니다. 예를 들어 누군가가 사용자의 계정에 파일을 업로드하는 경우 파일을 업로드할 수 있을 만큼 충분한 시간(파일의 크기에 따라 다름) 동안 해당 사용자에게 쓰기 액세스 권한을 부여할 수 있습니다. 또한 마음이 바뀌면 부여한 권한을 취소할 수 있습니다.

그뿐 아니라 SAS를 사용하여 수행한 요청이 Azure 외부의 특정 IP 주소 또는 IP 주소 범위로 제한되도록 지정할 수 있습니다. 특정 프로토콜(HTTPS 또는 HTTP/HTTPS)을 사용하여 요청이 수행되도록 요구할 수도 있습니다. 즉, HTTPS 트래픽만 허용하려면 필요한 프로토콜을 HTTPS로만 설정할 수 있으며 이 경우 HTTP 트래픽은 차단됩니다.

#### <a name="definition-of-a-shared-access-signature"></a>공유 액세스 서명의 정의

공유 액세스 서명은 허용되는 액세스에 대한 정보와 액세스가 허용되는 기간을 제공하는

리소스를 가리키는 URL에 추가된 쿼리 매개 변수 집합입니다. 다음은 예제입니다. 이 URI는 5분 동안 Blob에 대한 읽기 권한을 제공합니다. SAS 쿼리 매개 변수는 URL로 인코딩되어야 합니다(예: 콜론(:)의 경우 %3A, 공백의 경우 %20).

```
http://mystorage.dfs.core.windows.net/myfilesystem/myfile.txt (URL to the file)
?sv=2015-04-05 (storage service version)
&st=2015-12-10T22%3A18%3A26Z (start time, in UTC time and URL encoded)
&se=2015-12-10T22%3A23%3A26Z (end time, in UTC time and URL encoded)
&sr=b (resource is a blob)
&sp=r (read access)
&sip=168.1.5.60-168.1.5.70 (requests can only come from this range of IP addresses)
&spr=https (only allow HTTPS requests)
&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D (signature used for the authentication of the SAS)
```

#### <a name="how-the-shared-access-signature-is-authorized-by-the-azure-storage-service"></a>Azure Storage 서비스에서 공유 액세스 서명에 권한을 부여하는 방법

저장소 서비스가 요청을 받으면 입력 쿼리 매개 변수를 수용한 후 호출 프로그램과 동일한 방법을 사용하여 서명을 만듭니다. 그런 후 두 서명을 비교합니다. 두 서명이 일치하면 저장소 서비스는 저장소 서비스 버전이 유효한지 확인하고, 현재 날짜 및 시간이 지정된 기간 내에 속하는지 확인하고, 요청된 액세스가 수행된 요청과 일치할 수 있습니다.

예를 들어 위의 URL이 Blob 대신 파일을 가리킨다면 공유 액세스 서명은 Blob에 대한 것이라고 지정하고 있으므로 이 요청은 실패할 것입니다. 호출되는 REST 명령이 Blob을 업데이트하는 명령이라면 공유 액세스 서명은 읽기 액세스만 허용된다고 지정하고 있으므로 실패할 것입니다.

#### <a name="types-of-shared-access-signatures"></a>공유 액세스 서명의 유형

* 서비스 수준 SAS를 사용하여 저장소 계정의 특정 리소스에 액세스할 수 있습니다. 이에 대한 몇 가지 예로 파일 시스템에서 파일 목록을 검색하거나 파일을 다운로드합니다.
* 서비스 수준 SAS를 사용하여 서비스 수준 SAS가 사용될 수 있는 모든 항목에 액세스할 수 있습니다. 또한 서비스 수준 SAS를 통해 허용되지 않는 리소스에 대한 옵션(예: 파일 시스템을 만들기)이 제공될 수 있습니다.

#### <a name="creating-a-sas-uri"></a>SAS URI 만들기

1. 필요할 때마다 모든 쿼리 매개 변수를 정의하여 URI를 만들 수 있습니다.

   이 방식은 유연한 방법이지만 매번 비슷한 매개 변수의 논리적 집합을 사용한다면 저장된 액세스 정책을 사용하는 것이 더 좋습니다.
2. 전체 파일 시스템, 파일 공유, 테이블 또는 큐에 대한 저장된 액세스 정책을 만들 수 있습니다. 그런 다음 이 정책을 만드는 SAS URI의 기반으로 사용할 수 있습니다. 저장된 액세스 정책을 기준으로 하는 사용 권한은 쉽게 해지할 수 있습니다. 각 파일 시스템, 큐, 테이블 또는 파일 공유에 대해 최대 5개의 정책을 정의할 수 있습니다.

   예를 들어 많은 사람들이 특정 파일 시스템의 Blob을 읽을 수 있도록 하려면 다른 설정은 항상 동일하게 유지하는 한편 "읽기 액세스 권한 부여"라는 저장된 액세스 정책을 만들 수 있습니다. 그런 다음 해당 저장된 액세스 정책의 설정을 사용하고 만료 날짜/시간을 지정하여 SAS URI를 만들 수 있습니다. 이렇게 하면 매번 모든 쿼리 매개 변수를 지정할 필요가 없습니다.

#### <a name="revocation"></a>해지

SAS가 손상되었거나, 회사 보안 또는 규정 준수 요구 때문에 변경하려는 경우를 가정해 봅니다. 해당 SAS를 사용하여 리소스에 대한 액세스를 해지하려면 어떻게 할까요? SAS URI를 만든 방법에 따라 다릅니다.

임시 URI를 사용하는 경우 세 가지 옵션이 있습니다. 만료 기간이 짧은 정책을 사용하여 SAS 토큰을 발급하고 해당 SAS가 만료되기를 기다릴 수 있습니다. 리소스의 이름을 변경하거나 삭제할 수 있습니다(토큰 범위가 단일 개체로 지정되었다고 가정). 저장소 계정 키를 변경할 수 있습니다. 이 마지막 옵션은 해당 저장소 계정을 사용하는 서비스 수에 따라 지대한 영향을 미칠 수 있으므로 계획 없이 수행할 수 있는 단순한 작업은 아닐 것입니다.

저장된 액세스 정책에서 파생된 SAS를 사용하는 경우 저장된 액세스 정책을 취소하여 액세스 권한을 제거할 수 있습니다. 즉, 이미 만료된 상황이므로 그냥 변경하거나 함께 제거할 수 있습니다. 이러한 작업은 즉시 적용되며 해당 저장된 액세스 정책을 사용하여 만든 모든 SAS가 무효화됩니다. 저장된 액세스 정책을 업데이트하거나 제거하면 SAS를 통해 해당 특정 파일 시스템, 파일 공유, 테이블 또는 큐에 액세스하는 사용자에게 영향을 줄 수 있지만, 이전 SAS가 무효화될 때 클라이언트에서 새 SAS를 요청하도록 작성된 경우에는 정상적으로 작동합니다.

저장된 액세스 정책에서 파생된 SAS를 사용하면 해당 SAS를 즉시 해지할 수 있으므로 가능한 경우 항상 저장된 액세스 정책을 사용하는 것이 바람직합니다.

#### <a name="resources"></a>리소스

공유 액세스 서명 및 저장된 액세스 정책 사용에 대한 자세한 내용과 예제를 보려면 다음 문서를 참조하세요.

* 다음은 참조 문서입니다.

  * [서비스 SAS(영문)](https://msdn.microsoft.com/library/dn140256.aspx)

    이 문서에서는 Blob, 큐 메시지, 테이블 범위 및 파일에서 서비스 수준 SAS를 사용하는 예제를 제공합니다.
  * [서비스 SAS 생성(영문)](https://msdn.microsoft.com/library/dn140255.aspx)
  * [계정 SAS 생성(영문)](https://msdn.microsoft.com/library/mt584140.aspx)
* .NET 클라이언트 라이브러리를 사용하여 공유 액세스 서명 및 저장된 액세스 정책을 만드는 방법에 대한 자습서입니다.

  * [SAS(공유 액세스 서명) 사용](../storage-dotnet-shared-access-signature-part-1.md)
  * [공유 액세스 서명 2부: Blob Service를 사용하여 SAS 만들기 및 사용](../blobs/storage-dotnet-shared-access-signature-part-2.md)

    이 문서에서는 SAS 모델에 대한 설명, 공유 액세스 서명의 예, SAS에 대한 권장 모범 사용 사례를 제공합니다. 부여된 사용 권한을 해지하는 방법도 설명합니다.

* 인증

  * [Azure Storage 서비스에 대한 인증](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* 공유 액세스 서명 시작 자습서

  * [SAS 시작 자습서(영문)](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

## <a name="encryption-in-transit"></a>전송 중 암호화

### <a name="transport-level-encryption--using-https"></a>전송 수준 암호화 - HTTPS 사용

Azure Storage 데이터의 보안을 유지하기 위해 수행해야 하는 또 다른 단계는 클라이언트와 Azure Storage 간에 데이터를 암호화하는 것입니다. 첫 번째 권장 지침은 항상 [HTTPS](https://en.wikipedia.org/wiki/HTTPS) 프로토콜을 사용하여 공용 인터넷에서의 통신 보안을 유지하는 것입니다.

보안 통신 채널을 포함하려면 REST API를 호출하거나 저장소의 개체를 액세스할 때는 항상 HTTPS를 사용해야 합니다. 또한 Azure Storage 개체에 대한 액세스를 위임하는 데 사용할 수 있는 **공유 액세스 서명**에는 공유 액세스 서명을 사용할 때 HTTPS 프로토콜만 사용할 수 있도록 지정하여 SAS 토큰이 있는 링크를 보내는 누구든지 적절한 프로토콜을 사용할 수 있도록 하는 옵션이 포함되어 있습니다.

저장소 계정에 대해 [보안 전송 필요](../storage-require-secure-transfer.md)를 설정하여 저장소 계정의 개체에 액세스하도록 REST API를 호출할 때 HTTPS 사용을 적용할 수 있습니다. 설정된 경우 HTTP를 사용한 연결은 거부됩니다.

## <a name="encryption-at-rest"></a>휴지 상태의 암호화

### <a name="storage-service-encryption-sse"></a>SSE(Storage 서비스 암호화)

SSE는 모든 저장소 계정에 대해 사용되도록 설정되며 사용되지 않도록 설정할 수 없습니다. SSE는 Azure Storage에 쓸 때 자동으로 데이터를 암호화합니다. Azure Storage에서 데이터를 읽을 때 데이터가 반환되기 전에 Azure Storage에서 암호가 해독됩니다. SSE를 사용하여 코드를 수정하거나 응용 프로그램에 코드를 추가하지 않고도 데이터를 보호할 수 있습니다.

Microsoft 관리 키 또는 자체 사용자 지정 키를 사용할 수 있습니다. Microsoft에서는 관리 키를 생성하고 내부 Microsoft 정책에 정의된 대로 키의 정기적인 순환뿐 아니라 보안 저장도 처리합니다. 사용자 지정 키 사용에 대한 자세한 내용은 [Azure Key Vault의 고객 관리 키를 사용하는 Storage 서비스 암호화](storage-service-encryption-customer-managed-keys.md)를 참조하세요.