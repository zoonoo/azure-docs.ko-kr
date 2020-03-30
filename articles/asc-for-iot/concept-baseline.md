---
title: IoT 기준을 위한 Azure 보안 센터 이해 | 마이크로 소프트 문서
description: IoT 기준을 위한 Azure 보안 센터의 개념에 대해 알아봅니다.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2019
ms.author: mlottner
ms.openlocfilehash: ce5a0625a16c5a02d03ee74f894c585820414fa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72176611"
---
# <a name="azure-security-center-for-iot-baseline-and-custom-checks"></a>IoT 기준 및 사용자 지정 검사를 위한 Azure 보안 센터

이 문서에서는 IoT에 대한 Azure 보안 센터에 대해 설명하고 기준 사용자 지정 검사의 모든 관련 속성을 요약합니다.

## <a name="baseline"></a>기초

기준은 각 장치에 대한 표준 동작을 설정하고 예상 된 규범에서 비정상적인 동작 또는 편차를 쉽게 설정할 수 있습니다.  

## <a name="baseline-custom-checks"></a>기준선 사용자 지정 검사

기준 사용자 지정 검사는 장치의 **Module ID 쌍선을** 사용하여 각 장치 베이스기준에 대한 사용자 지정 검사 목록을 설정합니다. 

## <a name="setting-baseline-properties"></a>기준선 속성 설정

1. IoT Hub에서 변경하려는 장치를 찾아 선택합니다.
1. 장치를 클릭한 다음 **azureiot보안** 모듈을 클릭합니다.
1. **모듈 ID 트윈을 클릭합니다.**
1. **베이스라인 사용자 지정 검사** 파일을 장치에 업로드합니다.
1. 보안 모듈에 기준 속성을 추가하고 **저장을**클릭합니다.

### <a name="baseline-custom-check-file-example"></a>기준선 사용자 지정 검사 파일 예제

기준 사용자 지정 검사를 구성하려면 다음을 수행하십시오.

   ```json
    "desired": {
      "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
        "baselineCustomChecksEnabled": {
          "value" : true
        },
        "baselineCustomChecksFilePath": {
          "value" : "/home/user/full_path.xml"
        },
        "baselineCustomChecksFileHash": {
          "value" : "#hashexample!"
        }
      }
    },
   ```

## <a name="baseline-custom-check-properties"></a>기준선 사용자 지정 검사 속성

| 이름| 상태 | 유효한 값| 기본값| 설명 |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|기준선사용자정의검사사용 가능|필수: true |유효한 값: **부울** |기본값: **false** |우선 순위가 높은 메시지를 보내기 전의 최대 시간 간격입니다.|
|기준선사용자 정의 검사파일 경로 |필수: true|유효한 값: **문자열,** **null** |기본값: **null** |기준선 xml 구성의 전체 경로|
|기준선사용자 정의 검사파일 해시 |필수: true|유효한 값: **문자열,** **null** |기본값: **null** |`sha256sum`xml 구성 파일입니다. 자세한 내용은 [sha256sum 참조를](https://linux.die.net/man/1/sha256sum) 사용합니다. |

추가 기준 예제를 검토하려면 [사용자 지정 기준 선 -1](https://ascforiot.blob.core.windows.net/public/custom_baseline_example_hyperv_ubuntu1804.xml) 및 사용자 지정 기준 선 [-2를](https://ascforiot.blob.core.windows.net/public/oms_audits.xml)참조하십시오.

## <a name="next-steps"></a>다음 단계

- 원시 [보안 데이터에](how-to-security-data-access.md) 액세스
- [디바이스 조사](how-to-investigate-device.md)
- [보안 권장 사항](concept-recommendations.md) 이해 및 탐색
- [보안 경고](concept-security-alerts.md) 이해 및 탐색
