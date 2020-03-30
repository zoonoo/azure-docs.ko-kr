---
title: 포함 파일
description: 포함 파일
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2020
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 2eb82eb8b4a64cbbfef91539f46ba084aa73f207
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77205698"
---
다음 방법을 사용하여 기본 개발 환경에서 지원을 추가합니다.

| 개발 환경  | 애플리케이션 종류      | 지원을 추가하려면 |
|--------------------------|-----------------------|----------------|
| Visual Studio            | C# 클래스 라이브러리      | [NuGet 패키지 설치](../articles/azure-functions/functions-bindings-register.md#vs) |
| Visual Studio Code       | 핵심 [도구](../articles/azure-functions/functions-run-local.md) 기반 | [확장 번들 등록](../articles/azure-functions/functions-bindings-register.md#extension-bundles)<br><br>[Azure 도구 확장을](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) 설치하는 것이 좋습니다. |
| 기타 편집기/IDE     | 핵심 [도구](../articles/azure-functions/functions-run-local.md) 기반 | [확장 번들 등록](../articles/azure-functions/functions-bindings-register.md#extension-bundles) |
| Azure Portal             | 포털에서만 온라인 | 바인딩을 추가할 때 설치<br /><br /> 함수 앱을 다시 게시할 필요 없이 기존 바인딩 확장을 업데이트하려면 [확장 업데이트가](../articles/azure-functions/install-update-binding-extensions-manual.md) 참조됩니다. |
