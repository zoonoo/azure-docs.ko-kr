<properties 
    pageTitle="DocumentDB를 사용하여 Python 및 Flask로 웹 앱 작성 | Azure" 
    description="DocumentDB를 사용하여 Azure에 호스팅되는 Python 및 Flask(MVC) 웹 응용 프로그램의 데이터를 저장하고 액세스하는 방법에 대해 알아봅니다." 
    services="documentdb" 
    documentationCenter="" 
    authors="crwilcox" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/12/2015" 
    ms.author="crwilcox"/>

# DocumentDB를 사용하여 Python 및 Flask(MVC)로 웹 응용 프로그램 작성
------------------------------------------------------------------------

고객이 Azure DocumentDB를 효율적으로 활용하여JSON 문서를 저장 및 쿼리할 수 있는 방법을 강조하기 위해 이 문서에서는Azure DocumentDB를 사용하여 투표 웹 응용 프로그램을 작성하는 종합적인 연습을 제공합니다.

이 연습에서는 Azure가 제공하는 DocumentDB 서비스를 사용하여Azure에 호스트된 Python 웹 응용 프로그램에서 데이터를 저장하고 액세스하는방법을 보여 주며 이전에 Python 및 Azure 웹 사이트를 사용한 경험이있다고 가정합니다.

이 연습에서는 다음 내용을 다룹니다.

1. DocumentDB 계정 만들기 및 프로비전

2. Python MVC 응용 프로그램 만들기

3. 웹 응용 프로그램에서 Azure DocumentDB에 연결 및 사용

4. Azure 웹 사이트에 웹 응용 프로그램 배포

이 연습을 수행하면 설문 조사를 위해 투표할 수 있는
간단한 투표 응용 프로그램이 작성됩니다.

![Alt text](./media/documentdb-python-application/image1.png)


## 필수 조건

이 문서의 지침을 따르기 전에 다음이 설치되어 있는지 확인해야 합니다.

- Visual Studio 2013(또는 [Visual Studio Express][](무료 버전))

- Python Tools for Visual Studio([여기][]서 사용 가능)

- Azure SDK for Visual Studio 2013 버전 2.4 이상([여기][1]서 사용 가능)

- Python 2.7([여기][2]서 사용 가능)

- Microsoft Visual C++ Compiler for Python 2.7([여기][3]서 사용 가능)

## 1단계: DocumentDB 데이터베이스 계정 만들기

Azure에서 DocumentDB 데이터베이스 계정을 프로비전하려면 [Azure 관리 포털][]을 열고 홈페이지에서 Azure 갤러리 타일을 클릭하거나 화면 왼쪽 아래에 있는 "+"를 클릭합니다.

![Alt text](./media/documentdb-python-application/image2.png)


Azure 마켓플레이스가 열리며, 여기에서 제공되는 다양한 Azure 서비스 중에 선택할 수 있습니다. 마켓플레이스의 범주 목록에서 "데이터 + 분석"을 선택합니다.

![Alt text](./media/documentdb-python-application/image3.png)

여기에서 documentdb를 검색하고 DocumentDB에 대한 옵션을 선택합니다.

![Alt text](./media/documentdb-python-application/image4.png)

그런 다음 화면 맨 아래에서 "만들기"를 선택합니다.

![Alt text](./media/documentdb-python-application/image5.png)

"새 DocumentDB" 블레이드가 열립니다. 여기서 새 계정의 이름, 지역, 크기, 리소스 그룹 및 기타 설정을 지정할 수 있습니다.

![Alt text](./media/documentdb-python-application/image6.png)

계정에 대한 값을 지정한 후 "만들기"를 클릭하면 프로비저닝 프로세스에서 데이터베이스 계정 만들기가 시작됩니다.
프로비저닝 프로세스가 완료되면 포털의 알림 영역에 알림이 표시되며 시작 화면의 타일(만들도록 선택한 경우)이 변경되어 완료된 작업이 표시됩니다.

![Alt text](./media/documentdb-python-application/image7.png)

프로비저닝이 완료된 후 시작 화면에서 DocumentDB 타일을 클릭하면 새로 만든 이 DocumentDB 계정의 기본 블레이드가 표시됩니다.

![Alt text](./media/documentdb-python-application/image8.png)

"키" 단추를 사용하여 끝점 URL과 기본 키에 액세스한 후 클립보드로 복사한 후에 다음에 만들 웹 응용 프로그램에서 해당 값을사용할 수 있도록 유지합니다.


## 2단계: 새 Python Flask 웹 응용 프로그램 만들기

Visual Studio, 파일 -\> 새 프로젝트 -\> Python -\> 이름이 **tutorial**인 Flask 웹 프로젝트를 엽니다. 

Flask를 처음 사용하는 경우 Flask는 Python에서 웹 응용 프로그램을 더 빨리 작성하는 데 도움이 되는 웹 프레임워크입니다. [Flask 자습서에 액세스하려면 여기를 클릭하세요][].

![Alt text](./media/documentdb-python-application/image9.png)

외부 패키지를 설치할지 여부를 묻습니다. **가상 환경에 설치**를 클릭합니다. PyDocumentDB에서는 현재까지 Python 3.x가 지원되지 않으므로 Python 2.7을 기본 환경으로 사용해야 합니다.  프로젝트에 필요한 Python 가상 환경이 설정됩니다.

![Alt text](./media/documentdb-python-application/image10.png)


## 3단계: Python Flask 웹 응용 프로그램 수정


### 프로젝트에 Flask 패키지 추가

프로젝트가 설정된 후 DocumentDB용 python 패키지인 pydocumentdb를 포함해서 프로젝트에 필요한 특정 flask 패키지를 추가해야 합니다. **requirements.txt** 파일을 열고 내용을 다음으로 바꿉니다.

    flask==0.9
    flask-mail==0.7.6
    sqlalchemy==0.7.9
    flask-sqlalchemy==0.16
    sqlalchemy-migrate==0.7.2
    flask-whooshalchemy==0.55a
    flask-wtf==0.8.4
    pytz==2013b
    flask-babel==0.8
    flup
    pydocumentdb>=0.9.4-preview

**env**를 마우스 오른쪽 단추로 클릭하고 **requirements.txt에서 설치**를 클릭합니다.

![Alt text](./media/documentdb-python-application/image11.png)

**참고:** 드문 경우지만 출력 창에 오류가 표시될 수 있습니다. 이 경우에는 오류가 정리와 관련이 있는지 확인합니다. 일부 경우에는 정리가 실패하더라도 설치가 성공할 수 있습니다(출력 창에서 위로 스크롤하여 확인).
<a name="verify-the-virtual-environment"></a> 이 경우에는 계속해도 됩니다.


### 가상 환경 확인

모두 올바르게 설치되었는지 확인해 봅시다. **F5**를 눌러서 웹 사이트를 시작합니다. 그러면 flask 개발 서버가 실행되고 웹 브라우저가 시작됩니다. 다음 페이지를 참조해야 합니다.

![Alt text](./media/documentdb-python-application/image12.png)

### 데이터베이스, 컬렉션 및 문서 정의 만들기

솔루션 탐색기의 **tutorial** 폴더에서 마우스 오른쪽 단추로 클릭하여 Python 파일을 추가합니다.  파일 이름을 **forms.py**로 지정합니다.  여기에서는 폴링 응용 프로그램을 만드는 중입니다.

    from flask.ext.wtf import Form
    from wtforms import RadioField

    class VoteForm(Form):
        deploy_preference  = RadioField('Deployment Preference', choices=[
            ('Web Site', 'Web Site'),
            ('Cloud Service', 'Cloud Service'),
            ('Virtual Machine', 'Virtual Machine')], default='Web Site')

### 필요한 가져오기를 views.py에 추가합니다.

다음 가져오기 문을 **views.py** 맨 위에 추가합니다. 이렇게 하면 DocumentDB의 PythonSDK 및 Flask 패키지를 가져옵니다.


    from forms import VoteForm
    import config
    import pydocumentdb.document_client as document_client
    

### 데이터베이스, 컬렉션 및 문서 만들기

다음 코드를 **views.py**에 추가합니다. 이 코드는 폼에서 사용되는 데이터베이스를 만듭니다. **views.py**에서 기존 코드는 삭제하지 않습니다. 단순히 끝 부분에 추가합니다.

    @app.route('/create')
    def create():
        """Renders the contact page."""
        client = document_client.DocumentClient(config.DOCUMENTDB_HOST, {'masterKey': config.DOCUMENTDB_KEY})
    
        # Attempt to delete the database.  This allows this to be used to recreate as well as create
        try:
            db = next((data for data in client.ReadDatabases() if data['id'] == config.DOCUMENTDB_DATABASE))
            client.DeleteDatabase(db['_self'])
        except:
            pass
    
        # Create database
        db = client.CreateDatabase({ 'id': config.DOCUMENTDB_DATABASE })
        # Create collection
        collection = client.CreateCollection(db['_self'],{ 'id': config.DOCUMENTDB_COLLECTION })
        # Create document
        document = client.CreateDocument(collection['_self'],
            { 'id': config.DOCUMENTDB_DOCUMENT,
            'Web Site': 0,
            'Cloud Service': 0,
            'Virtual Machine': 0,
            'name': config.DOCUMENTDB_DOCUMENT })
    
        return render_template(
            'create.html', 
            title='Create Page', 
            year=datetime.now().year,
            message='You just created a new database, collection, and document.  Your old votes have been deleted')


### 데이터베이스 및 컬렉션과 문서를 읽고 폼 제출

다음 코드를 **views.py**에 추가합니다. 이 코드는 데이터베이스, 컬렉션 및 문서를 읽고 폼을 설정합니다. **views.py**에서 기존 코드는 삭제하지 않습니다. 단순히 끝 부분에 추가합니다.
    
    @app.route('/vote', methods=['GET', 'POST'])
    def vote(): 
        form = VoteForm()
        replaced_document ={}
        if form.validate_on_submit(): # is user submitted vote  
            client = document_client.DocumentClient(config.DOCUMENTDB_HOST, {'masterKey': config.DOCUMENTDB_KEY})
            
            # Read databases and take first since id should not be duplicated.
            db = next((data for data in client.ReadDatabases() if data['id'] == config.DOCUMENTDB_DATABASE))
            
            # Read collections and take first since id should not be duplicated.
            coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config.DOCUMENTDB_COLLECTION))
    
            # Read documents and take first since id should not be duplicated.
            doc = next((doc for doc in client.ReadDocuments(coll['_self']) if doc['id'] == config.DOCUMENTDB_DOCUMENT))
    
            # Take the data from the deploy_preference and increment our database
            doc[form.deploy_preference.data] = doc[form.deploy_preference.data] + 1
            replaced_document = client.ReplaceDocument(doc['_self'], doc)
    
            # Create a model to pass to results.html
            class VoteObject:
                choices = dict()
                total_votes = 0
    
            vote_object = VoteObject()
            vote_object.choices = {
                "Web Site" : doc['Web Site'],
                "Cloud Service" : doc['Cloud Service'],
                "Virtual Machine" : doc['Virtual Machine']
            }
            vote_object.total_votes = sum(vote_object.choices.values())
    
            return render_template(
                'results.html', 
                year=datetime.now().year, 
                vote_object = vote_object)
    
        else :
            return render_template(
                'vote.html', 
                title = 'Vote',
                year=datetime.now().year,
                form = form)


### html 파일 만들기

templates 폴더 아래에 다음 html 파일을 추가합니다. create.html, results.html, vote.html

**create.html**에 다음 코드를 추가합니다. 이 코드는 새 데이터베이스, 컬렉션 및 문서를 만들었다는 메시지 표시를 처리합니다.

    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}

**results.html**에 다음 코드를 추가합니다. 이 코드는 설문 조사 결과를 표시합니다.

    {% extends "layout.html" %}
    {% block content %}
    <h2>Results of the vote</h2>
    <br />

    {% for choice in vote_object.choices %}
    <div class="row">
        <div class="col-sm-5">{{choice}}</div>
        <div class="col-sm-5">
            <div class="progress">
                <div class="progress-bar" role="progressbar" aria-valuenow="{{vote_object.choices[choice]}}" aria-valuemin="0" 
                     aria-valuemax="{{vote_object.total_votes}}" style="width: {{(vote_object.choices[choice]/vote_object.total_votes)*100}}%;">
                    {{vote_object.choices[choice]}}
                </div>
            </div>
        </div>
    </div>
    {% endfor %}

    <br />
    <a class="btn btn-primary" href="{{ url_for('vote') }}">Vote again?</a>
    {% endblock %}

**vote.html**에 다음 코드를 추가합니다. 이 코드는 설문 조사 표시 및 투표 수락을 처리합니다. 투표를 등록하면 제어가 views.py로 전달되며, 여기서 투표 완료를 인식하고 그에 따라 문서를 추가합니다.

    {% extends "layout.html" %}
    {% block content %}
    <h2>What is your favorite way to host an application on Azure?</h2>
    <form action="" method="post" name="vote">
        {{form.hidden_tag()}}
        {{form.deploy_preference}}
        <button class="btn btn-primary" type="submit">Vote</button>
    </form>
    {% endblock %}

**index.html**의 내용을 다음과 같이 바꿉니다. 이 코드는 응용 프로그램의 방문 페이지 역할을 합니다.

    {% extends "layout.html" %}
    {% block content %}
    <h2>Python + DocumentDB Voting Application.</h2>
    <h3>This is a sample DocumentDB voting application using PyDocumentDB</h3>
    <p><a href="{{ url_for('create') }}" class="btn btn-primary btn-large">Create/Clear the Voting Database &raquo;</a></p>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}


### 구성 파일 추가 및 \_\_init\_\_.py 변경

프로젝트 이름 tutorial을 마우스 오른쪽 단추로 클릭하고 **config.py** 파일을 추가합니다.
이 구성은 Flask의 폼에 필요합니다. 이 파일을 사용하여 비밀 키를 제공할 수도 있습니다. 하지만 이 자습서에서는 이 키가 필요하지 않습니다. config.py에 다음 코드를 추가합니다.   **DOCUMENTDB\_HOST** 및 **DOCUMENTDB\_KEY** 값을 수정합니다.

    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
    
    DOCUMENTDB_HOST = 'https://YOUR_DOCUMENTDB_NAME.documents.azure.com:443/'
    DOCUMENTDB_KEY = 'YOUR_SECRET_KEY_ENDING_IN_=='
    
    DOCUMENTDB_DATABASE = 'voting database'
    DOCUMENTDB_COLLECTION = 'voting collection'
    DOCUMENTDB_DOCUMENT = 'voting document'


마찬가지로 **\_\_init\_\_.py**의 내용을 다음과 같이 바꿉니다.

    from flask import Flask
    app = Flask(__name__)
    app.config.from_object('config')
    import tutorial.views

위에서 언급한 단계를 수행하고 나면 솔루션 탐색기는
다음과 같습니다.

![Alt text](./media/documentdb-python-application/image15.png)


## 4단계: 로컬에서 응용 프로그램 실행

Visual Studio에서 F5 키 또는 실행 단추를 누르면 화면에 다음이 표시됩니다.

![Alt text](./media/documentdb-python-application/image16.png)

"투표 데이터베이스 만들기/지우기"를 클릭해서 데이터베이스를 생성합니다.

![Alt text](./media/documentdb-python-application/image17.png)

그런 후 "투표"를 클릭하고 옵션을 선택합니다.

![Alt text](./media/documentdb-python-application/image18.png)

투표할 때마다 해당 카운터가 증가합니다.

![Alt text](./media/documentdb-python-application/image19.png)


## 5단계: Azure 웹 사이트에 응용 프로그램 배포

이제 완료된 응용 프로그램이 DocumentDB에 대해 올바르게 작동하므로 Azure 웹 사이트에 배포하겠습니다. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고(로컬에서 실행하고 있지 않은지 확인) 게시를 선택합니다.  그런 후 Microsoft Azure 웹 사이트를 선택합니다.

![Alt text](./media/documentdb-python-application/image20.png)


자격 증명을 제공하여 Azure 웹 사이트를 만들고 게시를 클릭합니다.

![Alt text](./media/documentdb-python-application/image21.png)

몇 초 후에 Visual Studio에서 웹 응용 프로그램 게시를 완료하고 브라우저를 시작하며, Azure에서 실행되는 작업 내용을 확인할 수 있습니다.


## 다음 단계

축하합니다. 지금까지 Azure DocumentDB를 사용하여 첫 Python 응용 프로그램을 작성하고 Azure 웹 사이트에 게시했습니다.


  [Flask 자습서에 액세스하려면 여기를 클릭하세요.]: http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world

  [Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
  [여기]: http://aka.ms/ptvs
  [1]: http://go.microsoft.com/fwlink/?linkid=254281&clcid=0x409
  [2]: https://www.python.org/downloads/windows/
  [3]: http://aka.ms/vcpython27 
  [Microsoft 웹 플랫폼 설치 관리자]: http://www.microsoft.com/web/downloads/platform.aspx
  [Azure 관리 포털]: http://portal.azure.com
<!--HONumber=47-->
