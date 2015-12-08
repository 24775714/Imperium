# Imperium
帝國是一個基於 PHP 的權限管理類別，然而，一個帝國並不是這麼好管理的，

如果這個類別對你來說並不滿意，你可以參考 [Authority（已廢棄）](https://github.com/machuga/authority) 或 [Lock](https://github.com/BeatSwitch/lock)。

**閱讀這份文件，你需要有一定的英文基礎。**

**但是我們已經將基本用法翻譯成中文，你當然可以照做，但是我們希望你撰寫程式時，還是只有英文。**

&nbsp;

## 特色

1. 簡單簡短。

2. 支援動作紀錄。

3. 較彈性的使用方式。

4. 動作捷徑。

&nbsp;

## 索引

1. 舉例

2. RBAC 和 ACL 的差異

3. 角色

  * 新增
  
  * 選擇
  
  * 指派 
 

4. 檢查和設置權限

  * 檢查

  * 設置
  
    * 允許和禁止動作和萬用符號
     
    * 允許和禁止種類
     
    * 允許和禁止特定編號
    
  * 設置捷徑

&nbsp;

## 舉例

當你建立了一個帝國後，你可以開始新增角色，

和配予他們權限，然後再要執行動作時，檢查他們是否有權限：

```php
/** 新增一個管理員角色，然後可以做任何事 */
$imper->setRole('管理員')
      ->allow('%');

/** 然後如果擁有移除的權限，那麼就可以 .. */
if($imper->can('移除'))
{
    ... 程式 ...
}
```

&nbsp;

## RBAC 和 ACL 的差異
> Role Based Access Control（角色基礎存取控制） 

> 以角色為判斷目標，例如：你是否為「管理員」？

讓我們假設你有一個文章的類別 `$post`，然後你想要移除它。

這是你採用 RBAC 時的範例：

```php
/** 如果目前角色是 管理員、使用者、作家，那就移除文章 */
if($user->hasRole('管理員') || $user->hasRole('使用者') || $user->hasRole('作家'))
{
    $post->delete();
}
```

所以你應該能夠知道，RBAC 是**透過取得你的「角色」來決定你能做什麼**了，

但是考慮到未來可能會有更多的角色，每新增一個角色不就每次都要修改程式了嗎？

除非你擁有一個非常良好的架構，否則未來你將可能會癱瘓。

&nbsp;

> Access Control List（存取控制串列）

> 以動作為判斷目標，例如：你是否能執行「移除」動作？

現在，讓我們採用 ACL 的方式來移除一篇文章。

```php
/** 如果你有權限執行移除，那就移除裝 ... */
if($impire->can('移除'))
{
    $post->delete();
}
```

ACL 只詢問**你有沒有這個權限**，而不在乎你是誰，如此一來，彈性就更大，

當你變動角色的時候，也無需更改程式。

&nbsp;

## 角色

角色是 ACL 中重要的一環，沒有角色，我們就無法得知你有權利做什麼，

所以你必須**先新增角色**，然後**選擇該角色**，**新增**它的權限，然後再將該角色**指派到某個人身上**。

&nbsp;

### 新增

你可以新增一個角色，或是繼承一個角色。

```php
/** 新增管理員這個角色 */
->setRole('管理員')

/** 這樣就會新增「訪客」這個角色，而相關權限繼承於「使用者」*/
->setRole('訪客', '使用者')

/** 這樣就會新增「作者」和「使用者」這個角色，而相關權限繼承於「管理員」*/
->setRole(['作者', '使用者'], '管理員')
```

&nbsp;

### 選擇

透過 `role()` 來選擇你新增的角色，然後開始替他們新增或是拒絕權限。

```php
->role('管理員')
->allow('建立')  // 新增一個「建立」的權限，接下來會提到如何新增或拒絕
```

&nbsp;

### 指派

當你完成權限配置之後，你就可以透過 `assign()` 來給現在這個人配予一個角色。

```php
->assign('管理員')
```

&nbsp;

## 檢查和設置權限

通常一個權限會有兩個設置，第一個是動作，第二個是種類，

例如「建立」（動作）「文章」（種類）。

&nbsp;

### 檢查權限

首先你要先知道如何檢查權限，這樣在接下來的步驟中，

才能更好的讓你知道做了什麼，會讓什麼權限被移除。

透過 `can()` 來得到一個布林值，用來確認**能否做某件事**，

相反的，透過 `cannot()` 來詢問**是否「不能」做某件事**。

```php
/** 可以執行「建立」這個動作嗎？ */
->can('建立')

/** 可以「建立」「文章」嗎？ */
->can('建立', '文章')

/** 可以「編輯」編號為 5 的「文章」嗎？ */
->can('編輯', '文章', 5)

/** 可以「編輯」與「建立」「文章」嗎？必須兩個都符合 */
->can(['編輯', '建立'], '文章')

/** 不能執行「建立」這個動作嗎 */
->cannot('建立')

/** 不能「移除」與「檢視」「文章」嗎？必須兩個都符合 */
->cannot(['移除', '檢視'], '文章')
```

&nbsp;

現在你清楚如何確認自己是否有權限了，你可以將他應用在你的條件式中：

```php
if($imper->can('建立', '文章'))
{
    .. 程式 ..
}
```

&nbsp;

### 設置權限

透過 `allow()` 或 `deny()` 來允許和拒絕這個角色可以執行的動作。

&nbsp;

#### 允許和禁止動作和萬用符號

你可以**完全**禁止或允許一個角色做什麼。

```php
/** 允許所有「建立」的動作 */
->allow('建立')

->can('建立', '文章') // true

/** 禁止所有「建立」的動作 */
->deny('建立')

->can('建立', '文章') // false
```

&nbsp;

你可以透過 **`%`** 符號來代替所有的事物。

```php
->allow('%')

->can('建立')            // true
->can('移除', '文章')    // true
->can('移除', '文章', 3) // true
```

&nbsp;

#### 允許和禁止種類

假設你想要禁止一個角色去建立**文章**，**而不是**禁止任何有關「建立」的這個動作，

**你就必須在「動作」後新增一個種類**。

```php
/** 允許「建立」「文章」*/
->allow('建立', '文章')

->can('建立')            // false
->can('建立', '文章')    // true
->can('建立', '文章', 5) // true


/** 禁止「建立」「文章」*/
->deny('建立', '文章');
```

&nbsp;

#### 允許和禁止特定編號

倘若你要指定的更仔細，例如你希望使用者**只可以編輯編號 5 的文章而非所有文章**，

那你就可以在最後面指定編號：

```php
/** 允許「編輯」編號為 5 的「文章」*/
->allow('編輯', '文章', 5)

->can('編輯')            // false
->can('編輯', '文章')    // false
->can('編輯', '文章', 5) // true


/** 禁止「編輯」編號為 5 的「文章」*/
->deny('編輯', '文章', 5)
```

&nbsp;

### 設置捷徑

當你有了許多動作，你透過 `alias()` 建立一個捷徑，像這樣：

```php
/** 建立一個名為「管理」的動作，而該動作包括「建立」、「編輯」、「刪除」*/
->alias('管理', ['建立', '編輯', '刪除'])

/** 允許這個角色「管理」「文章」*/
->allow('管理', '文章')

->can('建立', '文章')           // true
->can(['編輯', '刪除'], '文章') // true
->can('刪除', '文章', 3)        // true
->can('刪除')                   // false
```



