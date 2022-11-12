---
title: 正则替换回调在JAVA中的实现
date: 2017-12-14 20:13:49
tags: 
    - 正则
    - tech
---

## 问题

项目中需要将中文字符等特殊符号转位HTML的转义序列，用于HTML文档进行渲染，例如
例如：中国 -> &amp;#x4e2d;&amp;#x56fd;

## JS正则替换

由于javascript 提供的replace方法制成回调，所以很容易解决该问题：

```javascript
function encodeUnicode(str) {
  var str
  var reg = \\;
  str = str.replace(reg,funtion(){
    return "&#x"+str.charCodeAt(i).toString(16)
  })
  return str;
}
```



## JAVA如何实现

因为java 中并没有类似的替换回调，所以需要通过java中的正则相关的类和方法进行实现：

```java
public class ReplaceUtils {

    public static String replaceWithFn(CharSequence input, String regexp, Method fn, int group) {
        Matcher m = Pattern.compile(regexp).matcher(input);
        StringBuffer sb = new StringBuffer();
        try {
            Object obj = Modifier.toString(fn.getModifiers()).indexOf("static") > -1 ?
                    fn.getClass() : fn.getDeclaringClass().newInstance();
            if (fn.getReturnType() != String.class) {
                System.out.println("replacement function must return type \"String\".");
            }
            while (m.find()) {
                m.appendReplacement(sb, (String) fn.invoke(obj, m.group(group)));
            }
            m.appendTail(sb);
        } catch (Exception e) {
            //e.printStackTrace();
        }
        return sb.toString();
    }


    public String char2hex(String s) {
        String str="<mo>&#x";
        for (int i=0;i<s.length();i++){
            int ch = (int)s.charAt(i);
            String s4 = Integer.toHexString(ch);
            str = str + s4+";</mo>";
        }
        return str;
    }
}
```

使用如下：
```java
public static String char2Hex(String input) {
    try {
        //StringBuffer input = new StringBuffer("中国的String");
        System.out.println("input=" + input);
        String result = ReplaceUtils.replaceWithFn(
            input,
            "([^\\u0000-\\u00FF])",
            ReplaceUtils.class.getMethod("char2hex", String.class),
            1
        );
        System.out.println("output=" + result);
        return result;
    } catch (SecurityException e) {
        //e.printStackTrace();
        return input;
    } catch (NoSuchMethodException e) {
        //e.printStackTrace();
        return input;
    }
}
```

参考：

[Using functions or methods in Java's String.replaceAll() regex](https://stackoverflow.com/questions/4742183/using-functions-or-methods-in-javas-string-replaceall-regex)
[一道小小的题目引发对javascript支持正则表达式相关方法的探讨](https://segmentfault.com/a/1190000012147039)
