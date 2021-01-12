---
title: 포함 파일
description: 포함 파일
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: 45c6bbb88ef1f01f729451af27ecc73244483216
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121675"
---
| 도메인 이름                                          | 아웃바운드 포트 | Description                |
| ----------------------------------------------------- | -------------- | ---------------------------|
| 공용 클라우드: `*.servicebus.windows.net` <br> Azure Government: `*.servicebus.usgovcloudapi.net` <br> 중국 `*.servicebus.chinacloudapi.cn`   | 443            | 대화형 작성을 위해 자체 호스팅 통합 런타임에 필요합니다. |
| 공용 클라우드: `{datafactory}.{region}.datafactory.azure.net`<br> 또는 `*.frontend.clouddatahub.net` <br> Azure Government: `{datafactory}.{region}.datafactory.azure.us` <br> 중국 `{datafactory}.{region}.datafactory.azure.cn` | 443            | 자체 호스팅 통합 런타임에서 Data Factory 서비스에 연결하는 데 필요합니다. <br>공용 클라우드에서 새로 만든 Data Factory의 경우 {datafactory} 형식의 자체 호스트 된 Integration Runtime 키에서 FQDN을 확인 하세요. {region}. datafactory. 이전 Data Factory의 경우 FQDN이 자체 호스팅 통합 키에 표시되지 않으면 *.frontend.clouddatahub.net을 대신 사용하세요. |
| `download.microsoft.com`    | 443            | 업데이트를 다운로드하기 위해 자체 호스팅 통합 런타임에서 필요합니다. 자동 업데이트를 사용하지 않도록 설정한 경우 이 도메인 구성을 건너뛸 수 있습니다. |
| 주요 자격 증명 모음 URL | 443           | Key Vault에 자격 증명을 저장 하는 경우 Azure Key Vault에 필요 합니다. |
