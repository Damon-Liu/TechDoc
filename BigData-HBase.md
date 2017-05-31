### 架构 ###

```@viz
digraph G {
  Master [shape=box];
  ZooKeeper;
  rs1 [label="Region Server"];
  rs2 [label="Region Server"];
  rs3 [label="Region Server"];
  Master->HDFS;
  rs1 -> HDFS;
  rs2 -> HDFS;
  rs3 -> HDFS;
  rs1 -> Master;
  rs2 -> Master;
  rs3 -> Master;
  rs1 -> ZooKeeper;
  rs2 -> ZooKeeper;
  rs3 -> ZooKeeper;
}
