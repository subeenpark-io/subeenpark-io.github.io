---
layout: post
title: Django Taggit을 이용한 해시태그 검색 기능 구현하기
description: >
  Django-taggit과 query parameter를 이용해 포스트에 해시태그를 달고 검색 기능을 구현해보자.
sitemap: false
hide_last_modified: true
tags: [django]
---

[django-taggit](https://pypi.org/project/django-taggit/) 패키지는 django 서비스에 쉽게 태그를 할 수 있도록 도와주는 앱이다. 약간의 커스텀을 통해서 우리가 일반적으로 인스타그램 등 기타 SNS에서 쉽게 볼 수 있는 해시태그 기능을 구현하는 법을 알아보자.
...{:.message}

### 1. Taggit 패키지 설치

아래 코드를 터미널에 입력해 패키지를 설치하자.

```
pip install django-taggit
```

### 2. `settings.py` 수정

`INSTALLED_APPS`에 taggit을 추가한다.

```py
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'django.contrib.sites',

    # ...

    # taggit
    'taggit',
]
```

### 3. `${app_name}/models.py` 수정

##### 관련 함수 import

```py
# 관련 module import
from taggit.managers import TaggableManager
from taggit.models import (
    TagBase, TaggedItemBase
)
```

관련 모듈인 TaggableManager, TagBase, TaggedItemBase를 import한다.

##### Tag 모델, TagPortfolio(N:M 중개모델) 생성 + Portfolio 모델에 tags fields 추가

```py
# TagBase를 상속받은 tag model
class Tag(TagBase):

    slug = models.SlugField(
        verbose_name='slug',
        unique=True,
        max_length=100,
        allow_unicode=True,
    )

```

`TagBase`를 상속받아서 커스텀된 태그 모델을 생성한다. 원할 시 이 과정에서 `meta` 클래스 등을 추가하거나 `slugfield` 형성 방식을 커스텀할 수 있다.

```py
# Tag와 Tag가 달리는 포스트를 연결하는 N:M 중개모델
class TaggedPortfolio(TaggedItemBase):
    content_object = models.ForeignKey('Portfolio', on_delete=models.CASCADE)
    tags = models.ForeignKey(
        'Tag', related_name='tagged_portfolios', on_delete=models.CASCADE, null=True)

```

안정적인 DB관리를 위해 N:M 모델을 many-to-many 방식으로 한 모델을 다른 모델에 종속시키는 방식 대신 중개모델을 만드는 방식을 사용한다. 중개모델은 `tag` 모델과 `post`(여기서는 `portfolio`) 모델을 연결한다. 이 때 중개모델은 `TaggedItemBase`를 상속받아서 커스텀된 `foreignKey`를 가질 수 있다.

```py
# 포스트 모델에 Tag field 추가
class Portfolio(models.Model):
    # ...
    tags = TaggableManager(
        verbose_name='tags', help_text='A comma-separated list of tags.', blank=True, through=TaggedPortfolio)
```

`post`(여기서는 `Portfolio`) 모델 내부에 `tags` field를 만든다. 이 때 `tags`의 데이터 타입은 `TaggableManager`로, 이 객체가 태그의 생성, 삭제 등을 관리한다.

### 4. `views.py` 수정

`form.save_m2m()`을 추가해서 `MODEL.save()`후 입력된 tag를 별도로 저장해주는 과정을 거친다. update 역시 비슷한 방식으로 작업하지만 tag를 clear 하고 다시 저장해주어야 한다.

```py
@login_required
def portfolio_create(request):
    # 'extra' : number of photos
    if request.method == 'POST':
        form = PortfolioForm(request.POST, request.FILES,)

        if form.is_valid():
            portfolio = form.save(commit=False)
            portfolio.user = request.user
            portfolio.save()
            form.save_m2m() # tag를 저장하는 부분
            # ...
```

### 5.`utils.py` 수정

```py
def hashtag_splitter(tag_string):
    return [t.strip() for t in tag_string.split('#') if t.strip()]


def hashtag_joiner(tags):
    return '#' + ' #'.join(t.name for t in tags).lstrip()
```

기존의 `taggit` 패키지는 사용자의 태그 입력을 `[태그1, 태그2, ...]`와 같이 콤마를 구분자로 이용한다. 내가 개발중인 서비스에서는 `#`를 이용해서 구분자를 두기 위해 `hashtag_splitter()` 함수와 `hashtag_joiner()` 함수를 커스텀하고 커스텀된 함수가 작동할 수 있도록 `settings.py에 등록했다.`

##### `settings.py` 에 custom `hashtag_splitter/joiner` 추가

```
# taggit
TAGGIT_CASE_INSENSITIVE = True
TAGGIT_TAGS_FROM_STRING = 'portfolio.utils.hashtag_splitter'
TAGGIT_STRING_FROM_TAGS = 'portfolio.utils.hashtag_joiner'
```

이러한 방식으로 `taggit` 모듈을 이용해 검색 기능을 구현할 수 있다.
