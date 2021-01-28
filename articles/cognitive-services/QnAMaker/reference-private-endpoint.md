---
title: 개인 끝점을 설정 하는 방법-QnA Maker
description: QnA Maker 관리에서 사용할 수 있는 개인 끝점 만들기를 이해 합니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 01/12/2021
ms.openlocfilehash: 5a7ddcf25993ab4ce87bdc47c5e0ae32fa24b07f
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956539"
---
# <a name="private-endpoints"></a>프라이빗 엔드포인트

Azure 프라이빗 엔드포인트는 Azure Private Link가 제공하는, 서비스에 비공개로 안전하게 연결하는 네트워크 인터페이스입니다. 이제 QnA Maker에서는 Azure Search 서비스에 대 한 개인 끝점을 만들 수 있도록 지원 합니다. 이 기능은 QnA Maker 관리에서 사용할 수 있습니다. 

개인 끝점은 [Azure 개인 링크](https://docs.microsoft.com/azure/private-link/private-link-overview)에서 별도 서비스로 제공 됩니다. 비용에 대 한 자세한 내용은 [가격 책정 페이지를 참조 하세요.](https://azure.microsoft.com/pricing/details/private-link/) 

## <a name="prerequisites"></a>사전 요구 사항
> [!div class="checklist"]
> * Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.
> * Azure Portal에서 만든 QnA Maker [관리 되는 리소스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) 입니다. 리소스를 만들 때 선택한 Azure Active Directory ID, 구독, QnA 리소스 이름을 기억하세요.

## <a name="steps-to-enable-private-endpoint"></a>개인 끝점을 사용 하도록 설정 하는 단계
1. Azure Search 서비스 인스턴스에서 QnA Maker 관리 서비스에 *기여자* 역할을 할당 합니다. 이 작업을 수행 하려면 구독에 대 한 *소유자* 권한이 있어야 합니다. Id를 가져오려면 서비스 리소스에서 Id 탭으로 이동 합니다.
![관리 서비스 Id](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-identity.png)

2. Azure Search Service IAM 탭으로 이동 하 여 위의 id를 *기여자* 로 추가 합니다. ![ 관리 서비스 IAM](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-access-control.png)

3. *역할 할당 추가* 를 클릭 하 고, id를 추가 하 고, *저장* 을 클릭 합니다.
![관리 역할 할당](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-role-assignment.png)

4. 이제 Azure Search 서비스 인스턴스에서 *네트워킹* 탭으로 이동 하 고 끝점 연결 데이터를 *공용* 에서 *개인* 으로 전환 합니다. 이 작업은 장기 실행 프로세스 이며 완료 하는 데 최대 30 분 정도 걸릴 수 있습니다. 
![관리 되는 Azure search 네트워킹](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking.png)

5. QnA Maker 관리 서비스의 *네트워킹* 탭으로 이동 하 여 *액세스 허용에서* *선택 된 네트워크 및 개인 끝점* 옵션을 선택 하 고 *저장* 을 클릭 합니다. 
![Managed QnA maker newtorking](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-2.png)

그러면 QnA maker 서비스와 Azure 인지 검색 서비스 인스턴스 간의 개인 끝점 연결이 설정 됩니다. Azure Search 서비스 인스턴스의 *네트워킹* 탭에서 개인 끝점 연결을 확인할 수 있습니다. 전체 작업이 완료 되 면 QnA Maker 서비스를 사용 하는 것이 좋습니다. 
![관리 네트워킹 서비스](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-3.png)


## <a name="support-details"></a>지원 세부 정보
 * QnAMaker 서비스에 대 한 개인 액세스를 사용 하도록 설정 하면 Azure Search 서비스 변경을 지원 하지 않습니다. 개인 액세스를 사용 하도록 설정한 후 ' 구성 ' 탭을 통해 Azure Search 서비스를 변경 하면 QnAMaker 서비스를 사용할 수 없게 됩니다.
 * 개인 끝점 연결을 설정한 후 Azure Search 서비스 네트워킹을 ' Public '으로 전환 하면 QnAMaker 서비스를 사용할 수 없습니다. 개인 끝점 연결이 작동 하려면 Azure Search 서비스 네트워킹이 ' 개인 ' 이어야 합니다.
