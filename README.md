R on Online for Yamadai 20220420 by Ryosuke TAJIMA 
==============  
  
# 基本
## R言語の基本的な操作
```R  
x<-2
y<-5
a<-c(1,3,5,7,9) #数字列
b<-c(2,4,6,8,10) #数字列
b<-c(3,6,9,12,15) #後から入れたものに更新される
e<-a+b #要素の足し算
f<-a*b #要素の掛け算
moji<-c("A","B","C","D") #文字列も要素になる
```

## ディレクトリ
GUIでFile > Change Directory ...  

## データの読み込み  
```R  
d<-read.csv("Test.csv")
d # 読み込んだデータのチェック
head(d)
nrow(d) #行数
ncol(d) #列数
dim(d) #行列数
```  
  
## データセットの一部を抽出  
```R  
A<-d[1,1]
A<-d[10,1]  
A<-d[1:3,4:7]  
A<-d[3,]  
A<-d[,8]  
A<-d[1:4,]  
A<-d[,1:4]  
```  
  
## headerを使った抽出  
```R  
Tr<-d$Treatment  
Tr<-d$treatment #null, 大文字と小文字は区別される
```

## クラスの確認
```R  
class(d$Treatment)  
class(d$SDW)  
# numeric -> 数値，character -> 文字列，factor -> ファクターなど
```  

## subsetを使った抽出
```R  
sd5<-subset(d,d$Stage=="5week")  
sd8<-subset(d,d$Stage=="8week")  
```  

## データの追加
```R  
# 換算値を足す
SPU<-d$SDW*d$SPC/100*1000 #リン吸収量
SRratio<-d$SDW/d$RDW #地上部地下部比
SRL<-d$RLD/d$RDW #比根長
d2<-cbind(d,SPU, SRratio, SRL)
```  

## データのアウトラインを相関図で確認する  
```R  
plot(d2[,6:12])  
plot(d2[,6], d2[,7])
plot(d2$SDW, d2$RDW)
```  

## 図示大事  
[Anscombe's_quartet](https://en.wikipedia.org/wiki/Anscombe%27s_quartet)  

## 箱ひげ図  
```R  
boxplot(d$SDW)  
boxplot(sd5$SDW, sd8$SDW)  
boxplot(sd5$SDW~sd5$Limitation)  
boxplot(sd5$SDW~sd5$Inoculation)  
```  

# 統計解析  
## 一元配置の分散分析  
```R  
result<-aov(SDW~Limitation, sd5)  
summary(result)  
```  

## 一元配置の分散分析：乱塊法
```R  
result<-aov(SDW~Rep+Limitation, sd5)
summary(result)  
```  

## 二元配置の分散分析  
```R  
result<-aov(SDW~Limitation+Inoculation+Limitation*Inoculation, sd5)  
summary(result)  
result<-aov(SDW~Limitation*Inoculation, sd5) #省略記法  
summary(result)  
```  

## 二元配置の分散分析：乱塊法
```R  
result<-aov(SDW~Rep+Limitation*Inoculation, sd5)
summary(result)  
```  

## 三元配置の分散分析  
```R  
result<-aov(SDW~Stage*Limitation*Inoculation, d) #省略記法  
summary(result)  
```  

## 一度にまとめておこなえる  
```R  
# 5week  
sd25<-subset(d2,d2$Stage=="5week")  #データの切り分け
summary(aov(SDW~Limitation*Inoculation, sd25))  
summary(aov(RDW~Limitation*Inoculation, sd25))  
summary(aov(SPC~Limitation*Inoculation, sd25))  
summary(aov(RLD~Limitation*Inoculation, sd25))  
summary(aov(SPU~Limitation*Inoculation, sd25))  
summary(aov(SRratio~Limitation*Inoculation, sd25))  
summary(aov(SRL~Limitation*Inoculation, sd25))  
```

## TukeyHSDによる多重比較  
```R  
result<-aov(SDW~Treatment, sd5) # 一元配置の分散分析
summary(result)  
TukeyHSD(result)  
```  
  
## ライブラリmultcomp利用
```R  
install.packages("multcomp")
library(multcomp)
d$Treatment<-as.factor(d$Treatment) #クラスをfactorにする
result<-aov(SDW~Treatment, sd5) #一元配置の分散分析
Tukey<-glht(result, linfct=mcp(Treatment="Tukey"))  
summary(Tukey)  
cld(Tukey, level = 0.05, decreasing = TRUE) # アルファベットをつける
```  
  
## 相関  
```R  
cor(sd25$SDW, sd25$SPC)  
allcor<-cor(sd25[,6:12]) #まとめて解析  
allcor
```  
  
## 回帰分析
```R  
result<-lm(SDW~SPC, sd5)  
summary(result)  
```  
  
## 重回帰分析
```R  
allcor # 事前に多重共線性のチェック
result<-lm(SDW~SPC+RLD, sd5)  
summary(result)  
```  

以上