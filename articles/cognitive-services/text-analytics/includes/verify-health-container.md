---
title: 상태 컨테이너 인스턴스를 확인 합니다.
titleSuffix: Azure Cognitive Services
description: 상태 컨테이너 인스턴스를 확인 하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 83a9eeb7644d107a808494ad06a8bef91d471fe1
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779788"
---
### <a name="verify-that-a-container-is-running"></a>컨테이너가 실행 중인지 확인 합니다.

1. **개요** 탭을 선택 하 고 IP 주소를 복사 합니다.
1. 새 브라우저 탭을 열고 IP 주소를 입력 합니다. 예를 들어를 입력 `http://<IP-address>:5000 (http://55.55.55.55:5000` 합니다. 컨테이너의 홈 페이지가 표시 됩니다. 그러면 컨테이너가 실행 중임을 알 수 있습니다.

    ![컨테이너 홈 페이지를 확인 하 여 실행 중인지 확인 합니다.](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. **서비스 API 설명** 링크를 선택 하 여 컨테이너의 Swagger 페이지로 이동 합니다.

1. **POST** api 중 하나를 선택 하 고 **사용해 보기**를 선택 합니다. 예제 입력을 포함 하는 매개 변수가 표시 됩니다.

컨테이너가 실행 중인지 확인 하는 데 사용할 수 있는 몇 가지 Url이 있습니다.

|요청|용도|
|--|--|
|`http://localhost:5000/`|컨테이너는 홈페이지를 제공합니다.|
|`http://localhost:5000/ready`|GET을 사용 하 여 요청 하면 컨테이너에서 모델에 대 한 쿼리를 수락할 준비가 되었음을 확인할 수 있습니다. 이 요청은 Kubernetes [활동성 및 준비 상태 프로브](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)에 사용될 수 있습니다.|
|`http://localhost:5000/status`|/준비 된 끝점과 같이 GET으로 요청 된 경우 모델에 대 한 쿼리를 발생 시 키 지 않고 컨테이너가 실행 중인지 확인 합니다. 이 요청은 Kubernetes [활동성 및 준비 상태 프로브](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)에 사용될 수 있습니다.|
|`http://localhost:5000/swagger`|이 URL을 통해 컨테이너는 끝점 및 기능에 대 한 전체 설명서 집합을 제공 합니다 `Try it now` . 이 기능을 사용하면 웹 기반 HTML 양식으로 설정을 입력할 수 있고 코드 작성 없이 쿼리를 만들 수 있습니다. 쿼리가 반환되면 필요한 HTTP 헤더 및 본문 형식을 보여주기 위해 예제 CURL 명령이 제공됩니다. |
|`http://localhost:5000/demo`| 브라우저를 통해 요청 된이 기능은 입력 텍스트 샘플 또는 사용자가 제공한 쿼리의 결과에 대 한 대화형 시각화를 제공 합니다.  |

이 요청 URL을 사용 `http://localhost:5000/text/analytics/v3.2-preview.1/entities/health` 하 여 쿼리를 컨테이너에 제출 합니다.
