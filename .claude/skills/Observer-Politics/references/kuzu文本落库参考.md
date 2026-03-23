
文本资料在 Neo4j/kuzu 中是怎么存的？

在实际的宏观投研中，文本信息通常会以以下两种方式落库：

**1. 作为节点的属性 (Node Properties)**
当你用 Claude 搜集到一个特定机构的表态时，机构本身是节点，而它的具体言论、长篇公报文本，就可以作为这个节点的属性存起来。

比如，在分析地缘政治冲突时，你可以这样存入一条新闻文本：

```cypher
CREATE (gov:GovernmentAgency {
    name: "美国国务院",
    stance: "Hawkish",
    latest_statement: "我们正在密切关注霍尔木兹海峡的动向，并准备好在必要时采取一切措施保障能源供应链的安全。任何针对盟友的挑衅都将面临严重后果...", // 这里可以存几千字的长文本
    publish_date: "2026-03-07",
    source_url: "https://..."
})

```

**2. 作为关系的属性 (Edge Properties)**
有时候，文本信息是用来解释“为什么 A 会影响 B”的。把逻辑写在“边”上，是做中期宏观驱动投资体系时非常精妙的一招。

例如，你可以建立一个事件驱动模型，把具体的传导逻辑文本存入关系中：

```cypher
MATCH (event:GeopoliticalEvent {name: "美伊局部冲突"}), (commodity:Commodity {name: "燃料油"})
CREATE (event)-[r:IMPACTS_PRICE {
    direction: "Bullish",
    rationale: "冲突升级可能导致中东地区重质原油及燃料油的装船与运输受阻，短期内供给端溢价飙升，从而大幅拉阔 FU05 与 FU09 的日历价差 (Calendar Spread)。"
}]->(commodity)

```