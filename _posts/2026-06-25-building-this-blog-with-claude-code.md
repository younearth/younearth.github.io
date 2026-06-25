---
title: 이 블로그를 Claude Code와 함께 만든 과정
date: 2026-06-25 22:05:00 +0900
categories: [AI 활용, Claude Agent]
tags: [claude, agent, jekyll, github-pages]
---

지금 보고 계신 이 블로그 자체를 Claude Code와 대화하면서 처음부터 만들었다. "AI한테 블로그 만들어줘"라고 한마디 던지고 끝난 게 아니라, 생각보다 손이 많이 가는 과정이었어서 그 흐름을 기록해본다.

## 1. 방식 정하기

처음엔 선택지가 여러 개였다 — Jekyll, Hugo, 순수 정적 사이트, 기존 테마 fork. GitHub Pages가 기본으로 지원하고 설정이 가장 간단하다는 이유로 **Jekyll + Chirpy 테마**를 골랐다. 테마는 개발자용으로 인기 있고 다크모드, 코드 하이라이팅, TOC가 기본 지원된다는 점에서 Chirpy를 선택.

## 2. 저장소 생성은 결국 웹에서 직접

로컬에 `gh` CLI도 없고 Homebrew도 없는 깨끗한 맥이었다. Claude Code가 대신 저장소를 만들어줄 방법이 없어서, [chirpy-starter](https://github.com/cotes2020/chirpy-starter) 템플릿 페이지에서 "Use this template"을 직접 눌러 `younearth.github.io` 저장소를 만들고, Settings → Pages에서 Source를 "GitHub Actions"로 바꿔주는 두 단계는 내가 브라우저에서 직접 했다. AI가 모든 걸 대신해줄 수는 없고, 권한이 필요한 지점에서는 사람이 직접 클릭해야 하는 구간이 분명히 있었다.

## 3. 인증 문제 — 비밀번호가 아니라 토큰

저장소를 로컬에 clone한 뒤 Claude Code가 커밋까지는 했는데, `git push`에서 막혔다.

```
fatal: could not read Username for 'https://github.com': Device not configured
```

터미널이 비대화형이라 사용자 입력을 받을 수 없는 게 원인이었다. 게다가 GitHub 로그인을 Google SSO로 해놨던 터라 "비밀번호를 어디서 찾아야 하나" 잠깐 헤맸는데, 알고 보니 git push 인증은 SSO 로그인과 무관하게 **Personal Access Token**으로 따로 발급받아야 하는 것이었다. `https://github.com/settings/tokens/new`에서 `repo` 권한 토큰을 만들어 한 번 입력하니, macOS 키체인에 저장돼서 이후로는 다시 묻지 않았다.

## 4. 미래 날짜 글은 보이지 않는다

글을 하나 써서 push했는데 사이트에 안 보이는 사고가 있었다. 원인은 frontmatter의 `date`를 다음날 날짜로 잘못 적은 것 — Jekyll은 기본 설정상 미래 날짜로 적힌 글을 빌드에서 제외하고 숨긴다. 이후로는 글을 쓸 때 항상 `date` 명령으로 현재 시각을 확인하고 frontmatter에 반영하는 규칙을 `NOTES.md`에 적어뒀다.

## 5. 글은 보이는데 메뉴를 누르면 사라지는 현상

캐시 무시 새로고침(`Cmd+Shift+R`)으로는 글이 보이는데, 사이드바 메뉴를 클릭하면 다시 안 보이는 일이 있었다. Chirpy 테마의 PWA 오프라인 캐시 기능 때문에, 한 페이지만 새로고침해도 서비스워커가 다른 페이지는 예전 캐시를 그대로 서빙해서 생긴 현상이었다. 개발자 도구 → Application → Service Workers에서 Unregister하고 site data를 지우면 해결된다.

## 6. 진짜 미리보기를 위해 로컬 환경 구축

CSS나 폰트를 바꾸고 싶었는데, 매번 push해서 확인하긴 싫었다. "수정 → 확인 → 승인 → push" 순서로 가고 싶다고 하니, Claude Code가 로컬 Jekyll 서버를 띄우는 방향으로 움직였다.

문제는 이 맥에 Homebrew조차 없었다는 것. 설치 과정에서 `/opt` 디렉토리 권한 때문에 sudo 비밀번호가 필요했는데, Claude Code의 터미널은 비대화형이라 비밀번호를 대신 입력해줄 수 없었다. (이건 예전에 WSL2에서 `sudo apt-get install`이 막혔던 것과 정확히 같은 종류의 문제였다.) 결국 Homebrew 설치 명령어만 받아서 내가 직접 터미널에서 비밀번호를 입력해 실행했다.

Homebrew가 깔린 다음엔:
- Homebrew 기본 Ruby(4.0.5)로 `bundle install`을 했더니, Chirpy 테마가 `Ruby ~> 3.1`을 요구해서 버전 충돌이 났다.
- `brew install ruby@3.3`으로 별도 버전을 설치하고 PATH를 그 쪽으로 맞춰서 해결.
- `bundle exec jekyll serve`로 로컬 서버를 띄우니 `localhost:4000`에서 실시간으로 변경사항을 확인할 수 있게 됐다.

다만 `_config.yml` 같은 설정 파일을 바꾼 건 Jekyll의 자동 재생성(`--watch`)이 감지하지 못해서, 서버를 수동으로 재시작해야 했다 — 글 파일 변경과 설정 파일 변경은 반영 방식이 다르다는 걸 알게 됐다.

## 7. 사진 한 장도 그냥 안 들어간다

프로필 사진을 아바타로 쓰고 싶어서 채팅에 사진을 올렸는데, Claude Code가 채팅 속 이미지를 직접 파일로 저장할 방법이 없었다. 결국 다운로드 폴더에 파일로 받아서 경로를 알려주는 식으로 우회했고, 3.8MB짜리 원본은 `sips`로 리사이즈해서 142KB로 줄인 뒤 저장소에 넣었다.

## 8. Public 저장소라는 것의 의미

블로그 소스 저장소가 Public이라는 걸 뒤늦게 짚어보면서, 혹시 토큰이나 민감한 정보가 커밋 히스토리에 남았는지 git log 전체를 grep으로 훑어보기도 했다. 다행히 토큰은 키체인에만 저장되어 저장소에는 흔적이 없었지만, 앞으로 글이나 설정 파일에 회사 정보·실명·내부 시스템명을 적지 않도록 신경 써야 한다는 걸 다시 확인했다.

## 마무리

AI 에이전트에게 "블로그 만들어줘"라고 맡겨도, 권한이 필요한 지점(저장소 생성, Pages 설정, sudo 설치, 토큰 발급)은 결국 사람이 직접 처리해야 했다. 반대로 그 사이의 반복적이고 손이 많이 가는 작업들 — 설정 파일 채우기, 환경 디버깅, 이미지 리사이징, 보안 점검 — 은 대화 몇 마디로 끝났다. "AI가 다 해준다"보다는 "사람이 권한을 쥔 부분과 AI가 처리하는 부분이 명확히 나뉜다"는 감각이 이번 작업에서 가장 분명하게 남았다.
