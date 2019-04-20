# Deeplearning Note
## Detection:
  **one-stage:**
    
    yolov1:
      将图像分成s\*s的网格，每个格子分别预测中心点落在此格子内的物体，没有使用anchor，所以没有RPN层的操作，速度上会快很多。但是网络最后有两层全连接，
      且分类和回归之路共享参数，最后一层的输出维度为（5\*B+C）
    yolov2：
      引入BN，使用多尺度训练，引入anchor，并且删除全连接层，使用kmeans做聚类得到先验的anchor尺寸。为回轨加入边界条件，使之不会产生太大的偏移量，
      使回归后的bounding box的中心点还是在预测cell内。引入shortcut，使底层特征与高层特征进行连接。
    yolov3:
      使用3层FPN，每层预测3个anchor box，backbone使用darknet53，引入像resnet一样的shortcut。
    ssd：
      使用金字塔结构以及anchor box进行多层预测，分类和回归依然共享参数
    dssd:
      在预测模块上使用shortcut结构，增加反卷积操作并于底层特征相连接进行多层预测
    dsod：
      不使用imagenet预训练模型直接进行训练，backbone使用densenet，增加底层特征下采样与高层特征concatenate的操作。
    rfbnet：
      增加RFB层和RFB-s层，使用了inception结构和shortcut，并在inception的每一个分支引入比率不同的dilated conv layer或者pooling layer，来使网络可
      以结合不同感受野的信息。
    retinanet：
      使用fpn操作，引入focal loss解决训练过程中的类别不平衡问题，对更难预测的bounding box给予更大的惩罚，反之对容易预测的bounding box给予较小的惩
      罚。在每层fpn中使用3个不同长宽比以及3个不同面积，共九个anchor。分类和回归使用各自的子网络进行预测。
    FCOS：
      放弃了anchor，以retinanet为基础，回归是不再预测左上角点以及宽高，而是预测特征图上的点距离groundtruth四个边的距离。由于取消了anchor，在分类
      支路增加了centerness支路centerness = min(l，r)/max(l, r)×min(t, b)/max(t, b)，来限制回归的offset的大小，防止offset过大。
    cornernet：
      预测网络的左上角以及右下角点的热力图以及对应的嵌入向量，并使用嵌入向量对两对热力图进行匹配，嵌入向量之间的距离越小则是一个物体的可能性越大。为
      了更好的预测角点，引入corner pooling layer，输出为横向或纵向的max pooling。
    NAS-FPN：
       google brain出的检测论文，使用了增强学习来构建fpn的结构，训练RL时由RNN网络每次选两个特征层作为输入，选择一种尺度作为输出，选择一种操作结合两
       个输入。操作有两种，分别是求和和global pooling，输出会自动经过一层relu，3\*3conv，bn，再结合两组输入前会使用双线性插值或者max pooling放缩到
       同一尺度。每次使用子网络的AP作为reward训练RL，搜索FPN结构时，backbone使用的时resnet-18。网络backbone使用的是AmoebaNet（也是使用NAS搜索出
       的网络），并结合了dropblock。
      
