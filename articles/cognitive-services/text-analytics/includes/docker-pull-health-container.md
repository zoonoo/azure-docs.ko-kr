---
title: 상태 컨테이너에 대 한 Docker 풀
titleSuffix: Azure Cognitive Services
description: 상태 컨테이너의 Text Analytics에 대 한 Docker pull 명령
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: a0b2c9548f9c1289ae0abd61a72d7146a3bbca29
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965158"
---
상태 공개 미리 보기에 대 한 Text Analytics에 대 한 액세스를 요청 하는 [Cognitive Services 요청 양식을](https://aka.ms/csgate) 작성 하 고 제출 합니다.  이 응용 프로그램은 컨테이너와 호스트 된 web API 공개 미리 보기 모두에 적용 됩니다.
이 양식에서는 컨테이너를 사용할 사용자, 회사 및 사용자 시나리오에 대한 정보를 요청합니다. 양식을 제출한 후 Azure Cognitive Services 팀은이를 검토 하 여 개인 컨테이너 레지스트리에 대 한 액세스 조건을 충족 하는지 확인 합니다.

> [!IMPORTANT]
> * 양식에서 Azure 구독 ID와 연결 된 전자 메일 주소를 사용 해야 합니다.
> * 컨테이너를 실행 하는 데 사용 하는 Azure 리소스는 승인 된 Azure 구독 ID로 생성 되어야 합니다. 
> * Microsoft에서 응용 프로그램의 상태에 대 한 업데이트를 확인 하려면 전자 메일 (받은 편지함 및 정크 폴더 모두)을 확인 합니다.

승인 되 면 개인 컨테이너 레지스트리에 액세스 하기 위한 자격 증명을 포함 하는 전자 메일을 받게 됩니다.  온 보 딩 전자 메일에 제공 된 자격 증명과 함께 docker login 명령을 사용 하 여 Cognitive Services 컨테이너를 위한 개인 컨테이너 레지스트리에 연결 합니다.


```Docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

명령을 사용 [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) 하 여 개인 컨테이너 레지스트리에서이 컨테이너 이미지를 다운로드 합니다.

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
