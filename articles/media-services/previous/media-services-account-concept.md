---
title: Azure Media Services v2 계정 관리 | Microsoft Docs
description: Azure에서 미디어 콘텐츠 관리, 암호화, 인코딩, 분석 및 스트리밍을 시작하려면 Media Services 계정을 만들어야 합니다. 이 문서에서는 Azure Media Services v2 계정을 관리하는 방법을 설명합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: a097b186a2287ec13866c8a5ee9420641b44d131
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103015951"
---
# <a name="manage-azure-media-services-v2-accounts"></a>Azure Media Services v2 계정 관리

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Azure에서 미디어 콘텐츠 관리, 암호화, 인코딩, 분석 및 스트리밍을 시작하려면 Media Services 계정을 만들어야 합니다. Media Services 계정을 만들려면 Azure Storage 계정 리소스의 이름을 제공해야 합니다. 지정된 스토리지 계정은 Media Services 계정에 연결됩니다. Media Services 계정 및 연결된 모든 스토리지 계정은 동일한 Azure 구독에 포함되어야 합니다.  

## <a name="moving-a-media-services-account-between-subscriptions"></a>구독 간에 Media Services 계정 이동

Media Services 계정을 새 구독으로 이동해야 하는 경우 먼저 Media Services 계정을 포함한 전체 리소스 그룹을 새 구독으로 이동해야 합니다. Azure Storage 계정, Azure CDN 프로필 등 연결된 모든 리소스를 이동해야 합니다. 자세한 내용은 [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md)을 참조하세요. Azure의 모든 리소스와 마찬가지로 리소스 그룹 이동은 완료하는 데 다소 시간이 걸릴 수 있습니다.

Media Services v2는 다중 테넌트 모델을 지원하지 않습니다. Media Services 계정을 새 테넌트의 구독으로 이동해야 하는 경우 새 테넌트에 새 Azure AD(Azure Active Directory) 응용 프로그램을 만듭니다. 그런 다음 계정을 새 테넌트의 구독으로 이동합니다. 테넌트 이동이 완료된 후 새 테넌트에서 Azure AD 응용 프로그램을 사용하여 v2 API로 Media Services 계정에 액세스할 수 있습니다.

> [!IMPORTANT]
> Media Services v2 API에 액세스하려면 [Azure AD 인증](media-services-portal-get-started-with-aad.md) 정보를 다시 설정해야 합니다.
  
### <a name="considerations"></a>고려 사항

* 다른 구독으로 마이그레이션하기 전에 계정에 있는 모든 데이터의 백업을 만듭니다.
* 모든 스트리밍 엔드포인트와 라이브 스트리밍 리소스를 중지해야 합니다. 사용자는 리소스 그룹 이동 기간 동안 콘텐츠에 액세스할 수 없습니다.

> [!IMPORTANT]
> 이동이 성공적으로 완료될 때까지 스트리밍 엔드포인트를 시작하지 마세요.

### <a name="troubleshoot"></a>문제 해결

리소스 그룹 이동 후에 Media Services 계정 또는 연결된 Azure Storage 계정이 "연결 끊김" 상태가 되면 스토리지 계정 키를 전환해 보세요. 스토리지 계정 키를 전환해도 Media Services 계정의 "연결 끊김" 상태가 해결되지 않으면 Media Services 계정의 "지원 + 문제 해결" 메뉴에서 새로운 지원 요청을 파일에 추가합니다.  

## <a name="next-steps"></a>다음 단계

[계정 만들기](media-services-portal-create-account.md)
