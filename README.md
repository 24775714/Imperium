<p align="center">
  <img src="http://imgur.com/NFw6Fwu.png"/>
</p>
<p align="center">
  <i>Do what you want, if you can.</i>
</p>

&nbsp;

# Imperium

**由於帝國複雜，文件偏長，建議你先從索引選擇自己想要知道的。**

&nbsp;

帝國是一個基於 PHP 權限管理類別，具有龐大的複雜性，**不應該將帝國用於小型網站（如論壇，購物系統）**

建立一個帝國，精心的規畫是不能少的。帝國針對資源有做特別的處理，

一般來說，權限管理**只管你能否執行什麼動作**，但在帝國之中，

**你可以選擇可否針對「特別物品」執行什麼動作**。

&nbsp;

# 特色

1. 動作請求紀錄

2. 明瞭的使用方法

3. 動作捷徑

4. 角色系統

5. 組織系統

6. 單個使用者可以擁有多個角色

7. 資源針對系統

&nbsp;

# 索引

1. 示範

2. 解釋

  * 我為什麼需要它？
  
  * RBAC、ACL、DAC？
  
  * 帝國包含什麼？
  
    * 角色和組織系統？
    
    * 資源系統？

3. 組織

  * 新增
  
  * 選擇

4. 角色

  * 新增
  
  * 選擇
  
  * 指派

5. 權限

  * 基礎於資源上
  
    * 角色
    
    * 組織
    
    * 種類
    
    * 編號

  * 檢查
  
    * 可以嗎？
    
    * 不可以嗎？
  
  * 設置

    * 允許和禁止動作和萬用符號
  
    * 允許和禁止種類

    * 允許和禁止特定編號
    
    * 動作捷徑

  * 取得
  
    * 允許和禁止的資源
    
    * 允許和禁止的權限

6. 「別當個雞掰人」公眾授權條款

7. 可參考文件 


&nbsp;

# 示範

現在的示範可能跟其他人的不太一樣，但是，當你習慣之後，

你就會發現其實更好上手。然而這個示範其實**只有展示 50% 不到**的功能。

```php
$imperium
/** 建立 網站 組織 */
->addOrg('網站')

/** 網站組織 下新增 管理員 角色 */
->addRole('管理員')

/** 給予 管理員 一個 編輯 的權限 */
->allow('編輯')

/** 讓現在這個 使用者 成為 管理員 */
->assign('管理員');


/** 然後判斷的時候就像這樣，詢問是否有權力執行 編輯 動作 */
if($imperium->can('編輯'))
{
    ... 程式 ...
}
```

&nbsp;

# 解釋

這裡包含了一些基礎的權限管理解釋，還有你為何會需要它，

然後如果你看完了之後發現你並不需要，請向時間銀行要求索賠吧。

&nbsp;

## 我為什麼需要它？

一個沒有權限管理系統的網站是**很恐怖**的，

我的意思是，就算你有分階層*（例如：訪客、使用者、管理員）*，

這樣看起來雖然沒什麼，但當你需要人手來管理網站的時候，**你會賦予他們「管理員」的權限**，

好死不死，「管理員」就是網站中**最大的權限**，甚至可以把**整個網站關機**，

設想看看，今天一個 22K 的上班族，**隨時都可以把你的網站關閉**，不覺得很恐怖麼？

這就是為什麼**你應該採用權限管理系統**，額外新增一個「版主」來管理網站，

如此一來，他們就**沒辦法**碰到系統深層的功能，當然，

你**也可以**不使用權限管理系統來新增「版主」，但是日復一日，

**你可能會有更多的階級**，這個時候，你就沒辦法不使用權限管理系統，

除非**你希望每次有新的階級，就更新一次程式碼**。

&nbsp;

## RBAC、ACL、DAC？

管理權限系統分成很多類，連我也不是很明白（沒錯。）

讓我們先談談 **RBAC（Role-Based Access Control）**，下面是你使用 RBAC 會遇見的情況：

```php
if($this->hasRole('管理員') || $this->hasRole('版主') $this->hasRole('使用者'))
{
    ... 程式 ...
}
```

發現了嗎？RBAC 想知道的，**不是你可以做什麼**，而是**你是誰**。

&nbsp;

接下來談談 **ACL （Access Control List）**，說到這裡，ACL 感覺簡單多了。

```php
if($this->can('編輯', '文章'))
{
    ... 程式 ...
}
```

ACL 詢問的是：**你有權力這樣做嗎**？而**不在乎你是誰**（也許還是會在乎一下啦）。

&nbsp;

然後是 **DAC（Discretionary Access Control）**，假設這樣好了，你**不能**編輯 `檔案A` 但是 小明 可以。

那麼你就可以要求 小明 將這個權限新增給你，接著你有了編輯權限後，**你也可以新增給其他人**，讓其他人有權限來編輯這個檔案。

&nbsp;

總而言之：

> RBAC：詢問「你是誰」？

> ACL：詢問「你可以做這件事」嗎？

> DAC：你可以新增給別人相同的權限。

&nbsp;

## 帝國包含什麼？

所以帝國算是 RBAC 或是 ACL 或是 DAC？

我也不清楚，也許他是另一種架構，如果真的是這樣，那個時候，

*我一定要把我的名字加在這個架構裡。*

&nbsp;

那麼，帝國包含什麼？帝國是基於動作的權限管理系統（大部分使用的時候像是 ACL），

在帝國中，你通常會想詢問使用者「能不能幹嘛」。

但是帝國有個更龐大，更複雜的構想，那就是角色和組織系統，甚至還有資源系統。

&nbsp;

### 角色和組織系統？

你可以建立一個角色，例如「版主」，然後配予他一個「編輯」的權限，

然後**別忘記**，一個使用者可以擁有多個角色，我想這對你來說這應該好理解。

&nbsp;

但是今天加上了組織系統，**你的角色就必須要有組織**，這樣說好了，你可能想做一個線上約砲網站，

然後還可以自己在網站內開一個新的群組，

你還希望這個群組中擁有自訂階級的功能，例如：*管理員*、*版主*、*一般人*。

這個時候組織系統就派上用場了，什麼意思？我的意思是：

> 假設 *小明* 是約砲網站的使用者，然後**他建立了一個專門聊色的群組**

那麼他就有**兩種身分和兩個組織**：

> 分別是位於**「網站」組織中的「使用者」身分**，還有**「聊色群組」組織中的「管理員」身分**。

不覺得這樣很好嗎？**你就不用在替群組功能新增一個額外的身分功能啦**。

&nbsp;

### 資源系統？

有了組織系統，仍然不能滿足一些人，設想看看，假設你有這個問題：

> 而版主可以移除任何使用者的文章。

> 管理員發布了一篇文章。

什麼問題發生了？**版主可以刪除管理員的文章**。

RBAC 和 ACL 並**不能**夠讓你做到「版主」無法刪除「管理員」所發布的文章，

因為他們**只在乎**「你能不能刪除文章」這個動作，一但他們可以刪除，**那就是任何文章都可以**。

&nbsp;

帝國的出現，就是想要使用在這種情況：

> 版主 可以刪除所有文章，**但是** 管理員 的不行。

> 版主 可以刪除所有文章，**但是** 編號 3 的文章就是不行。

同樣的，RBAC 和 ACL **都沒辦法**做到指定特別組織，或是特別角色，甚至特別文章。

而帝國想要特別針對這一點做出改進，我們將特別的對象稱之為資源。

&nbsp;

# 組織

組織在帝國中是極為重要的一環，組織名稱不可以重複，如果你用不到組織功能，你也可以選擇**不建立**組織。

&nbsp;

## 新增

透過 `addOrg()` 來建立一個組織，**請注意：當你建立組織之後，你接下來新增的角色都會所屬這個組織**。

```php
->addOrg('組織名稱')

/** 或者這個組織還有個父組織 */
->addOrg('組織名稱', '父組織名稱')
```

&nbsp;

## 選擇

若要避免接下來新增的角色都屬於這個組織，透過 `org()` 函式，來選擇其他組織，或是直接讓他們不屬於任何組織。

```php
->org('組織名稱')

/** 或是選擇不屬於任何組織 */
->org()
```

&nbsp;

# 角色

角色讓帝國能夠認清你具有什麼樣的權限。

&nbsp;

## 新增

透過 `addRole()` 新增一個角色，你也可以讓角色繼承另一個角色（也就是繼承權限）。

```php
->addRole('管理員')

/** 讓 管理員 繼承 版主的權限 */
->addRole('管理員', '版主')

/** 讓 訪客 和 使用者 繼承版主的權限 */
->addRole(['訪客', '使用者'], '版主')
```
 
&nbsp;

## 選擇

透過 `role()` 來選擇一個角色，接下來的動作都會有關這個角色。

```php
->role('管理員')
```

&nbsp;

## 指派

透過 `assign()` 讓目前的使用者成為某個角色。

```php
->assign('管理員')

/** 或者如果有所屬組織 */
->org('單身俱樂部')
->assign('管理員')
```

&nbsp;

# 權限

## 基礎於資源上
  
### 角色
    
### 組織
    
### 種類
    
### 編號

## 檢查
  
### 可以嗎？
    
### 不可以嗎？
  
## 設置

### 允許和禁止動作和萬用符號
  
### 允許和禁止種類

### 允許和禁止特定編號
    
### 動作捷徑

## 取得
  
### 允許和禁止的資源
    
### 允許和禁止的權限
&nbsp;

# 「別當個雞掰人」公眾授權條款

> 版本一，2009 年 12 月

> 版權所有 (C) 2009 Philip Sturgeon <me@philsturgeon.uk>
 
任何人都有權限複製與發佈本認證的原始或修改過的版本。若要修改本認證，須修改認證名稱。

> 「別當個雞掰人」公眾授權條款
>  複製、散布以及重製的條款和條件

 1. 只要別當個雞掰人，你可以對原作品做任何事情。

     成為雞掰人的定義包括下列 - 但不僅限於：
     
	 1a. 徹底侵權 — 別只是複製這個作品然後改個名字而已。  
	 1b. 販售未經更改的原始碼，這樣**真的**很雞掰。  
	 1c. 修改原始碼並偷偷增加一些有害的內容。這樣會使你成為**真正的**雞掰人。  

 2. 如果你透過修改，或是為此提供相關服務，而或支持原作者而致富，請分享這份愛。只有雞掰人才會只幫自己，而不協助原始作品。
 
 3. 此份原始碼並不具有保固。當你使用他人原始碼發生錯誤，而指責他人時，會讓你看起來**夭壽**雞掰。自己學會修正問題。而一個不雞掰的人應該會送出這個修正給原作者。

&nbsp;

# 可參考文件

[Role Based Access Control in PHP](http://www.sitepoint.com/role-based-access-control-in-php/)

[efficiently/authority-controller](https://github.com/efficiently/authority-controller)

[BeatSwitch/lock](https://github.com/BeatSwitch/lock)

[OWASP/rbac](https://github.com/OWASP/rbac)

[machuga/authority](https://github.com/machuga/authority)
