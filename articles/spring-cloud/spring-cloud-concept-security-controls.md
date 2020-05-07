---
title: 개념-Azure 스프링 클라우드 서비스에 대 한 보안 제어
description: Azure 스프링 클라우드 서비스에 기본 제공 되는 보안 제어를 사용 합니다.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: 5b459ef57d0e8a22ce1cd53f56c44d31e53c7c93
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594987"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Azure Spring Cloud Service의 보안 컨트롤
보안 제어는 Azure 스프링 클라우드 서비스에 기본 제공 됩니다.

보안 제어는 서비스에서 보안 취약성을 예방, 감지 및 대응 하는 기능을 제공 하는 Azure 서비스의 품질 또는 기능입니다.  각 컨트롤에 대해 *예* 또는 *아니요* 를 사용 하 여 서비스에 대 한 현재 준비 여부를 나타냅니다.  서비스에 적용 되지 않는 컨트롤에는 *N/A* 를 사용 합니다. 

**데이터 보호 보안 제어**

| 보안 제어 | 예/아니요 | 참고 | 문서화 |
|:-------------|:-------|:-------------------------------|:----------------------|
| 미사용 서버 쪽 암호화: Microsoft 관리 키 | 예 | 사용자가 업로드 한 원본 및 아티팩트, 구성 서버 설정, 앱 설정 및 영구 저장소의 데이터는 Azure Storage에 저장 되며, 미사용 콘텐츠를 자동으로 암호화 합니다.<br><br>구성 서버 캐시, 업로드 된 원본에서 빌드된 런타임 이진 파일 및 응용 프로그램 수명 중에 응용 프로그램 로그는 미사용 콘텐츠를 자동으로 암호화 하는 Azure 관리 디스크에 저장 됩니다.<br><br>사용자가 업로드 한 원본에서 빌드된 컨테이너 이미지는 미사용 이미지 콘텐츠를 자동으로 암호화 하는 Azure Container Registry에 저장 됩니다. | [미사용 데이터에 대한 Azure Storage 암호화](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)<br><br>[Azure managed disks의 서버 쪽 암호화](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption)<br><br>[Azure Container Registry의 컨테이너 이미지 스토리지](https://docs.microsoft.com/azure/container-registry/container-registry-storage) |
| 일시적인 암호화 | 예 | 사용자 앱 공용 끝점은 기본적으로 인바운드 트래픽에 대해 HTTPS를 사용 합니다. |  |
| API 호출 암호화 | 예 | Azure 스프링 클라우드 서비스를 구성 하기 위한 관리 호출은 HTTPS를 통한 Azure Resource Manager 호출을 통해 수행 됩니다. | [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) |

