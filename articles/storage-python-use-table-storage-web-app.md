<properties linkid="develop-python-web-app-with-blob-storage" urlDisplayName="Web App with Blob Storage" pageTitle="Python web app with table storage | Microsoft Azure" metaKeywords="Azure table storage Python, Azure Python application, Azure Python tutorial, Azure Python example" description="A tutorial that teaches you how to create a Python web application using the Azure Client Libraries. Django is used as the web framework." metaCanonical="" services="storage" documentationCenter="Python" title="Python Web Application using Table Storage" authors="" solutions="" videoId="" scriptId="" manager="" editor="mollybos" />

테이블 저장소를 사용하는 Python 웹 응용 프로그램
================================================

이 자습서에서는 Python용 Azure 클라이언트 라이브러리로 테이블 저장소를 사용하는 응용 프로그램을 만드는 방법에 대해 알아봅니다. 이 응용 프로그램이 첫 번째 Python Azure 앱인 경우 먼저 [Django Hello World 웹 응용 프로그램](http://windowsazure.com/ko-kr/documentation/articles/virtual-machines-python-django-web-app-windows-server)을 살펴보는 게 좋습니다.

이 가이드의 경우 Azure에 배포할 수 있는 웹 기반 작업 목록 응용 프로그램을 만듭니다. 작업 목록을 통해 사용자는 작업을 가져오고 새 작업을 추가하고 작업을 완료로 표시할 수 있습니다. 웹 프레임워크로 Django를 사용합니다.

작업 항목은 Azure 저장소에 저장됩니다. Azure 저장소는 내결함성과 고가용성이 있는 구조화되지 않은 데이터 저장소를 제공합니다. Azure 저장소에는 데이터를 저장하고 액세스할 수 있는 일부 데이터 구조가 포함되며, Python용 Azure SDK에 포함된 API 또는 REST API를 통해 저장소 서비스를 활용할 수 있습니다. 자세한 내용은 [Azure에 데이터 저장 및 액세스](http://msdn.microsoft.com/ko-kr/library/windowsazure/gg433040.aspx)를 참조하십시오.

다음 내용을 배웁니다.

-   Azure 테이블 저장소 서비스에 대한 작업 방법

완성된 응용 프로그램의 스크린샷은 아래 스크린샷과 유사합니다(추가된 작업 항목은 다름).

![](./media/storage-python-use-table-storage-web-app/web-app-with-storage-Finaloutput-mac.png)

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

개발 환경 설정
--------------

**참고:** Python 또는 클라이언트 라이브러리를 설치해야 하는 경우 [Python 설치 가이드](http://windowsazure.com/ko-kr/documentation/articles/python-how-to-install)(영문)를 참조하십시오.

*Windows의 경우*: Windows WebPI 설치 관리자를 사용했으면 이미 Django와 Client Libs가 설치되어 있습니다.

Azure에서 저장소 계정 만들기
----------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Django 프로젝트 만들기
----------------------

다음은 앱을 만드는 단계입니다.

-   'TableserviceSample'이라는 이름의 기본 Django 프로젝트를 만듭니다.
-   명령줄에서 코드를 저장할 디렉터리에 cd를 입력한 후 다음 명령을 실행합니다.

        django-admin.py startproject TableserviceSample

-   새 Python 파일인 **views.py**를 프로젝트에 추가합니다.
-   다음 코드를 **views.py**에 추가하여 필요한 Django 지원을 가져옵니다.

         from django.http import HttpResponse
        from django.template.loader import render_to_string
        from django.template import Context

-   **TableserviceSample/TableserviceSample** 폴더 아래에 **templates**라는 폴더를 새로 만듭니다.
-   템플릿을 찾을 수 있도록 응용 프로그램 설정을 편집합니다. **settings.py**를 열고 다음 항목을 INSTALLED\_APPS에 추가합니다.

        'TableserviceSample',

-   새 Django 템플릿 파일인 **mytasks.html**을 **templates** 폴더에 추가하고 다음 코드를 추가합니다.

``` {}
    <html>
    <head><title></title></head>
    <body>
    <h2>My Tasks</h2> <br>
    <table border="1"> 
    <tr>
    <td>Name</td><td>Category</td><td>Date</td><td>Complete</td><td>Action</td></tr>
    {% for entity in entities %}
    <form action="update_task" method="GET">
    <tr><td>{{entity.name}} <input type="hidden" name='name' value="{{entity.name}}"></td>
    <td>{{entity.category}} <input type="hidden" name='category' value="{{entity.category}}"></td>
    <td>{{entity.date}} <input type="hidden" name='date' value="{{entity.date}}"></td>
    <td>{{entity.complete}} <input type="hidden" name='complete' value="{{entity.complete}}"></td>

    <td><input type="submit" value="Complete"></td>
    </tr>
    </form>
    {% endfor %}
    </table>
    <br>
    <hr>
    <table border="1">
    <form action="add_task" method="GET">
    <tr><td>Name:</td><td><input type="text" name="name"></input></td></tr>
    <tr><td>Category:</td><td><input type="text" name="category"></input></td></tr>
    <tr><td>Item Date:</td><td><input type="text" name="date"></input></td></tr>
    <tr><td><input type="submit" value="add task"></input></td></tr>
    </form>
    </table>
    </body>
    </html>    
```

windowsazure 저장소 모듈 가져오기
---------------------------------

Django를 가져온 직후 **views.py**의 맨 위에 다음 코드를 추가합니다.

    from azure.storage import TableService

저장소 계정 이름 및 계정 키 가져오기
------------------------------------

windowsazure를 가져온 직후 다음 코드를 **views.py**에 추가하고 'youraccount' 및 'yourkey'를 실제 계정 이름과 키로 바꿉니다. Azure 관리 포털에서 계정 이름과 키를 가져올 수 있습니다.

    account_name = 'youraccount'
    account_key = 'yourkey'

TableService 만들기
-------------------

**account\_name** 뒤에 다음 코드를 추가합니다.

    table_service = TableService(account_name=account_name, account_key=account_key)
    table_service.create_table('mytasks')

작업 나열
---------

**views.py**에 list\_tasks 함수를 추가합니다.

    def list_tasks(request): 
        entities = table_service.query_entities('mytasks', '', 'name,category,date,complete')    
        html = render_to_string('mytasks.html', Context({'entities':entities}))
        return HttpResponse(html)

작업 추가
---------

**views.py**에 add\_task 함수를 추가합니다.

    def add_task(request):
        name = request.GET['name']
        category = request.GET['category']
        date = request.GET['date']
        table_service.insert_entity('mytasks', {'PartitionKey':name+category, 'RowKey':date, 'name':name, 'category':category, 'date':date, 'complete':'No'}) 
        entities = table_service.query_entities('mytasks', '', 'name,category,date,complete')
        html = render_to_string('mytasks.html', Context({'entities':entities}))
        return HttpResponse(html)

작업 상태 업데이트
------------------

**views.py**에 update\_task 함수를 추가합니다.

    def update_task(request):
        name = request.GET['name']
        category = request.GET['category']
        date = request.GET['date']
        partition_key = name + category
        row_key = date
        table_service.update_entity('mytasks', partition_key, row_key, {'PartitionKey':partition_key, 'RowKey':row_key, 'name': name, 'category':category, 'date':date, 'complete':'Yes'})
        entities = table_service.query_entities('mytasks', '', 'name,category,date,complete')    
        html = render_to_string('mytasks.html', Context({'entities':entities}))
        return HttpResponse(html)

URL 매핑
--------

이제 Django 앱에서 URL을 매핑해야 합니다. **urls.py**를 열고 다음 매핑을 urlpatterns에 추가합니다.

    url(r'^$', 'TableserviceSample.views.list_tasks'),
    url(r'^list_tasks$', 'TableserviceSample.views.list_tasks'),
    url(r'^add_task$', 'TableserviceSample.views.add_task'),
    url(r'^update_task$', 'TableserviceSample.views.update_task'),

응용 프로그램 실행
------------------

-   **TableserviceSample** 디렉터리로 전환하지 않았으면 전환하고 다음 명령을 실행합니다.

    python manage.py runserver

-   브라우저에서 `http://127.0.0.1:8000/`을 가리키고 8000을 실제 포트 번호로 바꿉니다.

이제 **작업 추가**를 클릭하여 작업을 만든 다음 **완료** 단추를 클릭하여 작업을 업데이트하고 작업 상태를 예로 설정합니다.

계산 에뮬레이터에서 응용 프로그램 실행, 응용 프로그램 게시 및 중지/삭제
-----------------------------------------------------------------------

기본 제공된 Django 서버에서 앱을 성공적으로 실행했으면 Azure 에뮬레이터(Windows에만 해당)에 배포한 다음 Azure에 게시하여 좀 더 테스트할 수 있습니다. 이 작업을 수행하는 방법에 대한 일반적인 지침은 이러한 단계를 자세히 설명하는 [Django Hello World 웹 응용 프로그램](http://windowsazure.com/ko-kr/documentation/articles/virtual-machines-python-django-web-app-windows-server) 문서를 참조하십시오.

다음 단계
---------

이제 Azure 테이블 저장소 서비스의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 저장소 작업을 수행하는 방법을 알아보십시오.

-   다음 MSDN 참조를 확인하십시오. [Azure에서 데이터 저장 및 액세스](http://msdn.microsoft.com/ko-kr/library/windowsazure/gg433040.aspx)
-   다음 Azure 저장소 팀 블로그(영문)를 방문하십시오. http://blogs.msdn.com/b/windowsazurestorage/

