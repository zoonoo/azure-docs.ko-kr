---
title: 미사용 데이터의 음성 서비스 암호화
titleSuffix: Azure Cognitive Services
description: 미사용 데이터의 음성 서비스 암호화.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 3ccc9820f38a8c32d0b390663eb6b4430b42e8f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372439"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>미사용 데이터의 음성 서비스 암호화

음성 서비스는 데이터가 클라우드에 유지될 때 자동으로 암호화됩니다. 음성 서비스 암호화는 데이터를 보호하고 조직의 보안 및 규정 준수 약속을 충족하는 데 도움이 됩니다.

## <a name="about-cognitive-services-encryption"></a>코그너티브 서비스 암호화 정보

데이터는 [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) 준수 [256비트 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 암호화를 사용하여 암호화되고 해독됩니다. 암호화 및 암호 해독은 투명하므로 암호화 및 액세스가 관리됩니다. 데이터는 기본적으로 안전하며 암호화를 활용하기 위해 코드 나 응용 프로그램을 수정할 필요가 없습니다.

## <a name="about-encryption-key-management"></a>암호화 키 관리 정보

사용자 지정 음성 및 사용자 지정 음성을 사용하는 경우 음성 서비스는 다음과 같은 데이터를 클라우드에 저장할 수 있습니다.  

* 음성 추적 데이터 - 사용자 지정 끝점에 대한 추적을 켜는 경우에만
* 업로드된 교육 및 테스트 데이터

기본적으로 데이터는 Microsoft 저장소에 저장되며 구독은 Microsoft에서 관리하는 암호화 키를 사용합니다. 자체 저장소 계정을 준비하는 옵션도 있습니다. 저장소에 대한 액세스는 관리되는 ID에 의해 관리되며 음성 서비스는 음성 추적 데이터, 사용자 지정 학습 데이터 및 사용자 지정 모델과 같은 사용자 고유의 데이터에 직접 액세스할 수 없습니다.

관리되는 ID에 대한 자세한 내용은 [관리되는 ID를](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)참조하십시오.

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>사용자 지정 및 로깅을 위해 자체 스토리지(BYOS) 가져오기

자신의 저장소를 가져오기 위해 액세스를 요청하려면 음성 서비스를 작성하고 [제출하십시오 - 자신의 저장소(BYOS) 요청 양식을 지참하십시오.](https://aka.ms/cogsvc-cmk) 승인되면 사용자 지정 및 로깅에 필요한 데이터를 저장하려면 자체 저장소 계정을 만들어야 합니다. 저장소 계정을 추가할 때 음성 서비스 리소스는 관리되는 ID를 할당된 시스템을 사용하도록 설정합니다. 관리ID를 할당받은 시스템이 활성화되면 이 리소스는 AAD(Azure Active Directory)에 등록됩니다. 등록 후 관리되는 ID는 저장소 계정에 대한 액세스 권한이 부여됩니다. 관리되는 ID에 대한 자세한 내용은 여기에서 확인할 수 있습니다. 관리되는 ID에 대한 자세한 내용은 [관리되는 ID를](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)참조하십시오.

> [!IMPORTANT]
> 관리되는 ID를 할당된 시스템을 사용하지 않도록 설정하면 저장소 계정에 대한 액세스가 제거됩니다. 이렇게 하면 저장소 계정에 대한 액세스가 필요한 음성 서비스의 일부가 작동을 중지합니다.  

## <a name="regional-availability"></a>국가별 가용성

BYOS는 현재 다음 리전에서 사용할 수 있습니다.

* 미국 중남부
* 미국 서부 2
* 미국 동부

## <a name="next-steps"></a>다음 단계

* [음성 서비스 - 자신의 스토리지 (BYOS) 요청 양식을 가지고](https://aka.ms/cogsvc-cmk)
* [관리되는 ID는 무엇입니까.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)
