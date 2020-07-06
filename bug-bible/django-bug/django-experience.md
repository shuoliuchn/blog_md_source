## Django 学习过程的小经验小技巧

1. 关于 DRF 的，如果模型中的字段查询不到，可以检查一下是否是因为继承了 Serializer 类，而没有继承 ModelSerializer 类。