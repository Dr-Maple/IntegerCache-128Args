# IntegerCache-128Args

# Integer源码，为什么缓存范围在【-128—+127】？

public final class Integer extends Number implements Comparable<Integer>
 

Integer是final类型的，表示不能被继承，同时实现了Number类，并实现了Comparable接口；

java中数据类型可以分为两类，一种的基本数据类型，一种是引用数据类型。

基本数据类型的数据不是对象，所以对于要将数据类型作为对象来使用的情况，java提供了相对应的包装类。

int是基本数据类型，integer是引用数据类型，是int的包装类。

自动装箱的过程：引用了valueOf()的方法

   public static Integer valueOf(int i) {
          assert IntegerCache.high >= 127;
          if (i >= IntegerCache.low && i <= IntegerCache.high)
              return IntegerCache.cache[i + (-IntegerCache.low)];
          return new Integer(i);
      }
assertion就是在程序中的一条语句，它对一个boolean表达式进行检查，一个正确程序必须保证这个boolean表达式的值为true；如果该值为false，说明程序已经处于不正确的状态下，系统将给出警告并且退出。一般来说，assertion用于保证程序最基本、关键的正确性。
    java内部为了节省内存，IntegerCache类中有一个数组缓存了值从-128到127的Integer对象。当我们调用Integer.valueOf（int i）的时候，如果i的值时结余-128到127之间的，会直接从这个缓存中返回一个对象，否则就new一个新的Integer对象。

 

即：当我们定义两个Integer的范围在【-128—+127】之间，并且值相同的时候，用==比较值为true；

       当大于127或者小于-128的时候即使两个数值相同，也会new一个integer,那么比较的是两个对象，用==比较的时候返回false

 

  private static class IntegerCache {
        static final int low = -128;
        static final int high;
        static final Integer cache[];
 
        static {
            // high value may be configured by property
            int h = 127;
            String integerCacheHighPropValue =
                sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
            if (integerCacheHighPropValue != null) {
                int i = parseInt(integerCacheHighPropValue);
                i = Math.max(i, 127);
                // Maximum array size is Integer.MAX_VALUE
                h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
            }
            high = h;
 
            cache = new Integer[(high - low) + 1];
            int j = low;
            for(int k = 0; k < cache.length; k++)
                cache[k] = new Integer(j++);
        }
 
        private IntegerCache() {}
    }

IntegerCache是Integer的内部类，用来将-128——high之间的对象进行实例化

 

这边固定了缓存的下限，但是上限可以通过设置jdk的AutoBoxCacheMax参数调整，自动缓存区间设置为[-128,N]；

IntegerCache 不会有实例，它是 private static class IntegerCache，在 Integer 中都是直接使用其 static 方法

原文链接：https://blog.csdn.net/wang0112233/article/details/78641951
