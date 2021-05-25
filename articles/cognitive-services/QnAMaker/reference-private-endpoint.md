---
title: 프라이빗 엔드포인트를 설정하는 방법 - QnA Maker
description: QnA Maker 관리에서 사용할 수 있는 프라이빗 엔드포인트 생성을 이해합니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 01/12/2021
ms.openlocfilehash: 7907c81e45680de49f6653891fb4204a59db1002
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101710552"
---
# <a name="private-endpoints"></a>프라이빗 엔드포인트

Azure 프라이빗 엔드포인트는 Azure Private Link가 제공하는, 서비스에 비공개로 안전하게 연결하는 네트워크 인터페이스입니다. 이제 QnA Maker에서는 Azure Search Service에 대한 프라이빗 엔드포인트를 만들 수 있도록 지원합니다. 이 기능은 QnA Maker 관리에서 사용할 수 있습니다. 

프라이빗 엔드포인트는 [Azure Private Link](../../private-link/private-link-overview.md)에 의해 별도의 서비스로 제공됩니다. 비용에 대한 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/private-link/)를 참조하세요. 

## <a name="prerequisites"></a>사전 요구 사항
> [!div class="checklist"]
> * Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.
> * Azure Portal에서 만든 QnA Maker [관리형 리소스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)입니다. 리소스를 만들 때 선택한 Azure Active Directory ID, 구독, QnA 리소스 이름을 기억하세요.

## <a name="steps-to-enable-private-endpoint"></a>프라이빗 엔드포인트를 사용하도록 설정하는 단계
1. Azure Search Service 인스턴스에서 QnA Maker 관리 서비스에 *기여자* 역할을 할당합니다. 이 작업을 수행하려면 구독에 대한 *소유자* 권한이 있어야 합니다. 서비스 리소스의 ID 탭으로 이동하여 ID를 가져옵니다.
![관리 서비스 ID](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-identity.png)

2. Azure Search Service IAM 탭으로 이동하여 위의 ID를 *기여자* 로 추가합니다. ![관리 서비스 IAM](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-access-control.png)

3. *역할 할당 추가* 를 클릭하고, ID를 추가하고, *저장* 을 클릭합니다.
![관리형 역할 할당](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-role-assignment.png)

4. 이제 Azure Search Service 인스턴스의 *네트워킹* 탭으로 이동하고 엔드포인트 연결 데이터를 *퍼블릭* 에서 *프라이빗* 으로 전환합니다. 이 작업은 장기 실행 프로세스이며, 완료하는 데 최대 30분이 걸릴 수 있습니다. 
![관리형 Azure 검색 네트워킹](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking.png)

5. QnA Maker 관리 서비스의 *네트워킹* 탭으로 이동하여 *액세스 허용 위치* 아래에서 *선택한 네트워크 및 프라이빗 엔드포인트* 옵션을 선택하고 *저장* 을 클릭합니다. 
![관리형 QnA Maker 네트워킹](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-2.png)

그러면 QnA Maker 서비스와 Azure 인지 검색 서비스 인스턴스 간에 프라이빗 엔드포인트 연결이 설정됩니다. Azure Search 서비스 인스턴스의 *네트워킹* 탭에서 프라이빗 엔드포인트 연결을 확인할 수 있습니다. 전체 작업이 완료되면 QnA Maker 서비스를 사용하는 것이 좋습니다. 
![관리형 네트워킹 서비스](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-3.png)


## <a name="support-details"></a>지원 세부 정보
 * QnAMaker 서비스에 대한 프라이빗 액세스를 사용하도록 설정하면 Azure Search Service 변경을 지원하지 않습니다. 프라이빗 액세스를 사용하도록 설정한 후 '구성' 탭을 통해 Azure Search Service를 변경하면 QnAMaker 서비스를 사용할 수 없게 됩니다.
 * 프라이빗 엔드포인트 연결을 설정한 후 Azure Search Service 네트워킹을 'Public'으로 전환하면 QnAMaker 서비스를 사용할 수 없습니다. 프라이빗 엔드포인트 연결이 작동하려면 Azure Search 서비스 네트워킹이 'Private'이어야 합니다.