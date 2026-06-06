# 약속지도

친구, 가족, 학부모 모임의 시간별 장소와 이동 경로를 공유하는 정적 웹앱입니다.

## 기능

- 장소 검색과 일정 추가
- 시간대별 장소 목록
- 지도 위 번호 핀과 이동 경로 표시
- 핀 드래그로 위치 수정
- 이동 방법 선택
- 카카오톡 공유 문구 복사
- 네이버 지도와 카카오맵 링크 연결
- Supabase 설정 시 같은 약속 ID로 서버 저장/불러오기

## GitHub Pages 배포

1. GitHub 저장소의 `Settings > Pages`로 이동합니다.
2. Source를 `Deploy from a branch`로 선택합니다.
3. Branch는 `main`, folder는 `/root`를 선택하고 저장합니다.
4. 잠시 후 `https://jiamom.github.io/yaksojido/` 주소로 접속합니다.

## Supabase 저장 기능 설정

GitHub Pages만 쓰면 공유 링크는 스냅샷입니다. 한 사람이 수정한 최신 내용을 모두가 보게 하려면 Supabase 같은 데이터 저장소가 필요합니다.

Supabase에서 새 프로젝트를 만든 뒤 SQL Editor에서 아래 SQL을 실행합니다.

```sql
create table if not exists public.yaksojido (
  id bigint primary key,
  data jsonb not null,
  updated_at timestamptz not null default now()
);

alter table public.yaksojido enable row level security;

create policy "yaksojido plans are readable by link id"
on public.yaksojido
for select
to anon
using (true);

create policy "yaksojido plans can be created by link users"
on public.yaksojido
for insert
to anon
with check (true);

create policy "yaksojido plans can be updated by link users"
on public.yaksojido
for update
to anon
using (true)
with check (true);
```

그다음 `index.html`의 아래 값을 Supabase 프로젝트 값으로 채웁니다.

```js
const SUPABASE_URL = "https://xbvoqrvnyozilurrvvra.supabase.co";
const SUPABASE_ANON_KEY = "public-anon-key";
const SUPABASE_TABLE = "yaksojido";
```

`anon key`는 브라우저 앱에서 쓰도록 설계된 공개 키입니다. 다만 현재 정책은 링크를 아는 사람이 수정할 수 있는 MVP 설정입니다. 나중에 수정 비밀번호나 편집 토큰을 추가하면 더 안전하게 만들 수 있습니다.
