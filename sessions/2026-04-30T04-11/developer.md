# 💻 Developer — 작성된 지식을 Gemma-4에 효율적으로 주입하기 위한 데이터 포맷(JSON/Markdown)을 정의하고, 지식 베이스 자동 업데이트 파이프라인을 설계하세요.

💻 Developer: 작업 시작합니다.

CEO의 지시사항에 따라, Gemma-4의 컨텍스트 주입 효율을 극대화하기 위한 **[Hybrid Data Format]**과 데이터 유입부터 저장까지의 **[Automated Knowledge Pipeline]** 설계를 완료했습니다.

---

### 1. 데이터 포맷 정의 (Dual-Layer Format)

RAG(검retrieval-Augmented Generation)의 검색 성능과 LLM의 추론 성능을 동시에 잡기 위해, **사람이 읽기 좋은 Markdown**과 **시스템이 처리하기 좋은 JSONL**의 하이브리드 구조를 채택합니다.

#### **[Layer 1] Machine-Readable Format (JSONL)**
파이프라인의 중간 저장 및 벡터 DB(ChromaDB/Pinecone) 인덱싱용입니다. 각 라인은 독립된 JSON 객체입니다.

```json
{
  "doc_id": "KB-20240522-TECH-001",
  "metadata": {
    "source": "Anthropic Blog",
    "date": "2024-05-22",
    "domain": "B. Deep Tech",
    "tags": ["AI_Agent", "Workflow"],
    "importance": 0.95
  },
  "content_md": "# [Document ID: KB-20240522-TECH-001] ... (Writer가 정의한 Markdown 내용)",
  "embedding_ref": "vec_idx_001",
  "status": "processed"
}
```

#### **[Layer 2] Contextual Content Format (Markdown)**
`content_md` 필드 내부에 들어갈 실제 데이터입니다. Writer 에이전트가 정의한 구조를 엄격히 준수하여 Gemma-4의 '추론'을 유도합니다.
*(구조: Metadata $\rightarrow$ Context $\rightarrow$ Structured Details $\rightarrow$ Actionable Insights)*

---

### 2. 지식 베이스 자동 업데이트 파이프라인 설계

데이터 수집(Scraping)부터 벡터화(Embedding)까지의 전 과정을 **Event-Driven Architecture**로 설계합니다.

#### **[Pipeline Architecture Diagram]**
`Source (RSS/API)` $\rightarrow$ `Ingestion Worker` $\rightarrow$ `LLM Structurer` $\rightarrow$ `Vector DB` $\rightarrow$ `Gemma-4 Context`

#### **[단계별 상세 설계]**

| 단계 | 컴포넌트 | 역할 및 기술 스택 | 핵심 로직 |
| :--- | :--- | :--- | :--- |
| **1. Ingestion** | **Scraper Agent** | Python (BeautifulSoup, Feedparser), GitHub Actions | RSS 피드, arXiv API, TechCrunch 등을 주기적으로 폴링하여 Raw Data 수집 |
| **2. Processing** | **LLM Structurer** | Gemma-4 (Small/Fast version) | Raw Text를 읽어 Writer의 Markdown 템플릿으로 변환 및 JSONL 구조화 |
| **3. Embedding** | **Vectorizer** | OpenAI `text-embedding-3-small` 또는 HuggingFace BGE | 구조화된 `content_md`를 벡터로 변환하여 고차원 공간에 매핑 |
| **4. Storage** | **Knowledge Base** | ChromaDB (Local) or Pinecone (Cloud) | Metadata와 Vector를 함께 저장하여 Semantic Search 가능하게 구축 |
| **5. Delivery** | **Context Injector** | API Gateway / System Prompt Updater | Gemma-4 호출 시 최신 지식을 RAG로 불러와 Context Window에 주입 |

---

### 3. 구현을 위한 핵심 스크립트 구조 (Pseudo-Code)

```python
import json
from datetime import datetime

class KnowledgePipeline:
    def __init__(self, db_path):
        self.db = self.load_vector_db(db_path)

    def run_pipeline(self):
        # 1. Scrape raw data
        raw_data = scraper.fetch_latest_tech_news() 
        
        for item in raw_data:
            # 2. LLM Structuring (Writer's Template 적용)
            structured_md = llm_structurer.transform(item.text, template="Writer_Template")
            
            # 3. Create JSONL Object
            knowledge_entry = {
                "doc_id": f"KB-{datetime.now().strftime('%Y%m%d')}-{item.id}",
                "metadata": {"source": item.url, "domain": item.category},
                "content_md": structured_md
            }
            
            # 4. Upsert to Vector DB
            self.db.upsert(knowledge_entry)
            self.save_to_jsonl_archive(knowledge_entry)

# 실행 명령: python pipeline_main.py --mode daily_update
```

📊 평가: 완료 — 데이터 포맷 정의와 파이프라인 아키텍처 설계를 마쳤으며, 즉시 구현 가능한 수준임.

📝 다음 단계: Scraper Agent를 위한 구체적인 소스 리스트(RSS/API) 확정 및 Python 기반 Ingestion 스크립트 작성.
