Java中的substring()用法
String str = "Hello Java World!";

Method1:  substring(int beginIndex)

　　　　　　返回从起始位置（beginIndex）至字符串末尾的字符串

　　　　　　str.substring(2);

　　　　　　//return "llo Java World!";

 

Method2:  substring(int beginIndex, int endIndex)

　　　　　　返回从起始位置（beginIndex）到目标位置（endIndex）之间的字符串，但不包含目标位置（endIndex）的字符

　　　　　　str.substring(2,4);

　　　　　　//return "ll";