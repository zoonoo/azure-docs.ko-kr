---
title: Azure Media Services의 자산 | Microsoft Docs
description: 이 문서에서는 자산이 무엇이고 Azure Media Services가 어떤 자산을 사용하는지 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 928d88d51503350abe7df847ce58ff066b987c8e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="assets"></a>자산

**자산** 에는 디지털 파일(비디오, 오디오, 이미지, 미리 보기 컬렉션, 텍스트 트랙 및 선택 캡션 파일 포함)과 이러한 파일에 대한 메타데이터가 포함됩니다. 디지털 파일은 자산에 업로드한 후 Media Services 인코딩 및 스트리밍 워크플로에서 사용할 수 있습니다.

자산은 [Azure Storage 계정](storage-account-concept.md)의 BLOB 컨테이너에 매핑되고 자산의 파일은 해당 컨테이너에 블록 BLOB으로 저장됩니다. Storage SDK 클라이언트를 사용하여 컨테이너의 자산 파일과 상호 작용할 수 있습니다.

Azure Media Services는 계정이 범용 v2(GPv2) 저장소를 사용할 때 Blob 계층을 지원합니다. GPv2를 사용하는 경우 파일을 서늘하거나 추운 저장소로 이동할 수 있습니다. 콜드 저장소는 더 이상 필요 없는 mezzanine 파일을 보관하는 데 적합니다.(예: 인코딩된 후)

Media Services v3에서 작업 입력은 자산 또는 HTTP URL에서 만들 수 있습니다. 작업에 대한 입력으로 사용할 수 있는 자산을 만들려면 [로컬 파일에서 작업 입력 만들기](job-input-from-local-file-how-to.md)를 참조하세요.

또한 [Media Services에서 저장소 계정](storage-account-concept.md)과 [변환 및 작업](transform-concept.md)에 대해 읽어보세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [파일 스트리밍](stream-files-dotnet-quickstart.md)
