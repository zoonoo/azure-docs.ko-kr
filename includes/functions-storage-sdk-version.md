---
title: 포함 파일
description: 포함 파일
services: functions
author: ggailey777
manager: cfowler
ms.service: azure-functions
ms.topic: include
ms.date: 05/17/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: c2fff707dcaafac69efcad3dbf33446a7b797396
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608288"
---
### <a name="azure-storage-sdk-version-in-functions-1x"></a>Functions 1.x의 Azure Storage SDK 버전

Functions 1.x에서 Storage 트리거 및 바인딩은 버전 7.2.1의 Azure Storage SDK([WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) NuGet 패키지)를 사용합니다. 다른 버전의 Storage SDK를 참조하며, 함수 서명에서 Storage SDK 형식에 바인딩하는 경우 Functions 런타임은 해당 형식에 바인딩할 수 없음을 보고할 수 있습니다. 해결 방법은 프로젝트가 [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1)를 참조하도록 하는 것입니다.
