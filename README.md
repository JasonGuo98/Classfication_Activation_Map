# Classfication_Activation_Map
Show activation area for classfication on the original img
## Classfication_Activation_Map可视化


* 使用方法：
    0. 将本文件和utils.py放在slim根目录
    1. 加载自己的dataset
        ```python
        from datasets import card_classfication
        dst = card_classfication
        dst_dir = './cards_for_train/'
        dataset = dst.get_split('validation', dst_dir)# 默认验证集
        ```
    2. 修改参数kw
       其中参数是：
       
        ---
        ```python
        graph_path: 计算图的的path，.meta文件
        weight_path: 参数的path,.ckpt
        input_name: 计算图入口
        dataset: 使用的数据集，slim框架下的
        weight_name:GAP层后直接用于分类的weight的name
        last_conv_layers：最后一个卷积层得到的tensor的name,其实是输入GAP层的tensor
        logit: logit tensor的name
        plot_dir: 保存图片的地址,图片将保存到"plot_dir/CAM_OUT/iter_t/" 文件夹下
        _iters:循环次数
        ```
        ---
    3. 运行函数`show_conv_out(**kw)`
* exp
    ---
    
    ```python
    from datasets import card_classfication
    dst = card_classfication
    dst_dir = './cards_for_train/'
    dataset = dst.get_split('validation', dst_dir)# 默认验证集

    kw1 = {
        'graph_path':'MOBILE_NET_V2_CAM/model.ckpt-46542.meta',
        'weight_path':'MOBILE_NET_V2_CAM/model.ckpt-46542',
        'input_name':'fifo_queue_Dequeue',
        'dataset':dataset,
        'weight_name':'MobilenetV2/Logits/Conv2d_1c_1x1/weights:0',
        'last_conv_layers':'MobilenetV2/embedding:0',
        'logit':'MobilenetV2/Predictions/Reshape:0',
        'plot_dir':'out',
        'batch_size':64,
        '_iters':1,
    }
    show_cam_out(**kw1)
    ```
    ---
    
    
* 需要注意的点:
    1. 依赖的py文件：utils.py, 用于prepare_dir
    2. 计算图中的变量定义不同需要在计算图中找到对应的变量名修改
    3. 因为计算meta文件定义了计算图,在这里需要使用和它相同的batch_size才能使用
    4. 未完成的点:在原本的计算图中得到的logit是有bias的,需要对last_conv层进行补偿才能得到与计算logit相同的 featuremap,所以这里在数学上有部分不合理.但考虑到这样已经能够达到很好的效果了,我并未如此严谨.
    5. last_conv参数需要的是输入GAP(global_average_pooling)层的tensor
    6. 创建dataset的时候使用, 使用slim创建tfrecord文件
    
