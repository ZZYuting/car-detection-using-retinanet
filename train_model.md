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
   ```
   SOLVER:
     WEIGHT_DECAY: 0.0001
     LR_POLICY: steps_with_decay
     BASE_LR: 0.01
     GAMMA: 0.1
     MAX_ITER: 90000
     STEPS: [0, 60000, 80000]
   ```


```
线性规则，实验分析有数据
Equivalent schedules with...
  # 1 GPU:
  #   BASE_LR: 0.0025
  #   MAX_ITER: 60000
  #   STEPS: [0, 30000, 40000]
  # 2 GPUs:
  #   BASE_LR: 0.005
  #   MAX_ITER: 30000
  #   STEPS: [0, 15000, 20000]
  # 4 GPUs:
  #   BASE_LR: 0.01
  #   MAX_ITER: 15000
  #   STEPS: [0, 7500, 10000]
  # 8 GPUs:
  #   BASE_LR: 0.02
  #   MAX_ITER: 7500
  #   STEPS: [0, 3750, 5000]
```



## 3.测试自己的模型

```
python2 tools/infer_simple.py \
    --cfg configs/12_2017_baselines/e2e_mask_rcnn_R-101-FPN_2x.yaml \
    --output-dir /tmp/detectron-visualizations \
    --image-ext png \
    --wts https://s3-us-west-2.amazonaws.com/detectron/35861858/12_2017_baselines/e2e_mask_rcnn_R-101-FPN_2x.yaml.02_32_51.SgT4y1cO/output/train/coco_2014_train:coco_2014_valminusminival/generalized_rcnn/model_final.pkl \
    demo
```

--cfg：改为configs/cars/cars_retinanet_R-50-FPN_1x.yaml

--image-ext :改为 png

--wts:改为/tmp/detectron-output/.pkl

demo :可以改为test

```
python2 tools/test_net.py \
    --cfg configs/12_2017_baselines/e2e_mask_rcnn_R-101-FPN_2x.yaml \
    TEST.WEIGHTS https://s3-us-west-2.amazonaws.com/detectron/35861858/12_2017_baselines/e2e_mask_rcnn_R-101-FPN_2x.yaml.02_32_51.SgT4y1cO/output/train/coco_2014_train:coco_2014_valminusminival/generalized_rcnn/model_final.pkl \
    NUM_GPUS 1
```



```
python2 tools/infer_simple.py \
    --cfg configs/cars/cars_retinanet_R-50-FPN_1x.yaml \
    --output-dir /tmp/detectron-visualizations \
    --image-ext png \
    --wts /tmp/detectron-output/.pkl \
    test
```

```
python2 tools/infer2_simple.py \
    --cfg configs/12_2017_baselines/retinanet_R-50-FPN_1x.yaml \
    --output-dir /tmp/detectron-visualizations \
    --image-ext png \
    --wts https://s3-us-west-2.amazonaws.com/detectron/36768636/12_2017_baselines/retinanet_R-50-FPN_1x.yaml.08_29_48.t4zc9clc/output/train/coco_2014_train:coco_2014_valminusminival/retinanet/model_final.pkl \
    cars
    
```

# 4.修改bbox和输出类型

[Detectron](https://github.com/facebookresearch/Detectron/tree/80f329530843e66d07ca39e19901d5f3e5daf009)/[lib](https://github.com/facebookresearch/Detectron/tree/80f329530843e66d07ca39e19901d5f3e5daf009/lib)/[utils](https://github.com/facebookresearch/Detectron/tree/80f329530843e66d07ca39e19901d5f3e5daf009/lib/utils)/**vis.py**

在vis.py中可以修改bbox的颜色

```
_GRAY = (218, 227, 218)
_GREEN = (18, 127, 15)
//_Red=(220,20,60)修改为红色：220,20,60
_WHITE = (255, 255, 255)
```

```
def vis_class(img, pos, class_str, font_scale=0.35):
    """Visualizes the class."""
    x0, y0 = int(pos[0]), int(pos[1])
    # Compute text size.
    txt = class_str
    font = cv2.FONT_HERSHEY_SIMPLEX
    ((txt_w, txt_h), _) = cv2.getTextSize(txt, font, font_scale, 1)
    # Place text background.
    back_tl = x0, y0 - int(1.3 * txt_h)
    back_br = x0 + txt_w, y0
    cv2.rectangle(img, back_tl, back_br, _GREEN, -1)
    # Show text.
    txt_tl = x0, y0 - int(0.3 * txt_h)
    cv2.putText(img, txt, txt_tl, font, font_scale, _GRAY, lineType=cv2.LINE_AA)
    return img


def vis_bbox(img, bbox, thick=1)://thick=2，增加宽度，便于观察
    """Visualizes a bounding box."""
    (x0, y0, w, h) = bbox
    x1, y1 = int(x0 + w), int(y0 + h)
    x0, y0 = int(x0), int(y0)
    cv2.rectangle(img, (x0, y0), (x1, y1), _GREEN, thickness=thick)
    return img
```

修改输出类型：

```
vis_utils.vis_one_image(
            im[:, :, ::-1],  # BGR -> RGB for visualization
            im_name,
            args.output_dir,
            cls_boxes,
            cls_segms,
            cls_keyps,
            dataset=dummy_coco_dataset,
            box_alpha=0.3,
            show_class=True,
            thresh=0.7,
            kp_thresh=2,
         // ext="png"
        )
```

在infer_simple.py中的vis_utils.vis_one_image添加：

ext=“png”或者“jpg”

5.配置

测试model:

```
python2 tools/test_net.py \
    --cfg configs/12_2017_baselines/e2e_mask_rcnn_R-101-FPN_2x.yaml \
    TEST.WEIGHTS https://s3-us-west-2.amazonaws.com/detectron/35861858/12_2017_baselines/e2e_mask_rcnn_R-101-FPN_2x.yaml.02_32_51.SgT4y1cO/output/train/coco_2014_train:coco_2014_valminusminival/generalized_rcnn/model_final.pkl \
    NUM_GPUS 1
```

