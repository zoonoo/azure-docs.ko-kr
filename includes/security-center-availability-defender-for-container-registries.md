---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 11/22/2020
ms.topic: include
ms.openlocfilehash: 30de9181fd23a29b28973d01899f0b23fca3ae89
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95530000"
---
## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|가격 책정:|**컨테이너 레지스터리용 Azure Defender** 는 [가격 책정 페이지](../articles/security-center/security-center-pricing.md)에 표시된 대로 요금이 청구됩니다.|
|지원되는 레지스트리 및 이미지:|셸 액세스를 사용하여 공용 인터넷에서 액세스할 수 있는 ACR 레지스트리의 Linux 이미지|
|지원되지 않는 레지스트리 및 이미지:|Windows 이미지<br>'프라이빗' 레지스트리<br>방화벽, 서비스 엔드포인트 또는 Azure Private Link와 같은 프라이빗 엔드포인트로 액세스가 제한된 레지스트리<br>[Docker 스크래치](https://hub.docker.com/_/scratch/) 이미지와 같은 초 미니멀 이미지 또는 패키지 관리자, 셸 또는 OS 없이 애플리케이션과 그 런타임 종속성만 포함하는 "Distroless" 이미지|
|필요한 역할 및 권한:|**보안 읽기 권한자** 및 [Azure Container Registry 역할 및 권한](../articles/container-registry/container-registry-roles.md)|
|클라우드:|:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: 상용 클라우드<br>:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: US Gov - 현재 푸시 기능의 검색만 지원됩니다. [이미지가 언제 검사되나요?](../articles/security-center/defender-for-container-registries-introduction.md#when-are-images-scanned)에서 자세히 알아보기<br>:::image type="icon" source="../articles/security-center/media/icons/no-icon.png" border="false"::: 중국 정부, 기타 정부|
|||