---
title: IoT 용 Azure Defender에서 등록 센서 보기 및 관리
description: 이 문서에서는 등록 센서를 확인 하 고 삭제 하는 방법 및 IoT 용 Azure Defender에서 등록 센서에 대 한 새 활성화 파일을 다운로드 하는 방법을 설명 합니다.
author: rkarlin
ms.author: rkarlin
ms.date: 10/10/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 6aec19d413e1730bc1599f6cbac1c62e9b304ecd
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094405"
---
# <a name="view-and-manage-onboarded-sensors"></a>등록 센서 보기 및 관리

이 문서에서는 등록 센서를 확인 하 고 삭제 하는 방법 뿐만 아니라 등록 센서에 대 한 새 활성화 파일을 다운로드 하는 방법을 설명 합니다.

## <a name="update-sensor-management-mode"></a>센서 관리 모드 업데이트

센서가 관리 되는 모드를 업데이트 하는 것이 좋습니다. 이 작업을 수행 하는 경우 센서 관리 페이지에서 현재 센서를 제거 하 고 새 활성화 파일을 업로드 해야 합니다.

- **로컬로 관리 되는 모드 대신 클라우드 관리 모드에서 작업**: 클라우드 관리 센서에 대 한 정품 인증 파일을 사용 하 여 로컬로 관리 되는 센서에 대 한 정품 인증 파일을 업데이트 합니다. 다시 활성화 한 후 센서 검색은 IoT 용 센서 및 Azure Defender 포털에 모두 표시 됩니다. 다시 활성화 파일이 성공적으로 업로드 되 면 새로 검색 된 경고 정보가 Azure로 전송 됩니다.

- **클라우드 관리 모드 대신 로컬로 관리 되는 모드에서 작업**: 로컬로 관리 되는 센서에 대 한 정품 인증 파일을 사용 하 여 클라우드 관리 센서에 대 한 정품 인증 파일을 업데이트 합니다. 다시 활성화 한 후 센서 검색 정보는 센서에만 표시 됩니다.

- **센서를 새 IoT Hub에 연결**: 센서를 새 IoT Hub에 연결 하는 것이 좋습니다. 이렇게 하려면 센서를 다시 등록 하 고 새 활성화 파일을 업로드 합니다.

**센서를 다시 활성화 하려면:**

1. IoT 용 Azure Defender, **센서 관리** 페이지로 이동 합니다.

2. 새 정품 인증 파일을 업로드 하려는 센서를 선택 합니다.

3. 삭제합니다.

4. 새 모드의 **온 보 딩** 페이지 또는 새 IoT Hub에서 센서를 다시 등록 합니다.

5. 활성화 파일 **다운로드** 페이지에서 활성화 파일을 다운로드 합니다.

6. IoT 센서 콘솔용 Azure Defender에 로그인 합니다.

7. 센서 콘솔에서 **시스템 설정** 을 선택 하 고 다시 **활성화**를 선택 합니다.

   ![다시 활성화 보기의 스크린샷](media/updates/image14.png)

8. **업로드** 를 선택 하 고 저장 한 파일을 선택 합니다.

9. **활성화**를 선택합니다.
 
## <a name="delete-sensors"></a>센서 삭제

클라우드 관리 센서를 삭제 하는 경우 정보가 IoT Hub 전송 되지 않습니다.

더 이상 사용 하지 않는 경우 로컬에서 관리 되는 센서를 삭제 합니다.

**센서를 삭제 하려면:**

1. IoT **센서 관리용** Azure Defender 페이지로 이동 합니다.

2. 삭제 하려는 센서를 선택 합니다.

3. **센서 삭제**를 선택 합니다.

## <a name="next-steps"></a>다음 단계

구성 옵션에 대 한 자세한 내용을 보려면 모듈 구성 방법 가이드를 참조 하세요.
> [!div class="nextstepaction"]
> [모듈 구성 방법 가이드](./how-to-agent-configuration.md)
