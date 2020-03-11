---
title: Azure Portal을 사용하여 Azure Media Services 계정 만들기
description: 이 자습서에서는 Azure Portal을 사용하여 Azure Media Services 계정을 만드는 단계를 안내합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/09/2020
ms.author: juliako
ms.openlocfilehash: c90278eccf889595378d3b6b07de2468910c660c
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2020
ms.locfileid: "79080879"
---
# <a name="use-the-azure-portal-to-create-a-media-services-account"></a>Azure Portal를 사용 하 여 Media Services 계정 만들기

Azure Portal은 Azure Media Services 계정을 신속 하 게 만드는 방법을 제공 합니다. 계정을 사용하여 Azure에서 미디어 콘텐츠를 저장, 암호화, 인코딩, 관리 및 스트리밍할 수 있는 Media Services에 액세스할 수 있습니다.

현재 [Azure Portal](https://portal.azure.com/) 를 사용 하 여 다음을 수행할 수 있습니다.

* Media Services v3 [라이브 이벤트](live-events-outputs-concept.md)관리 
* v3 [자산](assets-concept.md)보기 (관리 안 함) 
* [api에 액세스 하는 방법에 대 한 정보를 가져옵니다](access-api-portal.md). 

다른 모든 관리 작업 (예: [변환 및 작업](transforms-jobs-concept.md) 및 [콘텐츠 보호](content-protection-overview.md))의 경우 [REST API](https://aka.ms/ams-v3-rest-ref), [CLI](https://aka.ms/ams-v3-cli-ref)또는 지원 되는 [sdk](media-services-apis-overview.md#sdks)중 하나를 사용 합니다.

이 문서에서는 Azure Portal을 사용하여 Media Services 계정을 만드는 방법을 보여줍니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 Azure 계정이 필요합니다. 자세한 내용은 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요. 

## <a name="create-a-media-services-account"></a>Media Services 계정 만들기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **+ 리소스 만들기** > **미디어** > **Media Services**를 클릭 합니다.
1. **Media Services 계정 만들기** 섹션에서 필요한 값을 입력 합니다.
    
    | 속성 | Description |
    | ---|---|
    |**계정 이름**|새 Media Services 계정의 이름을 입력 합니다. Media Services 계정 이름은 공백 없이 모두 소문자로 이루어진 3-24자의 숫자 또는 문자입니다.|
    |**구독**|둘 이상의 구독이 있는 경우 액세스 권한이 있는 Azure 구독 목록에서 하나를 선택 합니다.|
    |**리소스 그룹**|새 리소스 또는 기존 리소스를 선택 합니다. 리소스 그룹은 수명 주기, 권한 및 정책을 공유하는 리소스의 컬렉션입니다. [여기](../../azure-resource-manager/management/overview.md#resource-groups)를 참조하세요.|
    |**위치**|Media Services 계정에 대 한 메타 데이터 레코드 및 미디어를 저장 하는 데 사용할 지역을 선택 합니다. 이 지역은 미디어를 처리하고 스트림하는 데 사용됩니다. 사용 가능한 Media Services 지역만 드롭다운 목록 상자에 표시됩니다. |
    |**Storage 계정**|저장소 계정을 선택 하 여 Media Services 계정의 미디어 콘텐츠 blob storage를 제공 합니다. Media Services 계정과 동일한 지역의 기존 스토리지 계정을 선택하거나 새 스토리지 계정을 만들 수 있습니다. 동일한 지역에 새 스토리지 계정이 생성됩니다. 스토리지 계정 이름에 대한 규칙은 Media Services 계정의 경우와 같습니다.<br/><br/>**기본** 스토리지 계정은 하나 있어야 하며 Media Services 계정과 연결된 **보조** 스토리지 계정은 여러 개 사용할 수 있습니다. Azure Portal를 사용 하 여 보조 저장소 계정을 추가할 수 있습니다. 자세한 내용은 계정 [으로 계정 Azure Storage Azure Media Services](storage-account-concept.md)를 참조 하세요.<br/><br/>Media Services 계정 및 연결된 모든 스토리지 계정은 동일한 Azure 구독에 포함되어야 합니다. 추가 대기 시간 및 데이터 송신 비용이 발생하지 않도록 Media Services 계정과 동일한 위치에 있는 스토리지 계정을 사용하는 것이 좋습니다.|
    
1. 계정 배포 진행 상태를 보려면 **대시보드에 고정** 을 선택합니다.
1. 양식 맨 아래에 있는 **만들기** 를 클릭합니다.
   
    계정이 성공적으로 만들어지면 개요 페이지가 로드됩니다. 스트리밍 끝점 테이블에서 계정은 **중지 됨** 상태의 기본 스트리밍 끝점을 가집니다. 

    Azure Media Services 계정이 만들어지면 **기본** 스트리밍 엔드포인트가 **중지됨** 상태에 있는 계정에 추가됩니다. 콘텐츠 스트리밍을 시작 하 고 [동적 패키징](dynamic-packaging-overview.md) 및 [동적 암호화](content-protection-overview.md)를 활용 하려면 콘텐츠를 스트리밍 하려는 스트리밍 끝점이 **실행** 상태에 있어야 합니다. 

## <a name="next-steps"></a>다음 단계

프로그래밍 방식으로 Media Services API에 액세스 하려는 경우 [AZURE AD 인증을 사용 하 여 AZURE MEDIA SERVICES api에 액세스](access-api-portal.md)를 참조 하세요.

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

