# ArangoDB 

- ArangoDB는 오픈소스 다중 모델 NoSQL DB입니다.
- 하나의 인스턴스에서 문서(Document), 키-값(Key-Value), 그래프(Graph) 데이터를 동시에 처리할 수 있습니다.
- **AQL(ArangoDB Query Language)**이라는 자체 쿼리 언어를 사용합니다 (SQL과 유사)

### 주요특징 

| 항목                                   | 설명                                                        |
| ------------------------------------ | ------------------------------------------------------------- |
| 🔁 **Multi-Model**                   | 문서, 그래프, 키-값 모델을 하나의 DB에서 통합적으로 지원                         |
| 💬 **AQL (ArangoDB Query Language)** | SQL과 유사한 문법의 강력한 쿼리 언어                                    |
| ⚡ **성능**                             | 하나의 저장 엔진으로 모든 모델을 처리하므로 성능 및 일관성 유지                      |
| 🧱 **Scalability**                   | 샤딩(sharding) 및 클러스터 구성이 가능하여 수평 확장 지원                     |
| 🔒 **ACID 지원**                       | 단일 문서 수준에서 ACID 트랜잭션 보장                                   |
| 🚀 **Foxx Microservices**            | ArangoDB 내에서 RESTful API 및 마이크로서비스를 작성 가능 (JavaScript 기반) |


#### 데이터 모델 

- 🔸 Document Store  
  - MongoDB와 유사한 JSON 기반 문서 저장  
  - 컬렉션(Collection)에 저장  

- 🔸 Graph Store  
  - **Vertex(정점)**와 **Edge(간선)**를 컬렉션으로 구성하여 그래프 모델 구성  
  - Named Graph로 그래프를 정의 가능  

- 🔸 Key-Value Store  
  - 단순 키-값 저장으로도 사용 가능 (특별한 설정 없이 document 기반으로 구현 가능)  

## 기본 명령어 

```aql
FOR user IN users
  FILTER user.age > 30
  SORT user.age DESC
  RETURN user.name
```

- SQL 유사 문법
- FOR ~ IN 구문으로 반복 및 탐색
- RETURN, FILTER, LIMIT, SORT 등 SQL 유사 기능 제공

| 항목            | 의미                    | 키워드                                            |
| ------------- | --------------------- | ---------------------------------------------- |
| INBOUND       | 들어오는 간선만 따라 탐색        | `INBOUND`                                      |
| BIDIRECTIONAL | 방향 무시하고 모두 탐색         | `ANY`                                          |
| N단계 탐색        | 관계를 N단계까지 확장          | `1..N`                                         |
| 경로 추적         | 경로 전체(v, e, p)를 함께 추적 | `RETURN p.vertices`, `p.edges`                 |
| 조건부 필터링       | 탐색 대상 속성에 조건 적용       | `FILTER v.xxx == ...` 또는 `FILTER e.xxx == ...` |

#### 1. INBOUND: 역방향(도착 방향) 탐색

- 특정 정점으로 들어오는 엣지를 따라 연결된 정점을 탐색
- 반대 방향의 관계를 찾을 때 사용 (ex: "누가 나를 팔로우하고 있는가")

```aql
FOR v, e IN 1..1 INBOUND "people/bob" GRAPH "social_graph"
RETURN v
```

#### 2. BIDIRECTIONAL: 양방향 탐색

- OUTBOUND + INBOUND 동시에 탐색 (방향 무시)
- 네트워크 그래프, 친구 관계, 추천 시스템 등에서 유용

```aql
FOR v, e IN 1..1 ANY "people/alice" GRAPH "social_graph"
RETURN v
```

#### 3. N단계 탐색: 지정된 깊이까지의 관계 탐색

- 탐색 깊이를 숫자로 지정 (1..3 → 1단계 ~ 3단계까지)
- 친구의 친구, 다단계 연결 분석 등에 사용

```aql
FOR v, e, p IN 1..3 OUTBOUND "people/alice" GRAPH "social_graph"
RETURN v
```

#### 4. 경로 추적 (WITH PATH)

- 탐색 결과뿐 아니라 경로 자체도 함께 반환
- v, e, p를 함께 사용해야 경로 추적 가능 (p는 path 객체)

```aql
FOR v, e, p IN 1..2 OUTBOUND "people/alice" GRAPH "social_graph"
RETURN {
  vertex: v,
  path: p.vertices,
  edges: p.edges
}
```

#### 5. 조건부 필터링 (FILTER)

- 탐색 대상의 속성값을 조건으로 필터링
- 정점 또는 엣지의 필드에 조건을 줄 수 있음

##### AQL 예시 ( 정점 필터 )

```aql
FOR v, e IN 1..2 OUTBOUND "people/alice" GRAPH "social_graph"
  FILTER v.name == "Charlie"
RETURN v
```

##### AQL 예시 ( 엣지 필터 )

```aql
FOR v, e IN 1..2 OUTBOUND "people/alice" GRAPH "social_graph"
  FILTER e.relation == "friend"
RETURN v
```

##### with arangosh

- [with arangosh](https://docs.arangodb.com/3.11/aql/how-to-invoke-aql/with-arangosh/)
- [High-level Operations](https://docs.arangodb.com/3.11/aql/high-level-operations/)