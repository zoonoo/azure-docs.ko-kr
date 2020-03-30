---
title: Azure 미디어 서비스 v2 계정 관리 | 마이크로 소프트 문서
description: Azure에서 미디어 콘텐츠 관리, 암호화, 인코딩, 분석 및 스트리밍을 시작하려면 Media Services 계정을 만들어야 합니다. 이 문서에서는 Azure 미디어 서비스 v2 계정을 관리하는 방법을 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: 09a5f004570430fafe5c86f4f8ae048f2d1fe4c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981937"
---
# <a name="manage-azure-media-services-v2-accounts"></a>Azure 미디어 서비스 v2 계정 관리

Azure에서 미디어 콘텐츠 관리, 암호화, 인코딩, 분석 및 스트리밍을 시작하려면 Media Services 계정을 만들어야 합니다. Media Services 계정을 만들려면 Azure Storage 계정 리소스의 이름을 제공해야 합니다. 지정된 스토리지 계정은 Media Services 계정에 연결됩니다. Media Services 계정 및 연결된 모든 스토리지 계정은 동일한 Azure 구독에 포함되어야 합니다.  

## <a name="moving-a-media-services-account-between-subscriptions"></a>구독 간에 미디어 서비스 계정 이동 

Media Services 계정을 새 구독으로 이동해야 하는 경우 먼저 미디어 서비스 계정이 포함된 전체 리소스 그룹을 새 구독으로 이동해야 합니다. 연결된 모든 리소스(Azure 저장소 계정, Azure CDN 프로필 등)를 이동해야 합니다. 자세한 내용은 [리소스이동을 참조하여 새 리소스 그룹 또는 구독으로](../../azure-resource-manager/management/move-resource-group-and-subscription.md)이동합니다. Azure의 모든 리소스와 마찬가지로 리소스 그룹 이동을 완료하는 데 다소 시간이 걸릴 수 있습니다.

미디어 서비스 v2는 다중 테넌시 모델을 지원하지 않습니다. Media Services 계정을 새 테넌트의 구독으로 이동해야 하는 경우 새 테넌트에서 새 Azure Active Directory(Azure AD) 응용 프로그램을 만듭니다. 그런 다음 새 테넌트의 구독으로 계정을 이동합니다. 테넌트 이동이 완료되면 새 테넌트의 Azure AD 응용 프로그램을 사용하여 v2 API를 사용하여 Media 서비스 계정에 액세스할 수 있습니다. 

> [!IMPORTANT]
> 미디어 서비스 v2 API에 액세스하려면 [Azure AD 인증](media-services-portal-get-started-with-aad.md) 정보를 재설정해야 합니다.  
### <a name="considerations"></a>고려 사항

* 다른 구독으로 마이그레이션하기 전에 계정의 모든 데이터의 백업을 만듭니다.
* 모든 스트리밍 엔드포인트와 라이브 스트리밍 리소스를 중지해야 합니다. 사용자는 리소스 그룹 이동 기간 동안 콘텐츠에 액세스할 수 없습니다. 

> [!IMPORTANT]
> 이동이 성공적으로 완료될 때까지 스트리밍 끝점을 시작하지 마십시오.

### <a name="troubleshoot"></a>문제 해결 

리소스 그룹 이동 후 Media Services 계정 또는 연결된 Azure Storage 계정이 "연결 해제"된 경우 저장소 계정 키를 회전해 보십시오. 저장소 계정 키를 회전해도 Media Services 계정의 "연결이 끊긴" 상태가 해결되지 않으면 Media Services 계정의 "지원 + 문제 해결" 메뉴에서 새 지원 요청을 제출합니다.  
 
## <a name="next-steps"></a>다음 단계

[거래처 만들기](media-services-portal-create-account.md)
