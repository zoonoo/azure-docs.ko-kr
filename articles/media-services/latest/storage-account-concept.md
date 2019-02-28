---
title: Azure Media Services를 사용한 클라우드 업로드 및 스토리지 | Microsoft Docs
description: 이 문서에서는 클라우드 업로드 및 스토리지 개념을 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/12/2019
ms.author: juliako
ms.openlocfilehash: d49d056ab84b60389df8bcaf1c75d6224633863d
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/18/2019
ms.locfileid: "56337871"
---
# <a name="cloud-upload-and-storage"></a>클라우드 업로드 및 저장

Azure에서 미디어 콘텐츠 관리, 암호화, 인코딩, 분석 및 스트리밍을 시작하려면 Media Services 계정을 만들어야 합니다. Media Services 계정을 만들려면 Azure Storage 계정 리소스의 이름을 제공해야 합니다. 지정된 저장소 계정은 Media Services 계정에 연결됩니다. 

Media Services 계정 및 연결된 모든 스토리지 계정은 동일한 Azure 구독에 포함되어야 합니다. 추가 대기 시간 및 데이터 송신 비용이 발생하지 않도록 Media Services 계정과 동일한 위치에 있는 스토리지 계정을 사용하는 것이 좋습니다.

**기본** 저장소 계정은 하나 있어야 하며 Media Services 계정과 연결된 **보조** 저장소 계정은 여러 개 사용할 수 있습니다. Media Services는 **범용 v2**(GPv2) 또는 **범용 v1**(GPv1) 계정을 지원합니다. 

>[!NOTE]
> Blob 전용 계정은 **기본**으로 허용되지 않습니다. 

핫 저장소 계층과 쿨 저장소 계층 중 하나를 선택할 수 있도록 GPv2를 사용하는 것이 좋습니다. 스토리지 계정을 자세히 알아보려면 [Azure Storage 계정 개요](../../storage/common/storage-account-overview.md)를 참조하세요. 

## <a name="assets-in-a-storage-account"></a>저장소 계정의 자산

Media Services v3에서는 Storage API를 사용하여 파일을 업로드합니다.

> [!Note]
> Media Service API를 사용하지 않고 Media Services SDK에서 생성된 Blob 컨테이너의 콘텐츠를 변경하려고 하면 안 됩니다.

Media Services에서 Storage API를 사용하여 입력 파일을 업로드하는 방법을 보려면 [로컬 파일에서 작업 입력 만들기](job-input-from-local-file-how-to.md)를 참조하세요. 
 
## <a name="next-steps"></a>다음 단계

Media Services 계정에 저장소 계정을 연결하는 방법을 알아보려면 [계정 만들기](create-account-cli-quickstart.md)를 참조하세요.
