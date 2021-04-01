---
title: "无限级分类设计-Java实现"
date: 2021-04-01T17:56:54+08:00
draft: false
---

# 数据库设计
```sql
CREATE SCHEMA `ccccye` DEFAULT CHARACTER SET utf8mb4 ;

CREATE TABLE.`prod_category_1` (
  `prod_id` INT NOT NULL,
  `prod_name` VARCHAR(45) NULL,
  `pid` INT NOT NULL,
  PRIMARY KEY (`prod_id`))
ENGINE = InnoDB
DEFAULT CHARACTER SET = utf8mb4;

insert into prod_category_1(prod_id, prod_name, pid) values(1, '水果', 0);
insert into prod_category_1(prod_id, prod_name, pid) values(2, '苹果', 1);
insert into prod_category_1(prod_id, prod_name, pid) values(3, '橙子', 1);
insert into prod_category_1(prod_id, prod_name, pid) values(4, '广西橙子', 3);
insert into prod_category_1(prod_id, prod_name, pid) values(5, '江西橙子', 3);

CREATE TABLE `prod_category_2` (
  `prod_id` INT NOT NULL,
  `prod_name` VARCHAR(45) NULL,
  PRIMARY KEY (`prod_id`));

DROP TABLE IF EXISTS `prod_category_tree`;
CREATE TABLE `prod_category_tree`  (
  `ancestor` int(255) NOT NULL,
  `descendant` int(255) NOT NULL,
  `distance` int(11) NOT NULL,
  PRIMARY KEY (`ancestor`, `descendant`, `distance`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8mb4 COLLATE = utf8mb4_general_ci ROW_FORMAT = Dynamic;

INSERT INTO prod_category_2(prod_id, prod_name) values(1, '水果');
INSERT INTO prod_category_2(prod_id, prod_name) values(2, '苹果');
INSERT INTO prod_category_2(prod_id, prod_name) values(3, '橙子');
INSERT INTO prod_category_2(prod_id, prod_name) values(4, '广西橙子');
INSERT INTO prod_category_2(prod_id, prod_name) values(5, '广东橙子');

INSERT INTO prod_category_tree(ancestor, descendant, distance) values(0, 0, 0);
INSERT INTO prod_category_tree(ancestor, descendant, distance) values(0, 1, 1);
INSERT INTO prod_category_tree(ancestor, descendant, distance) values(1, 1, 0);
INSERT INTO prod_category_tree(ancestor, descendant, distance) values(0, 2, 2);
INSERT INTO prod_category_tree(ancestor, descendant, distance) values(1, 2, 1);
INSERT INTO prod_category_tree(ancestor, descendant, distance) values(2, 2, 0);
INSERT INTO prod_category_tree(ancestor, descendant, distance) values(0, 3, 2);
INSERT INTO prod_category_tree(ancestor, descendant, distance) values(1, 3, 1);
INSERT INTO prod_category_tree(ancestor, descendant, distance) values(3, 3, 0);
INSERT INTO prod_category_tree(ancestor, descendant, distance) values(0, 4, 3);
INSERT INTO prod_category_tree(ancestor, descendant, distance) values(1, 4, 2);
INSERT INTO prod_category_tree(ancestor, descendant, distance) values(3, 4, 1);
INSERT INTO prod_category_tree(ancestor, descendant, distance) values(4, 4, 0);
INSERT INTO prod_category_tree(ancestor, descendant, distance) values(0, 5, 3);
INSERT INTO prod_category_tree(ancestor, descendant, distance) values(1, 5, 2);
INSERT INTO prod_category_tree(ancestor, descendant, distance) values(3, 5, 1);
INSERT INTO prod_category_tree(ancestor, descendant, distance) values(5, 5, 0);
```

# 方案一
**父id**

用类型父id建立关系。

优点：
1. 容易理解设计思路
2. 容易找到结点的上级、下级

缺点：
1. 查找多层下级需要使用递归
2. 查找节点深度需要使用递归



# 方案二
**closure table**

用树结构来存储节点之间的关系。

优点：
1. 查询多级节点一条语句搞定，查询性能不用多说
2. 尽可能的避免递归

缺点：
1. 需要更多的存储空间
2. 设计上会比父id复杂，例如：比父id方案多了一个表专门存储节点关系

# 项目地址
https://gitee.com/ccccye74/example/tree/master/MultiLevel

# 性能测试
**父id和closure table**

场景：1W 5层 每层均匀分布

父id方案测试结果：
- selectList # 1s
- selectListForTree # 1s
- selectPeerById # 0s
- selectChildById # 0s
- deepById # 0s

父id方案   1W 5层的测试结果如上，而且没有索引优化，由此可见多级分类用父id法性能没有想象中的差。
closure table方案  不测试了，父id可读性好的情况下性能也不算差。

# 总结
无限级分类的设计常用的是父id法，在分类数量不多不复杂的场景可以使用。在分类数量特别多的场景建议使用closure table，绝大多数功能一条语句就能查询出来，不过这种方案会增加一个额外的关系表和关系数据，每个节点会记录着从自身到根节点的所有联系并占用空间，在微服务、分布式、大数据的时代为了性能这点存储空间算的了什么呢？场景决定了应该使用什么方案！
