---
title: Health 컨테이너용 docker pull
titleSuffix: Azure Cognitive Services
description: Text Analytics for Health 컨테이너용 docker pull 명령
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/25/2021
ms.author: aahi
ms.openlocfilehash: 8595af7f46b63f9991d6a02279ccad76afb38311
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106089726"
---
Text Analytics for Health 공개 미리 보기에 대한 액세스를 요청하는 [Cognitive Services 요청 양식](https://aka.ms/csgate)을 작성하고 제출합니다.  이 애플리케이션은 컨테이너와 호스트된 Web API 공개 미리 보기 모두에 적용됩니다.
이 양식에서는 컨테이너를 사용할 사용자, 회사 및 사용자 시나리오에 대한 정보를 요청합니다. 양식을 제출하면 Azure Cognitive Services 팀에서 검토하여 프라이빗 컨테이너 레지스트리에 대한 액세스 조건을 충족하는지 확인합니다.

> [!IMPORTANT]
> * 양식에서 Azure 구독 ID와 연결된 이메일 주소를 사용해야 합니다.
> * 컨테이너를 실행하는 데 사용하는 Text Analytics 리소스(청구 엔드포인트 및 apikey)는 승인된 Azure 구독 ID로 만들어야 합니다. 
> * Microsoft에서 애플리케이션의 상태에 대한 업데이트를 확인하려면 이메일(받은 편지함 및 스팸 메일함 모두)을 확인합니다.
> * 이 컨테이너 URL이 변경되었습니다. 아래 예제를 참조하세요. 2021년 4월 26일부터 컨테이너를 `containerpreview.azurecr.io`에서 다운로드할 수 없습니다.


승인되면 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 명령을 사용하여 Microsoft 공용 컨테이너 레지스트리에서 이 컨테이너 이미지를 다운로드합니다.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest
```
