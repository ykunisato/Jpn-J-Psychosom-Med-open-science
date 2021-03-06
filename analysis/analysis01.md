---
title: "BioPsychoSocial Medicineにおけるオープンサイエンスの現状"
author: "国里愛彦（専修大学人間科学部心理学科），遠山朝子（専修大学大学院文学研究科心理学専攻・日本学術振興会特別研究員PD）"
output: 
  html_document:
    toc: true
    toc_depth: 2
    toc_float: true
    number_section: true
    keep_md: true
---
## 使用するRパッケージ


```r
library(tidyverse)
library(ggrepel)
library(gridExtra)
library(knitr)
```


## データの読み込み


```r
setwd("../data")
data <- read_csv("data.csv")
```

## データ内の変数

著者ら（国里・遠山）は，2019年と2020年にBioPsychoSocial Medicineに掲載された論文における，オープンサイエンス実践の状況を調査した。個々の論文の調査は，２名が独立に実施してから，統合した。独立に評価したファイルは「data」フォルダ内の「data_extraction」内にあり，統合したファイルは「data」フォルダ内のdata.csvである。以下の解析では，data.csvを用いる。data.csv内の変数と説明は以下の通りである。

- paper: 論文の引用情報
- type: 論文種別(Case report, Commentary, Correction, Editorial, Research article, Review) 
- with_data: データを用いた研究か(1 = データを用いている, 0 = データを用いてない(Reviewやメタ分析は0とする))
- pre_registered： 事前登録状況(事前登録なし，事前登録あり，事後登録あり)
- where_pre_registered：事前登録に用いたサイト
- share_data：データ共有状況(共有なし，外部リポジトリ， 著者問い合わせ，雑誌ウェブサイト，雑誌ウェブサイト(データなし）)
- share_code：コード共有状況(共有なし，著者問い合わせ)


## 論文種別

調査対象論文の論文種別は以下のようになる。Research article(40本)の内，データを用いた研究（メタ分析は除く）38本を対象に以降の検討を行う。


```r
kable(table(data$type))
```



|Var1             | Freq|
|:----------------|----:|
|Case report      |    5|
|Commentary       |    1|
|Correction       |    2|
|Editorial        |    1|
|Research article |   40|
|Review           |   12|

## 事前登録

事前登録状況を円グラフで示す。


```r
data %>% 
  filter(with_data == 1) %>% 
  select(pre_registered) %>% 
  mutate(n = 1) %>% 
  group_by(pre_registered) %>% 
  summarize(total = sum(n)) %>% 
  mutate(percentage = total/sum(total)) %>% 
  ggplot(aes(x = "", y = total, fill = reorder(pre_registered, -total))) +
  geom_bar(width = 1, stat = "identity", color = "white") +
  coord_polar(theta = "y", direction = -1) +
  theme_bw(base_family = "Noto Sans CJK JP") +
  labs(fill = "事前登録") +
  theme(axis.ticks = element_blank(),
        axis.text = element_blank(),
        panel.grid = element_blank(),
        panel.border = element_blank()) +
  xlab("") + ylab("") +
  geom_text(aes(x = 1, label = sprintf("%0.2f", round(percentage, digits = 2))), position = position_stack(vjust = 0.5))
```

![](analysis01_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

## 事前登録で用いたリポジトリ

事前（もしくは事後）登録をしていた研究の内，事前登録で用いたリポジトリの使用状況を円グラフで示す。


```r
data %>% 
  filter(with_data == 1 & pre_registered != "事前登録なし") %>% 
  select(where_pre_registered) %>% 
  mutate(n = 1) %>% 
  group_by(where_pre_registered) %>% 
  summarize(total = sum(n)) %>% 
  mutate(percentage = total/sum(total)) %>% 
  ggplot(aes(x = "", y = total, fill = reorder(where_pre_registered, -total))) +
  geom_bar(width = 1, stat = "identity", color = "white") +
  coord_polar(theta = "y", direction = -1) +
  theme_bw(base_family = "Noto Sans CJK JP") +
  labs(fill = "登録リポジトリ") +
  theme(axis.ticks = element_blank(),
        axis.text = element_blank(),
        panel.grid = element_blank(),
        panel.border = element_blank()) +
  xlab("") + ylab("") +
  geom_text(aes(x = 1, label = sprintf("%0.2f", round(percentage, digits = 2))), position = position_stack(vjust = 0.5))
```

![](analysis01_files/figure-html/unnamed-chunk-5-1.png)<!-- -->


## データ共有

データ共有状況を円グラフで示す。


```r
data %>% 
  filter(with_data == 1) %>% 
  select(share_data) %>% 
  mutate(n = 1) %>% 
  group_by(share_data) %>% 
  summarize(total = sum(n)) %>% 
  mutate(percentage = total/sum(total)) %>% 
  ggplot(aes(x = "", y = total, fill = reorder(share_data, -total))) +
  geom_bar(width = 1, stat = "identity", color = "white") +
  coord_polar(theta = "y", direction = -1) +
  theme_bw(base_family = "Noto Sans CJK JP") +
  labs(fill = "データ共有") +
  theme(axis.ticks = element_blank(),
        axis.text = element_blank(),
        panel.grid = element_blank(),
        panel.border = element_blank()) +
  xlab("") + ylab("") +
  geom_text_repel(aes(x = 1, label = sprintf("%0.2f", round(percentage, digits = 2))), position = position_stack(vjust = 0.5))
```

![](analysis01_files/figure-html/unnamed-chunk-6-1.png)<!-- -->

## コード共有

コード共有状況を円グラフで示す。


```r
data %>% 
  filter(with_data == 1) %>% 
  select(share_code) %>% 
  mutate(n = 1) %>% 
  group_by(share_code) %>% 
  summarize(total = sum(n)) %>% 
  mutate(percentage = total/sum(total)) %>%
  ggplot(aes(x = "", y = total, fill = reorder(share_code, -total))) +
  geom_bar(width = 1, stat = "identity", color = "white") +
  coord_polar(theta = "y", direction = -1) +
  theme_bw(base_family = "Noto Sans CJK JP") +
  labs(fill = "コード共有") +
  theme(axis.ticks = element_blank(),
        axis.text = element_blank(),
        panel.grid = element_blank(),
        panel.border = element_blank()) +
  xlab("") + ylab("") +
  geom_text(aes(x = 1, label = sprintf("%0.2f", round(percentage, digits = 2))), position = position_stack(vjust = 0.5))
```

![](analysis01_files/figure-html/unnamed-chunk-7-1.png)<!-- -->

## 論文に掲載する図の作成コード

### Figure 1


```r
p1 <- data %>% 
  filter(with_data == 1) %>% 
  select(pre_registered) %>% 
  mutate(n = 1) %>% 
  group_by(pre_registered) %>% 
  summarize(total = sum(n)) %>% 
  mutate(percentage = total/sum(total)) %>% 
  ggplot(aes(x = "", y = total, fill = reorder(pre_registered, -total))) +
  geom_bar(width = 1, stat = "identity", color = "white") +
  coord_polar(theta = "y", direction = -1) +
  theme_bw(base_family = "Noto Sans CJK JP") +
  scale_fill_grey(start = 0.6, end = 0.9) + 
  labs(fill = "事前登録") +
  theme(axis.ticks = element_blank(),
        axis.text = element_blank(),
        panel.grid = element_blank(),
        panel.border = element_blank()) +
  xlab("") + ylab("") +
  geom_text(aes(x = 1, label = sprintf("%0.2f", round(percentage, digits = 2))), position = position_stack(vjust = 0.5))+
  ggtitle("A") 

p2 <- data %>% 
  filter(with_data == 1 & pre_registered != "事前登録なし") %>% 
  select(where_pre_registered) %>% 
  mutate(n = 1) %>% 
  group_by(where_pre_registered) %>% 
  summarize(total = sum(n)) %>% 
  mutate(percentage = total/sum(total)) %>% 
  ggplot(aes(x = "", y = total, fill = reorder(where_pre_registered, -total))) +
  geom_bar(width = 1, stat = "identity", color = "white") +
  coord_polar(theta = "y", direction = -1) +
  theme_bw(base_family = "Noto Sans CJK JP") +
  scale_fill_grey(start = 0.6, end = 0.9) + 
  labs(fill = "登録リポジトリ") +
  theme(axis.ticks = element_blank(),
        axis.text = element_blank(),
        panel.grid = element_blank(),
        panel.border = element_blank()) +
  xlab("") + ylab("") +
  geom_text(aes(x = 1, label = sprintf("%0.2f", round(percentage, digits = 2))), position = position_stack(vjust = 0.5))+
  ggtitle("B") 

grid.arrange(p1, p2, nrow = 1)
```

![](analysis01_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

### Figure 2


```r
p3 <- data %>% 
  filter(with_data == 1) %>% 
  select(share_data) %>% 
  mutate(n = 1) %>% 
  group_by(share_data) %>% 
  summarize(total = sum(n)) %>% 
  mutate(percentage = total/sum(total)) %>% 
  ggplot(aes(x = "", y = total, fill = reorder(share_data, -total))) +
  geom_bar(width = 1, stat = "identity", color = "white") +
  coord_polar(theta = "y", direction = -1) +
  theme_bw(base_family = "Noto Sans CJK JP") +
  scale_fill_grey(start = 0.6, end = 0.9) + 
  labs(fill = "データ共有") +
  theme(axis.ticks = element_blank(),
        axis.text = element_blank(),
        panel.grid = element_blank(),
        panel.border = element_blank()) +
  xlab("") + ylab("") +
  geom_text_repel(aes(x = 1, label = sprintf("%0.2f", round(percentage, digits = 2))), position = position_stack(vjust = 0.5)) +
  ggtitle("A") 
  
  
p4 <- data %>% 
  filter(with_data == 1) %>% 
  select(share_code) %>% 
  mutate(n = 1) %>% 
  group_by(share_code) %>% 
  summarize(total = sum(n)) %>% 
  mutate(percentage = total/sum(total)) %>%
  ggplot(aes(x = "", y = total, fill = reorder(share_code, -total))) +
  geom_bar(width = 1, stat = "identity", color = "white") +
  coord_polar(theta = "y", direction = -1) +
  theme_bw(base_family = "Noto Sans CJK JP") +
  scale_fill_grey(start = 0.6, end = 0.9) + 
  labs(fill = "コード共有") +
  theme(axis.ticks = element_blank(),
        axis.text = element_blank(),
        panel.grid = element_blank(),
        panel.border = element_blank()) +
  xlab("") + ylab("") +
  geom_text(aes(x = 1, label = sprintf("%0.2f", round(percentage, digits = 2))), position = position_stack(vjust = 0.5))　+
  ggtitle("B") 

grid.arrange(p3, p4, widths=c(1,0.9), nrow = 1)
```

![](analysis01_files/figure-html/unnamed-chunk-9-1.png)<!-- -->
