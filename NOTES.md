# 블로그 작성 규칙 (Claude Code와 협업 시 참고)

이 블로그의 글은 Claude Code(나)를 통해 작성한다. 다음 규칙을 항상 지킨다.

## 1. 글 날짜는 항상 현재 날짜/시각으로 작성

- frontmatter의 `date`는 글을 실제로 push하는 시점의 현재 날짜/시각으로 작성한다.
- Jekyll은 기본 설정상 미래 날짜로 적힌 글을 빌드에서 제외하고 숨긴다. 날짜를 실수로 미래로 적으면 push와 빌드가 성공해도 사이트에 글이 보이지 않는다.
- 글 작성 전 `date` 명령으로 현재 시각을 확인하고 frontmatter에 반영한다.
- 파일명(`YYYY-MM-DD-제목.md`)의 날짜도 frontmatter의 `date`와 일치시킨다.

## 2. 카테고리 체계

- `categories: [AI 활용, Claude Agent]` — Claude 에이전트로 작업한 기록용 카테고리(상위: AI 활용, 하위: Claude Agent).
- 그 외 일상/기타 글은 상황에 맞는 카테고리를 새로 정한다.
