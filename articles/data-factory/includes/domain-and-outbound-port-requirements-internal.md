---
title: 포함 파일
description: 포함 파일
author: lrtoyou1223
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: 09f61060f9a76036f5e2d58512ddde9248402bbd
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122642863"
---
| 도메인 이름                                          | 아웃바운드 포트 | Description                |
| ----------------------------------------------------- | -------------- | ---------------------------|
| 퍼블릭 클라우드: `*.servicebus.windows.net` <br> Azure Government: `*.servicebus.usgovcloudapi.net` <br> 중국: `*.servicebus.chinacloudapi.cn`   | 443            | 대화형 작성을 위해 자체 호스팅 통합 런타임에 필요합니다. |
| 퍼블릭 클라우드: `{datafactory}.{region}.datafactory.azure.net`<br> 또는 `*.frontend.clouddatahub.net` <br> Azure Government: `{datafactory}.{region}.datafactory.azure.us` <br> 중국: `{datafactory}.{region}.datafactory.azure.cn` | 443            | 자체 호스팅 통합 런타임에서 Data Factory 서비스에 연결하는 데 필요합니다. <br>퍼블릭 클라우드에서 새로 만든 Data Factory의 경우 {datafactory}.{region}.datafactory.azure.net 형식의 자체 호스팅 통합 런타임 키에서 FQDN을 확인하세요. 이전 Data Factory의 경우 FQDN이 자체 호스팅 통합 키에 표시되지 않으면 *.frontend.clouddatahub.net을 대신 사용하세요. |
| `download.microsoft.com`    | 443            | 업데이트를 다운로드하기 위해 자체 호스팅 통합 런타임에서 필요합니다. 자동 업데이트를 사용하지 않도록 설정한 경우 이 도메인 구성을 건너뛸 수 있습니다. |
| 주요 자격 증명 모음 URL | 443           | Key Vault에 자격 증명을 저장하는 경우 Azure Key Vault별로 필요합니다. |
