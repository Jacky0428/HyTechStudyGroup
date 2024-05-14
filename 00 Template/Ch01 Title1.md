# h1
## h2
### h3

一般文字

**粗體字**

*斜體字*

---

水平線

---


* list1

  1-1

  1-2


* list2

  2-1

  2-2


* 程式碼

  單行
  `int x = 1;`

  多行
  ```java
  public List<int[]> getThem() {
    List<int[]> list1 = new ArrayList<int[]>();
    for (int[] x : theList)
      if (x[0] == 4) list1.add(x);
    return list1;
  }
  ```


* toggle

  <details>
    <summary>Use Exceptions Rather Than Return Codes (open)</summary>
  
    程式碼：
    ```java
        // Bad
      function sendShutDown() {
          const handleRes = getHandle(DEV1)
          if (handleRes !== DEVICE_HANDLE.INVALID) {
              // do something...
          } else {
              console.log("Device suspended.  Unable to shut down")
          }
      }
      
      // Good
      function sendShutDown() {
          try {
              const handleRes = getHandle(DEV1)
              if (handleRes !== DEVICE_HANDLE.INVALID) {
                  // do something...
              } else {
                  console.log("Device suspended.  Unable to shut down")
              }
          } catch(error) {
              console.error(error)
          }
          
      }
      function getHandle(type) {
          // ...
           throw new Error("Invalid handle for: " + id.toString());
          // ...
      }
    ```
  </details>


* 圖片

  ![image](images/bad.png)



