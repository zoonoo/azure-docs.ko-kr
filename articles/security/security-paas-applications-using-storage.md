---
title: Azure Storage를 사용하여 PaaS 애플리케이션 보안 | Microsoft Docs
description: PaaS 웹 및 모바일 애플리케이션 보안을 위한 Azure Storage 보안 모범 사례에 대해 알아봅니다.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: TomShinder
ms.openlocfilehash: 3ad97c7adb5901c1da1d174d12d5d6a91831cc74
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56108928"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-storage"></a>Azure Storage를 사용하여 PaaS 웹 및 모바일 응용 프로그램 보안을 유지하는 모범 사례
이 문서에서는 PaaS(Platform as a Service) 웹 및 모바일 응용 프로그램 보안을 위한 Azure Storage 보안 모범 사례에 대해 설명합니다. 이러한 모범 사례는 Azure에 대한 Microsoft와 고객의 경험에서 비롯된 것입니다.

Azure에서는 온-프레미스에서는 쉽게 달성할 수 없는 방식으로 저장소를 배포하고 사용할 수 있습니다. Azure Storage를 사용하면 비교적 적은 노력으로 높은 수준의 확장성 및 가용성을 얻을 수 있습니다. Azure Storage는 Windows 및 Linux Azure Virtual Machines의 기반일 뿐만 아니라 대규모 분산 애플리케이션도 지원할 수 있습니다.

Azure Storage는 다음과 같은 4가지 서비스를 제공합니다. Blob Storage, Table Storage, Queue Storage 및 File Storage. 자세한 내용은 [Microsoft Azure Storage 소개](../storage/storage-introduction.md)를 참조하세요.

[Azure Storage 보안 가이드](../storage/common/storage-security-guide.md)는 Azure Storage 및 보안에 대한 자세한 정보를 제공합니다. 이 모범 사례 문서에서는 보안 가이드에 나오는 일부 개념을 소개하고 좀 더 자세한 정보를 원하는 사용자를 위해 보안 가이드 및 기타 원본에 대한 링크를 제공합니다.

이 문서에서는 다음과 같은 모범 사례를 다룹니다.

- SAS(공유 액세스 서명)
- RBAC(역할 기반 액세스 제어)
- 고가치 데이터에 대한 클라이언트 쪽 암호화
- Storage 서비스 암호화


## <a name="use-a-shared-access-signature-instead-of-a-storage-account-key"></a>저장소 계정 키 대신 공유 액세스 서명 사용
액세스 제어는 중요합니다. Azure Storage 액세스를 쉽게 제어할 수 있도록 하기 위해 저장소 계정을 만들 때는 512비트 SAK(저장소 계정 키) 2개가 생성됩니다. 이처럼 키가 중복 생성되므로 일상적인 키 순환 중에 서비스 중단을 방지할 수 있습니다. 

저장소 액세스 키는 우선 순위가 높은 비밀로, 저장소 액세스 제어를 담당하는 사용자만 액세스할 수 있어야 합니다. 잘못된 사용자가 이러한 키에 액세스하면 저장소를 완전히 제어하고 저장소에서 파일을 바꾸거나 삭제하거나 추가할 수 있게 됩니다. 여기에는 조직 또는 고객에게 손상을 줄 수 있는 맬웨어 및 다른 유형의 콘텐츠가 포함됩니다.

저장소의 개체에 대한 액세스를 제공하는 방법이 필요합니다. 보다 세부적인 액세스 권한을 제공하려면 SAS(공유 액세스 서명)를 활용할 수 있습니다. SAS를 사용하면 미리 정의된 시간 간격 동안 특정 사용 권한을 사용하여 저장소의 특정 개체를 공유할 수 있습니다. 공유 액세스 서명을 통해 다음을 정의할 수 있습니다.

- SAS가 유효한 경우 시작 시간 및 만료 시간을 포함하는 간격입니다.
- SAS에서 부여된 권한입니다. 예를 들어 blob의 SAS는 해당 blob에 사용자 읽기 및 쓰기 권한을 부여할 수 있지만 삭제 권한은 부여하지 않습니다.
- Azure Storage가 SAS를 수락하는 선택적 IP 주소 또는 IP 주소 범위입니다. 예를 들어 조직에 속한 IP 주소 범위를 지정할 수 있습니다. 이것은 SAS에 대한 보안의 또 다른 측정값을 제공합니다.
- Azure Storage가 SAS을 수락하는 프로토콜입니다. HTTPS를 사용하여 클라이언트에 대한 액세스를 제한하려면 이 선택적 매개 변수를 사용할 수 있습니다.

SAS를 사용하여 Storage 계정 키를 제공하지 않고도 공유하려는 방식으로 콘텐츠를 공유할 수 있습니다. 애플리케이션에서 항상 SAS를 사용하는 것은 스토리지 계정 키를 손상시키지 않고 저장 리소스를 공유할 수 있는 보안 방법입니다.

공유 액세스 서명에 대해 자세히 알아보려면 [공유 액세스 서명 사용](../storage/common/storage-dotnet-shared-access-signature-part-1.md)을 참조하세요. 

## <a name="use-role-based-access-control"></a>역할 기반 액세스 제어 사용
액세스를 관리하는 또 다른 방법은 RBAC([역할 기반 액세스 제어](../role-based-access-control/overview.md))를 사용하는 것입니다. RBAC를 사용하는 경우 알아야 할 사항 및 최소 권한 보안 원칙에 따라 직원들에게 필요한 정확한 권한을 부여하는 데 주안점을 둡니다. 권한이 너무 많으면 공격자에게 계정이 노출될 수 있고, 권한이 너무 적으면 직원이 업무를 효율적으로 수행할 수 없습니다. RBAC는 Azure에 대한 세밀한 액세스 관리를 제공하여 이 문제를 해결하도록 도와줍니다. 이는 데이터 액세스에 대한 보안 정책을 적용하려는 조직에 필수적입니다.

Azure의 기본 제공 RBAC 역할을 사용하여 사용자에게 권한을 할당할 수 있습니다. 예를 들어 저장소 계정을 관리해야 하는 클라우드 운영자의 경우 저장소 계정 참가자 역할과 클래식 저장소 계정 관리용 클래식 저장소 계정 참가자 역할을 할당할 수 있습니다. VM을 관리해야 하지만 VM이 연결된 가상 네트워크 또는 저장소 계정은 관리할 필요가 없는 클라우드 운영자의 경우 Virtual Machine 참가자 역할에 추가할 수 있습니다.

RBAC와 같은 기능을 사용하여 데이터 액세스 제어를 적용하지 않는 조직은 사용자에게 필요 이상으로 많은 권한을 부여하게 될 수 있습니다. 그로 인해 일부 사용자가 원래는 액세스할 수 없어야 하는 데이터에 액세스할 수 있게 되어 데이터 손상이 발생할 수 있습니다.

RBAC에 대한 자세한 내용은 다음을 참조하세요.

- [RBAC 및 Azure Portal을 사용하여 액세스 관리](../role-based-access-control/role-assignments-portal.md)
- [Azure 리소스에 대한 기본 제공 역할](../role-based-access-control/built-in-roles.md)
- [Azure Storage 보안 가이드](../storage/common/storage-security-guide.md) 

## <a name="use-client-side-encryption-for-high-value-data"></a>고가치 데이터에 대해 클라이언트 쪽 암호화 사용
클라이언트 쪽 암호화를 사용하여 Azure Storage에 데이터를 업로드하기 전에 전송 중인 데이터를 프로그래밍 방식으로 암호화하고, 데이터를 검색할 때 프로그래밍 방식으로 암호를 해독할 수 있습니다. 이를 통해 전송 중인 데이터와 미사용 데이터를 모두 암호화할 수 있습니다. 클라이언트 쪽 암호화는 데이터를 암호화하는 가장 안전한 방법이지만 애플리케이션을 프로그래밍 방식으로 변경하고 키 관리 프로세스를 배치해야 합니다.

또한 클라이언트 쪽 암호화를 사용하면 암호화 키를 단독으로 제어할 수 있습니다. 자체 암호화 키를 생성하고 관리할 수 있습니다. 클라이언트 쪽 암호화는 Azure Storage 클라이언트 라이브러리가 CEK(콘텐츠 암호화 키)를 생성하고 KEK(키 암호화 키)를 사용하여 래핑(암호화)하는 봉투(envelope) 기술을 사용합니다. KEK는 키 식별자로 식별되고 비대칭 키 쌍 또는 대칭 키일 수 있으며 로컬로 관리되거나 [Azure Key Vault](../key-vault/key-vault-whatis.md)에 저장됩니다.

클라이언트 쪽 암호화는 Java 및 .NET 저장소 클라이언트 라이브러리에 기본적으로 제공되어 있습니다. 클라이언트 응용 프로그램 내에서 데이터를 암호화하고 자체 암호화 키를 생성 및 관리하는 방법에 대한 자세한 내용은 [Microsoft Azure Storage용 클라이언트 쪽 암호화 및 Azure Key Vault](../storage/storage-client-side-encryption.md)를 참조하세요.

## <a name="enable-storage-service-encryption-for-data-at-rest"></a>미사용 데이터에 대해 저장소 서비스 암호화 사용
File Storage에 대해 [Storage 서비스 암호화](../storage/storage-service-encryption.md)가 사용되도록 설정되면 데이터는 AES-256 암호화를 사용하여 자동으로 암호화됩니다. Microsoft는 모든 암호화, 해독 및 키 관리를 처리합니다. 이 기능은 LRS 및 GRS 중복 유형에 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 PaaS 웹 및 모바일 애플리케이션 보안을 위한 Azure Storage 보안 모범 사례 모음을 소개했습니다. PaaS 배포 보안 유지에 대한 자세한 내용은 다음을 참조하세요.

- [PaaS 배포 보안](security-paas-deployments.md)
- [Azure App Services를 사용하여 PaaS 웹 및 모바일 애플리케이션 보안](security-paas-applications-using-app-services.md)
- [Azure에서 PaaS 데이터베이스 보안 유지](security-paas-applications-using-sql.md)
