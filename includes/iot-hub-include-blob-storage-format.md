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
ms.openlocfilehash: c779147e464a592d45da8a9a2d8e812320dc23e8
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66162665"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> 데이터는 [Apache Avro](https://avro.apache.org/)(기본 형식) 또는 JSON(미리 보기)으로 Blob Storage에 작성할 수 있습니다. 
>    
> JSON 형식을 인코딩하는 기능은 미국 동부, 미국 서부 및 서유럽을 제외하고, IoT Hub가 지원되는 모든 지역에서 미리 보기로 제공되고 있습니다. 인코딩 형식은 Blob Storage 엔드포인트를 구성할 때만 설정할 수 있습니다. 이미 설정된 엔드포인트에 대해서는 형식을 변경할 수 없습니다. JSON 인코딩을 사용하는 경우에는 메시지 시스템 속성에서 contentType을 JSON으로 설정하고 contentEncoding을 UTF-8로 설정해야 합니다. 
>
> Blob Storage 엔드포인트 사용에 대한 자세한 내용은 [Blob Storage로 라우팅에 대한 지침](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-blob-storage)을 참조하세요.
>