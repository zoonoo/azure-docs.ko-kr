---
title: 포함 파일
description: 포함 파일
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/15/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: b566fc5c8d490d9fbad8ba64217d9ab3110cd709
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108313495"
---
> [!NOTE]
> 데이터는 [Apache Avro](https://avro.apache.org/)(기본 형식) 또는 JSON으로 Blob Storage에 작성할 수 있습니다. 
>    
> 인코딩 형식은 Blob Storage 엔드포인트를 구성할 때만 설정할 수 있습니다. 이미 설정된 엔드포인트에 대해서는 형식을 변경할 수 없습니다. JSON 인코딩을 사용하는 경우에는 메시지 시스템 속성에서 contentType을 JSON으로 설정하고 contentEncoding을 UTF-8로 설정해야 합니다. 
>
> Blob Storage 엔드포인트 사용에 대한 자세한 내용은 [Storage로 라우팅에 대한 지침](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint)을 참조하세요.
>