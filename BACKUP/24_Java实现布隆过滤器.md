# [Java实现布隆过滤器](https://github.com/humyna/gitblog/issues/24)

写在前面
最近在面试候选人的时候，很多简历上都写了布隆过滤器，但问其原理大都说不清楚。
记得之前在研究比特币SPV的时候写过相关文章，忘得差不多了，这次重写一篇，也是让自己加深记忆。


布隆过滤器（Bloom Filter）是一种空间效率极高的概率型数据结构，用于检测一个元素是否在一个集合中。

它的原理是，当一个元素被加入集合时，通过K个哈希函数将这个元素映射成位数组中的K个点，把它们置为1。
检索时，我们只需将待检索元素同样通过哈希函数映射到位数组，如果数组的相应位置已经被置为1，则说明该元素可能在集合中。注意这里是“可能”，因为存在哈希碰撞的情况，即不同的元素可能被哈希到同一位置。  

布隆过滤器的优点是空间效率和查询时间都远超一般的算法，缺点是存在一定的误判率和删除困难。  

以下是一个简单的布隆过滤器的实现(使用了6个哈希函数)

````
import java.util.BitSet;

public class BloomFilter {
    private static final int DEFAULT_SIZE = 2 << 24;
    private static final int[] seeds = new int[]{7, 11, 13, 31, 37, 61};
    private BitSet bits = new BitSet(DEFAULT_SIZE);
    private SimpleHash[] func = new SimpleHash[seeds.length];

    public BloomFilter() {
        for (int i = 0; i < seeds.length; i++) {
            func[i] = new SimpleHash(DEFAULT_SIZE, seeds[i]);
        }
    }

    public void add(Object value) {
        for (SimpleHash f : func) {
            bits.set(f.hash(value), true);
        }
    }

    public boolean contains(Object value) {
        if (value == null) {
            return false;
        }
        boolean ret = true;
        for (SimpleHash f : func) {
            ret = ret && bits.get(f.hash(value));
        }
        return ret;
    }

    public static class SimpleHash {
        private int cap;
        private int seed;

        public SimpleHash(int cap, int seed) {
            this.cap = cap;
            this.seed = seed;
        }

        public int hash(Object value) {
            int h;
            return (value == null) ? 0 : Math.abs(seed * (cap - 1) & ((h = value.hashCode()) ^ (h >>> 16)));
        }
    }

         public static void main(String[] args) {
            String value = "test";
             BloomFilter filter = new BloomFilter();
            System.out.println(filter.contains(value));
            filter.add(value);
            System.out.println(filter.contains(value));
         }
}
````