---
title: AWS 클라우드트레일을 Azure 센티넬에 연결 | 마이크로 소프트 문서
description: AWS CloudTrail 데이터를 Azure Sentinel에 연결하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 5cbef1f31ea7088d4fab4888f5630af1b765a910
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588657"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>Azure 센티넬을 AWS 클라우드트레일에 연결

AWS 커넥터를 사용하여 모든 AWS CloudTrail 이벤트를 Azure Sentinel으로 스트리밍합니다. 이 연결 프로세스는 Aws 리소스 로그에 Azure Sentinel에 대한 액세스를 위임하여 AWS CloudTrail과 Azure Sentinel 간의 신뢰 관계를 만듭니다. 이 작업은 Azure Sentinel이 AWS 로그에 액세스할 수 있는 권한을 부여하는 역할을 만들어 AWS에서 수행됩니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure Sentinel 작업 영역에 대한 쓰기 권한이 있어야 합니다.

> [!NOTE]
> Azure Sentinel은 모든 리전에서 CloudTrail 이벤트를 수집합니다. 한 리전에서 다른 리전으로 이벤트를 스트리밍하지 않는 것이 좋습니다.

## <a name="connect-aws"></a>AWS 연결 


1. Azure Sentinel에서 **데이터 커넥터를** 선택한 다음 테이블과 오른쪽AWS 창에서 **Amazon Web Services** 회선을 선택한 다음 커넥터 열기 **페이지를**클릭합니다.

1. 다음 단계를 사용하여 **구성** 아래의 지침을 따릅니다.
 
1.  Amazon 웹 서비스 콘솔에서 **보안, ID & 규정 준수에서** **IAM을**선택합니다.

    ![AWS1](./media/connect-aws/aws-1.png)

1.  **역할을** 선택하고 **역할 만들기를**선택합니다.

    ![AWS2](./media/connect-aws/aws-2.png)

1.  **다른 AWS 계정을 선택합니다.** 계정 **ID** 필드에 Azure Sentinel 포털의 AWS 커넥터 페이지에서 찾을 수 있는 **Microsoft 계정** ID(123412341234)를 입력합니다.**123412341234**

    ![AWS3](./media/connect-aws/aws-3.png)

1.  외부 **ID 필요를** 선택한 다음 Azure Sentinel 포털의 AWS 커넥터 페이지에서 찾을 수 있는 외부 ID(작업 영역 ID)를 입력합니다.

    ![AWS4](./media/connect-aws/aws-4.png)

1.  권한 연결 정책에서 **AWSCloudTrailReadOnlyAccess**를 **선택합니다.**

    ![AWS5](./media/connect-aws/aws-5.png)

1.  태그를 입력합니다(선택 사항).

    ![AWS6](./media/connect-aws/aws-6.png)

1.  그런 다음 **역할 이름을** 입력하고 **역할 만들기** 단추를 선택합니다.

    ![AWS7](./media/connect-aws/aws-7.png)

1.  역할 목록에서 만든 역할을 선택합니다.

    ![AWS8](./media/connect-aws/aws-8.png)

1.  역할 **ARN을**복사합니다. Azure Sentinel 포털에서 Amazon 웹 서비스 커넥터 화면에서 **필드를 추가하고** **추가를**클릭하여 역할에 붙여넣습니다.

    ![AWS9](./media/connect-aws/aws-9.png)

1. AWS 이벤트에 대한 로그 분석에서 관련 스키마를 사용하려면 **AWSCloudTrail**을 검색합니다.



## <a name="next-steps"></a>다음 단계
이 문서에서는 AWS CloudTrail을 Azure Sentinel에 연결하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용하여](tutorial-monitor-your-data.md) 데이터를 모니터링합니다.

