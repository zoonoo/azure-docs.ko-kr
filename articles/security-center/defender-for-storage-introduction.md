---
title: 저장소에 대 한 Azure Defender-혜택 및 기능
description: 저장소에 대 한 Azure Defender의 이점 및 기능에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 1c47c1cd464c9dc0ac8dc78f5d6f91f33481922a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91307520"
---
# <a name="introduction-to-azure-defender-for-storage"></a>저장소에 대 한 Azure Defender 소개

**Storage 용 Azure Defender는** Azure Storage 계정에서 잠재적으로 유해한 작업을 검색 합니다. Blob 컨테이너, 파일 공유 또는 데이터 레이크 저장 되어 있는지에 관계 없이 데이터를 보호할 수 있습니다.

이 보호 계층을 사용 하면 보안 전문가가 아니어도 *위협을 해결* 하 고 보안 모니터링 시스템을 관리할 수 있습니다.


## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|결정|**저장소에 대 한 Azure Defender** 는 [가격 책정 페이지](security-center-pricing.md) 에 표시 된 대로 청구 됩니다.|
|보호 된 저장소 유형|[Blob Storage](https://azure.microsoft.com/services/storage/blobs/)<br>[Azure 파일](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)<br>[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)|
|클라우드:|![예](./media/icons/yes-icon.png) 상용 클라우드<br>![예](./media/icons/yes-icon.png) US Gov<br>![예](./media/icons/no-icon.png) 중국 .Gov, 기타 .Gov|
|||


## <a name="what-kind-of-alerts-does-azure-defender-for-storage-provide"></a>Azure Defender에서 저장소에 제공 하는 경고 종류는 무엇 인가요?

보안 경고는 다음과 같은 경우에 트리거됩니다.

- **의심 스러운 활동** -예를 들어 저장소 계정에 대 한 액세스 권한이 있는 IP 주소에서의 활성 종료 노드 라고 합니다.
- **비정상 동작** -예를 들어 저장소 계정에 대 한 액세스 패턴의 변경 내용
- **업로드 된 잠재적인 맬웨어** -해시 평판 분석 업로드 된 파일에 맬웨어가 포함 되어 있음을 나타냅니다.

경고에는 위협을 조사 하 고 수정 하는 방법에 대 한 권장 사항 뿐만 아니라 트리거를 트리거한 인시던트의 세부 정보가 포함 됩니다.

> [!TIP]
> [이 블로그 게시물](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131)의 지침에 따라 저장소 경고를 시뮬레이션할 수 있습니다.


## <a name="what-is-hash-reputation-analysis-for-malware"></a>맬웨어에 대 한 해시 평판 분석 이란?

업로드 된 파일이 의심 스러운 지 여부를 확인 하기 위해 Azure Defender for Storage는 [Microsoft 위협 인텔리전스](https://go.microsoft.com/fwlink/?linkid=2128684)에서 지 원하는 해시 평판 분석을 사용 합니다. 위협 방지 도구는 업로드 된 파일을 검사 하지 않고, 저장소 로그를 검토 하 고 새로 업로드 된 파일의 해시를 알려진 바이러스, 트로이 목마, 스파이웨어 및 랜 섬 웨어의 해시와 비교 합니다. 

파일이 맬웨어를 포함 하는 것으로 의심 되는 경우 Security Center 경고를 표시 하 고 의심 스러운 파일을 삭제 하기 위해 필요에 따라 저장소 소유자에 게 메일을 보낼 수 있습니다. 해시 평판 분석에 맬웨어 포함을 나타내는 파일의 자동 제거를 설정 하려면 ["저장소 계정에 업로드 된 잠재적인 맬웨어"가 포함 된 경고에 대해 트리거할 워크플로 자동화](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005)를 배포 합니다.

> [!NOTE]
> Security Center의 위협 방지 기능을 사용 하도록 설정 하려면 해당 하는 작업을 포함 하는 구독에서 Azure Defender를 사용 하도록 설정 해야 합니다.
>
> 구독 수준 또는 리소스 수준에서 **저장소에 대해 Azure Defender를** 사용 하도록 설정할 수 있습니다.



## <a name="next-steps"></a>다음 단계

이 문서에서는 저장소에 대 한 Azure Defender에 대해 알아보았습니다.

관련 자료는 다음 문서를 참조하세요. 

- Security Center에서 경고를 생성하든 Security Center에서 다른 보안 제품으로부터 경고를 받든 이를 내보낼 수 있습니다. 경고를 Azure Sentinel, 타사 SIEM 또는 기타 외부 도구로 내보내려면 [SIEM으로 경고 내보내기](continuous-export.md)의 지침을 따릅니다.
- [저장소에 대해 Advanced Defender를 사용 하도록 설정 하는 방법](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
- [저장소 경고에 대 한 Azure Defender 목록](alerts-reference.md#alerts-azurestorage)
- [Microsoft의 위협 인텔리전스 기능](https://go.microsoft.com/fwlink/?linkid=2128684)