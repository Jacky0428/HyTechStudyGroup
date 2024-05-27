# 6-1 TDD為何
## 主要由三個動作組成
1. 寫一個錯誤的測試
2. 使測試通過
3. 重構

即所謂的「紅綠燈原則」與「Baby Step原則」
先依照需求寫出第一個不會通過的測試，即紅燈狀態

接下來要秉持著「Baby Step原則」，即每次只能寫一個短小的程式碼，讓紅燈變為綠燈
再來看能否有更好的寫法，進行重構，請注意在重構後要再次確認測試是否通過

# 6-2 TDD的困難之處
能夠活用TDD有以下能力需求
### 1. 需求訪談能力
TDD提倡避免浪費，源頭就是好的需求訪談開始
### 2. 建模能力
了解使用者問題後，下一步要「解釋給RD聽」，需要在腦中或紙上建立一個「模型」，來描述使用者現在的context與未來期待的新context步驟
### 3. 分析、提Solution，與列測項的能力
決定solution必須要「在什麼條件下，要有什麼表現」
### 4. 安排先後次序的能力
### 5. 維持抽象思考的能力
「15秒前你決定實現的邏輯，跟你現在正在打的Code，是不是同一件事」
### 6. 測試與程式的能力
### 7. 辨識Code smell的能力
### 8. 重構能力
### 9. 打字速度，包含工具的使用能力

# 6-3 TDD實彈演習：Magneto Effect
在畫UML流程圖、繪圖軟體中，有個功能是有個已存在的錨點，滑鼠點一下鼠標就會像磁鐵般移動到最近的錨點上。

舉例來說，有個(50,50)錨點，磁力作用範圍是5，當滑鼠在(49,49)點擊時，鼠標會被磁力吸到(50,50)
## Magneto Effect的測項
| 已存在錨點           | 滑鼠原位置   | 滑鼠新位置   | 測試功能       |
|-----------------|---------|---------|------------|
| 無               | (49,50) | (49,50) | 定介面、沒有錨點   |
| (50,50)         | (49,50) | (50,50) | 一個錨點，而且夠近  |
| (0,0)           | (49,50) | (50,50) | 一個錨點，但太遠   |
| (50,50),(0,0)   | (49,50) | (50,50) | 2個錨點，一個在附近 |
| (50,50),(50,49) | (49,50) | (50,50) | 2個錨點，都在附近 |

## 循環一：定介面
### 程式6-1
```java
class MagnetoEffect {
    @Test
    void no_anchor() {
        MagnetoEffect magnetoEffect = new MagnetoEffect();
        Point before = new Point(49, 50);
        Point after = magnetoEffect.check(before);
        assertEquals(new Point(49, 50), after); //這段執行時預計錯誤
    }
}
```

### 程式6-2 MagnetoEffect的空實作
```java
public class MagnetoEffect {
    public Point check(Point before) {
        return null;
    }
}
```

接下來要讓測試Pass，有兩種方法
1. 直接回傳new Point(49, 50)
2. 回傳輸入值
如果對需求理解清楚，會知道直接回傳原本滑鼠位置即可，因此選2
### 程式6-3
```java
public class MagnetoEffect {
    public Point check(Point before) {
        return point;
    }
}
```

## 循環二：一個錨點，而且夠近
### 程式6-4
```java
@Test
void one_near_anchor() {
    //Arrange 
    MagnetoEffect magnetoEffect = new MagnetoEffect(); //創造物件
    magnetoEffect.addAnchor(new Point(50, 50)); //新增錨點
    Point before = new Point(49, 50); //滑鼠位置
    
    //Act
    Point after = magnetoEffect.check(before); //執行
    
    //Assert
    assertEquals(new Point(50, 50), after); //檢查新位置是否與錨點一致
}
```

### 程式6-5 加程式
```java
public class MagnetoEffect {
    
    private Point anchor;
    
    public Point check(Point point) {
        return point; //這時在這裡回傳原本滑鼠位置會導致測試錯，情況不同要加上新判斷，請看6-6
    }
    
    public void addAnchor(Point anchor) {
        this.anchor = anchor;
    }
}
```

### 程式6-6
```java
public Point check(Point point) {
    if (anchor == null) {
        return point;
    }
    return null;
}
```

## 重構
Test code中內容相似可抽方法，同時把測試改得更有閱讀性

### 程式6-7
```java
class MagetoEffect {
    private final MagnetoEffect magnetoEffect = new MagnetoEffect();
    private Point before;
    private Point after;
    
    @Test
    void no_anchor() {
        given_mouse_was_at(49, 50);
        when_check();
        then_mouse_new_position_is(49, 50);
    }
    
    private void given_mouse_was_at(int x, int y) {
        before = new Point(x, y);
    }
    
    private void when_check() {
        after = magnetoEffect.check(before);
    }
    
    private void then_mouse_new_position_is(int x, int y) {
        assertEquals(new Point(x, y), after);
    }
    
    @Test
    void one_near_anchor() {
        given_anchor_at(50, 50); //新增錨點
        given_mouse_was_at(49, 50); //滑鼠位置
        when_check(); //執行檢查
        then_mouse_new_position_is(50, 50); //檢查新位置是否與錨點一致
    }
    
    private void given_anchor_at(int x, int y) {
        magnetoEffect.addAnchor(new Point(x, y));
    }
    
}
```
## 循環三：一個錨點，但太遠

### 程式6-8
```java
//這時候6-8會掛掉，因為6-6的判斷式不夠完整，應該要加上距離判斷
@Test
void one_far_anchor() {
    given_anchor_at(0, 0);
    given_mouse_was_at(49, 50);
    when_check();
    then_mouse_new_position_is(49, 50);
}
```

### 程式6-9 先寫一版會過的醜code
```java
public Point check(Point point) {
    if (anchor == null) {
        return point;
    }
    if (Math.pow(anchor.x - point.x, 2) + Math.pow(anchor.y - point.y, 2) > Math.pow(5, 2)) {
        return point;
    }
    return anchor;
}
```

### 程式6-10 重構
```java
public Point check(Point point) {
    if (anchor == null) {
        return point;
    }
    if (isFarFromAnchor(anchor)) {
        return point;
    }
    return anchor;
}

private boolean isFarFromAnchor(Point anchor) {
    return Math.pow(anchor.x - point.x, 2) + Math.pow(anchor.y - point.y, 2) > Math.pow(5, 2);
}
```

## 循環四：2個錨點，一個在附近

### 程式6-11
```java
//程式中還沒有支援兩個錨點，因此預計會掛掉
@Test
void two_anchors_one_near() {
    given_anchor_at(50, 50);
    given_anchor_at(0, 0);
    given_mouse_was_at(49, 50);
    when_check();
    then_mouse_new_position_is(50, 50);
}
```

### 程式6-12
```java
public void addAnchor(Point anchor) {
    this.anchors.add(anchor);
}
```

### 程式6-13 加上功能(醜code
```java
public Point check(Point point) {
    if(anchors.isEmpty()){
        return point;
    }
    Point nearAnchor = null;
    for(Point anchor : anchors){
        double distance = Math.pow(anchor.x - point.x, 2) + Math.pow(anchor.y - point.y, 2);
        if(distance <= Math.pow(5, 2)){
            nearAnchor = anchor;
        }
    }
    
    if(nearAnchor == null){
        return point;
    }else{
        return nearAnchor;
    }
}
```

## 循環五：2個錨點，都在附近 (故意不重構看會怎樣)
作者提到不重構次數最多不要超過三次，不然會很難改，因此這裏故意不重構看會多醜

### 程式6-14
```java
@Test
void two_anchors_both_near() {
    given_anchor_at(50, 50);
    given_anchor_at(50, 49);
    
    given_mouse_was_at(49, 50);
    when_check();
    then_mouse_new_position_is(50, 50);
}
```
這時再去重構程式

### 程式6-15
```java
public class MagetoEffect{
    private List<Point> anchors = new ArrayList<>();
    
    public Point check(Point point) {
        if(anchors.isEmpty()){
            return point;
        }
        
        double nearestDistance = Double.MAX_VALUE;
        Point nearestAnchor = null;
        for(Point anchor : anchors){
            double distance = Math.pow(anchor.x - point.x, 2) + Math.pow(anchor.y - point.y, 2);
            if(distance <= Math.pow(5, 2)){
                nearestAnchor = anchor;
                nearestDistance = distance;
            }
        }

        if(nearAnchor == null){
            return point;
        }else{
            return nearestAnchor;
        }
    }
}
```

### 程式6-16 重構：刪去多餘邏輯
```java
public Point check(Point point) {
    if(anchors.isEmpty()){ //少了這段也不會有問題
        return point;
    }
    
    double nearestDistance = Double.MAX_VALUE;
    Point nearestAnchor = null;
    for(Point anchor : anchors){
        double distance = Math.pow(anchor.x - point.x, 2) + Math.pow(anchor.y - point.y, 2);
        if(distance <= Math.pow(5, 2)){
            nearestAnchor = anchor;
            nearestDistance = distance;
        }
    }

    if(nearAnchor == null){
        return point;
    }else{
        return nearestAnchor;
    }
}
```

### 程式6-17 重構：抽方法
```java
public Point check(Point point) {
    
    Point nearestAnchor = findNearestAnchor(point);

    if(nearAnchor == null){
        return point;
    }else{
        return nearestAnchor;
    }
}
```

### 程式6-18 抽出方法，用Lambda改寫
```java
private Point findNearestAnchor(Point point) {
    return anchors.stream()
        .map(anchor -> new AbstractMap.SimpleEntry<Point, Double>(anchor, getDistance(anchor, point)))
        .filter(entry -> entry.getValue() <  Math.pow(5,2))
        .min(java.util.Map.Entry.comparingByValue())
        .map(AbstractMap.SimpleEntry::getKey)
        .orElse(null);
}
```
### 程式6-19 重構：用Optional取代null
```java
public Point check(Point point) {
    Optional<Point> nearestAnchor = findNearestAnchor(point);
    return nearestAnchor.orElse(point);
}

private Point findNearestAnchor(Point point) {
        return anchors.stream()
        .map(anchor -> new AbstractMap.SimpleEntry<Point, Double>(anchor, getDistance(anchor, point)))
        .filter(entry -> entry.getValue() <  Math.pow(5,2))
        .min(java.util.Map.Entry.comparingByValue())
        .map(AbstractMap.SimpleEntry::getKey)
        .orElse(null);
}   
```