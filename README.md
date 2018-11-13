# DC_car
##### Solution by [Seaty-朴素贝叶斯baseline-0.42](http://www.pkbigdata.com/common/bbs/topicDetails.html?tid=2510)

1. 使用DBSCAN对所有数据的起点与终点进行密度聚类，结果作为数据的标签，只保留训练集中不被判定为噪声的数据。
2. 根据日期为每个数据增加：出发日（day of week）、小时、是否节假日
3. 计算条件概率（似然度）——
   * P(start\_block|end\_block)、P(out_id|end_block)、P(is_holiday|end_block)、
   * P((is_holiday, hour)|end_block)、P(day|end_block)、
   * P(hour|end_block)、P((hour,half)|end_block)

4. 计算先验概率——

   * P(end_block)

5. 使用merge，分别以——

   * ['out_id']，[['is_holiday', 'end_block']，['is_holiday', 'hour', 'end_block']，
   *  ['start_block', 'end_block']，['end_block']

   为键左外连接，为测试数据增加上述条件概率列，具体操作见代码***（上述合并会出现同一个样本id有不同条件概率的情况，如一个end_block中有多个out_id，做合并后一个out_id会对应多个end_block和P(out_id|end_block)）***

6. 计算测试集的P(end_block|(start_block, out_id, is_holiday, hour))，取最大值作为end_block的预测值



##### Train each car

1. 训练数据中每辆车（相同out_id）最少有87个样例，最多有582个样例，考虑对每辆车进行训练和预测
2. 对每辆车根据经纬度分类，具体地，设置一个经纬度网格，每一网格的大小为0.005 * 0.005，若在同一网格内，设置为同一类（若分类后类别超过10个，则增大网格数）
3. 使用几个模型做多分类，预测结果的经纬度为在对应网格中的所有点经纬度的均值





##### 可能的特征

平均车速、平均行驶时长、平均里程