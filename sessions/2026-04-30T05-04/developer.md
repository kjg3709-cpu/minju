# 💻 Developer — 리서치된 고품질 코딩 데이터를 수집·처리하는 지식 파이프라인 구축 및 실시간 검색 도구와 연동된 코딩 에이전트 아키텍처 구현

💻 Developer: 작업 시작합니다.

CEO의 지시사항인 **'고품질 코딩 데이터 파이프라인'**과 **'실시간 검색 연동 코딩 에이전트 아키텍처'**를 구현하기 위한 엔지니어링 설계도입니다. Researcher의 기술 분석과 Business의 로드맵을 실행 가능한 기술 스택과 아키텍테처로 구체화하였습니다.

---

# 🛠️ Gemma-4 Coding Intelligence Upgrade: System Architecture

## 1. Knowledge Pipeline: High-Density Data Ingestion
단순 크롤링을 넘어, '문제-해결-테스트' 구조를 가진 고밀도 데이터를 생성하는 파이프라인입니다.

### [Data Flow]
1.  **Source Ingestion (Scrapers)**
    *   `Web-Search Scraper`: Tavily API 또는 Serper API를 사용하여 최신 라이브러리 문서, StackOverflow, GitHub Issues 수집.
    *   `Repo-Miner`: GitHub API를 통해 특정 기술 스택의 우수 레포지토리(Star 수 기반)에서 `.py`, `.js` 등 핵심 로직 추출.
2.  **Processing Layer (The Refiner)**
    *   `Markdown Converter`: HTML/JSON을 LLM이 이해하기 쉬운 구조화된 Markdown으로 변환.
    *   `Logic Extractor (LLM-based)`: 수집된 코드에서 **[Problem] - [Implementation] - [Unit Test] - [Complexity Analysis]** 패턴을 추출하여 정형화된 JSON/Markdown 포맷으로 재구성.
    *   `Synthetic Data Generator`: 기존 코드를 기반으로 에러 케이스(Edge Case)를 생성하여 데이터 증강(Augmentation).
3.  **Storage Layer (Dual-Store)**
    *   `Vector DB (ChromaDB/Pinecone)`: 코드 스니펫의 Semantic Embedding 저장 (RAG용).
    **`Metadata DB (PostgreSQL)`**: 코드 버전, 라이브러리 의존성, 테스트 통과 여부, 출처(URL) 저장.

## 2. Agentic Architecture: ReAct-based Coding Agent
Gemma-4가 스스로 도구를 사용하여 코드를 작성하고 검증하는 루프 구조입니다.

### [Core Loop: ReAct (Reasoning + Acting)]
에이전트는 다음 4단계 사이클을 무한 반복하거나 종료 조건(성공/실패)에 도달할 때까지 수행합니다.

1.  **Thought (Reasoning)**: 현재 요구사항 분석 및 필요한 도구(Search, Python, File) 결정.
2.  **Action (Tool Use)**:
    *   `search_web(query)`: 최신 API 스펙 및 에러 해결책 검색.
    *   `read_docs(library_name)`: RAG를 통해 저장된 지식 베이스에서 컨텍스트 추출.
    *   `write_code(filename, code)`: 로컬 샌드박스 내 파일 생성.
3.  **Observation (Execution & Feedback)**:
    *   `execute_python(filename)`: **Docker Sandbox** 내에서 코드 실행.
    *   `check_test(test_file)`: 생성된 Unit Test 실행 및 `Stdout/Stderr` 획수.
4.  **Self-Correction (Refinement)**: 에러 로그(Traceback)를 `Thought` 단계로 다시 피드백하여 코드 수정.

## 3. Technical Stack Blueprint

| Layer | Technology | Purpose |
| :--- | :--- | :--- |
| **Orchestration** | **LangGraph** | 에이전트의 순환 구조(Loop) 및 상태 관리(State Management) 제어 |
| **Search Engine** | **Tavily API** | AI 에이전트 최적화 검색 엔진 (LLM 친화적 결과 반환) |
| **Execution Env** | **Docker (Python Sandbox)** | 격리된 환경에서 안전한 코드 실행 및 시스템 오염 방지 |
| **Vector Engine** | **ChromaDB** | 로컬 기반의 경량 고성능 벡터 스토어 |
| **Data Format** | **JSONL / Markdown** | 학습 및 프롬프트 주입을 위한 표준 데이터 포맷 |

## 4. Implementation Roadmap (Immediate Action)

### [Step 1] Prototype: Ingestion Script 개발
*   `Tavily`를 연동하여 특정 키워드(예: "FastAPI Pydantic v2 migration")에 대한 검색 결과를 Markdown으로 저장하는 스크립트 작성.

### [Step 2] Sandbox: Python Executor 구축
*   `subprocess` 또는 `Docker SDK`를 사용하여 에이전트가 작성한 코드를 격리된 환경에서 실행하고 에러를 캡처하는 모듈 구현.

### [Step 3] Integration: LangGraph 기반 ReAct Loop 구현
*   `Search -> Code -> Test -> Error? -> Loop` 흐름을 가진 Graph 에이전트 설계.

---

📊 평가: 완료 — 설계 및 아키텍처 정의 완료. 실행 가능한 기술 스택과 데이터 흐름을 확정함.

📝 다음 단계: `Step 1`에 해당하는 'Tavily 기반 고품질 데이터 수집 스크립트(Python)' 초안 작성 및 구현.
