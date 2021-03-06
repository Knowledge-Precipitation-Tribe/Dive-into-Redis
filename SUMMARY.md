# Table of contents

* [Dive-into-Redis](README.md)
* [NoSQL和传统数据库有什么区别？](nosql-he-chuan-tong-shu-ju-ku-you-shen-me-qu-bie.md)
* [Redis特性](redis-te-xing.md)
* [Redis可以做什么](redis-ke-yi-zuo-shen-me.md)
* [下载与安装](xia-zai-yu-an-zhuang/README.md)
  * [redis在线练习](xia-zai-yu-an-zhuang/redis-zai-xian-lian-xi.md)
  * [直接下载](xia-zai-yu-an-zhuang/zhi-jie-xia-zai.md)
  * [Docker](xia-zai-yu-an-zhuang/docker.md)
* [登陆Redis数据库](deng-lu-redis-shu-ju-ku.md)
* [可视化工具](ke-shi-hua-gong-ju.md)
* [应用场景](ying-yong-chang-jing.md)

## API理解与使用

* [全局命令1](api-li-jie-yu-shi-yong/quan-ju-ming-ling.md)
* [全局命令2](api-li-jie-yu-shi-yong/quan-ju-ming-ling-2.md)
* [数据类型](api-li-jie-yu-shi-yong/shu-ju-lei-xing.md)
* [string\(字符串\)](api-li-jie-yu-shi-yong/string-zi-fu-chuan/README.md)
  * [设置值](api-li-jie-yu-shi-yong/string-zi-fu-chuan/she-zhi-zhi.md)
  * [获取值](api-li-jie-yu-shi-yong/string-zi-fu-chuan/huo-qu-zhi.md)
  * [批量设置值](api-li-jie-yu-shi-yong/string-zi-fu-chuan/pi-liang-she-zhi-zhi.md)
  * [批量获取值](api-li-jie-yu-shi-yong/string-zi-fu-chuan/pi-liang-huo-qu-zhi.md)
  * [计数](api-li-jie-yu-shi-yong/string-zi-fu-chuan/ji-shu.md)
  * [追加值](api-li-jie-yu-shi-yong/string-zi-fu-chuan/zhui-jia-zhi.md)
  * [字符串长度](api-li-jie-yu-shi-yong/string-zi-fu-chuan/zi-fu-chuan-chang-du.md)
  * [设置并返回原值](api-li-jie-yu-shi-yong/string-zi-fu-chuan/she-zhi-bing-fan-hui-yuan-zhi.md)
  * [设置指定位置的字符](api-li-jie-yu-shi-yong/string-zi-fu-chuan/she-zhi-zhi-ding-wei-zhi-de-zi-fu.md)
  * [获取部分字符串](api-li-jie-yu-shi-yong/string-zi-fu-chuan/huo-qu-bu-fen-zi-fu-chuan.md)
  * [字符串命令总结](api-li-jie-yu-shi-yong/string-zi-fu-chuan/zi-fu-chuan-ming-ling-zong-jie.md)
  * [使用场景](api-li-jie-yu-shi-yong/string-zi-fu-chuan/dian-xing-shi-yong-chang-jing.md)
* [hash\(哈希\)](api-li-jie-yu-shi-yong/hash-ha-xi/README.md)
  * [设置值](api-li-jie-yu-shi-yong/hash-ha-xi/she-zhi-zhi.md)
  * [获取值](api-li-jie-yu-shi-yong/hash-ha-xi/huo-qu-zhi.md)
  * [删除field](api-li-jie-yu-shi-yong/hash-ha-xi/shan-chu-field.md)
  * [计算field个数](api-li-jie-yu-shi-yong/hash-ha-xi/ji-suan-field-ge-shu.md)
  * [批量设置或获取field-value](api-li-jie-yu-shi-yong/hash-ha-xi/pi-liang-she-zhi-huo-huo-qu-fieldvalue.md)
  * [判断field是否存在](api-li-jie-yu-shi-yong/hash-ha-xi/pan-duan-field-shi-fou-cun-zai.md)
  * [获取所有field](api-li-jie-yu-shi-yong/hash-ha-xi/huo-qu-suo-you-field.md)
  * [获取所有value](api-li-jie-yu-shi-yong/hash-ha-xi/huo-qu-suo-you-value.md)
  * [获取所有的field-value](api-li-jie-yu-shi-yong/hash-ha-xi/huo-qu-suo-you-de-fieldvalue.md)
  * [计算value的字符串长度](api-li-jie-yu-shi-yong/hash-ha-xi/ji-suan-value-de-zi-fu-chuan-chang-du.md)
  * [哈希命令总结](api-li-jie-yu-shi-yong/hash-ha-xi/ha-xi-ming-ling-zong-jie.md)
  * [使用场景](api-li-jie-yu-shi-yong/hash-ha-xi/shi-yong-chang-jing.md)
* [list\(列表\)](api-li-jie-yu-shi-yong/list-lie-biao/README.md)
  * [从右边插入元素](api-li-jie-yu-shi-yong/list-lie-biao/cong-you-bian-cha-ru-yuan-su.md)
  * [从左边插入元素](api-li-jie-yu-shi-yong/list-lie-biao/cong-zuo-bian-cha-ru-yuan-su.md)
  * [向某个元素前或者后插入元素](api-li-jie-yu-shi-yong/list-lie-biao/xiang-mou-ge-yuan-su-qian-huo-zhe-hou-cha-ru-yuan-su.md)
  * [获取指定范围内的元素列表](api-li-jie-yu-shi-yong/list-lie-biao/huo-qu-zhi-ding-fan-wei-nei-de-yuan-su-lie-biao.md)
  * [获取列表指定索引下标的元素](api-li-jie-yu-shi-yong/list-lie-biao/huo-qu-lie-biao-zhi-ding-suo-yin-xia-biao-de-yuan-su.md)
  * [获取列表长度](api-li-jie-yu-shi-yong/list-lie-biao/huo-qu-lie-biao-chang-du.md)
  * [从列表左侧弹出元素](api-li-jie-yu-shi-yong/list-lie-biao/cong-lie-biao-zuo-ce-tan-chu-yuan-su.md)
  * [从列表右侧弹出元素](api-li-jie-yu-shi-yong/list-lie-biao/cong-lie-biao-you-ce-tan-chu-yuan-su.md)
  * [删除指定元素](api-li-jie-yu-shi-yong/list-lie-biao/shan-chu-zhi-ding-yuan-su.md)
  * [按照索引范围修剪列表](api-li-jie-yu-shi-yong/list-lie-biao/an-zhao-suo-yin-fan-wei-xiu-jian-lie-biao.md)
  * [修改元素](api-li-jie-yu-shi-yong/list-lie-biao/xiu-gai-yuan-su.md)
  * [阻塞操作](api-li-jie-yu-shi-yong/list-lie-biao/zu-sai-cao-zuo.md)
  * [列表命令总结](api-li-jie-yu-shi-yong/list-lie-biao/lie-biao-ming-ling-zong-jie.md)
  * [使用场景](api-li-jie-yu-shi-yong/list-lie-biao/shi-yong-chang-jing.md)
* [set\(集合\)](api-li-jie-yu-shi-yong/set-ji-he/README.md)
  * [添加元素](api-li-jie-yu-shi-yong/set-ji-he/tian-jia-yuan-su.md)
  * [删除元素](api-li-jie-yu-shi-yong/set-ji-he/shan-chu-yuan-su.md)
  * [计算元素个数](api-li-jie-yu-shi-yong/set-ji-he/ji-suan-yuan-su-ge-shu.md)
  * [判断元素是否在集合中](api-li-jie-yu-shi-yong/set-ji-he/pan-duan-yuan-su-shi-fou-zai-ji-he-zhong.md)
  * [随机从集合返回指定个数元素](api-li-jie-yu-shi-yong/set-ji-he/sui-ji-cong-ji-he-fan-hui-zhi-ding-ge-shu-yuan-su.md)
  * [从集合随机弹出元素](api-li-jie-yu-shi-yong/set-ji-he/cong-ji-he-sui-ji-tan-chu-yuan-su.md)
  * [获取所有元素](api-li-jie-yu-shi-yong/set-ji-he/huo-qu-suo-you-yuan-su.md)
  * [求多个集合的交集](api-li-jie-yu-shi-yong/set-ji-he/qiu-duo-ge-ji-he-de-jiao-ji.md)
  * [求多个集合的并集](api-li-jie-yu-shi-yong/set-ji-he/qiu-duo-ge-ji-he-de-bing-ji.md)
  * [求多个集合的差集](api-li-jie-yu-shi-yong/set-ji-he/qiu-duo-ge-ji-he-de-cha-ji.md)
  * [将交集、并集、差集的结果保存](api-li-jie-yu-shi-yong/set-ji-he/jiang-jiao-ji-bing-ji-cha-ji-de-jie-guo-bao-cun.md)
  * [集合命令总结](api-li-jie-yu-shi-yong/set-ji-he/ji-he-ming-ling-zong-jie.md)
  * [使用场景](api-li-jie-yu-shi-yong/set-ji-he/shi-yong-chang-jing.md)
* [zset\(有序集合\)](api-li-jie-yu-shi-yong/zset-you-xu-ji-he/README.md)
  * [添加成员](api-li-jie-yu-shi-yong/zset-you-xu-ji-he/tian-jia-cheng-yuan.md)
  * [计算成员个数](api-li-jie-yu-shi-yong/zset-you-xu-ji-he/ji-suan-cheng-yuan-ge-shu.md)
  * [计算某个成员的分数](api-li-jie-yu-shi-yong/zset-you-xu-ji-he/ji-suan-mou-ge-cheng-yuan-de-fen-shu.md)
  * [计算成员的排名](api-li-jie-yu-shi-yong/zset-you-xu-ji-he/ji-suan-cheng-yuan-de-pai-ming.md)
  * [删除成员](api-li-jie-yu-shi-yong/zset-you-xu-ji-he/shan-chu-cheng-yuan.md)
  * [增加成员的分数](api-li-jie-yu-shi-yong/zset-you-xu-ji-he/zeng-jia-cheng-yuan-de-fen-shu.md)
  * [返回指定排名范围的成员](api-li-jie-yu-shi-yong/zset-you-xu-ji-he/fan-hui-zhi-ding-pai-ming-fan-wei-de-cheng-yuan.md)
  * [返回指定分数范围的成员](api-li-jie-yu-shi-yong/zset-you-xu-ji-he/fan-hui-zhi-ding-fen-shu-fan-wei-de-cheng-yuan.md)
  * [返回指定分数范围成员个数](api-li-jie-yu-shi-yong/zset-you-xu-ji-he/fan-hui-zhi-ding-fen-shu-fan-wei-cheng-yuan-ge-shu.md)
  * [删除指定排名内的升序元素](api-li-jie-yu-shi-yong/zset-you-xu-ji-he/shan-chu-zhi-ding-pai-ming-nei-de-sheng-xu-yuan-su.md)
  * [删除指定分数范围的成员](api-li-jie-yu-shi-yong/zset-you-xu-ji-he/shan-chu-zhi-ding-fen-shu-fan-wei-de-cheng-yuan.md)
  * [交集](api-li-jie-yu-shi-yong/zset-you-xu-ji-he/jiao-ji.md)
  * [并集](api-li-jie-yu-shi-yong/zset-you-xu-ji-he/bing-ji.md)
  * [有序集合命令总结](api-li-jie-yu-shi-yong/zset-you-xu-ji-he/you-xu-ji-he-ming-ling-zong-jie.md)
  * [使用场景](api-li-jie-yu-shi-yong/zset-you-xu-ji-he/shi-yong-chang-jing.md)

## 数据结构与对象

* [简单动态字符串](shu-ju-jie-gou-yu-dui-xiang/jian-dan-dong-tai-zi-fu-chuan.md)
* [链表](shu-ju-jie-gou-yu-dui-xiang/lian-biao.md)
* [字典](shu-ju-jie-gou-yu-dui-xiang/zi-dian.md)
* [跳表](shu-ju-jie-gou-yu-dui-xiang/tiao-biao.md)
* [整数集合](shu-ju-jie-gou-yu-dui-xiang/zheng-shu-ji-he.md)
* [压缩列表](shu-ju-jie-gou-yu-dui-xiang/ya-suo-lie-biao.md)

## 进阶功能

* [慢查询分析](jin-jie-gong-neng/man-cha-xun-fen-xi/README.md)
  * [最佳实践](jin-jie-gong-neng/man-cha-xun-fen-xi/zui-jia-shi-jian.md)
* [Pipeline](jin-jie-gong-neng/pipeline/README.md)
  * [原生批量命令与Pipeline对比](jin-jie-gong-neng/pipeline/yuan-sheng-pi-liang-ming-ling-yu-pipeline-dui-bi.md)
  * [最佳实践](jin-jie-gong-neng/pipeline/zui-jia-shi-jian.md)
* [事务与Lua](jin-jie-gong-neng/shi-wu-yu-lua/README.md)
  * [事务](jin-jie-gong-neng/shi-wu-yu-lua/shi-wu.md)
  * [Lua用法简述](jin-jie-gong-neng/shi-wu-yu-lua/lua-yong-fa-jian-shu.md)
* [Bitmap](jin-jie-gong-neng/bitmap.md)
* [发布订阅](jin-jie-gong-neng/fa-bu-ding-yue/README.md)
  * [命令](jin-jie-gong-neng/fa-bu-ding-yue/ming-ling.md)
  * [使用场景](jin-jie-gong-neng/fa-bu-ding-yue/shi-yong-chang-jing.md)
* [GEO](jin-jie-gong-neng/geo.md)

## 持久化

* [持久化](chi-jiu-hua/chi-jiu-hua.md)
* [RDB](chi-jiu-hua/rdb.md)
* [AOF](chi-jiu-hua/aof.md)
* [问题定位与优化](chi-jiu-hua/wen-ti-ding-wei-yu-you-hua.md)
* [多实例部署](chi-jiu-hua/duo-shi-li-bu-shu.md)

## 复制

* [复制](fu-zhi/fu-zhi.md)
* [配置](fu-zhi/pei-zhi.md)
* [拓扑](fu-zhi/tuo-pu.md)
* [原理](fu-zhi/yuan-li.md)
* [开发与运维中的问题](fu-zhi/kai-fa-yu-yun-wei-zhong-de-wen-ti/README.md)
  * [读写分离](fu-zhi/kai-fa-yu-yun-wei-zhong-de-wen-ti/du-xie-fen-li.md)
  * [主从配置不一致](fu-zhi/kai-fa-yu-yun-wei-zhong-de-wen-ti/zhu-cong-pei-zhi-bu-yi-zhi.md)
  * [规避全量复制](fu-zhi/kai-fa-yu-yun-wei-zhong-de-wen-ti/gui-bi-quan-liang-fu-zhi.md)
  * [规避复制风暴](fu-zhi/kai-fa-yu-yun-wei-zhong-de-wen-ti/gui-bi-fu-zhi-feng-bao.md)

## 阻塞

* [阻塞](zu-sai/zu-sai.md)
* [发现阻塞](zu-sai/fa-xian-zu-sai.md)
* [内在原因](zu-sai/nei-zai-yuan-yin.md)
* [外在原因](zu-sai/wai-zai-yuan-yin.md)

## 内存

* [理解内存](nei-cun/li-jie-nei-cun.md)
* [内存消耗](nei-cun/nei-cun-xiao-hao.md)
* [内存管理](nei-cun/nei-cun-guan-li.md)
* [内存优化](nei-cun/nei-cun-you-hua.md)

## 哨兵

* [基本概念](shao-bing/ji-ben-gai-nian.md)
* [安装和部署](shao-bing/an-zhuang-he-bu-shu/README.md)
  * [部署拓扑结构](shao-bing/an-zhuang-he-bu-shu/bu-shu-tuo-pu-jie-gou.md)
  * [部署Redis数据节点](shao-bing/an-zhuang-he-bu-shu/bu-shu-redis-shu-ju-jie-dian.md)
  * [部署Sentinel节点](shao-bing/an-zhuang-he-bu-shu/bu-shu-sentinel-jie-dian.md)
  * [配置优化](shao-bing/an-zhuang-he-bu-shu/pei-zhi-you-hua.md)
  * [部署技巧](shao-bing/an-zhuang-he-bu-shu/bu-shu-ji-qiao.md)
* [API](shao-bing/api.md)

## 集群操作

* [集群简介](ji-qun-cao-zuo/ji-qun-jian-jie.md)
* [数据分布](ji-qun-cao-zuo/shu-ju-fen-bu/README.md)
  * [数据分布理论](ji-qun-cao-zuo/shu-ju-fen-bu/shu-ju-fen-bu-li-lun.md)
  * [Redis数据分区](ji-qun-cao-zuo/shu-ju-fen-bu/redis-shu-ju-fen-qu.md)
  * [集群功能限制](ji-qun-cao-zuo/shu-ju-fen-bu/ji-qun-gong-neng-xian-zhi.md)
* [搭建集群](ji-qun-cao-zuo/da-jian-ji-qun/README.md)
  * [节点握手](ji-qun-cao-zuo/da-jian-ji-qun/jie-dian-wo-shou.md)
  * [分配槽](ji-qun-cao-zuo/da-jian-ji-qun/fen-pei-cao.md)
  * [用redis-trib.rb搭建集群](ji-qun-cao-zuo/da-jian-ji-qun/yong-redistrib.rb-da-jian-ji-qun.md)
* [节点通信](ji-qun-cao-zuo/untitled/README.md)
  * [通信流程](ji-qun-cao-zuo/untitled/tong-xin-liu-cheng.md)
  * [Gossip消息](ji-qun-cao-zuo/untitled/gossip-xiao-xi.md)
  * [节点选择](ji-qun-cao-zuo/untitled/jie-dian-xuan-ze.md)
* [集群伸缩](ji-qun-cao-zuo/ji-qun-shen-suo/README.md)
  * [伸缩原理](ji-qun-cao-zuo/ji-qun-shen-suo/shen-suo-yuan-li.md)
  * [扩容集群](ji-qun-cao-zuo/ji-qun-shen-suo/kuo-rong-ji-qun.md)
  * [收缩集群](ji-qun-cao-zuo/ji-qun-shen-suo/shou-suo-ji-qun.md)
* [请求路由](ji-qun-cao-zuo/qing-qiu-lu-you.md)

## 缓存设计

* [缓存设计](huan-cun-she-ji/huan-cun-she-ji.md)
* [缓存的收益和成本](huan-cun-she-ji/huan-cun-de-shou-yi-he-cheng-ben.md)
* [缓存更新策略](huan-cun-she-ji/huan-cun-geng-xin-ce-lve.md)
* [缓存粒度控制](huan-cun-she-ji/huan-cun-li-du-kong-zhi.md)

## 开发运维的“陷阱”

* [开发运维的“陷阱”](kai-fa-yun-wei-de-xian-jing/kai-fa-yun-wei-de-xian-jing.md)
* [Linux配置优化](kai-fa-yun-wei-de-xian-jing/linux-pei-zhi-you-hua/README.md)
  * [内存分配控制](kai-fa-yun-wei-de-xian-jing/linux-pei-zhi-you-hua/nei-cun-fen-pei-kong-zhi.md)

## Redis监控运维云平台

* [Redis监控运维云平台CacheCloud](redis-jian-kong-yun-wei-yun-ping-tai/redis-jian-kong-yun-wei-yun-ping-tai-cachecloud/README.md)
  * [CacheCloud是什么](redis-jian-kong-yun-wei-yun-ping-tai/redis-jian-kong-yun-wei-yun-ping-tai-cachecloud/cachecloud-shi-shen-me/README.md)
    * [现有问题](redis-jian-kong-yun-wei-yun-ping-tai/redis-jian-kong-yun-wei-yun-ping-tai-cachecloud/cachecloud-shi-shen-me/xian-you-wen-ti.md)
    * [CacheCloud基本功能](redis-jian-kong-yun-wei-yun-ping-tai/redis-jian-kong-yun-wei-yun-ping-tai-cachecloud/cachecloud-shi-shen-me/cachecloud-ji-ben-gong-neng.md)
  * [快速部署](redis-jian-kong-yun-wei-yun-ping-tai/redis-jian-kong-yun-wei-yun-ping-tai-cachecloud/kuai-su-bu-shu.md)
* [redis-manager](redis-jian-kong-yun-wei-yun-ping-tai/redis-manager.md)

---

* [常见问题](chang-jian-wen-ti-1.md)
* [参考文献](can-kao-wen-xian.md)

