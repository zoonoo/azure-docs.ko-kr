---
title: Azure Data Share에 대한 보안 개요
description: Azure Data Share에 대한 보안 개요
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/17/2020
ms.openlocfilehash: 4e62645dd5a7a8336df4fccf12daebc730a91168
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97678425"
---
# <a name="security-overview-for-azure-data-share"></a>Azure Data Share에 대한 보안 개요

이 문서에서는 Azure 데이터 공유 서비스에 대 한 보안 개요를 제공 합니다.

## <a name="security-overview"></a>보안 개요

Azure Data Share는 Azure에서 제공하는 기본 보안을 활용하여 저장 데이터와 전송 중 데이터를 보호합니다. 기본 데이터 저장소에서 지원하는 경우 미사용 데이터는 암호화됩니다. 또한 TLS 1.2를 사용 하 여 전송 중에 데이터를 암호화 합니다. 데이터 공유에 대한 메타데이터도 저장 상태 및 전송 중 상태에서 암호화됩니다. Azure 데이터 공유는 공유 중인 고객 데이터의 콘텐츠를 저장 하지 않습니다.

Azure 데이터 공유는 관리 id (이전에는 MSI)를 활용 하 여 데이터 공유에 사용 되는 데이터 저장소에 액세스 합니다. 자격 증명은 데이터 공급자와 데이터 소비자 간에 교환되지 않습니다. 관리 id에 대 한 자세한 내용은 [Azure 리소스에 대 한 관리 되는 id](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)를 참조 하세요. 데이터를 공유 하는 데 필요한 역할 및 권한에 대 한 자세한 내용은 [역할 및 요구 사항](concepts-roles-permissions.md)을 참조 하세요.

## <a name="access-control"></a>Access Control

Azure 데이터 공유에 대 한 액세스 제어는 권한이 부여 된 리소스에 액세스 하도록 데이터 공유 리소스 수준에서 설정할 수 있습니다. 데이터 공유 리소스의 소유자와 참가자는 데이터를 공유 하 고, 공유를 수신 하 고, 기존 공유를 변경할 수 있습니다. 데이터 공유 리소스의 판독기는 공유를 볼 수 있지만 변경할 수는 없습니다. 

공유가 만들어지거나 수신 되 면 데이터 공유 리소스에 대 한 적절 한 사용 권한이 있는 사용자가 변경할 수 있습니다. 공유를 만들거나 받는 사용자가 조직을 떠나는 경우 공유를 종료 하거나 데이터 흐름을 중지 하지 않습니다. 데이터 공유 리소스에 대 한 적절 한 사용 권한이 있는 다른 사용자는 계속 해 서 공유를 관리할 수 있습니다.

## <a name="share-data-from-or-to-data-stores-with-firewall-enabled"></a>방화벽이 설정 된 데이터 저장소에서 데이터 공유
방화벽이 설정 된 저장소 계정에서 데이터를 공유 하려면 저장소 계정에서 **신뢰할 수 있는 Microsoft 서비스 허용** 을 사용 하도록 설정 해야 합니다. 자세한 내용은 [Azure Storage 방화벽 및 가상 네트워크 구성](
https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services) 을 참조 하세요.


## <a name="next-steps"></a>다음 단계

데이터 공유를 시작하는 방법을 알아보려면 [데이터 공유](share-your-data.md) 자습서로 계속 진행하세요.
