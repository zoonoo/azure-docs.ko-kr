---
title: 포함 파일
description: 포함 파일
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 01/11/2019
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: ac6b008597b6d6e557a0cc412c00c2202231bc3d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60921881"
---
> [!NOTE]
> 다중 파트 요청에는 세 가지 정보가 필요합니다.
> * **Content-Type** 헤더:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * **Content-Disposition**:
>   * `form-data; name="metadata"`
> * 업로드할 파일 콘텐츠
>
> **Content-Type** 및 **Content-Disposition**은 사용 시나리오에 따라 달라질 수 있습니다.

다중 파트 요청을 C#을 통해, REST 클라이언트 또는 [Postman](https://docs.microsoft.com/azure/digital-twins/how-to-configure-postman#multi)과 같은 도구를 통해 프로그래밍 방식으로 만들 수 있습니다. REST 클라이언트 도구에는 다양한 수준의 복잡한 다중 파트 요청에 대한 지원이 있을 수 있습니다. 구성 설정은 도구마다 약간씩 다를 수 있습니다. 사용자의 요구에 가장 적합한 도구를 확인합니다.

> [!IMPORTANT]
> Azure Digital Twins 관리 API에 대한 다중 파트 요청은 일반적으로 다음과 같은 두 파트로 구성됩니다.
> * **Content-Type** 및/또는 **Content-Disposition**에 명시된 Blob 메타데이터(예: 연결된 MIME 형식)
> * 업로드할 파일의 비정형 콘텐츠를 포함하는 blob 콘텐츠
>
> **PATCH** 요청에는 두 파트 중 어떤 것도 필요하지 않습니다. 두 파트는 모두 **POST** 또는 만들기 작업에 필요합니다.

[점유율 빠른 시작 소스 코드](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs)에는 Azure Digital Twins 관리 API에 대한 다중 파트 요청을 수행하는 방법을 설명하는 전체 C# 예제가 포함됩니다.