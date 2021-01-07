---
title: Azure Lab Services를 사용 하 여 MATLAB를 학습 하기 위한 랩 설정 Microsoft Docs
description: Azure Lab Services를 사용 하 여 MATLAB를 학습 하도록 랩을 설정 하는 방법을 알아봅니다.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: 137959f51b08dceee150962f77110ee2ac1dc193
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85445001"
---
# <a name="setup-a-lab-to-teach-matlab"></a>MATLAB를 학습 하기 위한 랩 설정

행렬 실험을 의미 하는 [MATLAB](https://www.mathworks.com/products/matlab.html)는 [MathWorks](https://www.mathworks.com/)의 프로그래밍 플랫폼입니다.  계산 기능과 시각화를 결합 하 여 수학, 엔지니어링, 물리학 및 화학 분야에서 널리 사용 되는 도구를 만듭니다.

[캠퍼스 전체 라이선스](https://www.mathworks.com/academia/tah-support-program/administrators.html)를 사용 하는 경우 [MATLAB 설치 파일 다운로드](https://www.mathworks.com/matlabcentral/answers/259632-how-can-i-get-matlab-installation-files-for-use-on-an-offline-machine) 에서 지침을 참조 하 여 템플릿 컴퓨터에서 MATLAB 설치 관리자 파일을 다운로드 합니다.  

이 문서에서는 라이선스 서버와 함께 MATLAB client 소프트웨어를 사용 하는 클래스를 설정 하는 방법을 보여 줍니다.

## <a name="license-server"></a>라이선스 서버

랩에 대 한 템플릿 컴퓨터를 수정 하기 전에 [네트워크 라이선스 관리자](https://www.mathworks.com/help/install/administer-network-licenses.html) 소프트웨어를 실행 하도록 서버를 설정 해야 합니다.  이러한 지침은 MATLAB에 대 한 네트워킹 라이선스 옵션을 선택 하 여 사용자가 라이선스 키 풀을 공유할 수 있도록 하는 기관에만 적용 됩니다.  또한 나중에 사용 하기 위해 라이선스 파일 및 파일 설치 키를 저장 해야 합니다.  라이선스 파일을 다운로드 하는 방법에 대 한 자세한 지침은 [인터넷 연결을 사용 하 여 네트워크 라이선스 관리자 설치](https://www.mathworks.com/help/install/ug/install-network-license-manager-with-internet-connection.html) 문서의 첫 번째 단계를 참조 하세요.

라이선스 서버를 설치 하는 방법에 대 한 자세한 지침은 [인터넷 연결을 사용 하 여 네트워크 라이선스 관리자 설치](https://www.mathworks.com/help/install/ug/install-network-license-manager-with-internet-connection.html)에서 확인할 수 있습니다.  Borrowing를 사용 하도록 설정 하려면 다음 [라이선스](https://www.mathworks.com/help/install/license/borrow-licenses.html) 문서를 참조 하세요.

라이선스 서버가 Azure 내의 온-프레미스 네트워크 또는 개인 네트워크에 있는 경우 [가상 네트워크](how-to-connect-peer-virtual-network.md) 를 [랩 계정](tutorial-setup-lab-account.md)으로 피어 링 해야 합니다.  랩 가상 머신에서 라이선스 서버에 액세스할 수 있도록 랩을 만들기 전에 네트워크 피어 링을 수행 해야 합니다.

## <a name="lab-configuration"></a>랩 구성

이 랩을 설정하려면 Azure 구독을 시작해야 합니다.  Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Azure 구독을 받은 후 Azure Lab Services에서 새 랩 계정을 만들거나 기존 계정을 사용할 수 있습니다.  새 랩 계정을 만들려면 [랩 계정 설정 자습서](tutorial-setup-lab-account.md)를 참조 하세요.

새 랩을 만들려면 [교실 랩 설정 자습서](tutorial-setup-classroom-lab.md)를 수행 합니다.  다음 설정을 적용 합니다.

| 가상 머신 크기 | 이미지 |
| -------------------- | ----- |
| 중간 | 윈도우 10 |

MATLAB은 더 많은 운영 체제에서 지원 됩니다.  자세한 내용은 [MATLAB 시스템 요구 사항](https://www.mathworks.com/support/requirements/matlab-system-requirements.html) 을 참조 하세요.

> [!WARNING]
> 랩을 만들기 전에 랩 계정에 대 한 [가상 네트워크](https://www.mathworks.com/support/requirements/matlab-system-requirements.html) 를 라이선스 서버의 가상 네트워크에 피어 링 해야 합니다.

## <a name="template-machine"></a>템플릿 컴퓨터

템플릿 컴퓨터를 만든 후 컴퓨터를 시작 하 고 연결 하 여 다음과 같은 주요 작업을 완료 합니다.

1. MATLAB 클라이언트 소프트웨어에 대 한 설치 파일을 다운로드 합니다.
2. 파일 설치 키를 사용 하 여 MATLAB를 설치 합니다.

MATLAB를 설치 하는 과정은 여러 부분으로 구성 됩니다.  첫 번째 단계에서는 MATLAB 및 설치 하려는 다른 모든 제품에 대 한 파일을 다운로드 합니다.  파일 설치 키를 사용 하려면 설치할 제품의 모든 설치 파일이 미리 다운로드 되어 있어야 합니다.  두 번째 부분은 템플릿 VM에 MATLAB 소프트웨어를 설치 하 고 소프트웨어를 활성화 하는 것입니다.  템플릿 VM이 라이선스 서버를 사용 하 여 활성화 하도록 구성 된 경우 학생 Vm은 동일한 작업을 수행 합니다.

### <a name="download-installation-files"></a>설치 파일 다운로드

설치 파일을 다운로드 하 고 라이선스 파일 및 파일 설치 키를 가져오려면 라이선스 관리자 여야 합니다.  설치 파일을 다운로드 하는 단계는 다음과 같습니다.

1. 에 대 한 계정에 로그인 [https://www.mathworks.com](https://www.mathworks.com) 합니다.
2. **내 계정**을 선택 합니다.
3. 계정 페이지의 **내 소프트웨어** 섹션에서 랩에 대 한 네트워크 라이선스 관리자 설정에 연결 된 라이선스를 클릭 합니다.
4. 라이선스 정보 페이지에서 **제품 다운로드**를 클릭 합니다.
5. 설치 관리자가 자동으로 추출 될 때까지 기다립니다.
6. 설치 관리자를 시작합니다.  
7. **MathWorks 계정에 로그인** 페이지에서 MathWorks 계정을 입력 합니다.
8. **MathWorks 사용권 계약** 페이지에서 약관에 동의 하 고 **다음** 단추를 클릭 합니다.
9. **고급 옵션** 드롭다운을 클릭 하 고를 **설치 하지 않고 다운로드를**선택 합니다.
10. **대상 선택 폴더**에서 **다음**을 클릭 합니다.
11. MATLAB을 설치할 컴퓨터의 플랫폼으로 **Windows** 를 선택 합니다.
12. **제품 선택** 페이지에서 설치 하려는 다른 MathWorks 제품과 함께 MATLAB이 선택 되어 있는지 확인 합니다.
13. **선택 항목 및 다운로드 확인** 페이지에서 **다운로드 시작**을 클릭 합니다.  
14. 선택한 제품이 다운로드 될 때까지 기다립니다.  **Finish**를 클릭합니다.

MathWorks 웹 사이트에서 ISO 이미지를 다운로드할 수도 있습니다.

1. 에 대 한 계정에 로그인 [https://www.mathworks.com](https://www.mathworks.com) 합니다.
2. [https://www.mathworks.com/downloads](https://www.mathworks.com/downloads)으로 이동합니다.
3. 설치 하려는 MATLAB의 릴리스를 선택 합니다.
4. 관련 링크 아래의 "Get {version} .iso image" 링크를 클릭 합니다. 여기서 {version}은 R2020a와 같습니다.
5. Windows 용 blue **다운로드 릴리스** 링크를 클릭 합니다.

### <a name="run-installer"></a>설치 관리자 실행

파일이 다운로드 되 면 두 번째 단계는 설치 관리자를 실행 하는 것입니다. 이 단계를 완료 하려면 라이선스 관리자 여야 합니다.  라이선스 관리자만 파일 설치 키로 MATLAB를 설치할 수 있습니다.

1. 다운로드 한 라이선스 파일을 확인 하 고 서버 줄에 라이선스 서버가 올바르게 나열 되는지 확인 합니다.  라이선스 파일의 형식을 지정 하는 방법에 대 한 자세한 내용은 [네트워크 라이선스 업데이트](https://www.mathworks.com/help/install/ug/network-license-files.html), [라이선스 BORROWING](https://www.mathworks.com/help/install/license/borrow-licenses.html)및 [호스트 ID 찾기](https://www.mathworks.com/matlabcentral/answers/101892-what-is-a-host-id-how-do-i-find-my-host-id-in-order-to-activate-my-license) 문서를 참조 하세요.
2. MATLAB 설치 관리자를 시작 합니다.
3. **MathWorks 계정에 로그인** 페이지에서 MathWorks 계정을 입력 합니다.
4. **MathWorks 사용권 계약** 페이지에서 약관에 동의 하 고 **다음** 단추를 클릭 합니다.
5. **고급 옵션** 드롭다운을 클릭 하 고 **파일 설치 키를**선택 합니다.
6. **파일 설치 키를 사용 하 여 설치** 페이지에서 라이선스 서버의 파일 설치 키를 입력 합니다.   **다음**을 클릭합니다.
7. **라이선스 파일 선택** 페이지에서 이전에 설치 파일을 다운로드할 때 저장 된 라이선스 파일로 이동 합니다.
8. **대상 폴더 선택** 페이지에서 **다음**을 클릭 합니다.
9. **제품 선택** 페이지에서 **다음**을 클릭 합니다.
10. **옵션 선택** 페이지에서 **다음**을 클릭 합니다.
11. **선택 항목 및 설치 확인** 페이지에서 **설치 시작**을 클릭 합니다.
12. **설치 완료** 페이지에서 **MATLAB 활성화** 가 선택 되어 있는지 확인 합니다.  **Finish**를 클릭합니다.

## <a name="cost-estimate"></a>예상 비용

이 클래스에 대해 가능한 예상 비용을 살펴보겠습니다.  이 예상치는 라이선스 서버를 실행 하는 비용을 포함 하지 않습니다.  25 명의 학생 클래스를 사용 합니다.  예약 된 클래스 시간은 20 시간입니다.  또한 각 학생은 예약 된 클래스 시간 외에도 과제 또는 배정에 대해 10 시간 할당량을 얻습니다.  선택한 가상 컴퓨터 크기는 55 lab 단위인 medium입니다.

이 클래스에 대 한 예상 비용 예상 예는 다음과 같습니다.

25 개의 학생 \* (20 개의 예약 된 시간 + 10 할당량 시간) \* 55 lab 단위 \*  0.01 usd/시간 = 412.50 USD

>[!IMPORTANT]
> 비용 예측은 예를 들어 목적 으로만 사용 됩니다.  가격 책정에 대 한 최신 정보는 [Azure Lab Services 가격 책정](https://azure.microsoft.com/pricing/details/lab-services/)을 참조 하세요.  

## <a name="next-steps"></a>다음 단계

다음 단계는 랩을 설정 하는 데 일반적입니다.

- [템플릿 만들기, 관리 및 게시](how-to-create-manage-template.md)
- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [학생에 대 한 전자 메일 등록 링크](how-to-configure-student-usage.md#send-invitations-to-users)
