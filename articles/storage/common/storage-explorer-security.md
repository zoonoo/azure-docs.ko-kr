---
title: Azure Storage 탐색기 보안 가이드 | Microsoft Docs
description: Azure Storage 탐색기에 대 한 보안 지침
services: storage
author: cralvord
ms.service: storage
ms.topic: best-practice
ms.date: 07/30/2020
ms.author: cralvord
ms.openlocfilehash: 283ec9999f9b4362035b6770383984efb0879d49
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92783762"
---
# <a name="azure-storage-explorer-security-guide"></a>Azure Storage 탐색기 보안 가이드

Microsoft Azure Storage 탐색기를 사용 하면 Windows, macOS 및 Linux에서 Azure Storage 데이터를 안전 하 고 안전 하 게 사용할 수 있습니다. 이러한 지침에 따라 데이터를 보호 된 상태로 유지 하도록 보장할 수 있습니다.

## <a name="general"></a>일반

- **항상 최신 버전의 Storage 탐색기을 사용 합니다.** Storage 탐색기 릴리스에는 보안 업데이트가 포함 될 수 있습니다. 최신 상태를 유지 하는 데 도움이 되는 일반적인 보안.
- **신뢰 하는 리소스에만 연결 합니다.** 신뢰할 수 없는 소스에서 다운로드 한 데이터는 악성이 될 수 있으며, 신뢰할 수 없는 소스에 데이터를 업로드 하면 데이터가 손실 되거나 도난당 해질 수 있습니다.
- **가능 하면 항상 HTTPS를 사용 합니다.** Storage 탐색기는 기본적으로 HTTPS를 사용 합니다. 일부 시나리오에서는 HTTP를 사용할 수 있지만 HTTP는 최후의 수단 으로만 사용 해야 합니다.
- **필요한 사용자에 게 필요한 권한만 부여 해야 합니다.** 사용자에 게 리소스에 대 한 액세스 권한을 부여할 때 과도 하 게 허용 되지 않도록 합니다.
- **중요 한 작업을 실행 하는 경우 주의 해야 합니다.** 삭제 및 덮어쓰기와 같은 특정 작업은 되돌릴 수 없으며이로 인해 데이터가 손실 될 수 있습니다. 이러한 작업을 실행 하기 전에 올바른 리소스로 작업 하 고 있는지 확인 합니다.

## <a name="choosing-the-right-authentication-method"></a>올바른 인증 방법 선택

Storage 탐색기는 Azure Storage 리소스에 액세스 하는 다양 한 방법을 제공 합니다. 어떤 방법을 선택 하 든 권장 사항은 다음과 같습니다.

### <a name="azure-ad-authentication"></a>Azure AD 인증

Azure Storage 리소스에 가장 쉽고 안전 하 게 액세스 하는 방법은 Azure 계정으로 로그인 하는 것입니다. 로그인 할 때 Azure AD 인증을 사용 하 여 다음 작업을 수행할 수 있습니다.

- 특정 사용자 및 그룹에 대 한 액세스를 제공 합니다.
- 언제 든 지 특정 사용자 및 그룹에 대 한 액세스를 취소 합니다.
- Multi-factor authentication 요구와 같은 액세스 조건을 적용 합니다.

가능 하면 항상 Azure AD 인증을 사용 하는 것이 좋습니다.

이 섹션에서는 저장소 리소스를 보호 하는 데 사용할 수 있는 두 가지 Azure AD 기반 기술을 설명 합니다.

#### <a name="azure-role-based-access-control-azure-rbac"></a>Azure RBAC(Azure 역할 기반 액세스 제어)

Azure [RBAC (역할 기반 액세스 제어)](../../role-based-access-control/overview.md) 는 azure 리소스에 대 한 세부적인 액세스 제어를 제공 합니다. Azure Portal에서 Azure 역할 및 사용 권한을 관리할 수 있습니다.

Storage 탐색기는 저장소 계정, Blob 및 큐에 대 한 Azure RBAC 액세스를 지원 합니다. 파일 공유 또는 테이블에 액세스 해야 하는 경우 저장소 계정 키를 나열 하는 권한을 부여 하는 Azure 역할을 할당 해야 합니다.

#### <a name="access-control-lists-acls"></a>ACL(액세스 제어 목록)

[Acl (액세스 제어 목록)](../blobs/data-lake-storage-access-control.md) 을 사용 하면 ADLS Gen2 blob 컨테이너에서 파일 및 폴더 수준 액세스를 제어할 수 있습니다. Storage 탐색기를 사용 하 여 Acl을 관리할 수 있습니다.

### <a name="shared-access-signatures-sas"></a>SAS(공유 액세스 서명)

Azure AD 인증을 사용할 수 없는 경우 공유 액세스 서명을 사용 하는 것이 좋습니다. 공유 액세스 서명을 사용 하 여 다음을 수행할 수 있습니다.

- 보안 리소스에 대 한 익명의 제한 된 액세스를 제공 합니다.
- SAP (공유 액세스 정책)에서 생성 된 경우 즉시 SAS를 해지 합니다.

그러나 공유 액세스 서명을 사용 하면 다음을 할 수 없습니다.

- SAS를 사용할 수 있는 사용자를 제한 합니다. 유효한 SAS는 모든 사용자가 사용할 수 있습니다.
- 공유 액세스 정책 (SAP)에서 생성 되지 않은 경우 SAS를 해지 합니다.

Storage 탐색기에서 SAS를 사용 하는 경우 다음 지침을 따르는 것이 좋습니다.

- **SAS 토큰과 Uri의 배포를 제한 합니다.** SAS 토큰과 Uri만 신뢰할 수 있는 개인에 게 배포 합니다. SAS 배포를 제한 하면 SAS가 오용 될 가능성이 줄어듭니다.
- **신뢰 하는 엔터티의 SAS 토큰 및 Uri만 사용 합니다.**
- **가능 하면 SAS 토큰 및 Uri를 생성할 때 SAP (공유 액세스 정책)를 사용 합니다.** 공유 액세스 정책을 기반으로 하는 SAS는 SAP를 삭제 하 여 SAS를 취소할 수 있기 때문에 운영 체제 SAS 보다 더 안전 합니다.
- **최소 리소스 액세스 및 사용 권한으로 토큰을 생성 합니다.** 최소 사용 권한은 SAS가 잘못 사용 되는 경우 수행할 수 있는 잠재적 손상을 제한 합니다.
- **필요한 기간 동안만 유효한 토큰을 생성 합니다.** 짧은 수명은 생성 된 후에 취소할 수 있는 방법이 없기 때문에 운영 체제 SAS에 특히 중요 합니다.

> [!IMPORTANT]
> 서비스 요청 또는 버그 보고서와 같은 문제 해결을 위해 SAS 토큰과 Uri를 공유 하는 경우 항상 SAS의 서명 부분을 하나 이상 교정 합니다.

### <a name="storage-account-keys"></a>Storage 계정 키

저장소 계정 키는 저장소 계정 내에서 서비스 및 리소스에 무제한으로 액세스할 수 있는 권한을 부여 합니다. 따라서 Storage 탐색기에서 리소스에 액세스할 때 키 사용을 제한 하는 것이 좋습니다. 대신 Azure RBAC 기능 또는 SAS를 사용 하 여 액세스를 제공 합니다.

일부 Azure 역할은 저장소 계정 키를 검색할 수 있는 권한을 부여 합니다. 이러한 역할을 가진 개인은 Azure RBAC에서 부여 하거나 거부 한 권한을 효과적으로 피할 수 있습니다. 필요한 경우가 아니면이 사용 권한을 부여 하지 않는 것이 좋습니다.

Storage 탐색기는 저장소 계정 키 (사용 가능한 경우)를 사용 하 여 요청을 인증 하려고 합니다. 설정 ( **서비스 > 저장소 계정 > 키 사용을 사용 하지 않도록** 설정)에서이 기능을 사용 하지 않도록 설정할 수 있습니다. 일부 기능은 클래식 저장소 계정으로 작업 하는 등의 Azure RBAC를 지원 하지 않습니다. 이러한 기능에는 키가 필요 하지만이 설정의 영향을 받지 않습니다.

키를 사용 하 여 저장소 리소스에 액세스 해야 하는 경우 다음 지침을 따르는 것이 좋습니다.

- **계정 키를 누구와도 공유 하지 마세요.**
- **저장소 계정 키를 암호와 같이 처리 합니다.** 키에 액세스할 수 있도록 설정 해야 하는 경우 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)와 같은 보안 저장소 솔루션을 사용 합니다.

> [!NOTE]
> 저장소 계정 키가 실수로 공유 되거나 배포 되었다고 생각 되는 경우 Azure Portal에서 저장소 계정에 대 한 새 키를 생성할 수 있습니다.

### <a name="public-access-to-blob-containers"></a>Blob 컨테이너에 대 한 공용 액세스

Storage 탐색기를 사용 하 여 Azure Blob Storage 컨테이너의 액세스 수준을 수정할 수 있습니다. Private이 아닌 blob 컨테이너는 누구나 해당 컨테이너의 데이터에 대 한 익명 읽기 액세스를 허용 합니다.

Blob 컨테이너에 대 한 공용 액세스를 사용 하도록 설정 하는 경우 다음 지침을 따르는 것이 좋습니다.

- **잠재적으로 중요 한 데이터가 포함 될 수 있는 blob 컨테이너에 대 한 공용 액세스를 사용 하도록 설정 하지 마세요.** Blob 컨테이너가 모든 개인 데이터를 사용할 수 있는지 확인 합니다.
- **Blob 또는 컨테이너 액세스를 사용 하 여 잠재적으로 중요 한 데이터를 blob 컨테이너에 업로드 하지 마세요.** 

## <a name="next-steps"></a>다음 단계

- [보안 권장 사항](../blobs/security-recommendations.md)