---
layout: post
title: "2021 HackaLearn 참가 후기: 멜하는 감자🥔"
description: >
  "2021 HackaLearn 참가 후기: Azure Static Web App, GitHub Actions, Svelte, MongoDB로 웹서비스 만들기"
sitemap: true
hide_last_modified: true
tags: []
image:
  path:    /assets/img/blog/2021-08-15-HackaLearn/ServiceMain.gif
---



HackaLearn이 거의 다 끝나가서 쓰는 글! 배울게 산더미지만 그래도 한발짝 한발짝🏔
{:.message}

한동안 참여하고 있는 연구들이 너무 안풀려서 매너리즘에 빠져있었다. 새로운 걸 배우는 일은 언제나 고통스럽지만 그만큼 매너리즘을 깨는 데 쉬운 방법도 없기에…😖 한참을 매너리즘을 타파해줄 만한 무언가가 없나 찾아다녔다. 주기적으로 구경하는 페이스북 그룹 여러 곳에 동시다발적으로 HackaLearn이 올라왔고, 내가 열심히 찾아다니던 그것이라는 걸 깨달았다🎉 그 길로 즉시 피로그래밍에서 멤버를 모집했고, 2주만에 뚝딱 웹사이트 하나를 완성했다. 주제도 재밌었고, 결과물도 만족스럽고, 과정도 즐거웠어서 남기는 글으로 남기기로 했다🥰



1. this ordered seed list will be replaced by the toc
{:toc}

# HackaLearn이란? 

[*공식 깃허브 링크*](https://github.com/devrel-kr/HackaLearn?fbclid=IwAR1Ppi_zfEnkK-gBtd_BKl70FBbYlj2WtGulDktikRaPShlp7JvuCy-fhjQ)

HackaLearn이란  ‘Hackathon + Learn의 합성어로 주어진 기간 안에 관련 지식을 직접 배워서 해커톤에 적용하는 이벤트’라고 나와있다. 이름 그대로 새로운 기술을 배워서 적용시켜서 비교적 단기간(약 2주)동안 결과물을 완성해내는 대회다. 이번 주제는 [애저 정적 웹앱(Azure Static Web App)](https://azure.microsoft.com/ko-kr/services/app-service/static/?WT.mc_id=devcloud-33673-juyoo&ocid=AID3035096)과 [깃헙 액션(Github Actions)](https://github.com/features/actions)였다. 참여하기 부담스럽지 않으면서도 마이크로소프트가 후원해서 상이 매력적인 대회 🏆



# 대회에서 얻은 것

워낙 병행하고 있는 일이 많아서 수상을 목표로 한 것은 아니었다. 애초에 피로그래밍 톡방에서 멤버를 구할 때도 부담가지지 않고 함께 배운다는 마음가짐으로 했으면 좋겠다고 말을 했었고, 소기의 목적은 아~주 잘 달성했다고 생각한다. 이번 대회에서 목표로 했고, 실제로 얻은 것들은 



## 깃헙 액션 사용 방법

요즘 여러 회사의 채용공고를 보며 CI/CD를 제대로 공부할 필요가 있겠다는 생각이 여러번 들었다. 그동안 취미로, 그리고 1인 개발을 하다보니 해야지 해야지 하면서도 미뤄두고 있었는데 이 기회에 [MicrosoftLearn](https://docs.microsoft.com/ko-kr/learn/)을 통해서 드디어! CI/CD에 한 발자국 가까워질 수 있었다. 여담이지만 [MicrosoftLearn](https://docs.microsoft.com/ko-kr/learn/)에는 대회 주제와 관련된 것 이외에도 되게 유용한 정보가 많았는데, 이번에 처음 알게 되었다. 워낙 탄탄하게 튜토리얼들이 준비되어있어서 앞으로도 애용할듯 !!



## Azure 정적 웹앱 구축 방법

지금도 깃허브 블로그를 운영하고 있지만 항상 주먹구구식으로 커밋하고 확인하고 커밋하고 확인하는 방식을 사용했는데, 이번에 튜토리얼을 차근차근 따라하면서 정적 웹앱이라는 것이 어떠한 방식으로 돌아가는지 알 수 잇어서 좋았다!! 그리고 덤으로 항상 AWS만 사용해왔는데 새로운 클라우드 서비스를 써볼 수 있어서 좋았다! 개인적으로는 AWS에 비해서 학생 지원도 조금 더 많고, UI도 훨씬 직관적인 면이 있어서 토이 프로젝트 용으로 굳이 정적 웹앱이 아니더라도 요긴하게 사용할 것 같다. 



## Svelte 실력

웹에서 호다닥 호다닥 프로토타이핑을 해야 할 경우가 많다보니 프론트 프레임워크를 똑바로 한번 연습을 해야겠다는 생각을 꾸준히 품고는 있었지만 이것 역시 계속계속 미뤄왔다 222… 근데 svelte app이 정적 웹앱 튜토리얼에 있어서 대회를 보며 바로 이거다!! 라고 생각했음 호호! 편하다는 소리는 많이 들었는데 제대로 써본 건 처음이다. 후기는… Svelte 최고! 추가로 서버리스 앱에 가까워서 서버 구축에 많이 시간을 뺏기지 않아서 충분히 프론트 프레임워크를 연습할 수 있었던 것 같다. 좋아좋아!



## 그리고 덤으로… MongoDB와 mongoose에 대한 경험

대부분이 서버 하던 팀원들이라서 정적 웹앱을 가만 보다 적응을 못하고 우리 너무 날로 먹는거 아니냐? 라는 얘기가 나왔고 그래서 API를 만들어보자고 했다. 그래서 내가 그럼 내가 해볼게! 라고 대답했는데 거기서부터 잘못된 거였다… 흑흑… 대회에서 반드시 익혀야 하는 클라우드 스킬 챌린지에서 보여준 API는 깃허브에 DB가 그대로 올라가는 방식이었고… 우리는 유저 프로필을 저장하고 싶었고… 그래서 검색해보니 MongoDB API를 연결하는 솔루션이 있었지만 정보가 정말 공식 문서 말고는 전무해서 4일을 꼬박 삽질하며 구축했다. 그래도 만들고 나니 넘 뿌듯!! 그리고 NoSQL 구경해본게 처음이었는데 매우매우 신기하고 좋았음. 무엇보다.. 점점 강인해지는 기분이 든다.💪 나는 이제 공식 문서만 보고도 코딩하는 코린이!!



# 서비스 소개: “멜”하는 감자



## 기획 의도

예기치 못하게 길어진 코로나 사태로 인해 많은 대학생들이 가시밭길 대학생 생활을 하고 있을거라고 생각한다. 나역시도…흑흑😢 평소에 에브리타임 커뮤니티 같은 곳에 교수님께 #@!$를 물어보고 싶은데 메일을 어떻게 보내야하나요 ㅜㅜ? 같은 글이 올라오는 것을 자주 목격했고, 대학교를 4년 가까이 다니고 있는 나에게도 메일 쓰기는 여전히 어려운 일이기에… 이 기회에 교수님에게 보낼 메일창만 켜면 옴짝달싹도 못하는 대학생들을 위한 서비스를 좀 만들어보자는 생각을 했다. 추가적으로 HackaLearn의 스킬 중 하나가 정적 웹앱이었으니 최대한 그 이점을 살리고 서버리스에 가깝게 가벼운 서버를 가져가자는 합의가 팀 내에서 있었고, 이 니즈 역시 함께 충족시킬 수 있는 아이템이라는 생각이 들어서 그길로 땅땅 하고 제작하기 시작했다.



## 서비스 이름의 탄생 배경

말하는 감자는 대학생들이라면 모르는 사람이 없는 밈일거고, 우리는 메일을 주 아이템으로 삼은 서비스니 그렇다면 멜하는감자?! 이런 의견이 나와서 얘도 그길로 박수로 인준했다. 넘 기여우엉. 추가적으로 우리 팀 이름은 월급두배받는법이었는데, 얘의 탄생 배경은 아래에…ㅋㅋㅋㅋㅋㅋ



## 구현

![](/assets/img/blog/2021-08-15-HackaLearn/MailPage.png){:.lead width="800" height="100" loading="lazy"}

![](/assets/img/blog/2021-08-15-HackaLearn/LoginPage.png){:.lead width="800" height="100" loading="lazy"}

A caption for an image.
{:.figcaption}

호스팅은 Azure, 프론트는 Svelte, DB는 MongoDB, API는 mongoose를 사용했다. 서비스의 주요 페이지는 아래와 같이 생겼음. 많은 애로사항이 있었찌만…결국 완성해냈다! 자세한 코드는 프로젝트에 등록하게 되면 첨부할 예정. 전반적인 뷰 디자인은 피그마를 사용해서 내가 뽑았고, 프론트 왕들이 뚝딱뚝딱 만들어줬다. 프린이는..아직도 flex가 어려워오… 덤으로 프로젝트를 진행하는 내내 우리 팀의 목표는 귀엽고 하찮고 웃긴거였는데 이걸 한번에 담아낸 깜찍이 캐릭터를 그려준 사람은 정현언니! 언니의 그림 계정은 [요기](https://instagram.com/drawme_giraffe?utm_medium=copy_link). 캐릭터가 너무너무 기여워서 온 페이지를 얘로 도배하고 싶은 욕구가 계속 퐁퐁 샘솟아서 참느라 고생했다.  



# 마무리

항상 하는 말이지만, 모든 일은 누구와 함께 하느냐가 가장 중요한 것 같다. 인복이 넘치는 나이기에 이번에도 너무 즐거웠고😘 기대보다도 훨씬 많은 것들을 배워서 쪼금 피곤하긴 하지만 후회는 없다 히히🤓 그리고 무엇보다 하다보니까 또 진심되어버려서 서비스가 생각보다 더 괜찮게 뽑혔다. 도메인 사서 계속 열어둬야징~~ 오늘도 여전히 배울게 산더미같은 우리 인생이지만 즐겁게 즐겁게~~ 🏔어서 코로나 끝나서 한강가서 애들이랑 치맥하구 싶다🍗

 
