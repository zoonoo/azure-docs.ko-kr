---
title: Azure 정적 Web Apps 미리 보기를 사용 하 여 프런트 엔드 프레임 워크 구성
description: Azure 정적 Web Apps에 필요한 인기 있는 프런트 엔드 프레임 워크에 대 한 설정
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 07/18/2020
ms.author: cshoe
ms.openlocfilehash: 4b1bc58b6b4a87cd6e5e09e83020a38261b8746f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90905372"
---
# <a name="configure-front-end-frameworks-and-libraries-with-azure-static-web-apps-preview"></a>Azure 정적 Web Apps 미리 보기를 사용 하 여 프런트 엔드 프레임 워크 및 라이브러리 구성

Azure Static Web Apps을 사용 하려면 프런트 엔드 프레임 워크 또는 라이브러리의 [빌드 구성 파일](github-actions-workflow.md) 에 적절 한 구성 값이 있어야 합니다.

## <a name="configuration"></a>구성

다음 표에서는 일련의 프레임 워크 및 라이브러리<sup>1</sup>에 대 한 설정을 보여 줍니다.

테이블 열의 의도는 다음 항목에 설명 되어 있습니다.

- **앱 아티팩트 위치**: `app_artifact_location` [응용 프로그램 파일의 빌드 버전 폴더](github-actions-workflow.md#build-and-deploy)에 해당 하는의 값을 나열 합니다.

- **사용자 지정 빌드 명령**: 프레임 워크에서 또는와 다른 명령이 필요한 경우 `npm run build` `npm run azure:build` [사용자 지정 빌드 명령을](github-actions-workflow.md#custom-build-commands)정의할 수 있습니다.

| 프레임워크 | 앱 아티팩트 위치 | 사용자 지정 빌드 명령 |
|--|--|--|
| [Alpine.js](https://github.com/alpinejs/alpine/) | `/` | 해당 없음 <sup>2</sup> |
| [Angular](https://angular.io/) | `dist/<APP_NAME>` | `npm run build -- --prod` |
| [각도 유니버설](https://angular.io/guide/universal) | `dist/<APP_NAME>/browser` | `npm run prerender` |
| [Aurelia](https://aurelia.io/) | `dist` | 해당 없음 |
| [Backbone.js](https://backbonejs.org/) | `/` | 해당 없음 |
| [Blazor](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) | `wwwroot` | 해당 없음 |
| [Ember](https://emberjs.com/) | `dist` | 해당 없음 |
| [Flutter](https://flutter.dev/) | `build/web` | `flutter build web` |
| [Framework7](https://framework7.io/) | `www` | `npm run build-prod` |
| [Glimmer](https://glimmerjs.com/) | `dist` | 해당 없음 |
| [HTML](https://developer.mozilla.org/docs/Web/HTML) | `/` | 해당 없음 |
| [하이퍼 앱](https://hyperapp.dev/) | `/` | 해당 없음 |
| [JavaScript](https://developer.mozilla.org/docs/Web/javascript) | `/` | 해당 없음 |
| [jQuery](https://jquery.com/) | `/` | 해당 없음 |
| [KnockoutJS](https://knockoutjs.com/) | `dist` | 해당 없음 |
| [LitElement](https://lit-element.polymer-project.org/) | `dist` | 해당 없음 |
| [Marko](https://markojs.com/) | `public` | 해당 없음 |
| [유성](https://www.meteor.com/) | `bundle` | 해당 없음 |
| [Mithril](https://mithril.js.org/) | `dist` | 해당 없음 |
| [Polymer](https://www.polymer-project.org/) | `build/default` | 해당 없음 |
| [사전 act](https://preactjs.com/) | `build` | 해당 없음 |
| [React](https://reactjs.org/) | `build` | 해당 없음 |
| [스텐실과](https://stenciljs.com/) | `www` | 해당 없음 |
| [Svelte](https://svelte.dev/) | `public` | 해당 없음 |
| [Three.js](https://threejs.org/) | `/` | 해당 없음 |
| [TypeScript](https://www.typescriptlang.org/) | `dist` | 해당 없음 |
| [Vue.js](https://vuejs.org/) | `dist` | 해당 없음 |

<sup>1</sup> 위의 표는 Azure Static Web Apps와 작동 하는 프레임 워크 및 라이브러리의 완전 한 목록이 아닙니다.

<sup>2</sup> 해당 없음

## <a name="next-steps"></a>다음 단계

- [빌드 및 워크플로 구성](github-actions-workflow.md)
