---
title: Azure 미리 보기 Sentinel Symantec AWS 데이터 연결할 | Microsoft Docs
description: Symantec AWS 데이터 Azure Sentinel를 연결 하는 방법에 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/04/2019
ms.author: rkarlin
ms.openlocfilehash: 214269bc5c854aa4d3bfd508b0adb5a53ec096df
ms.sourcegitcommit: 80aaf27e3ad2cc4a6599a3b6af0196c6239e6918
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673974"
---
# <a name="connect-azure-sentinel-to-aws"></a>Azure Sentinel AWS 연결

Azure Sentinel에 모든 AWS CloudTrail 이벤트를 스트림 하려면 AWS 커넥터를 사용 합니다. 이 연결 프로세스는 AWS CloudTrail와 Azure Sentinel 간의 트러스트 관계를 만들고, AWS 리소스 로그로 Azure Sentinel에 대 한 액세스를 위임 합니다. 이렇게 하려면 AWS에서 Azure에 AWS 로그에 액세스 하려면 Sentinel 권한을 제공 하는 역할을 만들어 합니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure Sentinel 작업 영역에 대 한 쓰기 권한이 있어야 합니다.

## <a name="connect-aws"></a>AWS 연결 
 
1.  Amazon 웹 서비스에서 콘솔의 **Security, Identity & Compliance**, 클릭 **IAM**합니다.

    ![AWS1](./media/connect-aws/aws-1.png)

2.  선택할 **역할** 누릅니다 **역할 만들기**합니다.

    ![AWS2](./media/connect-aws/aws-2.png)

3.  선택 **다른 AWS 계정.** **계정 ID** 필드를 입력 합니다 **Microsoft 계정 ID** (**123412341234**)는 Sentinel Azure portal의 AWS 커넥터 페이지에서 찾을 수 있습니다.

    ![AWS3](./media/connect-aws/aws-3.png)

4.  했는지 **외부 ID 필요** 선택 차례로 Sentinel Azure portal의 AWS 커넥터 페이지에서 찾을 수 있는 외부 ID (작업 영역 ID)를 입력 합니다.

    ![AWS4](./media/connect-aws/aws-4.png)

5.  아래 **사용 권한 정책 연결** 선택 **AWSCloudTrailReadOnlyAccess**합니다.

    ![AWS5](./media/connect-aws/aws-5.png)

6.  (선택 사항) 태그를 입력 합니다.

    ![AWS6](./media/connect-aws/aws-6.png)

7.  그런 다음 입력을 **역할 이름** 클릭 하 고는 **역할 만들기** 단추.

    ![AWS7](./media/connect-aws/aws-7.png)

8.  역할 목록에서 만든 역할을 선택 합니다.

    ![AWS8](./media/connect-aws/aws-8.png)

9.  복사 합니다 **역할 ARN** 에 붙여 넣습니다 합니다 **추가할 역할** Sentinel Azure Portal에서 필드.

    ![AWS9](./media/connect-aws/aws-9.png)

10. Log Analytics에서 관련 스키마를 사용 하 여 AWS 이벤트를 검색할 **AWSCloudTrail**합니다.



## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Sentinel AWS CloudTrail 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- 에 대해 알아봅니다 하는 방법 [데이터에 잠재적 위협을 파악](quickstart-get-visibility.md)합니다.
- 시작 [사용 하 여 Azure Sentinel 위협을 감지 하도록](tutorial-detect-threats.md)합니다.

