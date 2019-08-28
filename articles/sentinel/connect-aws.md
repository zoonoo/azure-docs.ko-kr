---
title: Azure 센티널 Preview에 Symantec AWS 데이터 연결 | Microsoft Docs
description: Azure 센티널에 Symantec AWS 데이터를 연결 하는 방법에 대해 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2019
ms.author: rkarlin
ms.openlocfilehash: 246d4cd7d64554ae575767cdba2e26066ad1720d
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295614"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>AWS CloudTrail에 Azure 센티널 연결

AWS 커넥터를 사용 하 여 모든 AWS CloudTrail 이벤트를 Azure 센티널로 스트리밍합니다. 이 연결 프로세스는 AWS 리소스 로그에 Azure 센티널에 대 한 액세스를 위임 하 여 AWS CloudTrail와 Azure 센티널 간의 트러스트 관계를 만듭니다. 이는 AWS 로그에 액세스할 수 있도록 Azure 센티널에 권한을 부여 하는 역할을 만들어 AWS에서 수행 됩니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure 센티널 작업 영역에 대 한 쓰기 권한이 있어야 합니다.

> [!NOTE]
> Azure 센티널은 모든 지역에서 CloudTrail 이벤트를 수집 합니다. 한 지역에서 다른 지역으로 이벤트를 스트리밍하는 것이 좋습니다.

## <a name="connect-aws"></a>AWS 연결 


1. Azure 센티널에서 **데이터 커넥터** 를 선택한 다음 테이블에서 **Amazon Web Services** 줄을 선택 하 고 오른쪽에 있는 AWS 창에서 **커넥터 페이지 열기**를 클릭 합니다.

1. 다음 단계를 사용 하 여 **구성** 의 지침을 따릅니다.
 
1.  Amazon Web Services 콘솔의 **보안, id & 준수**에서 **IAM**을 선택 합니다.

    ![AWS1](./media/connect-aws/aws-1.png)

1.  **역할** 을 선택 하 고 **역할 만들기**를 선택 합니다.

    ![AWS2](./media/connect-aws/aws-2.png)

1.  **다른 AWS 계정을 선택 합니다.** **계정 id** 필드에 Azure 센티널 포털의 AWS 커넥터 페이지에서 찾을 수 있는 **Microsoft 계정 id** (**123412341234**)를 입력 합니다.

    ![AWS3](./media/connect-aws/aws-3.png)

1.  **외부 Id 필요** 가 선택 되어 있는지 확인 하 고 Azure 센티널 포털의 AWS 커넥터 페이지에서 찾을 수 있는 외부 Id (작업 영역 id)를 입력 합니다.

    ![AWS4](./media/connect-aws/aws-4.png)

1.  **권한 연결 정책** 에서 **AWSCloudTrailReadOnlyAccess**를 선택 합니다.

    ![AWS5](./media/connect-aws/aws-5.png)

1.  태그를 입력 합니다 (선택 사항).

    ![AWS6](./media/connect-aws/aws-6.png)

1.  그런 다음 **역할 이름을** 입력 하 고 **역할 만들기** 단추를 선택 합니다.

    ![AWS7](./media/connect-aws/aws-7.png)

1.  역할 목록에서 만든 역할을 선택 합니다.

    ![AWS8](./media/connect-aws/aws-8.png)

1.  **역할 ARN**을 복사 합니다. Azure 센티널 포털의 Amazon Web Services 커넥터 화면에서 **추가할 역할** 에 해당 필드를 붙여넣고 **추가**를 클릭 합니다.

    ![AWS9](./media/connect-aws/aws-9.png)

1. AWS 이벤트에 대 한 Log Analytics에서 관련 스키마를 사용 하려면 **Awscloudtrail**를 검색 합니다.



## <a name="next-steps"></a>다음 단계
이 문서에서는 AWS CloudTrail를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터 및 잠재적 위협에 대 한 가시성을 얻는](quickstart-get-visibility.md)방법에 대해 알아봅니다.
- [Azure 센티널로 위협 검색을](tutorial-detect-threats.md)시작 합니다.

