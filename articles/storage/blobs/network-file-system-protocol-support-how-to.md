---
title: NFS 3.0 프로토콜을 사용하여 Azure Blob Storage 탑재(미리 보기) | Microsoft Docs
description: NFS 3.0 프로토콜을 사용하여 온-프레미스에서 실행되는 Azure VM(가상 머신) 또는 클라이언트에서 Blob Storage에 컨테이너를 탑재하는 방법을 알아봅니다.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 2a37d206955e3372b9ecf97be8d27142bd417192
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490457"
---
# <a name="mount-blob-storage-by-using-the-network-file-system-nfs-30-protocol-preview"></a>NFS(Network File System) 3.0 프로토콜을 사용하여 Blob Storage 탑재(미리 보기)

NFS 3.0 프로토콜을 사용하여 온-프레미스에서 실행되는 Linux 기반 Azure VM(가상 머신) 또는 Linux 시스템에서 Blob Storage에 컨테이너를 탑재할 수 있습니다. 이 문서에서는 단계별 지침을 제공합니다. Blob Storage에서 NFS 3.0 프로토콜 지원에 대한 자세한 내용은 [Azure Blob Storage에서 NFS(Network File System) 3.0 프로토콜 지원(미리 보기)](network-file-system-protocol-support.md)을 참조하세요.

## <a name="step-1-register-the-nfs-30-protocol-feature-with-your-subscription"></a>1단계: 구독에 NFS 3.0 프로토콜 기능 등록

1. PowerShell 명령 창을 엽니다. 

2. `Connect-AzAccount` 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

   ```powershell
   Connect-AzAccount
   ```

3. ID가 둘 이상의 구독과 연결된 경우 활성 구독을 설정합니다.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```
   
   `<subscription-id>` 자리 표시자 값을 구독의 ID로 바꿉니다.

4. 다음 명령을 사용하여 `AllowNFSV3` 기능을 등록합니다.

   ```powershell
   Register-AzProviderFeature -FeatureName AllowNFSV3 -ProviderNamespace Microsoft.Storage 
   ```

5. 다음 명령을 사용하여 리소스 공급자를 등록합니다.
    
   ```powershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.Storage   
   ```

## <a name="step-2-verify-that-the-feature-is-registered"></a>2단계: 기능이 등록되었는지 확인 

등록이 승인되기까지 최대 1시간 정도 걸릴 수 있습니다. 등록이 완료되었는지 확인하려면 다음 명령을 사용합니다.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNFSV3
```

## <a name="step-3-create-an-azure-virtual-network-vnet"></a>3단계: Azure VNet(Virtual Network) 만들기

스토리지 계정은 VNet 내에 포함되어야 합니다. 클라이언트는 VNet을 통해 스토리지 계정에 안전하게 연결할 수 있습니다. VNet에 대한 자세한 내용과 VNet을 만드는 방법은 [Virtual Network 설명서](../../virtual-network/index.yml)를 참조하세요.

> [!NOTE]
> 동일한 VNet의 클라이언트는 계정에 컨테이너를 탑재할 수 있습니다. 온-프레미스 네트워크에서 실행되는 클라이언트에서 컨테이너를 탑재할 수도 있지만 먼저 온-프레미스 네트워크를 VNet에 연결해야 합니다. [지원되는 네트워크 연결](network-file-system-protocol-support.md#supported-network-connections)을 참조하세요.

## <a name="step-4-configure-network-security"></a>4단계: 네트워크 보안 구성

계정에서 데이터를 보호하는 유일한 방법은 VNet 및 기타 네트워크 보안 설정을 사용하는 것입니다. 계정 키 인증, Azure AD(Azure Active Directory) 보안 및 ACL(액세스 제어 목록)을 포함하여 데이터를 보호하는 데 사용되는 다른 모든 도구는 NFS 3.0 프로토콜 지원이 설정된 계정에서 아직 지원되지 않습니다. 

계정에서 데이터를 보호하려면 권장 사항: [Blob Storage에 대한 네트워크 보안 권장 사항](security-recommendations.md#networking)을 참조하세요.

## <a name="step-5-create-and-configure-a-storage-account"></a>5단계: 스토리지 계정 만들기 및 구성

NFS 3.0을 사용하여 컨테이너를 탑재하려면 구독에 기능을 등록한 **후** 스토리지 계정을 만들어야 합니다. 기능을 등록하기 전에 존재했던 계정은 사용하도록 설정할 수 없습니다. 

이 기능의 미리 보기 릴리스에서는 NFS 3.0 프로토콜이 [BlockBlobStorage](../blobs/storage-blob-create-account-block-blob.md) 및 [범용 V2](../common/storage-account-overview.md#general-purpose-v2-accounts) 계정에서 지원됩니다.

계정을 구성하는 경우 다음 값을 선택합니다.

|설정 | 프리미엄 성능 | 표준 성능  
|----|---|---|
|위치|사용 가능한 모든 지역 |오스트레일리아 동부, 한국 중부, 미국 동부 및 미국 중남부 지역 중 하나   
|성능|Premium| Standard
|계정 종류|BlockBlobStorage| 범용 V2
|복제|LRS(로컬 중복 스토리지)| LRS(로컬 중복 스토리지)
|연결 방법|퍼블릭 엔드포인트(선택한 네트워크) 또는 프라이빗 엔드포인트 |퍼블릭 엔드포인트(선택한 네트워크) 또는 프라이빗 엔드포인트
|보안 전송 필요|사용 안 함|사용 안 함
|계층 구조 네임스페이스|사용|사용
|NFS V3|사용 |사용 

다른 모든 설정에 대한 기본값을 수락할 수 있습니다. 

## <a name="step-6-create-a-container"></a>6단계: 컨테이너 만들기

다음 도구 또는 SDK 중 하나를 사용하여 스토리지 계정에 컨테이너를 만듭니다.

|도구|SDK|
|---|---|
|[Azure Portal](https://portal.azure.com)|[.NET](data-lake-storage-directory-file-acl-dotnet.md#create-a-container)|
|[AZCopy](../common/storage-use-azcopy-v10.md#transfer-data)|[Java](data-lake-storage-directory-file-acl-java.md)|
|[PowerShell](data-lake-storage-directory-file-acl-powershell.md#create-a-container)|[Python](data-lake-storage-directory-file-acl-python.md#create-a-container)|
|[Azure CLI](data-lake-storage-directory-file-acl-cli.md#create-a-container)|[JavaScript](data-lake-storage-directory-file-acl-javascript.md)|
||[REST (영문)](/rest/api/storageservices/create-container)|

## <a name="step-7-mount-the-container"></a>7단계: 컨테이너 탑재

Linux 시스템에서 디렉터리를 만든 다음, 컨테이너를 스토리지 계정에 탑재합니다.

1. Linux 시스템에서 디렉터리를 만듭니다.

   ```
   mkdir -p /mnt/test
   ```

2. 다음 명령을 사용하여 컨테이너를 탑재합니다.

   ```
   mount -o sec=sys,vers=3,nolock,proto=tcp <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name>  /mnt/test
   ```

   - 이 명령에 표시되는 `<storage-account-name>` 자리 표시자를 스토리지 계정의 이름으로 바꿉니다.  

   - `<container-name>` 자리 표시자를 컨테이너의 이름으로 바꿉니다.

---

## <a name="resolve-common-issues"></a>일반적인 문제 해결

|문제/오류 | 해결 방법|
|---|---|
|`Access denied by server while mounting`|지원되는 서브넷 내에서 클라이언트가 실행되고 있는지 확인합니다. [지원되는 네트워크 위치](network-file-system-protocol-support.md#supported-network-connections)를 참조하세요.|
|`No such file or directory`| 기능이 등록되었는지 확인한 후 탑재할 컨테이너가 생성되었는지 확인합니다. [2단계: 기능이 등록되었는지 확인](#step-2-verify-that-the-feature-is-registered)을 참조하세요. 또한 탑재 명령과 해당 매개 변수를 터미널에 직접 입력해야 합니다. 이 명령의 일부를 복사하여 다른 애플리케이션의 터미널에 붙여넣는 경우 붙여넣은 정보에 숨겨진 문자가 있으면 이 오류가 발생할 수 있습니다.|

## <a name="see-also"></a>참고 항목

[Azure Blob Storage에서 NFS(Network File System) 3.0 프로토콜 지원(미리 보기)](network-file-system-protocol-support.md)
