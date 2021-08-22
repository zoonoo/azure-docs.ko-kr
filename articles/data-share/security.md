---
title: Azure Data Share에 대한 보안 개요
description: Azure Data Share에 대한 보안 개요
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/17/2020
ms.openlocfilehash: 770ee01d054aec084e9abd609af28e8125844e0f
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112895871"
---
# <a name="security-overview-for-azure-data-share"></a>Azure Data Share에 대한 보안 개요

이 문서에서는 Azure Data Share 서비스에 대한 보안 개요를 제공합니다.

## <a name="security-overview"></a>보안 개요

Azure Data Share는 Azure에서 제공하는 기본 보안을 활용하여 저장 데이터와 전송 중 데이터를 보호합니다. 기본 데이터 저장소에서 지원하는 경우 미사용 데이터는 암호화됩니다. 전송 중 데이터도 TLS 1.2를 사용하여 암호화됩니다. 데이터 공유에 대한 메타데이터도 저장 상태 및 전송 중 상태에서 암호화됩니다. Azure Data Share는 공유 중인 고객 데이터의 콘텐츠를 저장하지 않습니다.

Azure Data Share는 관리 ID(이전에는 MSI)를 활용하여 데이터 공유에 사용되는 데이터 저장소에 액세스합니다. 자격 증명은 데이터 공급자와 데이터 소비자 간에 교환되지 않습니다. 관리 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리 ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)를 참조하세요. 데이터를 공유하는 데 필요한 역할 및 권한에 대한 자세한 내용은 [역할 및 요구 사항](concepts-roles-permissions.md)을 참조하세요.

## <a name="access-control"></a>Access Control

권한이 부여된 사용자가 액세스할 수 있도록 Azure Data Share에 대한 액세스 제어는 Data Share 리소스 수준에서 설정할 수 있습니다. Data Share 리소스의 소유자와 참가자는 데이터를 공유하고, 공유를 받고, 기존의 공유를 변경할 수 있습니다. Data Share 리소스의 읽기 권한자는 공유를 볼 수 있지만 변경할 수는 없습니다. 

공유를 만들거나 받으면 Data Share 리소스에 대한 적절한 권한이 있는 사용자가 공유를 변경할 수 있습니다. 공유를 만들거나 받는 사용자가 조직을 떠나도 공유가 종료되거나 데이터의 흐름이 중지되지 않습니다. Data Share 리소스에 대한 적절한 권한이 있는 다른 사용자는 계속해서 공유를 관리할 수 있습니다.

## <a name="share-data-from-or-to-data-stores-with-firewall-enabled"></a>방화벽이 사용된 데이터 저장소에서 데이터 공유
방화벽이 사용되는 저장소 계정에서 데이터를 공유하려면 저장소 계정에서 **신뢰할 수 있는 Microsoft 서비스 허용** 을 사용해야 합니다. 자세한 내용은 [Azure Storage 방화벽 및 가상 네트워크 구성](../storage/common/storage-network-security.md#trusted-microsoft-services)을 참조하세요.


## <a name="next-steps"></a>다음 단계

데이터 공유를 시작하는 방법을 알아보려면 [데이터 공유](share-your-data.md) 자습서로 계속 진행하세요.
