---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/19/2021
ms.author: alkohli
ms.openlocfilehash: aef81c1ad80d29df05ab8e8c5f27b8827d7993f7
ms.sourcegitcommit: eda26a142f1d3b5a9253176e16b5cbaefe3e31b3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109805157"
---
|    URL 패턴 |    구성 요소 또는 기능  |
|-----------------------------------------------------|-----------------------------------------|
|    https://\*.databoxedge.azure.com/\*<br>https://\*.servicebus.windows.net/\*<br>https://login.microsoftonline.com |    Azure Stack Edge 서비스<br>Azure Service Bus<br>인증 서비스 - Azure Active Directory                           |
|    http:\//crl.microsoft.com/pki/\*<br>http:\//www.microsoft.com/pki/\*                                                                                                                                                                                                                                                                                                                                                                                                  |    인증서 해지                                                                               |
|    https://\*.core.windows.net/\*<br>https://\*.data.microsoft.com<br>http://\*.msftncsi.com                                                                                                                                                                                                                                                                                                                                                                            |    Azure Storage 계정 및 모니터링                                                                |
|    http:\//windowsupdate.microsoft.com<br>http://\*.windowsupdate.microsoft.com<br>https://\*.windowsupdate.microsoft.com<br>http://\*.update.microsoft.com<br>https://\*.update.microsoft.com<br>http://\*.windowsupdate.com<br>http://download.microsoft.com<br>http://\*.download.windowsupdate.com<br>http://wustat.windows.com<br>http://ntservicepack.microsoft.com<br>http://\*.ws.microsoft.com<br>https://\*.ws.microsoft.com<br>http://\*.mp.microsoft.com |    Microsoft 업데이트 서버                                                                             |
|    http://\*.deploy.akamaitechnologies.com                                                                                                                                                                                                                                                                                                                                                                                                                          |    Akamai CDN                                                                                           |
|    http://\*.data.microsoft.com     |    Windows의 원격 분석 서비스는 사용자 환경 및 진단 원격 분석 업데이트를 참조하세요. |
<!--|    http://www.msftconnecttest.com/connecttest.txt  |    진단의 경우     ||  |-->   

