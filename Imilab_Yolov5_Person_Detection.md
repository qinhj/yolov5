# Person Detection: Yolov5 #
[SINGLE-CLASS TRAINING EXAMPLE](https://github.com/ultralytics/yolov3/issues/102)  
```
==============================================================================
    model   | device | GPU Memory |    VmPeak   | Threads | Cpus_allowed_list
==============================================================================
  yolov5s   |  CPU   |  --------  | 15182444 kB |   34    |        0-7
------------------------------------------------------------------------------
  yolov5s   | CUDA:0 |  ?????MiB  | ???????? kB |    3    |        0-3
==============================================================================
```

## Test Benchmark ##
```
## quick commands
$ export MODELS="yolov5l yolov5l6 yolov5m yolov5m6 yolov5s yolov5s6 yolov5x yolov5x6"
$ export OPTIONS="--data data/coco5k.yaml --img-size 640 --verbose --save-txt --save-conf"
$ for m in $MODELS; do python test.py --weights weights/${m}.pt --project runs/test/${m} $OPTIONS > runs/test/${m}.txt; done

## runtime Namespace(others)
batch_size=32, conf_thres=0.001, iou_thres=0.6, task='val', single_cls=False, augment=False,
verbose=True, save_txt=True, save_hybrid=False, save_conf=True, save_json=False, exist_ok=False

## output results
===========================================================================================================================================
  model  | GPU Memory | Layers | Parameters  | Gradients |  FLOPS  | Precision    Recall  mAP@0.5 mAP@0.5:0.95 | Rank
===========================================================================================================================================
yolov5x  |   ------   |  476   | 87,730,285  |    0      | 219.0 G |     0.841    0.735    0.818      0.583    | 1
-------------------------------------------------------------------------------------------------------------------------------------------
yolov5x6 |   ------   |  606   | 141,755,500 |    0      |  -----  |     0.831    0.727    0.807      0.574    | 2
-------------------------------------------------------------------------------------------------------------------------------------------
yolov5l  |   ------   |  392   | 47,025,981  |    0      | 115.6 G |     0.837    0.724    0.808      0.566    | 3
-------------------------------------------------------------------------------------------------------------------------------------------
yolov5l6 |   ------   |  501   | 77,218,620  |    0      |  -----  |     0.841    0.708    0.799      0.562    | 4
-------------------------------------------------------------------------------------------------------------------------------------------
yolov5m  |   ------   |  308   | 21,356,877  |    0      |  51.4 G |     0.819    0.707    0.789      0.539    | 5
-------------------------------------------------------------------------------------------------------------------------------------------
yolov5m6 |   ------   |  396   | 35,889,612  |    0      |  -----  |     0.819    0.706    0.784      0.539    | 6
-------------------------------------------------------------------------------------------------------------------------------------------
yolov5s  |   ------   |  224   | 7,266,973   |    0      |  17.1 G |     0.794    0.667    0.746      0.472    | 8  <-- pick to vs yolov3-tiny
-------------------------------------------------------------------------------------------------------------------------------------------
yolov5s6 |   ------   |  291   | 12,653,596  |    0      |  -----  |     0.813    0.66     0.744      0.485    | 7
===========================================================================================================================================
```

## Test Case 0: scratch without any pretrained weights ##
```
===========================================================================================================================================
  model  | --device |      --weights      |       --cfg         |         --hyp         | Precision Recall mAP@0.5 mAP@0.5:0.95
===========================================================================================================================================
yolov5s  |  CUDA:0  | weights/yolov5s.pt  | models/yolov5s.yaml | data/hyp.scratch.yaml |   TODO ...
-------------------------------------------------------------------------------------------------------------------------------------------
yolov5m  |  CUDA:0  | weights/yolov5m.pt  | models/yolov5m.yaml | data/hyp.scratch.yaml |   TODO ...
===========================================================================================================================================

## train model
$ MODEL=yolov5s # yolov5m
$ OPTIONS="--data data/coco_person.yaml --single-cls --epochs 300 --batch-size 24"
$ python train.py --weights '' --cfg models/$MODEL.yaml --project runs/train/$MODEL $OPTIONS
```

## Test Case 1: scratch/finetune with official weights (X: optimize too slow) ##
```
$ MODEL=yolov5s
===========================================================================================================================================
  yolov5s  | --device |     --weights      |        --cfg        |         --hyp           | Precision Recall mAP@0.5 mAP@0.5:0.95
===========================================================================================================================================
(-) case1  |   CPU    | weights/yolov5s.pt | models/yolov5s.yaml | data/hyp.scratch.yaml   |
-------------------------------------------------------------------------------------------------------------------------------------------
(-) case2  |  CUDA:0  | weights/yolov5s.pt | models/yolov5s.yaml | data/hyp.finetune.yaml  |
===========================================================================================================================================

$ MODEL=yolov5m
===========================================================================================================================================
  yolov5m  | --device |     --weights      |        --cfg        |         --hyp           | Precision Recall mAP@0.5 mAP@0.5:0.95
===========================================================================================================================================
(-) case1  |  CUDA:0  | weights/yolov5m.pt | models/yolov5m.yaml | data/hyp.scratch.yaml   |
-------------------------------------------------------------------------------------------------------------------------------------------
(-) case2  |  CUDA:0  | weights/yolov5m.pt | models/yolov5m.yaml | data/hyp.finetune.yaml  |
===========================================================================================================================================

## train model
$ OPTIONS="--data data/coco_person.yaml --single-cls --epochs 300 --batch-size 24"
$ python train.py --weights weights/$MODEL.pt --cfg models/$MODEL.yaml --project runs/train/$MODEL $OPTIONS --hyp data/hyp.scratch.yaml
$ python train.py --weights weights/$MODEL.pt --cfg models/$MODEL.yaml --project runs/train/$MODEL $OPTIONS --hyp data/hyp.finetune.yaml

* Common Options
data: data/coco_person.yaml
epochs: 300
batch_size: 24
img_size: [640, 640]
multi_scale: false
single_cls: true
adam: false
```

## Test Case 2: scratch/finetune with warmup weights on val data (Y: seems overfit a lot) ##
```
$ MODEL=yolov5s
===========================================================================================================================================
  yolov5s   | --epochs | --device |       --weights        |         --hyp          |         --data          | Precision Recall mAP@0.5 mAP@0.5:0.95
===========================================================================================================================================
(-) step1   |   10/20  |   CPU    | weights/yolov5s.pt     | data/hyp.scratch.yaml  | data/coco_person5k.yaml |
-------------------------------------------------------------------------------------------------------------------------------------------
(-) step2.1 |   300    |  CUDA:0  | warmup/weights/best.pt | data/hyp.scratch.yaml  | data/coco_person.yaml   |
-------------------------------------------------------------------------------------------------------------------------------------------
(-) step2.2 |   300    |  CUDA:0  | warmup/weights/best.pt | data/hyp.finetune.yaml | data/coco_person.yaml   |
===========================================================================================================================================

$ MODEL=yolov5m
===========================================================================================================================================
  yolov5m   | --epochs | --device |       --weights        |         --hyp          |         --data          | Precision Recall mAP@0.5 mAP@0.5:0.95
===========================================================================================================================================
(-) step1   |   10/20  |  CUDA:0  | weights/yolov5m.pt     | data/hyp.scratch.yaml  | data/coco_person5k.yaml |
-------------------------------------------------------------------------------------------------------------------------------------------
(-) step2.1 |   300    |  CUDA:0  | warmup/weights/best.pt | data/hyp.scratch.yaml  | data/coco_person.yaml   |
-------------------------------------------------------------------------------------------------------------------------------------------
(-) step2.2 |   300    |  CUDA:0  | warmup/weights/best.pt | data/hyp.finetune.yaml | data/coco_person.yaml   |
===========================================================================================================================================

## train model: warmup
$ OPTIONS="--data data/coco_person5k.yaml --single-cls --epochs 20 --batch-size 24"
$ python train.py --weights weights/$MODEL.pt --cfg models/$MODEL.yaml --project runs/train/$MODEL $OPTIONS --hyp data/hyp.scratch.yaml
$ python train.py --weights weights/$MODEL.pt --cfg models/$MODEL.yaml --project runs/train/$MODEL $OPTIONS --hyp data/hyp.finetune.yaml
## train model: scratch/finetune
$ OPTIONS="--data data/coco_person.yaml --single-cls --epochs 300 --batch-size 24"
$ python train.py --weights warmup/weights/best.pt --cfg models/$MODEL.yaml --project runs/train/$MODEL $OPTIONS --hyp data/hyp.scratch.yaml
$ python train.py --weights warmup/weights/best.pt --cfg models/$MODEL.yaml --project runs/train/$MODEL $OPTIONS --hyp data/hyp.finetune.yaml

* Common Options
batch_size: 24
img_size: [640, 640]
multi_scale: false
single_cls: true
adam: false

* Results:
```

## Test Results ##
```
```