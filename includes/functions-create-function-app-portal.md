1. Azure Portal의 왼쪽 위에 있는 **새로 만들기** 단추를 클릭합니다.

1. **계산** > **함수 앱**을 클릭하고 **구독**을 선택합니다. 그런 다음 표에 지정된 것처럼 함수 앱 설정을 사용합니다.

    ![Azure Portal에서 함수 앱 만들기](./media/functions-create-function-app-portal/function-app-create-flow.png)

    | 설정      | 제안 값  | 설명                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **앱 이름** | 전역적으로 고유한 이름 | 새 함수 앱을 식별하는 이름입니다. | 
    | **[리소스 그룹](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | 함수 앱을 만들 새 리소스 그룹의 이름입니다. | 
    | **[호스팅 계획](../articles/azure-functions/functions-scale.md)** |   소비 계획 | 함수 앱에 리소스가 할당되는 방법을 정의하는 호스팅 계획입니다. 기본 **소비 계획**에서 함수의 필요에 따라 리소스가 동적으로 추가됩니다. 함수가 실행되는 시간 만큼만 요금을 지불하면 됩니다.   |
    | **위치**: | 서유럽 | 사용자 근처 또는 함수가 액세스할 기타 서비스에 가까운 위치를 선택합니다. |
    | **[저장소 계정](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account)** |  전역적으로 고유한 이름 |  함수 앱에 사용된 새 저장소 계정의 이름. 저장소 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 포함할 수 있습니다. 기존 계정을 사용할 수도 있습니다. |

1. **만들기** 를 클릭하여 새 함수 앱을 프로비전하고 배포합니다.