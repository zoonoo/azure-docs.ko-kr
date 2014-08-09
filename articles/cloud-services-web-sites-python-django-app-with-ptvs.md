<properties  linkid="develop-python-tutorials-django-with-python-tools-for-visual-studio" urlDisplayName="Django with Python Tools for Visual Studio 2.0" pageTitle="Creating Django applications with Python Tools for Visual Studio 2.0" metaKeywords="" description="Learn how to use the Python Tools for Visual Studio to create a Django application that stores data in a SQL Database or MySQL database instance and can be deployed to a web site or cloud service." metaCanonical="" services="web-sites,cloud-services" documentationCenter="Python" title="Creating Django applications with Python Tools 2.0 for Visual Studio" authors="" solutions="" manager="" editor="" />

# Python Tools 2.0 for Visual Studio를 사용하여 Django 응용 프로그램 만들기

<div  class="dev-onpage-video-clear clearfix">
<div  class="dev-onpage-left-content" markdown="1">
이 자습서에서는 Python Tools 2.0 for Visual Studio를 사용하여 간단한 Django 응용 프로그램을
만들어봅니다. 응용 프로그램에서 설문 조사를 투표할 수 있을 것입니다. 먼저 로컬 sqlite3 데이터베이스를 사용한 다음 SQL
Server 또는 Azure의 MySQL 데이터베이스로 이동합니다. Django 관리 인터페이스를 활성화하고 데이터베이스에 설문
조사를 추가하는 방법을 보여 줄 것입니다. 또한 Visual Studio에 통합된 Django 셸을 사용할 것입니다. 마지막으로
Azure 웹 사이트 및 Azure 클라우드 서비스에 응용 프로그램을 배포할 것입니다.


<p>동영상을 시청하려는 경우 오른쪽에 있는 클립은 이 자습서와 동일한 단계를 따릅니다.</p>


</div>
<div class="dev-onpage-video-wrapper"><a href="http://www.youtube.com/watch?v=wkqjafvvU5w" target="_blank" class="label">자습서 보기</a> <a style="background-image: url('/media/devcenter/python/videos/django-tutorial-180x120.png') !important;" href="http://www.youtube.com/watch?v=wkqjafvvU5w" target="_blank" class="dev-onpage-video"><span class="icon">동영상 재생</span></a> <span class="time">33:08</span></div>
</div>

이 자습서는 Python Tools for Visual Studio 및 Azure를 중점적으로 다룹니다. Django 및 이
자습서에서 빌드한 설문 조사 응용 프로그램에 대한 자세한 내용은
[https://www.djangoproject.com/][2](영문)을 참조하십시오.

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## 요구 사항

자습서를 완료하려면 다음이 필요합니다.

* [Python Tools 2.0 for Visual Studio][3]
* [Python 2.7(32비트)][4]
* Visual Studio 및 Azure SDK:
  * VS 2010 Pro 이상 및 Azure SDK 2.1
  * VS 2012 Pro 이상 및 Azure SDK 2.1, 2.2 이상
  * VS 2013 Pro 이상 및 Azure SDK 2.2 이상
  * 무료 VS 2013 통합 셸. **Azure SDK는 통합 셸을 지원하지 않습니다**. 무료 통합 셸을 사용하여
    Django 응용 프로그램을 로컬에서 개발, 디버그 및 실행할 수 있지만, Visual Studio를 사용하여 Azure
    웹 사이트 또는 클라우드 서비스에 게시할 수 없습니다.

웹 플랫폼 설치 관리자를 사용하여 Azure SDK를 설치합니다. 그러면 SDK, 에뮬레이터 및 Azure Tools for
Visual Studio가 설치됩니다. 웹 플랫폼 설치 관리자에서 **Azure SDK for .NET**을 검색한 다음 해당 Visual Studio 버전에 대해 지원되는 SDK 버전을 선택합니다.

**참고:** Azure 웹 사이트 또는 클라우드 서비스에서 이 응용 프로그램을 성공적으로 실행하기 위해
[python.org][4](영문)에 공식 배포된 CPython 2.7을 사용해야 합니다. 다른 배포판도 작동할 수 있지만
공식적으로 지원되지 않습니다.

## 기존 프로젝트 다운로드

자습서를 건너뛰고 싶은 경우 [이 프로젝트의 소스 코드를 다운로드][5]할 수 있습니다.

superuser에 대해 다음 자격 증명을 사용하여 sqlite3 데이터베이스가 이미 만들어져 있습니다.

`Username: tutorial 
 Password: azure`

다운로드에 가상 환경은 포함되지 않습니다. [가상 환경 만들기](#creating-a-virtual-environment) 섹션의
단계에 따라 가상 환경을 만들어야 합니다. 가상 환경을 만들면 [디버그](#debugging) 섹션에 대해 프로젝트가 준비됩니다.

## 프로젝트 만들기

Python Tools for Visual Studio는 Python 가상 환경을 지원합니다. django 프로젝트를 만들고 가상
환경을 사용하여 종속성을 설치할 것입니다. 이는 Azure 웹 사이트 또는 클라우드 서비스에 게시되는 프로젝트를 설정하기 위해
권장되는 방법입니다.

1.  Visual Studio, 파일/새 프로젝트, **tutorial**이라는 이름의 Django 응용 프로그램을 엽니다.
    
    ![새
    프로젝트](./media/cloud-services-python-create-deploy-django-app/django-tutorial-001-new-project.png)

**참고:** 솔루션 탐색기의 참조 아래에 Django 1.4에 대한 노드가 표시됩니다. 이 노드는 대상 컴퓨터에 Python 및
Django를 설치하기 위해 Azure 클라우드 서비스 배포용으로 사용됩니다. 참조 메모에서 Django 1.4에 대한 참조를
삭제하지 마십시오. 가상 환경을 사용하고 Django 패키지를 설치할 것이기 때문에 가상 환경에 설치된 Django 패키지가
사용됩니다.

## <a name="creating-a-virtual-environment"></a>가상 환경 만들기

가상 환경을 사용하여 종속성을 설치할 것입니다. 이는 모든 Python 응용 프로그램에 대해 좋은 방법이며 Azure에 게시할
때에는 필수입니다.

1.  새 가상 환경을 만듭니다. 솔루션 탐색기에서 **Python Environments**를 마우스 오른쪽 단추로 클릭하고
    **Add Virtual Environment**를 선택합니다.
    
    ![가상 환경
    추가](./media/cloud-services-python-create-deploy-django-app/django-tutorial-002-add-virtual-env.png)

2.  기본 Python Interpreter로 Python 2.7을 선택하고 기본 이름 **env**를 사용합니다. pip
    및/또는 virtualenv를 이미 설치하지 않은 경우 PTVS에서 이를 설치합니다.

3.  **env** 및 **Install Python Package**: **django를 마우스 오른쪽 단추로 클릭합니다.**
    
    ![Django
    설치](./media/cloud-services-python-create-deploy-django-app/django-tutorial-003-install-django.png)

4.  Django에는 파일이 많으므로 설치하는 데 시간이 걸립니다. 출력 창에서 진행률을 볼 수 있습니다.
    
    ![Django 설치
    출력](./media/cloud-services-python-create-deploy-django-app/django-tutorial-004-install-django-output.png)
    
    **참고:** 출력 창에 실패가 표시되는 경우가 드물게 발생합니다. 그런 경우 오류가 정리와 관련이 있는지 확인하십시오.
    때때로 정리는 실패하지만 설치는 성공하는 경우가 있습니다(이를 확인하려면 출력 창에서 위로 스크롤). 이는 PTVS에서
    새로 만든 임시 파일/폴더를 잠가서 pip 정리 단계에서 삭제하지 못하도록 하기 때문에 발생합니다.

5.  **env** 및 **Install Python Package**: **pytz**를 마우스 오른쪽 단추로 클릭합니다(선택
    사항이지만 권장함, 시간대 지원을 위해 django에서 사용함).

## 가상 환경 확인

1.  모두 올바르게 설치되었는지 확인해 봅시다. **F5** 또는 **CTRL+F5**를 눌러 웹 사이트를 시작합니다. 그러면
    django 개발 서버가 실행되고 웹 브라우저가 시작됩니다. 다음 페이지가 표시되어야 합니다.
    
    ![Django 웹
    브라우저](./media/cloud-services-python-create-deploy-django-app/django-tutorial-004b-itworked.png)

## 설문 조사 응용 프로그램 만들기

이 섹션에서는 설문 조사의 투표를 처리하는 응용 프로그램을 추가합니다.

Django 프로젝트에는 여러 응용 프로그램이 포함될 수 있습니다. 이 자습서에서 프로젝트의 이름은 'tutorial'이며 Visual Studio 프로젝트에 해당합니다. 추가하려는 응용 프로그램의 이름은 **polls**이며 프로젝트 노드 아래의
폴더가 될 것입니다.

1.  **프로젝트 노드**, **추가**->**Django 앱**(이름: **polls**)을 선택합니다. 그러면
    일반적으로 사용되는 응용 프로그램 파일에 대한 상용구 코드와 함께 응용 프로그램의 폴더가 생성됩니다.
    
    ![Django 앱
    추가](./media/cloud-services-python-create-deploy-django-app/django-tutorial-005-add-django-app.png)

2.  **tutorial/settings.py**에서 **INSTALLED_APPS**에 다음을 추가합니다.
    
         'polls',

3.  **INSTALLED_APPS**에서 주석 처리를 제거합니다.
    
         'django.contrib.admin',

4.  **tutorial/urls.py**를 다음 코드로 바꿉니다.
    
         from django.conf.urls import patterns, include, url
        
         from django.contrib import admin
         admin.autodiscover()
        
         urlpatterns = patterns('',
             url(r'^', include('polls.urls', namespace="polls")),
             url(r'^admin/', include(admin.site.urls)),
         )

5.  **polls/models.py**를 다음 코드로 바꿉니다.
    
         from django.db import models
        	
         class Poll(models.Model):
             question = models.CharField(max_length=200)
             pub_date = models.DateTimeField('date published')
        		
             def __unicode__(self):
                 return self.question
        	
         class Choice(models.Model):
             poll = models.ForeignKey(Poll)
             choice_text = models.CharField(max_length=200)
             votes = models.IntegerField(default=0)
        		
             def __unicode__(self):
                 return self.choice_text

6.  **polls/views.py**를 다음 코드로 바꿉니다.
    
         from django.shortcuts import get_object_or_404, render
         from django.http import HttpResponseRedirect
         from django.core.urlresolvers import reverse
         from polls.models import Choice, Poll
        	
         def vote(request, poll_id):
             p = get_object_or_404(Poll, pk=poll_id)
             try:
                 selected_choice = p.choice_set.get(pk=request.POST['choice'])
             except (KeyError, Choice.DoesNotExist):
             # Redisplay the poll voting form.
                 return render(request, 'polls/detail.html', {
                     'poll': p,
                     'error_message': "You didn't select a choice.",
                 })
             else:
                 selected_choice.votes += 1
                 selected_choice.save()
                 # Always return an HttpResponseRedirect after successfully dealing
                 # with POST data. This prevents data from being posted twice if a
                 # user hits the Back button.
                 return HttpResponseRedirect(reverse('polls:results', args=(p.id,)))

7.  다음 코드를 사용하여 새 Python 파일 **polls/urls.py**를 추가합니다.
    
         from django.conf.urls import patterns, url
         from django.views.generic import DetailView, ListView
         from polls.models import Poll
        
         urlpatterns = patterns('',
             url(r'^$',
                 ListView.as_view(
                     queryset=Poll.objects.order_by('-pub_date')[:5],
                     context_object_name='latest_poll_list',
                     template_name='polls/index.html'),
                 name='index'),
             url(r'^(?P<pk>\d+)/$',
                 DetailView.as_view(
                     model=Poll,
                     template_name='polls/detail.html'),
                 name='detail'),
             url(r'^(?P<pk>\d+)/results/$',
                 DetailView.as_view(
                     model=Poll,
                     template_name='polls/results.html'),
                 name='results'),
             url(r'^(?P<poll data-morhtml="true"_id>\d+)/vote/$', 'polls.views.vote', name='vote'),
         )

8.  다음 코드를 사용하여 새 항목 **polls/admin.py**를 만듭니다.
    
         from django.contrib import admin
         from polls.models import Choice, Poll
        	
         class ChoiceInline(admin.TabularInline):
             model = Choice
             extra = 3
        	
         class PollAdmin(admin.ModelAdmin):
             fieldsets = [
                 (None,               {'fields': ['question']}),
                 ('Date information', {'fields': ['pub_date'], 'classes': ['collapse']}),
             ]
             inlines = [ChoiceInline]
             list_display = ('question', 'pub_date')
             list_filter = ['pub_date']
             search_fields = ['question']
             date_hierarchy = 'pub_date'
        	
         admin.site.register(Poll, PollAdmin)

9.  **polls/templates** 폴더에서 **polls**라는 이름의 새 폴더를 만듭니다.

10. 끌어서 놓기 또는 잘라내기/붙여넣기를 사용하여 **polls/templates/index.html** 파일을
    **polls/templates/polls** 폴더로 이동합니다.

11. **polls/templates/polls/index.html**을 다음 변경 내용으로 바꿉니다.
    
         <html>
         <head></head>
         <body>
         {% if latest_poll_list %}
             <ul>
             {% for poll in latest_poll_list %}
                 <li><a href="{% url 'polls:detail' poll.id %}">{{ poll.question }}</a></li>
             {% endfor %}
             </ul>
         {% else %}
             <p>No polls are available.</p>
         {% endif %}
         </body>
         </html>

12. 다음을 사용하여 새 Django HTML 템플릿 **polls/templates/polls/detail.html**을
    만듭니다.
    
         <html>
         <head></head>
         <body>
         <h1>{{ poll.question }}</h1>
         {% if error_message %}<p><strong>{{ error_message }}</strong></p>{% endif %}
         <form  action="{% url 'polls:vote' poll.id %}" method="post">
         {% csrf_token %}
         {% for choice in poll.choice_set.all %}
             <input  type="radio" name="choice" id="choice{{ forloop.counter }}" value="{{ choice.id }}" />
             <label  for="choice{{ forloop.counter }}">{{ choice.choice_text }}</label><br  />
         {% endfor %}
         <input  type="submit" value="Vote" />
         </form>
         </body>
         </html>

13. 다음을 사용하여 새 Django HTML 템플릿 **polls/templates/polls/results.html**을
    만듭니다.
    
         <html>
         <head></head>
         <body>
         <h1>{{ poll.question }}</h1>
         <ul>
         {% for choice in poll.choice_set.all %}
             <li>{{ choice.choice_text }} -- {{ choice.votes }} vote{{ choice.votes|pluralize }}</li>
         {% endfor %}
         </ul>
         <a href="{% url 'polls:detail' poll.id %}">Vote again?</a>
         </body>
         </html>

14. 이제 프로젝트에 다음 파일이 있어야 합니다.
    
    ![솔루션
    탐색기](./media/cloud-services-python-create-deploy-django-app/django-tutorial-006-solution-explorer.png)

## 로컬에서 sqlite3 데이터베이스 만들기

웹 응용 프로그램을 사용할 준비할 거의 다 되었지만 먼저 데이터베이스를 구성해야 합니다. 로컬에서 웹 사이트를 테스트하기 위해
sqlite3 데이터베이스를 만들 것입니다. 이는 추가로 설치할 필요가 없는 아주 간단한 데이터베이스입니다. 데이터베이스 파일은
프로젝트 폴더에 생성됩니다.

1.  **tutorial/settings.py**에서 다음 import를 파일의 맨 위에 추가합니다.
    
         from os import path

2.  다음 정의를 파일의 맨 위 근처, import 다음에 추가합니다.
    
         PROJECT_ROOT = path.dirname(path.abspath(path.dirname(__file__)))

3.  DATABASES 섹션을 다음 코드로 변경합니다.
    
         DATABASES = {
             'default': {
                 'ENGINE': 'django.db.backends.sqlite3',
                 'NAME': path.join(PROJECT_ROOT, 'db.sqlite3'),
                 'USER': '',
                 'PASSWORD': '',
                 'HOST': '',
                 'PORT': '',
             }
         }

4.  프로젝트 노드를 마우스 오른쪽 단추로 클릭하고 **Django**->**Django Sync DB**를 선택합니다.
    Django 관리 대화형 창이 표시됩니다. 데이터베이스가 아직 존재하지 않으므로 관리자 자격 증명을 만들라는 메시지가
    표시됩니다. 사용자 이름 및 암호를 입력합니다. 전자 메일은 선택 사항입니다.
    
    ![Django 동기화
    DB](./media/cloud-services-python-create-deploy-django-app/django-tutorial-007-sqlite3.png)

5.  F5 또는 CTRL-F5를 눌러 웹 사이트를 시작합니다. 그러면 django 개발 서버가 실행되고 웹 브라우저가
    시작됩니다. 웹 사이트의 루트 url은 설문 조사의 인덱스를 표시하지만 데이터베이스에 아직 아무것도 없습니다.
    
    ![웹
    브라우저](./media/cloud-services-python-create-deploy-django-app/django-tutorial-008-dev-server.png)

6.  **http://localhost:{port}/admin**으로 이동합니다. 개발 서버 콘솔 창에서 포트 번호를 확인할 수
    있습니다. 이전 단계에서 만든 자격 증명을 사용하여 로그인합니다.
    
    ![설문 조사
    추가](./media/cloud-services-python-create-deploy-django-app/django-tutorial-009-admin-login.png)

7.  관리 인터페이스를 사용하여 설문 조사를 하나 또는 두 개 추가합니다. 로컬 데이터베이스에 설문 조사를 추가하는 데 너무
    많은 시간을 소비하지 마십시오. 나중에 클라우드 데이터베이스로 전환하고 데이터베이스를 다시 채울 것입니다.
    
    ![설문 조사
    인덱스](./media/cloud-services-python-create-deploy-django-app/django-tutorial-009-admin-add-poll.png)

8.  **http://localhost:{port}/**로 이동합니다. 추가한 설문 조사의 인덱스가 표시됩니다.
    
    ![](./media/cloud-services-python-create-deploy-django-app/django-tutorial-010-index.png)

9.  설문 조사를 하나 클릭하고 투표 페이지로 이동합니다.
    
    ![설문 조사 세부
    정보](./media/cloud-services-python-create-deploy-django-app/django-tutorial-011-detail.png)

10. 투표를 제출하면 투표 수가 증가하는 것을 볼 수 있는 결과 페이지로 리디렉션됩니다.
    
    ![설문 조사
    결과](./media/cloud-services-python-create-deploy-django-app/django-tutorial-012-results.png)

## 스타일시트 및 기타 정적 파일 사용

이 섹션에서는 스타일시트를 사용하기 위해 사이트의 모습을 업데이트할 것입니다. 스타일시트 같은 정적 파일은 별도로 처리되므로
올바른 위치에 저장하는 것이 중요합니다.

1.  **tutorial/settings.py**에서 **STATIC_ROOT**의 할당을 찾아 다음으로 변경합니다.
    
         STATIC_ROOT = path.join(PROJECT_ROOT, 'static').replace('\\','/')

2.  **polls** 폴더에 **static**이라는 이름의 새 폴더를 만듭니다.

3.  **polls/static** 폴더에 **polls**라는 이름의 새 폴더를 만듭니다.

4.  **polls/static/polls** 폴더에 **images**라는 이름의 새 폴더를 만듭니다.

5.  다음을 사용하여 새 스타일시트 파일 **polls/static/polls/style.css**를 추가합니다.
    
         body {
             color: darkblue;
             background: white url("images/background.jpg");
         }

6.  **polls/static/polls/images** 폴더에 기존 이미지를 추가하고 이름을
    **background.jpg**로 지정합니다.

7.  이제 프로젝트에 다음 파일이 있어야 합니다.
    
    ![솔루션
    탐색기](./media/cloud-services-python-create-deploy-django-app/django-tutorial-013-solution-explorer.png)

8.  다음 변경 내용을 사용하여 스타일시트를 참조하도록 모든 템플릿의 헤더를 편집합니다.
    
         <head>
         {% load staticfiles %}
         <link  rel="stylesheet" type="text/css" href="{% static 'polls/style.css' %}" />
         </head>

9.  웹 사이트를 다시 실행합니다. 인덱스, 설문 조사 및 결과 페이지에 진한 파란색 텍스트와 배경 이미지와 함께 만든
    스타일시트가 사용됩니다.
    
    ![설문 조사 세부
    정보](./media/cloud-services-python-create-deploy-django-app/django-tutorial-014-detail-styled.png)

## <a name="debugging"></a>디버그

Python Tools for Visual Studio에는 Django 템플릿 디버그에 대해 특별한 지원 기능이 있습니다.

1.  **polls/templates/polls/index.html**을 열고 다음 줄에서 **F9**을 눌러 중단점을
    설정합니다.
    
         {% if latest_poll_list %}

2.  **F5**를 눌러 디버그를 시작합니다. Visual Studio가 템플릿 파일에서 중단합니다.

3.  **디버그->창->지역**에서 **지역 창**을 열면 **latest_poll_list** 변수 및 값이
    표시됩니다.

4.  **F10**을 눌러 일반적인 Python 코드에서처럼 이동할 수 있습니다. for 루프에서 **poll**의 값을 검사할
    수 있습니다.
    
    ![디버그](./media/cloud-services-python-create-deploy-django-app/django-tutorial-015-debugging.png)

## Azure에서 데이터베이스 만들기

지금 설문 조사 응용 프로그램이 로컬에서 작동하는 것을 확인했으며, Azure에 호스트된 데이터베이스를 사용하도록 전환해봅시다.

다음 두 섹션에서는 SQL 데이터베이스와 MySQL 데이터베이스를 사용하는 방법을 설명합니다. 두 데이터베이스 모두 호스트된
서비스입니다.

가상 컴퓨터를 만들어 데이터베이스 서버를 설치할 수도 있습니다. Azure Linux VM에서 MySQL 설정에 대한 지침은
[여기][6]를 참조하십시오.

**참고:** Azure에서 sqlite3 데이터베이스를 사용할 수 있습니다(개발용으로만, 프로덕션에서 사용하는 것은 권장하지
않음). django 응용 프로그램과 함께 데이터베이스에 배포하려면 프로젝트에 **db.sqlite3** 파일을 추가해야 합니다.

### SQL 데이터베이스

이 섹션에서는 Azure에 SQL 데이터베이스를 만들고 가상 환경에 필요한 패키지를 추가한 후 새 데이터베이스를 사용하도록 설정을
변경할 것입니다.

1.  Azure 관리 포털에서 **SQL 데이터베이스**를 선택합니다.

2.  먼저 서버를 만들어 데이터베이스를 호스트합니다. **서버** 및 **추가**를 선택합니다.

3.  새로 만든 서버의 **구성** 탭에 현재 클라이언트 IP 주소가 표시됩니다. 그 옆에 **ADD TO THE ALLOWED
    IP ADDRESSES**를 클릭합니다.
    
    **참고:** 때때로 Azure에서 클라이언트 IP 주소를 올바르게 검색하지 않는 경우가 있습니다. 데이터베이스를 동기화할
    때 오류가 발생하는 경우, 오류 메시지에서 IP 주소를 복사하고 붙여넣어 허용된 IP 주소에 추가해야 합니다.

4.  다음으로 데이터베이스를 만들어보겠습니다. **데이터베이스** 탭에서 맨 아래 막대에 있는 **추가**를 클릭합니다.

5.  Visual Studio에서 Django의 SQL Server 데이터베이스에 액세스하는 데 필요한 패키지를 가상 환경에
    설치할 것입니다.

6.  **env** 및 **easy_install**을 사용하여 **Install Python Package**:
    **pyodbc**를 마우스 오른쪽 단추로 클릭합니다.

7.  **env** 및 **pip**를 사용하여 **Install Python Package**:
    **django-pyodbc-azure**를 마우스 오른쪽 단추로 클릭합니다.

8.  **tutorial/settings.py**를 편집하고 **NAME**, **USER**, **PASSWORD** 및
    **HOST**를 ClearDB 제어판에 나열된 값으로 바꿔 **DATABASES** 정의를 다음으로 변경합니다.
    
         DATABASES = {
             'default': {
                 'ENGINE': 'sql_server.pyodbc',
                 'NAME': '<database  name>',
                 'USER': '<user  name>@<server  name>',
                 'PASSWORD': '<user  password>',
                 'HOST': '<server  name>.database.windows.net',
                 'PORT': '',
                 'OPTIONS': {
                     'driver': 'SQL Server Native Client 11.0',
                     'MARS_Connection': True,
                 },
             }
         }
    
    컴퓨터에 설치한 드라이버를 사용하는지 확인해야 합니다. 시작 메뉴/화면에서 **관리 도구**를 열고 **ODBC 데이터
    원본(32비트)**을 선택합니다. 드라이버가 **드라이버** 탭에 나열됩니다.
    
    Azure 웹 사이트에서 실행하고 있는 경우 **SQL Server Native Client 10.0** 및 **SQL
    Server Native Client 11.0**이 모두 작동합니다.
    
    Azure 클라우드 서비스에서 실행하고 있는 경우 **SQL Server Native Client 11.0**만
    작동합니다.

9.  로컬 sqlite3 데이터베이스에 대해 했던 것처럼 데이터베이스를 동기화하고 관리자 자격 증명을 만듭니다.

### MySQL 데이터베이스

이 섹션에서는 Azure에 MySQL 데이터베이스를 만들고 가상 환경에 필요한 패키지를 추가한 후 새 데이터베이스를 사용하도록
설정을 변경할 것입니다.

Azure 스토어에서 MySQL 데이터베이스를 비롯하여 다양한 서비스를 계정에 추가할 수 있습니다. 무료 평가판 계정을 만들 수
있으며, 작은 데이터베이스를 특정 크기로 늘릴 수 있습니다.

1.  Azure 관리 포털에서 **새로 만들기**->**스토어**->**앱 서비스**->**ClearDB
    MySQL Database**를 선택합니다. 무료로 데이터베이스를 만듭니다.

2.  다음으로, Django의 MySQL 데이터베이스에 액세스하는 데 필요한 패키지를 가상 환경에 설치할 것입니다.
    **env** 및 **easy_install**을 사용하여 **Install Python Package**:
    **mysql-python**을 클릭합니다.

3.  **tutorial/settings.py**를 편집하고 **NAME**, **USER**, **PASSWORD** 및
    **HOST**를 ClearDB 제어판에 나열된 값으로 바꿔 **DATABASES** 정의를 다음으로 변경합니다.
    
         DATABASES = {
             'default': {
                 'ENGINE': 'django.db.backends.mysql',
                 'NAME': '<database  name>',
                 'USER': '<user  name>',
                 'PASSWORD': '<user  password>',
                 'HOST': '<host  url>',
                 'PORT': '',
             }
         }

4.  로컬 sqlite3 데이터베이스에 대해 했던 것처럼 데이터베이스를 동기화하고 관리자 자격 증명을 만듭니다.

## Django 셸 사용

1.  프로젝트 노드를 마우스 오른쪽 단추로 클릭하고 **Django** -> **Open Django Shell**을
    선택합니다.

2.  이 대화형 창에서 모델을 사용하여 데이터베이스에 액세스할 수 있습니다. 다음 코드를 입력하여 데이터베이스에 설문 조사를
    추가합니다.
    
         from polls.models import Poll, Choice
         from django.utils import timezone
        
         p = Poll(question="Favorite way to host Django on Azure?", pub_date=timezone.now())
         p.save()
        
         p.choice_set.create(choice_text='Web Site', votes=0)
         p.choice_set.create(choice_text='Cloud Service', votes=0)
         p.choice_set.create(choice_text='Virtual Machine', votes=0)
    
    ![Django 셸 설문 조사
    추가](./media/cloud-services-python-create-deploy-django-app/django-tutorial-018-shell-add-poll.png)

3.  모델에 대한 정적 분석은 전체 API에 대해 제한된 뷰를 제공합니다. 대화형 창에서는 라이브 개체에 대한
    IntelliSense를 얻을 수 있으므로 API를 탐색하기에 좋은 방법입니다. 다음은 대화형 창에서 시도해볼 수 있는 몇
    가지입니다.

         # all poll objects
         Poll.objects.all()

         # primary key for poll
         p.id

         # all choice objects for the poll
         p.choice_set.all()

         # get object by primary key
         Poll.objects.get(pk=1)
    
    ![Django 셸 설문 조사
    쿼리](./media/cloud-services-python-create-deploy-django-app/django-tutorial-019-shell-query.png)

4.  웹 사이트를 시작합니다. Django 셸을 사용하여 추가한 설문 조사가 표시되어야 합니다.

## Azure에 게시

이제 Azure에 데이터베이스가 있으므로 다음 단계에서는 Azure에 웹 사이트 자체를 호스트합니다.

Azure에는 Django 응용 프로그램을 호스트하는 몇 가지 방법이 있습니다.

* [웹 사이트][7]
* [클라우드 서비스][8]
* [가상 컴퓨터][9]

Python Tools for Visual Studio에는 Azure 웹 사이트 및 클라우드 서비스에 게시하는 기능이 있습니다.
다음 두 섹션에서는 이러한 기능에 대해 설명하며, 둘 중 하나를 선택하여 진행하거나 둘 다 진행할 수 있습니다.

두 경우 모두 PTVS에서 IIS를 구성하며, 프로젝트에 web.config 파일이 없는 경우 이 파일을 생성할 것입니다.
settings.py에 STATIC_ROOT를 설정한 경우 정적 파일이 자동으로 수집됩니다(manage.py collectstatic).

가상 컴퓨터에 Django를 호스트하는 작업은 이 자습서에서 다루지 않습니다. 이 자습서에서는 원하는 운영 체제(Windows
또는 Linux)에 VM 생성, Python 설치 및 Django 응용 프로그램을 수동으로 배포하는 방법을 다룹니다.

### Azure 웹 사이트

1.  먼저 웹 사이트를 만들어야 합니다. Azure 관리 포털에서 **새로 만들기**->**계산**->**웹
    사이트**->**빠른 생성**을 클릭합니다. 사용 가능한 이름을 선택합니다.

2.  웹 사이트를 만들었으면 웹 사이트의 게시 프로필을 다운로드합니다.
    
    ![웹 사이트 프로필
    다운로드](./media/cloud-services-python-create-deploy-django-app/django-tutorial-020-website-download-profile.png)

3.  Visual Studio에서 프로젝트 노드를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.
    
    ![웹 사이트
    게시](./media/cloud-services-python-create-deploy-django-app/django-tutorial-020-website-publish.png)

4.  이전에 다운로드한 웹 사이트 게시 프로필 파일을 가져옵니다.

5.  기본 설정을 적용하고 **게시**를 클릭하여 게시를 시작합니다.

6.  게시가 완료되면 웹 브라우저가 게시된 웹 사이트로 열립니다.
    
    ![웹 사이트
    브라우저](./media/cloud-services-python-create-deploy-django-app/django-tutorial-020-website.png)

### Azure 클라우드 서비스

1.  프로젝트 노드를 마우스 오른쪽 단추로 클릭하고 **Add Azure Cloud Service Project** 또는
    **Convert -> Convert to Azure Cloud Service Project**(Visual
    Studio 버전에 따라 표시되는 옵션이 다름)를 선택합니다. 그러면 접미사 .Azure를 사용하여 새 프로젝트가 솔루션에
    추가됩니다. 이 새 프로젝트는 솔루션에서 시작 프로젝트로 표시됩니다.
    
    **참고:** 프로젝트 상황에 맞는 메뉴에서 Azure 클라우드 서비스 프로젝트를 생성하는 명령이 표시되지 않는 경우,
    Azure Tools for Visual Studio를 설치해야 합니다. Azure Tools for Visual
    Studio는 Azure SDK for .NET의 일부로 설치됩니다. 이 자습서의 앞부분에서 요구 사항 세션을
    참조하십시오.
    
    ![솔루션
    탐색기](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-solution-explorer.png)

#### Azure 에뮬레이터 실행

1.  계산 에뮬레이터에서 실행하려면 **관리자로 Visual Studio를 다시 시작**해야 합니다.

2.  **F5**를 눌러 디버그를 시작하면 앱이 계산 에뮬레이터에서 실행되고 배포됩니다. 관리 인터페이스가 작동하며 설문
    조사에서 투표를 할 수 있는지 확인하십시오.
    
    ![계산
    에뮬레이터](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-emulator.png)

3.  관리자로 계속 실행하지 않으려면 Visual Studio를 다시 시작하면 됩니다.

#### Azure 클라우드 서비스에 게시

1.  다음으로 클라우드 서비스를 Azure에 게시하겠습니다. **tutorial.Azure** 클라우드 서비스 프로젝트 및
    **게시**를 마우스 오른쪽 단추로 클릭합니다.
    
    **참고:** 클라우드 서비스 프로젝트에서 **게시**를 선택하십시오. 그러면 **Publish Azure
    Application** 대화 상자가 실행되어 Azure 클라우드 서비스에 게시할 수 있습니다. Django 프로젝트에서
    **게시**를 선택하면 Azure 웹 사이트에 게시하는 데 사용되는 **웹 게시** 대화 상자가 실행됩니다.
    
    ![클라우드 서비스
    게시](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-publish.png)

2.  Azure 구독 파일을 가져와야 합니다. [로그인하여 자격 증명 다운로드][10] 링크를 클릭하여 Azure 포털에서
    다운로드합니다.

3.  설정 페이지에서 클라우드 서비스 콤보 상자의 **새로 만들기**를 선택하여 새 클라우드 서비스를 만듭니다. 사용 가능한
    아무 이름이나 사용할 수 있습니다.
    
    ![클라우드 서비스
    설정](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-settings.png)

4.  기본 설정을 적용하고 **게시**를 클릭합니다. 클라우드 서비스에 대해 VM을 프로비전해야 하기 때문에 웹 사이트에
    게시하는 것보다 시간이 더 걸립니다. Azure 활동 로그 창에서 진행률을 볼 수 있습니다.
    
    ![클라우드 서비스
    게시](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice-publish-progress.png)

5.  작업을 완료하면 Azure 활동 로그 창에서 웹 사이트 URL을 클릭하여 웹 브라우저를 엽니다.
    
    ![클라우드 서비스
    브라우저](./media/cloud-services-python-create-deploy-django-app/django-tutorial-021-cloudservice.png)

## 결론

이 자습서에서는 [Python Tools for Visual Studio][3]를 사용하여 Django 응용 프로그램을
개발했습니다. 세 가지 다른 데이터베이스, 즉 sqlite3, SQL Server 및 MySQL 데이터베이스를 사용했습니다.
마지막으로 Azure 웹 사이트 및 클라우드 서비스에 응용 프로그램을 게시했습니다.



[1]: http://www.youtube.com/watch?v=wkqjafvvU5w
[2]: https://www.djangoproject.com/
[3]: http://pytools.codeplex.com
[4]: http://www.python.org/download/
[5]: http://download-codeplex.sec.s-msft.com/Download?ProjectName=pytools&DownloadId=783376
[6]: http://www.windowsazure.com/ko-kr/manage/linux/common-tasks/mysql-on-a-linux-vm/
[7]: http://www.windowsazure.com/ko-kr/services/web-sites/
[8]: http://www.windowsazure.com/ko-kr/services/cloud-services/
[9]: http://www.windowsazure.com/ko-kr/services/virtual-machines/
[10]: https://manage.windowsazure.com/publishsettings/index?client=vs&schemaversion=2.0