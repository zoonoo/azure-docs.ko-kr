---
title: "Azure Government 계산 | Microsoft 문서"
description: "Azure Government용 응용 프로그램 개발에 대한 지침 및 기능 비교를 제공합니다."
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: fb11f60c-5a70-46a9-82a0-abb2a4f4239b
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 3/13/2017
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 146fcd9788bab55e4abd9e70dcfe25741f041d34
ms.lasthandoff: 03/15/2017


---
# <a name="azure-government-compute"></a>Azure 정부 계산
## <a name="virtual-machines"></a>가상 컴퓨터
이 서비스와 사용 방법에 대한 자세한 정보는 [Azure Virtual Machines 크기](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

### <a name="variations"></a>변형
제품(VM)는 Azure Government에서 사용할 수 있습니다.

| 제품(VM) | 미국 정부 VA | 미국 정부 IA | US DOD 동부 | US DOD 서부
| --- | --- | --- |--- |--- |
| A0-A7 |Y |Y |Y |Y |Y |
| Av2 |Y |Y |Y |Y |Y |
| D 시리즈 |Y |N |N |N |
| Dv2 시리즈 |Y |Y |Y |Y |
| DS 시리즈 |Y |N |N |N |
| DSv2 시리즈 |Y |N |Y |Y |
| F 시리즈 |Y |Y |Y |Y |
| FS 시리즈 |Y |N |Y |Y |
| G 시리즈 |Y |N |N |N |
| GS 시리즈 |Y |N |N |N |

### <a name="data-considerations"></a>데이터 고려 사항
다음 정보는 Azure 가상 컴퓨터에 대한 Azure Government 경계를 식별합니다.

| 규제된/제어된 데이터 허용됨 | 규제된/제어된 데이터 허용되지 않음 |
| --- | --- |
| VM 내에서 입력, 저장 및 처리된 데이터는 내보내기 제어된 데이터를 포함할 수 있습니다. Azure 가상 컴퓨터 내에서 실행되는 이진 파일. Azure 플랫폼 구성 요소에 대한 액세스를 위한 암호 및 스마트 카드 PIN과 같은 정적 인증자. Azure 플랫폼 구성 요소를 관리하는 데 사용되는 인증서의 개인 키. SQL 연결 문자열.  Azure 서비스에 저장된 인증서, 암호화 키, 마스터 키 및 저장소 키와 같은 기타 보안 정보/암호. |메타데이터는 내보내기 제어된 데이터를 포함하도록 허용되지 않습니다. 이 메타데이터는 Azure 가상 컴퓨터를 생성 및 유지 관리하는 경우 입력된 모든 구성 데이터를 포함합니다.  다음 필드, 테넌트 역할 이름, 리소스 그룹, 배포 이름, 리소스 이름, 리소스 태그에 규제/제어된 데이터를 입력하지 마세요. |

## <a name="next-steps"></a>다음 단계
추가 정보 및 업데이트는 <a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government 블로그</a>를 구독하세요.


