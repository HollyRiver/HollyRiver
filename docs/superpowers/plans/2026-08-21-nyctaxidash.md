# NYCTaxiDash 구현 계획

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** NYC 택시 14,587건(2016 H1)으로 "언제, 어디서 막히는가"에 답하는 정적 인터랙티브 대시보드를 신규 리포 `HollyRiver/NYCTaxiDash`에 빌드하고 GitHub Pages로 배포한다.

**Architecture:** 빌드 타임 Python 전처리(정제 → trips.json / osmnx 도로 플로우 → 8개 GeoJSON 슬라이스) + 순수 정적 프론트(MapLibre GL 지도 + 커스텀 헥스빈 + Plotly.js 차트 + 바닐라 JS 컨트롤). 서버 없음, `docs/`가 Pages 루트.

**Tech Stack:** Python(pandas, numpy, osmnx, networkx, pytest), MapLibre GL JS(CDN), Plotly.js(CDN), 바닐라 JS/CSS.

**참조 스펙:** `C:\Projects\HollyRiver\docs\superpowers\specs\2026-08-21-nyctaxidash-design.md`
**작업 루트:** `C:\Projects\NYCTaxiDash` (Task 1에서 생성)
**문체:** 화면·README·주석의 서술은 명사형/이다체, ~입니다체 금지.
**디자인:** Task 5~9의 시각 요소는 frontend-design 스킬을 참조해 다듬는다. 포트폴리오 토큰(잉크 #1c1c1e, 액센트 #345995)과 톤 통일.

---

## 파일 구조 (최종)

```
NYCTaxiDash/
├── README.md                  # 대외용 (명사형 문체)
├── CLAUDE.md                  # 이후 전용 세션용 프로젝트 컨텍스트
├── .gitignore                 # __pycache__, .pytest_cache, cache/
├── data/
│   └── NYCTaxi.csv            # 원본 동봉 (1.9MB, 링크 소멸 대비)
├── docs/                      # GitHub Pages 루트
│   ├── index.html
│   ├── css/style.css
│   ├── js/main.js
│   └── data/
│       ├── trips.json         # 슬림 전량 레코드 (컬럼 배열 형식)
│       ├── meta.json          # 표본수·기간·제거 건수·KPI
│       ├── landmarks.json     # 랜드마크 라벨 GeoJSON
│       └── flow_{wd|we}_{0..3}.geojson  # 도로 플로우 8슬라이스
├── docs_dev/superpowers/specs/2026-08-21-nyctaxidash-design.md  # 스펙 사본
├── scripts/
│   ├── build_data.py          # CSV → trips.json + meta.json
│   └── build_flows.py         # osmnx 최단경로 → flow GeoJSON 8장
└── tests/
    └── test_build_data.py
```

---

### Task 1: 리포 생성 및 스캐폴드

**Files:** Create: 전체 골격, `data/NYCTaxi.csv`, `.gitignore`, 스펙 사본

- [ ] **Step 1: 리포 생성 + 클론**

```powershell
cd C:\Projects
gh repo create HollyRiver/NYCTaxiDash --public --description "Interactive NYC taxi traffic dashboard - when and where NYC slows down" --clone
```

- [ ] **Step 2: 디렉토리·기본 파일**

```powershell
cd C:\Projects\NYCTaxiDash
New-Item -ItemType Directory -Force data, docs\css, docs\js, docs\data, scripts, tests, docs_dev\superpowers\specs
Copy-Item C:\Users\hollyriver\AppData\Local\Temp\nyctaxi.csv data\NYCTaxi.csv
Copy-Item C:\Projects\HollyRiver\docs\superpowers\specs\2026-08-21-nyctaxidash-design.md docs_dev\superpowers\specs\
```

(임시 CSV가 없으면 `curl -o data/NYCTaxi.csv https://raw.githubusercontent.com/guebin/DV2023/main/posts/NYCTaxi.csv` 후 `wc -l`로 14588행 확인)

`.gitignore`:
```
__pycache__/
.pytest_cache/
cache/
```

- [ ] **Step 3: CLAUDE.md 작성** — 아래 내용 그대로 (이후 전용 세션의 부트스트랩):

```markdown
# NYCTaxiDash

NYC 택시 트래픽 인터랙티브 대시보드. 정적 사이트(GitHub Pages, `docs/` 루트), 서버 없음.

## 구조
- `scripts/build_data.py` — data/NYCTaxi.csv → docs/data/trips.json·meta.json (정제 기준 포함)
- `scripts/build_flows.py` — osmnx 최단경로 → docs/data/flow_*.geojson 8슬라이스 (주중/주말 × 시간대 4구간)
- `docs/` — index.html + css/style.css + js/main.js. 데이터 수정 후 스크립트 재실행 필요.

## 명령
- 데이터 빌드: `python scripts/build_data.py` / `python scripts/build_flows.py` (후자는 osmnx 필요, 수 분 소요)
- 테스트: `python -m pytest tests/ -v`
- 로컬 확인: `python -m http.server 8000 --directory docs` → http://localhost:8000

## 원칙
- 화면·README 서술은 명사형/이다체 종결, ~입니다체 금지
- 디자인 토큰: 잉크 #1c1c1e, 액센트 #345995 (포트폴리오 hollyriver.github.io와 톤 통일)
- 도로 플로우는 8슬라이스 전환 전용, 요일×시간 자유 필터는 헥스빈·차트 담당 (구조적 분업 — 변경 금지)
- 통계적 정직: 표본 미달 셀 반투명, 이상치 제거 기준은 build_data.py 상수로 문서화
- 설계 배경: docs_dev/superpowers/specs/2026-08-21-nyctaxidash-design.md
```

- [ ] **Step 4: README.md 초안** (완성은 Task 10에서 갱신):

```markdown
# NYCTaxiDash

NYC 택시 운행 데이터로 "언제, 어디서 막히는가"에 답하는 인터랙티브 대시보드.

**Live:** https://hollyriver.github.io/NYCTaxiDash/

(빌드 중 — 상세 내용은 완성 후 갱신)
```

- [ ] **Step 5: 커밋·푸시 + Pages 활성화**

```powershell
git add -A; git commit -m 'chore: 리포 스캐폴드 및 원본 데이터 동봉'; git push -u origin main
gh api repos/HollyRiver/NYCTaxiDash/pages -X POST -f build_type=legacy -f "source[branch]=main" -f "source[path]=/docs"
```
Expected: Pages POST가 201 (이미 있으면 409 — 무시).

---

### Task 2: build_data.py — 정제·trips.json (TDD)

**Files:** Create: `scripts/build_data.py`, `tests/test_build_data.py`

- [ ] **Step 1: 실패하는 테스트 작성** — `tests/test_build_data.py`:

```python
import sys, pathlib
sys.path.insert(0, str(pathlib.Path(__file__).resolve().parents[1] / "scripts"))
import numpy as np
import pandas as pd
from build_data import haversine_km, load_clean

def test_haversine_known_distance():
    # 타임스퀘어(40.7580,-73.9855) ~ JFK(40.6413,-73.7781) ≈ 21.7km
    d = haversine_km(np.array([40.7580]), np.array([-73.9855]), np.array([40.6413]), np.array([-73.7781]))
    assert abs(d[0] - 21.7) < 0.5

def test_load_clean_removes_outliers(tmp_path):
    rows = [
        # 정상: 5km/600s = 30km/h
        dict(id="a", pickup_datetime="2016-03-14 08:00:00", dropoff_datetime="2016-03-14 08:10:00",
             passenger_count=1, pickup_longitude=-73.98, pickup_latitude=40.75,
             dropoff_longitude=-73.98, dropoff_latitude=40.795, trip_duration=600),
        # 이상치: 좌표가 NYC 밖
        dict(id="b", pickup_datetime="2016-03-14 08:00:00", dropoff_datetime="2016-03-14 08:10:00",
             passenger_count=1, pickup_longitude=0.0, pickup_latitude=0.0,
             dropoff_longitude=-73.98, dropoff_latitude=40.75, trip_duration=600),
        # 이상치: 30초 트립
        dict(id="c", pickup_datetime="2016-03-14 08:00:00", dropoff_datetime="2016-03-14 08:00:30",
             passenger_count=1, pickup_longitude=-73.98, pickup_latitude=40.75,
             dropoff_longitude=-73.97, dropoff_latitude=40.76, trip_duration=30),
    ]
    p = tmp_path / "t.csv"
    pd.DataFrame(rows).to_csv(p, index=False)
    df = load_clean(str(p))
    assert list(df.id) == ["a"]
    assert 25 < df.speed_kmh.iloc[0] < 35
    assert df.dow.iloc[0] == 0 and df.hour.iloc[0] == 8
```

- [ ] **Step 2: 실행해 실패 확인** — `python -m pytest tests/ -v` → FAIL (`ModuleNotFoundError: build_data`)

- [ ] **Step 3: 구현** — `scripts/build_data.py`:

```python
"""data/NYCTaxi.csv → docs/data/trips.json + meta.json.

정제 기준 (대시보드 푸터에도 명시):
- NYC 대역 밖 좌표 제거 (BBOX)
- 60초 미만 트립 제거
- 직선거리 0.05km 이하 / 60km 초과 제거
- 직선거리 기반 평균속력 120km/h 초과 제거
속력은 haversine 직선거리 / 소요시간 — 실주행 거리 기반이 아닌 하한 추정치.
"""
import json
import numpy as np
import pandas as pd

RAW = "data/NYCTaxi.csv"
OUT_TRIPS = "docs/data/trips.json"
OUT_META = "docs/data/meta.json"

BBOX = dict(lat_min=40.50, lat_max=41.00, lon_min=-74.30, lon_max=-73.60)
MIN_DURATION_S = 60
MIN_DIST_KM, MAX_DIST_KM = 0.05, 60.0
MAX_SPEED_KMH = 120.0

def haversine_km(lat1, lon1, lat2, lon2):
    R = 6371.0088
    p1, p2 = np.radians(lat1), np.radians(lat2)
    a = np.sin((p2 - p1) / 2) ** 2 + np.cos(p1) * np.cos(p2) * np.sin(np.radians(lon2 - lon1) / 2) ** 2
    return 2 * R * np.arcsin(np.sqrt(a))

def load_clean(path=RAW):
    df = pd.read_csv(path, parse_dates=["pickup_datetime", "dropoff_datetime"])
    df["dist_km"] = haversine_km(df.pickup_latitude.values, df.pickup_longitude.values,
                                 df.dropoff_latitude.values, df.dropoff_longitude.values)
    df["speed_kmh"] = df.dist_km / (df.trip_duration / 3600.0)
    def in_bbox(lat, lon):
        return lat.between(BBOX["lat_min"], BBOX["lat_max"]) & lon.between(BBOX["lon_min"], BBOX["lon_max"])
    mask = (in_bbox(df.pickup_latitude, df.pickup_longitude)
            & in_bbox(df.dropoff_latitude, df.dropoff_longitude)
            & (df.trip_duration >= MIN_DURATION_S)
            & df.dist_km.between(MIN_DIST_KM, MAX_DIST_KM, inclusive="neither")
            & (df.speed_kmh <= MAX_SPEED_KMH))
    df = df[mask].copy()
    df["dow"] = df.pickup_datetime.dt.dayofweek
    df["hour"] = df.pickup_datetime.dt.hour
    return df

def main():
    raw_n = len(pd.read_csv(RAW, usecols=["id"]))
    df = load_clean()
    trips = dict(
        plat=df.pickup_latitude.round(4).tolist(), plon=df.pickup_longitude.round(4).tolist(),
        dlat=df.dropoff_latitude.round(4).tolist(), dlon=df.dropoff_longitude.round(4).tolist(),
        dow=df.dow.tolist(), hr=df.hour.tolist(),
        v=df.speed_kmh.round(1).tolist(), km=df.dist_km.round(2).tolist(),
    )
    slowest = df.groupby("hour").speed_kmh.mean().idxmin()
    meta = dict(
        n=len(df), n_raw=raw_n, n_dropped=raw_n - len(df),
        period=[str(df.pickup_datetime.min().date()), str(df.pickup_datetime.max().date())],
        avg_speed=round(df.speed_kmh.mean(), 1), avg_dist=round(df.dist_km.mean(), 2),
        slowest_hour=int(slowest),
    )
    with open(OUT_TRIPS, "w") as f:
        json.dump(trips, f, separators=(",", ":"))
    with open(OUT_META, "w", encoding="utf-8") as f:
        json.dump(meta, f, ensure_ascii=False)
    print(f"kept {meta['n']}/{raw_n}, dropped {meta['n_dropped']}")

if __name__ == "__main__":
    main()
```

- [ ] **Step 4: 테스트 통과 확인** — `python -m pytest tests/ -v` → 2 PASS

- [ ] **Step 5: 실행·산출 검증**

```powershell
python scripts/build_data.py
```
Expected: `kept 14xxx/14587` (제거 수백 건 이내), `docs/data/trips.json` 존재 (~1MB 내외).

- [ ] **Step 6: 커밋** — `git add -A; git commit -m 'feat: 데이터 정제 및 trips.json 빌드 스크립트'`

---

### Task 3: landmarks.json

**Files:** Create: `docs/data/landmarks.json`

- [ ] **Step 1: 랜드마크 GeoJSON 작성** (지도 방향감용, 8곳):

```json
{"type":"FeatureCollection","features":[
{"type":"Feature","properties":{"name":"Manhattan"},"geometry":{"type":"Point","coordinates":[-73.9712,40.7831]}},
{"type":"Feature","properties":{"name":"Central Park"},"geometry":{"type":"Point","coordinates":[-73.9665,40.7812]}},
{"type":"Feature","properties":{"name":"Times Square"},"geometry":{"type":"Point","coordinates":[-73.9855,40.7580]}},
{"type":"Feature","properties":{"name":"Wall St"},"geometry":{"type":"Point","coordinates":[-74.0088,40.7061]}},
{"type":"Feature","properties":{"name":"Brooklyn"},"geometry":{"type":"Point","coordinates":[-73.9442,40.6782]}},
{"type":"Feature","properties":{"name":"Queens"},"geometry":{"type":"Point","coordinates":[-73.7949,40.7282]}},
{"type":"Feature","properties":{"name":"JFK"},"geometry":{"type":"Point","coordinates":[-73.7781,40.6413]}},
{"type":"Feature","properties":{"name":"LaGuardia"},"geometry":{"type":"Point","coordinates":[-73.8740,40.7769]}}
]}
```

- [ ] **Step 2: 커밋** — `git add -A; git commit -m 'feat: 지도 랜드마크 라벨 데이터'`

---

### Task 4: build_flows.py — 도로 플로우 8슬라이스

**Files:** Create: `scripts/build_flows.py`

- [ ] **Step 1: osmnx 설치 확인**

```powershell
pip install osmnx scikit-learn
python -c "import osmnx; print(osmnx.__version__)"
```
설치 실패(휠 없음 등) 시: **이 Task 전체를 건너뛰고 Task 7에서 플로우 UI를 숨김 처리** (스펙의 폴백 경로). 이후 재시도 가능.

- [ ] **Step 2: 구현** — `scripts/build_flows.py`:

```python
"""트립별 도로망 최단경로 → 세그먼트 통행량 GeoJSON 8슬라이스.

슬라이스: 주중(wd)/주말(we) × 시간대 0:새벽(0-6) 1:오전(6-12) 2:오후(12-18) 3:저녁(18-24)
한계(대시보드에 명시): 최단경로는 실주행 경로가 아닌 추정.
실행: python scripts/build_flows.py  (그래프 다운로드 포함 수 분~수십 분)
"""
import json
from collections import Counter
import numpy as np
import osmnx as ox

from build_data import load_clean

BBOX = (-74.05, 40.60, -73.75, 40.88)  # (west, south, east, north) 핵심 운행 대역
MIN_COUNT = 3          # 이 미만 통행 세그먼트는 출력 제외 (용량·노이즈 컷)
CACHE_GRAPH = "cache/nyc_drive.graphml"

def get_graph():
    import os
    if os.path.exists(CACHE_GRAPH):
        return ox.load_graphml(CACHE_GRAPH)
    G = ox.graph_from_bbox(BBOX, network_type="drive", simplify=True)
    os.makedirs("cache", exist_ok=True)
    ox.save_graphml(G, CACHE_GRAPH)
    return G

def main():
    df = load_clean()
    # 플로우 대역 밖(JFK 동측 등) 트립은 최근접 노드 왜곡을 피하기 위해 제외
    m = (df.pickup_longitude.between(BBOX[0], BBOX[2]) & df.pickup_latitude.between(BBOX[1], BBOX[3])
         & df.dropoff_longitude.between(BBOX[0], BBOX[2]) & df.dropoff_latitude.between(BBOX[1], BBOX[3]))
    df = df[m].reset_index(drop=True)
    G = get_graph()
    orig = ox.distance.nearest_nodes(G, X=df.pickup_longitude.values, Y=df.pickup_latitude.values)
    dest = ox.distance.nearest_nodes(G, X=df.dropoff_longitude.values, Y=df.dropoff_latitude.values)
    routes = ox.routing.shortest_path(G, orig, dest, weight="length", cpus=None)

    wp = np.where(df.dow < 5, "wd", "we")
    band = (df.hour // 6).values
    counters = {(w, b): Counter() for w in ("wd", "we") for b in range(4)}
    for i, r in enumerate(routes):
        if r is None or len(r) < 2:
            continue
        c = counters[(wp[i], band[i])]
        for u, v in zip(r[:-1], r[1:]):
            c[(u, v)] += 1

    for (w, b), counter in counters.items():
        feats = []
        for (u, v), n in counter.items():
            if n < MIN_COUNT:
                continue
            data = min(G.get_edge_data(u, v).values(), key=lambda d: d.get("length", 0))
            if "geometry" in data:
                coords = [[round(x, 5), round(y, 5)] for x, y in data["geometry"].coords]
            else:
                coords = [[round(G.nodes[u]["x"], 5), round(G.nodes[u]["y"], 5)],
                          [round(G.nodes[v]["x"], 5), round(G.nodes[v]["y"], 5)]]
            feats.append({"type": "Feature", "properties": {"n": n},
                          "geometry": {"type": "LineString", "coordinates": coords}})
        out = {"type": "FeatureCollection", "features": feats}
        path = f"docs/data/flow_{w}_{b}.geojson"
        with open(path, "w") as f:
            json.dump(out, f, separators=(",", ":"))
        print(path, len(feats), "segments")

if __name__ == "__main__":
    main()
```

- [ ] **Step 3: 실행** — `python scripts/build_flows.py` (장시간: run_in_background 사용).
Expected: `docs/data/flow_*.geojson` 8개, 각 수천 세그먼트, 파일당 수백 KB~2MB. 2MB 초과 슬라이스가 있으면 MIN_COUNT를 4~5로 올려 재실행.

- [ ] **Step 4: 커밋** — `git add -A; git commit -m 'feat: osmnx 도로망 통행량 8슬라이스 빌드'`

---

### Task 5: 프론트 뼈대 — index.html + style.css

**Files:** Create: `docs/index.html`, `docs/css/style.css`

frontend-design 스킬을 읽고 진행. 핵심 요구:

- [ ] **Step 1: index.html 구조** — 시맨틱 골격 (CDN: MapLibre GL 4.x css+js, Plotly.js 2.x):

```html
<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8"><meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>NYC Taxi Traffic — 언제, 어디서 막히는가</title>
<meta name="description" content="NYC 택시 14,587건으로 본 요일·시간·지역별 트래픽 대시보드">
<link href="https://unpkg.com/maplibre-gl@4.7.1/dist/maplibre-gl.css" rel="stylesheet">
<link rel="stylesheet" href="css/style.css">
</head>
<body>
<header class="hero">
  <h1>NYC Taxi Traffic</h1>
  <p class="sub">2016년 상반기 표본 <span id="kpi-n">—</span>건으로 본 "언제, 어디서 막히는가"</p>
</header>
<section id="kpis" class="kpis"><!-- JS가 meta.json으로 채움: 총 트립·평균속력·최저속 시간대·평균거리 --></section>
<section class="controls">
  <div class="ctl-days">
    <button data-day="0">월</button> … <button data-day="6">일</button>
    <button id="days-all">전체 선택</button><button id="days-none">해제</button>
  </div>
  <div class="ctl-hour"><input type="range" id="hour" min="-1" max="23" value="-1"><span id="hour-label">전체 시간</span></div>
  <div class="ctl-metric"><button data-metric="count" class="on">트립 밀도</button><button data-metric="speed">평균 속력</button></div>
  <div class="ctl-layer"><button data-layer="hex" class="on">헥스빈</button><button data-layer="flow">도로 플로우</button></div>
  <div class="ctl-flow" hidden><!-- 주중/주말 + 시간대 4버튼 --></div>
</section>
<main><div id="map"></div></main>
<section class="charts"><div id="chart-heat"></div><div id="chart-hour"></div></section>
<section class="notes" id="insights"><!-- 인사이트 노트 --></section>
<footer class="foot"><!-- 출처·정제 기준·한계·Claude Code 페어링·리포 링크 --></footer>
<script src="https://unpkg.com/maplibre-gl@4.7.1/dist/maplibre-gl.js"></script>
<script src="https://cdn.plot.ly/plotly-2.35.2.min.js"></script>
<script src="js/main.js"></script>
</body>
</html>
```

- [ ] **Step 2: style.css** — 포트폴리오 토큰 재사용(--ink #1c1c1e, --accent #345995), 지도 높이 62vh 이상, 컨트롤은 pill 버튼(.on 상태 액센트 채움), 모바일 1열 스택. 상세 시각은 frontend-design 스킬 원칙으로 다듬기.

- [ ] **Step 3: 로컬 서버로 골격 확인** — `python -m http.server 8000 --directory docs` (background) 후 헤드리스 크롬 스크린샷으로 레이아웃 확인.

- [ ] **Step 4: 커밋** — `git add -A; git commit -m 'feat: 대시보드 HTML/CSS 뼈대'`

---

### Task 6: main.js — 상태·헥스빈·지도

**Files:** Create: `docs/js/main.js`

- [ ] **Step 1: 전역 상태와 데이터 로드**

```js
const state = { days: new Set([0,1,2,3,4,5,6]), hour: null, metric: "count", layer: "hex", flowWp: "wd", flowBand: 1 };
const D = {};   // trips 컬럼 배열
let META = {};
async function loadData() {
  [D.t, META] = await Promise.all([
    fetch("data/trips.json").then(r => r.json()),
    fetch("data/meta.json").then(r => r.json()),
  ]);
}
```

- [ ] **Step 2: 헥스빈 구현** (pointy-top axial, 승차 좌표 기준, ~350m 셀):

```js
const COS0 = Math.cos(40.75 * Math.PI / 180);
const R_HEX = 0.0032; // 위도度 단위 반지름 ≈ 350m
function hexKey(lat, lon) {
  const x = lon * COS0 / R_HEX, y = lat / R_HEX;
  let q = (Math.sqrt(3) / 3 * x - y / 3), r = (2 / 3 * y);
  // cube rounding
  let s = -q - r, rq = Math.round(q), rr = Math.round(r), rs = Math.round(s);
  const dq = Math.abs(rq - q), dr = Math.abs(rr - r), ds = Math.abs(rs - s);
  if (dq > dr && dq > ds) rq = -rr - rs; else if (dr > ds) rr = -rq - rs;
  return rq + "," + rr;
}
function hexCenter(key) {
  const [q, r] = key.split(",").map(Number);
  const x = Math.sqrt(3) * (q + r / 2) * R_HEX, y = 1.5 * r * R_HEX;
  return [x / COS0, y]; // [lon, lat]
}
function hexPolygon(center) {
  const pts = [];
  for (let i = 0; i < 7; i++) {
    const a = Math.PI / 180 * (60 * i - 30);
    pts.push([center[0] + R_HEX * Math.cos(a) / COS0, center[1] + R_HEX * Math.sin(a)]);
  }
  return pts;
}
```

- [ ] **Step 3: 필터·집계** — `state.days`/`state.hour`로 인덱스 필터 → 셀별 {n, 평균속력} 집계 → GeoJSON. **n < 5 셀은 opacity 0.25** (통계적 정직):

```js
function aggregate() {
  const cells = new Map();
  const { plat, plon, dow, hr, v } = D.t;
  for (let i = 0; i < plat.length; i++) {
    if (!state.days.has(dow[i])) continue;
    if (state.hour !== null && hr[i] !== state.hour) continue;
    const k = hexKey(plat[i], plon[i]);
    let c = cells.get(k);
    if (!c) cells.set(k, c = { n: 0, sv: 0 });
    c.n++; c.sv += v[i];
  }
  return { type: "FeatureCollection", features: [...cells].map(([k, c]) => ({
    type: "Feature",
    properties: { n: c.n, speed: +(c.sv / c.n).toFixed(1), faint: c.n < 5 ? 1 : 0 },
    geometry: { type: "Polygon", coordinates: [hexPolygon(hexCenter(k))] },
  }))};
}
```

- [ ] **Step 4: MapLibre 초기화** — CARTO Positron GL 스타일, 로드 후 수변/공원 대비 강화(setPaintProperty), 랜드마크 symbol 레이어(작은 회색 라벨), hex fill 레이어(색: metric에 따라 n 또는 speed 보간, fill-opacity: faint면 0.25 아니면 0.75), hover 팝업(트립 수·평균 속력). 지도 중심 [-73.95, 40.74], zoom 10.7.

- [ ] **Step 5: 컨트롤 바인딩** — 요일 버튼 토글(멀티)·전체/해제, 시간 슬라이더(-1=전체), 지표 전환. 변경 시 `map.getSource("hex").setData(aggregate())` + 차트 갱신 호출.

- [ ] **Step 6: 로컬 검증** — 스크린샷: 기본 상태, 월요일만+8시, 속력 지표 3장 확인 (헥스 분포·색·반투명 셀 동작).

- [ ] **Step 7: 커밋** — `git add -A; git commit -m 'feat: 헥스빈 지도와 요일·시간 필터'`

---

### Task 7: 도로 플로우 레이어

**Files:** Modify: `docs/js/main.js`, `docs/index.html`(.ctl-flow 내부)

- [ ] **Step 1: 슬라이스 로더·레이어** — flow_{wp}_{band}.geojson lazy fetch + 캐시. line 레이어: line-width = interpolate(n): 3→0.6px, 상위→5px; line-color 액센트 계열(밝기도 n 연동); layer 전환 시 hex/flow visibility 토글, 컨트롤 표시 전환(플로우 모드에선 요일 버튼·슬라이더 비활성 + "플로우는 주중/주말×시간대 단위" 안내 문구).

- [ ] **Step 2: 플로우 슬라이스 컨트롤** — 주중/주말 토글 + 시간대 4버튼(새벽/오전/오후/저녁).

- [ ] **Step 3: 로컬 검증** — 주중 오전 vs 주말 새벽 스크린샷 비교 (미드타운 도로가 두꺼워지는지).

- [ ] **Step 4: 커밋** — `git add -A; git commit -m 'feat: 도로망 통행량 플로우 레이어'`

(Task 4를 건너뛴 경우: .ctl-layer의 플로우 버튼을 hidden 처리하고 이 Task는 커밋 없이 종료)

---

### Task 8: Plotly 보조 차트 + KPI

**Files:** Modify: `docs/js/main.js`, `docs/index.html`

- [ ] **Step 1: 요일×시간 평균 속력 히트맵** — 필터와 무관하게 전체 데이터 기준(전역 패턴 제시용), Plotly heatmap, 액센트 계열 colorscale, 셀 hover에 표본수 표시.

- [ ] **Step 2: 시간대별 트립 수 막대** — `state.days` 필터 반영(선택 요일 합), 현재 슬라이더 시간 하이라이트.

- [ ] **Step 3: KPI 채우기** — meta.json으로 총 트립·평균 속력·최저속 시간대·평균 거리 4칸.

- [ ] **Step 4: 로컬 검증 + 커밋** — `git add -A; git commit -m 'feat: 보조 차트와 KPI 스트립'`

---

### Task 9: 인사이트·푸터·마감 검수

**Files:** Modify: `docs/index.html`, `docs/css/style.css`

- [ ] **Step 1: 인사이트 노트** — 빌드된 실데이터에서 확인한 관찰 3~4개를 명사형 문체로 (예: 최저속 시간대, 주중/주말 대비, 공항 노선 패턴). **실제 집계값을 확인해 작성 — 추정 서술 금지.**

- [ ] **Step 2: 푸터** — 데이터 출처(Kaggle NYC Taxi Trip Duration 표본, 2016 H1, n=…), 정제 기준 요약, 한계(직선거리 속력은 하한 추정·최단경로는 실주행 아님·2016 표본), "Claude Code와 페어링으로 제작" + 리포 링크.

- [ ] **Step 3: 문체 검수** — `grep -n "습니다\|입니다" docs/index.html docs/js/main.js` → 0건.

- [ ] **Step 4: 모바일 확인** — 375px 뷰포트 스크린샷, 컨트롤 줄바꿈·지도 높이 확인.

- [ ] **Step 5: 커밋** — `git add -A; git commit -m 'feat: 인사이트 노트·푸터·모바일 대응'`

---

### Task 10: README 완성·배포 검증

**Files:** Modify: `README.md`

- [ ] **Step 1: README 전면 작성** (명사형 문체) — 개요·라이브 링크·스크린샷 1장(docs/assets/preview.png, 헤드리스 캡처)·주요 기능(두 레이어 분업 표 포함)·데이터 출처와 정제 기준·한계·빌드 방법(scripts 2종)·"Claude Code 페어링으로 리뉴얼" 한 줄.

- [ ] **Step 2: 푸시·Pages 확인**

```powershell
git push
```
배포 폴링: `until curl -s https://hollyriver.github.io/NYCTaxiDash/ | grep -q "NYC Taxi Traffic"; do sleep 15; done` (run_in_background)

- [ ] **Step 3: 라이브 스모크 테스트** — 라이브 URL 헤드리스 스크린샷으로 지도·차트 렌더 확인 (데이터 fetch가 상대경로로 동작하는지가 핵심).

- [ ] **Step 4: 최종 커밋·푸시**

---

## 후속 작업 (이 계획 밖, 완성 후 별도 진행)

- 포트폴리오 dash 카드 → NYCTaxiDash 리뉴얼 반영 (제목·본문·링크·태그)
- 프로필 README의 대시보드 링크 갱신
- (선택) velog 리뉴얼 과정기
