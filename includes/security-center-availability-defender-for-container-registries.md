---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 05/19/2021
ms.topic: include
ms.openlocfilehash: 103a23a478a3629eb913c3d1672b3665bc9cba9c
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113559415"
---
## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|가격 책정:|**컨테이너 레지스터리용 Azure Defender** 는 [가격 책정 페이지](../articles/security-center/security-center-pricing.md)에 표시된 대로 요금이 청구됩니다.|
|지원되는 레지스트리 및 이미지:|셸 액세스를 사용하여 공용 인터넷에서 액세스할 수 있는 ACR 레지스트리의 Linux 이미지|
|지원되지 않는 레지스트리 및 이미지:|Windows 이미지<br>'프라이빗' 레지스트리<br>[Docker 스크래치](https://hub.docker.com/_/scratch/) 이미지와 같은 초 미니멀 이미지 또는 패키지 관리자, 셸 또는 OS 없이 애플리케이션과 그 런타임 종속성만 포함하는 "Distroless" 이미지<br>[OCI(Open Container Initiative) 이미지 형식 사양](https://github.com/opencontainers/image-spec/blob/master/spec.md)을 사용하는 이미지|
|필요한 역할 및 권한:|**보안 읽기 권한자** 및 [Azure Container Registry 역할 및 권한](../articles/container-registry/container-registry-roles.md)|
|클라우드:|:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: 상용 클라우드<br>:::image type="icon" source="../articles/security-center/media/icons/yes-icon.png" border="false"::: 미국 정부 및 중국 정부 - 현재 푸시 기능의 검색만 지원됩니다. [이미지가 언제 검사되나요?](../articles/security-center/defender-for-container-registries-introduction.md#when-are-images-scanned)에서 자세히 알아보기|
|||
