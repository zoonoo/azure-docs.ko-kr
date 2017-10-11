---
title: "Power BI를 사용하여 Azure Analysis Services에 연결 | Microsoft Docs"
description: "Power BI를 사용하여 Azure Analysis Services 서버에 연결하는 방법에 대해 알아봅니다."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/15/2017
ms.author: owend
ms.openlocfilehash: 3a2af043feddb4a1d6d63f50e838c8a39035449f
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2017
---
# <a name="connect-with-power-bi"></a>Power BI로 연결

Azure에서 서버를 만들고 테이블 형식 모델을 배포하면 조직의 사용자는 데이터를 연결하고 탐색할 준비가 되었습니다. 

> [!TIP]
> 최신 버전의 [Power BI Desktop](https://powerbi.microsoft.com/desktop/)을 사용해야 합니다.
> 
> 
  
## <a name="connect-in-power-bi-desktop"></a>Power BI Desktop에서 연결

1. Power BI Desktop에서 **데이터 가져오기** > **Azure** > **Azure Analysis Services 데이터베이스**를 클릭합니다.

2. **서버**에 서버 이름을 입력합니다. 
    
    전체 URL을 포함해야 합니다. 예를 들어 asazure://westcentralus.asazure.windows.net/advworks 같이 입력합니다.

3. **데이터베이스**에서 연결하려는 테이블 형식 모델 데이터베이스 및 큐브 뷰의 이름을 알고 있는 경우 여기에 붙여 넣습니다. 그러지 않으면 이 필드를 비워 두고 나중에 데이터베이스 또는 큐브 뷰를 선택합니다.

4. 기본 **라이브 연결** 옵션을 그대로 두고 **연결**을 누릅니다. 

5. 메시지가 표시되면 로그인 자격 증명을 입력합니다. 

6. **탐색기**에서 서버를 확장한 다음 연결하려는 모델 또는 큐브 뷰를 선택하고 **연결**을 클릭합니다. 모델 또는 큐브 뷰를 클릭하여 해당 보기에 대한 모든 개체를 표시합니다.

    모델은 보고서 보기의 빈 보고서와 함께 Power BI Desktop에서 열립니다. 필드 목록에는 숨겨지지 않은 모든 모델 개체가 표시됩니다. 연결 상태는 오른쪽 아래 모서리에 표시됩니다.

## <a name="connect-in-power-bi-service"></a>Power BI(서비스)에서 연결

1. 서버의 모델에 대한 라이브 연결을 포함하는 Power BI Desktop 파일을 만듭니다.
2. [Power BI](https://powerbi.microsoft.com)에서 **데이터 가져오기** > **파일**을 클릭합니다. 파일을 찾아 선택합니다.



## <a name="see-also"></a>참고 항목
[Azure Analysis Services에 연결](analysis-services-connect.md)   
[클라이언트 라이브러리](analysis-services-data-providers.md)

