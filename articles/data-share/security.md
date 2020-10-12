---
title: Azure Data Share에 대한 보안 개요
description: Azure Data Share에 대한 보안 개요
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 06/05/2020
ms.openlocfilehash: 10f31b74b461941b15f13e45f90b5fbc408c90fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86108416"
---
# <a name="security-overview-for-azure-data-share"></a>Azure Data Share에 대한 보안 개요

이 문서에서는 Azure 데이터 공유 서비스에 대 한 보안 개요를 제공 합니다.

## <a name="security-overview"></a>보안 개요

Azure Data Share는 Azure에서 제공하는 기본 보안을 활용하여 저장 데이터와 전송 중 데이터를 보호합니다. 기본 데이터 저장소에서 지원하는 경우 미사용 데이터는 암호화됩니다. 전송 중 데이터도 암호화됩니다. 데이터 공유에 대한 메타데이터도 저장 상태 및 전송 중 상태에서 암호화됩니다. 

권한이 부여된 사용자가 액세스할 수 있도록 액세스 제어는 Azure Data Share 리소스 수준에서 설정할 수 있습니다. 

Azure 데이터 공유는 관리 id (이전에는 MSI)를 활용 하 여 데이터 공유에 사용 되는 데이터 저장소에 액세스 합니다. 자격 증명은 데이터 공급자와 데이터 소비자 간에 교환되지 않습니다. 관리 id에 대 한 자세한 내용은 [Azure 리소스에 대 한 관리 되는 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)를 참조 하세요. 데이터를 공유 하는 데 필요한 역할 및 권한에 대 한 자세한 내용은 [역할 및 요구 사항](concepts-roles-permissions.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

데이터 공유를 시작하는 방법을 알아보려면 [데이터 공유](share-your-data.md) 자습서로 계속 진행하세요.




