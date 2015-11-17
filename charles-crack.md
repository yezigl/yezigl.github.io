1、安装试用版后，得到charles.jar

2、修改charles.jar中的com/xk72/charles/gui/Licence.java，jad反编译之后修改即可

3、编译修改之后的原文件
```
javac -source 1.6 -target 1.6 -d . Licence.java
```

4、替换charles.jar中的Licence
```
jar -uvf charles.jar com/xk72/charles/gui/Licence*
```

5、用新获得的charles.jar替换安装目录中的jar

done
