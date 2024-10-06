# hadoop的安装

[TOC]

```bash
# 下载hadoop至/opt
wget https://archive.apache.org/dist/hadoop/common/hadoop-3.1.4/hadoop-3.1.4.tar.gz
```

## 环境配置

```shell
$hadoop/sbin/start-dfs.hs
HDFS_DATANODE_USER=root
HADOOP_DATANODE_SECURE_USER=hdfs
HDFS_NAMENODE_USER=root
HDFS_SECONDARYNAMENODE_USER=root
```

```shell
$hadoop/sbin/stop-dfs.hs
HDFS_DATANODE_USER=root
HADOOP_DATANODE_SECURE_USER=hdfs
HDFS_NAMENODE_USER=root
HDFS_SECONDARYNAMENODE_USER=root
```

## 使用MapReduce进行WordCount统计

```java
package cn.qingweico;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;

import java.io.IOException;
import java.util.Iterator;
import java.util.StringTokenizer;


/**
 * @author zqw
 * @date 2021/6/13
 */
public class WordCount {

    private static final int MAX_ARGS = 2;
    public WordCount() {
    }

    public static void main(String[] args) throws Exception {
        Configuration conf = new Configuration();
        String[] otherArgs = (new GenericOptionsParser(conf, args)).getRemainingArgs();
        if (otherArgs.length < MAX_ARGS) {
            System.err.println("Usage: wordCount <in> [<in>...] <out>");
            System.exit(-1);
        }
        long start = System.currentTimeMillis();
        Job job = Job.getInstance(conf, "word count");
        job.setJarByClass(WordCount.class);
        job.setMapperClass(WordCount.TokenizerMapper.class);
        job.setCombinerClass(WordCount.IntSumReducer.class);
        job.setReducerClass(WordCount.IntSumReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        for (int i = 0; i < otherArgs.length - 1; ++i) {
            FileInputFormat.addInputPath(job, new Path(otherArgs[i]));
        }
        FileOutputFormat.setOutputPath(job, new Path(otherArgs[otherArgs.length - 1]));
        long end = System.currentTimeMillis();
        System.out.println("Spend time total:" + (end - start) + "ms");
        System.exit(job.waitForCompletion(true) ? 0 : 1);
    }

    public static class TokenizerMapper extends Mapper<Object, Text, Text, IntWritable> {
        private static final IntWritable ONE = new IntWritable(1);
        private final Text word = new Text();

        public TokenizerMapper() {
        }

        @Override
        public void map(Object key, Text value, Mapper<Object, Text, Text, IntWritable>.Context context) throws InterruptedException, IOException {
            StringTokenizer itr = new StringTokenizer(value.toString());
            while (itr.hasMoreTokens()) {
                this.word.set(itr.nextToken());
                context.write(this.word, ONE);
            }
        }
    }

    public static class IntSumReducer extends Reducer<Text, IntWritable, Text, IntWritable> {
        private final IntWritable result = new IntWritable();

        public IntSumReducer() {
        }

        @Override
        public void reduce(Text key, Iterable<IntWritable> values, Reducer<Text, IntWritable, Text, IntWritable>.Context context) throws IOException, InterruptedException {
            int sum = 0;
            IntWritable val;
            for (Iterator<IntWritable> i = values.iterator(); i.hasNext(); sum += val.get()) {
                val = i.next();
            }
            this.result.set(sum);
            context.write(key, this.result);
        }
    }
}

```

```bash
# 启动hdfs和yarn
# $hadoop
cd sbin
./start-dfs.sh
./start-yarn.sh
```

```bash
# 将WordCount打包(jar)至($hadoop)exec文件夹下
```

```bash
# 新建hdfs文件夹
bin/hdfs dfs -mkdir /user
bin/hdfs dfs -mkdir /user/root
bin/hdfs dfs -mkdir /user/root/input
# 将需统计的文本文件上传至hdfs
bin/hdfs dfs -put 1M-Word.txt /user/root/input
```

```bash
# 执行jar
bin/hadoop jar exec/WordCount.jar /user/root/input/1M-Word.txt ouput
```

```bash
# 查看执行结果
bin/hdfs dfs -cat /user/root/ouput/part-r-00000
```

```bash
# 或者将hfs中的文件复制到本地
bin/hdfs dfs -get /user/root/ouput ouput
```

![image-20211023141132735](https://static-i0.oss-cn-shanghai.aliyuncs.com/pic/image-20211023141132735.png)
