---
title: Azure Media Services v3 계정 관리 | Microsoft Docs
description: Azure에서 미디어 콘텐츠 관리, 암호화, 인코딩, 분석 및 스트리밍을 시작하려면 Media Services 계정을 만들어야 합니다. 이 문서에서는 Azure Media Services v3 계정을 관리 하는 방법에 설명 합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: fa9720c2c29af184016d2903e60520e701b4cf79
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670679"
---
# <a name="manage-azure-media-services-v3-accounts"></a>Azure Media Services v3 계정 관리

Azure에서 미디어 콘텐츠 관리, 암호화, 인코딩, 분석 및 스트리밍을 시작하려면 Media Services 계정을 만들어야 합니다. Media Services 계정을 만들려면 Azure Storage 계정 리소스의 이름을 제공해야 합니다. 지정된 저장소 계정은 Media Services 계정에 연결됩니다. Media Services 계정 및 연결된 모든 스토리지 계정은 동일한 Azure 구독에 포함되어야 합니다. 자세한 내용은 [저장소 계정](storage-account-concept.md)합니다.

## <a name="moving-a-media-services-account-between-subscriptions"></a>Media Services 계정 구독 간의 이동 

Media Services 계정을 새 구독으로 이동 해야 할 경우 새 구독으로 Media Services 계정을 포함 하는 전체 리소스 그룹을 먼저 이동 해야 합니다. 모든 연결 된 리소스를 이동 해야 합니다. Azure 저장소 계정, Azure CDN 프로필 등입니다. 자세한 내용을 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](../../azure-resource-manager/resource-group-move-resources.md)을 참조하세요. Azure에 모든 리소스, 리소스 그룹 이동 완료 하려면 다소 시간이 걸릴 수 있습니다.

> [!NOTE]
> Media Services v3는 다중 테 넌 트 모델을 지원합니다.

### <a name="considerations"></a>고려 사항

* 계정에서 다른 구독으로 마이그레이션하기 전에 모든 데이터의 백업을 만듭니다.
* 모든 스트리밍 끝점을 중지 하 고 라이브 스트리밍 리소스 해야 합니다. 사용자가 리소스 그룹 이동 기간 동안 콘텐츠에 액세스할 수 없습니다. 

> [!IMPORTANT]
> 이동이 성공적으로 완료 될 때까지 스트리밍 끝점을 시작 하지 않습니다.

### <a name="troubleshoot"></a>문제 해결 

그래도 Media Services 계정 또는 연결된 된 Azure Storage 계정이 "끊어질" 리소스 그룹 이동, 저장소 계정 키를 회전 합니다. 저장소 계정 키를 회전 하는 Media Services 계정의 상태를 "연결이 끊긴된 된" 해결 되지 않으면, 파일에서 새 지원 요청을 "지원 + 문제 해결" Media Services 계정에 대 한 메뉴입니다.  

## <a name="next-steps"></a>다음 단계

[계정 만들기](create-account-cli-quickstart.md)
