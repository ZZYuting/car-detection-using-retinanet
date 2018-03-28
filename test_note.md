## 1.先用infer_simle.py测试kiit数据集合，随便几张就行了

cp infer_simple.py  infer2_simple.py

备份用

注意：要进入到detectron目录，把照片添加到demo里面，参数改成png

尝试输出改成.jpg

## 2.训练自己的模型

2.1连接coco dataset

```
ln -s /path/to/coco $DETECTRON/lib/datasets/data/coco
```

/path/to/coco 是coco的路径

2.2新建配置文件

在$DETECTRON根目录下打开Terminal复制：

1. cd configs

2. mkdir cars

3. cp 12_2017_baselines/retinanet_R-50-FPN_1x.yaml cars/cars_retinanet_R-50-FPN_1x.yaml

4. 打开.yaml文件进行编辑

5. MODEL：NUM_CLASSES改为3（因为的训练集分为3类）

6. 关于NUM_GPUS，这个需要与SOLVER里面的学习率等一干参数进行联动设置

7. DATASETS改为coco_2014_train;coco_2014_minival;

8. ```
   python2 tools/train_net.py \
       --cfg configs/cars/cars_retinanet_R-50-FPN_1x.yaml \
       OUTPUT_DIR /tmp/detectron-output
   ```

## 3.测试自己的模型

```
python2 tools/infer_simple.py \
    --cfg configs/12_2017_baselines/e2e_mask_rcnn_R-101-FPN_2x.yaml \
    --output-dir /tmp/detectron-visualizations \
    --image-ext jpg \
    --wts https://s3-us-west-2.amazonaws.com/detectron/35861858/12_2017_baselines/e2e_mask_rcnn_R-101-FPN_2x.yaml.02_32_51.SgT4y1cO/output/train/coco_2014_train:coco_2014_valminusminival/generalized_rcnn/model_final.pkl \
    demo
```

--cfg：改为configs/cars/cars_retinanet_R-50-FPN_1x.yaml

--image-ext :改为 png

--wts:改为/tmp/detectron-output/.pkl

demo :可以改为test

```
python2 tools/infer_simple.py \
    --cfg configs/cars/cars_retinanet_R-50-FPN_1x.yaml \
    --output-dir /tmp/detectron-visualizations \
    --image-ext png \
    --wts /tmp/detectron-output/.pkl \
    test
```

