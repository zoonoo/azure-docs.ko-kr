---
title: 포함 파일
description: 포함 파일
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: b640a3cb9382ad72bb48e06c6a7074c96409e2e4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66162673"
---
<!-- description of message routing used in the Azure CLI, PowerShell, and RM routing articles.-->

시뮬레이션된 디바이스에 의해 메시지에 연결된 속성에 따라 다양한 리소스에 메시지를 라우팅합니다. 사용자 지정 라우팅되지 않은 메시지는 기본 엔드포인트로 전송됩니다(메시지/이벤트). 다음 자습서에서는 IoT Hub에 메시지를 보내서 다른 대상으로 라우팅되는 것을 살펴봅니다.

|값 |결과|
|------|------|
|level="storage" |Azure Storage에 씁니다.|
|level="critical" |Service Bus 큐에 씁니다. Logic App은 큐의 메시지를 검색하고 Office 365를 사용하여 메시지를 이메일로 전송합니다.|
|기본값 |Power BI를 사용하여 이 데이터를 표시합니다.|

첫 단계에서는 데이터가 라우팅될 엔드포인트를 설정합니다. 두 번째 단계에서는 이 엔드포인트를 사용하는 메시지 경로를 설정합니다. 라우팅을 설정한 후에는 포털에서 엔드포인트와 메시지 경로를 볼 수 있습니다.