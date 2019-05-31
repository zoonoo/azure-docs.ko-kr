---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: d965b89659a9e3dc01035221a729f094c336eb5b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244568"
---
| 엔터티                                       | 규칙   |
|----------------------------------------------|-----------------------------------------------------|
| 블록 Blob 및 페이지 Blob에 대한 컨테이너 이름 | 올바른 DNS 이름은 3~63자여야 합니다. <br>  문자 또는 숫자로 시작해야 합니다. <br> 소문자, 숫자 및 하이픈(-)만 포함할 수 있습니다. <br> 모든 하이픈(-)은 앞뒤에 문자 또는 숫자가 와야 합니다. <br> 이름에 하이픈을 연속으로 허용 되지 않습니다. |
| Azure 파일에 대한 공유 이름                  | 위와 동일                                          |
| Azure 파일에 대한 디렉터리와 파일 이름     |<li> 대/소문자 구분, 대/소문자를 구분하지 않으며 길이가 255자를 초과할 수 없습니다. </li><li> 슬래시(/)로 끝낼 수 없습니다. </li><li>사용된 경우 자동으로 제거됩니다. </li><li> 다음 문자는 허용 되지 않습니다. <code>" \\ / : \| < > * ?</code></li><li> 예약된 URL 문자는 적절히 이스케이프되어야 합니다. </li><li> 잘못 된 URL 경로 문자는 허용 되지 않습니다. 과 같은 코드 포인트 \\uE000 유효한 유니코드 문자가 없습니다. 일부 ASCII 또는 유니코드 문자 같은 제어 문자 ((0x00-0x1f, \\u0081, 등), 허용 되지 않습니다. HTTP/1.1에서 유니코드 문자열을 제어하는 규칙은 RFC 2616, 섹션 2.2: 기본 규칙 및 RFC 3987을 참조하세요. </li><li> 다음 파일 이름은 허용 되지 않습니다. LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, 점 문자(.) 및 2개 점 문자(..)</li>|
| 블록 Blob 및 페이지 Blob에 대한 Blob 이름      | </li><li>Blob 이름은 대/소문자를 구분하며 문자 조합을 포함할 수 있습니다. </li><li>Blob 이름은 길이가 1~1,024자 사이여야 합니다. </li><li>예약된 URL 문자는 적절히 이스케이프되어야 합니다. </li><li>Blob 이름을 구성하는 경로 세그먼트 수는 254개를 초과할 수 없습니다. 경로 세그먼트는 가상 디렉터리 이름에 해당하는 연속 구분 기호 문자 사이의 문자열입니다(예를 들어 슬래시 '/').</li> |
