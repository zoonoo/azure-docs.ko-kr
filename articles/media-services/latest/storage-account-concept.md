---
title: Azure Storage 계정
titleSuffix: Azure Media Services
description: Azure 미디어 서비스와 함께 사용할 Azure 저장소 계정을 만드는 방법에 대해 알아봅니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/01/2019
ms.author: juliako
ms.openlocfilehash: 72aa0762d001c28b21d5e27ed8f6f9d099f62bfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499829"
---
# <a name="azure-storage-accounts"></a>Azure Storage 계정

Azure에서 미디어 콘텐츠 관리, 암호화, 인코딩, 분석 및 스트리밍을 시작하려면 Media Services 계정을 만들어야 합니다. Media Services 계정을 만들려면 Azure Storage 계정 리소스의 이름을 제공해야 합니다. 지정된 스토리지 계정은 Media Services 계정에 연결됩니다.

Media Services 계정 및 연결된 모든 스토리지 계정은 동일한 Azure 구독에 포함되어야 합니다. 추가 대기 시간 및 데이터 송신 비용을 피하기 위해 Media Services 계정과 동일한 위치에 있는 저장소 계정을 사용하는 것이 좋습니다.

**기본** 저장소 계정이 하나 이어야 하며 미디어 서비스 계정과 연결된 **여러 개의 보조** 저장소 계정을 가질 수 있습니다. Media Services는 **범용 v2**(GPv2) 또는 **범용 v1**(GPv1) 계정을 지원합니다. Blob 계정만 **기본**계정으로 허용되지 않습니다.

최신 기능과 성능을 활용할 수 있도록 GPv2를 사용하는 것이 좋습니다. 스토리지 계정을 자세히 알아보려면 [Azure Storage 계정 개요](../../storage/common/storage-account-overview.md)를 참조하세요.

> [!NOTE]
> 핫 액세스 계층만 Azure Media Services에서 사용할 수 있지만 다른 액세스 계층은 적극적으로 사용되지 않는 콘텐츠의 저장소 비용을 줄이는 데 사용할 수 있습니다.

저장소 계정에 대해 선택할 수 있는 다른 SCO가 있습니다. 자세한 내용은 [스토리지 계정](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest)을 참조하세요. 스토리지 계정을 실험하려면 `--sku Standard_LRS`를 사용합니다. 그러나 프로덕션용 SKU를 선택할 때는 `--sku Standard_RAGRS`비즈니스 연속성을 위한 지리적 복제를 제공하는 것을 고려해야 합니다.

## <a name="assets-in-a-storage-account"></a>스토리지 계정의 자산

미디어 서비스 v3에서 저장소 API는 파일을 자산에 업로드하는 데 사용됩니다. 자세한 내용은 [Azure 미디어 서비스 v3의 자산을](assets-concept.md)참조하십시오.

> [!Note]
> 미디어 서비스 API를 사용하지 않고 미디어 서비스 SDK에서 생성된 Blob 컨테이너의 내용을 변경하지 마십시오.

## <a name="storage-side-encryption"></a>스토리지 쪽 암호화

미사용 자산을 보호하려면 자산을 저장소 측 암호화로 암호화해야 합니다. 다음 표는 Media Services v3에서 스토리지 쪽 암호화가 작동하는 원리를 보여줍니다.

|암호화 옵션|설명|Media Services v3|
|---|---|---|
|미디어 서비스 스토리지 암호화| AES-256 암호화, 미디어 서비스에서 관리하는 키. |지원되지 않습니다. <sup>(1)</sup>|
|[미사용 데이터에 대한 스토리지 서비스 암호화](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Azure 저장소에서 제공하는 서버 측 암호화, Azure 또는 고객이 관리하는 키입니다.|지원됩니다.|
|[스토리지 클라이언트 측 암호화](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Azure 저장소에서 제공하는 클라이언트 측 암호화로 키 볼트에서 고객이 관리하는 키입니다.|지원되지 않습니다.|

<sup>1</sup> Media Services v3에서 저장소 암호화(AES-256 암호화)는 자산이 Media Services v2로 생성된 경우에만 이전 버전과의 호환성을 위해 지원되며, 이는 v3가 기존 저장소 암호화 된 자산과 함께 작동하지만 새 자산의 생성을 허용하지 않음을 의미합니다.

## <a name="storage-account-errors"></a>저장소 계정 오류

Media Services 계정의 "연결 끊김" 상태는 스토리지 액세스 키가 변경되어 해당 계정이 하나 이상의 연결된 스토리지 계정에 액세스할 수 없는 경우를 나타냅니다. Media Services가 계정에서 많은 작업을 수행하려면 최신 스토리지 액세스 키가 필요합니다.

다음은 연결된 스토리지 계정에 대한 액세스 권한이 Media Services 계정에서 없어지는 주요 시나리오입니다.

|문제|해결 방법|
|---|---|
|Media Services 계정이나 연결된 스토리지 계정이 별도의 구독으로 마이그레이션되었습니다. |저장소 계정 또는 미디어 서비스 계정을 모두 동일한 구독에 있도록 마이그레이션합니다. |
|이것이 지원되던 초기 Media Services 계정이기 때문에, Media Services 계정이 다른 구독에 속하는 연결된 스토리지 계정을 사용합니다. 모든 초기 미디어 서비스 계정은 최신 Azure Resources Manager 기반 계정으로 변환되었으며 연결이 끊긴 상태입니다. |저장소 계정 또는 미디어 서비스 계정을 모두 동일한 구독에 있도록 마이그레이션합니다.|

## <a name="azure-storage-firewall"></a>Azure 저장소 방화벽

Azure Media 서비스는 Azure 저장소 방화벽 또는 [개인 끝점을](https://docs.microsoft.com/azure/storage/common/storage-network-security) 사용하도록 설정한 저장소 계정을 지원하지 않습니다.

## <a name="next-steps"></a>다음 단계

Media Services 계정에 스토리지 계정을 연결하는 방법을 알아보려면 [계정 만들기](create-account-cli-quickstart.md)를 참조하세요.
