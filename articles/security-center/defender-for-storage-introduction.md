---
title: Azure Defender for Storage - 이점 및 특징
description: Azure Defender for Storage의 이점 및 특징에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 9b2855f82927b6f1707fd748f097dd357818ac4b
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92341977"
---
# <a name="introduction-to-azure-defender-for-storage"></a>Azure Defender for Storage 소개

**Azure Defender for Storage**는 Azure Storage 계정에서 이루어지는 잠재적으로 유해한 활동을 탐지합니다. Blob 컨테이너, 파일 공유 또는 데이터 레이크로 저장된 데이터를 보호할 수 있습니다.

이 보호 계층을 사용하면 보안 전문가가 *아니더라도* 위협을 처리할 수 있으며, 보안 모니터링 시스템을 관리할 수 있습니다.


## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|가격 책정:|**Azure Defender for Storage**는 [가격 책정 페이지](security-center-pricing.md)에 표시된 대로 요금이 청구됩니다.|
|보호된 스토리지 형식:|[Blob Storage](https://azure.microsoft.com/services/storage/blobs/)<br>[Azure 파일](../storage/files/storage-files-introduction.md)<br>[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)|
|클라우드:|![예](./media/icons/yes-icon.png) 상용 클라우드<br>![예](./media/icons/yes-icon.png) US Gov<br>![아니요](./media/icons/no-icon.png) 중국 정부, 기타 정부|
|||


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>Azure Defender for Storage는 어떤 종류의 경고를 제공하나요?

다음과 같은 경우에 보안 경고가 트리거됩니다.

- **의심스러운 활동** - 예를 들어 Tor의 활성 종료 노드로 알려진 IP 주소에서 스토리지 계정에 성공적으로 액세스했습니다.
- **비정상 동작** - 예를 들어 스토리지 계정에 대한 액세스 패턴이 변했습니다.
- **잠재적 맬웨어 업로드** - 해시 평판 분석을 보면 업로드된 파일에 맬웨어가 포함되어 있습니다.

경고에는 경고를 트리거한 인시던트의 세부 정보와 위협을 조사하고 완화하는 방법에 대한 권장 사항이 포함됩니다.

> [!TIP]
> [이 블로그 게시물](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131)의 지침에 따라 스토리지 경고를 시뮬레이션할 수 있습니다.


## <a name="what-is-hash-reputation-analysis-for-malware"></a>맬웨어에 대한 해시 평판 분석이란?

업로드된 파일이 의심스러운지 확인하기 위해 Azure Defender for Storage는 [Microsoft 위협 인텔리전스](https://go.microsoft.com/fwlink/?linkid=2128684)에서 지원하는 해시 평판 분석을 사용합니다. 위협 방지 도구는 업로드된 파일을 검사하는 것이 아니라 스토리지 로그를 검사하고 새로 업로드된 파일의 해시를 알려진 바이러스, 트로이 목마, 스파이웨어 및 랜섬웨어의 해시와 비교합니다. 

파일에 맬웨어가 들어 있는 것으로 의심되면 Security Center는 경고를 표시하고 경우에 따라 의심스러운 파일을 삭제하기 위해 스토리지 소유자에 삭제 승인을 요청하는 이메일을 보낼 수 있습니다. 해시 평판 분석 결과, 맬웨어를 포함하고 있는 것으로 보이는 파일을 자동으로 제거하도록 설정하려면 ["스토리지 계정에 잠재적 맬웨어가 업로드"되었다는 내용의 경고 발생 시 트리거되는 워크플로 자동화를 배포](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005)합니다.

> [!NOTE]
> Security Center의 위협 방지 기능을 사용하도록 설정하려면 해당 워크로드가 포함된 구독에서 Azure Defender를 사용하도록 설정해야 합니다.
>
> **Azure Defender for Storage**는 구독 수준 또는 리소스 수준에서 사용하도록 설정할 수 있습니다.



## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Defender for Storage에 대해 알아보았습니다.

관련 자료는 다음 문서를 참조하세요. 

- Security Center에서 경고를 생성하든 Security Center에서 다른 보안 제품으로부터 경고를 받든 이를 내보낼 수 있습니다. 경고를 Azure Sentinel, 타사 SIEM 또는 기타 외부 도구로 내보내려면 [SIEM으로 경고 내보내기](continuous-export.md)의 지침을 따릅니다.
- [Advanced Defender for Storage를 사용하도록 설정하는 방법](../storage/common/azure-defender-storage-configure.md)
- [Azure Defender for Storage 경고 목록](alerts-reference.md#alerts-azurestorage)
- [Microsoft의 위협 인텔리전스 기능](https://go.microsoft.com/fwlink/?linkid=2128684)